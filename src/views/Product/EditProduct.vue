<template>
  <div class="container">
    <div class="row">
      <div class="col-12 text-center">
        <h4 class="pt-3">Edit Product</h4>
      </div>
    </div>

    <div class="row">
      <div class="col-3"></div>
      <div class="col-md-6 px-5 px-md-0">
        <form v-if="product">
          <div class="form-group">
            <label>Danh mục</label>
            <select class="form-control" v-model="product.categoryId" required>
              <option v-for="category of categories" :key="category.id" :value="category.id">{{category.categoryName}}</option>
            </select>
          </div>
          <div class="form-group">
            <label>Tên</label>
            <input type="text" class="form-control" v-model="product.name" required>
          </div>
          <div class="form-group">
            <label>Mô tả</label>
            <input type="text" class="form-control" v-model="product.description" required>
          </div>
          <div class="form-group">
            <label>URL ảnh</label>
            <input type="url" class="form-control" v-model="product.imageUrl" required>
          </div>
          <div class="form-group">
            <label>Price</label>
            <input type="number" class="form-control" v-model="product.price" required>
          </div>
          <button type="button" class="btn btn-primary" @click="editProduct">Xác nhận</button>
          <!-- <button type="button" class="btn btn-primary" @click="deleteProduct">Delete</button> -->
        </form>
      </div>
      <div class="col-3"></div>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      product: null
    }
  },
  props : ["baseURL", "products", "categories"],
  methods : {
    async editProduct() {
      axios.post(this.baseURL+"product/update/"+this.id, this.product)
      .then(res => {
        //sending the event to parent to handle
        this.$emit("fetchData");
        this.$router.push({name : 'AdminProduct'});
        swal({
          text: "Product Updated Successfully!",
          icon: "success",
          closeOnClickOutside: false,
        });
      })
      .catch(err => console.log("err", err));
    },
    deleteProduct() {
      swal({
        text: "Are you sure to delete?",
        buttons: {
          confirm: {
            text: "Delete",
            value: "del"
          },
          cancel: true,
          closeOnClickOutside: false,
        }
      }).then(value => {
        if (value == "del") {
          axios.post(this.baseURL+"product/delete/"+this.id)
          .then(res => {
            swal({
              text: "Product has been deleted",
              icon: "warning",
            })
          })
          .catch(err => console.log(err))
        }
      })
    }
  },
  mounted() {
    if (!localStorage.getItem('token')) {
      this.$router.push({name : 'Signin'});
      return;
    }
    this.id = this.$route.params.id;
    this.product = this.products.find(product => product.id == this.id);
  }
}
</script>

<style scoped>
h4 {
  font-family: 'Roboto', sans-serif;
  color: #484848;
  font-weight: 700;
}
.btn {
  margin-right: 20px;
}

</style>
