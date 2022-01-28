<template>
  <el-main class="div">
    <!-- 表头 -->
    <el-table :data=this.goodsList
              style="width: 100%"
              height="420"
              show-summary :summary-method="getSummaries">
      <el-table-column
        align="center"
        label="商品ID">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p>书名: {{ scope.row.title }}</p>
            <p>单价: {{ scope.row.price }}</p>
            <div slot="reference" class="name-wrapper">
              <el-tag size="medium">{{ scope.row.bid }}</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="数量">
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.num }}</span>
        </template>
      </el-table-column>
      <el-table-column
        label="操作"
        align="center">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleEditUp(scope.$index, scope.row)">增加</el-button>
          <el-button
            size="mini"
            @click="handleEditDown(scope.$index, scope.row)">减少</el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-button
      type="primary"
      style="width: 40%; border: none; margin: 20px"
      v-on:click="Bill">结账</el-button>
    <!-- 已经购买 -->
    <el-header><el-button @click="show3 = !show3">点击查看已购订单</el-button></el-header>
    <div style="margin-top: 20px; height: 200px;">
    <el-collapse-transition>
      <div v-show="show3">
    <el-table :data=this.goodsListBuy
              style="width: 100%">
      <el-table-column
        align="center"
        label="商品ID">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p>书名: {{ scope.row.title }}</p>
            <p>单价: {{ scope.row.price }}</p>
            <div slot="reference" class="name-wrapper">
              <el-tag size="medium">{{ scope.row.bid }}</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="数量">
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.num }}</span>
        </template>
      </el-table-column>
      <el-table-column
        label="状态"
        align="center">
        <span>已付款</span>
      </el-table-column>
    </el-table>
      </div>
    </el-collapse-transition>
    </div>
  </el-main>
</template>
<script>
export default {
  inject: ['reload'],
  name: 'life',
  data() {
    return {
      cartStatus: 'account', // 购物车状态，account结算，edit删除编辑状态
      sumPrice: 0, // 合计金额
      goodsList: [], // 商品列表
      goodsListBuy: [],
      show3: false
    }
  },
  watch: {

  },
  created() {
    this.selectCart()
  },
  methods: {
    selectCart() {
      this.$axios
        .post('cartByUid', {
          uid: (this.getCookie('user') - 10 / 2).toString()
        }).then(res => {
        console.log(res)
        this.goodsList = res.data
        res.data.forEach((raw) => {
          this.sumPrice = this.sumPrice + raw.price * raw.num
        })
      }).catch(error => {
        console.log(error)
      })
      this.$axios
        .post('cartBuyByUid', {
          uid: (this.getCookie('user') - 10 / 2).toString()
        }).then(res => {
        console.log(res)
        this.goodsListBuy = res.data
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
    },
    handleEditUp(index, row) {
      console.log(index, row)
      const newNum = row.num + 1
      this.$axios
        .post('updateCart', {
          uid: row.uid,
          bid: row.bid,
          num: newNum
        }).then(res => {
        console.log(res)
        this.reload()
        this.$message({
          showClose: true,
          message: '增加成功',
          type: 'success'
        })
      }).catch(error => {
        console.log(error)
        this.$message({
          showClose: true,
          message: '增加失败',
          type: 'warning'
        })
      })
    },
    handleEditDown(index, row) {
      console.log(index, row)
      const newNum = row.num - 1
      if (newNum <= 0) {
        this.$message({
          showClose: true,
          message: '商品数量必须大于0',
          type: 'warning'
        })
        return
      }
      this.$axios
        .post('updateCart', {
          uid: row.uid,
          bid: row.bid,
          num: newNum
        }).then(res => {
        console.log(res)
        this.reload()
        this.$message({
          showClose: true,
          message: '减少成功',
          type: 'success'
        })
      }).catch(error => {
        console.log(error)
        this.$message({
          showClose: true,
          message: '减少失败',
          type: 'warning'
        })
      })
    },
    handleDelete(index, row) {
      console.log(index, row)
      this.$axios
        .post('deleteCart', {
          uid: row.uid,
          bid: row.bid
        }).then(res => {
        console.log(res)
        this.reload()
        this.$message({
          showClose: true,
          message: '删除成功',
          type: 'success'
        })
      }).catch(error => {
        console.log(error)
        this.$message({
          showClose: true,
          message: '删除失败',
          type: 'warning'
        })
      })
    },
    getSummaries(param) {
      const { columns, data } = param
      const sums = []
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = '总价'
          return
        }
        const values = data.map(item => Number(item[column.property]))
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr)
            if (!isNaN(value)) {
              return prev + curr
            } else {
              return prev
            }
          }, 0)
          sums[index] += ' 元'
        } else {
          sums[index] = ''
        }
      })
      sums[1] = this.sumPrice + ' 元'
      return sums
    },
    Bill() {
      this.goodsList.forEach((raw) => {
        this.$axios
          .post('Bill', {
            uid: raw.uid,
            bid: raw.bid
          }).then(res => {
          console.log(res)
          this.reload()
        }).catch(error => {
          console.log(error)
        })
      })
      this.$message({
        showClose: true,
        message: '结账成功',
        type: 'success'
      })
    }
  }
}
</script>
<style lang="less" scoped>
.div{
  background-color: #ffebda;
}
.shop-list {
  width: 500px;
  margin-top: 20px
}
#num{
  margin: 0 10px
}
.total{
  margin-top: 30px;
  text-align: center;
  span{
    margin-right: 20px
  }
}
</style>
