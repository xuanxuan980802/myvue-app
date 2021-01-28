<template>
  <div>
    <ul>
      <li
        class="tv-item"
        v-for="tv in tvList"
        :key="tv.id"
        @click="getDetail(tv.id)"
      >
        <div class="img-box">
          <img :src="'https://images.weserv.nl/?url=' + tv.cover.url" alt="" />
        </div>
        <div class="content">
          <h3>{{ tv.title }}</h3> 
          <p>{{ tv.info }}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script> 
export default {
  data() {
    return {
      //下面请求接口是为了给tvList赋值
      tvList: [],
      start: 0,
      // 标识位：作用当数据大于50之后, 停止请求数据,默认上次请求结束，页面一加载结果为true,本次结果结束,拿到了,才允许发送下次请求
      isFinish: true,
    };
  },
  // 页面的初始数据，一加载就开始，调用getData方法，拿到方法里的数据
  created() {
    this.getData();
  },
  // 处理揽加载，页面加载完才会滚动实行揽加载，调用lazyLoad方法
  mounted() {
    this.lazyLoad();
  },
  methods: {
    // 封装一个 getData方法，在此方法里请求接口数据
    getData() {
      // 给getData加一个判断，如果
      if (this.isFinish) {
        // res里的数据还没拿到，发送请求也没意义，没有数据传到
        this.isFinish = false;
        this.axios
        .get( 
          "https://bird.ioliu.cn/v2?url=https://m.douban.com/rexxar/api/v2/subject_collection/tv_domestic/items", 
          { 
            params: {
        // 页面一加载时的默认值：count：每页有几个数据，start：从第几页开始
              //start是从第几个元素开始取 
              start: this.start,
              // count是每次加载出来的数有几个
              count:10,
            },
          }
        )
        // 拿到返回的结果，res拿到的是data里的subject_collection_items
        .then((res) =>{
          // this.tvList等于一个数组，数组里边每一次页面揽加载的数据都存在,拼接每一次新获取的数据，而不是替换数据，叫做数组的拼接，两种方法，第一种使用扩展运算符
            this.tvList=[
                ...this.tvList,
                ...res.data.subject_collection_items,
            ];
            // 当res里的数据拿到之后，才能允许发送下一条请求
            this.isFinish=true;
        });
      }
    },
    // 处理揽加载的方法
    lazyLoad(){
      // 用HTML，Dom元素拿到整个页面的高度,
        let htmlDom=document.documentElement;
        // 声明的页面高度，默认值为0
        let fullH=0;
        // 拿到HTML，Dom元素中设备的高度,就是可视窗口的高度
        let deviceH=htmlDom.clientHeight;
        console.log(deviceH);
        // 滚动条滚出去的距离,默认值为0,scrollT的值是页面滚出去屏幕多远
        let scrollT = 0;

// 打印发现fullH = scrollT + deviceH时,说明页面滚到底部了,在此事件处理函数中this指向 window。如果换成箭头函数就没有this, 就会取外边的this。
        // 监听滚动条滚动的事件
        window.onscroll=() =>{
          //获取htmlDom元素中的页面的高度,给页面的高度赋值
            fullH=htmlDom.offsetHeight;
          //获取htmlDom元素中的滚动条滚出去的距离, 给滚动条赋值
            scrollT=htmlDom.scrollTop;
          // 如果当等式成立,当页面滚动到底部,if条件成立后,就给页面重新加载新数据
            if(fullH == deviceH + scrollT){
              // 请求新数据即下一页数据,重新获取getData方法,如果滚动到底部，就给start加10,start从0开始,页面总共有50条数据,从0到40,每次申请十条数据,一次10个,不能无休止的请求新数据,要有条件判断,到50条结束获得新数据。什么时候可以请求新数据。
              if(this.start<40){
                  this.start += 10;
                  this.getData();
              }  
            }
        };
    },
    // 跳转电视剧详情页
    getDetail(id){
        this.$router.push("/tvDetail/" + id);
    }
  },
};
</script>

<style lang="scss" scoped>
.tv-item{
    display:flex;
    padding:0.2rem 0;
    border-bottom: 1px solid #ccc;
    .img-box{
        flex:3;
        img{
            width: 100%;
            height: 100%;
        }
    }
    .content{
        flex:5;
        padding:0.8rem;
        text-align: center;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        h3{
            font-weight: bold;
        }
    }
}
</style>