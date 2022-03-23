<template>
  <div id="wrapper">
    <sidebar-component></sidebar-component>
    <div id="content-wrapper" class="d-flex flex-column">
      <div id="content">
        <navbar-component></navbar-component>

        <div class="container-fluid">
          <h1 class="h3 mb-4 text-gray-800">Data Paket</h1>
          <div class="row">
            <div class="col-lg-12">
              <div class="card shadow mb-4">
                <div class="card-body">
                  <div class="table-response">
                    <router-link
                      to="/paket/tambah"
                      class="btn btn-info btn-icon-split"
                    >
                      <span class="icon text-white-50">
                        <i class="fas fa-plus"></i>
                      </span>
                      <span class="text">Tambah</span>
                    </router-link>
                    <table
                      class="table table-bordered mt-3"
                      width="100%"
                      cellspasing="0"
                    >
                      <thead>
                        <tr>
                          <th>No</th>
                          <th>Jenis</th>
                          <th>Harga</th>
                          <th>Aksi</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="(m, index) in paket" :key="index">
                          <td>{{ index + 1 }}</td>
                          <td>{{ m.jenis }}</td>
                          <td>{{ m.harga }}</td>
                          <td>
                            <router-link
                              :to="{
                                name: 'detailpaket',
                                params: { id_paket: m.id_paket },
                              }"
                              class="btn btn-success btn-circle"
                            >
                              <i class="far fa-eye"></i>
                            </router-link>
                            <router-link
                              :to="{
                                name: 'editpaket',
                                params: { id_paket: m.id_paket },
                              }"
                              class="btn btn-warning btn-circle"
                            >
                              <i class="fas fa-pen"></i>
                            </router-link>
                            <button
                              type="button"
                              @click="hapus(m.id_paket)"
                              class="btn btn-danger btn-circle"
                            >
                              <i class="fas fa-trash"></i>
                            </button>
                          </td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
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
    var data = JSON.parse(this.$store.state.datauser);
    var role = data.role;

    if (role == 'kasir' || role == 'owner') {
      this.$swal("Error", "Anda tidak dapat mengakses halaman ini", "error");
      this.$router.push('/');
    }
    this.axios
      .get("http://localhost/lat_laundry_8/public/api/tampilpaket", {
        headers: { Authorization: "Bearer " + this.$store.state.token },
      })
      .then((res) => {
        this.paket = res.data.data;
      });
  },
  methods: {
    hapus(id_paket) {
      this.axios
        .delete(
          `http://localhost/lat_laundry_8/public/api/deletepaket/${id_paket}`,
          {
            headers: { Authorization: "Bearer " + this.$store.state.token },
          }
        )
        .then(() => {
          let i = this.paket.map((item) => item.id_paket).indexOf(id_paket);
          this.paket.splice(i, 1);
        });
    },
  },
};
</script>
