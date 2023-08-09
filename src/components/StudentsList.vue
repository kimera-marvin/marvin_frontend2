<template>
    <div class="list row">
      <div class="col-md-8">
        <div class="input-group mb-3">
          <input type="text" class="form-control" placeholder="Search by Location"
            v-model="search_location"/>

          <div class="input-group-append">
            &nbsp;&nbsp;&nbsp;&nbsp;
            <button class="btn btn-outline-secondary" type="button"
              @click="searchLocation"
            >
              Search
            </button>
           
          </div>
        </div>
      </div>
      <div class="col-md-6">
        <h4>Students List
          &nbsp;&nbsp;&nbsp;&nbsp;
            <button class="btn btn-sm btn-danger" type="button"
              @click="removeAllStudents"
            >
              Delete All
            </button>
        </h4>
        <ul class="list-group">
          <li class="list-group-item"
            :class="{ active: index == currentIndex }"
            v-for="(student, index) in students"
            :key="index"
            @click="setActiveStudent(student, index)"
          >
          {{ student.last_name }} {{ student.first_name }} ({{ student.physical_address }})
          </li>
        </ul>
  
        <!-- <button class="m-3 btn btn-sm btn-danger" @click="removeAllStudents">
          Remove All
        </button> -->
      </div>
      <div class="col-md-6">
        <div v-if="currentStudent">
          <h4>Student Details</h4>
          <div>
            <label><strong>ID :</strong></label> {{ currentStudent.id}}
          </div>
          <div>
            <label><strong>First Name:</strong></label> {{ currentStudent.first_name }}
          </div>
          <div>
            <label><strong>Last Name:</strong></label> {{ currentStudent.last_name }}
          </div>
          <div>
            <label><strong>Gender:</strong></label> {{ currentStudent.gender }}
          </div>
          <div>
            <label><strong>Class:</strong></label> {{ currentStudent.class }}
          </div>
          <div>
            <label><strong>Physical Address:</strong></label> {{ currentStudent.physical_address }}
          </div>
          <!-- <div>
            <label><strong>Status:</strong></label> {{ currentStudent.status ? "At School" : "Left School" }}
          </div> -->

          <br />
          <!-- <br /> -->
          <button class="btn btn-sm btn-danger" type="button"
              @click="deleteStudent">
                Delete
            </button>
            &nbsp;&nbsp;&nbsp;&nbsp;
  
          <router-link :to="'/students/' + currentStudent.id">
            
            <button class="btn btn-sm btn-secondary">
                Update
            </button>
        
          </router-link>
        
          
        
        </div>
        <div v-else>
          <br />
          <p>Please click on a Student ...</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import StudentDataService from "../services/StudentDataService";

  // VueRouter from 'vue-router';
  
  export default {
    name: "students-list",
    data() {
      return {
        students: [],
        currentStudent: null,
        currentIndex: -1,
        search_location: ""
      };
    },
    methods: {
      // method that returns students
      retrieveStudents() {
        StudentDataService.getAll()
          .then(response => {
            this.students = response.data;
            console.log(response.data);
          })
          .catch(e => {
            console.log(e);
          });
      },
      // resting the sytem
      refreshList() {
        this.retrieveStudents();
        this.currentStudent = null;
        this.currentIndex = -1;
      },
      // when you click a student
      setActiveStudent(student, index) {
        this.currentStudent = student;
        this.currentIndex = index;
      },

      // delete student
      deleteStudent() {
        StudentDataService.delete(this.currentStudent.id)
          .then(response => {
            console.log(response.data);
            this.refreshList();
            //this.$router.replace('/');
            /*const currentLocation = this.$route.path;
              if (currentLocation !== '/') {
                this.$router.push('/');
              }
              */
            //this.$router.push({ name: "students" });
          })
          .catch(e => {
            console.log(e);
          });
      },
  
      removeAllStudents() {
        StudentDataService.deleteAll()
          .then(response => {
            console.log(response.data);
            this.refreshList();
          })
          .catch(e => {
            console.log(e);
          });
      },
      // when you click search
      searchLocation() {
        StudentDataService.findByPhysicalAddress(this.search_location)
          .then(response => {
            this.students = response.data;
            console.log(response.data);
          })
          .catch(e => {
            console.log(e);
          });
      }
    },
    mounted() {
      this.retrieveStudents();
    }
  };
  </script>
  
  <style>
  .list {
    text-align: left;
    max-width: 750px;
    margin: auto;
  }
  </style>