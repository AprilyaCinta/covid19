<template>
  <div>
    <div class="container mt-3">
      <div class="row">
        <div class="col-lg-12 grid-margin stretch-card">
          <div class="card">
            <div class="card-body">
              <p class="card-title float-left"><b>Data Pengaduan Masyarakat</b></p>
              <!-- <p class="card-description float-right">
                <b-button variant="dark" v-b-modal.modalPetugas v-on:click="Add"><i class="mdi mdi-note btn-icon-prepend"></i>Laporan</b-button>
              </p> -->
              <div class="table-responsive">
                <b-table striped hover :items="pengaduan" :fields="fields">
                  <!-- <template v-slot:cell(level)="data">
                    <b-badge variant="warning">{{ data.item.level }}</b-badge>
                  </template> -->
                    <template v-slot:cell(status)="data">
                    <b-badge variant="info" v-if="data.item.status === 'terkirim'">{{ data.item.status }}</b-badge>
                    <b-badge variant="warning" v-if="data.item.status === 'proses'">{{ data.item.status }}</b-badge>
                    <b-badge variant="success" v-if="data.item.status === 'selesai'">{{ data.item.status }}</b-badge>
                    <b-badge variant="danger" v-if="data.item.status === 'tolak'">{{ data.item.status }}</b-badge>
                  </template>
                  <template v-slot:cell(foto)="data">
                    <img
                      style="width: 150px; height: 120px; border-radius: 5%"
                      :src="'http://localhost:8000/uploads/' + data.item.foto"
                    />
                  </template>

                  <template v-slot:cell(Aksi)="data">
                    <b-button size="sm" variant="info" v-on:click="Edit(data.item)" v-b-modal.modalUser><i class="mdi mdi-pencil btn-icon-prepend"></i>Status</b-button>&nbsp;
                    <b-button size="sm" variant="danger" v-on:click="Add(data.item)" v-b-modal.modalTanggapan><i class="mdi mdi-pencil btn-icon-prepend"></i>Tanggapan</b-button>
                  </template>
                  <template v-slot:cell(telp)="data">
                    {{data.item.user.telp}}
                  </template>

                </b-table>
                <b-pagination
                  v-model="currentPage"
                  :per-page="perPage"
                  :total-rows="rows"
                  align="center"
                  v-on:input="pagination">
                </b-pagination>

                <b-toast id="loadingToast" title="Processing Data" no-auto-hide>
                  <b-spinner label="Spinning" variant="success"></b-spinner>
                  <strong class="text-success">Loading...</strong>
                </b-toast>

                <!-- toast untuk tampilan message box -->
                <b-toast id="message" title="Message">
                  <strong class="text-success">{{ message }}</strong>
                </b-toast>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>



       <b-modal 
      id="modalUser"
      @ok="Save"
      >
      <template v-slot:modal-title>
        Form 
      </template>
        <form ref="form">
          <div class="form-group">
            <!-- <div class="form-group">
              <label for="id_pengaduan" class="col-form-label">Id Pengaduan</label>
              <input type="text" class="form-control" placeholder="Id Pengaduan" v-model="id_pengaduan">
            </div> -->
            <div class="form-group">
              <label for="status" class="col-form-label">Status</label>
              <select class="form-control" name="status" id="status" v-model="status">
                <option value="terkirim" checked>Terkirim</option>
                <option value="tolak">Tolak</option>
                <option value="proses">Proses</option>
                <option value="selesai">Selesai</option>
              </select>
            </div>
          </div>
        </form>
    </b-modal>

    <b-modal 
      id="modalTanggapan"
      @ok="Save1"
    >
      <template v-slot:modal-title>
        Tanggapan 
      </template>
        <form ref="form">
          <div class="form-group">
             <div class="form-group">
              <label for="tanggapan" class="col-form-label">Tanggapan</label>
              <input type="text" class="form-control" placeholder="Tanggapan" v-model="tanggapan">
            </div>
          </div>
        </form>

    </b-modal>
  </div>
</template>

<script>
export default {
  data : function(){
    return {
      search: "",
    //   nik:"",
    //   nama:"",
    //   telp:"",
    //   username:"",
      telp: "",
      id_pengaduan:"",
      nama_kategori:"",
      id: "",
      tgl_pengaduan: "",
      isi_laporan: "",
      id_user:"",
      foto: "",
      status: "",
      id_kategori:"",
      tanggapan:"",
      id_petugas:"",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      key: "",
      user: [],
      pengaduan:[],
      tanggapan:[],
      kategori:[],
      fields: ["id_pengaduan","id_user", "tgl_pengaduan", "isi_laporan", "foto","status", "Aksi", "telp"],
    }
  },
  

  methods: {
    getData : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      this.axios.get("/pengaduan/" + this.perPage + "/" + offset, conf)
      .then(response => {
        if(response.data.success == true){
          this.$bvToast.hide("loadingToast");
          this.user = response.data.data.pengaduan.user;
          this.pengaduan = response.data.data.pengaduan;
          this.tanggapan = response.data.data.pengaduan.tanggapan;
          this.rows = response.data.data.count;
        } else {
          this.$bvToast.hide("loadingToast");
          this.message = "Gagal menampilkan data pengaduan."
          this.$bvToast.show("message");
          this.$router.push({name: "login"})
        }
        
      })
      .catch(error => {
        console.log(error);
      });
    },



    pagination : function(){
      if(this.search == ""){
        this.getData();
      } else {
        this.searching();
      }
    },

    Add : function(item){
      this.action = "insert";
      this.id_pengaduan = item.id_pengaduan;
      this.tanggapan = "";
    },

    Edit : function(item){
      this.action = "update";
      this.id_pengaduan = item.id_pengaduan;
      this.status = item.status;
    },

    Save : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      this.$bvToast.show("loadingToast");
      this.action === "update"
        let form = new FormData();
        form.append("id_pengaduan", this.id_pengaduan);
        form.append("status", this.status);

        this.axios.post("/pengaduan/status", form, conf)
        .then((response) => {
          if (response.data.success){
             this.$bvToast.hide("loadingToast");
             this.user = response.data.data.user;
             this.pengaduan = response.data.data.pengaduan;
             this.rows = response.data.data.count;
             this.getData();
          } else {
            this.$bvToast.hide("loadingToast");
            this.message = "Gagal menampilkan data pengaduan";
            this.$bvToast.show("message");
          }}
          )
          },

  },

  mounted(){
    this.key = localStorage.getItem("Authorization");
    this.getData();
    
  }
}
</script>