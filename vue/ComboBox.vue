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
  @import './main.scss';
</style>
