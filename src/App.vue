<template>
  <div id="app">
    <div class="header">
      <h1>Hangman</h1>
      <h2>Vanilla JavaScript Hangman Game</h2>
      <p>Use the alphabet below to guess the word, or click hint to get a clue. </p>
    </div>
    <div class="detail">
      <div id="buttons">
        <ul id="alphabet">
          <li v-for="item in alphabet" :key="item" :id="getAlphabetListId(item)" @click="check(item)">
            {{item}}
          </li>
        </ul>
      </div>
      <p id="catagory-name">{{catagory}}</p>
      <div id="hold">
      </div>
      <p id="lives-left">{{liveText}}</p>
      <p id="game-clue">{{clueText}}</p>
      <canvas id="stickman">
        This Text will show if the Browser does NOT support HTML5 Canvas tag
      </canvas>
      <div class="action-button">
        <button id="hint" @click="hintClicked()">Hint</button>
        <button id="reset" @click="resetClicked()">Play again</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      alphabet: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p',
        'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'],
      categories: [
        ['everton', 'liverpool', 'swansea', 'chelsea', 'hull', 'manchester-city', 'newcastle-united'],
        ['alien', 'dirty-harry', 'gladiator', 'finding-nemo', 'jaws'],
        ['manchester', 'milan', 'madrid', 'amsterdam', 'prague'],
      ],
      clueText: '',
      guesses: [],
      lives: null,
      counter: null,
      space: null,
      chosenCategory: null,
      word: null,
      guess: null,
      drawArray: [],
      list: null,
      correct: null,
      letters: null,
    };
  },
  mounted() {
    this.drawArray = [
      this.rightLeg,
      this.leftLeg,
      this.rightArm,
      this.leftArm,
      this.torso,
      this.head,
      this.frame4,
      this.frame3,
      this.frame2,
      this.frame1,
    ];
    this.play();
  },
  components: {
  },
  computed: {
    catagory() {
      if (this.chosenCategory === this.categories[0]) {
        return 'The Chosen Category Is Premier League Football Teams';
      }
      if (this.chosenCategory === this.categories[1]) {
        return 'The Chosen Category Is Films';
      }
      if (this.chosenCategory === this.categories[2]) {
        return 'The Chosen Category Is Cities';
      }
      return '';
    },
    liveText() {
      let description = `You have ${this.lives} lives`;
      if (this.lives < 1) {
        description = 'Game Over';
      }
      // eslint-disable-next-line no-plusplus
      for (let i = 0; i < this.guesses.length; i++) {
        if (this.counter + this.space === this.guesses.length) {
          description = 'You Win!';
        }
      }
      return description;
    },
  },
  methods: {
    getAlphabetListId(item) {
      return `alphabet-${item}`;
    },
    check(item) {
      const sender = document.getElementById(this.getAlphabetListId(item));
      const guess = sender.innerHTML;
      if (sender.hasAttribute('class')) {
        return;
      }
      sender.setAttribute('class', 'active');
      sender.onclick = null;
      // eslint-disable-next-line no-plusplus
      for (let i = 0; i < this.word.length; i++) {
        if (this.word[i] === guess) {
          this.guesses[i].innerHTML = guess;
          this.counter += 1;
        }
      }
      const j = (this.word.indexOf(guess));
      if (j === -1) {
        this.lives -= 1;
        this.animate();
      }
    },
    resetAlphabets() {
      // eslint-disable-next-line no-plusplus
      for (let i = 0; i < this.alphabet.length; i++) {
        const listItem = document.getElementById(this.getAlphabetListId(this.alphabet[i]));
        if (listItem.hasAttribute('class')) {
          listItem.removeAttribute('class');
        }
      }
    },
    result() {
      const wordHolder = document.getElementById('hold');
      this.correct = document.createElement('ul');

      // eslint-disable-next-line no-plusplus
      for (let i = 0; i < this.word.length; i++) {
        this.correct.setAttribute('id', 'my-word');
        this.guess = document.createElement('li');
        this.guess.setAttribute('class', 'guess');
        if (this.word[i] === '-') {
          this.guess.innerHTML = '-';
          this.space = 1;
        } else {
          this.guess.innerHTML = '_';
        }

        this.guesses.push(this.guess);
        wordHolder.appendChild(this.correct);
        this.correct.appendChild(this.guess);
      }
    },
    animate() {
      const drawMe = this.lives;
      this.drawArray[drawMe]();
    },
    canvas() {
      const myStickman = document.getElementById('stickman');
      const context = myStickman.getContext('2d');
      context.beginPath();
      context.strokeStyle = '#fff';
      context.lineWidth = 2;
    },
    head() {
      const myStickman = document.getElementById('stickman');
      const context = myStickman.getContext('2d');
      context.beginPath();
      context.arc(60, 25, 10, 0, Math.PI * 2, true);
      context.stroke();
    },
    draw(pathFromx, pathFromy, pathTox, pathToy) {
      const myStickman = document.getElementById('stickman');
      const context = myStickman.getContext('2d');
      context.moveTo(pathFromx, pathFromy);
      context.lineTo(pathTox, pathToy);
      context.stroke();
    },
    frame1() {
      this.draw(0, 150, 150, 150);
    },
    frame2() {
      this.draw(10, 0, 10, 600);
    },
    frame3() {
      this.draw(0, 5, 70, 5);
    },
    frame4() {
      this.draw(60, 5, 60, 15);
    },
    torso() {
      this.draw(60, 36, 60, 70);
    },
    rightArm() {
      this.draw(60, 46, 100, 50);
    },
    leftArm() {
      this.draw(60, 46, 20, 50);
    },
    rightLeg() {
      this.draw(60, 70, 100, 100);
    },
    leftLeg() {
      this.draw(60, 70, 20, 100);
    },
    play() {
      this.chosenCategory = this.categories[Math.floor(Math.random() * this.categories.length)];
      this.word = this.chosenCategory[Math.floor(Math.random() * this.chosenCategory.length)];
      this.word = this.word.replace(/\s/g, '-');
      console.log(this.word);

      this.guesses = [];
      this.lives = 10;
      this.counter = 0;
      this.space = 0;
      this.resetAlphabets();
      this.result();
      this.canvas();
    },
    hintClicked() {
      const hints = [
        ['Based in Mersyside', 'Based in Mersyside', 'First Welsh team to reach the Premier Leauge', 'Owned by A russian Billionaire', 'Once managed by Phil Brown', '2013 FA Cup runners up', 'Gazza\'s first club'],
        ['Science-Fiction horror film', '1971 American action film', 'Historical drama', 'Anamated Fish', 'Giant great white shark'],
        ['Northern city in the UK', 'Home of AC and Inter', 'Spanish capital', 'Netherlands capital', 'Czech Republic capital'],
      ];

      const catagoryIndex = this.categories.indexOf(this.chosenCategory);
      const hintIndex = this.chosenCategory.indexOf(this.word);
      this.clueText = `Clue: - ${hints[catagoryIndex][hintIndex]}`;
    },
    resetClicked() {
      this.correct.parentNode.removeChild(this.correct);
      // this.letters.parentNode.removeChild(this.letters);
      this.clueText = '';

      const myStickman = document.getElementById('stickman');
      const context = myStickman.getContext('2d');
      context.clearRect(0, 0, 400, 400);
      this.play();
    },
  },
};
</script>

