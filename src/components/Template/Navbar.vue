<template>
  <nav
    class="navbar navbar-expand navbar-light bg-white topbar mb-4 static-top shadow"
  >
    <ul class="navbar-nav ml-auto">
      <li class="nav-item">
        <button class="btn btn-sm btn-warning btn-icon-split" @click="logout">
          <span class="icon text-white-50">
            <i class="fas fa-sign-out-alt"></i>
          </span>
          <span class="text">Logout</span>
        </button>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  created() {
    this.axios
      .get("http://localhost/lat_laundry_8/public/api/login/check", {
        headers: { Authorization: "Bearer " + this.$store.state.token },
      })
      .then((res) => {
        if (!res.data.success) {
          this.$store.commit("clearToken");
          this.$store.commit("clearUser");
          this.$swal("Error", "Sesi Anda sudah habis", "error");
          this.$router.push("/login");
        }
      });
  },

  methods: {
    logout() {
      this.axios
        .post("http://localhost/lat_laundry_8/public/api/logout", {
          headers: { Authorization: "Bearer " + this.$store.state.token },
        })
        .then(() => {
          this.$store.commit("clearToken");
          this.$store.commit("clearUser");
          this.$router.push("/login");
        });
    },
  },
};
</script>
