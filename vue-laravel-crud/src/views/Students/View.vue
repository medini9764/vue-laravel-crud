
import { onMounted } from 'vue';

import { createHydrationRenderer } from 'vue';
<template>
    <div class="card">
        <div class="card-header">
            <h4>
                Student
                <!-- <RouterLink to="/student/create" class="btn btn-primary float-end" >Add Student</RouterLink> -->
          
            </h4>
        </div>
        
        <div class="card-body">
            <div class="col-md-6 mx-auto">
                <form @submit.prevent="save">
                    <div class="mb-3">
                        <label for="exampleInputEmail1" class="form-label" >Name</label>
                        <input type="text" v-model="student.name" class="form-control" placeholder="Student Name">
                        <span class="text-danger" v-if="errors?.name">{{ errors.name[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label for="exampleInputPassword1" class="form-label" >Address</label>
                        <input type="text" v-model="student.address" class="form-control" placeholder="Student Address ">
                        <span class="text-danger" v-if="errors?.address">{{ errors.address[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label for="exampleInputPassword1" class="form-label" >Phone Number</label>
                        <input type="text" v-model="student.phone" class="form-control" placeholder="Student Phone Number">
                        <span class="text-danger" v-if="errors?.phone">{{ errors.phone[0] }}</span>
                    </div>
                    
                    <button type="submit" class="btn btn-primary">Submit</button>
                </form>
            </div>
            <br>
            <table class="table table-striped">
                <thead>
                    <tr>
                        <th scope="col">#</th>
                        <th scope="col">Name</th>
                        <th scope="col">Address</th>
                        <th scope="col">Phone</th>
                        <th scope="col">Action</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="student in result" v-bind:key="student.id">
                        <th scope="row">{{student.id}}</th>
                        <td>{{student.name}}</td>
                        <td>{{student.address}}</td>
                        <td>{{student.phone}}</td>
                        <td>
                            <button type="button" class="btn btn-warning me-1" @click="edit(student)">Edit</button>
                            <button type="button" class="btn btn-danger" @click="remove(student)">Delete</button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
<script>
import axios from 'axios';
export default{
   
    name: 'Student',
    data(){
        return{
            result: {},
            student:{
                id: '',
                name: '',
                address: '',
                phone: '',
            },
            errors: {},
        }
    },
    created(){
        this.StudentLoad();
    },
    mounted(){
        console.log("mounted() called----");
    },
    methods:{
        StudentLoad(){
            var page = "http://127.0.0.1:8000/api/student";
            axios.get(page)
            .then(
                ({data}) => {
                    console.log(data);
                    this.result = data;
                }
            );
        },
        save()
        {
            if(this.student.id == ''){
                this.saveData();
            }else{
                this.updateData();
            }
        },
        saveData(){
            axios.post("http://127.0.0.1:8000/api/student", this.student)
            .then(
                ({data})=>{
                    this.StudentLoad();
                    this.student.name = '';
                    this.student.address = '';
                    this.student.phone = '';
                    this.id = '';
                }
            ) .catch(error => {
                this.errors = error.response.data.errors
        })
        },
        edit(student){
            this.student = student;
        },
        updateData(){
            var editrecords = 'http://127.0.0.1:8000/api/student/'+ this.student.id;
            axios.put(editrecords , this.student)
            .then(
                ({data})=>{
                    this.student.name = '';
                    this.student.address = '';
                    this.student.phone = '';
                    this.id = '';
                    this.StudentLoad();
                }
            );
        },
        remove(student){
            var deleterecode = `http://127.0.0.1:8000/api/student/${student.id}`;
            axios.delete(deleterecode);
            this.StudentLoad();
        },

    }
}
</script>
  