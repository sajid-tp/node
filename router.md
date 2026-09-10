### What express.Router() actually is
```javascript
const router = express.Router();
```
This creates a separate, isolated instance of Express's routing system — it has its own .get(), .post(), .use(), etc., completely independent of your main app. Think of it as a miniature Express app that only knows about routes you explicitly attach to it.
