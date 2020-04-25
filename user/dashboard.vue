<template>
    <section class="section">
          <h1 class="section-header">
            <div>Dashboard</div>
          </h1>
          <div class="row">
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-primary">
                  <i class="ion ion-person"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Total Member</h4>
                  </div>
                  <div class="card-body">
                   {{users}}
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-danger">
                  <i class="ion ion-ios-paper-outline"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Total Buku</h4>
                  </div>
                  <div class="card-body">
                    {{books}}
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-warning">
                  <i class="ion ion-paper-airplane"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Reports</h4>
                  </div>
                  <div class="card-body">
                    1,201
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 col-12">
              <div class="card card-sm-3">
                <div class="card-icon bg-success">
                  <i class="ion ion-record"></i>
                </div>
                <div class="card-wrap">
                  <div class="card-header">
                    <h4>Online Users</h4>
                  </div>
                  <div class="card-body">
                    47
                  </div>
                </div>
              </div>
            </div>                  
          </div>
        </section>
</template>

<script>
module.exports = {
  data: function(){
    return{
      books: 0,
      users: 0,
      perPage: 5,
      currentPage: 1,
      key: "",
    }
  },
  methods: {
   getDataUser : function(){
      this.key = this.$cookies.get("Authorization");
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      axios.get(base_url + "/user/" + this.perPage + "/" + offset, conf)
      .then(response => {
          this.$bvToast.hide("loadingToast");
          this.users = response.data.count;    
          console.log(response.data);
             
      })
      .catch(error => {
        console.log(error);
      });
    },
    getDataBook : function(){
      this.key = this.$cookies.get("Authorization");
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      axios.get(base_url + "/book/" + this.perPage + "/" + offset, conf)
      .then(response => {
          this.$bvToast.hide("loadingToast");
          this.books = response.data.count;    
          console.log(response.data);
             
      })
      .catch(error => {
        console.log(error);
      });
    },
  },
  mounted(){
    this.getDataUser();
    this.getDataBook();
  }
}
</script>