# Use multiple files in vue-router


```javascript
// file -> /src/router.js

import Vue from 'vue'
import VueRouter from 'vue-router'
import Dashboard from '/modules/dashboard/routes.js'
import Users from '/modules/users/routes.js'
import Clients from '/modules/clients/routes.js'

const Router = new VueRouter()

const routes = Object.assign({}, Dashboard, Users, Clients)
Router.map(routes)

//  --- alternative ---
const routes = [Dashboard, Users, Clients]
Router.map(Object.assign({}, ...routes)

export default Router
```

```javascript
// file -> /src/modules/clients/routes.js

import Main from './Main.vue'
import Detail from './Detail.vue'

export default {
  '/clients': {
    component: Main,
  },
  '/clients/:id': {
    component: Detail,
  },
}
```
------------

Created by [vinicius73](https://github.com/vinicius73) in 2016-08-12
