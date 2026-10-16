## PortSwigger Lab: Unprotected admin functionality
**Vulnerability class:** Broken Access Control
**What the app did wrong:** Admin functionality existed with no
server-side permission check — it relied on the link simply not being
shown to non-admin users in the UI.
**How I exploited it:** Found the admin panel's URL directly (visible
in the UI / disclosed via robots.txt), navigated to it, and reached
admin functionality with no authorization check blocking the request.
**How I'd fix it:** Enforce server-side authorization on every admin
request — deny by default, checked against the user's actual role —
rather than relying on the link being hidden from non-admin users.

## PortSwigger Lab: Unprotected admin functionality with unpredictable URL
**Vulnerability class:** Broken Access Control (security through
obscurity)
**What the app did wrong:** The admin URL wasn't linked anywhere in
the UI, but was still disclosed elsewhere — in this case, in the
page's JavaScript source sent to every user.
**How I exploited it:** Found the admin URL inside the client-side
JavaScript, navigated to it directly, and was able to delete data with
no server-side permission check in place.
**How I'd fix it:** Same underlying fix as above — an unguessable URL
is not a real access control, no matter how obscure. The endpoint
needs its own server-side authorization check regardless of whether
anyone can find the URL.

## PortSwigger Lab: User ID controlled by request parameter
**Vulnerability class:** IDOR leading to horizontal privilege escalation
**What the app did wrong:** The user account page trusted the user ID
supplied in the request, with no server-side check that it matched the
currently authenticated user.
**How I exploited it:** Logged in as a regular user, then changed the
user ID in the URL to another user's ID, and was able to view that
user's API key.
**How I'd fix it:** Enforce a server-side check on every request:
compare the requested user ID against the ID of the authenticated
session, and reject the request if they don't match — the same
ownership-check pattern that fixes IDOR generally, regardless of what
the URL or request parameters claim.

## PortSwigger Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

**Vulnerability class:** SQL injection (CWE-89): user input concatenated into a database query.

**What the app did wrong:** The product category filter took the `category` URL parameter and placed it directly into the SQL query as a string. The database could not tell the developer's SQL from the user's input, so characters like `'` and `--` were treated as SQL syntax. The query also relied on a `released = 1` condition to hide unreleased products, so one injection point exposed data the app meant to hide.

**How I exploited it:** I added a single quote to the category value to close the string, then added a SQL comment (`--`) so the database ignored the rest of the original query, including the `released = 1` filter. The page then returned all products, including unreleased ones. (A payload such as `' OR 1=1--` also works, and is more reliable when the rest of the query is unknown, because the always-true condition makes the WHERE clause match every row.)

**How I'd fix it:** Use parameterized queries (prepared statements) so user input is always treated as data, never as SQL. Add allow-list validation on the category value as defense in depth. Run the application's database account with least privilege, so a successful injection exposes less. In code review or SAST, flag any query built by string concatenation or formatting with request input.