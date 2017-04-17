<template>
  <div class="alert">
     <section class="value"  @click="HandleClick1"></section>
     
     <section class="modal-mask" ref="mask" :style="'display:' + (!shows ? 'none' : 'block')">
        <header class="modal-header">
            <header class="heading">日历选择</header>
            <span class="close" @click="HandleClick">完成</span>
        </header>
        <section class="modal-body">
            <div class="box">
              <dl class="modal-body-con" ref="year">
                  <dd v-for="n of space.year" v-text='!min ? (1980 + n) : (mins.year - 1 + n)'></dd>
              </dl>
            </div>
            <div class="box">
              <dl class="modal-body-con" ref="month">
                  <dd v-for="n of space.month" v-text=" month + n"></dd>
              </dl>
            </div>
            <div class="box">
              <dl class="modal-body-con" ref="day">
                  <dd v-for="n of space.day" v-text=" day + n "></dd>
              </dl>
            </div>
            <section class="borders"> </section>
        </section>
     </section>
  </div>
</template>

<script>
import _touch from './util'
import timers from './timers'

export default {
  props: {
    maxDay: {
      type: String,
      default: ''
    },
    minDay: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      yearElement: null,
      monthElement: null,
      dayElement: null,
      values: '',
      val: null,      
      flag: 0,
      space: {
        year: 0,
        month: 0,
        day: 0
      },
      mins:{
        year: 0,
        month: 0,
        day: 0
      },
      maxs:{
        year: 0,
        month: 0,
        day: 0
      },
      pos: {
        year: 0,
        month: 0,
        day: 0
      },
      month: 0,
      day: 0,
      shows: false,
      max: null,
      min: null
    }
  },

  created () {
     let value = this.times() 
     let nowDay = +new Date()
     let year =  Math.ceil(value.split('-')[0])
     if (!this.maxDay && !this.minDay) {
      this.flag = 3
      this.max = '2050-12-31'
      this.min = '1980-01-01'
      this.val = value
      return
     }

     if (this.maxDay && !this.minDay) {
      this.max = this.maxDay
      this.min = '1980-01-01'
     }

     if (!this.maxDay && this.minDay) {
      this.max = '2050-12-31'
      this.min = this.minDay
     }

     if(this.maxDay && this.minDay) {
      this.max = this.maxDay
      this.min = this.minDay
     }

     let max = !this.maxDay ? '' : this.maxDay.replace(/\-/g, '/')
     let min = !this.minDay ? '' : this.minDay.replace(/\-/g, '/')
    
     let space = !max ? '' : (+new Date(max)) - nowDay
     let space1 = !min ? '' : (+new Date(min)) - nowDay

     if ( Math.ceil(max.split('/')[0]) == Math.ceil(min.split('/')[0])) {
       this.val = this.values = this.minDay
       this.flag = 4
       return
     }

     if (space && space < 0) {
       this.val = this.values = this.maxDay
       this.flag = 1
       return
     }

     if(space1 && space1 > 0) {
       this.val = this.values = this.minDay
       this.flag = 2
       return
     } 
    
     if ((space >= 0 && space1 <= 0) || (space >= 0 && !space1) || (!space && !space1 <= 0)) {
       this.flag = 3
       this.val =this.values = value
       return
     }
  },

  mounted() {
    document.addEventListener("click", (e) => {
      let el = e.target.nodeName
      if (el === 'HTML') {
          this.shows = false
      }
    }, false)
  },
  
  methods: {
    init(value) {
       let year,
          month,
          day,
          minYear,
          minMonth,
          minDay,
          maxYear,
          maxMonth,
          maxDay
      
      year = Math.ceil(value.split('-')[0])
      month = Math.ceil(value.split('-')[1])
      day = Math.ceil(value.split('-')[2])

      this.maxs.year = Math.ceil(this.max.split('-')[0])
      this.maxs.month = Math.ceil(this.max.split('-')[1])
      this.maxs.day = Math.ceil(this.max.split('-')[2])

      this.mins.year = Math.ceil(this.min.split('-')[0])
      this.mins.month = Math.ceil(this.min.split('-')[1])
      this.mins.day = Math.ceil(this.min.split('-')[2])

      this.space.year = this.maxs.year - this.mins.year + 1

      if (this.flag == 1) {
        this.pos.year = this.maxs.year - this.mins.year
        this.month = 0
        this.day = 0

        this.space.month = this.maxs.month
        this.space.day = timers(year, month)

        this.pos.month = 0
        this.pos.day = 0
      }
      
      if (this.flag == 2) {
        this.pos.year = 0
        this.month = month - 1
        this.day = day - 1
        
        this.space.month = 13 - month
        this.space.day = timers(year, month) - day

        this.pos.month = 0
        this.pos.day = 0
      }
      
      if (this.flag == 3) {
        this.pos.year = year - this.mins.year
        this.month = 0
        this.day = 0
        this.space.month = (year - this.mins.year) > 0 ? 12 : this.maxs.month
        this.space.day = timers(year, month)

        this.pos.month = month - 1
        this.pos.day = day - 1
      }

      if (this.flag == 4) {
        this.pos.year = year - this.mins.year
        this.month = this.mins.month - 1
        this.day = this.mins.day - 1
        this.space.month = this.maxs.month - this.mins.month + 1
        this.space.day = timers(year, month) - this.mins.day
        this.pos.month = 0
        this.pos.day = 0
      }
    },

    HandleClick1() {
      this.shows = true
      this.init(this.val)
      this.yearElement = this.$refs['year']
      this.monthElement = this.$refs['month']
      this.dayElement = this.$refs['day']

      let year = new _touch({
        element: this.yearElement,
        subElement: 'dd',
        active: 'active',
        pos: this.pos.year,
        fn: () => {
          let year = Math.ceil(this.$refs['year'].querySelector('.active').innerText)
         
          if (year == this.mins.year) {
            this.creatMonth(this.min, year, 0)
          } else if(year == this.maxs.year) {
            this.creatMonth(this.max, year, 1)
          } else {
            this.month = 0
            this.day = 0
            this.space.month = 12 - this.month
            let day = timers(year, this.month)
            this.space.day = day - this.day
            new _touch({
              element: this.monthElement,
              subElement: 'dd',
              active: 'active',
              pos: 0
            })
            
            new _touch({
              element: this.dayElement,
              subElement: 'dd',
              active: 'active',
              pos: 0
            })
          }
        }
      })


      let month = new _touch({
        element: this.monthElement,
        subElement: 'dd',
        active: 'active',
        pos: this.pos.month,
        fn: () => {
          let year = Math.ceil(this.$refs['year'].querySelector('.active').innerText)
          let month = Math.ceil(this.$refs['month'].querySelector('.active').innerText)

          if(year == this.mins.year && month == this.mins.month) {
            this.creatDay(this.min, month, year, 1)
            return
          }
          if(year == this.maxs.year && month == this.maxs.month) {
            this.creatDay(this.max, month, year, 0)
            return
          }

          this.day = 0
          let day = timers(year, month)
          this.space.day = day - this.day

          new _touch({
            element: this.dayElement,
            subElement: 'dd',
            active: 'active',
            pos: 0
          })
        }
        
      })

      new _touch({
        element: this.dayElement,
        subElement: 'dd',
        active: 'active',
        pos: this.pos.day
      })

    },
    creatMonth (value, year, flag) {
      this.month = Math.ceil(value.split('-')[1]) - 1
      this.day = Math.ceil(value.split('-')[2]) - 1
      if (!flag) {
        this.space.month = 12 - this.month
      } else {
        this.space.month = this.month + 1
        this.month = 0
      }

      if(year == this.maxs.year) {
        this.day = 0
      }
      
      let day = timers(year, this.month)
      this.space.day = day - this.day

      new _touch({
        element: this.monthElement,
        subElement: 'dd',
        active: 'active',
        pos: 0
      })
      
      new _touch({
        element: this.dayElement,
        subElement: 'dd',
        active: 'active',
        pos: 0
      })
    },

    creatDay(value, month, year, flag) {
      this.day = Math.ceil(value.split('-')[2]) - 1
      let day = timers(year, month)
      this.space.day = day - this.day

      if (!flag) {
        this.space.day = this.day + 1
        this.day = 0
      }

      new _touch({
        element: this.dayElement,
        subElement: 'dd',
        active: 'active',
        pos: 0
      })
    },

    times() {
      let time = new Date()
      return time.getFullYear() + '-' + ((time.getMonth() + 1) > 9 ? (time.getMonth() + 1) : '0' + (time.getMonth() + 1)) +
            '-' + (time.getDate() > 9 ? time.getDate() : '0' + time.getDate())
    },

    HandleClick() {
      let year = this.$refs['year'].querySelector('.active').innerText
      let month = this.$refs['month'].querySelector('.active').innerText
      let day = this.$refs['day'].querySelector('.active').innerText
      this.values = year + '-' + (Math.ceil(month) > 9 ? month : '0' + month) + '-' + (Math.ceil(day) > 9 ? day : '0' + day)
      this.shows = false
      let box1 = this.$refs['year']
      let pos1 = box1.querySelectorAll('dd')
      let box2 = this.$refs['month']
      let pos2 = box2.querySelectorAll('dd')
      let box3 = this.$refs['day']
      let pos3 = box3.querySelectorAll('dd')
      for (let i = 0; i < pos1.length; i++) {
        pos1[i].setAttribute('class', '')
      }
      for (let i = 0; i < pos2.length; i++) {
        pos2[i].setAttribute('class', '')
      }
      for (let i = 0; i < pos3.length; i++) {
        pos3[i].setAttribute('class', '')
      }
      this.$emit('HandleDatepicker', this.values)
    }
  }
}
</script>

<style lang='scss' scoped>
  @import './main.scss';
</style>
