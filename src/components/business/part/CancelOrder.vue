<template>
  <div class="order">
    <div class="header">
      <ul>
        <li>
          <router-link to="/MainPage/order">新订单</router-link>
        </li>
        <li>
          <router-link to="/MainPage/cancelOrder" class="active">取消订单</router-link>
        </li>
        <li>
          <router-link to="/MainPage/queryOrder">查询订单</router-link>
        </li>
      </ul>
    </div>
    <section>
      <aside>
        <div class="order_info" v-for="(item, index) in orderList" :key="index" v-if="item">
          <div class="orderStatus">
            <p>
              <span>{{item[0].id}}</span>
              <span>希望尽快送达</span>
            </p>
            <p>未付款</p>
          </div>
          <div>
            <p class="person">
              <span>
                {{item[0].receiver}}
              </span>
              
              <span>
                <i class="iconfont">&#xe678;</i>
                {{item[0].phone}}
              </span>
            </p>
            <p class="addr">
              <span>{{item[0].province}}{{item[0].city}}{{item[0].country}}{{item[0].address}}</span>
              <span>地图</span>
            </p>
            <p>
              <span>备注:</span>
              <span>{{item[0].message}}</span>
            </p>
          </div>
          <div>
            <table>
              <thead>
                <tr>
                  <th>商品</th>
                  <th>数量</th>
                  <th class="toggle">
                    <p >
                      单价
                    </p>
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(f, index) in item" :key="index">
                  <td>{{f.name}}</td>
                  <td>X{{f.number}}</td>
                  <td>{{f.price.toFixed(2)}}</td>
                </tr>
                <tr>
                  <td class="other">其他</td>
                  <td></td>
                  <td></td>
                </tr>
                <tr>
                  <td>配送费</td>
                  <td></td>
                  <td>{{item[0].deliver_fee.toFixed(2)}}</td>
                </tr>
                <tr class="total">
                  <td>总价</td>
                  <td></td>
                  <td>￥{{item | countPrice}}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </aside>
      <aside>
        <div class="money">
          <p>今日总营业额</p>
          <p>
            订单量：
            <span>{{allOrder}}</span>单
          </p>
          <p>
            营业额：
            <span>{{countPrice.toFixed(2)}}</span>元
          </p>
        </div>
        <div class="info">
          <p>订单小贴士</p>
          <ul>
            <li>建议在十点前打开客户端</li>
            <li>建议填写接单手机号</li>
            <li>建议填写菜品的介绍</li>
            <li>建议填写完整的详细地址</li>
          </ul>
        </div>
      </aside>
    </section>
  </div>
</template>
<script>
export default {
  data() {
    return {
      baseUrl: this.$store.getters.getBaseUrl,
      orderList: [],
      allOrder: 0,
      countPrice: 0,
    };
  },
  mounted() {
    this.getOrderInfo();
    this.getPrice();
  },
  methods: {
     getOrderInfo() {
      var url = `${this.baseUrl}/business/cancleOrderInfo`;
      this.axios(url, {
        params: {
          bphone: localStorage.getItem("business"),
        }
      }).then(res => {
        var list = [];
        for (const item of res.data.data) {
          list[item.id]=new Array();
        }
        for (const item of res.data.data) {
          list[item.id].push(item)
        }
        this.orderList = list;
      })
    },
    getPrice() {
      var url = `${this.baseUrl}/business/getAllPrice`;
      this.axios
        .get(url, {
          params: {
            bphone: localStorage.getItem("business")
          }
        })
        .then(result => {
          for (const item of result.data.data) {
            if (
              new Date(item.order_time).getFullYear() ==
                new Date().getFullYear() &&
              new Date(item.order_time).getMonth() == new Date().getMonth() &&
              new Date(item.order_time).getDate() == new Date().getDate()
            ) {
              this.countPrice += item.price;
              this.allOrder ++;
            }
          }
        });
    }
  },
  filters: {
    countPrice(value){
      var price = 0;
      for (const item of value) {
        price += item.price*item.number;
      }
      return price+value[0].deliver_fee;
    }
  }
};
</script>
<style lang="css" scoped>
    @import '../../../assets/css/busiOrder.css'
</style>

