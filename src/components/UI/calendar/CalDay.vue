<template>
    <div class="day"  >
        <p>{{day.getDate()}}</p>
    </div>
</template>

<script>
    export default {
        name:'cal-day',
        data(){
            return{
                
            }
        },
        props:{
            day:{type: Date},
            
        },
       
    }
</script>

<style scoped>
.day{
    height: 30px;
    width: 30px;
    /* background: rgb(167, 207, 211); */
    box-shadow: 0px 0px 4px 0px rgba(244, 187, 144, 0.2);
    border-bottom: 1px solid rgb(255, 255, 255,0);
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 2px;
    cursor:pointer;
}
.day:hover{
    background: rgba(244, 187, 144, 0.2); 
}

</style>