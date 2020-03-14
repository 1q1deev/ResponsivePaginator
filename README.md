# Vue Responsive Pagination with flexible settings

Thanks to [Jason Watmore](https://github.com/cornflourblue) for the [pagination logic](https://github.com/cornflourblue/jw-paginate).

## Demo
[Demo Responsive Pagination](https://1q1deev.github.io/ResponsivePaginator/dist/demo)

## Installation

Use [npm](https://www.npmjs.com/) to install Responsive Pagination.

```bash
npm install responsive-pagination
```

## Usage

```vue
<template>
  <div id="app">
    <div>....
    <ResponsivePagination 
     ref="ResponsivePaginationRef"
     v-bind:total-items-prop="1000"
     v-on:set-page="Paginate"
     v-bind:class="'is-green'"/>
  </div>
</template>

import ResponsivePagination from 'responsive-pagination'

export default {
  name: 'App',
  components: {
    ResponsivePagination
  },
  methods: {
    Paginate(result) {
      // The result is an object with data, includes:
      // totalItems: 
      // currentPage: 
      // pageSize: 
      // totalPages: 
      // startPage:
      // endPage:
      // startIndex: 
      // endIndex:
      // pages:
    }
  }
}

<style>

.responsive-pagination.is-green a {
  background-color: hsl(143, 100%, 34%);
  border-color: hsl(143, 100%, 34%);
  color: white;
}

.responsive-pagination.is-green a.is-active {
  background-color: white;
  border-color: hsl(143, 100%, 34%);
  color: hsl(143, 100%, 34%);
}

</style>
```
## To change color

Use css and add a class to the paginator
```
<style>
.responsive-pagination.is-green a {
  background-color: hsl(143, 100%, 34%);
  border-color: hsl(143, 100%, 34%);
  color: white;
}

.responsive-pagination.is-green a.is-active {
  background-color: white;
  border-color: hsl(143, 100%, 34%);
  color: hsl(143, 100%, 34%);
}
</style>
```
```
<ResponsivePagination v-bind:class="'is-green'"/>
```
## Props

| Prop | Type | Required | Default | Description |
| ------ | ------ | ------ | ------ | ------ |
**total-items-prop**| Number | true | - | The total number of items to paginate
**page-size-prop**| Number | false | 10 | The number of items per page
**max-pages-prop**| Number | false | 10 | The maximum number of page navigation links to display, *max-pages-prop* is valid if *responsive-row-prop* is not active.
**current-page-prop**| Number | false | 1 | Current active page. You can use the method *setPage(page)* to set the value from the parent component after loading at any time. Example: ``` this.$refs.ResponsivePaginationRef.setPage(4);```
**own-size-prop**| Number | false | 16 | "font-size" in pixels. Determines the size of paginator elements. Valid if *responsive-size-prop* is false (default *true*)
**responsive-size-prop**| Boolean | false | true | Determines if the paginator size changes when a window is resized. 
**own-responsive-size-prop**| Object | false | { small:14, normal:16, medium:16, large:20, extraLarge:22 } | "font-size" in pixels at different screen widths: **small**: up 414px **normal**: from 415px up 768px **medium**: from 769px up 1023px **large:** from 1024px up 1407px **extraLarge:** from 1408px. Example: ```:own-responsive-size-prop="{ small:14, normal:16, medium:18, large:20, extraLarge:24 }"``` It works when a *responsive-size-prop* is *true* (default).
**type-prop**| Number | false | 1 | Sets the type of paginator. Possible options: *1* - The classic version with navigation buttons: "first", "prev", "next", "last" and pages. *2* - Option with navigation buttons: "prev", "next" and pages. *3* - Option without navigation buttons, only pages. *4* - Option with navigation buttons: "first", "last" and pages. *5* - Option with navigation buttons: "prev", "next" without pages.
**nav-item-prop**| Array | false | ['\u00AB', '\u2039', '\u203A', '\u00BB'] | Navigation buttons. Subject to change at any time. Example: ``` :nav-item-prop="['\u00AB', 'prev', 'next', '\u00BB']" ```
**responsive-row-prop**| Number | false | 1 | Sets the number of displayed links, so as to fit all the elements evenly into one or more (with *multi-row-prop* > 1) lines. Possible options: *0* - Not active, the number of links is determined by the *max-pages-prop* prop. *1* - Partially "responsive row" when loading. *2* - Any changes in the width of the paginator will lead to a recount of the links displayed simultaneously. **Note:** If *responsive-row-prop* is active, then the *max-pages-prop* parameter is ignored and will be applied only if it is not possible to determine the width of the paginator or track its changes.
**multi-row-prop**| Number | false | 1 | It works in conjunction with *responsive-row-prop* and sets the number of rows.


## Contributing
Pull requests are welcome.

## License
[MIT](https://opensource.org/licenses/MIT)
