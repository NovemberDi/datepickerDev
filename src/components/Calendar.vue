<template>

    <div class="wrap" >
        
        <div class="calendar" onselectstart="return false" >
            <select-month  v-show="show" 
            :curentPoint="curentPoint"
            @unshow="show = !show"
            @changeMonth="changeMonth"
            @changeYear="changeYear"
            
            ></select-month>
            <div class="select__days" v-show="!show">     
                <div class="btns">
                    <button @click="monthDown">&lt;</button>
                    <button @click="show = !show"><p>{{(curentPoint.getFullYear()+'-'+(curentPoint.getMonth()+1)) }}</p></button>
                    <button @click="monthUp"> &gt; </button>
                </div>
                <div  class="dayweek">
                    <div class="dayw"><p>пн</p></div>
                    <div class="dayw"><p>вт</p></div>
                    <div class="dayw"><p>ср</p></div>
                    <div class="dayw"><p>чт</p></div>
                    <div class="dayw"><p>пт</p></div>
                    <div class="dayw"><p>сб</p></div>
                    <div class="dayw"><p>вс</p></div>
                </div>
                <div class="wrapper_month" 
                @wheel="onWheel"
                @touchstart="startSwipe"
                @touchend="endSwipe">
               
                    <div class="wrap__calmonth" id="block" :style="`transform: translateX(${trans+'px'}); transition: all ${transition}s linear`">
                        <cal-month class="calmonth" v-for:v-for="month in listOfMonth" 
                            :monthList="month"
                            :nowDate="nowDate"
                            :curentPoint="curentPoint"
                            :periods="periods"
                            @returnDay="returnDay"
                        ></cal-month>   
                    </div>   
                </div>  
            </div>      
        </div>
</div>
</template>

<script>    
    export default {
        data(){
            return{
                dateList:[],
                monthList:[],
                listOfMonth:[],              
                nowDate: new Date(),
                curentPoint: new Date(),
                trans: 0,
                transition:1,
                transitionValue: 0.3,
                switcher: true,
                isUp: false,
                isDown:false,
                show:false,
                swipe:{ startX:0,
                        targetX:0,  
                        deltaX:0,
                        isStarted:false}
            }
        },
        props:{
            periods:{type: Array}
        },
        methods:{
            returnDay(event){
                this.$emit('returnDay', event);
                // this.curentPoint = event;
            },
            startSwipe(event){ this.swipe.startX = event.changedTouches[0].clientX;},
            endSwipe(event){
                this.swipe.deltaX = this.swipe.startX - event.changedTouches[0].clientX;
                if (Math.abs(this.swipe.deltaX)>40){
                    this.swipe.deltaX>0?this.monthUp():this.monthDown();}
            },
            onWheel(event){
                if (this.listOfMonth.length<6) {
                event.wheelDelta<0?this.monthUp():this.monthDown();
                }
            },
            changeYear(year){
                this.curentPoint.setFullYear(year, 0, 1);
                this.listOfMonth = [];
                this.createListOfMonth(this.curentPoint);
             },
            changeMonth(month){
                this.curentPoint.setMonth(month, 1);
                this.listOfMonth = [];
                this.createListOfMonth(this.curentPoint);

            },
            createListOfMonth(curentP){
                let curentPoint = new Date(curentP)
                curentPoint.setMonth(curentPoint.getMonth()-1, 1);
                this.listOfMonth.push(this.createMonthList(curentPoint));
                curentPoint.setMonth(curentPoint.getMonth()+1, 1);
                this.listOfMonth.push(this.createMonthList(curentPoint));        
            },

            createMonthList(current){  
                    let firstDay = new Date(current);
                    let lastDay = new Date(current);
                    let monthList =[];
                    firstDay.setMonth(firstDay.getMonth()-1, 1);
                    lastDay.setMonth(lastDay.getMonth()+2, 1);
                    while (firstDay<lastDay){
                        monthList.push(new Date(firstDay));
                        firstDay.setDate(firstDay.getDate()+1 );          
                    };
                    return monthList;
                    
            },
            moveRight(curentPoint){
                let current = new Date(curentPoint); 
                this.listOfMonth.push(this.createMonthList(current));     
            },
            moveLeft(curentPoint){
                let current = new Date(curentPoint);
                current.setMonth(this.curentPoint.getMonth()-1,1);  
                this.listOfMonth.unshift(this.createMonthList(current));     
            },
            monthUp(){
                if (this.switcher&&!this.isDown) {
                    this.switcher = false; 
                    this.isUp = true;
                    this.transition = this.transitionValue;
                    const block = document.getElementById('block'); 
                    this.curentPoint.setMonth(this.curentPoint.getMonth()+1,1);
                    this.moveRight(this.curentPoint); 
                    this.trans-=238;  
                    block.addEventListener('transitionend', ()=>{
                        this.transition = 0;
                        this.trans+=238;
                        this.listOfMonth.splice(0,1);
                       this.switcher=true;
                        this.isUp = false;
                        console.log('end') 
                        },  { once: true });
                    console.log('Up');
                }    
            },
           
            monthDown(){
                if (this.switcher&&!this.isUp) {
                    this.switcher = false; 
                    this.isDown = true;
                    this.transition = this.transitionValue ;
                    this.curentPoint.setMonth(this.curentPoint.getMonth()-1,1);
                    
                    this.trans+= 238;
                    
                    const block = document.getElementById('block');
                    block.addEventListener('transitionend', ()=>{
                        this.transition = 0 ; 
                        this.moveLeft(this.curentPoint); 
                        this.listOfMonth.pop();
                        this.trans-=238; 
                        this.isDown = false;
                        this.switcher = true;
                        }, { once: true });
                    console.log('Down');
                }
                    
            }
        },
        mounted(){   
            this.createListOfMonth(this.curentPoint);
   
        },
    }
</script>

<style scoped>
.wrap{
    /* display: flex;
    justify-content: center;  */
}
.calendar{
    /* overflow: hidden; */

    background: rgba(38, 37, 36, 0.7);
    backdrop-filter: blur(6px);
    width: 242px;
    height: 294px; 
    border: 2px solid rgb(163, 163, 163);
    color: rgb(205, 192, 181);
    box-shadow: 4px 4px 8px 0px rgba(150, 188, 218, 0.2);
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
    z-index: 999;
}

.dayweek{
    display: flex;
}
.dayw{
    height: 30px;
    width: 30px;
    /* background: rgb(61, 86, 44); */
    color: rgb(225, 175, 73);
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 2px;
    box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
}
.wrapper_month{
    display: flex;
    overflow: hidden;
    height: 216px;
    position: relative;
    z-index: 999;
    
    
    
}
.wrap__calmonth{ 
    display: flex;
    position: absolute;
    top: -138px;
    right: -2;
    left: -238px;

    /* transition: 1s; */

   
}
/* .calmonth{
   
} */
</style>