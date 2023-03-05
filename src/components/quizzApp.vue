<template>
<section class="container">
  <h1>Practice Hiragana & Katakana</h1>
  <Vue3Lottie v-show="!isRUnning" :animationData="lottie" :height="200" :width="200" />
  <div class="timeLeft" v-show="!showReport && time != 0"><span>{{ time }}</span></div>
  <div v-show="isRUnning">
    <div class="counter" v-show="total">{{ correctCounter }} / {{ total }}</div>
    <div class="kana"> <h2>{{ currentKana.kana }}</h2> </div>
    <input ref="input" type="text" name="" id="inputField" v-model="input" @keyup="checkAnswer">
  </div>
  <div v-show="this.picked && isRUnning">{{ Math.round((sessionTimeLimit / 60))}}min left</div>
  <div v-show="isRUnning"><button @click="endSession">End Quizz</button></div>
  <div v-show="!isRUnning"><button @click="restart">Start Quizz</button></div>
  <!-- option -->
  <div class="options">
    <div class="inlineBlock">
      <input v-model="options" 
           type="checkbox" 
           value="hiragana" 
           name="hiragana"
           @click="isDisabled($event)"
            />
      <label for="hiragana">hiragana</label>
    </div>
    <div class="inlineBlock">
      <input v-model="options" 
           type="checkbox" 
           value="katakana" 
           name="katakana" 
           @click="isDisabled($event)"
           />
      <label for="katakana">katakana</label>
    </div>
    <div v-show="!isRUnning">
      <h5>How long do you want to practice for?</h5>
      <div>
        <input type="radio" id="one" value="0" v-model="picked" />
        <label for="one">no timer</label>
        <input type="radio" id="5min" value= 300 v-model="picked" />
        <label for="5">5 min</label>
        <input type="radio" id="10min" value= 600 v-model="picked" />
        <label for="10">10 min</label>
        <input type="radio" id="15min" value=900 v-model="picked" />
        <label for="15">15 min</label>
        <input type="radio" id="20min" value=1200 v-model="picked" />
        <label for="20">20 min</label>
      </div>
      <h5>Time limit per question</h5>
      <div>
        <input type="radio" value="0" v-model="questionTimer" />
        <label for="one">no timer</label>
        <input type="radio" id="5sec" value= 5 v-model="questionTimer" />
        <label for="5">5 seconds</label>
        <input type="radio" id="10sec" value= 10 v-model="questionTimer" />
        <label for="10">10 seconds</label>
      </div>
    </div>
    <!-- <div>
      <label for="time">time between questions</label>
      <input v-model="time" type="text" name="time">
    </div> -->
  </div>
  <div class="report" v-show="showReport">
    <h3>Session Report</h3>
    <p>{{ correctCounter }} / {{ total }}</p>
    <span> You got {{ Math.round((correctCounter/total) * 100)|| 0}}%</span>
    <div v-if="wrongAnswers.length != 0">
      <h5>Wrong answers : {{ total - correctCounter }}</h5>
      <div class="wrongAnswers" v-for = "item in wrongAnswers" :key="item.kana">
        <span>{{ item.kana }} : {{ item.roumaji }}</span>
      </div>
    </div>
  </div>
  <!-- <p v-show="answer"> You got the answer</p> -->
  <!-- <section class="answserSummary">
    <div v-for = "item in answers" :key="item.key">
      <span v-if="item.correct" class="pills correct">{{ item.jp }}</span>
      <span v-else class="pills wrong">{{ item.jp }}</span>
    </div>
  </section> -->
</section>
</template>

