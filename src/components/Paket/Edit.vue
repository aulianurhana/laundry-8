<template>
  <div id="wrapper">
    <sidebar-component></sidebar-component>
    <div id="content-wrapper" class="d-flex flex-column">
      <div id="content">
        <navbar-component></navbar-component>

        <div class="container-fluid">
          <div class="row">
            <div class="col-lg-8">
              <div class="card shadow mb-4">
                <div class="card-header py-3">
                  <h6 class="m-0 font-weight-bold text-primary">
                    Edit Data Paket
                  </h6>
                </div>
                <div class="card-body">
                  <form @submit.prevent="edit">
                    <div class="form-group">
                      <div>
                        <label>Jenis Paket</label>
                      </div>
                      <select
                        class="form-select form-control"
                        aria-label="Default select example"
                        v-model="paket.jenis"
                      >
                        <option value="kiloan">Kiloan</option>
                        <option value="selimut">Selimut</option>
                        <option value="bed_cover">Bed Cover</option>
                      </select>
                    </div>
                    <div class="form-group">
                      <label>Harga</label>
                      <textarea
                        rows="4"
                        class="form-control"
                        v-model="paket.harga"
                      ></textarea>
                    </div>

                    <button type="submit" class="btn btn-success btn-block">
                      Simpan
                    </button>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <footer-component></footer-component>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      paket: {},
    };
  },
  created() {
    this.axios
      .get(
        `http://localhost/lat_laundry_8/public/api/tampilpaket/${this.$route.params.id_paket}`,
        { headers: { Authorization: "Bearer " + this.$store.state.token } }
      )
      .then((res) => {
        this.paket = res.data;
      });
  },
  methods: {
    edit() {
      this.axios
        .put(
          `http://localhost/lat_laundry_8/public/api/updatepaket/${this.$route.params.id_paket}`,
          this.paket,
          { headers: { Authorization: "Bearer " + this.$store.state.token } }
        )
        .then((res) => {
         if (res.data.success) {
            this.$swal("Sukses", res.data.message, "success");
            this.$router.push("/paket");
          }
        })
        .catch((err) => console.log(err));
    },
  },
};
</script>
