import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import axios from "axios";

axios.defaults.baseURL =
  process.env.NODE_ENV === "development" ? "http://localhost:3000" : "/";

Vue.config.productionTip = false;

new Vue({
  router,
  render: h => h(App)
}).$mount("#app");


<template>
<div class="home">
<form action = "#">
        <h1>Add Employee</h1>
         
        <p>First name: <input type="text" v-model="currentEmployee.fname"></p>
        <p>Last name: <input type="text" v-model="currentEmployee.lname"></p>
        <p>email: <input type="text" v-model="currentEmployee.email"></p>
        <p>job_id: <input type="number" v-model="currentEmployee.job_"></p>
        <button v-on:click="createRecipe(currentEmployee)">Create</button>
       
        

      </form>
<table>

  <thead>
    
    <th>First name</th>
    <th>Last name</th>
    <th>Email</th>
    <th>Job</th>
    <th>&nbsp;&nbsp;&nbsp;&nbsp;</th>
    <th>&nbsp;&nbsp;&nbsp;&nbsp;</th>
  </thead>
  <tbody>

  
  
  <tr v-for="employee in employees">
    
    <td>{{employee.fname}}</td>
    <td>{{employee.lname}}</td>
    <td>{{employee.email}}</td>
    <td>{{employee.job_id}} </td>
    <td><button v-on:click="getScores(employee)"> scores</button></td>
    <dialog id="recipe-detail">
     <form method = "dialog">
      <button v-on:click="close()">Close</button>
      <table>
      <thead>
      <th>Competency</th>
      <th>Score</th>
      </thead>
      <tbody>
      <tr v-for="score in scores">
      <td>{{score.name}}</td>
      <td>{{score.score}}</td>
      </tr>
      
      </tbody>
      </table>
     
      </form>
    </dialog>
    <td>
<button v-on:click="showForm(employee)">Edit</button>
    </td>
    <dialog id="form-edit">
      <form method="dialog">
       
         
        <p>First name: <input type="text" v-model="currentEmployee.fname"></p>
        <p>Last name: <input type="text" v-model="currentEmployee.lname"></p>
        <p>email: <input type="text" v-model="currentEmployee.email"></p>
        <p>job_id: <input type="number" v-model="currentEmployee.job_id"></p>
        
       <button v-on:click="updateRecipe(currentEmployee)">Update</button>
        <button v-on:click="destroyRecipe(currentEmployee)">Destroy</button>
        <button>Close</button>
      </form>
    </dialog>
   
  </tr>

</tbody>
</table>

    
      
      

    


  </div>
</template>

<style>
</style>

