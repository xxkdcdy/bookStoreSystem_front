<template>
    <div>
        <el-menu
                calss="el-menu"
                router
                mode="horizontal"
                background-color="white"
                text-color="#222"
                active-text-color="red"
                style="min-width: 1300px;">
            <el-menu-item v-for="(item,i) in navList" :key="i" :index="item.name">
                {{ item.navItem }}</el-menu-item>
            <change-password class="logout"></change-password>
            <div class="logout"  @click="logout">{{ ress }}  注销</div>
            <div class="text">叮叮书城</div>
            </el-menu>
    </div>
</template>
<script>
import ChangePassword from '@/components/admin/ChangePassword'
export default {
  name: 'NavMenu',
  components: {
    ChangePassword
  },
  data () {
    return {
      navList: this.checkAdmin(),
      ress: (this.getCookie('user') - 10) / 2
    }
  },
  methods: {
    logout() {
      // 删除cookie
      const date = new Date()
      date.setTime(date.getTime() + (0))
      document.cookie = 'user=' + ';' + 'expires=' + date.toUTCString()
      this.$router.replace({ path: '/login' })
      this.$message({
        showClose: true,
        message: '注销成功',
        type: 'success'
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
    },
    checkAdmin() {
      if ((this.getCookie('user') - 10) / 2 === 123456) {
        return [
          { name: '/index', navItem: '首页' },
          { name: '/library', navItem: '图书馆' },
          { name: '/jotter', navItem: '购物车' },
          { name: '/admin', navItem: '商品管理' }
        ]
      } else {
        return [
          { name: '/index', navItem: '首页' },
          { name: '/library', navItem: '图书馆' },
          { name: '/jotter', navItem: '购物车' }
        ]
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>

<style scoped>
    .logout{
        color: #44c068;
        float: right;
        padding: 20px;
        cursor:pointer
    }
    .logout:hover{
        color: #e5e747;
    }
    a{
        text-decoration: none;
    }
    span {
        pointer-events: none;
    }
    .text{
      color: #fafafa;
      letter-spacing: 0;
      font-size: 35px;
      text-shadow: 0px 1px 0px #999, 0px 2px 0px #888, 0px 3px 0px #777, 0px 4px 0px #666, 0px 5px 0px #555, 0px 6px 0px #444, 0px 7px 0px #333, 0px 8px 7px #001135
    }
</style>
