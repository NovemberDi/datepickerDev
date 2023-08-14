<template>
  <modal-result v-show="showResult" @hideModal="showResult=!showResult" :result="result"></modal-result>
  <div class="header__wrap">
    <div  class="header">
        <div class="logo" ><h3>Даты для КТП онлайн</h3></div>
    </div>
  </div>
  <div  class="main__wrap" > 
    <Calendar  @wheel.prevent @touchmove.prevent @scroll.prevent  @mousedown.prevent
      @returnDay="setDay"
      :periods="periods"
      :style="`top:${calPosition.top+'px'}; left: ${calPosition.left+'px'}; display: ${calPosition.display}`" class="datepicker"></Calendar>
    <div  class="main-block">
    <div  class="main">
      <div class="periods">
          <div  class="title"><h2> Учебные периоды</h2></div>
          <button-add @click="addPeriod" :yellow="true">Добавить</button-add>
          <transition-group name="list-complete" tag="p">
            <date-input 
            class="item-period list-complete-item" 
            v-for="period in periods"
            :key="period.id"
            :index="periods.indexOf(period)"
            :period="period"
            v-model:start="period.start"
            v-model:end="period.end"
            @remove="removePeriod"
            @sendPosition="setPosition"
            @unfocus="unFocus"
            @wrong="isWronger"
            
            ></date-input>
        </transition-group>
      </div>

      <div class="periods">
          <div class="title"><h2> Часов за день </h2></div>
          <p>Укажите сколько часов в день</p>
          <day-hours v-for="day in days"
          :day="day"
          @dayUp="dayUp"
          @dayDown="dayDown"  
          ></day-hours>
      </div>
    </div>
    <div @click="unFocus"  class="btn-wrap">
      <button-add  @click="getResult"   :green="true">Расчитать</button-add>
  </div>
  </div>
    
  </div>
  
</template>



<script>
import Calendar from './components/Calendar.vue';
import axios from 'axios';

    export default {
        components:{
          Calendar
        },
        data(){
          return{
            periods:[
              {id:1, start:'', end:''},
              {id:2, start:'', end:''},
              {id:3, start:'', end:''},
              {id:4, start:'', end:''},
              {id:5, start:'', end:''},
            ],
            days:[
              {name:'Понедельник', value:0,num:1},
              {name:'Вторник', value:0,num:2},
              {name:'Среда',value:0,num:3},
              {name:'Четверг',value:0,num:4},
              {name:'Пятница',value:0,num:5},
            ],
            calPosition:{
              top:0,
              left:0,
              display:'none',
            },
            currentInput: {},
            result: [],
            showResult: false,
            isWrong: false,
            
          }
        },
        methods:{
          isWronger(event){
            this.isWrong = event;
          },
          removePeriod(period){
            this.periods = this.periods.filter(item => item.id !== period.id )
          },
          addPeriod(){
            let newPeriod = {id:  Date.now(), start:'', end:''};
            this.periods.push(newPeriod)
          },
          dayDown(event){
            this.days.forEach(item =>{item==event&&item.value>0?item.value-=1:''})
          },
          dayUp(event){
            this.days.forEach(item =>{item==event&&item.value<10?item.value+=1:''})
          },
          setPosition(event){
            (window.innerHeight - event.top) > 330? this.calPosition.top = event.top-30:this.calPosition.top = event.top-370;
            this.calPosition.left = event.left;
            this.calPosition.display='';

            this.currentInput.elem = event.elem;
            this.currentInput.period = event.period;
            this.currentInput.target = event.target;

            // console.log(this.currentInput)

          },
          unFocus(){
            this.calPosition.display='none'
          },
          setDay(event){
            this.periods.forEach(item =>{
              if (item == this.currentInput.period){
              let year = event.getFullYear();
              let month = (event.getMonth()+2)/10>1?event.getMonth()+1:'0'+(event.getMonth()+1);
              let date = (event.getDate()+1)/10>1?event.getDate():'0'+event.getDate();
              item[this.currentInput.elem] = year+'-'+month+'-'+date}
            });
            this.calPosition.display = 'none';

            this.currentInput.target.blur();
            
            
          },
           getResult(){
            if (!this.isWrong && this.days.find(item => item.value>0) && this.periods.find(item => item.start && item.end) ) {
            this.result = [];
            this.showResult=true;
            let allDate = [];
            let allDays = [];
            let result = [];
            this.days.forEach(item =>item.value>0?allDays.push(item.num):'');
            this.periods.forEach(item => {
              
              let start = new Date(item.start);
              let end = new Date(item.end);
              while (start<=end) {
                if (allDays.includes(start.getDay())){allDate.push(new Date(start))};
                 start.setDate(start.getDate()+1);
            }});
             (async () => {
              for (const item of allDate) {
                  let year = item.getFullYear();
                  let month = (item.getMonth()+2)/10>1?item.getMonth()+1:'0'+(item.getMonth()+1);
                  let date = (item.getDate()+1)/10>1?item.getDate():'0'+item.getDate();
                    //Сделать обравотку ошибок!!!----------------------------------------------------
                      let response = await axios.get('https://isdayoff.ru/'+year+'-'+month+'-'+date);
                      this.days.forEach(day => {
                        if (day.value>0&&day.num==item.getDay()&&response.data==0)
                        {
                          for (let i = 0; i < day.value; i++)
                          {
                            result.push(year+'-'+month+'-'+date)
                          }
                        }
                      })

              };
              if (result.length>0){
                for (let i = 0; i < result.length; i++){
                  this.result.push({date:result[i],num:i+1})
                }
              }else{
                this.result.push({date:'Список пуст',num:1})
              }
              
              // console.log(this.result);
              })();
            
          }
            
          
        } 
    }
    }
</script>



<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 100vh;
  /* background-image: url('@/assets/wall.jpg'); */
  background: linear-gradient(45deg, #13547a, #80d0c7);
  /* background: linear-gradient(45deg, #84cbf4, #b4f8f0); */
}
h1 h2 h3{
  font-size: 16px;
}
.datepicker{
  position:absolute;
  /* scale: 0.8; */
  /* top: 60px;
  left: 30px; */
  /* display: none; */
  
}
.header__wrap{
  /* background: rgb(236, 252, 253); */
  background: rgb(227, 239, 240);
  box-shadow: 0px 0px 8px 5px rgba(39, 44, 47, 0.2);
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.header{
width: 1100px;
}
.logo{
  font-size: 28px;
}
.main__wrap{
  display: flex;
  justify-content: center;
  position: relative;
}
.main-block{
  width: 950px;
  background: rgba(227, 239, 240, 0.4);
  box-shadow: 0px 0px 8px 5px rgba(39, 44, 47, 0.2);
  min-height: 540px;
  margin-top: 60px;
  margin-bottom: 70px;
}
.main{
  min-height: 470px;
  
  display: flex;
  /* flex-direction: column; */
}
.btn-wrap{
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 70px;
}

.periods{
  /* border: 1px #fff solid; */
  width: 50%;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  align-items: center;
}
.periods>p{
  /* color: rgb(255, 255, 255); */
  margin-top: 6px;
  margin-bottom: 4px;
}

.title{
  margin: 14px;
  font-size: 20px;
}
.item-period{
  
}
/* -------Анимация списка-------- */
.list-complete-item {
  transition: all 0.5s ease;
  display: inline-block;
  margin-right: 10px;
}

.list-complete-enter-from,
.list-complete-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}

.list-complete-leave-active {
  position: absolute;
}

</style>
