<template>
  <div id="wrapper">
    <sidebar-component></sidebar-component>
    <div id="content-wrapper" class="d-flex flex-column">
      <div id="content">
        <navbar-component></navbar-component>
        <div class="container-fluid">
          <div class="card shadow mb-4">
            <div class="card-header py-3">
              <h6 class="m-0 font-weight-bold text-primary">
                Cetak Report Transaksi
              </h6>
            </div>
            <div class="card-body">
              <form class="form-group" @submit.prevent="tampil">
                <form class="form-group" @submit.prevent="tampil">
                  <label>Bulan</label>
                  <select class="form-control" v-model="report.bulan">
                    <option value="01">Januari</option>
                    <option value="02">Februari</option>
                    <option value="03">Maret</option>
                    <option value="04">April</option>
                    <option value="05">Mei</option>
                    <option value="06">Juni</option>
                    <option value="07">Juli</option>
                    <option value="08">Agustus</option>
                    <option value="09">September</option>
                    <option value="10">Oktober</option>
                    <option value="11">November</option>
                    <option value="12">Desember</option>
                  </select>
                  <br />
                  <label>Tahun</label>
                  <select class="form-control" v-model="report.tahun">
                    <option value="2020">2020</option>
                    <option value="2021">2021</option>
                    <option value="2022">2022</option>
                  </select>
                  <button
                    type="button"
                    class="btn btn-sm btn-info"
                    @click="tampil"
                  >
                    Tampilkan
                  </button>
                </form>
              </form>
              <div class="report">
                <VueHtml2pdf
                  :show-layout="true"
                  :float-layout="false"
                  :enable-download="true"
                  :preview-modal="true"
                  :paginate-elements-by-height="1400"
                  filename="report_transaksi"
                  :pdf-quality="2"
                  :manual-pagination="false"
                  pdf-format="a4"
                  pdf-orientation="portrait"
                  pdf-content-width="800px"
                  ref="html2Pdf"
                >
                  <section slot="pdf-content">
                    <h1 class="text-center">Report Transaksi</h1>
                    <h3 class="text-center">Laundry Online</h3>
                    <h5 class="text-center">
                      Jl Danau Kerinci IV Sawojajar Malang
                    </h5>
                    <br /><br />
                    <table class="table table-striped text-center">
                      <thead>
                        <tr>
                          <th class="font-weight-bold">No</th>
                          <th class="font-weight-bold">Member</th>
                          <th class="font-weight-bold">Tanggal Transaksi</th>
                          <th class="font-weight-bold">Tanggal Pembayaran</th>
                          <th class="font-weight-bold">Nominal Pembayaran</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="(t, index) in transaksi" :key="index">
                          <td>{{ index + 1 }}</td>
                          <td>{{ t.nama_member }}</td>
                          <td>{{ t.tgl | moment("DD/MM/YYYY") }}</td>
                          <td v-if="t.tgl_bayar == null">Belum Dibayar</td>
                          <td v-else>
                            {{ t.tgl_bayar | moment("DD/MM/YYYY") }}
                          </td>
                          <td v-if="t.total_bayar == null">Rp. -</td>
                          <td v-else>Rp {{ t.total_bayar }}</td>
                        </tr>
                      </tbody>
                    </table>
                  </section>
                </VueHtml2pdf>
                <button type="button" @click="generateReport">
                  Buat Report
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
h1,
h3,
h5 {
  text-align: center;
}
.report {
  width: 800px;
  margin: 20px auto 0 auto;
}
table {
  margin: 25px 20px;
  border-collapse: collapse;
  width: 760px;
}
thead tr {
  background-color: #5465ff;
  color: white;
  text-align: left;
}
th,
td {
  padding: 5px;
}
tbody tr {
  border-bottom: 1px solid #eee;
}
tbody tr:nth-of-type(even) {
  background-color: #f3f3f3;
}
tbody tr:last-of-type {
  border-bottom: 2px solid #0010a3;
}
button {
  width: 120px;
  height: 35px;
  margin-top: 20px;
  margin-left: 20px;
  border: none;
  background-color: #788bff;
  color: white;
  border-radius: 3px;
}
button:hover {
  background-color: #335fff;
}
button:active {
  background-color: #0028b8;
}
</style>
<script>
export default {
  data() {
    return {
      report: {},
      transaksi: {},
    };
  },
  created() {
    var data = JSON.parse(this.$store.state.datauser);
    var role = data.role;
    var id_outlet = data.id_outlet;

    if (role == "kasir") {
      this.$swal("Anda tidak dapat mengakses halaman ini");
      this.$router.push("/");
    }

    var date = new Date();
    this.report.id_outlet = id_outlet;
    this.report.tahun = date.getFullYear();
    this.report.bulan = ("0" + (date.getMonth() + 1)).slice(-2);
  },
  methods: {
    tampil() {
      this.axios
        .post("http://localhost/lat_laundry_8/public/api/report", this.report, {
          headers: { Authorization: "Bearer " + this.$store.state.token },
        })
        .then((res) => {
          this.transaksi = res.data;
        })
        .catch((err) => console.log(err));
    },
    generateReport() {
      this.$refs.html2Pdf.generatePdf();
    },
  },
};
</script>
