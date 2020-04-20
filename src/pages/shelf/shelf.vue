<template>
  <div>
    <ShelfUserInfo
      :user-info="userInfo"
      :read-day="readDay"
      :num="shelfList.length-1"
    />
    <ShelfList :shelf-list="shelfList"/>
  </div>
</template>
<script>
  import {bookIsInShelf, userDay} from '../../api'
  import {getStorageSync} from '../../api/wechat'
  import ShelfUserInfo from '../../components/shelf/ShelfUserInfo'
  import ShelfList from '../../components/shelf/ShelfList'

  export default {
    name: 'shelf',
    components: {ShelfList, ShelfUserInfo},
    data() {
      return {
        shelfList: [],
        userInfo: {},
        readDay: 0,
        num: 0
      }
    },
    onShow() {
      const openId = getStorageSync('openId')
      this.userInfo = getStorageSync('userInfo')
      userDay({openId}).then(response => {
        this.readDay = response.data.data.day
      })
      bookIsInShelf({openId}).then(response => {
        this.shelfList = response.data.data
        this.shelfList.push({})
      })
    }
  }
</script>
<style lang="scss" scoped>

</style>
