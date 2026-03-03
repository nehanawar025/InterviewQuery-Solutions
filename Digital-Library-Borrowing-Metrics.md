```sql
SELECT 
    br.book_id,
    ROUND(
        SUM(TIMESTAMPDIFF(MINUTE, borrowed_at, returned_at)/1440.0)
        /COUNT(IF(returned_at IS NOT NULL, 1, NULL)) , 2) AS avg_borrow_duration_days,
    IF(copies_available > 10, "Yes", "No") AS has_over_10_copies
FROM
    borrows br LEFT JOIN books bk
    ON br.book_id = bk.book_id
GROUP BY 
    br.book_id
HAVING 
    SUM(TIMESTAMPDIFF(HOUR, borrowed_at, returned_at)/24.0)/COUNT(IF(returned_at IS NOT NULL, 1, NULL)) IS NOT NULL
ORDER BY 
    avg_borrow_duration_days DESC,
    book_id
```
