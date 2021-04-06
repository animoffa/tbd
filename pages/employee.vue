<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="employee" :list="employee" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)"  />
       <transition name="modal">
          <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
                <form class="modal" @submit.prevent="onSubmit" >
                <div class="modal-close" @click="closeModal"> ╳</div>
                    <h3 class="modal-title">{{edit ? 'Изменить сотрудника' : 'Добавить сотрудника'}}</h3>
                    <div class="modal-content">
                        <input class="modal_input" placeholder="ФИО сотрудника" v-model.trim="fullName"/>
                         <input class="modal_input" placeholder="Дата рождения" v-model.trim="birthday"/>
                        <input class="modal_input" type="number" placeholder="Телефон" v-model.trim="phone"/>
                        <select class="modal_input" v-model.trim="post">
                          <option value="Должность" selected>Должность</option>
                          <option v-for="p of posts" :key="p.name">{{p.name}}</option>
                        </select>
                         <select class="modal_input" v-model.trim="pass">
                          <option value="Паспорт" selected>Паспорт</option>
                          <option v-for="p of passports" :key="p.name">{{p.seria}} {{p.num}}</option>
                        </select>
                          <select class="modal_input" v-model.trim="departament">
                          <option value="Отдел" selected>Отдел</option>
                          <option v-for="p of departaments" :key="p.name">{{p.name}}</option>
                        </select>
                    </div>
                     <button class="modal-button" @click="closeModal" type="submit">{{edit ? 'Изменить' : 'Создать'}}</button>
                </form>
            </div>
    </transition>
    </div>
  </div>
</template>

<script>
import Nav from '@/components/Nav.vue'
import Table from '@/components/Table.vue'
import * as axios from 'axios';

export default {
  components:{
    Nav,Table
  },
  data(){
    return{
       isOpen: false,
       employee:[],
       fullName:'',
       birthday:'',
       departaments:[],
       departament:'Отдел',
       passports:[],
       pass:'Паспорт',
       phone:'',
       post:'Должность',
       posts:[],
      edit: false,
    }
  },
  mounted() {
      axios.get('http://localhost:4201/api/EmployeeList').then((response)=>{
          this.employee=response.data
      })
  },
  methods: {
    deleteItem(id){
      axios.delete(`http://localhost:4201/api/Employee/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.employee.find(item => item.id == id);
      this.fullName=el.fullName
      this.birthday = el.birthday
      this.post = el.post.name
      this.phone = el.phone
      this.edit=id
      this.showModal()
    },
    showModal() {
      this.isOpen = !this.isOpen
      window.document.body.classList.add("modal-active")

      axios.get('http://localhost:4201/api/PostList').then((response)=>{
          this.posts=response.data
      })

      axios.get('http://localhost:4201/api/PassportList').then((response)=>{
          this.passports=response.data
      })

       axios.get('http://localhost:4201/api/DepartamentList').then((response)=>{
          this.departaments=response.data
      })
    },
    closeModal() {
        this.isOpen = !this.isOpen
        console.log(this.post)
        window.document.body.classList.remove("modal-active")
    },
    onSubmit() {
     let el = this.posts.find(item => item.name == this.post)
       let p = this.passports.find(item => item.name == this.pass)
         let d = this.departaments.find(item => item.name == this.departament)
     console.log('dd', el)
      if (this.edit) {
       axios.post(`http://localhost:4201/api/Employee/`, {"id":this.edit, "fullName": this.fullName, "birthday": this.birthday, "phone": this.phone, "post": el, "passport": p, "departament":d}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Employee', {"fullName": this.fullName, "birthday": this.birthday, "phone": this.phone, "post": el, "passport": p, "departament":d}).then((response)=>{
           window.location.reload(false); 
           this.closeModal()
      })
      }
    }
  }
}
</script>

<style lang="less">

</style>
