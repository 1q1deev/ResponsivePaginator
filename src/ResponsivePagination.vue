<template>
  <ul class="responsive-pagination" v-bind:style="[paginationOwnSize ? {'font-size':paginationOwnSize + 'px'} : {}]">
    <PaginationLink v-if="getPaginationTypeOptions.first_last" ref="pagination_first_ref" class="first-link"
      v-bind:page="1" v-bind:disabled="getPagination.currentPage == 1" v-bind:text="navItemProp[0]" v-on:page-click="setPage" />
    <PaginationLink v-if="getPaginationTypeOptions.prev_next" ref="pagination_prev_ref" class="prev-link"
      v-bind:page="(getPagination.currentPage - 1)" v-bind:disabled="getPagination.currentPage == 1" v-bind:text="navItemProp[1]" v-on:page-click="setPage" />
    <PaginationLink v-for="(page, index) in getPagination.pages" v-bind:key="index" ref="pagination_page_ref"
      class="page-link" v-bind:display="getPaginationTypeOptions.display" v-bind:active="getPagination.currentPage == page" v-bind:page="page" v-bind:disabled="getPagination.currentPage == page" 
      v-bind:text="page" v-on:page-click="setPage" />
    <PaginationLink v-if="getPaginationTypeOptions.prev_next" ref="pagination_next_ref" class="next-link"
      v-bind:page="(getPagination.currentPage + 1)" v-bind:disabled="getPagination.currentPage == getPagination.totalPages" v-bind:text="navItemProp[2]" v-on:page-click="setPage" />
    <PaginationLink v-if="getPaginationTypeOptions.first_last" ref="pagination_last_ref" class="last-link"
      v-bind:page="getPagination.totalPages" v-bind:disabled="getPagination.currentPage == getPagination.totalPages" v-bind:text="navItemProp[3]" v-on:page-click="setPage" />
  </ul>
</template>

<script>
import PaginationLink from './PaginationLink'

