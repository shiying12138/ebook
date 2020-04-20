<template>
  <div>
    <div class="home" v-if="isAuth">
      <SearchBar
        :disabled="true"
        @onClick="onSearchBarClick"
        :hot-search="hotSearch"
      />
      <HomeCard
        :data="homeCard"
        @onClick="onHomeBookClick"
      />
      <HomeBanner
        img="http://www.youbaobao.xyz/book/res/bg.jpg"
        title="mpvue2.0学习"
        subTitle="马上开始"
        @onClick="onBannerClick"
      ></HomeBanner>
      <div :style="{'marginTop':'23px'}">
        <HomeBook
          title="为你推荐"
          :col="3"
          :row="1"
          :data="recommend"
          mode="col"
          btn-text="换一批"
          @onMoreClick="recommendChange('recommend')"
          @onBookClick="onHomeBookClick"
        />
      </div>
      <div :style="{'marginTop':'23px'}">
        <HomeBook
          title="免费阅读"
          :col="2"
          :row="2"
          :data="freeRead"
          mode="row"
          btn-text="换一批"
          @onMoreClick="recommendChange('freeRead')"
          @onBookClick="onHomeBookClick"
        />
      </div>
      <div :style="{'marginTop':'23px'}">
        <HomeBook
          title="当前最热"
          :col="4"
          :row="1"
          :data="hotBook"
          mode="col"
          btn-text="换一批"
          @onMoreClick="recommendChange('hotBook')"
          @onBookClick="onHomeBookClick"
        />
      </div>
      <div :style="{'marginTop':'23px'}">
        <HomeBook
          title="分类"
          :col="2"
          :row="3"
          :data="category"
          mode="category"
          btn-text="查看全部"
          @onMoreClick="onCategoryMoreClick"
          @onBookClick="onCategoryClick"
        />
      </div>

    </div>
    <Auth v-if="!isAuth" @getUserInfo="init"/>
  </div>
</template>

<script>
  import SearchBar from '../../components/home/SearchBar'
  import ImageView from '../../components/base/ImageView'
  import HomeCard from '../../components/home/HomeCard'
  import HomeBanner from '../../components/home/HomeBanner'
  import HomeBook from '../../components/home/HomeBook'
  import {getHomeData, recommend, freeRead, hotBook} from '../../api'
  import {
    getSetting,
    getUserInfo,
    setStorageSync,
    getStorageSync,
    getUserOpenId,
    showLoading,
    hideLoading
  } from '../../api/wechat'
  import Auth from '../../components/base/Auth'

  export default {
    components: {Auth, HomeBook, HomeBanner, HomeCard, ImageView, SearchBar},
    data() {
      return {
        hotSearch: '',
        homeCard: {},
        banner: {},
        recommend: [],
        freeRead: [],
        hotBook: [],
        category: [],
        isAuth: true
      }
    },
    mounted() {
      this.init()
    },
    methods: {
      init() {
        this.getSetting()
      },
      getSetting() { // 授权
        getSetting('userInfo',
          () => {
            this.isAuth = true
            showLoading('正在加载中')
            this.getUserInfo()
          },
          () => {
            this.isAuth = false
          }
        )
      },
      getUserInfo() { // 获取用户信息
        const onOpenIdComplete = (openId, userInfo) => {
          this.getHomeData(openId, userInfo)
        }
        getUserInfo(
          (userInfo) => {
            console.log(userInfo)
            setStorageSync('userInfo', userInfo)
            const openId = getStorageSync('openId')
            if (!openId || openId.length === 0) {
              getUserOpenId(openId => onOpenIdComplete(openId, userInfo))
            } else {
              onOpenIdComplete(openId, userInfo)
            }
          },
          () => {
            console.log('fail...')
          }
        )
      },
      recommendChange(key) {
        switch (key) {
          case 'recommend':
            recommend().then(response => {
              this.recommend = response.data.data
            })
            break
          case 'freeRead':
            freeRead().then(response => {
              this.freeRead = response.data.data
            })
            break
          case 'hotBook':
            hotBook().then(response => {
              this.hotBook = response.data.data
            })
            break
        }
      },
      onCategoryMoreClick() {
        this.$router.push({
          path: '/pages/categoryList/main'
        })
      },
      onCategoryClick(category) {
        this.$router.push({
          path: '/pages/list/main',
          query: {key: 'categoryId', title: category.categoryText, text: category.category}
        })
      },
      onHomeBookClick(book) {
        this.$router.push({
          path: '/pages/detail/main',
          query: {
            fileName: book.fileName
          }
        })
      },
      onSearchBarClick() {
        // 跳转到搜索页
        this.$router.push({
          path: '/pages/search/main',
          query: {
            hotSearch: this.hotSearch
          }
        })
      },
      onBannerClick() {
        console.log('hhhh')
      },
      getHomeData(openId, userInfo) {
        getHomeData({openId}).then(response => {
          const {data: {hotSearch: {keyword}, shelf, banner, recommend, freeRead, hotBook, category, shelfCount}} = response.data
          this.hotSearch = keyword
          this.banner = banner
          this.recommend = recommend
          this.freeRead = freeRead
          this.hotBook = hotBook
          this.category = category
          this.homeCard = {
            bookList: shelf,
            num: shelfCount,
            userInfo
          }
          hideLoading()
        }).catch(() => {
          hideLoading()
        })
      }
    }
  }
</script>

<style lang="scss" scoped>

</style>
