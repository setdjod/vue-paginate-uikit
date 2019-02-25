<template>
  <ul class="uk-pagination uk-flex-center">
    <li v-if="firstLastButton" :class="[pageClass, firstPageSelected() ? disabledClass : '']">
      <a @click="selectFirstPage()" @keyup.enter="selectFirstPage()" :class="pageLinkClass">
        <span uk-icon="icon: chevron-double-left"></span>
      </a>
    </li>

    <li v-if="!(firstPageSelected() && hidePrevNext)" :class="[prevClass, firstPageSelected() ? disabledClass : '']">
      <a @click="prevPage()" @keyup.enter="prevPage()" :class="prevLinkClass">
        <span uk-icon="icon: chevron-left"></span>
      </a>
    </li>

    <li v-for="page in pages" :class="[pageClass, page.selected ? activeClass : '', page.disabled ? disabledClass : '']">
      <a v-if="page.disabled" :class="pageLinkClass">{{ page.content }}</a>
      <a v-else @click="handlePageSelected(page.index + 1)" @keyup.enter="handlePageSelected(page.index + 1)" :class="pageLinkClass">{{ page.content }}</a>
    </li>

    <li v-if="!(lastPageSelected() && hidePrevNext)" :class="[nextClass, lastPageSelected() ? disabledClass : '']">
      <a @click="nextPage()" @keyup.enter="nextPage()" :class="nextLinkClass">
        <span uk-icon="icon: chevron-right"></span>
      </a>
    </li>

    <li v-if="firstLastButton" :class="[pageClass, lastPageSelected() ? disabledClass : '']">
      <a @click="selectLastPage()" @keyup.enter="selectLastPage()" :class="pageLinkClass">
        <span uk-icon="icon: chevron-double-right"></span>
      </a>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    value: {
      type: Number
    },
    pageCount: {
      type: Number,
      required: true
    },
    forcePage: {
      type: Number
    },
    clickHandler: {
      type: Function,
      default: () => { }
    },
    pageRange: {
      type: Number,
      default: 3
    },
    marginPages: {
      type: Number,
      default: 0
    },
    pageClass: {
      type: String
    },
    pageLinkClass: {
      type: String
    },
    prevClass: {
      type: String
    },
    prevLinkClass: {
      type: String
    },
    nextClass: {
      type: String
    },
    nextLinkClass: {
      type: String
    },
    activeClass: {
      type: String,
      default: 'uk-active'
    },
    disabledClass: {
      type: String,
      default: 'uk-disabled'
    },
    firstLastButton: {
      type: Boolean,
      default: true
    },
    hidePrevNext: {
      type: Boolean,
      default: false
    }
  },
  beforeUpdate() {
    if (this.forcePage === undefined) return
    if (this.forcePage !== this.selected) {
      this.selected = this.forcePage
    }
  },
  computed: {
    selected: {
      get: function() {
        return this.value || this.innerValue
      },
      set: function(newValue) {
        this.innerValue = newValue
      }
    },
    pages: function () {
      let items = {}
      if (this.pageCount <= this.pageRange) {
        for (let index = 0; index < this.pageCount; index++) {
          let page = {
            index: index,
            content: index + 1,
            selected: index === (this.selected - 1)
          }
          items[index] = page
        }
      } else {
        const halfPageRange = Math.floor(this.pageRange / 2)

        let setPageItem = index => {
          let page = {
            index: index,
            content: index + 1,
            selected: index === (this.selected - 1)
          }

          items[index] = page
        }

        // 1st - loop thru low end of margin pages
        for (let i = 0; i < this.marginPages; i++) {
          setPageItem(i);
        }

        // 2nd - loop thru selected range
        let selectedRangeLow = 0;
        if (this.selected - halfPageRange > 0) {
          selectedRangeLow = this.selected - 1 - halfPageRange;
        }

        let selectedRangeHigh = selectedRangeLow + this.pageRange - 1;
        if (selectedRangeHigh >= this.pageCount) {
          selectedRangeHigh = this.pageCount - 1;
          selectedRangeLow = selectedRangeHigh - this.pageRange + 1;
        }

        for (let i = selectedRangeLow; i <= selectedRangeHigh && i <= this.pageCount - 1; i++) {
          setPageItem(i);
        }

        // 3rd - loop thru high end of margin pages
        for (let i = this.pageCount - 1; i >= this.pageCount - this.marginPages; i--) {
          setPageItem(i);
        }
      }
      return items
    }
  },
  data() {
    return {
      innerValue: 1,
    }
  },
  methods: {
    handlePageSelected(selected) {
      if (this.selected === selected) return

      this.innerValue = selected
      this.$emit('input', selected)
      this.clickHandler(selected)
    },
    prevPage() {
      if (this.selected <= 1) return

      this.handlePageSelected(this.selected - 1)
    },
    nextPage() {
      if (this.selected >= this.pageCount) return

      this.handlePageSelected(this.selected + 1)
    },
    firstPageSelected() {
      return this.selected === 1
    },
    lastPageSelected() {
      return (this.selected === this.pageCount) || (this.pageCount === 0)
    },
    selectFirstPage() {
      if (this.selected <= 1) return

      this.handlePageSelected(1)
    },
    selectLastPage() {
      if (this.selected >= this.pageCount) return

      this.handlePageSelected(this.pageCount)
    }
  }
}
</script>
