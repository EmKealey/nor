<!-- Window is fixed, 102px, pointer cursor, more gradual blurry effect on surrounding words. -->
<!--  Comprehension questions appear afterwards in the same slide -->

<template>
  <!-- disabled translation -->
  <Experiment title="Emma Test MoTR Study" translate="no">
    <Screen :title="'Welcome! This is Emma\'s Test Page'" class="instructions" :validations="{
        ProlificID: {
          minLength: $magpie.v.minLength(2)
        }
      }">
        <!-- <WelcomeScreen /> -->
        <div style="width: 40em; margin: auto;">

          <div style="background-color: lightgrey; padding: 10px;">
              <b> University of Southern California Information Sheet:</b>
          </div>
          <p>
            Hello! My name is Emma Kealey. I am learning about MoTR at the University of Southern California in Los Angeles. Help!
            <br><br>
             This is an example information sheet. Please participate in this study. I need to practice my implementation
            <br><br>
            Your participation is mostly voluntary.
            <br><br>
            You can participate in the study if you meet the following criteria
            <br><br>
            1.	You understand English<br>
            2.	You are using a computer with a mouse or mouse tracker. <br>
            3.	You promise to be nice to me. 
            <br><br>
            If you agree to participate, you will read sentences in English (and maybe Spanish?) for 5 minutes. You may also be asked to answer some comprehension questions.
            <br><br>
            Upon completion, you will receive a heartfelt thank you from me, Emma Kealey
            <br><br>
            These results will inform my future research studies, and they will boost my ego (if you're nice)   
            <br>
          </p>
          <br>
        </div>
        <tr>
          <!-- Explain TurkID -->          
          <td>Please enter your ProlificID to continue: </td><td><input name="ProlificID" type="text" class="obligatory" v-model="$magpie.measurements.ProlificID"/></td>
        </tr>
        <div v-if="
            $magpie.measurements.ProlificID&&
            !$magpie.validateMeasurements.ProlificID.$invalid 
            ">
          <br> By clicking the button below you agree to continue <br><br>
          <br />
          <button 
            @click=" $magpie.addExpData({ ProlificID: $magpie.measurements.ProlificID}); $magpie.nextScreen()">
            Continue
          </button>
          
        </div>
    </Screen>

    <Screen class="instructions">
        <div style="width: 40em; margin: auto;">
          <div style="background-color: lightgrey; padding: 10px;">
              <b>Task Explanation</b>
          </div>
          <br>
          <p>In this study, you will read short texts and answer questions about them. However, unlike regular reading, the texts will be blurred.
To make the text clear, hover your mouse cursor over it. Take as much time as you need to understand the text.
When you have finished reading the text, answer the question located at the bottom of the screen and click "Next" to continue.</p>   
          
          <p>To help you get used to how the study is structured, you will complete several practice tasks before the main study begins.</p> 
          <br>
       </div>
      <button style="transform: translate(-50%, -50%)" @click="$magpie.nextScreen()">
        continue
      </button>
    </Screen>

    <template v-for="(trial, i) of practice_trials">
      <Screen :key="i" class="main_screen" :progress="i / practice_trials.length">
        <Slide>
          <form>
            <input type="hidden" class="item_id" :value="trial.item_id">
            <input type="hidden" class="type_id" :value="trial.type_id">
            <input type="hidden" class="condition" :value="trial.condition">
            <input type="hidden" class="list" :value="trial.list">
          </form>
          <div class="oval-cursor"></div>
          <template>
            <div v-if="showFirstDiv" class="readingText" @mousemove="moveCursor" @mouseleave="changeBack">
              <template v-for="(word, index) of trial.text.split(' ')">
                <span :key="index" :data-index="index" >
                  {{ word }}
                </span>
              </template>
            </div>
            <div class="blurry-layer" style="opacity: 0.3; filter: blur(3.5px); transition: all 0.3s linear 0s;"> 
              {{trial.text}}
            </div>
          </template>
          <button v-if="showFirstDiv" style= "bottom:50%; transform: translate(-50%, -50%)" @click="trial.question !== null ? toggleDivs(): saveAndDisable()" :disabled="!hasRead">
            {{ trial.question !== null ? 'Continue' : 'Continue' }}
          </button>

          <div v-else style = "position:absolute; bottom:15%; text-align: center; width: 100%; min-width: -webkit-fill-available;" >
            <template>
              <form>
                <!-- comprehension questions and the response options -->
                <!-- <div>{{ trial.question ? trial.question.replace(/ ?["]+/g, '') : '' }}</div> -->
                <div>{{ trial.question }}</div>
                <template v-for='(word, index) of trial.response_options'>
                  <input :id="'opt_'+index" type="radio" :value="word" name="opt" v-model="$magpie.measurements.response"/>{{ word }}<br/>
                    <!-- <label :for="'opt_'+index"> {{ word }}&nbsp</label> -->
                </template>
              </form>
            </template>
          </div>
          
          <button v-if="$magpie.measurements.response" style="transform: translate(-50%, -50%)" @click="toggleDivs(); $magpie.saveAndNextScreen()">
            Continue
          </button>
        </Slide>
      </Screen>
    </template>
    
    <Screen :title="'Practice Session is over'" class="instructions">
      <p>You have completed the training! Now you can proceed to the main study. </p>
      <p>Please click the button below to begin.</p> 
      <button style="transform: translate(-50%, -50%)" @click=" $magpie.nextScreen()">
            Advance / Continue
      </button>
    </Screen>

    <template v-for="(trial, i) of trials">
      <Screen :key="i" class="main_screen" :progress="i / trials.length">
        <Slide>
          <form>
            <input type="hidden" class="item_id" :value="trial.item_id">
            <input type="hidden" class="type_id" :value="trial.type_id">
            <input type="hidden" class="condition" :value="trial.condition">
            <input type="hidden" class="list" :value="trial.list">
          </form>
          <div class="oval-cursor"></div>
          <template>
            <div v-if="showFirstDiv" class="readingText" @mousemove="moveCursor" @mouseleave="changeBack">
              <template v-for="(word, index) of trial.text.split(' ')">
                <span :key="index" :data-index="index" >
                  {{ word }}
                </span>
              </template>
            </div>
            <div class="blurry-layer" style="opacity: 0.3; filter: blur(3.5px); transition: all 0.3s linear 0s;"> 
              {{trial.text}}
            </div>
          </template>
          <button v-if="showFirstDiv" style= "bottom:50%; transform: translate(-50%, -50%)" @click="trial.question !== null ? toggleDivs(): saveAndDisable()" :disabled="!hasRead">
            {{ trial.question !== null ? 'Продолжить' : 'Продолжить' }}
          </button>

          <div v-else style = "position:absolute; bottom:15%; text-align: center; width: 100%; min-width: -webkit-fill-available;" >
            <template>
              <form>
                <!-- comprehension questions and the response options -->
                <!-- <div>{{ trial.question ? trial.question.replace(/ ?["]+/g, '') : '' }}</div> -->
                <div>{{ trial.question }}</div>
                <template v-for='(word, index) of trial.response_options'>
                  <input :id="'opt_'+index" type="radio" :value="word" name="opt" v-model="$magpie.measurements.response"/>{{ word }}<br/>
                    <!-- <label :for="'opt_'+index"> {{ word }}&nbsp</label> -->
                </template>
              </form>
            </template>
          </div>
          
          <button v-if="$magpie.measurements.response" style="transform: translate(-50%, -50%)" @click="toggleDivs(); $magpie.saveAndNextScreen()">
            Продолжить
          </button>
        </Slide>
      </Screen>

    </template>

    <Screen style="min-height: 700px;">
      <div style="background-color: lightgrey; padding: 10px; width: 650px;">
            <b> демографическая информация </b>
      </div>
    
      <p>1. В данный момент я живу в русскоговорящей стране. &nbsp</p>
      <MultipleChoiceInput
          :response.sync= "$magpie.measurements.russianCountry"
          orientation="horizontal"
          :options="['Да', 'Нет']" />
      <p v-if = "$magpie.measurements.russianCountry == 'Нет'"> Как долго вы проживаете в нерусскоязычной стране? &nbsp </p>
      <TextareaInput v-if = "$magpie.measurements.russianCountry == 'Нет'"
            :response.sync= "$magpie.measurements.russianCountry2"
      />
      <br>
      <p>2. Я использую _____.</p>
        <MultipleChoiceInput
            :response.sync= "$magpie.measurements.device"
            orientation="horizontal"
            :options="['Компьютерную мышку', 'Трекпад', 'другое' ]" />
        <p v-if = "$magpie.measurements.device == 'другое'">Если вы указали другое, пожалуйста уточните: &nbsp &nbsp</p>
        <TextareaInput v-if = "$magpie.measurements.device == 'другое'"
              :response.sync= "$magpie.measurements.otherDevice"
        />
        <br>
      <p>3. Я _____. &nbsp</p>
        <MultipleChoiceInput
            :response.sync= "$magpie.measurements.hand"
            orientation="horizontal"
            :options="['Левша', 'Правша', 'Амбидекстр']" />
      <button style= "bottom:0%; transform: translate(-50%, -50%)" @click="$magpie.saveAndNextScreen();">Submit</button>
    </Screen>

    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import russ_list1 from '../trials/Russ_MoTR_List4.tsv';
import russ_list2 from '../trials/Russ_MoTR_List4.tsv';
import russ_list3 from '../trials/Russ_MoTR_List4.tsv';
import russ_list4 from '../trials/Russ_MoTR_List4.tsv';
import russ_practice from '../trials/emmaPracticeTrials.tsv';

import _ from 'lodash';
export default {
  name: 'App',
  data() {
    const lists = [russ_list1, russ_list2, russ_list3, russ_list4];
    const chosenItems = lists[Math.floor(Math.random() * lists.length)]; // randomly choose one of the lists
    const shuffledItems = _.shuffle(chosenItems); 
    const practice_trials = russ_practice ;
    const trials = shuffledItems;
    // Create a new column in localCoherences called 'response_options'
    // that concatenates the word in response_true with the two words in response_distractors
    const updatedTrials = trials.map(trial => {
      return {
        ...trial,
        response_options: _.shuffle(`${trial.response_true}|${trial.response_distractors}`.replace(/ ?["]+/g, "").split("|")),
      }
    });
    const updatedPracticeTrials = practice_trials.map(trial => {
      return {
        ...trial,
        response_options: _.shuffle(`${trial.response_true}|${trial.response_distractors}`.replace(/ ?["]+/g, "").split("|")),
      }
    });
    return {
      hasRead: false,
      trials: updatedTrials,
      practice_trials: updatedPracticeTrials,
      currentIndex: null,
      showFirstDiv: true,
      // currentItem: null,
      mousePosition: {
          x: 0,
          y: 0,
        },
  }},
  computed: {
  },
  mounted() { 
    setInterval(this.saveData, 50);
    },
  methods: {
    changeBack() {
      this.$el.querySelector(".oval-cursor").classList.remove('grow');
      this.$el.querySelector(".oval-cursor").classList.remove('blank');
      this.currentIndex = null;
    },
    saveData() {
        if (this.currentIndex !== null) {
          const currentElement = this.$el.querySelector(`span[data-index="${this.currentIndex}"]`);
          if (currentElement) {
            // console.log('word: ', currentElement.innerHTML);
            $magpie.addTrialData({
              Type: this.$el.querySelector(".type_id").value,
              Condition: this.$el.querySelector(".condition").value,
              ItemId: this.$el.querySelector(".item_id").value,
              List: this.$el.querySelector(".list").value,
              Index: this.currentIndex,
              Word: currentElement.innerHTML,
              mousePositionX: this.mousePosition.x,
              mousePositionY: this.mousePosition.y,
              wordPositionTop: currentElement.offsetTop,
              wordPositionLeft: currentElement.offsetLeft,
              wordPositionBottom: currentElement.offsetHeight + currentElement.offsetTop,
              wordPositionRight: currentElement.offsetWidth + currentElement.offsetLeft
          });
        } else {
          $magpie.addTrialData({
              Type: this.$el.querySelector(".type_id").value,
              Condition: this.$el.querySelector(".condition").value,
              ItemId: this.$el.querySelector(".item_id").value,
              List: this.$el.querySelector(".list").value,
              Index: this.currentIndex,
              mousePositionX: this.mousePosition.x,
              mousePositionY: this.mousePosition.y,
          });
          
        }
      }},
    moveCursor(e) {
      this.hasRead = true;
      this.$el.querySelector(".oval-cursor").classList.add('grow');
      let x = e.clientX;
      let y = e.clientY;
      const elementAtCursor= document.elementFromPoint(x, y).closest('span');
      if (elementAtCursor){
        this.$el.querySelector(".oval-cursor").classList.remove('blank');
        this.currentIndex = elementAtCursor.getAttribute('data-index');
      } else {
        this.$el.querySelector(".oval-cursor").classList.add('blank');
        const elementAboveCursor = document.elementFromPoint(x, y-10).closest('span');
        if (elementAboveCursor){
          this.currentIndex = elementAboveCursor.getAttribute('data-index');
        } else {
          this.currentIndex = -1;
        }
      }
      
      this.$el.querySelector(".oval-cursor").style.left = `${x+12}px`;
      this.$el.querySelector(".oval-cursor").style.top = `${y-6}px`;
      this.mousePosition.x = e.offsetX;
      this.mousePosition.y = e.offsetY;
    },
    toggleDivs() {
    this.showFirstDiv = !this.showFirstDiv;
    this.hasRead = false;
    },

    saveAndDisable() {
    $magpie.saveAndNextScreen();
    this.hasRead = false;
    },

    getScreenDimensions() {
      return {
        window_inner_width: window.innerWidth,
        window_inner_height: window.innerHeight
      };
    }
  },
};
</script>


<style>
  .experiment {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .main_screen {
    isolation: isolate;
    position: relative;
    width: 100%;
    height: auto;
    font-size: 18px;
    line-height: 40px;
  }
  .debugResults{
    width: 100%;
  }
  .readingText {
    /* z-index: 1; */
    position: absolute;
    color: white;
    text-align: left;
    font-weight: 450;
    cursor: pointer;
    padding-top: 2%;
    padding-bottom: 2%;
    padding-left: 12%;
    padding-right: 12%;
    top: 12%;
  }
  button {
    position: absolute;
    bottom: 0;
    left: 50%;
  }
  .oval-cursor {
    position: fixed;
    /* left: 10px; */
    z-index: 2;
    width: 1px;
    height: 1px;
    transform: translate(-50%, -50%);
    background-color: white;
    mix-blend-mode: difference;
    border-radius: 50%;
    pointer-events: none;
    transition: width 0.5s, height 0.5s;
  } 
  .oval-cursor.grow.blank {
    width: 80px;
    height: 38px;
  }
  .oval-cursor.grow {
    width: 102px;
    height: 38px;
    border-radius: 50%;
    box-shadow: 30px 0 8px -4px rgba(255, 255, 255, 0.1), -30px 0 8px -4px rgba(255, 255, 255, 0.1);
    background-color: rgba(255, 255, 255, 0.3);
    background-blend-mode: screen;
    pointer-events: none;
    transition: width 0.5s, height 0.5s;
    filter:blur(3px);
  }
  .oval-cursor.grow::before {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 70%;
    height: 70%;
    background-color: white;
    mix-blend-mode: normal;
    border-radius: 50%;
}
  .blurry-layer {
    position: absolute;
    pointer-events: none;
    color: black;
    text-align: left;
    font-weight: 450;
    padding-top: 2%;
    padding-bottom: 2%;
    padding-left: 12%;
    padding-right: 12%;
    top: 12%;
  }

  * {
    user-select: none; 
    -webkit-user-select: none; 
    -moz-user-select: none; 
    -ms-user-select: none; 
    }
</style>

