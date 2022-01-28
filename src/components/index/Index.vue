<template >
<div class="div">
  <!-- 滑动图  第一个main -->
  <el-main>
    <div class="banner">
      <div class="item">
        <img :src="dataList[currentIndex]" style="height: 100%; width: 100%">
      </div>
      <div class="page" v-if="this.dataList.length > 1">
        <ul>
          <li @click="gotoPage(prevIndex)">&lt;</li>
          <li v-for="(item,index) in dataList" @click="gotoPage(index)" :class="{'current':currentIndex == index}" v-bind:key="index">  {{index+1}}</li>
          <li @click="gotoPage(nextIndex)">&gt;</li>
        </ul>
      </div>
    </div>
  </el-main>
  <!-- 新品标题  第一个header -->
  <el-header><div class="text">新品</div></el-header>
  <!-- 新品  第二个main -->
  <el-main class="el-main">
    <el-row style="height: 200px;">
      <el-tooltip effect="dark" placement="right"
                  v-for="item in newBooks.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                  :key="item.id">
        <p slot="content" style="font-size: 14px;margin-bottom: 6px;">《{{item.title}}》</p>
        <p slot="content" style="font-size: 13px;margin-bottom: 6px">
          <span>{{item.author}}</span> /
          <span>{{item.date}}</span> /
          <span>{{item.price}}元</span>
        </p>

        <el-card style="width: 135px;margin-bottom: 20px;height: 233px;float: left;margin-right: 40px" class="book"
                 bodyStyle="padding:10px" shadow="hover">
          <router-link target="_blank" :to="{
                    name:BookMsg,
                    query:{
                    id:item.id,
                    title:item.title,
                    cover:item.cover,
                    price:item.price,
                    author:item.author,
                    date:item.date,
                    newOld:item.newOld,
                    contact:item.contact,
                    phone:item.phone,
                    qq:item.qq,
                    weChat:item.weChat,
                    abs:item.abs,
                    img_1:item.img_1,
                    img_2:item.img_2,
                    img_3:item.img_3,
                    img_4:item.img_4,
                    img_5:item.img_5,
                    }}" class="cover" @click.native="sendBookMsg(item)">
            <img :src="item.cover" alt="封面">
          </router-link>
          <div class="info">
            <div class="title">
              <a href="">{{item.title}}</a>
            </div>
          </div>
          <div class="author" style="color: #ee231e;">{{item.price}}元 </div>
        </el-card>
      </el-tooltip>
    </el-row>
  </el-main>
  <!-- 热卖标题  第二个header -->
  <el-header><div class="text">热卖</div></el-header>
  <!-- 热卖  第三个main -->
  <el-main class="el-main">
    <el-row style="height: 200px;">
      <el-tooltip effect="dark" placement="right"
                  v-for="item in hotBooks.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                  :key="item.id">
        <p slot="content" style="font-size: 14px;margin-bottom: 6px;">《{{item.title}}》</p>
        <p slot="content" style="font-size: 13px;margin-bottom: 6px">
          <span>{{item.author}}</span> /
          <span>{{item.date}}</span> /
          <span>{{item.price}}元</span>
        </p>

        <el-card style="width: 135px;margin-bottom: 20px;height: 233px;float: left;margin-right: 40px" class="book"
                 bodyStyle="padding:10px" shadow="hover">
          <router-link target="_blank" :to="{
                    name:BookMsg,
                    query:{
                    id:item.id,
                    title:item.title,
                    cover:item.cover,
                    price:item.price,
                    author:item.author,
                    date:item.date,
                    newOld:item.newOld,
                    contact:item.contact,
                    phone:item.phone,
                    qq:item.qq,
                    weChat:item.weChat,
                    abs:item.abs,
                    img_1:item.img_1,
                    img_2:item.img_2,
                    img_3:item.img_3,
                    img_4:item.img_4,
                    img_5:item.img_5,
                    }}" class="cover" @click.native="sendBookMsg(item)">
            <img :src="item.cover" alt="封面">
          </router-link>
          <div class="info">
            <div class="title">
              <a href="">{{item.title}}</a>
            </div>
          </div>
          <div class="author" style="color: #ee231e;">{{item.price}}元 </div>
        </el-card>
      </el-tooltip>
    </el-row>
  </el-main>
  <!-- 联系窗口  第一个footer -->
  <el-footer>@xxkdcdy</el-footer>
