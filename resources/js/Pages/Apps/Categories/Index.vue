<template>
    <Head>
        <title>Kategori - Aplikasi Kasir</title>
    </Head>
    <main class="c-main">
        <div class="container-fluid">
            <div class="fade-in">
                <div class="row">
                    <div class="col-md-12">
                        <div class="card border-0 rounded-3 shadow border-top-purple">
                            <div class="card-header">
                                <span class="font-weight-bold"><i class="fa fa-folder"></i> SEMUA KATEGORI</span>
                            </div>
                            <div class="card-body">
                                <form @submit.prevent="handleSearch">
                                    <div class="input-group mb-3">
                                        <Link href="/apps/categories/create" v-if="hasAnyPermission(['categories.create'])" class="btn btn-primary input-group-text"> <i class="fa fa-plus-circle me-2"></i> TAMBAH</Link>
                                        <input type="text" class="form-control" v-model="search" placeholder="Cari berdasarkan nama kategori...">

                                        <button class="btn btn-primary input-group-text" type="submit"> <i class="fa fa-search me-2"></i> CARI</button>

                                    </div>
                                </form>
                                <table class="table table-striped table-bordered table-hover">
                                    <thead>
                                        <tr>
                                            <th scope="col">Nama</th>
                                            <th scope="col">Gambar</th>
                                            <th scope="col" style="width:20%">Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        <tr v-for="(category, index) in categories.data" :key="index">
                                            <td>{{ category.name }}</td>
                                            <td class="text-center"><img :src="category.image" width="40"></td>
                                            <td class="text-center">
                                                <Link :href="`/apps/categories/${category.id}/edit`" v-if="hasAnyPermission(['categories.edit'])" class="btn btn-success btn-sm me-2"><i class="fa fa-pencil-alt me-1"></i> UBAH</Link>
                                                <button @click.prevent="destroy(category.id)" v-if="hasAnyPermission(['categories.delete'])" class="btn btn-danger btn-sm"><i class="fa fa-trash"></i> HAPUS</button>
                                            </td>
                                        </tr>
                                    </tbody>
                                </table>
                                <Pagination :links="categories.links" align="end"/>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>
</template>

<script>
    //import layout
    import LayoutApp from '../../../Layouts/App.vue';

    //import component pagination
    import Pagination from '../../../Components/Pagination.vue';

    //import Head and Link from Inertia
    import { Head, Link } from '@inertiajs/inertia-vue3';

    //import ref from vue
    import { ref } from 'vue';

    //import inertia adapter
    import { Inertia } from '@inertiajs/inertia';

    //import sweet alert2
    import Swal from 'sweetalert2';

    export default {
        //layout
        layout: LayoutApp,

        //register component
        components: {
            Head,
            Link,
            Pagination
        },

        //props
        props: {
            categories: Object,
        },

        //composition API
        setup() {

            //define state search
            const search = ref('' || (new URL(document.location)).searchParams.get('q'));

            //define method search
            const handleSearch = () => {
                Inertia.get('/apps/categories', {

                    //send params "q" with value from state "search"
                    q: search.value,
                });
            }

            //method destroy
            const destroy = (id) => {
                Swal.fire({
                    title: 'Apakah anda yakin?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Ya, hapus ini !'
                })
                .then((result) => {
                    if (result.isConfirmed) {

                        Inertia.delete(`/apps/categories/${id}`);

                        Swal.fire({
                            title: 'Terhapus!',
                            text: 'Kategori berhasil di hapus.',
                            icon: 'success',
                            timer: 2000,
                            showConfirmButton: false,
                        });
                    }
                })
            }

            //return
            return {
                search,
                handleSearch,
                destroy
            }

        }
    }
</script>

<style>

</style>
