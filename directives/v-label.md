
# v-label

```javascript
// directives/label.js

const label = {
  update(value) {
    let className = this.el.className;
    if (value) {
      className = `${className} label label-success`;
    } else {
      className = `${className} label label-danger`;
    }

    this.el.className = className;
  },
};

//
Vue.directive('label', label);
```

----------------------

### Demo

```html
<span v-label="status">{{ txt }}</span>
```

```javascript
new Vue({
  el: '...', // omitted 
  data: {
    status: false,
  };
  computed() {
    txt() {
      return this.status ? 'Active' : 'Inactive';
    },
  },
});
```
