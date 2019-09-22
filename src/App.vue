<template>
  <div id="app">

    <div style="height: 700px">

      <transition-group tag="ul" name="animlist" class="all-items">
        <li v-for="item in sorted" :key="item.id">
            <div class="item">
                <div v-text="`${item.text} - ${item.mine}`" />
            </div>
        </li>
       </transition-group>
    </div>
    <br>
    <div>
        <button @click="add">+</button> | 
        <button @click="complete">Complete</button> |
        <button @click="take">Take</button> | 
    </div>
  </div>
</template>

<script>

let id = 1
function gen (mine = false) {
    return { id: mine ? -1 : id, text: 'TEXT:' + id++, mine }
}

export default {
  data: () => ({
    list: [ gen(), gen(), gen(), gen(), gen(), gen(), gen() ]
  }),

  computed: {
      sorted () {
        return this.list
      }
  },
  methods: {
      add () {
        //   const index = 0
        //   this.list.splice(index, 0, gen())
          this.list.push(gen())
      },
      complete () {
        //   this.list.pop()
        this.list.shift()
      },
      take () {
          this.list[0].mine = false
          this.list[this.list.length - 2 ].mine = true
      },
  }
}
</script>

<style scoped>
#app {
    overflow: hidden;
    font-weight: 'bold';
    font-family: 'Be Vietnam', sans-serif;
}
ul, li { 
    padding: 0;
    margin: 0;
    list-style-type: none;
}
ul > li:nth-child(1) {
    margin-bottom: 100px;
}
.all-items {
    border: 1px solid blue;
    border-radius: 10px;
}
.item {
    margin: 8px;
    padding: 8px;
    border: 1px solid black;
    background-color: #888;
    border-radius: 10px;
    color: white;
    box-sizing: border-box;
}
/* anim */
.animitem-enter {
    opacity: 0
}
.animitem-enter-active {
    transition: all 2s;
}


.animlist-enter {
    opacity: 0;
    /* transform: translateX(50px) scaleY(0.5); */
    transform: translateY(800px)
}
.animlist-enter-active {
    transition: all 0.5s;
}


.animlist-leave-active {
    transition: all 0.5s;
}

.animlist-leave-to {
    opacity: 0;
    transform: translateX(-1150px);
}
</style>
