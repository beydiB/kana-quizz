<template>
<section class="container">
  <h1>Kana</h1>
  <div class="timeLeft"><span>{{ time }}</span></div>
  <div class="counter" v-show="total">{{correctCounter}} / {{ total }}</div>
  <div class="kana"> <h2>{{ currentKana.jp }}</h2> </div>
  <input type="text" name="" id="inputField" v-model="input" @keyup="checkAnswer">
  <div><button @click="endSession">End session</button></div>
  <div v-show="showReport">
    <h3>Wrong Answers</h3>
    <div v-for = "item in wrongAnswers" :key="item.key">
      <span>{{ item}}</span>
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

<script>
export default {
  name: 'quizzApp',
  data() {
    return {
      input: '',
      answer: false,
      currentKana: '',
      correctCounter: 0,
      total: 0,
      answers: [],
      correctAnswers: [],
      wrongAnswers: [],
      time : 15,
      maxId: 0,
      isRUnning: false,
      timer : null,
      showReport: false,
      kana : [
  {
    jp: 'あ',
    romanji: 'a'
  },
  {
    jp: 'い',
    romanji: 'i'
  },
  {
    jp: 'う',
    romanji: 'u'
  },
  {
    jp: 'え',
    romanji: 'e'
  },
  {
    jp: 'お',
    romanji: 'o'
  }
]
    }
  },
  methods: {
    generateId() {
      this.maxId++
      return this.maxId
    },
    checkAnswer(){
      let temp = this.currentKana
      if(this.input === this.currentKana.romanji) {
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
      this.currentKana = this.kana[Math.floor(Math.random()*this.kana.length)]
      this.input = ''
      this.start()
    },
    start(){
      let temp = this.currentKana
      this.time = 10
      this.isRUnning = true
      if(this.isRUnning) {
        this.timer = setInterval(() => {
        this.time --
        if(this.time < 0) {
          temp.correct = false
          temp.key = this.generateId()
          this.wrongAnswers.push(temp.jp)
          this.answers.push(temp)
          console.log(this.answers)
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
  
  mounted() {
    this.pickKana()
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
</style>
