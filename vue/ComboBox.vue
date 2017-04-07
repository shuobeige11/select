<template>
  <div class="alert">
     <section class="modal-mask" ref="mask" v-if="value.length" :style="'display:' + (!showSelect ? 'none' : 'block')">
        <header class="modal-header">
            <header class="heading" v-text="title">弹出层</header>
            <span class="close" @click="HandleClick">完成</span>
        </header>
        <section class="modal-body">
            <div class="box">
              <dl class="modal-body-con" ref="box">
                  <dd v-for="n of value" v-text='n'></dd>
              </dl>
            </div>
            <section class="borders"> </section>
        </section>
     </section>
  </div>
</template>

<script>
import _touch from './util'

export default {
  props: {
    value: {
      type: Array,
      default: []
    },
    title: {
      type: String,
      default: ''
    },
    showSelect: {
      type: Boolean,
      default: true
    }
  },

  data () {
    return {
      pos: 0
    }
  },

  mounted () {
    if (!this.value.length) return
    if (this.value.length > 0) {
        let box = this.$refs['box']
        new _touch({
            element: box,
            subElement: 'dd',
            active: 'active',
            pos: this.pos
        })
    }
  },
  
  updated () {
    if (!this.value.length) return
    if (this.value.length > 0) {
        let box = this.$refs['box']
        new _touch({
            element: box,
            subElement: 'dd',
            active: 'active',
            pos: this.pos
        })
    }
  },

  methods: {
    HandleClick() {
      let value = this.$refs['box'].querySelector('.active').innerText
      let pos = this.$refs['box'].querySelectorAll('dd')
      for (let i = 0; i < pos.length; i++) {
          let className = pos[i].getAttribute('class')
          if (className == 'active') {
              this.pos = i
          }
      }
      this.$store.dispatch('selectHide')
      this.$emit('HandleClick', value)
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
  z-index: 10007
}
.modal-cover {
  position: absolute;
  z-index: 10000;
  height: 100%;
  width: 100%;
  top:0;
  left:0;
  background: rgba(0, 0, 0, .7)
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
