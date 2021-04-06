<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="cheque"  :list="cheques" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)" />
       <transition name="modal">
        <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
            <form class="modal" @submit.prevent="onSubmit">
                <div class="modal-close" @click="closeModal"> ╳</div>
                <slot name="title">
                    <h3 class="modal-title">{{edit ? 'Изменить чек' : 'Добавить чек'}}</h3>
                </slot>
                <slot name="body">
                    <div class="modal-content">
                        <select class="modal_input"  v-model.trim="client">
                          <option value="ФИО клиента" selected>ФИО клиента</option>
                          <option v-for="c of clients" :key="c.fullName">{{c.fullName}}</option>
                        </select>
                        <select class="modal_input" v-model.trim="employee">
                          <option value="ФИО сотрудника" selected>ФИО сотрудника</option>
                           <option v-for="c of employees" :key="c.fullName">{{c.fullName}}</option>
                        </select>
                          <ul class="listI" >
                            <li v-for="(item,i) of dishes" v-bind:key="i" v-bind:quote="item">{{item.name}}</li>
                        </ul>
                        <form @submit.prevent="onAddQuote">
                            <div class="textarea-container">
                    <select v-model.trim="dish" name="a" id="b">
                      <option value="Выберите блюдо" selected>Выберите блюдо</option>
                      <option v-for="c of allDishes" :key="c.name">{{c.name}}</option>
                        </select>
                                <button class="modal-button" type="submit">+</button>
                            </div>
                        </form>
                            <input class="modal_input" placeholder="Дата" v-model.trim="date"/>
                        <button class="modal-button"  type="submit">{{edit ? 'Изменить' : 'Создать'}}</button>
                    </div>
                </slot>
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
      cheques:[],
       isOpen: false,
       client:'ФИО клиента',
       clients:[],
       employees:[],
       employee:'ФИО сотрудника',
       date:null,
       dish:'Выберите блюдо',
       dishes:[],
       allDishes:[],
       edit: false,
    }
  },
  mounted() {
      axios.get('http://localhost:4201/api/ChequeList').then((response)=>{
          this.cheques=response.data

      })
  },
  methods: {
     deleteItem(id){
     axios.delete(`http://localhost:4201/api/Cheque/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.cheques.find(item => item.id == id);
      this.client=el.client.fullName
      this.employee = el.employee.fullName
      this.dishes=el.dishes
      this.date=el.date
      this.edit=id
      this.showModal()
    },
    showModal() {
      this.isOpen = !this.isOpen
      window.document.body.classList.add("modal-active")

       axios.get('http://localhost:4201/api/ClientList').then((response)=>{
          this.clients=response.data
      })
       axios.get('http://localhost:4201/api/EmployeeList').then((response)=>{
          this.employees=response.data
      })
       axios.get('http://localhost:4201/api/DishList').then((response)=>{
          this.allDishes=response.data
      })
    },
    closeModal() {
        this.isOpen = !this.isOpen
        window.document.body.classList.remove("modal-active")
    },
    onAddQuote() {
                this.dishes.push(this.allDishes.find(item => item.name == this.dish))
                this.dish = ''
    },
    onSubmit() {
      console.log('esfsefsefsefsefsefes')
       let client = this.clients.find(item => item.fullName == this.client);
        let empl = this.employees.find(item => item.fullName == this.employee);
        
       
      if (this.edit) {
       axios.post(`http://localhost:4201/api/Cheque/`, {"id":this.edit, "client": client, 'employee': empl, 'dishes':this.dishes, 'date':this.date}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Cheque', {"client": client, 'employee': empl, 'dishes':this.dishes, 'date':this.date}).then((response)=>{
           window.location.reload(false); 
           this.closeModal()
      })
      }
    }
  }

}
</script>

<style lang="less">
.modal{
.listI{
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;

  li{
    padding: 1rem 2rem;
    margin-right: 2rem;
    background: rgba(0, 0, 0, 0.068);
    border-radius: 50px;
    display: flex;
    width: auto;
  
    justify-content: center;

  }
}
}
</style>
