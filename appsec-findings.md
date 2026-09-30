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