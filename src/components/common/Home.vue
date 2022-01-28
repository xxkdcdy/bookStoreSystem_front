<template>
    <div>
        <nav-menu class="nav-menu"></nav-menu>
        <keep-alive><router-view></router-view></keep-alive>
    </div>
</template>

<script>
import NavMenu from '@/components/common/NavMenu'
export default {
  components: { NavMenu },
  methods: {
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
    .nav-menu {
        /*margin-bottom: 40px;*/
        box-shadow: 0 2px 4px 0 rgba(0,0,0,.05);
    }
</style>
