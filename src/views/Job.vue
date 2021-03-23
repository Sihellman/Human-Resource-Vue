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
  <div class="job">
    <table>
    <thead>
    
    <th>name</th>
    <th>competencies</th>
    <th>&nbsp;&nbsp;&nbsp;&nbsp;</th>
    </thead>
    <tbody>
    <tr v-for="job in jobs">
    
    <td>{{job.name}}</td>
    <td><button v-on:click="showRecipe(job)">competencies</button></td>
     <td>
<button v-on:click="showForm(job)">Edit</button>
    </td>
    <dialog id="form-edit">
      <form method="dialog">
      
        <p>Job name: <input type="text" v-model="currentJob.name"></p>
       
        
       <button v-on:click="updateRecipe(currentJob)">Update</button>
        <button v-on:click="destroyRecipe(currentJob)">Destroy</button>
         
        
        <button>Close</button>
      </form>
    </dialog>
    </tr>
    </tbody>
    </table>
    
      <dialog id="recipe-details">
      <form method = "dialog">
      <button v-on:click="close()">Close</button>
      <button v-on:click="linkOrUnlinkComp(comps, currentJob)">Apply</button>
      <div v-for="comp in comps">
      
       <input type="checkbox"  v-model="comp.toggle" true-value="yes"
  false-value="no">
  <label for="comp"> {{comp.name}}</label><br>
      
      </div>
      </form>
      </dialog>
      
    
    <form action = "#">
        
        <p>Job name: <input type="text" v-model="newName"></p>
       
        <button v-on:click="createRecipe(currentJob)">Create</button>
       
        

      </form>



  </div>
</template>

<style>
</style>

<script>
var axios = require("axios");

