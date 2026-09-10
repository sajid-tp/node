### What express.Router() actually is
```javascript
const router = express.Router();
```
This creates a separate, isolated instance of Express's routing system — it has its own .get(), .post(), .use(), etc., completely independent of your main app. Think of it as a miniature Express app that only knows about routes you explicitly attach to it.  

---
each router.post(...), router.get(...), etc. call registers an entry in the router's internal list, rather than executing anything immediately. Let's look at what that actually means under the hood.

What's really happening internally

express.Router() returns an object that maintains an internal array — often called a middleware/route stack. Every time you call router.post(path, handler) or router.get(path, handler), Express doesn't run your handler — it just pushes an entry onto that stack:

```javascript
// Roughly what's happening internally (simplified):
router.stack = [
  { method: 'POST', path: '/signup', handler: signUp },
  { method: 'POST', path: '/login',  handler: login },
];
```
So if your file looks like:

```javascript
router.post('/signup', signUp);
router.post('/login', login);
router.get('/profile', getProfile);
```
each of these three lines is just adding one more entry to that same router's stack — you're right that they all accumulate onto the same router object. Nothing executes yet; you're just building up a lookup table of "if a request matches this method+path, call this function."

Where the handler actually runs

The functions only get called later, when an actual HTTP request comes in and gets routed through:
```
Request arrives → app.js matches prefix (`/api/auth`) → hands off to router
→ router scans its stack top-to-bottom → finds matching method+path
→ calls the corresponding handler(req, res)
```
This is also why route order matters within a router — Express checks entries in the order you registered them, and uses the first match it finds. If you accidentally registered two routes with overlapping patterns, whichever was added first wins.
