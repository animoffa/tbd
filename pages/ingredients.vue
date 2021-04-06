<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="ingredients" :list="ingredients" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)" />
       <transition name="modal">
      <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
           <form class="modal" @submit.prevent="onSubmit" >
                <div class="modal-close" @click="closeModal"> ╳</div>
              
                    <h3 class="modal-title">{{edit ? 'Изменить ингредиент' : 'Добавить ингредиент'}}</h3>
                
                    <div class="modal-content">
                        
                        <input class="modal_input" placeholder="Название ингредиента" v-model.trim="name"/>
                        <textarea class="modal_input" placeholder="Описание" v-model.trim="description"/>
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
       description:'',
       ingredients:[],
      edit: false,
    }
  },
  mounted() {
  
      axios.get('http://localhost:4201/api/IngredientList').then((response)=>{
          this.ingredients=response.data
      })
  },
  methods: {
     deleteItem(id){
      axios.delete(`http://localhost:4201/api/Ingredient/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.ingredients.find(item => item.id == id);
      this.name=el.name
      this.description=el.description
      this.edit=id
      this.showModal()
    },
    showModal() {
      this.isOpen = !this.isOpen
      window.document.body.classList.add("modal-active")
    },
    closeModal() {
        this.isOpen = !this.isOpen
        window.document.body.classList.remove("modal-active")
    },
    onSubmit() {
      if (this.edit) {
       axios.post(`http://localhost:4201/api/Ingredient/`, {"id":this.edit, "name": this.name, "description": this.description}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Ingredient', { "name": this.name, "description": this.description}).then((response)=>{
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
