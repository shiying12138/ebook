<template>
  <div>
    <SearchTable
      :data="data"
    />
  </div>
</template>

<script>
  import SearchTable from '../../components/search/SearchTable'
  import {searchList} from '../../api'
  import {setNavigationBarTitle, showToast} from '../../api/wechat'

  export default {
    name: 'list',
    components: {SearchTable},
    data() {
      return {
        data: [],
        page: 1
      }
    },
    methods: {
      getSearchList() {
        const {key, text} = this.$route.query
        const params = {}
        if (key && text) {
          params[key] = text
        }
        params.page = this.page
        searchList(params).then(response => {
          const {data} = response.data
          if (data.length > 0) {
            this.data.push(...data)
          } else {
            showToast('没有更多数据了')
          }
        })
      }
    },
    onShow() {
      this.page = 1
      const {title} = this.$route.query
      this.getSearchList()
      setNavigationBarTitle(title)
    },
    onReachBottom() {
      this.page++
      this.getSearchList()
    }
  }
</script>

<style lang="scss" scoped>

</style>
