# TIP: Booking Cancellation Implementation

**Author**: Senior Engineer (Jane Smith)  
**Date**: 2024-01-22  
**Source PRD**: PRD-015  
**Status**: Draft (for RFC)  

---

## Overview

This is a lightweight technical proposal for implementing the booking cancellation feature. Since this is a straightforward CRUD operation on an existing domain model, we can skip the full DAA and move directly to technical implementation details.

---

## Database Changes

### Schema Modification

Add cancellation-related columns to existing `bookings` table:

```sql
ALTER TABLE bookings 
  ADD COLUMN cancelled_at TIMESTAMP NULL,
  ADD COLUMN cancellation_reason VARCHAR(50) NULL,
  ADD COLUMN cancellation_notes TEXT NULL;

-- Add check constraint for cancellation reason enum
ALTER TABLE bookings
  ADD CONSTRAINT check_cancellation_reason 
  CHECK (cancellation_reason IN (
    'change_of_plans',
    'double_booking', 
    'found_alternative',
    'no_longer_needed',
    'other'
  ));

-- Add constraint: if status = 'cancelled', reason must exist
ALTER TABLE bookings
  ADD CONSTRAINT check_cancellation_consistency
  CHECK (
    (status = 'cancelled' AND cancellation_reason IS NOT NULL) OR
    (status != 'cancelled' AND cancellation_reason IS NULL)
  );

-- Add index for querying cancelled bookings
CREATE INDEX idx_bookings_cancelled_at ON bookings(cancelled_at) 
  WHERE cancelled_at IS NOT NULL;
```

### Event Outbox Table

Create outbox table for reliable event publishing:

```sql
CREATE TABLE booking_outbox (
  id BIGSERIAL PRIMARY KEY,
  booking_id UUID NOT NULL,
  event_type VARCHAR(100) NOT NULL,
  event_payload JSONB NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  processed_at TIMESTAMP NULL
);

CREATE INDEX idx_outbox_unprocessed ON booking_outbox(created_at) 
  WHERE processed_at IS NULL;
```

---

## API Changes

### New Endpoint

```
POST /v1/bookings/{booking_id}/cancel
```

**Request Headers:**
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
  "reason": "change_of_plans",
  "notes": "Found a better option"
}
```

**Response (200 OK):**
```json
{
  "booking_id": "a1b2c3d4-5678-90ab-cdef-1234567890ab",
  "status": "cancelled",
  "cancelled_at": "2024-01-22T14:30:00Z",
  "cancellation_reason": "change_of_plans",
  "cancellation_notes": "Found a better option"
}
```

**Error Responses:**

```json
// 400 Bad Request - Invalid state
{
  "error": "INVALID_STATUS",
  "message": "Cannot cancel booking with status 'completed'"
}

// 403 Forbidden - Not owner
{
  "error": "UNAUTHORIZED",
  "message": "You do not own this booking"
}

// 404 Not Found
{
  "error": "NOT_FOUND",
  "message": "Booking not found"
}
```

---

## Service Layer Changes

### BookingService Updates

Add new method to `BookingService`:

```go
// CancelBooking cancels a booking and publishes cancellation event
func (s *BookingService) CancelBooking(
  ctx context.Context,
  bookingID uuid.UUID,
  userID uuid.UUID,
  reason string,
  notes string,
) (*Booking, error) {
  
  // Start transaction
  tx, err := s.db.BeginTx(ctx, nil)
  if err != nil {
    return nil, err
  }
  defer tx.Rollback()
  
  // Load booking
  booking, err := s.repo.FindByID(ctx, tx, bookingID)
  if err != nil {
    return nil, err
  }
  
  // Verify ownership
  if booking.UserID != userID {
    return nil, ErrUnauthorized
  }
  
  // Verify status
  if booking.Status != StatusConfirmed {
    return nil, ErrInvalidStatus
  }
  
  // Update booking
  booking.Status = StatusCancelled
  booking.CancelledAt = time.Now()
  booking.CancellationReason = reason
  booking.CancellationNotes = notes
  
  // Save booking
  if err := s.repo.Update(ctx, tx, booking); err != nil {
    return nil, err
  }
  
  // Write to outbox
  event := BookingCancelledEvent{
    BookingID:   booking.ID,
    UserID:      booking.UserID,
    ResourceID:  booking.ResourceID,
    CancelledAt: booking.CancelledAt,
    Reason:      reason,
    Notes:       notes,
  }
  if err := s.outbox.Insert(ctx, tx, event); err != nil {
    return nil, err
  }
  
  // Commit transaction
  if err := tx.Commit(); err != nil {
    return nil, err
  }
  
  return booking, nil
}
```

---

## Event Publishing

### Outbox Relay Worker

Background worker to poll outbox and publish to Kafka:

```go
// OutboxRelay polls outbox table and publishes events
func (r *OutboxRelay) Run(ctx context.Context) {
  ticker := time.NewTicker(5 * time.Second)
  defer ticker.Stop()
  
  for {
    select {
    case <-ctx.Done():
      return
    case <-ticker.C:
      r.processOutbox(ctx)
    }
  }
}

