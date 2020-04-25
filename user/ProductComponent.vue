<template>
  <div id="myproduct">
    <div class="row mt-2 mb-2">
      <div class="col-md-10">&nbsp;</div>
      <div class="col-md-2 text-right">
        <button class="btn btn-primary" data-toggle="modal" data-target="#cart">
          <i class="fa fa-shopping-cart"></i>
          <span class="badge badge-light">{{badge}}</span>
        </button>
        <div class="modal fade" id="cart">
          <div class="modal-dialog modal-dialog-centered modal-lg">
            <div class="modal-content">
              <div class="modall-header">
                <h5 class="modal-title">Your Cart</h5>
                <button class="close" data-dismiss="modal">&times;</button>
              </div>
              <div class="modal-body">
                <table class="table table-striped text-left">
                  <tbody>
                    <tr v-for="(cart, n) in carts" v-bind:key="cart.id">
                      <td>{{cart.name}}</td>
                      <td>Rp. {{cart.price}}</td>
                      <td width="100">
                        <input type="text" readonly class="form-control" v-model="quantity" />
                      </td>
                      <td width="60">
                        <button @click="removeCart(n)" class="btn btn-danger btn-sm">
                          <i class="fa fa-close"></i>
                        </button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div class="modal-footer">
                Total Price: Rp. {{totalprice}} &nbsp;
                <button
                  data-dismiss="modal"
                  class="btn btn-primary"
                >Checkout</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-8">
        <div class="row">
          <div v-for="gambar in gambars" v-bind:key="gambar.id" class="card card-body col-md-6">
            <h4>{{gambar.nama_produk}}</h4>
            <p>{{gambar.jenis}}</p>
            <div class="row">
              <div class="col-md-6">Stock {{gambar.stock}}</div>
              <div class="col-md-6 text-right">Rp. {{gambar.harga}}</div>
            </div>
            <p class="text-right mt-2">
              <button @click="addCart(gambar)" class="btn btn-primary">Add to Cart</button>
              <button @click="editProduct(gambar)" class="btn btn-warning">Edit</button>
              <button @click="deleteProduct(gambar.id)" class="btn btn-danger">Delete</button>
            </p>
          </div>
        </div>
        <div class="row mt-2">
          <div class="col-md-8">
            <nav>
              <ul class="pagination">
                <li v-bind:class="{disabled:!pagination.first_link}" class="page-item">
                  <a href="#" @click="viewProduct()"></a>
                </li>
                <li v-bind:class="{disabled:!pagination.prev_link}" class="page-item">
                  <a href="#" @click="viewProduct()"></a>
                </li>
                <li v-for="n in pagination.last_page" v-bind:key="n" class="page-item"></li>
                <li v-bind:class="{disabled:!pagination.next_link}" class="page-item"></li>
                <li v-bind:class="{disabled:!pagination.last_link}" class="page-item"></li>
              </ul>
            </nav>
          </div>
          <div class="col-md-4">
            Page: {{pagination.from_page}} - {{pagination.to_page}}
            Total: {{pagination.total_page}}
          </div>
        </div>
      </div>
      <div class="col-md-4">
        <form>
          <div class="form-group">
            <label>Name</label>
            <input type="text" class="form-control" v-model="product.name" />
          </div>
          <div class="form-group">
            <label>Description</label>
            <input type="text" class="form-control" v-model="product.description" />
          </div>
          <div class="form-group">
            <label>Price</label>
            <input type="number" class="form-control" v-model="product.price" />
          </div>
          <div class="form-group">
            <label>Amount</label>
            <input type="number" class="form-control" v-model="product.amount" />
          </div>
          <button v-if="add" @click.prevent="addProduct()" class="btn btn-primary">Add Product</button>
          <button
            v-if="edit"
            @click.prevent="updateProduct(product.id)"
            class="btn btn-warning"
          >Edit Product</button>
          <button @click.prevent="clearProduct()" class="btn btn-info">Clear</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
module.exports = {
  data() {
    return {
      products: [],
      product: {
        id: "",
        name: "",
        description: "",
        price: "",
        amount: ""
      },
      add: true,
      edit: false,
      pagination: {},
      carts: [],
      cartadd: {
        id: "",
        name: "",
        // description: '',
        price: "",
        amount: ""
      },
      badge: "0",
      quantity: "1",
      totalprice: "0"
    };
  },
  created() {
    this.viewProduct();
    this.viewCart();
  },
  methods: {
    viewCart() {
      if (localStorage.getItem("carts")) {
        this.carts = JSON.parse(localStorage.getItem("carts"));
        this.badge = this.carts.length;
        this.totalprice = this.carts.reduce((total, item) => {
          return total + this.quantity * item.price;
        }, 0);
      }
    },
    addCart(pro) {
      this.cartadd.id = pro.id;
      this.cartadd.name = pro.name;
      this.cartadd.price = pro.price;
      this.cartadd.amount = pro.amount;
      this.carts.push(this.cartadd);
      this.cartadd = {};
      this.storeCart();
    },
    removeCart(pro) {
      this.carts.splice(pro, 1);
      this.storeCart();
    },
    storeCart() {
      let parsed = JSON.stringify(this.charts);
      localStorage.setItem("carts", parsed);
      this.viewCart();
    },
    viewProduct(pagi) {
      pagi = pagi || "api/products";
      fetch(pagi)
        .then(res => res.json())
        .then(res => {
          this.products = res.data;
        })
        .catch(err => console.log(err));
    },
    addProduct() {
      fetch("api/products", {
        method: "post",
        body: JSON.stringify(this.product),
        headers: {
          "content-type": "application/json"
        }
      })
        .then(res => res.json())
        .then(data => {
            this.product.name = '',
            this.product.description = '',
            this.product.price = '',
            this.product.amount = '',
          alert("Produk Added");
          this.viewProduct();
        })
        .catch(err => console.log(err));
    },
    editProduct() {
      this.add = false;
      this.edit = true;
      this.product.id = pro.id
      this.product.name = pro.name
      this.product.description = pro.description
      this.product.price = pro.price
      this.product.amount = pro.amount
    },

    updateProduct(id) {
      fetch("api/products/${id}", {
        method: "put",
        body: JSON.stringify(this.product),
        headers: {
          "content-type": "application/json"
        }
      })
        .then(res => res.json())
        .then(data => {
          this.add = true;
          this.edit = false;
          this.product.name = '',
            this.product.description = '',
            this.product.price = '',
            this.product.amount = '',
          alert("Produk Updated");
        })
        .catch(err => console.log(err));
      this.viewProduct();
    },

    deleteProduct(id) {
      fetch("api/products/${id}", {
        method: "delete"
      })
        .then(res => res.json())
        .then(data => {
          alert("Produk Deleted");
          this.viewProduct();
        })
        .catch(err => console.log(err));
    }
  }
};
</script>

<style>
.product-item {
  width: 350px;
  float: left;
}
</style>