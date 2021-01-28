<template>
  <div>
    <div class="content">
      <h2>{{ this.tvObj.title }}</h2>
      <p>{{ this.tvObj.info }}</p>
    </div> 
    <div class="img-box">
        <img :src="'https://images.weserv.nl/?url=' +this.tvObj.img" alt="">
    </div>
  </div>
</template>

<script>
export default {
    data () {
        return {
          // 给拿到的数据赋值给data里的tvObj对象,打印出来的东西是对象，所以定义一个对象,把数据放对象中
            tvObj:{
                img:" ",
                title:" ",
                info:" ",
            },
        };
    },
    created() {
    this.getData();
  },
  methods:{
    // 获取加载数据的方法
      getData(){
           this.axios
        .get(
          "https://bird.ioliu.cn/v2?url=https://m.douban.com/rexxar/api/v2/tv/" +this.$route.params.id)
          .then((res) => {
            // 给tvObj对象里的数据赋予从res.data中得到的值
              this.tvObj.img=res.data.cover_url;
              this.tvObj.title=res.data.title;
              this.tvObj.info=res.data.intro;
          });
      },
  },
};
</script>

<style lang="scss" scoped>
.img-box{
  img{
    width: 100%;
    height: 100%;
  }
}
</style>