<template>
    <div class="input-wrap line"  >
        
            <div class="num" >&numero;{{index+1}}</div>
            <input
            readonly
                :class="{
                    wrong: isWrong
                }"
                @focusout="focusout"
                @focus="focusOfStart"
                :value="start" 
                placeholder="от.."  
                @input="updateStart" 
                class="input" >
            <div class="num">&rang;</div> 
            <input 
            readonly
                :class="{
                    wrong: isWrong
                }"
                @focusout="focusout"
                @focus="focusOfEnd"
                :value="end"
                placeholder="до.."  
                @input="updateEnd" 
                class="input" >
            <button @click="$emit('remove', period)" class="remove">&#10008;</button>
        
    </div>
</template>

<script>
    export default {
        name: 'date-input',
        data(){
            return{
                isWrong: false
            }
        },
        props:{
            index:{type: Number},
            period :{type: Object},
            start : [String, Number],
            end : [String, Number],
        },
        methods:{
            updateStart(event){
                this.$emit('update:start', event.target.value);

            },
            updateEnd(event){
                this.$emit('update:end', event.target.value)
            },
            focusOfStart(event){
                let ev = this.getCoords(event.target);
                ev.elem = 'start';
                ev.period = this.period;
                ev.target = event.target;
                this.$emit('sendPosition', ev);
               
            },
            focusOfEnd(event){
                let ev = this.getCoords(event.target);
                ev.elem = 'end';
                ev.period = this.period;
                ev.target = event.target;
                this.$emit('sendPosition', ev)
            },
            focusout(){
                this.$emit('unfocus')
            },
            getCoords(elem) {
                let box = elem.getBoundingClientRect();
                return {
                    top: box.top + window.pageYOffset,
                    left: box.left + window.pageXOffset,
                    height: elem.height
                }
            },
            
        },
        watch:{
            period:{
                handler(){
                let start = new Date(this.period.start);
                let end = new Date(this.period.end);
                start>end? this.isWrong = true:this.isWrong = false;
                this.$emit('wrong',this.isWrong)

                },
                deep: true
            },
            
            }
        

    }
</script>

<style scoped>
.wrong{
    background: #f59090;
    color:red;

}
.input-wrap{
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 8px;
    font-size: 16px;

}
.input{
    width: 80px;
    height: 24px;
    border: 1px  #e2f5f3 solid;
    margin:  16px 8px 16px 8px;
    padding: 0;   
}
.line{
    width: 268px;
    border-bottom: 1px  #e2f5f3 solid;
    border-radius: 5px;
}
input{
    text-align: center;
}
.remove{
    color:rgb(201, 25, 25);
    font-size: 16px;
    background: rgba(255, 255, 255, 0.3);
    border: none;
    height: 24px;
    width: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    line-height: 0;
    border-radius: 50%;
}
</style>