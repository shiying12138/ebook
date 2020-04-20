<template>
  <div class="home-banner" @click="onClick">
    <div
      class="bg-img"
      :style="{backgroundImage:bgImage}"
      >
      <div class="title">{{title}}</div>
      <div class="sub-title">{{subTitle}}</div>
    </div>

  </div>
</template>

<script>
  export default {
    name: 'HomeBanner',
    props: {
      img: String,
      title: String,
      subTitle: String
    },
    computed: {
      bgImage() {
        return 'url(' + this.img + ')'
      }
    },
    methods: {
      onClick() {
        this.$emit('onClick')
      }
    }
  }
</script>

<style lang="scss" scoped>
  .home-banner {
    margin-top: 20px;
    padding: 0 20px;

    .bg-img {
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      height: 125px;
      border-radius: 10px;
      background-size: 100% 100%;
      background-repeat: no-repeat;

      .title {
        color: #ffffff;
        font-size: 18px;
        font-weight: 500;
      }

      .sub-title {
        background: #4585ff;
        padding: 5px 15px;
        border: 1px solid #ffffff;
        border-radius: 50px;
        margin-top: 10px;
        color: #ffffff;
        font-size: 14px;
      }
    }
  }

</style>
