<template>
  <div>
    <h2 class="style-title">Vehicle Groups</h2>
    <p>Each vehicle belongs to a group, and will inherit the same settings.</p>
    <p>&nbsp;</p>
    <vs-button color="primary" type="filled" to="/vehicle_groups/add" style="margin-bottom:20px;">Add vehicle group</vs-button>
    <vs-table
        pagination
        max-items="12"
        stripe
        search
        v-model="data"
        :data="data">

      <template slot="thead">
        <vs-th>Enabled</vs-th>
        <vs-th sort-key="name">Name</vs-th>
        <vs-th>Trigger duration</vs-th>
        <vs-th>Time between triggers</vs-th>
        <vs-th>Options</vs-th>
      </template>

      <template slot-scope="{data}">
        <vs-tr v-for="item in data" v-bind:key="item.id">
          <vs-td>{{item.enabled ? 'Yes' : 'No'}}</vs-td>
          <vs-td>{{item.name}}</vs-td>
          <vs-td>{{item.trigger_duration_seconds}} sec</vs-td>
          <vs-td>{{item.time_between_trigger}} sec</vs-td>
          <vs-td><vs-button v-on:click="edit(item.id)" class="mr-3 mb-2">Edit</vs-button><vs-button color="danger" @click="openPopup(item.id)"  class="mr-3 mb-2">Delete</vs-button></vs-td>
        </vs-tr>
      </template>

    </vs-table>
    <vs-popup class="holamundo"  title="Are you sure you want to delete this vehicle group" :active.sync="popupActive">
      <vs-button color="danger" v-on:click="deleteDeviceGroup" class="mr-3 mb-2">Yes</vs-button>
      <vs-button @click="popupActive=false"  class="mr-3 mb-2">No</vs-button>
    </vs-popup>
  </div>
</template>

<script>

export default {
    data(){
      return {
        popupActive:false,
        id:1,
        data:[]
      }
    },
    methods:{
      getData(){
        let user = JSON.parse(localStorage.user)
        let token = user.api_token
        this.$axios.get('/api/admin/vehicle_groups', {params:{api_token:token}}).then((res) =>{
          this.data = res.data
        }).catch((err) => {
          console.log(err);
        })
      },
      edit(id){
        window.location.href = "/app/vehicle_groups/edit/" + id
      },
      openPopup(id){
        this.id = id
        this.popupActive = true
      },
      deleteDeviceGroup(){
        let user = JSON.parse(localStorage.user)
  			let token = user.api_token
  			let url = '/api/admin/vehicle_group/delete/' + this.id
  			this.$axios({
  				method: 'DELETE',
                  url: url,
  								params: {
  									'api_token': token
  								},
  			}).then((res) =>{
          this.popupActive = false
          this.getData()
        }).catch((err) => {
          console.log(err);
        })
      }
    },
    beforeMount(){
    this.getData()
 },
}
</script>
