<template>
  <div id="wrapper">
    <sidebar-component></sidebar-component>
    <div id="content-wrapper" class="d-flex flex-column">
      <div id="content">
        <navbar-component></navbar-component>

        <div class="container-fluid">
          <h1 class="h3 mb-4 text-gray-800">Data User</h1>
          <div class="row">
            <div class="col-lg-12">
              <div class="card shadow mb-4">
                <div class="card-body">
                  <div class="table-response">
                    <router-link
                      to="/user/tambah"
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
                          <th>Nama</th>
                          <th>Role</th>
                          <th>Outlet</th>
                          <th>Aksi</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="(u, index) in user" :key="index">
                          <td>{{ index + 1 }}</td>
                          <td>{{ u.nama }}</td>
                          <td>{{ u.role }}</td>
                          <td>{{ u.nama_outlet }}</td>
                          <td>
                            <router-link
                              :to="{
                                name: 'edituser',
                                params: { id_user: u.id },
                              }"
                              class="btn btn-warning btn-circle"
                            >
                              <i class="fas fa-pen"></i>
                            </router-link>

                            <button
                              type="button"
                              @click="hapus(u.id_user)"
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
      user: {},
    };
  },
  created() {
    var data = JSON.parse(this.$store.state.datauser);
    var role = data.role;
    if (role == "kasir" || role == "owner") {
      this.$swal("Anda tidak dapat mengakses halaman ini");
      this.$router.push("/");
    }

    this.axios
      .get("http://localhost/lat_laundry_8/public/api/tampiluser", {
        headers: { Authorization: "Bearer " + this.$store.state.token },
      })
      .then((res) => {
        this.user = res.data;
      });
  },
  methods: {
    hapus(id) {
      this.axios
        .delete(`http://localhost/lat_laundry_8/public/api/deleteuser/${id}`, {
          headers: { Authorization: "Bearer " + this.$store.state.token },
        })
        .then((res) => {
          if (res.data.success) {
            this.$swal("Sukses", res.data.message, "success");
            this.$router.push("/user");
            let i = this.user.map((item) => item.id).indexOf(id);
            this.user.splice(i, 1);
          }
        });
    },
  },
};
</script>
