<template>
    <div class="modal-wrap" @mousedown="hideModal">
        
        <div class="modal" @mousedown.stop>
            <div class="modal-title">
                <h3>Результат</h3>
                <button @click="copyAll" class="copyAll">Скопировать всё &#10064;</button>
            </div>
            <div v-if="result.length>0" class="modal-item" >
                <div>
                    <div class="part"   v-for="item in result" >
                        <p onselectstart="return false">{{  item.num  }})</p>
                    </div>
                </div>
                <div id="dates">
                    <div  class="part" v-for="item in result" >
                    <p>{{  item.date  }}</p>
                    </div>
                </div>
                <div>
                    <div  class="part" v-for="item in result" >
                    <button @click="copy(item)" class="copy">&#10064;</button>
                    </div>
                </div>  
            </div>
             <my-preloader v-else class="loader"></my-preloader>
        </div>
            <div class="copired" v-show="show">Скопировано!</div>
            
            
    </div>
</template>

<script>
    export default {
        name:'modal-result',
        data(){
            return{
                show:false
            }
        },
        props:{
            result: {type: Array}
        },
        methods:{
            hideModal(){
                this.$emit('hideModal')
            },
            copy(item){
                this.show=true;
                setTimeout(()=>this.show=false, 1000);
                navigator.clipboard.writeText(item.date)
                    .then(() => {
                        // Получилось!
                    })
                    .catch(err => {
                        console.log('Something went wrong', err);
                    });
                
            },
            copyAll(){
                this.show=true;
                setTimeout(()=>this.show=false, 1000); 
                let range = document.createRange();
                range.selectNode(document.getElementById('dates'));
                window.getSelection().removeAllRanges();
                window.getSelection().addRange(range);
                document.execCommand("copy");
                window.getSelection().removeAllRanges();
            }
        }

    }
</script>

<style scoped>
.modal-wrap{
    position: fixed;
    
    height: 100%;
    width: 100%;
    background: rgba(0,0,0,0.5);
    z-index: 1;

}
.modal{
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);

    height: 500px;
    width: 400px;
    background: #fff;
    border: 2px solid rgb(140, 140, 140);
    /* border-top: 3px solid rgb(140, 140, 140); */
    box-shadow: 0px 0px 15px 5px rgba(39, 44, 47, 0.6);

    overflow-y: scroll;
    overflow-x:hidden;
    display: flex;
    flex-direction: column;
    align-items: center;
}
.copired{
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);  
    height: 50px;
    width: 160px;
    color: rgb(237, 237, 237);
    background: rgba(62, 62, 62,0.9);
    border-radius: 20px;
    z-index: 10;
    display: flex;
    align-items: center;
    justify-content: center;
}
.modal-item{
    font-size: 16px;
    margin: 4px;
    width: 250px;
    display: flex;
    justify-content: space-between ;
    z-index: 1;

}
.part{
    display: flex;
    align-items: center;
    height: 28px;
    
}
.copy{
    color:rgb(119, 119, 119);
    font-size: 16px;
    background: rgb(239, 238, 238);
    border: none;
    height: 24px;
    width: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    line-height: 0;
    border-radius: 10px;
    cursor: pointer;
}
.copyAll{
    color:rgb(119, 119, 119);
    font-size: 16px;
    background: rgb(239, 238, 238);
    border: none;
    height: 24px;
    width: fit-content;
    display: flex;
    align-items: center;
    justify-content: center;
    line-height: 0;
    border-radius: 10px;
    cursor: pointer;
    margin-top: -8px;
}
.modal-title{
position: sticky;
top: 0;

width: 100%;
background: rgb(255, 255, 255);
margin-top: -3px;
z-index: 5;
padding: 10px;
display: flex;
flex-direction: column;
justify-content: center;

align-items: center;


/* border-bottom: 1px solid rgb(140, 140, 140); */
}
.modal-title > h3{
    margin-top: 5px;
}

.loader{
    position: absolute;
    left: 0;
    top: 30%;
    
}

</style>