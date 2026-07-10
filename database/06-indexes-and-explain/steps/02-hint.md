`CREATE INDEX idx_events_user_id ON events (user_id);` then `ANALYZE events;`. The check looks for an index on `events` whose definition mentions `user_id`.
