<template>
  <div class="container">
    <div class="hello">
      <Nav class="nav"/>
      <Table table="departament" :list="departaments" @addItem="showModal" @deleteItem="deleteItem($event)" @editItem="editItem($event)" />
       <transition name="modal">
      <div v-if="isOpen" class="modal-shadow" @click.self="closeModal">
            <form class="modal" @submit.prevent="onSubmit" >
                <div class="modal-close" @click="closeModal"> ╳</div>
                    <h3 class="modal-title">{{edit ? 'Изменить отдел' : 'Добавить отдел'}}</h3>
                    <div class="modal-content">
                        <input class="modal_input" placeholder="Отдел" v-model.trim="name"/>
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
       departaments:[],
       name:'',
      edit: false,
    }
  },
  mounted() {
     
      axios.get('http://localhost:4201/api/DepartamentList').then((response)=>{
          this.departaments=response.data
      })
  },
  methods: {
     deleteItem(id){
      axios.delete(`http://localhost:4201/api/Departament/${id}`).then(()=>{
           window.location.reload(false); 
      })
    },
     editItem(id){
      let el = this.posts.find(item => item.id == id);
      this.name=el.name
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
       axios.post(`http://localhost:4201/api/Departament/`, {"id":this.edit, "name": this.name}).then((response)=>{
           window.location.reload(false);
           this.edit=false; 
           this.closeModal()
      })
      } else {
        axios.post('http://localhost:4201/api/Departament', {"name": this.name}).then((response)=>{
           window.location.reload(false); 
           this.closeModal()
      })
      }
    }
  }
}
</script>