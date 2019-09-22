<template>
  <div id="app">
    <div style="position: absolute; left: 0; right: 0; top: 50px;">
        <transition :css="false" @enter="eItem" @leave="lItem">
            <div class="item" v-if="taken && taken.state !== 'Complete'">
                <div v-text="`${taken.text} - ${taken.mine}`" />
            </div>
        </transition>
    </div>


    <div style="position: absolute; left: 0; right: 0; top: 200px;">
        <transition-group name="anim-items" tag="ul" class="all-items" @enter="eList" @before-leave="blList" @leave="lList">
            <li v-for="item in sorted" :key="item.id" class="li-item">
                <div class="item" v-if="!item.state">
                    <div v-text="`${item.text} - ${item.mine}`" />
                </div>
            </li>
        </transition-group>
    </div>

    <div style="position: absolute; left: 0; right: 0; bottom: 200px">
        <button @click="add">+</button> | 
        <button @click="complete">Complete</button> |
        <button @click="take">Take</button> | 
        <button @click="untake">Untake</button> | 
        <button @click="sort">Sort</button> | 
    </div>

  </div>
</template>

<script>
import { TimelineLite } from 'gsap'
import shuffle from 'lodash.shuffle'

let id = 1
function gen (mine = false) {
    return { id: mine ? -1 : id, text: 'TEXT:' + id++, mine, state: null }
}

export default {
  data: () => ({
    list: [ gen(), gen(), gen(), gen() ],
    taken: null,

    takenPos: null,
    animTimeInSecs: .3,

    itemLeaveAnimation: 'left' // left, return
  }),

  computed: {
      sorted () {
          return this.list
        // return this.list.filter(i => i !== this.taken && i.state === null).sort((a, b) => b.id - a.id)
      }
  },
  methods: {
      add () {
          this.list.push(gen())
      },
      complete () {
          this.taken.state = 'Complete'
        // this.taken = null
      },
      take () {
          this.taken = this.sorted[2]
          this.taken.state = 'OnTheWay'
      },
      untake () {
          this.taken.state = null
          this.taken = null
      },
      sort () {
        //   this.list = shuffle(this.list)
        const copy3 = this.list[3]
        const copy0 = this.list[0]
        this.list.splice(0, 1, copy3)
        this.list.splice(3, 1, copy0)
      },

      /* item */
      eItem (el, onComplete) {
          this.$nextTick(() => {
            new TimelineLite({ onComplete }).set(el, { transform: `translateY(${this.takenPos + 200}px)` }).to(el, this.animTimeInSecs, { transform: 'translateY(0)' })
          })
      },
      lItem (el, onComplete) {
          if (this.taken && this.taken.state === 'Complete') {
            return new TimelineLite({ onComplete }).to(el, this.animTimeInSecs, { transform: 'translateX(-1000px)' })
          }
          else {
            return new TimelineLite({ onComplete }).to(el, this.animTimeInSecs, { transform: `translateY(${this.takenPos + 200}px)` })
          }
      },

       blList (el) {
          this.takenPos = el.offsetTop
      },
      eList(el, onComplete) {
          if (this.taken && this.taken.state === 'Complete') {
            return new TimelineLite({ onComplete }).set(el, { transform: 'translateY(100px)' }).to(el, this.animTimeInSecs, { transform: 'translateY(0)' })
          }
          else {
            return new TimelineLite({ onComplete }).set(el, { height: 0 }).to(el, this.animTimeInSecs, { height: '40px' })
          }
      },
      lList (el, onComplete) {
          new TimelineLite({ onComplete }).to(el, this.animTimeInSecs, { height: 0 })
      }
  }
}
</script>

<style>
html, body {
    height: 100%;
    width: 100%;
    margin: 0;
    padding: 0;
}
</style>

<style scoped>
#app {
    height: 100%;
    width: 100%;
    overflow: hidden;
    font-weight: 'bold';
    font-family: 'Be Vietnam', sans-serif;
    position: relative;
}
ul, li { 
    padding: 0;
    margin: 0;
    list-style-type: none;
}
.all-items {
    border: 1px solid blue;
    border-radius: 10px;
    height: 400px;
    overflow: hidden;
    overflow-y: scroll;
}
.li-item {
    overflow: hidden;
}
.item {
    padding: 8px;
    border: 1px solid black;
    background-color: #888;
    border-radius: 10px;
    color: white;
    box-sizing: border-box;
}
.anim-items-move {
    transition: .3s;
}
</style>
