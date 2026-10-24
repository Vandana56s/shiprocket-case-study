# AI-Use Note

- **Tools Used:** Claude 3.5 Sonnet & ChatGPT for code boilerplate generation, regex cleaning logic, and sanity check debugging.
- **Issue Encountered:** An initial AI-suggested SQL query divided total spend by all signups in `users.csv`, rather than restricting the denominator to users with delivered orders as required by the brief.
- **Correction Applied:** Refined the logic to join with `orders.csv` filtered on `status == 'delivered'` and `order_value > 0`.