func (r *OutboxRelay) processOutbox(ctx context.Context) {
  // Fetch unprocessed events (limit 100)
  events, err := r.outbox.FetchUnprocessed(ctx, 100)
  if err != nil {
    log.Error("failed to fetch outbox events", err)
    return
  }
  
  for _, event := range events {
    // Publish to Kafka
    if err := r.kafka.Publish(ctx, event); err != nil {
      log.Error("failed to publish event", err)
      continue
    }
    
    // Mark as processed
    if err := r.outbox.MarkProcessed(ctx, event.ID); err != nil {
      log.Error("failed to mark event processed", err)
    }
  }
}
```

---

## Testing Plan

### Unit Tests

1. `TestCancelBooking_Success` - Happy path cancellation
2. `TestCancelBooking_AlreadyCancelled` - Reject already cancelled
3. `TestCancelBooking_Completed` - Reject completed booking
4. `TestCancelBooking_Unauthorized` - Reject wrong user
5. `TestCancelBooking_NotFound` - Handle missing booking
6. `TestOutboxRelay_PublishesEvents` - Verify event publishing

### Integration Tests

1. End-to-end cancellation flow with database
2. Event publishing via outbox pattern
3. Concurrent cancellation attempts (race condition)

### Load Tests

1. 1000 concurrent cancellation requests
2. Verify p95 latency < 500ms per NFR-015-1

---

## Deployment

### Database Migration

```bash
# Run migration
./migrate up booking_cancellation_v1

# Rollback plan
./migrate down booking_cancellation_v1
```

### Feature Flag

Deploy behind feature flag `enable_booking_cancellation`:

```yaml
# config.yaml
features:
  enable_booking_cancellation: true
```

### Monitoring

Add Prometheus metrics:
- `booking_cancellation_requests_total` (counter)
- `booking_cancellation_duration_seconds` (histogram)
- `booking_cancellation_errors_total` (counter by error type)

---

## Open Questions

### Q1: Outbox Worker Failure Handling
**Question**: What happens if outbox worker crashes? Do we risk losing events?  
**Answer Needed**: Define retry strategy and dead-letter queue

### Q2: Cancellation Rate Limiting
**Question**: Should we rate-limit cancellations per user?  
**Answer Needed**: Product decision - probably not for MVP

### Q3: Audit Logging
**Question**: Should we log cancellations to separate audit table?  
**Answer Needed**: Compliance requirement?

### Q4: Idempotency
**Question**: Should API be idempotent (same cancel request twice)?  
**Answer Needed**: Probably yes, but how to implement?

---

## Timeline Estimate

- Database migration: 1 hour
- Service layer implementation: 4 hours
- Outbox relay worker: 3 hours
- API endpoint + validation: 2 hours
- Unit tests: 3 hours
- Integration tests: 2 hours
- Documentation: 1 hour

**Total**: ~16 hours (2 days)

---

## Next Steps

1. Review this TIP with team
2. Create RFC for collaborative design review
3. Consult Architectural Playbook for outbox pattern best practices
4. Finalize design and create ADR

---

**Status**: Ready for RFC review
