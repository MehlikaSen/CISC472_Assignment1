# SQL Injection

**Location:** `src/routes/items.js`, `searchItems()`

**Trigger:** Entering the following request in the search bar:

```text
GET /?q=' OR 1=1 --
```

The search bar inserts user input directly into a SQL query. Because the input is not treated as plain text, a user can enter SQL syntax that changes the query's logic. For example, a payload such as `' OR 1=1 --` adds a condition that is always true, which can cause the search to return listings that do not match the intended search. This happens because `1=1` always evaluates to true, while `--` comments out the rest of the query.

The issue can be fixed by using parameterized queries. This keeps the SQL instructions separate from the user's input and ensures that the input is treated only as search text, not as SQL code.

## Patch

This issue was fixed by parameterizing the user's input instead of concatenating it into the SQL command. The SQL structure is now fixed, and the user's input is passed separately as a parameter. This ensures that the input is treated as text to search for rather than as part of the SQL command.