<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      message: "Welcome to Vue.js!",
      employees: [],
      jobs: [],
      newFname: "",
      newLname: "",
      newEmail: "",
      newJob: "",
      newEmployee:{},
      newEmployeeId: "",
      currentEmployee: {},
      jobs: [],
      comps: [],
      scores: [],
     
     

    };
  },
  created: function() {
    this.getRecipe();
  },
  methods: {
    getScores:function(employee){
      
      const params = new URLSearchParams();
      let comps = [];
      //console.log("hook");
      params.append('mode', "getlinks");
      params.append('type', "job");
      params.append('job_id', employee.job_id);
     
      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        //console.log("Success creating recipe", response.data);
       for (let i = 0; i < response.data.length; i++){
          this.comps.push(response.data[i]);
         //console.log("good", response.data[0]);
        comps.push(response.data[i]);
        console.log("ya");
       }
        //console.log(comps);
        this.getScores2(employee, comps);
      });
      //console.log(comps);
      
      

    },
    getScores2:function(employee, comps){
      //console.log("looch");
      //console.log(comps.length);
     
        const param = new URLSearchParams();
        //console.log( "gooch");
        param.append('mode', "getlinks");
        param.append('type', "job");
        param.append('job_id', employee.job_id);
        param.append('type', "competency");
        
        param.append('employee_id', employee.employee_id);
        //console.log(employee.employee_id);
        //console.log(employee.job_id);
      
        axios.post("http://localhost/project2/data_p2.php", param).then(response => {
          this.scores.length = 0;
          this.scores = [];
          //console.log(this.scores, "start");
          //console.log("Success creating recipe", response.data);
          for (let j = 0; j < response.data.length; j++){
            console.log(response.data[j]);
            this.scores.push(response.data[j]);
          }
           //console.log(this.scores);
           if (response.data.length === 0){
             this.scores.length = 0;
        
     
       }
          
          
          
        });

      
      
      document.querySelector("#recipe-detail").showModal();
      
      
      
    },
    close:function(){
      this.scores.length =0;
      this.comps.length = 0;
    },
    showForm: function(employee) {
      console.log(employee);
      this.currentEmployee = employee;
      document.querySelector("#form-edit").showModal();
    },
    createRecipe: function(currentEmployee) {
      console.log("Create the recipe...");
      
      const params = new URLSearchParams();
      params.append('mode', "save");
      params.append('type', "employee");
      params.append('fname', currentEmployee.fname);
      params.append('lname', currentEmployee.lname);
      params.append('email', currentEmployee.email);
      params.append('job_id', currentEmployee.job_id);
     

      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        console.log("Success creating recipe", response.data);
        this.employees.push(response.data[0]);
      });

    },
    updateRecipe: function(employee) {
     
       
         const params = new URLSearchParams();
      params.append('mode', "save");
      params.append('type', "employee");
      params.append('fname', employee.fname);
      params.append('lname', employee.lname);
      params.append('email', employee.email);
       params.append('employee_id', employee.employee_id);
       console.log(employee.job_id);
      params.append('job_id', employee.job_id);
     
     
      
      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        console.log("Success updating recipe", response.data);
        
        for (let i = 0; i < this.employees.length; i++){
          if (response.data[0].employee_id === this.employees[i].employee_id){
            this.employees.splice(i, 1, response.data[0]);
            //console.log(response.data[0].employee_id);
            //console.log(this.employees[i].employee_id);
          
          }
          // response.data[0].employee_id = parseInt(response.data[0].employee_id);
          //   response.data[0].job_id = parseInt(response.data[0].job_id);
          //   //console.log(response.data[0].employee_id )
          //   console.log(this.employees[i]);
          // if (response.data[0] === this.employees[i]){
            
          //   console.log("yay");
          // }
        }
        //this.employees[employee.employee_id - 1] = response.data[0];
       //console.log("Success updating recipe", response.data[0]);
        //this.getRecipe();
      });
     
     
    },
    destroyRecipe: function(employee) {
     
        const params = new URLSearchParams();
      params.append('mode', "delete");
      params.append('type', "employee");
       params.append('fname', employee.fname);
      params.append('lname', employee.lname);
      params.append('email', employee.email);
       params.append('job_id', employee.job_id);
      params.append('employee_id', employee.employee_id);
      
    // const found = this.employees.findIndex(element => element.employee_id = 17);
    // const isLargeNumber = (element) => element.employee_id === 17;
    // console.log(this.employees.findIndex(isLargeNumber));
     //console.log(found);
    //console.log(this.employees.indexOf(found));
    var x = employee.employee_id;
      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
         console.log("Success deleting recipe", employee);
         console.log(response.data.employee_id);
        for (let i = 0; i < this.employees.length; i++){
          if (parseInt(x) === this.employees[i].employee_id){
            this.employees.splice(i, 1);
            console.log("yay");
          }
        }      
          
        
      });
      

        
     
      //this.getRecipe();
     
    },
    
   
       
    getRecipe: function() {
      
        const params = new URLSearchParams();
      params.append('mode', "all");
      params.append('type', "employee");
     
      
     
     
     
            axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        console.log("Success getting recipe", response.data);
        for (let i = 0; i < response.data.length; i++){
            this.employees.push(response.data[i]);
        }
        
      });
      const param = new URLSearchParams();
      param.append('mode', "all");
      param.append('type', "job");
     
      
     
     
     
            axios.post("http://localhost/project2/data_p2.php", param).then(response => {
        //console.log("Success getting recipe", response.data);
        for (let i = 0; i < this.employees.length; i++){
          for (let j = 0; j < response.data.length; j++){
              this.employees[i].job_name = response.data[j].name;
              console.log(this.employees[i].job_name);
          }
            
        }
        
      });

        
    
    },
    



  },
 
};
</script>


