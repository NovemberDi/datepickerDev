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
    <div  class="main" >
      <div class="periods left-block" >
          <div  class="title"><h2> Учебные периоды</h2></div>
          <div class="step_two">
            <button-add @click="addPeriod" :yellow="true">Добавить</button-add>
          </div>
          <transition-group name="list-complete" tag="span">
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

      <div class="periods right-block">
          <div class="title"><h2> Часов за день </h2></div>
          <div class="step_two">
            <p>Укажите сколько часов в день</p>
          </div>
          <day-hours v-for="day in days"
          :day="day"
          @dayUp="dayUp"
          @dayDown="dayDown"  
          ></day-hours>
      </div>
      <div @click="unFocus"  class="btn-wrap">
      <button-add  @click="getResult"   :green="true">Рассчитать</button-add>
      </div>
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
            (window.innerHeight + window.scrollY - event.top) > 330? this.calPosition.top = event.top-30:this.calPosition.top = event.top-370;
            (document.documentElement.clientWidth) > 550? this.calPosition.left = event.left:this.calPosition.left = (document.documentElement.clientWidth - 256)/2;
            this.calPosition.display='';
            this.currentInput.elem = event.elem;
            this.currentInput.period = event.period;
            this.currentInput.target = event.target;
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
                    //обработка ошибок----------------------------------------------------
                    let response = null;
                      try{
                      response = await axios.get('https://isdayoff.ru/'+year+'-'+month+'-'+date);
                    } 
                      catch {
                        alert('Сервер не отвечает, попробуйте позже..');
                        break
                    
                      }
                      if (response) {
                        this.days.forEach(day => {
                          if (day.value>0&&day.num==item.getDay()&&response.data==0)
                          {
                            for (let i = 0; i < day.value; i++)
                            {
                              // result.push(year+'-'+month+'-'+date)
                              result.push(date+'.'+month+'.'+year)

                            }
                          }
                        })
                      }
              };
              if (result.length>0){
                for (let i = 0; i < result.length; i++){
                  this.result.push({date:result[i],num:i+1})
                }
              }else{
                this.result.push({date:'Список пуст',num:1})
              }
              })();
          }
            
          
        } 
    }
    }
</script>



<style >
  html body{
      margin: 0;
      padding: 0;   
      background-color: #2f2e2e;
    }
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
 
  min-height: 100vh;
  margin: 0;
  padding: 0;

  background: linear-gradient(45deg, #13547a, #80d0c7);
}
h1 h2 h3{
  font-size: 16px;
}
.datepicker{
  position:absolute;  
}
.header__wrap{
  background: rgb(227, 239, 240);
  box-shadow: 0px 0px 8px 5px rgba(39, 44, 47, 0.2);
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.header{
display: flex;
width: 100%;
}
.logo{
  align-items: center;
  justify-content: flex-start;
  margin-left: 5%;
  font-size: 28px;
}
.main__wrap{
  position: relative;
}
.main-block{
  display: flex;
  align-items: center;
  justify-content: center;
}
.main{
  margin-top: 100px;
  margin-bottom: 100px;
  width: 780px;
  background: rgba(227, 239, 240, 0.4);
  display: grid;
  grid-template: 1fr 60px/ 1fr 1fr ; 

  box-shadow: 0px 0px 8px 5px rgba(39, 44, 47, 0.2);

}
.btn-wrap{
  display: flex;
  justify-content: center;
  grid-area: 2/ 1 / 3 / 3;
  align-self: center;
  justify-self: center;
}

.periods{
  width: 100%;
  /* min-height: 484px; */
  justify-self: center;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.left-block{
  grid-area: 1 / 1 / 1 / 1;
}
.right-block{
  grid-area: 1 / 2 / 1 / 2;
}
.periods>p{
  margin-top: 6px;
  margin-bottom: 4px;
}
.step_two{
  height: 32px;;
  display: flex;
  align-items: center;
  
}
.title{
  margin: 14px;
  font-size: 20px;
  line-height: 20px;
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
@media screen and (max-width: 610px) {
  .main{  
  grid-template: auto 1fr 60px/ 1fr ; 
  margin: 50px 0 50px 0 ;
  width: 90%;
}
.btn-wrap{
  grid-area: 3/ 1 / 3 / 1;
}

.left-block{
  grid-area: 1 / 1 / 1 / 1;
  height: auto;
}
.right-block{
  grid-area: 2 / 1 / 2 / 2;
}
}

</style>
