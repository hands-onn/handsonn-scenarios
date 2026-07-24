`CREATE INDEX idx_line_items_order_id ON line_items (order_id);` then `ANALYZE line_items;`. The check looks for exactly one index on `line_items` whose definition mentions `order_id`.
