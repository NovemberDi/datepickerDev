<template>
    <div>
        <div class="sy-warp">
        <div class="btns">
            <button @click="yearDown">&lt;</button>
            <button @click="$emit('unshow')">
            <p>{{(current.getFullYear()-7)}} &ndash; {{(current.getFullYear()+10)}}</p></button>
            <button @click="yearUp"> &gt; </button>
         </div>
         <div class="year-list" @wheel="onWheel">
            
            <div class="years" v-for:v-for="year in listOfYears" :key="year" @click="onYear(year)"
            :class="{current: setCurrent(year)}">
               {{year.getFullYear()}}
         </div>
        
        </div>
    </div>
    </div>
</template>

<script>
    export default {
        name:'select-year',
        data(){
            return{
                listOfYears: [],
                current: new Date(this.curentPoint),
                
            }
        },
        props:{
            curentPoint: {type: Object},     
        }, 
        methods:{
            onYear(year){
                this.$emit('changeYear', year.getFullYear());
                this.unShow();
            },
            unShow(){
            this.$emit('unshow')
            },
            onWheel(event){
                event.wheelDelta>0?this.yearUp():this.yearDown();
            },
            createYears(current){
                let start = new Date(current);
                start.setFullYear(start.getFullYear()-7);
                for (let i=0; i<18; i++){     
                this.listOfYears.push(new Date(start));
                start.setFullYear(start.getFullYear()+1);
                }    
            },
            yearUp(){
                this.current.setFullYear(this.current.getFullYear()+18);
                this.listOfYears = [];
                this.createYears(this.current);

            },
            yearDown(){
                this.current.setFullYear(this.current.getFullYear()-18);
                this.listOfYears = [];
                this.createYears(this.current);
            },
            setCurrent(year){
            return ((year.getFullYear())==(this.curentPoint.getFullYear()));       
            },

        },
        mounted(){
            this.createYears(this.current);
        } 

    }
</script>

<style scoped>
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
    z-index: 999;
}
.year-list{
    margin-top: 5px;
    display: grid;
    grid-template: 1fr 1fr 1fr 1fr/ 1fr 1fr 1fr;
    align-items: center;
}
.years{
    height: 35px;
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
.years:hover{
    background: rgba(244, 187, 144, 0.2); 
}
.years:active{
    background: rgba(127, 94, 68, 0.2); 
}
.current{
    background: rgb(225, 175, 73);
    color:rgb(41, 40, 38);
}

.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}

</style>