<style>
body {
  margin: 0px;
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-left: 5%;
  margin-right: 5%;
}
</style>

<style lang="scss">
/* Variabes */
$orange: #ffa600;
$green: #c1d72e;
$blue: #82d2e5;
$grey: #f3f3f3;
$white: #fff;
$base-color: $green ;

/* Mixin's */
@mixin transition {
  -webkit-transition: all 0.5s ease-in-out;
  -moz-transition: all 0.5s ease-in-out;
  transition: all 0.5s ease-in-out;
}
@mixin clear {
  &:after {
    content: "";
    display: table;
    clear: both;
  }
}
@mixin box-size {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
@mixin transition {
  -webkit-transition: all 0.3s ease-in-out;
  -moz-transition: all 0.3s ease-in-out;
  transition: all 0.3s ease-in-out;
}
@mixin fade {
  -moz-transition: all 1s ease-in;
  -moz-transition:all 0.3s ease-in-out;
  -webkit-transition:all 0.3s ease-in-out;
}
@mixin opacity {
  opacity:0.4;
  filter:alpha(opacity=40);
  @include fade;
}
@mixin corners ($radius) {
  -moz-border-radius: $radius;
  -webkit-border-radius: $radius;
  border-radius: $radius;
  -khtml-border-radius: $radius;
}
body {
  background: $base-color;
  font-family: "HelveticaNeue-Light","Helvetica Neue Light",
  "Helvetica Neue",Helvetica, Arial,"Lucida Grande",sans-serif;
  color: $white;
  height:100%;
  text-align:center;
  font-size:18px;
}
.header .detail{
  @include clear;
  width:100%;
  margin:0 auto;
}
canvas{
  color:$white;
  border: $white dashed 2px;
  padding:15px;
}
h1, h2, h3 {
  font-family: 'Roboto', sans-serif;
  font-weight: 100;
  text-transform: uppercase;
  margin:5px 0;
}
h1 {
  font-size: 2.6em;
}
h2 {
  font-size: 1.6em;
}
p{
  font-size: 1.6em;
}
#alphabet {
  @include clear;
  margin:15px auto;
  padding:0;
  max-width:900px;
}
#alphabet li {
  float:left;
  margin: 0 10px 10px 0;
  list-style:none;
  width:35px;
  height:30px;
  padding-top:10px;
  background:$white;
  color:$base-color;
  cursor:pointer;
  @include corners(5px);
  border: solid 1px $white;
  &:hover{
    background:$base-color;
    border: solid 1px $white;
    color:$white;
  }
}
#my-word {
  margin: 0;
  display: block;
  padding: 0;
  display:block;
}
#my-word li {
  position: relative;
  list-style: none;
  margin: 0;
  display: inline-block;
  padding: 0 10px;
  font-size:1.6em;
}
.active {
  @include opacity;
  cursor:default;
  &:hover{
    @include fade;
    @include opacity;
  }
}
#lives-left{
  font-size:1.6em;
  text-align:center;
  display:block;
}
button{
  @include corners (5px);
  background:$base-color;
  color:$white;
  border: solid 1px $white;
  text-decoration:none;
  cursor:pointer;
  font-size:1.2em;
  padding:18px 10px;
  width:180px;
  margin: 10px;
  outline: none;
  &:hover{
    @include transition;
    background:$white;
    border: solid 1px $white;
    color:$base-color;
  }
}
@media (max-width: 767px) {
  #alphabet {
    padding:0 0 0 15px;
  }
}
@media (max-width: 480px) {
  #alphabet {
    padding:0 0 0 25px;
  }
}
</style>
