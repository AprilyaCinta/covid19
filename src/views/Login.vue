<template>
    <div>
        <!-- Navbar -->
        <nav id="navbar-main" class="navbar navbar-horizontal navbar-transparent navbar-main navbar-expand-lg navbar-light">
            <div class="container">
                <a class="navbar-brand m-auto" href="dashboard.html">
                    <img src="../../public/assets/img/brand/white.png">
                </a>
            </div>
        </nav>
        <!-- Main content -->
        <div class="main-content">
            <!-- Header -->
            <div class="header bg-gradient-primary py-7 py-lg-8 pt-lg-9">
            <div class="container">
                <div class="header-body text-center mb-7">
                <div class="row justify-content-center">
                    <div class="col-xl-5 col-lg-6 col-md-8 px-5">
                    <h1 class="text-white">Selamat Datang!</h1>
                    <p class="text-lead text-white">Aplikasi Seputar Covid dan Info Lainnya.</p>
                    </div>
                </div>
                </div>
            </div>
            <div class="separator separator-bottom separator-skew zindex-100">
                <svg x="0" y="0" viewBox="0 0 2560 100" preserveAspectRatio="none" version="1.1" xmlns="http://www.w3.org/2000/svg">
                <polygon class="fill-default" points="2560 0 2560 100 0 100"></polygon>
                </svg>
            </div>
            </div>
            <!-- Page content -->
            <div class="container mt--9 pb-5">
                <div class="row justify-content-center">
                    <div class="col-lg-5 col-md-7">
                        <div class="card bg-secondary border-0 mb-0">
                            
                            <div class="card-body px-lg-5 py-lg-5">
                                <div class="text-center text-muted mb-4">
                                    <small>Masukan username dan password anda!.</small>
                                </div>
                                
                                <form v-on:submit.prevent="Login">
                                    <div class="form-group mb-3">
                                        <div class="input-group input-group-merge input-group-alternative">
                                            <div class="input-group-prepend">
                                            <span class="input-group-text"><i class="ni ni-email-83"></i></span>
                                            </div>
                                            <input class="form-control" placeholder="Username" type="text" v-model="username">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <div class="input-group input-group-merge input-group-alternative">
                                            <div class="input-group-prepend">
                                            <span class="input-group-text"><i class="ni ni-lock-circle-open"></i></span>
                                            </div>
                                            <input class="form-control" placeholder="Password" type="password" v-model="password">
                                        </div>
                                    </div>
                                    <div class="text-left">
                                        <small class="text-red">{{ message }}</small>
                                    </div>
                                    
                                    <div class="text-center">
                                    <button type="submit" class="btn btn-primary btn-block my-4">Login</button>
                                    </div>
                                    
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
</template>
<script>
export default {
    data() {
      return {
        username: '',
        password: '',
        message: '',
      }
    },
    methods: {
        Login: function(){
            this.message = "Mohon tunggu..."
            let username = this.username 
            let password = this.password
            this.$store.dispatch('login', { username, password })
            .then((response) => {
                this.message = response.data.message
                if(response.data.success == true){
                    this.$router.push('/dashboard')
                }
            })
            .catch(err => console.log(err))
        }
    },
    mounted(){
        //jika posisi sudah login tidak bisa menampilkan halaman login lagi
        window.onpopstate = event => {
        if (
            window.localStorage.getItem("Authorization") !== null &&
            this.$route.path == "/login"
        ) {
            this.$router.push("/dashboard"); // redirect to home, for example
        }
        };
    }
}
</script>