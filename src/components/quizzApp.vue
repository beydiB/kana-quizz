<template>
<section class="container">
  <h1>Kana</h1>
  <Vue3Lottie v-show="!isRUnning" :animationData="lottie" :height="200" :width="200" />
  <div class="timeLeft" v-show="!showReport"><span>{{ time }}</span></div>
  <div v-show="isRUnning">
    <div class="counter" v-show="total">{{correctCounter}} / {{ total }}</div>
    <div class="kana"> <h2>{{ currentKana.kana }}</h2> </div>
    <input ref="input" type="text" name="" id="inputField" v-model="input" @keyup="checkAnswer">
  </div>
  
  <div v-show="isRUnning"><button @click="endSession">End Quizz</button></div>
  <div v-show="!isRUnning"><button @click="restart">Start Quizz</button></div>
  <!-- Radio option -->
  <!-- <div class="options">
    <input v-model="options" 
           type="checkbox" 
           value="hiragana" 
           name="ds" />
    <label for="ds">hiragana</label>
    <input v-model="options" 
           type="checkbox" 
           value="hiragana combinations" 
           name="al" />
    <label for="al">hiragana combinations</label>
    <input v-model="options"
             type="checkbox"
             value="hiragana extended"
             name="ml" />
    <label for="ml">hiragana extended</label>
  </div> -->
  <div class="report" v-show="showReport">
    <h3>Session Report</h3>
    <span> You got {{ Math.round((correctCounter/total) * 100)|| 0}}%</span>
    <div v-show="wrongAnswers.length != 0">
      <h5>Wrong answers : {{ total - correctCounter }}</h5>
      <div class="wrongAnswers" v-for = "item in wrongAnswers" :key="item.key">
        <span>{{ item.kana}} : {{ item.roumaji}}</span>
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
      options: [],
      input: '',
      answer: false,
      currentKana: '',
      correctCounter: 0,
      total: 0,
      answers: [],
      correctAnswers: [],
      wrongAnswers: [],
      time : null,
      maxId: 0,
      isRUnning: false,
      timer : null,
      showReport: false,
      hiragana: hiragana,
      katakana: katakana,
      lottie: lottie

    }
  },
  methods: {
    focusInput() {
      this.$refs.input.focus();
    },
    restart() {
      this.showReport = false;
      this.isRUnning = true;
      this.$nextTick(() => this.$refs.input.focus())
      this.correctAnswers = [];
      this.total = 0;
      this.correctCounter = 0;
      this.wrongAnswers = [],
      clearInterval(this.timer);
      this.pickKana();
    },
    generateId() {
      this.maxId++
      return this.maxId
    },
    checkAnswer(){
      let temp = this.currentKana
      if(this.input === this.currentKana.roumaji || this.input === this.currentKana.kana ) {
        this.answer = true
        temp.correct = true
        temp.key = this.generateId()
        // this.currentKana = this.currentKana.correct = true
        this.answers.push(temp)
        this.correctCounter ++
        this.stop()
      } else {this.answer = false}
    },

    pickKana() {
      this.focusInput();
      this.currentKana = this.selectedKana[Math.floor(Math.random()*this.hiragana.length)]
      console.log(this.currentKana)
      this.input = ''
      this.start()
    },
    start(){
      let temp = this.currentKana
      this.time = 5
      this.isRUnning = true
      if(this.isRUnning) {
        this.timer = setInterval(() => {
        this.time --
        if(this.time < 0) {
          temp.correct = false
          temp.key = this.generateId()
          this.wrongAnswers.push(temp)
          this.answers.push(temp)
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
      this.pickKana()
    },
    endSession() {
      clearInterval(this.timer)
      this.isRUnning = false
      this.timer = 0
      this.showReport = true
    }
  },
  computed: {
    selectedKana() {
      return hiragana.concat(katakana).filter(x => x.type != "extended")
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
</style>
