# Vue Permission Directive
### FK v-permission


Allows you to show or hide the element that contains this directive.

Usage:

`<my-element v-permission="'permission'">This can or cannot show to user...</my-element>`

Literal use:

`<my-element v-permission.literal="permission">This can or cannot show to user...</my-element>`

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
const { util, FragmentFactory } = Vue
const isArray = Array.isArray
const isProduction = process.env.NODE_ENV !== 'production'

/* Directive to show a v-cannot element */
Vue.directive('cannot', function () {
  return
})

Vue.directive('permission', {

  bind() {
    this.permissions = [] /* IMPORT FROM YOUR CHOICE/SERVICE */

    let el = this.el
    if(!el.__vue__) {
      //check v-cannot block
      var next = el.nextElementSibling
      if (next && util.getAttr(next, 'v-cannot') !== null) {
        util.remove(next)
        this.elseEl = next
      }
      //check permission block
      this.anchor = util.createAnchor('v-permission')
      util.replace(el, this.anchor)
    } else {
      isProduction && util.warn(
        'v-permission="' + this.expression + '" cannot be ' +
        'used on an instance root element.',
        this.vm
      )
      this.invalid = true
    }
  },

  hasPermission(value) {
    if(isArray(this.permissions)) {
      return this.permissions.indexOf(value) > -1
    } else {
      util.warn('this.permissions needs to be an Array')
    }
  },

  update(value) {
    if(this.invalid) return
    if(this.hasPermission(value)) {
      if(! this.frag) {
        this.insert()
        this.updateRef(value)
      }
    } else {
      this.updateRef(value)
      this.remove()
    }
  },

  insert() {
    if (this.elseFrag) {
      this.elseFrag.remove()
      this.elseFrag = null
    }
    if(! this.factory) {
      this.factory = new FragmentFactory(this.vm, this.el)
    }
    this.frag = this.factory.create(this._host, this._scope, this._frag)
    this.frag.before(this.anchor)
  },

  remove() {
    if(this.frag){
      this.frag.remove()
      this.frag = null
    }
    if (this.elseEl && !this.elseFrag) {
      if (!this.elseFactory) {
        this.elseFactory = new FragmentFactory(
          this.elseEl._context || this.vm,
          this.elseEl
        )
      }
      this.elseFrag = this.elseFactory.create(this._host, this._scope, this._frag)
      this.elseFrag.before(this.anchor)
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
        refs.push(util.findVmFromFrag(this._frag))
      } else {
        refs[key] = util.findVmFromFrag(this._frag)
      }
    } else {
      if (Array.isArray(refs)) {
        refs.$remove(util.findVmFromFrag(this._frag))
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
    if (this.elseFrag) {
      this.elseFrag.destroy()
    }
  }
})
```

Created by [jjsquad](http://github.com/jjsquad)
