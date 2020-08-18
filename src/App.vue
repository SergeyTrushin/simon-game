<template>
  <div id="app">
    <h1>Simon The Game</h1>

    <ul class="simon">
      <li class="blue" @click="sound(1); add(1)" :class="{ hl : hlBlue }"></li>
      <li class="red" @click="sound(2); add(2)" :class="{ hl : hlRed }"></li><br>
      <li class="yellow" @click="sound(3); add(3)" :class="{ hl : hlYellow }"></li>
      <li class="green" @click="sound(4); add(4)" :class="{ hl : hlGreen }"></li>
    </ul>

    <h2>Round: {{ round }}</h2>

    <button @click="start ? start = false : start = true; startGame()">
      {{start ? 'Stop' : 'Start'}}
    </button>

    <h2>Level:</h2>

    <form id="level" @change="log">

      <label for="easy">
      Easy  
      <input type="radio" id="easy" name="level" v-bind:value="1.5" v-model="freeze"> 
      </label>
      
      <label for="normal">
      Normal  
      <input type="radio" id="normal" name="level" v-bind:value="1" v-model="freeze">
      </label>

      <label for="hard">
      Hard  
      <input type="radio" id="hard" name="level" v-bind:value="0.4" v-model="freeze">
      </label>

    </form>

  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data(){
  	return{
      round: 0,
      freeze: 1.5,
      start: false,
      sequence: [],
      playerSequence: [],
      hlRed: false,
      hlBlue: false ,
      hlYellow: false,
      hlGreen: false, 
  	}
  },
  methods:{
    log(){
      console.log(this.freeze)
    },
    startGame(){
      if(this.start){
        this.addRandom()
        this.playSequence()
      }else this.endGame()
    },
    endGame(){
      this.start =false
      this.round = 0
      this.sequence = []
      this.playerSequence = []
      this.resetHighlight()
    },

    addRandom(){
      this.round++
      this.sequence.push(this.getRandom())
    },
    getRandom(){
        return Math.floor(Math.random() * (4)) + 1;
    },

    sound(n){
       let sound =  new Audio(require(`@/assets/${n}.ogg`) )
       return sound
    },
    highlight(n){
    switch(n){
      case 1:
        this.hlBlue = true
        break;
      case 2:
        this.hlRed = true
        break;
      case 3:
        this.hlYellow = true
        break;
      case 4:
        this.hlGreen = true
        break;  
      }
    },
    resetHighlight() {
        this.hlGreen = false
        this.hlRed = false
        this.hlYellow = false
        this.hlBlue = false
    },

    async playSequence(){
      if (this.start){
        for (let i=0; i<this.sequence.length; i++){
          let tone =  this.sound(this.sequence[i])
          tone.play()
          this.highlight(this.sequence[i])
          await this.delay(0.5)
          this.resetHighlight()
          await this.delay(this.freeze)
        }

      }
    },
    async delay(sec){
          return new Promise((res,rej)=>setTimeout(sec=>res(), sec*1e3))
    },
    async add(n){
        if (this.start){
          this.highlight(n)
          let tone =  this.sound(n)
          tone.play()
          this.resetHighlight()
          this.playerSequence.push(n)

          if(this.playerSequence[this.playerSequence.length - 1] !== this.sequence[this.playerSequence.length - 1]){
            this.endGame()
          }
          if((this.playerSequence.length === this.sequence.length) && (this.playerSequence[this.playerSequence.length - 1] === this.sequence[this.sequence.length - 1])){
             console.log(this.playerSequence)
            this.playerSequence = []
            await this.delay(1)
            this.startGame()
          }
      }
    }
  },
}
</script>

<style lang="scss">

*{
  box-sizing: border-box;
}


h1,h2{
  text-align: center;
}

button{
  border-radius: 10px;
  background: #6DABE8;
  display: block;
  font-size: 25px;
  width: 120px;
  padding: 0.3em 0.6em;
  margin: 0 auto;
  outline: none;
}
.simon{
  width: 400px;
  margin: 0 auto;
  padding: 0;
  list-style: none;
  li{
    width: 200px;
    height: 200px;
    display: inline-block;
    opacity: .6;
    &:active{
      opacity:1;
    };
     &:hover {
      border: 3px solid black;
    };
  }
  .blue{
    background: #1E90FF;
    border-radius: 100% 0 0 0;
  }
  .red{
    background: #FF5643;
    border-radius: 0 100% 0 0;
  }
  .yellow{
    background: #FEEF33;
    border-radius: 0 0 0 100%;
  }
  .green{
    background: #BEDE15;
    border-radius: 0 0 100% 0;
  }
}

#level{
  text-align: center;
  font-size: 25px;
  input{
    margin-right: 25px;
  }
}

.hl{
  opacity: 1!important;
  border: 3px solid;
}
</style>
