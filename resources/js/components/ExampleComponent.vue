<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">
                        {{ title }}
                    </div>

                    <div v-if="!loading" class="card-body">
                       <button class="btn btn-primary" @click="aksesApi">
                          Refresh Data
                        </button>

                        <input v-model="katakunci" type="search" placeholder="Masukan Kata Kunci Dan Tekan Enter" @change="jalankanPencarian" />

                       <table v-if="!error" class="table table-bordered">
                           <tr>
                               <td>Nama</td>
                                <td>JenisKelamin</td>
                                <td>Tanggal Dibuat</td>
                           </tr>

                           <tr v-for="item in data.data" :key="item.id">
                               <td>{{ item.nama }}</td>
                               <td>{{ item.jk }}</td>
                               <td>{{ item.created_at }}</td>
                           </tr>
                       </table>

                       <pagination :data="data" @pagination-change-page="aksesApi">
                       </pagination>
                       <div class="alert alert-warning" v-if="error">
                           {{ error }}
                       </div>
                    </div>
                    <div v-if="loading" class="card-body">
                      LOADING...
                    </div>
                </div>
            </div>
        </div>

        <vue-progress-bar></vue-progress-bar>
        <notifications group="foo" />

    </div>
</template>

<script>
    export default {
        data(){
            return {
                title: 'Data Siswa-Siswi',
                error:'',
                loading: false,
                katakunci: '',
                data : {}
            }
        },

        mounted() {
           this.aksesApi()
        }, 

        methods: {
            aksesApi(page = 1, katakunci){
                this.$Progress.start()
                this.loading = true

                const params ={
                  page: page
                }

            if (katakunci){
              params.katakunci = katakunci
            }

                axios.get('/testapi',{ params })
                .then(res => {
                    this.data = res.data
                    this.loading = false
                    this.$Progress.finish()
                    this.$notify({
                    type: 'bg-succes',
                    group: 'foo',
                    title: 'Sukses',
                    text: 'Hello user! Data Berhasil Ditampilkan!'});
                })
                
                .catch(error => {
                    this.error = error
                    this.loading = false
                    this.$Progress.fail()
                    this.$notify({
                    type: 'bg-danger',
                    group: 'foo',
                    title: 'Error',
                    text: 'Hello user! Data Tidak Ditemukan!'});
                })
            },

             jalankanPencarian(){
                this.aksesApi(1, this.katakunci)
             },

             resetData(){

             }
        }
    }
</script>
