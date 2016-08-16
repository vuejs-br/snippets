# Vue Permission Directive
### FK v-permission


Allows you to show or hide the element that contains this directive.

Usage:

`<my-element v-permission"'permission'">This can or cannot show to user...</my-element>`

Literal use:

`<my-element v-permission"permission">This can or cannot show to user...</my-element>`

PS: here 'permission' its a string value, not object.

Code:

``` javascript
/*
 * Permissions Vue Directive
 *
 * You need import from your choice/service an permissions Array
 * and set in `this.permissions` on bind() method
 * Feel it free to modify/update this code =)
 */
import Vue from 'vue'

Vue.directive('permission', {

  bind() {
    this.permissions = [] /* IMPORT FROM YOUR CHOICE/SERVICE */

    let el = this.el
    if(!el.__vue__) {
      this.anchor = Vue.util.createAnchor('v-permission')
      Vue.util.replace(el, this.anchor)
    } else {
      process.env.NODE_ENV !== 'production' && Vue.util.warn(
        'v-permission="' + this.expression + '" cannot be ' +
        'used on an instance root element.',
        this.vm
      )
      this.invalid = true
    }
  },

  hasPermission(value) {
    return this.permissions.indexOf(value) > -1
  },

  update(value) {
    if(this.invalid) return
    if(this.hasPermission(value)) {
      this.insert()
      this.updateRef(value)
    } else {
      this.updateRef(value)
      this.remove()
    }
  },

  insert() {
    if(! this.frag) {
      this.factory = new Vue.FragmentFactory(this.vm, this.el)
    }
    this.frag = this.factory.create(this._host, this._scope, this._frag)
    this.frag.before(this.anchor)
  },

  remove() {
    if(this.frag){
      this.frag.remove()
      this.frag = null
    }
  },

  updateRef(value) {
    var ref = this.descriptor.ref
    if (!ref) return
    var hash = (this.vm || this._scope).$refs
    var refs = hash[ref]
    var key = this._frag.scope.$key
    if (!refs) return
    if (value) {
      if (Array.isArray(refs)) {
        refs.push(findVmFromFrag(this._frag))
      } else {
        refs[key] = findVmFromFrag(this._frag)
      }
    } else {
      if (Array.isArray(refs)) {
        refs.$remove(findVmFromFrag(this._frag))
      } else {
        refs[key] = null
        delete refs[key]
      }
    }
  },

  unbind() {
    if (this.frag) {
      this.frag.destroy()
    }
  }
})
```

Created by [jjsquad](http://github.com/jjsquad)
