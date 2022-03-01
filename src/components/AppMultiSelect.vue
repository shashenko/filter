<script>
export default {
  name: "AppMultiSelect",
  props: {
    options: {
      type: Array,
      default: () => ([]),
    },
    value: {
      type: Array,
      default: () => ([]),
    },
    search: Boolean,
    disabled: Boolean,
    placeholder: String,
  },
  data() {
    return {
      isOpen: false,
      visibleOptions: [],
      selectedOptions: [],
      searchInput: '',
      isSearchTyping: false,
      searchTypingTimer: null,
    };
  },
  computed: {
    enabledOptions() {
      return this.options.filter(option => !option?.disabled)
    },
    selectedValues() {
      return this.selectedOptions.map(option => option.value)
    },
    allSelected() {
      return this.enabledOptions.length === this.selectedOptions.length
    },
  },
  methods: {
    closeDropDown($event) {
      console.log($event)
      if (!$event || !this.$el.contains($event.target)) {
        if (this.isOpen) {
          this.isOpen = false
        }
      }
    },
    toggleDropDown() {
      this.isOpen = !this.isOpen
    },
    selectOption(option) {
      if (option?.disabled) {
        return
      }
      const valueIndex = this.selectedOptions.findIndex(selectedOption => selectedOption.value === option.value)
      if (valueIndex > -1) {
        this.selectedOptions.splice(valueIndex, 1)
      } else {
        this.selectedOptions.push(option)
      }
      this.$emit('input', this.selectedValues)
    },
    selectAll() {
      this.selectedOptions = this.enabledOptions
    },
    unSelectAll() {
      this.selectedOptions = []
    },
    filterOptions() {
      if (!this.searchInput) {
        this.visibleOptions = this.options
        return
      }
      const reg = new RegExp(this.searchInput, 'gi')
      this.visibleOptions = this.options.filter(option => reg.test(option.label))
    },
    onSearchTyping() {
      if (!this.isSearchTyping) {
        this.filterOptions()
      }

      clearTimeout(this.searchTypingTimer)
      this.searchTypingTimer = setTimeout(() => {
        clearTimeout(this.searchTypingTimer)
        this.isSearchTyping = false
      }, 500)
    },
    onClickAll() {
      if (this.selectedOptions.length === this.enabledOptions.length) {
        this.unSelectAll()
      } else {
        this.selectAll()
      }
    },
    onClickApply() {
      this.closeDropDown()
      this.$emit('apply', this.selectedValues)
    },
    bind() {
      document.body.addEventListener('click', this.closeDropDown)
    },
    unbind() {
      document.body.removeEventListener('click', this.closeDropDown)
    },
  },
  mounted() {
    this.visibleOptions = this.options
    if (this.value.length) {
      this.selectedOptions = this.options.filter(option => this.value.includes(option.value))
    }
    this.bind()
  },
  destroyed() {
    this.unbind()
  },
}
</script>

<template>
  <div class="app-multi-select">
    <div class="app-multi-select__title" @click="toggleDropDown">
      {{ selectedOptions.length ? selectedOptions.map(option => option.label).join(', ') : placeholder }}
    </div>
    <div class="app-multi-select__dropdown" v-if="isOpen">
      <div class="app-multi-select__dropdown-head">
        <div class="app-multi-select__dropdown-action">
          <span @click="onClickAll">{{ allSelected ? 'Unselect all' : 'Select all' }}</span>
        </div>
        <div class="app-multi-select__dropdown-search">
          <input v-model="searchInput" @keyup="onSearchTyping" />
        </div>
      </div>
      <div class="app-multi-select__dropdown-list">
        <div class="app-multi-select__option" v-for="(option, index) in visibleOptions" :key="index">
          <label
              class="app-multi-select__option-label"
              :class="{
                'app-multi-select__option-label--disabled': option.disabled,
                'app-multi-select__option-label--selected': selectedValues.includes(option.value),
              }"
              @click="selectOption(option)"
          >
            <div class="app-multi-select__option-label-checkbox"></div>
            <div class="app-multi-select__option-label-text">{{ option.label }}</div>
          </label>
        </div>
      </div>
      <div class="app-multi-select__dropdown-foot">
        <button type="button" @click.prevent="onClickApply">Apply</button>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.app-multi-select {
  position: relative;
  width: 100%;
  max-width: 260px;

  &__title {
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 5px 10px;
    background-color: #fff;
    cursor: pointer;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  &__dropdown {
    position: absolute;
    top: calc(100% + 1px);
    left: 0;
    right: 0;
    display: flex;
    flex-direction: column;
    max-width: 450px;
    min-width: 220px;
    background-color: #fff;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 10px;
    white-space: nowrap;

    &-head {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 15px;
    }

    &-action,
    &-search {
      max-width: 50%;
    }

    &-action {
      span {
        color: #4e82c2;
        cursor: pointer;
      }
    }

    &-search {
      input {
        width: 100%;
        padding: 5px 10px;
        border-radius: 4px;
        border: 1px solid #ccc;
      }
    }

    &-list {
      max-height: 300px;
      overflow: hidden;
      overflow-y: auto;
      padding-bottom: 15px;
    }

    &-foot {
      margin-top: 15px;
      button {
        padding: 4px 10px;
        background-color: #364a63;
        border-color: #364a63;
        border-radius: 4px;
        color: #fff;
        cursor: pointer;
      }
    }
  }

  &__option {
    &:not(:last-child) {
      margin-bottom: 10px;
    }

    &-label {
      display: flex;
      cursor: pointer;

      &-checkbox {
        position: relative;
        width: 18px;
        height: 18px;
        margin-right: 10px;
        background-color: #fff;
        border: #dbdfea solid 2px;
        border-radius: 4px;
      }

      &-text {}

      &--selected &-checkbox {
        color: #fff;
        border-color: #2263b3;
        background-color: #2263b3;

        &:before {
          content: "";
          position: absolute;
          left: 4px;
          top: 1px;
          width: 4px;
          height: 8px;
          border: solid white;
          border-width: 0 2px 2px 0;
          -webkit-transform: rotate(45deg);
          -ms-transform: rotate(45deg);
          transform: rotate(45deg);
        }
      }

      &--disabled {
        opacity: 0.3;
      }
    }
  }

}
</style>
