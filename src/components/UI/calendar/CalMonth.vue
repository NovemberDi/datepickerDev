<template>
    <div class="wrap__dayweek">
    
    <div class="month__wrap">   
        
        <div class="month"  >
            
            <cal-day v-for:v-for="day in monthList"
            class="list"
            @click="onClick(day)"
            :periods="periods"
            :day="day"
            :key="day"
            :style="monthList.indexOf(day)==0?(`grid-column-start:${day.getDay()==0?7:day.getDay()}`):'' "
            
            :class="{current: setCurrent(day),
                    other: setOther(day),
                    week: setWeek(day),
                    active: isActive(day)
                    }" 
            ></cal-day>
     
        </div>
    
</div> 
</div>
</template>

<!-- :style="monthList.indexOf(day)==0?(`grid-column-start:${day.getDay()+1}`):''" -->
<script>
    export default {
        name:'cal-month',
        data(){
            return{

            }
        },
        props:{
            monthList: {type: Array },
            nowDate: {type: Object},
            curentPoint: {type: Object},
            periods:{type: Array}
            
        },
        methods:{
            setCurrent(day){
               return (day.getFullYear()+'-'+day.getMonth()+'-'+day.getDate())==(this.nowDate.getFullYear()+'-'+this.nowDate.getMonth()+'-'+this.nowDate.getDate());       
            },
            setOther(day){
                return (day.getMonth()!==this.curentPoint.getMonth())
            },
            setWeek(day){
                return (day.getDay()==6||day.getDay()==0)
            },
            onClick(day){
                this.$emit('returnDay', day);                
            },
            isActive(day){
                let year = day.getFullYear();
                let month = (day.getMonth()+2)/10>1?day.getMonth()+1:'0'+(day.getMonth()+1);
                let date = (day.getDate()+1)/10>1?day.getDate():'0'+day.getDate();
                let point = year+'-'+month+'-'+date;
                let answ;
                this.periods.forEach(item => item.start == point||item.end == point?answ = true:'');
                return answ;
                
                // return point == this.periods[1].start
            }
            
        }
    }
</script>

<style scoped>
/* .month__wrap{
    height:216px;
    overflow: hidden;
} */
.month{   
display: grid;
grid-template: 1fr 1fr 1fr 1fr 1fr 1fr/ 1fr 1fr 1fr 1fr 1fr 1fr 1fr ;


}


/* .wrap__dayweek{
    
} */
.current{
    background: rgb(225, 175, 73);
    color:rgb(41, 40, 38);
}
.other{
opacity: 0.5;
}
.week{
    color:rgb(189, 36, 36);
}
.active{
    background: rgb(185, 195, 203);
    color:rgb(41, 40, 38);  
}


</style>