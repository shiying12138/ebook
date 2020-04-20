<template>
  <div>
    <HomeBook
      :col="2"
      :row="category.length/2"
      :data="category"
      mode="category"
      :show-btn="false"
      :show-title="false"
      @onBookClick="onCategoryClick"
    />
  </div>
</template>

<script>
  import HomeBook from '../../components/home/HomeBook'
  import {bookCategoryList} from '../../api'

  export default {
    name: 'categoryList',
    components: {HomeBook},
    data() {
      return {
        category: []
      }
    },
    onShow() {
      bookCategoryList().then(response => {
        this.category = response.data.data
      })
    },
    methods: {
      onCategoryClick(category) {
        this.$router.push({
          path: '/pages/list/main',
          query: {key: 'categoryId', title: category.categoryText, text: category.category}
        })
      }
    }
  }
</script>

<style lang="scss" scoped>

</style>
