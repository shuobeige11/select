<template>
  <div class="alert">
     <section class="value" v-text="values" @click="HandleClick1"></section>
     
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
      values: '不限',
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
      this.values = value
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
       this.values = this.minDay
       this.flag = 4
       return
     }

     if (space && space < 0) {
       this.values = this.maxDay
       this.flag = 1
       return
     }

     if(space1 && space1 > 0) {
       this.values = this.minDay
       this.flag = 2
       return
     } 
    
     if ((space >= 0 && space1 <= 0) || (space >= 0 && !space1) || (!space && !space1 <= 0)) {
       this.flag = 3
       this.values = value
       return
     }
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
      this.init(this.values)
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
          this.$refs['month'].querySelector('.active').setAttribute('class', '')
          this.$refs['day'].querySelector('.active').setAttribute('class', '')

         
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
          this.$refs['day'].querySelector('.active').setAttribute('class', '')

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
      this.$emit('HandleDatepicker', this.values)
    }
  }
}
</script>

<style lang='scss' scoped>
@function toRem($px) {
  @return 64px * $px / 75px / 32px * 1rem;
}

.modal-mask {
  position:fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: toRem(380px);
  background: #fff;
  z-index: 10007;
}
.modal-cover {
  position: absolute;
  z-index: 10000;
  height: 100%;
  width: 100%;
  top:0;
  left:0;
  background: rgba(0, 0, 0, .7);
}
.modal-header {
  text-align: center;
  width:100%;
  line-height:toRem(98px);
  height:toRem(120px);
  font-size: toRem(28px);
  position: relative;
}

.close {
  position: absolute;
  line-height: toRem(88px);
  right:toRem(30px);
  top:0;
  color:#337bea;
  font-size:toRem(24px)
}
.modal-body {
  height: toRem(324px);
  overflow:hidden;
  position:relative;
  display:flex;
  .box {
    flex:1;
    position:relative;
  }
  dl {
    position:absolute;
    z-index: 10003;
    width:100%;
  }
  dd {
    height: toRem(78px);
    text-align:center;
    font-size:toRem(28px);
    line-height: toRem(78px);
    color:#9d9d9d;
  }
  dd.active {
    color:#444
  }
  .borders {
    color:#444;
    position:absolute;
    top:0;
    left:0;
    width:100%;
    height:toRem(76px);
    z-index: 10002;
    border-top: 1px solid #c6c6c6;
    border-bottom: 1px solid #c6c6c6
  }
}
</style>
