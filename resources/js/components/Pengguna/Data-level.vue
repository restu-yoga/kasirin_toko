<template>
    <div class="container">
            <div class="col-lg-10">
                <div class="card">
                    <div class="card-header">Halaman Level</div>

                    <div class="card-body">
                        <div class="form-group">
                            <div class="table-responsive">
                                <table class="table">
                                    <tr>
                                        <th>Nama Level</th>
                                    </tr>
                                    <tr v-for="item in levels" :key="item.id">
                                        <td> {{item.namalevel}} </td>
                                    </tr>
                                </table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                levels : {}
            };
        },
        methods: {
            loadData(){
                this.$Progress.start();
                axios.get('api/ambildatalevel').then(({data}) => (this.levels = data));
                this.$Progress.finish();
            }
        },
        created(){
            this.loadData();
        }
    }
</script>
