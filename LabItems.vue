<template>
<div class="mt-2"> <h3>List of labitems</h3></div>
<data-table :rows="tableData" :pagination="pagination" striped sn @loadData="loadData">

<template #thead>
    <table-head>S.No</table-head>
    <table-head>Lab Test Name</table-head>
    <!-- <table-head>Type</table-head> -->
    <table-head>Units</table-head>
    <table-head>Action</table-head>
</template>
<template #tbody="{row}">
            <table-body v-text="row.name"/>

            <table-body v-text="row.units.toLocaleString()"/>

            <table-body>
                <div class="d-flex align-items-center">
                    <div class="dt-ml-4">
                        <router-link v-if="auth_user_type===2 || auth_user_type===3 || auth_user_type===4" :to="{name: 'editlabitem', params: { id: labitem.id }}" class="btn btn-primary">Edit</router-link>
                        <button v-if="auth_user_type===4" class="btn btn-danger ml-2" @click="deleteLabitem(labitem.id)">Delete</button>
                    </div>
                </div>
            </table-body>
        </template>
    </data-table>

</template>

<script>
    import "@jobinsjp/vue3-datatable"
    import { defineComponent, ref, } from "vue"
    import { DataTable, TableBody, TableHead, } from "jobinsjp/vue3-datatable"

    const Paginated = defineComponent({ components: { TableBody, TableHead, DataTable },
    setup() {
            const tableData = ref([])
            const pagination = ref({})
            const loadData = async (query) => {
                const { data: { data, totalItems } } = await this.$axios.get("/api/labitems", {
                    params: {
                        page: (query.page - 1) < 0 ? 0 : query.page - 1,
                        size: query.per_page,
                    },
                })
                tableData.value = data
                pagination.value = { ...pagination.value, page: query.page, total: totalItems }
            }
            const formatItem = (item) => Array.isArray(item) ? item[0] : item
            const formatUrl = (url) => url.startsWith("http") ? url : `http://${url}`
            return { tableData, pagination, loadData, formatItem, formatUrl }
        },

    })
    export default Paginated;

    /* beforeRouteEnter(to, from, next) {
        if (!window.Laravel.isLoggedin) {
            window.location.href = "/lsyt/login";
        } else next();
    } */


/* export default {
    name: 'Labitems',
    data() {
        return {
            labitems:{},
            auth_user_type:'',
            // dtRef: null,
        }
    },
    computed() {
        this.labitems = $('#datatable').DataTable();
    },
    created() {
        this.$axios.get('/sanctum/csrf-cookie').then(response => {
            this.$axios.get('/api/labitems')
                .then(response => {
                    this.labitems = response.data;
                    setTimeout(function(){
                    $('#datatable').DataTable();
                    },1000 );
                    // console.log(response.data[0])
                    this.auth_user_type = window.Laravel.user.user_type
                })
                .catch(function (error) {
                    console.error(error);
                });
        })
    },
    
    methods: {
        deleteLabitem(id) {
            if(confirm('Are you sure to delete this record?'))
            this.$axios.get('/sanctum/csrf-cookie').then(response => {
                this.$axios.delete(`/api/labitems/${id}`)
                    .then(response => {
                        let i = this.labitems.map(item => item.id).indexOf(id); // find index of your object
                        this.labitems.splice(i, 1)
                    })
                    .catch(function (error) {
                        console.error(error);
                    });
            })
        }
    },
    beforeRouteEnter(to, from, next) {
        if (!window.Laravel.isLoggedin) {
            window.location.href = "/lsyt/login";
        } else next();
    }
} */
</script>
