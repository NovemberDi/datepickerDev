<template>
<div>
    <select-year :curentPoint="curentPoint" v-show="show"
    @unshow="show = !show"
    @changeYear="changeYear"
    ></select-year>
    <div class="sm-warp" v-show="!show"> 
        <div class="btns" @wheel="onWheel">
            <button @click="yearDown">&lt;</button>
            <button @click="show=!show"><p>{{(curentPoint.getFullYear()) }}</p></button>
            <button @click="yearUp"> &gt; </button>
         </div>
         <div class="month-list">
         <div class="month" v-for:v-for="month in listOfMonth" @click="onMonth(month)"  
         :class="{current: setCurrent(month)}" >
            <p>{{month}}</p>
         </div>
        </div>
    </div>
</div>
</template>

<script>


    
    export default {
     name:'select-month',
     data(){
            return{
            listOfMonth:['Январь','Февраль','Март','Апрель','Май','Июнь','Июль','Август','Сентябрь','Октябрь','Ноябрь','Декабрь'],
            show:false, 
            year: this.curentPoint.getFullYear(),   
            }
        },
    props:{
        curentPoint: {type: Object},
        
    },  
    methods:{
        onWheel(event){
                event.wheelDelta>0?this.yearUp():this.yearDown();
            },
        unShow(){
            this.$emit('unshow')
        },
        changeYear(year){ 
            this.$emit('changeYear', year)
        },
        yearUp(){
            this.year+=1;
            this.changeYear(this.year);
            },
        yearDown(){
            this.year-=1;
            this.changeYear(this.year);
        },
        onMonth(month){
            this.$emit('changeMonth', this.listOfMonth.indexOf(month));
            this.unShow();
        },
        setCurrent(month){
            return (this.listOfMonth.indexOf(month))==(this.curentPoint.getMonth());       
        },
    }   
    }
</script>

<style scoped>
.month-list{
    margin-top: 15px;
    display: grid;
    grid-template: 1fr 1fr 1fr 1fr/ 1fr 1fr 1fr;
    align-items: center;
}
.month{
    height: 50px;
    /* background: rgba(244, 187, 144, 0.2); */
    box-shadow: 0px 0px 10px 0px rgba(244, 187, 144, 0.2);
    border-bottom: 1px solid #fff;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 2px;
    cursor:pointer;
}
.month:hover{
    background: rgba(244, 187, 144, 0.2); 
}
.month:active{
    background: rgba(127, 94, 68, 0.2); 
}
.current{
    background: rgb(225, 175, 73);
    color:rgb(41, 40, 38);
}
.btns{
    display: flex;
    justify-content: space-between;
    background: rgba(0, 0, 0, 0.2);
}
button{
    display: flex;
    /* background: rgba(0, 0, 0, 0.2); */
    background: none;
    border: none;
    height: 40px;
    font-size: 25px;
    font-weight: 200;
    color: #fff;
    align-items: center;
    cursor:pointer;
    /* z-index: 999; */
}
</style>