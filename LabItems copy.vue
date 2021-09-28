<template>
<div class="mt-2"> <h3>List of labitems</h3></div>
<div  class="table-responsive">
<button v-if="auth_user_type===2 || auth_user_type===3 || auth_user_type===4" type="button" class="btn btn-info my-2" @click="this.$router.push({name:'addlabitem'})">Add Lab test item</button>
<table  class="table align-items-center table-flush table-bordered" id="datatable">
  <thead>
    <tr>
      <th>S.NO</th>
      <th>Lab Test Name</th>
      <th>Type</th>
      <!-- <th>Lab Sub Group</th> -->
      <th>Units</th>
      <th>Action</th>
    </tr>
  </thead>
  
  <tbody v-if="labitems">
    <tr v-for="(labitem, index) in labitems" :key="labitem.id">
    
      <td>{{ index + 1 }}</td>   
      <td>{{ labitem.name }} </td>      
      <td><span v-if="labitem.lab_item_id"> Item </span> <span v-else>Group</span></td>      
      <!-- <td>{{  }}</td> -->
      <td>{{ labitem.unit }}</td>      
      <td>
        <div class="d-flex justify-content-start">
            <router-link v-if="auth_user_type===2 || auth_user_type===3 || auth_user_type===4" :to="{name: 'editlabitem', params: { id: labitem.id }}" class="btn btn-primary">Edit
            </router-link>
            <button v-if="auth_user_type===4" class="btn btn-danger ml-2" @click="deleteLabitem(labitem.id)">Delete</button>
        </div>
      </td>
    
    </tr>
  </tbody>
  <div v-else> <div><h3>Loading data</h3></div></div>
</table>
</div>
<!-- Data : {{ labitems }} -->
</template>

<script>

import 'jquery/dist/jquery.min.js';
//Datatable Modules
import "datatables.net-dt/js/dataTables.dataTables"
import "datatables.net-dt/css/jquery.dataTables.min.css"
// import "datatables.net-buttons/js/dataTables.buttons.js"
// import "datatables.net-buttons/js/buttons.colVis.js"
// import "datatables.net-buttons/js/buttons.flash.js"
// import "datatables.net-buttons/js/buttons.html5.js"
// import "datatables.net-buttons/js/buttons.print.js"
import $ from 'jquery';

export default {
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
}
</script>
