<template>
  <div class="container">
    <div class="row">
      <div class="col-12 text-center">
        <h4 class="pt-3">Danh sách đơn hàng</h4>
      </div>
    </div>
    <!--        for each order display -->
    <div v-for="order in orderList" :key="order.pid" class="row mt-2 pt-3 justify-content-around">
      <div class="col-2"></div>
      <div class="col-md-3 embed-responsive embed-responsive-16by9">
        <!--                display image in left-->
        <img v-bind:src="order.imageUrl" class="w-100 card-img-top embed-responsive-item">
      </div>
      <div class="col-md-5 px-3">
        <div class="card-block px-3">
          <h6 class="card-title">
            <router-link v-bind:to="'/order/'+order.id">Mã đơn hàng : {{order.id}}</router-link>
          </h6>
          <p class="mb-0">{{order.totalItems}} sản phẩm</p>
          <p id="item-price" class="mb-0 font-weight-bold">Tổng giá : $ {{order.totalCost}}</p>
          <p id="item-total-price">Đặt vào ngày : {{order.orderdate}}</p>
        </div>
      </div>
      <div class="col-2"></div>
      <div class="col-12"><hr></div>
    </div>
  </div>

</template>

<script>
  const axios = require('axios')
  export default {

    data() {
      return {
        token: null,
        orderList : []
      }
    },
    props:["baseURL"],
    name: 'Order',
    methods: {
      // list of order histories
      listOrders(){
        axios.get(`${this.baseURL}order/?token=${this.token}`)
          .then((response) => {
              if(response.status==200){
                this.orders = response.data
                // for each order populate orderList
                this.orders.forEach((order) => {
                  let date = new Date(order.createdAt)
                  this.orderList.push({
                    id: order.id,
                    totalCost: order.totalCost,
                    // get short date
                    orderdate: date.toString().substring(0,10),
                    // get image of the first orderItem of the order
                    imageUrl: order.orderItemList[0].product.imageUrl,
                    // get total items
                    totalItems: order.orderItemList.length
                  })
                })
              }
            },
            (error)=>{
              console.log(error)
            });
      },
    },
    mounted() {
      this.token = localStorage.getItem("token");
      this.listOrders();
    },
  };

</script>

<style scoped>
  h4, h5 {
    font-family: 'Roboto', sans-serif;
    color: #484848;
    font-weight: 700;
  }

  .embed-responsive .card-img-top {
    object-fit: cover;
  }
</style>
