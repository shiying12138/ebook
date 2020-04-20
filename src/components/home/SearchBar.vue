<template>
  <div class="search-bar">
    <div class="search-bar-wrapper" @click="onSearchBarClick">
      <van-icon class="search" name="search" color="#858C96" size="16px"></van-icon>
      <input :focus="focus"
             :disabled="disabled"
             :maxlength="limit"
             :placeholder="hotSearch.length===0?'搜索':hotSearch"
             v-model="searchWord"
             @input="onChange"
             confirm-search="search"
             @confirm="onConfirm"
             placeholder-style="color:#ADB4BE;font-size: 15px"
             class="search-bar-input">
      <van-icon
        @click="onClearClick"
        class="clear"
        name="clear"
        color="#ccc"
        size="16px"
        v-if="searchWord.length>0"
      ></van-icon>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'SearchBar',
    props: {
      focus: {
        type: Boolean,
        default: false
      },
      disabled: {
        type: Boolean,
        default: false
      },
      limit: {
        type: Number,
        default: 50
      },
      hotSearch: {
        type: String,
        default: ''
      }
    },
    data() {
      return {
        searchWord: ''
      }
    },
    methods: {
      onSearchBarClick() {
        this.$emit('onClick')
      },
      onClearClick() {
        this.searchWord = ''
        this.$emit('onClear')
      },
      onChange(e) {
        const {value} = e.mp.detail
        console.log(value)
        this.$emit('onChange', value)
      },
      onConfirm(e) {
        const {value} = e.mp.detail
        this.$emit('onConfirm', value)
      },
      setValue(v) {
        this.searchWord = v
      },
      getValue() {
        return this.searchWord
      }
    }
  }
</script>

<style lang="scss" scoped>
  .search-bar {
    padding: 15px 15.5px;

    .search-bar-wrapper {
      height: 40px;
      box-sizing: border-box;
      display: flex;
      align-items: center;
      padding: 12px 17px;
      border-radius: 20px;
      background-color: #F5F7F9;
    }

    .search-bar-input {
      flex: 1;
      margin: 0 8px;
      color: #333;
      font-size: 15px;
    }

    .search, .clear {
      display: flex;
      align-items: center;
      height: 100%;
    }
  }

</style>
