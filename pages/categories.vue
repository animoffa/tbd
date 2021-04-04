<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="categories" :list="categories" @delete="deleteItem($event)" @edit="editItem($event)" @addItem="showModal"/>
       <transition name="modal">
        <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
            <div class="modal">
                <div class="modal-close" @click="closeModal"> ╳</div>
                <slot name="title">
                    <h3 class="modal-title">Добавить категорию</h3>
                </slot>
                <slot name="body">
                    <div class="modal-content">
                        <input class="modal_input" placeholder="Название категории" v-model.trim="category"/>
                    </div>
                </slot>

            </div>
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
       category:'',
       categories:[]
    }
  },
  mounted() {
     
      axios.get('http://localhost:4201/api/CategoryList').then((response)=>{
          this.categories=response.data
         
      })
  },
  methods: {
     deleteItem(id){
     console.log('d')
    },
     editItem(id){
     console.log('e')
    },
    showModal() {
      this.isOpen = !this.isOpen
      window.document.body.classList.add("modal-active")
    },
    closeModal() {
        this.isOpen = !this.isOpen
        window.document.body.classList.remove("modal-active")
    },
  }
}
</script>

<style lang="less">

</style>
