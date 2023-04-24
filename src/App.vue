<template>
  <div id="app">
    <div class="calendar">
      <div class="calendarHeader">
        <div class="calendarHeader-title">
          <button type="button" @click="handleMonth(-1)">
            이전달
          </button>
          <div>
            {{ visibleYear }}년 {{visibleMonth}}월
          </div>
          <button type="button" @click="handleMonth(1)">
            다음달
          </button>
        </div>

        <div class="calendarWeek">
          <div v-for="week in weeks" :key="week" class="weekStyle">{{week}}</div>
        </div>
      </div>
      <div class="calendarBody">
        <div v-for="(day, i) in visibleDayOfThisMonth" :key="i" :class="day.color" @click="openAddScheduleModal(day)">{{ day.day }}
        <div v-for="event in day.events" :style="`background: ${event.boxColor}`">{{ event.toDo }}</div>
        </div>
      </div>
    </div>
    <AddScheduleModal :key="addScheduleModalKey" :isModal="isAddScheduleModal" :pick-date="pickDate" @closeAddScheduleModal="closeAddScheduleModal" @clickFinish="clickFinish"/>
    </div>

</template>

<script>
import dayjs from 'dayjs'
import AddScheduleModal from "@/components/AddScheduleModal.vue";

export default {
  mounted() {
    this.createCalendar()
  },
  components: {
    AddScheduleModal
  },
  data(){
    return {
      currentDay : dayjs(),
      visibleDayOfThisMonth:[],
      weeks : ['일','월','화','수','목','금','토'],
      visibleYear : 2022,
      visibleMonth : 4,
      lastWeekdayOfThisMonth : 0,
      isAddScheduleModal: false,
      pickDate:'',
      addScheduleModalKey:0,
    }
  },
  watch:{
    currentDay: function (){
      this.resetDayArray()
      this.createCalendar()
    }
  },
  methods:{
    resetDayArray: function (){
      this.visibleDayOfThisMonth=[]
    },
    createCalendar: function (){
      this.visibleYear = this.currentDay.year();
      this.visibleMonth = this.currentDay.month() + 1;
      const dayOfThisMonth = dayjs(this.dateToString(this.visibleYear, this.visibleMonth, 1)).get('d')
      const dayOfLastMonth = dayjs(this.dateToString(this.visibleYear, this.visibleMonth,1)).add(-(dayOfThisMonth),'day').get('date')
      this.lastWeekdayOfThisMonth = this.currentDay.daysInMonth();
      this.getNConsecutiveNumbersFromStart(this.visibleYear+'-'+(this.visibleMonth-1), dayOfLastMonth,dayOfLastMonth+dayOfThisMonth-1,'grey')
      this.getNConsecutiveNumbersFromStart(this.visibleYear+'-'+this.visibleMonth, 1, this.lastWeekdayOfThisMonth );
      const remainingArrayLength = 42 - this.visibleDayOfThisMonth.length
      const dayOfNextMonth = dayjs(this.dateToString(this.visibleYear,this.visibleMonth,this.lastWeekdayOfThisMonth))
          .add(remainingArrayLength,'day')
          .get('date');
      this.getNConsecutiveNumbersFromStart(this.visibleYear+'-'+(this.visibleMonth+1),1, dayOfNextMonth,'grey')
    },
    dateToString : function (year,month,day){
      return `${year}-${month}-${day}`
    },
    getNConsecutiveNumbersFromStart: function(yearMonth,startDay, endDay,fontColor){
      for(let i = startDay; i<=endDay;i++){
        this.visibleDayOfThisMonth.push({fullDate : `${yearMonth}-${i}`, day: i, color:fontColor,events:[]})
      }
    },
    openAddScheduleModal: function(pickDay){
      this.pickDate = pickDay.fullDate
      this.isAddScheduleModal = true
    },
    closeAddScheduleModal: function(val){
      this.isAddScheduleModal = val;
      this.addScheduleModalKey += 1
    },
    clickFinish: function(boxColor,scheduleData){
      const index = this.visibleDayOfThisMonth.findIndex((x)=> x.fullDate === scheduleData.date)
      this.visibleDayOfThisMonth[index].events.push({toDo : scheduleData.setTime + ' ' + scheduleData.todo, boxColor: boxColor})
    },
    handleMonth: function(val){
      this.currentDay = dayjs(this.dateToString(this.visibleYear, this.visibleMonth,1)).add(val,'month')
    }
  }
}
</script>

<style scoped>
.calendar{
  height: 80vh;
  max-width: 80%;
  margin: 20px auto;
  font-size: 12px;
  color: #3c3c3c;
}
.calendarHeader{
  text-align: center;
  /*display: flex;*/
}
.calendarHeader-title{
  display: flex;
  justify-content: center;
  align-content: center;
  gap: 20px;
  margin-bottom: 20px;
}
.calendarWeek{
  display: grid;
  grid-template-columns: repeat(7 , 1fr);
}
.calendarWeek{
  border-top: 1px solid;
}
.calendarWeek,
.calendarBody{
  display: grid;
  grid-template-columns: repeat(7 , 1fr);
  text-align: center;
  border-left: 1px solid;
  border-color: #D9D9D9;
}
.calendarBody{
  height: 100%;
  grid-auto-rows: 1fr;
}
.calendarWeek > div,.calendarBody > div{
  border-bottom: 1px solid;
  border-right: 1px solid;
  border-color: #D9D9D9;
}
.grey{
  color: #959595;
}
.box-color{
  background: #3c3c3c;
}
</style>
