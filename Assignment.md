**Hosted application:** [Link](https://cisc472-assignment1.onrender.com)

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


**How request becomes row:**
The user enters information into the form, which sends a POST request containing those fields. Express parses the request body and makes the values available through req.body. The route checks that the user is authenticated, validates and normalizes the fields, and then runs a parameterized SQL INSERT statement. SQLite uses those values to create a new row in the items table. The server then redirects the user to the new item’s detail page.

**where is auth:**
In `auth.js`, a function (requireAuth) checks if the user is authenticated in the session. If the user is not authenticated, it gives an error message and redirects them to the login page. Otherwise, it lets the request continue. Then in `items.js`, there are GET and POST requests that have authentication requirements.

**how search, filters, and item pages load data**
A GET request in items processed the search text from req.query.q and filters come from req.query.category and req.query.kind and a searchItems() function takes those inputs and makes them parameters in a SQLite query(SELECT). SQLite then returns the resulto to then display it. If user clicks on item, then a GET (GET/items/:id) requests gets the specific id of that post and does a sqlite query that returns the info associated with that id.