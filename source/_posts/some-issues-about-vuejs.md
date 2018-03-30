---
title: some issues about vuejs
categories:
  - coding
  - vue
tags:
  - vue
  - issue
date: 2018-03-09 10:05:08
update: 2018-03-09 10:05:08
---

### custom v-focus not work when there are many v-if in parent node

#### issue detail

<!--more-->

I define the custom directive "focus" in my component:

```
<script>
export default {
  name: 'demo',
  data () {
    return {
      show: true
    }
  },
  methods: {
    showInput () {
      this.show = false
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  }
}
</script>
```

<!--more-->

And this is my html template:

```
<template>
  <div>
    <input type="number" id="readonly" v-if="show">
    <button type="button" @click="showInput" v-if="show">show</button>
    <input type="number" id="timing" v-model="timing" v-if="!show" v-focus>
  </div>
</template>
```

But when I click the button, input#timing can't **autofocus**.

When I put input#readonly and button into a div and use only one **v-if**, `input#timing` can be **autofocus**:

```
<template>
  <div>
    <div v-if="show">
      <input type="number" id="readonly">
      <button type="button" @click="showInput">show</button>
    </div>
    <input type="number" id="timing" v-model="timing" v-if="!show" v-focus>
  </div>
</template>
```

#### answer

The directive's code is indeed running and focusing the `<input>`.

But it is being removed from the DOM! When this happens, it loses focus.
Check the console of the fiddle below: https://jsfiddle.net/acdcjunior/srfse9oe/21/

Another important point is that, when inserted is called, the `<input id="timing">` is in the DOM (as mentioned above),
but it is in the DOM at the wrong location (between `<p>a</p>` and `<p>b</p>` where it was never supposed to be).
This happens because Vue tries to reuse elements.

And when the nextTick triggers (see fiddle), it is in its correct placement (between `<p>c</p>` and `<p>d</p>`),
because Vue moved it to the correct location. And is this moving that is taking focus out.

And because nextTick runs after the DOM moving around is done, the focus persists (see below).

```
...
  directives: {
    focus: {
      inserted: function (el) {
        Vue.nextTick(() => el.focus())
      }
    }
  }
```

**details for the reason, follow the [issue](https://github.com/vuejs/vue/issues/7761) in github**
