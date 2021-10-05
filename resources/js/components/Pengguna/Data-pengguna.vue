<template>
    <section class="content">

        <div class="card">
            <div class="card-header">
                <h3 class="card-title">Data User</h3>
                <div class="card-tools"><button type="button" class="btn btn-success" @click="showModal">Tambah
                        Pengguna </button>
            </div>
        </div>
            <!-- /.card-header -->
            <div class="card-body">
                <table id="example1" class="table table-bordered table-striped">
                    <thead class="h5">
                        <tr>
                            <th>ID</th>
                            <th>Nama Pengguna</th>
                            <th>Level</th>
                            <th>Email</th>
                            <th>Aksi</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in users" :key="item.id">
                                        <td>{{item.id}}</td>
                                        <td>{{item.name}}</td>
                                        <td>{{item.level_id}}</td>
                                        <td>{{item.email}}</td> 
                                        <td><a href="#" @click="showModalEdit(item)"><i class="fas fa-edit blue"></i></a> | <a href="#" @click="deleteData(item.id)" class="text-danger"> <i class="fas fa-trash-alt red"></i></a> </td>
                                    </tr>
                    </tbody>
                    <tfoot>
                    </tfoot>
                </table>
            </div>
            <!-- /.card-body -->
        </div>
        <!-- /.card -->
        
        <!-- Modal -->
        <div class="modal fade" id="modalmuncul" tabindex="-1" role="dialog" aria-labelledby="modalmuncul1"
            aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLongTitle" v-show="!statusmodal"
                        >Tambah Pengguna</h5>
                        
                        <h5 class="modal-title" id="exampleModalLongTitle" v-show="statusmodal"
                        >Edit Pengguna </h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <form @submit.prevent="statusmodal ? ubahData() : simpanData()">
                        <div class="modal-body">
                            <div class="form-group">
                                <input type="text" class="form-control" v-model="form.name" placeholder="Nama Pengguna"
                                    :class="{'is-invalid' : form.errors.has('name')}" >
                                <has-error :form="form" field="name"></has-error>
                            </div>
                            <div class="form-group">
                                <select class="form-control select2" v-model="form.level_id"
                                    :class="{'is-invalid' : form.errors.has('level_id')}" >
                                    <option value>Pilih Level</option>
                                    <option v-for="item in levels" :key="item.id" :value="item.id">{{item.namalevel}}
                                    </option>
                                </select>
                                <has-error :form="form" field="level_id"></has-error>
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" v-model="form.email" placeholder="Email"
                                    :class="{'is-invalid' : form.errors.has('email')}" >
                                <has-error :form="form" field="email"></has-error>
                            </div>
                            <div class="form-group">
                                <input type="password" class="form-control" v-model="form.password"
                                    placeholder="Password" :class="{'is-invalid' : form.errors.has('password')}"
                                    >
                                <has-error :form="form" field="password"></has-error>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                            <button type="submit" class="btn btn-primary" :disabled="disabled" v-show="!statusmodal">
                                <i v-show="loading" class="fas fa-spinner fa-spin"></i> Simpan</button>
                        
                            <button type="submit" class="btn btn-success" :disabled="disabled" v-show="statusmodal">
                                <i v-show="loading" class="fas fa-spinner fa-spin"></i> Edit</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </section>

</template>

<script>
    export default {
        data() {
            return {
                loading: false,
                disabled: false,
                levels: {},
                users: {},
                statusmodal: false,
                form: new Form({
                    id: "",
                    name: "",
                    level_id: "",
                    email: "",
                    password: ""

                })
            };
        },
        methods: {
            showModal() {
                this.statusmodal = false;
                this.form.reset();
                $("#modalmuncul").modal("show");
            },
            showModalEdit(item) {
                this.statusmodal = true;
                this.form.reset();
                $("#modalmuncul").modal("show");
                this.form.fill(item);
            },
            loadData() {
                this.$Progress.start();
                axios
                    .get("api/ambildatalevel")
                    .then(({
                        data
                    }) => (this.levels = data));
                axios
                    .get("api/user")
                    .then(({
                        data
                    }) => (this.users = data));
                this.$Progress.finish();
            },
            simpanData() {
                this.$Progress.start();
                this.loading = true;
                this.disabled = true;
                this.form
                    .post('api/user').then(() => {
                        Fire.$emit("refreshData");
                        $("#modalmuncul").modal("hide");
                        Toast.fire({
                            icon: 'success',
                            title: 'Data Tersimpan'
                        });
                        this.$Progress.finish();
                        this.loading = false;
                        this.disabled = false;
                    })
                    .catch(() => {
                        this.$Progress.fail();
                        this.loading = false;
                        this.disabled = false;
                    });
            },
            ubahData() {
                this.$Progress.start();
                this.loading = true;
                this.disabled = true;
                this.form
                    .put('api/user/' + this.form.id).then(() => {
                        Fire.$emit("refreshData");
                        $("#modalmuncul").modal("hide");
                        Toast.fire({
                            icon: 'success',
                            title: 'Data Terupdate'
                        });
                        this.$Progress.finish();
                        this.loading = false;
                        this.disabled = false;
                    })
                    .catch(() => {
                        this.$Progress.fail();
                        this.loading = false;
                        this.disabled = false;
                    });
            },
            deleteData(id) {
                Swal.fire({
                    title: "Anda Yakin Ingin Menghapus Data Ini ?",
                    text: "Klik Batal untuk Membatalkan Penghapusan",
                    icon: "warning",
                    showCancelButton: true,
                    confirmButtonColor: "#3085d6",
                    cancelButtonColor: "#d33",
                    cancelButtonText: "Batal",
                    confirmButtonText: "Hapus"
                }).then(result => {
                    if (result.value) {
                        this.form
                            .delete("api/user/" + id)
                            .then(() => {
                                Swal.fire(
                                    "Terhapus",
                                    "Data Anda Sudah Terhapus",
                                    "success"
                                );
                                Fire.$emit("refreshData");
                            })
                            .catch(() => {
                                Swal.fire(
                                    "Gagal",
                                    "Data Anda Gagal Terhapus",
                                    "warning"
                                );
                            });
                    }
                });
            },
        },
        created() {
            this.loadData();
            Fire.$on("refreshData", () => {
                this.loadData();
            });
        }
    }

</script>
