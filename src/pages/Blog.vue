<template>
  <Layout>
    <div class="overflow-x-hidden">
      <div id="projects" style="position: relative; top: -40px; left: 0"/>
      <div class="projects container-inner mx-auto text-xl border-t border-gray-500 border-b py-16 mb-16 relative">

        <div class="w-full px-4 md:w-1/2">
          <label class="block text-copy-primary mb-2" for="email">
            查询密码
          </label>
          <input type="text" name="email" id="email" placeholder="查询密码填写在这里" v-model='pwd' class="block w-full bg-background-form border border-border-color-primary shadow rounded outline-none focus:border-green-700 mb-2 p-4" required>
        </div>
        <div class="flex justify-end w-full">
          <input type="button" value="查询未完成订单" @click ="queryOrders(true)" class="block bg-green-700 hover:bg-green-800 text-white text-sm font-semibold tracking-wide uppercase shadow rounded cursor-pointer px-6 py-3">
          <input type="button" value="查询已完成订单" @click ="queryOrders(false)" class="block bg-green-700 hover:bg-green-800 text-white text-sm font-semibold tracking-wide uppercase shadow rounded cursor-pointer px-6 py-3">
        </div>

        <h2 class="font-bold mb-6">订购信息: 您还有{{pending.meat}}只肉粽和{{pending.eggAndMeat}}只蛋黄肉粽没有制作完成</h2>


        <ul class="text-lg sm:text-xl space-y-6">
          <table class="table table-auto">
            <thead>
            <tr>
              <th>姓名</th>
              <th>地址</th>
              <th>联系方式</th>
              <th>是否制作完成</th>
              <th>修改订单状态</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="item in orders" :key="item.id">
              <td>{{ item.name }}</td>
              <td>{{ item.address }}</td>
              <td>{{ item.contact }}</td>
              <td>{{map.boolean[item.status]}}</td>
              <button
                  class="bg-green-900 hover:bg-green-800 text-white px-4 py-2 rounded"
                  @click="changeStatus(item._id)">
                修改
              </button>
            </tr>
            </tbody>
          </table>
        </ul>
      </div> <!-- end projects -->
    </div>
  </Layout>
</template>


<script>
import PaginationPosts from '../components/PaginationPosts'
import axios from 'axios'

export default {
  metaInfo: {
    title: 'Blog'
  },
  components: {
    PaginationPosts
  },
  data() {
    return {
      orders: null,
      pwd: null,
      map: {boolean: {true: '已完成', false: '未完成'}},
      status: null,
      pending: {
        meat: null,
        eggAndMeat: null,
      }
    }
  },
  async mounted() {
    await this.queryUnfinished();
  },
  methods: {
    async changeStatus(id) {
      try {
        const results = await axios.post('http://13.89.121.207:3001/orders/changeStatus', {
          "pwd": this.pwd,
          "id": id,
        })
        await this.queryUnfinished();
        await this.queryOrders(this.status);
      } catch (e) {
        console.log(e)
      }
    },
    async queryUnfinished() {
      try {
        const results = await axios.post('http://13.89.121.207:3001/orders/getUnfinished')
        this.pending.meat = results.data.data.totalMeat;
        this.pending.eggAndMeat = results.data.data.totalEggAndMeat;
      } catch (e) {
        console.log(e)
      }
    },
    async queryOrders(status) {
      this.status = status
      try {
        const results = await axios.post('http://13.89.121.207:3001/orders/query', {
          "limit": 2000,
          "offset": 0,
          "notFinished": status,
          "pwd": this.pwd,
        })
        this.orders = results.data.data
      } catch (e) {
        console.log(e)
      }
    }
  }
}
</script>
