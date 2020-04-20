<template>
  <div>
    <DetailBook :book="book"/>
    <DetailStat
      :readers="book.readers"
      :readerNum="book.readerNum"
      :rankNum="book.rankNum"
      :rankAvg="book.rankAvg"
    />
    <DetailRate
      :rateValue="book.rateValue"
      @onRateChange="onRateChange"
    />
    <DetailContents
      v-if="isShow"
      :contents="contents"
      @readBook="readBook"
    />
    <div class="detail-bottom">
      <div class="detail-btn-wrapper">
        <van-button
          :custom-class="isInShelf ? 'detail-btn-remove' : 'detail-btn-shelf'"
          round
          @click="handleShelf"
        >
          {{isInShelf ? '移出书架' : '加入书架'}}
        </van-button>
      </div>
      <div class="detail-btn-wrapper">
        <van-button
          custom-class="detail-btn-read"
          round
          @click="() => readBook()"
        >
          阅读
        </van-button>
      </div>
    </div>
  </div>
</template>

<script>
  import DetailBook from '../../components/detail/DetailBook'
  import {bookDetail, bookRankSave, bookContents, bookIsInShelf, bookShelfSave, bookShelfRemove} from '../../api'
  import {getStorageSync} from '../../api/wechat'
  import DetailStat from '../../components/detail/DetailStat'
  import DetailRate from '../../components/detail/DetailRate'
  import DetailContents from '../../components/detail/DetailContents'

  export default {
    name: 'detail',
    components: {DetailContents, DetailRate, DetailStat, DetailBook},
    data() {
      return {
        book: {},
        contents: [],
        isInShelf: false,
        isShow: true
      }
    },
    beforeMount() {
      this.isShow = false
    },
    mounted() {
      this.getBookDetail()
      this.getBookContents()
      this.getBookIsInShelf()
    },
    methods: {
      handleShelf() {
        const openId = getStorageSync('openId')
        const {fileName} = this.$route.query
        if (!this.isInShelf) {
          this.isInShelf = false
          bookShelfSave({openId, fileName}).then(this.getBookIsInShelf)
        } else {
          this.isInShelf = true
          const vue = this
          mpvue.showModal({
            title: '提示',
            content: `是否将${this.book.title}移出书架`,
            success(res) {
              if (res.confirm) {
                bookShelfRemove({openId, fileName}).then(() => {
                  vue.getBookIsInShelf()
                })
              }
            }
          })
        }
      },
      onRateChange(value) {
        const openId = getStorageSync('openId')
        const {fileName} = this.$route.query
        bookRankSave({openId, fileName, rank: value}).then(() => {
          this.getBookDetail()
        })
      },
      getBookDetail() {
        const openId = getStorageSync('openId')
        const {fileName} = this.$route.query
        if (openId && fileName) {
          bookDetail({openId, fileName}).then(response => {
            this.book = response.data.data
          })
        }
      },
      getBookContents() {
        const {fileName} = this.$route.query
        if (fileName) {
          bookContents({fileName}).then(response => {
            this.contents = response.data.data
            this.isShow = true
          })
        }
      },
      getBookIsInShelf() {
        const openId = getStorageSync('openId')
        const {fileName} = this.$route.query
        if (openId && fileName) {
          bookIsInShelf({openId, fileName}).then(response => {
            const {data} = response.data
            data.length === 0 ? this.isInShelf = false : this.isInShelf = true
            console.log(this.isInShelf)
          })
        }
      },
      readBook(href) {
        const query = {
          fileName: this.book.fileName,
          opf: this.book.opf
        }
        if (href) {
          const index = href.indexOf('/')
          if (index >= 0) {
            query.navigation = href.slice(index + 1)
          } else {
            query.navigation = href
          }
        }
        if (this.book && this.book.fileName) {
          this.$router.push({
            path: '/pages/read/main',
            query
          })
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  .detail-bottom {
    position: fixed;
    bottom: 0;
    width: 100%;
    height: 60px;
    background: #fff;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    padding: 0 15px;
    border-top: 1px solid #eee;
    box-shadow: 0 -2px 4px 0 rgba(0, 0, 0, .1);

    .detail-btn-wrapper {
      flex: 1;
    }
  }

</style>
<style lang="scss">
  .detail-bottom {
    .detail-btn-read {
      width: 100%;
      border: none;
      color: #fff;
      background: #1EA3F5;
      margin-left: 7.5px;
    }

    .detail-btn-shelf {
      width: 100%;
      color: #1EA3F5;
      background: #fff;
      border: 1px solid #1EA3F5;
      margin-right: 7.5px;
    }

    .detail-btn-remove {
      width: 100%;
      color: #F96128;
      background: rgba(255, 175, 155, .3);
      border: 1px solid #FFAF9B;
      margin-right: 7.5px;
    }
  }
</style>