export default {
  props: {
    totalItemsProp: {
      type: Number,
      required: true,
      validator(value) {
        return value > 0
      }
    },
    pageSizeProp: {
      type: Number,
      default: 10
    },
    maxPagesProp: {
      type: Number,
      default: 10
    },
    currentPageProp: {
      type: Number,
      default: 1
    },
    ownSizeProp: {
      type: Number
    },
    responsiveSizeProp: {
      type: Boolean,
      default: true,
    },
    ownResponsiveSizeProp: {
      type: Object,
      default(){
        return { small:14, normal:16, medium:18, large:22, extraLarge:24 }
      },
      validator(value) {
        let obj = { small:14, normal:16, medium:18, large:22, extraLarge:24 }
        for (const prop in obj) {
          if (!Object.prototype.hasOwnProperty.call(value, prop)) {
            return false
          }
          else if (typeof value[prop] !== 'number') {
            return false
          }
        }
        return true
      }
    },
    typeProp: {
      type: Number,
      default: 1,
      validator(value) {
        return [1, 2, 3, 4, 5].indexOf(value) !== -1
      }
    },
    navItemProp: {
      type: Array,
      default() {
        return ['\u00AB', '\u2039', '\u203A', '\u00BB']
      } 
    },
    responsiveRowProp: {
      type: Number,
      default: 1,
      validator(value) {
        return [0, 1, 2].indexOf(value) !== -1
      }
    },
    multiRowProp: {
      type: Number,
      default: 1,
      validator(value) {
        return value > 0
      }
    }
  },
  components: {
    PaginationLink
  },
    data: function () {
      return {
        paginationPageSize:this.pageSizeProp,
        paginationMaxPages:this.maxPagesProp,
        paginationCurrentPage:this.currentPageProp,
        paginationOwnSize:this.ownSizeProp,
        paginationResponsiveSize:this.responsiveSizeProp,
        paginationOwnResponsiveSize:this.ownResponsiveSizeProp,
        paginationType:this.typeProp,
        paginationResponsiveRow:this.responsiveRowProp,
        paginationMultiRow:this.multiRowProp,
        paginationTypeOptions:{
        1: {
          'first_last':true,
          'prev_next':true,
          'display':true
        },
        2: {
          'first_last':false,
          'prev_next':true,
          'display':true
        },
        3: {
          'first_last':false,
          'prev_next':false,
          'display':true
        },
        4: {
          'first_last':true,
          'prev_next':false,
          'display':true
          },
        5: {
          'first_last':false,
          'prev_next':true,
          'display':false
          }
        },
        responsiveData: {
          responsiveObserver: typeof ResizeObserver,
          responsiveInterval: typeof setInterval,
          elemWidth:0,
          oldMaxPagesValue:0,
          mediaQuery: {
            small:window.matchMedia("(max-width: 414px)"),
            normal:window.matchMedia("(min-width: 415px) and (max-width: 768px)"),
            medium:window.matchMedia("(min-width: 769px) and (max-width: 1023px)"),
            large:window.matchMedia("(min-width: 1024px) and (max-width: 1407px)"),
            extraLarge:window.matchMedia("(min-width: 1408px)"),
          }
        }
      }
    },
    created() {
      this.$emit('set-page', this.getPagination);
    },
    mounted() {
      if (this.paginationResponsiveSize) {
        for (let mq in this.responsiveData.mediaQuery) {
          let newListener = this.setResponsiveSize.bind(null, this.responsiveData.mediaQuery[mq], this.paginationOwnResponsiveSize[mq]);
          newListener();
          this.responsiveData.mediaQuery[mq].addListener(newListener)
        }
      }
      this.$nextTick(() => {
        if (this.paginationResponsiveRow > 1) {
          this.oldMaxPagesValue = this.paginationMaxPages;
          this.setResponsiveObserver();
        }
        else if (this.paginationResponsiveRow == 1) {
          this.oldMaxPagesValue = this.paginationMaxPages;
          this.setResponsiveElemSize();
        }
      });
    },
    computed: {
      getPagination() {
        return this.Paginate();
      },
      getPaginationTypeOptions() {
        return this.paginationTypeOptions[this.paginationType] ? this.paginationTypeOptions[this.paginationType] : this.paginationTypeOptions[1];
      }
    },
    methods: {
      setResponsiveSize(evt, size) {
        if (evt.matches) {
          this.paginationOwnSize = size;
        }
      },
      setResponsiveObserver() {
        if (this.responsiveData.responsiveObserver) {
          this.responsiveData.responsiveObserver = new ResizeObserver(entries => {
          entries.forEach(entry => {

            let elemWidth;

            if (entry.borderBoxSize ) {
              elemWidth = entry.borderBoxSize.inlineSize;
            }

            else if (entry.contentRect) {
              elemWidth = entry.contentRect.width;
            }
            
            this.paginationMaxPages = this.getResponsiveMaxPagesValue(false, elemWidth);

            })
          });
          
          this.responsiveData.responsiveObserver.observe(this.$el);
          
        }
        else {
          this.responsiveData.responsiveInterval = setInterval(() => this.setResponsiveElemSize(), 500); 
          this.setResponsiveElemSize();
        }
      },
      setPage(page) {
        this.paginationCurrentPage = page;
        this.$emit('set-page', this.getPagination)
      },
      setPropNextPage() {
        if (this.getPagination.currentPage != this.getPagination.totalPages)
        this.paginationCurrentPage = this.getPagination.currentPage + 1;
        this.$emit('set-page', this.getPagination)
      },
      setPropPrevPage() {
        if (this.getPagination.currentPage > 1)
        this.paginationCurrentPage = this.getPagination.currentPage - 1;
        this.$emit('set-page', this.getPagination)
      },
      Paginate(
          totalItems=this.totalItemsProp,
          currentPage=this.paginationCurrentPage,
          pageSize=this.paginationPageSize,
          maxPages=this.paginationMaxPages
      ) {
          if (currentPage === undefined) {
              currentPage = 1;
          }

          if (pageSize === undefined) {
              pageSize = 10;
          }

          if (maxPages === undefined) {
              maxPages = 10;
          }

          let totalPages = Math.ceil(totalItems / pageSize);

          if (currentPage < 1) {
              currentPage = 1;
          } else if (currentPage > totalPages) {
              currentPage = totalPages;
          }

          let startPage; 
          let endPage;

          if (totalPages <= maxPages) {
              startPage = 1;
              endPage = totalPages;
          } else {

              let maxPagesBeforeCurrentPage = Math.floor(maxPages / 2);
              let maxPagesAfterCurrentPage = Math.ceil(maxPages / 2) - 1;

              if (currentPage <= maxPagesBeforeCurrentPage) {
                  startPage = 1;
                  endPage = maxPages;
              } else if (currentPage + maxPagesAfterCurrentPage >= totalPages) {
                  startPage = totalPages - maxPages + 1;
                  endPage = totalPages;
              } else {
                  startPage = currentPage - maxPagesBeforeCurrentPage;
                  endPage = currentPage + maxPagesAfterCurrentPage;
              }
          }

          let startIndex = (currentPage - 1) * pageSize;
          let endIndex = Math.min(startIndex + pageSize - 1, totalItems - 1);

          let pages = Array.from(Array((endPage + 1) - startPage).keys()).map(i => startPage + i);

          return {
              totalItems: totalItems,
              currentPage: currentPage,
              pageSize: pageSize,
              totalPages: totalPages,
              startPage: startPage,
              endPage: endPage,
              startIndex: startIndex,
              endIndex: endIndex,
              pages: pages
          };
        },
      setResponsiveElemSize() {

        let elemWidth;

        try {
          elemWidth = this.$el.clientWidth;
        } catch (error) {
            return
          }
        
        this.paginationMaxPages = this.getResponsiveMaxPagesValue(false, elemWidth);

      },
      getElemNavSizes() {

        let linkSize;
        let firstLinkSize;
        let lastLinkSize;
        let prevLinkSize;
        let nextLinkSize;

        try {

          linkSize = this.$refs.pagination_page_ref[0] ? Math.ceil(this.$refs.pagination_page_ref[0].$el.getBoundingClientRect().width) : 0;
          firstLinkSize = this.$refs.pagination_first_ref ? Math.ceil(this.$refs.pagination_first_ref.$el.getBoundingClientRect().width) : 0;
          lastLinkSize = this.$refs.pagination_last_ref ? Math.ceil(this.$refs.pagination_last_ref.$el.getBoundingClientRect().width) : 0;
          prevLinkSize = this.$refs.pagination_prev_ref ? Math.ceil(this.$refs.pagination_prev_ref.$el.getBoundingClientRect().width) : 0;
          nextLinkSize = this.$refs.pagination_next_ref ? Math.ceil(this.$refs.pagination_next_ref.$el.getBoundingClientRect().width) : 0;
            
        } catch (error) {
            return null
          }

        return {
          linkSize:linkSize,
          firstLinkSize:firstLinkSize,
          lastLinkSize:lastLinkSize,
          prevLinkSize:prevLinkSize,
          nextLinkSize:nextLinkSize,
        }
      },
      getResponsiveMaxPagesValue(setValue=false, newElemWidth) {

        if (!this.getPaginationTypeOptions.display) {
          return 1
        }

        if (!newElemWidth) {
          return this.oldMaxPagesValue
        }
        
        let elemSize;
        let navSize;
        let linkInRow;
        

        if (this.responsiveData.elemWidth !== newElemWidth) {
          this.responsiveData.elemWidth = newElemWidth;
          elemSize = this.responsiveData.elemWidth;
        }

        if (!elemSize) {
          return this.oldMaxPagesValue
        }
        
        navSize = this.getElemNavSizes();
        
        if (!navSize) {
          return this.oldMaxPagesValue
        }

        if (this.paginationMultiRow <= 1) {

          linkInRow = Math.floor((elemSize - navSize.firstLinkSize - navSize.prevLinkSize - navSize.nextLinkSize - navSize.lastLinkSize) / navSize.linkSize);

        } else {

          linkInRow = 0;

          for (let i = 0; i < this.paginationMultiRow; i++) {

            let newRow = 0;

            if (i == 0) {
              newRow = elemSize - navSize.firstLinkSize - navSize.prevLinkSize;
            } 
            
            else if (i + 1 == this.paginationMultiRow) {
              newRow = elemSize - navSize.nextLinkSize - navSize.lastLinkSize;
            }

            else {
              newRow = elemSize;
            }

            for (let a = newRow; a > navSize.linkSize; a -= navSize.linkSize) {
              linkInRow += 1;
            }
          }
        }

        if (linkInRow <= 0) {
          linkInRow = 1;
        }

        this.oldMaxPagesValue = linkInRow;

      if (setValue) {
        this.paginationMaxPages = linkInRow;
      } 
      
      return linkInRow
      
    }
  }
}
</script>

<style scoped>

.responsive-pagination {
  list-style: none;
  -webkit-box-flex: 1;
  -moz-box-flex: 1;
  -webkit-flex: 1;
  -ms-flex:  1;
  -webkit-box: 1;
  -moz-box: 1;
  flex: 1;
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
  -webkit-flex-wrap: wrap;
  -ms-flex-wrap:wrap;
  flex-wrap: wrap;
  -webkit-box-direction: normal;
  -webkit-box-orient: horizontal;
  -moz-box-direction: normal;
  -moz-box-orient: horizontal;
  -webkit-flex-direction: row;
  -ms-flex-direction: row;
  flex-direction: row;
  -webkit-box-align: center;
  -moz-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;
  -webkit-box-pack: center;
  -moz-box-pack: center;
  -ms-flex-pack: center;
  -webkit-justify-content: center;
  justify-content: center;
  background: transparent !important;
  -webkit-align-content: center;
  -ms-flex-line-pack: center;
  align-content: center;
  font-size: 16px;
  line-height: 1.8;
  min-width: 100%;
  width: 100%;
  min-height: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}

</style>