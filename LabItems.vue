<template>
<div class="mt-2"> <h3>List of labitems</h3></div>
<data-table :rows="tableData" :pagination="pagination" striped sn filter responsive @loadData="loadData">

<template #thead>
    <table-head>Lab Test Name</table-head>
    <table-head>Type</table-head>
    <table-head>Units</table-head>
    <table-head>Action</table-head>
</template>
<template #tbody="{row}">
            <table-body v-text="row.name"/>
            <table-body> <span v-if="row.lab_item_id">Item</span> <span v-else>Group</span>  </table-body>
            <table-body v-text="row.unit"/>
            <table-body>
                <div class="d-flex align-items-center">
                    <div class="dt-ml-4">
                        <router-link v-if="auth_user_type===2 || auth_user_type===3 || auth_user_type===4" :to="{name: 'editlabitem', params: { id: row.id }}" class="btn btn-primary">Edit</router-link>
                        <button v-if="auth_user_type===4" class="btn btn-danger ml-2" @click="deleteLabitem(row.id)">Delete</button>
                    </div>
                </div>
            </table-body>
        </template>
    </data-table>

</template>

<script>
    import axios from 'axios';
    import { ref, } from "vue"
    import { DataTable, TableBody, TableHead, } from "@jobinsjp/vue3-datatable"
    const Paginated = { components: { TableBody, TableHead, DataTable },
    setup() {
            const tableData = ref([])
            const pagination = ref({})
            const auth_user_type = ref(window.authUser.user_type)
            const loadData = async (query) => {
                const { data: { data, totalItems, ...paginationResponse } } = await axios.get("/api/labitems", {
                    params: {
                        page: query.page,
                        size: query.per_page,
                        search:query.search,
                    },
                })
                tableData.value = data
                pagination.value = { ...pagination.value, page: query.page, total: totalItems, ...paginationResponse }
            }
            const formatItem = (item) => Array.isArray(item) ? item[0] : item
            const formatUrl = (url) => url.startsWith("http") ? url : `http://${url}`
            const deleteLabitem = (id) => {
                if(confirm('Are you sure to delete this record?'))
                axios.get('/sanctum/csrf-cookie').then(response => {
                axios.delete(`/api/labitems/${id}`)
                    .then(response => {
                        let i = tableData.value.map(item => item.id).indexOf(id); 
                        tableData.value.splice(i, 1)
                    })
                    .catch( (error) => {
                        console.error(error);
                    });
                })
            }
            return { tableData, pagination, auth_user_type, loadData, formatItem, formatUrl, deleteLabitem }
        },
    }
    export default Paginated;
    
</script>
<style src='@jobinsjp/vue3-datatable/dist/style.css'>

</style>
