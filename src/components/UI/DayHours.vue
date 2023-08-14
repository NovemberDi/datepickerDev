<template>
    <div class="hours-wrap line">
        <div class="day">
            {{ day.name }}
        </div>
        <div class="score">
            <button @click="scoreDown" class="down score-btn"><p>&minus;</p></button>
            <div class="score-value">{{ day.value }}</div>
            <button @click="scoreUp" class="score-up score-btn"><p>&plus;</p></button>
        </div>
        
        
    </div>
</template>

<script>
    export default {
        name:'day-hours',
        props:{
            day:{type: Object}
        },
        methods:{
            scoreUp(){
                this.$emit('dayUp', this.day)
            },
            scoreDown(){
                this.$emit('dayDown', this.day)
            }
        }
    }
</script>

<style  scoped>
.hours-wrap{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 8px;
    margin-top: 2px;
    font-size: 16px;
}
.line{
    width: 268px;
    border-bottom: 1px  #e2f5f3 solid;
    border-radius: 5px;
    
}
.day{
    margin-left: 10px;
}
.score{
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 80px;
    height: 24px;
    margin:   16px 10px 16px 16px;
    padding: 0;   
}

.score-btn{
    color:rgb(201, 25, 25);
    font-size: 20px;
    background: rgba(255, 255, 255, 0.3);
    border: none;
    height: 24px;
    width: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    line-height: 0;
    border-radius: 50%;
    cursor: pointer;
}
</style>