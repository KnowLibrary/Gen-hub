# Gen-hub


# Express Middleware using Axios method
```
const axios = require('axios')
const authMiddleware = asyn (req, res, next) => {
  try {
    const data = await axios.get('http://example.com/data')
    if (response.data) {
      next();
    } else {
      res.status(401).send('Forbidden');
    }  
  } catch (error) {
    res.status(500).send('Interal Server Error');
  }  
}
const app = express();
app.use('/', authMiddleware);

const PORT = process.env.PORT || 3000;
app.listen( PORT, () => { console.log('Server is running on ' + PORT); } );

```

# Axios with Basic Auth Example
