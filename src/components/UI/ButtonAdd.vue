<template>
    <div>
        <button :class="{
            yellow: yellow,
            green: green
        }">
            <slot></slot>
        </button>
    </div>
</template>

<script>
    export default {
        name:'button-add',
        props:{
            yellow: {type: Boolean, default: false},
            green: {type: Boolean, default: false},
        }
    }
</script>

<style scoped>
button{
    width: 130px;
    height: 28px;
    outline: none;
    /* background: rgb(225, 175, 73); */
    /* background: rgb(0 94 116); */
    color: #FFFFFF;
    border: none;
    font-size: 14px;
    line-height: 0;
    cursor: pointer;       
}
.yellow{
    background: rgb(225, 175, 73);
}
.green{
background: rgb(0 94 116);
}
.yellow:hover{
    background: rgb(246, 203, 117);   
}
.yellow:active{
    background: rgb(212, 164, 68); 
}
.green:hover{
    background: rgb(246, 203, 117);   
}
.green:active{
    background: rgb(212, 164, 68); 
}
</style>