<template>
    <div class="nav">
        <ul>
            <li v-for="t of tables" :key="t.name">
                 <nuxt-link class="a" :to="t.link">{{t.name}}</nuxt-link>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    name:'Nav',
    data(){
        return{
            tables:[
            {
                name:'Категории',
                link:'/'
            },
            {
                name:'Чеки',
                link:'/cheque'
            },
             {
                name:'Клиенты',
                link:'/clients'
            },
             {
                name:'Блюда',
                link:'/dishes'
            },
             {
                name:'Сотрудники',
                link:'/employee'
            },
             {
                name:'Продукты',
                link:'/ingredients'
            },
             {
                name:'Склады',
                link:'/stocks'
            },
             {
                name:'Должности',
                link:'/post'
            }
            ]
        }
    }
}
</script>
<style lang="less">
.nav{
    border-radius: 10px;
    color:black;
    padding: 3rem 2rem;
    width:25rem;

    li{
        margin-bottom: 1rem;
        padding: 0.5rem 2rem 0.5rem 2rem;
        border-radius: 20px;

    }
    li:hover{
        background: rgba(231, 231, 231, 0.904);
    }
    li .a{
        color: black !important;
    }
    a.nuxt-link-exact-active {
        font-weight: bold;
    }
}
</style>