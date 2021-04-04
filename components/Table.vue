<template>
<div class="table">
    <ul class="table-item head">
            <li v-for="i of CurTable" :key="i" class="head-item">
                {{i}}
            </li>
             <li class="buttons head__buttons">
                <p class="head-item head__add" @click="addItem()">
                     +
                </p>
            </li>
    </ul>
     <TableItem v-for="(card,i) in list" v-bind:key="i" 
                    :post="card"  @delete="deleteCard($event)" @edit="editCard($event)">
    </TableItem>
</div>
</template>

<script>
import TableItem from '@/components/TableItem.vue'
export default {
    components:{
        TableItem
    },
    props:['table', 'list'],
    data(){
        return{
             tables:{
                categories:['id','name'],
                employee: ['id','fullName', 'birthday','phone','post'],
                post: ['id','name','salary'],
                cheque: ['id','client', 'employ','dishes','date'],
                clients: ['id', 'name', 'birthday'],
                dishes: ['id', 'category', 'name', 'price', 'ingredients'],
                stocks: ['id','name', 'address', 'ingredients'],
                ingredients: ['id','name', 'description']
            },
            Posts:[
                {name:'hot',id:1},
                {name:'cold',id:2}
            ],
           
        }
    },
    computed:{
        CurTable(){
             return this.tables[this.table]
        },
        CurView(){
              return this.views[this.table]
        },
    },
    methods: {
         deleteCard(id){
            console.log('e',id)
            this.$emit('deleteItem', id);
        },
        editCard(id){
            this.$emit('editItem', id);
        },
        addItem() {
            this.$emit('addItem');
        }
    }
}
</script>
<style scoped>
.head{
    background: rgb(255 255 255 / 85%);
    display: flex;
    border-top-left-radius: 30px;
    border-top-right-radius: 30px;

}
.head-item{
    margin: 1rem;
}
.table{
    width:120rem;
    padding-top: 3rem;
}
.head__buttons {
    display: flex;
    justify-content: center !important;
}
.head__add{
    display: flex;
    justify-content: center;
    font-size: 3rem;
}
</style>