<script >
import lottie from '../assets/json/lottie.json'
import hiragana from '../assets/json/hiragana.json'
import katakana from '../assets/json/katakana.json'
export default {
  name: 'quizzApp',
  data() {
    return {
      options: ['hiragana','katakana'],
      input: '',
      answer: false,
      currentKana: '',
      correctCounter: 0,
      total: 0,
      // answers: [],
      correctAnswers: [],
      wrongAnswers: [],
      time : null,
      maxId: 0,
      isRUnning: false,
      timer : null,
      showReport: false,
      hiragana: hiragana,
      katakana: katakana,
      lottie: lottie,
      sessionTimeLimit: 0,
      picked: 0,
      questionTimer: 0,
      timeInterval: 0

    }
  },
  methods: {
    isDisabled(event) {
      console.log(event.target.value)
      if(this.options.length === 1 && event.target.value === this.options[0] ) {
        event.preventDefault();
      }
    },
    focusInput() {
      this.$refs.input.focus();
    },
    restart() {
      this.showReport = false;
      
      this.correctAnswers = [];
      this.total = 0;
      this.correctCounter = 0;
      this.wrongAnswers = [],
      clearInterval(this.timer);
      this.sessionTimeLimit = this.picked
      if(this.picked) this.sessionTimer()
      this.pickKana();
    },
    generateId() {
      this.maxId++
      return this.maxId
    },
    checkAnswer(){
      // let temp = this.currentKana
      if(this.input === this.currentKana.roumaji || this.input === this.currentKana.kana ) {
        this.answer = true
        // temp.correct = true
        // temp.key = this.generateId()
        this.correctAnswers.push(this.currentKana)
        this.correctCounter ++
        this.stop()
      } else {this.answer = false}
    },

    pickKana() {
      this.$nextTick(() => this.$refs.input.focus())
      this.currentKana = this.selectedKana[Math.floor(Math.random()*this.selectedKana.length)]
      // console.log(this.currentKana)
      this.input = ''
      this.start()
    },
    start(){
      let temp = this.currentKana
      this.time = this.questionTimer
      this.isRUnning = true
      if(this.isRUnning && this.time != 0) {
        this.timer = setInterval(() => {
        this.time --
        if(this.time < 0) {
          // temp.correct = false
          temp.key = this.maxId
          this.wrongAnswers.push(temp)
          // this.answers.push(temp)
          this.stop()
        }
      }, 1000);
      } 
    },
    stop() {
      this.time = 0
      clearInterval(this.timer)
      this.isRUnning = false
      this.total ++
      this.maxId ++
      this.pickKana()
    },
    endSession() {
      clearInterval(this.timer)
      clearInterval(this.timeInterval)
      this.picked = 0
      this.isRUnning = false
      this.timer = 0
      this.showReport = true
    },
    sessionTimer() {
      this.timeInterval = setInterval(() => {
        this.sessionTimeLimit --
        if(this.sessionTimeLimit <= 0) {
          // temp.correct = false
          clearInterval(this.timeInterval)
          this.endSession()
        }
      }, 1000);
    }
  },
  computed: {
    
    selectedKana() {
      let pair = {'hiragana': hiragana, 'katakana' : katakana}
      let arr = []
      for(let i = 0; i < this.options.length; i++) {
        arr.push(pair[this.options[i]])
      }
      // return arr.flat().filter(x => x.type !== "extended")
      // console.log(pair['katakana'].filter(x => x.type !== 'extended'))
      console.log(arr.flat())
      return arr.flat()
    } 
  },
  
  mounted() {
    // this.focusInput()
  }


}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container {
  max-width: 400px;
  margin: 0 auto;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
h2 {
  font-size: 5em;
  margin: 0;
}
h3 {
  margin-bottom: 20px;
}
input {
  width: 50%;
}
.pills{
  display: inline-block;
  width: 30px;
  height: 3Opx;
  border: 1px solid black;
  margin: 3px;
  border-radius: 3px;
}

.answserSummary{
  display: flex;
  flex-flow: wrap;
  align-items: left;
}

.correct {
  background-color: #42b983;
}

.wrong{
  background-color: red;
}

button{
  margin-top: 20px;
}

.options{
  text-align: left;
}
.options input{
  width: auto;
}

.wrongAnswers{
  text-align: left;
  display: inline-block;
  border: 1px solid black;
  padding: 3px;
  margin-right: 3px
}

.report{
  text-align: left;
}
.options {
  text-align: left;
  margin-top: 20px;
  /* display: flex; */
}

.options input {
  margin-right: 5px;
}
h5{
  margin-bottom: 5px;
}

.inlineBlock {
  display: inline-block;
  margin-right: 50px;
}
</style>
