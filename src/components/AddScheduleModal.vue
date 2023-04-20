<template>
  <div v-if="isModal" class="modalContainer" @click="closeAddScheduleModal">
    <div class="modalCard" @click="clickInsideModal">
      <div>
        {{pickDate}}
      </div>
      <form @submit="clickFinish">
        <input type="time" required v-model="startTime">
        <input type="time" :min="startTime" required v-model="endTime">
        <input type="text" required v-model="inputTodo" placeholder="제목추가">
        <input type="color" v-model="boxColor" @change="changeBoxColor">
        <button type="submit">확인</button>
      </form>

    </div>
  </div>

</template>

<script>
export default {
  name: 'AddScheduleModal',
  props:{
    isModal: false,
    pickDate : String,
  },
  data(){
    return {
      startTime:'',
      endTime: '',
      inputTodo:'',
      boxColor:'#FFFFFF'
    }
  },
  methods:{
    closeAddScheduleModal : function(e){
      this.$emit('closeAddScheduleModal',false)
    },
    clickInsideModal: function (e){
      //이벤트 버블링 방지
      e.stopPropagation();
    },
    clickFinish: function (e){
      e.preventDefault()
      this.$emit('clickFinish',this.boxColor ,{setTime : `${this.startTime}~${this.endTime}`,todo:this.inputTodo,date: this.pickDate});
      this.closeAddScheduleModal()
    },
    changeBoxColor: function(e){
      this.boxColor = e.target.value;
    }
  }
}
</script>

<style scoped>
.modalContainer{
  width: 100vw;
  height: 100vh;
  background: rgba(0,0,0,.3);
  position: fixed;
  top:0;
}
.modalCard{
  width: 300px;
  height: 300px;
  border: 1px solid #959595;
  border-radius: 8px;
  position: absolute;
  top:50%;
  left:50%;
  transform: translate(-50%, -50%);
  background: #FFF;
  text-align: center;
  padding: 20px;
}
</style>
