<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="stocks" :list="stocks" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)"/>
       <transition name="modal">
      <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
           <form class="modal" @submit.prevent="onSubmit" >
                <div class="modal-close" @click="closeModal"> ╳</div>
               
                    <h3 class="modal-title">Добавить склад</h3>
                
                    <div class="modal-content">
                        
                        <input class="modal_input" placeholder="Название склада" v-model.trim="name"/>
                        <input class="modal_input" placeholder="адрес" v-model.trim="address"/>
                        <ul class="listI" >
                            <li v-for="(item,i) of ingredients" v-bind:key="i" v-bind:quote="item">{{item.name}}</li>
                        </ul>
                        <form @submit.prevent="onAddQuote">
                            <div class="textarea-container">
                    <select placeholder="Добавить ингредиент" v-model.trim="ingredient">
                       <option value="Добавить ингредиент" selected>Добавить ингредиент</option>
                        <option  v-for="f of allIngredients" :key="f.name">{{f.name}}</option>
                    </select>
                                <button class="modal-button" type="submit">+</button>
                            </div>
                        </form>
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
       name:'',
       stocks:[],
       address:'',
       ingredient:'Добавить ингредиент',
       ingredients:[],
       allIngredients:[],
       edit: false,
    }
  },
  mounted() {
     
      axios.get('http://localhost:4201/api/StockList').then((response)=>{
          this.stocks=response.data
      })
  },
  methods: {
      deleteItem(id){
      axios.delete(`http://localhost:4201/api/Stock/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.stocks.find(item => item.id == id);
      this.name=el.name
      this.address =el.address
      this.ingredients=el.ingredients
      this.edit=id
      this.showModal()
    },
    showModal() {
      this.isOpen = !this.isOpen
      window.document.body.classList.add("modal-active")

       axios.get('http://localhost:4201/api/IngredientList').then((response)=>{
          this.allIngredients=response.data
      })
    },
    closeModal() {
        this.isOpen = !this.isOpen
        window.document.body.classList.remove("modal-active")
    },
   onAddQuote() {
                  this.ingredients.push(this.allIngredients.find(item => item.name == this.ingredient))
                  this.ingredient = ''
    },
     onSubmit() {
      if (this.edit) {
       axios.post(`http://localhost:4201/api/Stock/`, {"id":this.edit, "name": this.name, "address": this.address, "ingredients": this.ingredients}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Stock', {"name": this.name, "address": this.address, "ingredients": this.ingredients}).then((response)=>{
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
