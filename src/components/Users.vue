<template>
  <div class="users">
    <div class="text-center text-success">{{ message }}</div>
    <h3>Users</h3>
    <div class="card">
      <div class="card-header">
        Add a new user
      </div>
      <div class="card-body">
        <form class="form-inline" v-on:submit.prevent="onSubmit">
          <div class="form-group">
            <label>Username</label>
            <input v-model="userData.username" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="form-group">
            <label>Email</label>
            <input v-model="userData.email" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="form-group">
            <label>Address</label>
            <input v-model="userData.address" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="ml-auto text-right">
            <button type="submit" class="btn btn-primary my-2">Add</button>
          </div>
        </form>
      </div>
    </div>

    <div class="card mt-5">
      <div class="card-header d-flex">
       <div class="mr-auto p-2">User List</div>
       <div class="p-2"><input v-model="search" type="text" placeholder="Search..."></div>
      </div>
      <div class="card-body">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <tr>
                <th>
                  Username
                </th>
                <th>
                  Email
                </th>
                <th>
                  Address
                </th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(user, index) in searchedUsers" v-bind:key="user.id">
                <template v-if="editData.id == user.id">
                  <td><input v-model="editData.username" type="text"></td>
                  <td><input v-model="editData.email" type="text"></td>
                  <td><input v-model="editData.address" type="text"></td>
                  <td>
                    <span class="icon">
                      <i  @click="onEditSubmit(user)" class="fa fa-check"></i>
                    </span>
                    <span class="icon">
                      <i  @click="onCancel" class="fa fa-ban"></i>
                    </span>
                  </td>
                </template>
                <template v-else>
                  <td>
                    {{ user.username }}
                  </td>
                  <td>
                    {{ user.email }}
                  </td>
                  <td>
                    {{ user.address }}
                  </td>
                  <td>
                    <a href="#" class="icon">
                      <i v-on:click="onDelete(user)" class="fa fa-trash"></i>
                    </a>
                    <a href="#" class="icon">
                      <i v-on:click="onEdit(user)" class="fa fa-pencil"></i>
                    </a>
                  </td>
                </template>
              </tr>

            </tbody>
          </table>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

const axios = require('axios');

export default {
  name: 'Users',
  data () {
    return {
      editId: null,
      search: '',
      userData: {
        'id' : '',
        'username': '',
        'email': '',
        'address': ''
      },
      editData: {
        'id' : '',
        'username': '',
        'email': '',
        'address': ''
      },
      users: [],
      message: ''
    }
  },
  created() {
    this.getUsers();
  },
  computed:{
    searchedUsers(){

      if(this.search != '')
      {

          let searchUsername = this.search.toLowerCase();
          return this.users.filter(function(item, index, array) {

              return  item.username.toLowerCase().indexOf(searchUsername) !== -1;

          });

      } else {

          return this.users;

      }

    }
  },
  methods: {
    getUsers() {

        let data = [];

        axios.get('https://jsonplaceholder.typicode.com/users')
        .then(response => {
          response.data.forEach(function(item, i, arr) {

            data.push({
              id: item.id,
              username: item.username,
              email: item.email,
              address: item.address.city + ' ' + item.address.street + ' ' + item.address.suite,
            });

          });

        });

        this.users = data;

    },
    onSubmit(){
      if(this.userData.username != '' && this.userData.email != '')
      {
          this.users.push({
              username: this.userData.username,
              email: this.userData.email,
              address: this.userData.address
          });

          this.userData.username = '';
          this.userData.email = ''; 
          this.userData.address = '';

          this.message = "New user saved";
          this.clearMessage();

      } else {

          this.message = "All fields must be filled";
          this.clearMessage();

      }
      
      
    },
    onDelete(user) {
      
      this.users.splice(this.users.indexOf(user), 1);

    },
    onEdit(user) {

      this.editData.id = user.id;
      this.editData.username = user.username;
      this.editData.email = user.email;
      this.editData.address = user.address;
      this.editId = this.users.indexOf(user);

    },
    onCancel() {

      this.editClearData();

    },
    onEditSubmit(id){

      if(this.editData.username != '' && this.editData.email != '')
      {
          this.users[this.editId].id = this.editData.id;
          this.users[this.editId].username = this.editData.username;
          this.users[this.editId].email = this.editData.email;
          this.users[this.editId].address = this.editData.address;

          this.message = "New data saved";
          this.editClearData();
          this.clearMessage();

      } else {

          this.message = "All fields must be filled";
          this.clearMessage();

      }
    },
    editClearData(){

        this.editData.id = '';
        this.editData.username = '';
        this.editData.email = '';
        this.editData.address = '';
        this.editId = null;
    },
    clearMessage(){
        setTimeout(() => this.message = "", 1000);
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3{
  text-align: center;
  margin-top: 30px;
  margin-bottom: 20px;
}
.icon{
  margin-right: 10px;
}
.icon i{
  cursor: pointer;
}
</style>