</div>
</template>

<script>
export default {
  name: 'Index',
  components: {
  },
  data() {
    return {
      newBooks: [],
      hotBooks: [],
      currentPage: 1,
      pageSize: 7,
      dataList: ['http://img60.ddimg.cn/upload_img/00877/202201/750x315_fr_20220106-1641520548.jpg',
        'http://img61.ddimg.cn/upload_img/00785/xssd202201/750-315-1641289230.jpg',
        'http://img60.ddimg.cn/2022/1/4/2022010416041397759.jpg'],
      currentIndex: 0,
      timer: null,
      BookMsg: 'BookMsg',
      uid: ''
    }
  },
  // 挂载数据后执行
  created () {
    this.loadNewBooks()
    this.loadHotBooks()
    this.show3 = false
  },
  mounted() {
    this.runInv()
  },
  computed: {
    prevIndex() {
      if (this.currentIndex === 0) {
        return this.dataList.length - 1
      } else {
        return this.currentIndex - 1
      }
    },
    nextIndex() {
      if (this.currentIndex === this.dataList.length - 1) {
        return 0
      } else {
        return this.currentIndex + 1
      }
    }
  },
  methods: {
    loadNewBooks () {
      this.$axios.get('/newBook')
        .then(resp => {
          if (resp && resp.status === 200) {
            this.newBooks = resp.data
          }
        })
    },
    runInv() {
      this.timer = setInterval(() => {
        this.gotoPage(this.nextIndex)
      }, 5000)
    },
    gotoPage(index) {
      this.currentIndex = index
    },
    loadHotBooks () {
      this.$axios
        .post('hotBook', {
          uid: (this.getCookie('user') - 10 / 2).toString()
        }).then(res => {
        console.log(res)
        this.hotBooks = res.data
      }).catch(error => {
        console.log(error)
      })
    },
    getCookie(name) {
      const strCookie = document.cookie// 获取cookie字符串
      const arrCookie = strCookie.split('; ')// 分割
      // 遍历匹配
      for (let i = 0; i < arrCookie.length; i++) {
        const arr = arrCookie[i].split('=')
        if (arr[0] === name) {
          return arr[1]
        }
      }
      return ''
    }
  }
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
}
.div{
  background-color: #ffebda;
}
ul li {
  list-style: none;
  float: left;
  width: 30px;
  height: 40px;
  line-height: 40px;
  text-align: center;
  cursor: pointer;
  color: rgba(255,255,255,.8);
  font-size: 14px;
}
.banner {
  max-width: 1200px;
  margin: 0 auto;
  position: relative;
  margin-top: 60px;
}
.banner img {
  width: 100%;
  display: block;
}
.banner .page {
  background: rgba(0,0,0,.5);
  position: absolute;
  right: 0;
  bottom: 0;
  width: 100%;
}
.banner .page ul {
  float: right;
}
.current {
  color: #ff6700;
}
.el-main{
  margin: 40px 50px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  color: #333;
}

.text{
  color: #fafafa;
  letter-spacing: 0;
  font-size: 50px;
  text-shadow: 0px 1px 0px #999, 0px 2px 0px #888, 0px 3px 0px #777, 0px 4px 0px #666, 0px 5px 0px #555, 0px 6px 0px #444, 0px 7px 0px #333, 0px 8px 7px #001135
}

.cover {
  width: 115px;
  height: 172px;
  margin-bottom: 7px;
  overflow: hidden;
  cursor: pointer;
}

img {
  width: 115px;
  height: 172px;
  /*margin: 0 auto;*/
}

.title {
  font-size: 14px;
  text-align: left;
}

.author {
  color: #333;
  width: 102px;
  font-size: 13px;
  margin-bottom: 6px;
  text-align: left;
}

.abstract {
  display: block;
  line-height: 17px;
}

.el-icon-delete {
  cursor: pointer;
  float: right;
}

.switch {
  display: flex;
  position: absolute;
  left: 780px;
  top: 25px;
}

a {
  text-decoration: none;
}

a:link, a:visited, a:focus {
  color: #3377aa;
}
</style>