export default {
  data:
 
    function() {
      
    return {
      message: "Welcome to Vue.js!",
      jobs: [],
      comps: [],
      currentComp: {},
      currentJob: {},
      toggle: 'no',
      checkedComps: [],
      newName:"",
      
      
      
     

    };
  },
  created: function() {
    this.getRecipe();
    //this.getComp();
    
  },
  methods: {
    getRecipe: function() {
      axios.get("http://localhost/project2/data_p2.php").then(response => {
        const params = new URLSearchParams();
      params.append('mode', "all");
      params.append('type', "job");
     
     
            axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        //console.log("Success creating recipe", response.data);
        for (let i = 0; i < response.data.length; i++){
            this.jobs.push(response.data[i]);
            console.log(response.data[i]);
        }
        
      });


        
      });
    },
    showForm: function(job) {
      console.log(job);
      this.currentJob = job;
      document.querySelector("#form-edit").showModal();
    },
    getCompLink: function(job) {
      let checkedComps = [];
      const params = new URLSearchParams();
          params.append('mode', "getlinks");
          params.append('type', "job");
          params.append('job_id', job.job_id);
          axios.post("http://localhost/project2/data_p2.php", params).then(response => {
          //console.log( response.data[0]);
          //console.log(this.comps[0]);
          //console.log("comps ", this.comps);
          //console.log("checkedComps ", this.checkedComps, " ya");
          
          for (let i = 0; i < response.data.length; i++){
            checkedComps.push(response.data[i]);
    
            
          }
          //console.log(checkedComps, "checkedComps");    
          this.getComp(checkedComps);
        
         });
         //console.log(checkedComps);
         
      
        

        
      
    },
    getComp:function(checkedComps){
      this.comps.length = 0;
      const param = new URLSearchParams();
      param.append('mode', "all");
      param.append('type', "competency");
     
     
            axios.post("http://localhost/project2/data_p2.php", param).then(response => {
        //console.log("Success creating recipe", response.data);
       
        
        for (let i = 0; i < response.data.length; i++){
            this.comps.push(response.data[i]);
            //console.log("foo");
            //console.log(checkedComps);
            for (let j = 0; j < checkedComps.length; j++){
              if (response.data[i].competency_id === checkedComps[j].competency_id){
                //console.log("hoooo");
                 this.comps[i].toggle = 'yes';
              }
            }
           
            //console.log(response.data[i]);
        }
        
      });


    },  
    createRecipe: function(job) {
      console.log("Create the recipe...");
      const params = new URLSearchParams();
      params.append('mode', "save");
      params.append('type', "job");
      params.append('job', this.newName);
     

      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        console.log("Success creating recipe", response.data);
        this.jobs.push(response.data[0]);
      });

    },
    updateRecipe: function(job) {
      
       
         const params = new URLSearchParams();
      params.append('mode', "save");
      params.append('type', "job");
      params.append('job_id', job.job_id);
      params.append('job', job.name);
      

      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
        console.log("Success updating recipe", response.data);
         for (let i = 0; i < this.jobs.length; i++){
          if (response.data[0].job_id === this.jobs[i].job_id){
            this.jobs.splice(i, 1, response.data[0]);
           
          }
       
        }
        
        
      });
     
    },
    destroyRecipe: function(job) {
     
        const params = new URLSearchParams();
      params.append('mode', "delete");
      params.append('type', "job");
      params.append('job_id', job.job_id);
      
     
     
     
      axios.post("http://localhost/project2/data_p2.php", params).then(response => {
         console.log("Success deleting recipe", response.data);
        
        for (let i = 0; i < this.jobs.length; i++){
          if (parseInt(job.job_id) === this.jobs[i].job_id){
            this.jobs.splice(i, 1);
            console.log("yay");
          }
        }      
        
      });
      
     
    },
    showRecipe: function(job) {
      //console.log("comps ", this.comps);
      //console.log("checkedComps ", this.checkedComps);
      this.currentJob = job;
      this.getCompLink(this.currentJob);
      document.querySelector("#recipe-details").showModal();


          
          
   

    },
    close:function(){
      // for (let i = 0; i < this.comps.length; i++){
      //   //this.comps[i].toggle = 'no';
      // }
      
      this.comps.length = 0;
      //this.checkedComps.length = 0;
      //console.log("comps ", this.comps);
      //console.log("checkedComps ", this.checkedComps);
    },
    linkOrUnlinkComp: function(comps, job){
      console.log(job);
      for (let i = 0; i < comps.length; i++){
        if (comps[i].toggle === 'yes'){
          //console.log(comps[i].competency_id);
           const params = new URLSearchParams();
          params.append('mode', "link");
          params.append('type', "job");
          params.append('job_id', job.job_id);
          params.append('type', "competency");
          params.append('competency_id', comps[i].competency_id);
          axios.post("http://localhost/project2/data_p2.php", params).then(response => {
          //console.log( response.data);
        
        
        
         });
        }
        else{
          const params = new URLSearchParams();
          params.append('mode', "unlink");
          params.append('type', "job");
          params.append('job_id', job.job_id);
          params.append('type', "competency");
          params.append('competency_id', comps[i].competency_id);
          axios.post("http://localhost/project2/data_p2.php", params).then(response => {
          //console.log( response.data);
        
        
        
         });

        }
        
      }
      
      
    },
    // getLinks: function(job){
    //    const params = new URLSearchParams();
    //       params.append('mode', "getlinks");
    //       params.append('type', "job");
    //       params.append('job_id', job.job_id);
    //       axios.post("http://localhost/project2/data_p2.php", params).then(response => {
    //       console.log( response.data[0]);
    //       console.log(this.comps[0]);
    //       for (let i = 0; i < response.data.length; i++){
    //         this.checkedComps.push(response.data[i]);
    //         for (let j = 0; j < this.comps.length; j++){
              
    //           if (response.data[i].competency_id === this.comps[j].competency_id){
    //             console.log("yahooo");
    //             this.comps[j].toggle = 'yes';
    //             if(this.comps[j].toggle === 'yes'){
    //                 console.log("dfdfdf");
    //             }
                
                
    //           }
    //         }
            
    //       }
          
          
        
        
        
    //      });
    // },



  },
 
};
</script>

