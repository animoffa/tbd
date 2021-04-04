<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="dishes" :list="dishes" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)" />
       <transition name="modal">
        <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
            <form class="modal" @submit.prevent="onSubmit" >
                <div class="modal-close" @click="closeModal"> ╳</div>
               
                    <h3 class="modal-title">Добавить блюдо</h3>
                
                    <div class="modal-content">
                        <input class="modal_input" placeholder="Название блюда" v-model.trim="name"/>
                        <select class="modal_input"  v-model.trim="category" >
                          <option value="Категория" selected>Категория</option>
                          <option v-for="f of categories" :key="f.name">{{f.name}}</option>
                        </select>
                        <input class="modal_input" type="number" placeholder="Цена" v-model.trim="price"/>
                         <ul class="listI" >
                            <li v-for="(item,i) of ingredients" v-bind:key="i" v-bind:quote="item">{{item.name}}</li>
                        </ul>
                        <form @submit.prevent="onAddQuote">
                            <div class="textarea-container">
                    <select  v-model.trim="ingredient">
                      <option value="Выберите ингредиент" selected>Выберите ингредиент</option>
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
       dishes:[],
       category:'Категория',
       categories:[],
       date:null,
       price:null,
       ingredient:'Выберите ингредиент',
       ingredients:[],
       allIngredients:[],
      edit: false,
    }
  },
  mounted() {

      axios.get('http://localhost:4201/api/DishList').then((response)=>{
          this.dishes=response.data
      })
  },
  methods: {
    deleteItem(id){
      axios.delete(`http://localhost:4201/api/Dish/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.dishes.find(item => item.id == id);
      this.category=el.category
      this.name=el.name
      this.price=el.price
      this.ingredients=el.ingredients
      this.edit=id
      this.showModal()
    },
    showModal() {
      this.isOpen = !this.isOpen
      window.document.body.classList.add("modal-active")

       axios.get('http://localhost:4201/api/CategoryList').then((response)=>{
          this.categories=response.data
      })

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
      let el = this.categories.find(item => item.name == this.category);
      if (this.edit) {
       axios.post(`http://localhost:4201/api/Dish/`, {"id":this.edit, "name": this.name, "category": el, "price": this.price, "ingredients": this.ingredients}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Dish', {"name": this.name, "category": el, "price": this.price, "ingredients": this.ingredients}).then((response)=>{
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
