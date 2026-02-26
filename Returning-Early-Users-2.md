```sql
WITH cte AS(
    SELECT 
        region,
        account_id,
        new_tier,
        upgrade_date,
        post_upgrade_active_days,
        RANK() OVER(PARTITION BY region ORDER BY region, post_upgrade_active_days DESC, upgrade_date) AS rn
    FROM
        account_subscription_activity
    WHERE
        previous_tier = "STANDARD"
        AND new_tier IN ("PREMIUM", "ENTERPRISE")
        AND post_upgrade_active_days >=20 
        AND upgrade_date >= (SELECT DATE_SUB(MAX(upgrade_date), INTERVAL 90 DAY) FROM account_subscription_activity)
)

SELECT
    region,
    account_id,
    new_tier,
    upgrade_date,
    post_upgrade_active_days
FROM
    cte
WHERE
    rn <= 3

```
