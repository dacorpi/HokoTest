<template>
    <nav class="main-header navbar navbar-expand navbar-white navbar-light">
        <!-- Left navbar links -->
        <ul class="navbar-nav">
            <li class="nav-item">
                <a class="nav-link" data-widget="pushmenu" href="#" role="button"><i class="fas fa-bars"></i></a>
            </li>
            <template v-if="listPermisos.includes('dashboard.index')">
                <li class="nav-item d-none d-sm-inline-block">
                    <router-link class="nav-link" :to="{name: 'dashboard.index'}">Inicio</router-link>
                </li>
            </template>
            <template v-if="listPermisos.includes('pedido.index')">
                <li class="nav-item d-none d-sm-inline-block">
                    <router-link class="nav-link" :to="{name: 'cliente.index'}">Clientes</router-link>
                </li>
            </template>
        </ul>


    </nav>
</template>

<script>
    export default {
        props: ['ruta', 'listPermisos', 'usuario'],
        data() {
            return {
                cBusqueda: '',
                links: [],
                listRolPermisosByUsuario: [],
                listRolPermisosByUsuarioFilter: [],
            }
        },
        mounted() {
            EventBus.$on('notifyRolPermisosByUsuario', data => {
                this.getListarRolPermisosByUsuario();
            })
            this.getListarRolPermisosByUsuario();
        },
        methods: {
            querySearch(queryString, cb) {
                var links = this.listRolPermisosByUsuarioFilter;
                var results = queryString ? links.filter(this.createFilter(queryString)) : links;
                // call callback function to return suggestions
                cb(results);
            },
            createFilter(queryString) {
                return (link) => {
                    return (link.value.toLowerCase().indexOf(queryString.toLowerCase()) != -1);
                };
            },
            getListarRolPermisosByUsuario(){
                var ruta = '/administracion/usuario/getListarRolPermisosByUsuario'
                axios.get(ruta,{
                    params: {
                        'nIdUsuario'    :   this.usuario.id
                    }
                }).then( response => {
                    this.listRolPermisosByUsuario = response.data;
                    this.filterListarRolPermisosByUsuario();
                }).catch(error => {
                    if (error.response.status == 401) {
                        this.$router.push({name: 'login'})
                        location.reload();
                        sessionStorage.clear();
                        this.fullscreenLoading = false;
                    }
                })
            },
            filterListarRolPermisosByUsuario() {
                let me = this;
                me.listRolPermisosByUsuarioFilter = [];
                me.listRolPermisosByUsuario.map(function(x, y){
                    if (x.slug.includes('index')) {
                        me.listRolPermisosByUsuarioFilter.push({
                            'value' : x.name,
                            'link'  : x.slug
                        })
                    }
                })
            },
            handleSelect(item) {
                if (this.$route.name != item.link) {
                    this.$router.push({name: item.link})
                    this.cBusqueda = ''
                } else {
                    this.cBusqueda = ''
                }
            }
        },
    }
</script>

<style>

</style>
