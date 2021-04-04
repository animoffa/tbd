<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="post" :list="posts" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)" />
       <transition name="modal">
      <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
            <form class="modal" @submit.prevent="onSubmit" >
                <div class="modal-close" @click="closeModal"> ╳</div>
               
                    <h3 class="modal-title">Добавить должность</h3>
               
                    <div class="modal-content">
                        
                        <input class="modal_input" placeholder="Должность" v-model.trim="name"/>
                        <input class="modal_input" placeholder="Оклад" v-model.trim="salary"/>
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
       posts:[],
       name:'',
       salary:'',
      edit: false,
    }
  },
  mounted() {
     
      axios.get('http://localhost:4201/api/PostList').then((response)=>{
          this.posts=response.data
         

      })
  },
  methods: {
     deleteItem(id){
      axios.delete(`http://localhost:4201/api/Post/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.posts.find(item => item.id == id);
      this.name=el.name
      this.salary=el.salary
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
       axios.post(`http://localhost:4201/api/Post/`, {"id":this.edit, "name": this.name, "salary": this.salary}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Post', {"name": this.name, "salary": this.salary}).then((response)=>{
           window.location.reload(false); 
           this.closeModal()
      })
      }
    }
  }
}
</script>