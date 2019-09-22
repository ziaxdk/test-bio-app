<template>
  <div id="app">
    <div style="position: absolute; left: 0; right: 0; top: 50px;">
        <transition :css="false" @enter="eItem" @leave="lItem">
            <div class="item" v-if="taken && taken.userId && taken.state !== 'Complete'">
                <div v-text="`${taken.text} - ${taken.userId}`" />
            </div>
        </transition>
    </div>


    <div style="position: absolute; left: 0; right: 0; top: 200px;">
        <transition-group name="anim-items" tag="ul" class="all-items" @before-enter="beList" @enter="eList" @before-leave="blList" @leave="lList">
            <li v-for="item in sorted" :key="item.id" class="li-item">
                <div class="item">
                    <div v-text="`${item.text} - ${item.userId}`" />
                </div>
            </li>
        </transition-group>
    </div>

    <div style="position: absolute; left: 0; right: 0; bottom: 200px">
        <input type="text" v-model="takeId" style="width: 20px" /> |
        <button @click="take" :disabled="!takeId">Take</button> | 
        <button @click="untake" :disabled="!takeId">Untake</button> | 
        <button @click="add">+</button> | 
        <button @click="complete" :disabled="!this.taken">Complete</button> |
        <button @click="sort">Sort</button> | 
    </div>

    <div style="position: absolute; right: 0; bottom: 0">
        <pre>{{ list }}</pre>
    </div>

  </div>
</template>

<script>
import { TimelineLite } from 'gsap'
import shuffle from 'lodash.shuffle'

let id = 1
function gen () {
    return { id, text: 'TEXT:' + id++, state: null, userId: null }
}

export default {
  data: () => ({
    list: [ gen(), gen(), gen(), gen() ],
    takeId: 3,
    taken: null,

    takenPos: 0,
    animTimeInSecs: 0.3,

    EnterListNew: true,
    LeaveItemComplete: true
  }),

  computed: {
      sorted () {
          return this.list.filter(i => i.userId === null && (i.state === null || i.state !== 'Complete'))
      }
  },
  methods: {
      add () {
          this.EnterListNew = true
          this.list.push(gen())
      },
      take () {
          this.taken = this.list.splice(this.takeId - 1, 1)[0]
          this.taken.userId = 1
      },
      untake () {
          this.EnterListNew = false
          this.LeaveItemComplete = false
          this.taken.userId = null
          this.list.splice(this.takeId - 1, 0, this.taken)
      },
      complete () {
          this.LeaveItemComplete = true
          this.taken.state = 'Complete'
          this.taken.userId = null
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
            new TimelineLite({ onComplete }).set(el, { transform: `translateY(${this.takenPos + 66}px)` }).to(el, this.animTimeInSecs, { transform: 'translateY(0)' })
          })
      },
      lItem (el, onComplete) {
          if (this.LeaveItemComplete) {
            return new TimelineLite({ onComplete }).to(el, this.animTimeInSecs, { transform: 'translateX(-1000px)' })
          }
          else {
            return new TimelineLite({ onComplete }).to(el, this.animTimeInSecs, { transform: `translateY(${this.takenPos + 66}px)` })
          }
      },

      /* List */
      beList (el) {
      },
      blList (el) {
        //   this.takenPos = el.offsetTop // TODO
      },
      eList(el, onComplete) {
          if (this.EnterListNew) {
            return new TimelineLite({ onComplete }).set(el, { transform: 'translateY(100px)' }).to(el, this.animTimeInSecs, { transform: 'translateY(0)' })
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
    transition: transform 0.3s;
}
</style>
