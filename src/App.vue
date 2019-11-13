<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
          <h1>Animations</h1>
          <hr>
          <select v-model="alertAnimation" class="form-control">
            <option value="fade">Fade</option>
            <option value="slide">Slide</option>
          </select>
          <br>
          <button class="btn btn-primary" @click="showAlert = !showAlert">Show Alert</button>
          <br><br>
          <transition :name="alertAnimation" type="transition">
            <div class="alert alert-info" v-show="showAlert">This is some info...</div>
          </transition>
          <transition :name="alertAnimation" type="animation">
            <div class="alert alert-info" v-if="showAlert">This is some info...</div>
          </transition>
          <transition name="fade" type="transition" appear>
            <div class="alert alert-info" v-if="showAlert">This is some info...</div>
          </transition>
          <transition 
            appear
            enter-active-class="animated bounce"
            leave-active-class="animated shake"
          >
            <div class="alert alert-info" v-if="showAlert">This is some info...</div>
          </transition>
          <transition :name="alertAnimation" type="transition" mode="out-in">
            <div class="alert alert-info" v-if="showAlert" key="info">This is some info...</div>
            <div class="alert alert-warning" v-else key="warning">This is some warning...</div>
          </transition>
          <hr>
          <button
            class="btn btn-primary"
            @click="loadElement = !loadElement"
          >Load or Remove Element</button>
          <br><br>
          <transition
            @before-enter="beforeEnter"
            @enter="enter"
            @after-enter="afterEnter"
            @enter-cancelled="enterCancelled"

            @before-leave="beforeLeave"
            @leave="leave"
            @after-leave="afterLeave"
            @leave-cancelled="leaveCancelled"
            :css="false"
          >
            <div style="width: 200px; height: 100px; background-color: lightgreen;" v-if="loadElement"></div>
          </transition>
          <hr>
          <button
            class="btn btn-primary"
            @click="selectedAlert === 'app-success-alert' ? selectedAlert = 'app-danger-alert' : selectedAlert = 'app-success-alert'"
          >Toggle</button>
          <br><br>
          <transition name="fade" mode="out-in">
            <component :is="selectedAlert"></component>
          </transition>
          <hr>
          <button class="btn btn-primary" @click="addItem">Add Item</button>
          <br><br>
          <ul class="list-group">
            <transition-group name="slide">
              <li 
                class="list-group-item" 
                v-for="(number, index) in numbers" 
                :key="index"
                @click="removeItem(index)"
                style="cursor: pointer"
              >{{ number }}</li>
            </transition-group>
          </ul>
          <hr>
        </div>
      </div>
    </div>
    <hr>
    <div class="container">
      <div class="row">
        <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
          <h1>Arithmatic Quiz</h1>
        </div>
      </div>
      <hr>
      <div class="row">
        <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
          <transition
            name="flip"
            mode="out-in"
          >
            <component 
              :is="qaMode" 
              @answered="answered($event)"
              @confirmed="qaMode = 'app-question'"
            ></component>
          </transition>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DangerAlert from './DangerAlert'
import SuccessAlert from './SuccessAlert'
import Answer from './components/Answer'
import Question from './components/Question'
export default {
  data () {
    return {
      showAlert: true,
      alertAnimation: 'fade',
      loadElement: true,
      elementWidth: 0,
      selectedAlert: 'appDangerAlert',
      numbers: [1,2,3,4,5],
      qaMode: 'app-question'
    }
  },
  methods: {
    answered (isCorrect) {
      if (isCorrect) {
        this.qaMode = 'app-answer'
      } else {
        this.qaMode = 'app-question'
        alert('Wrong selection, try again.')
      }
    },
    addItem () {
      const pos = Math.floor(Math.random() * this.numbers.length)
      this.numbers.splice(pos, 0, this.numbers.length + 1)
    },
    removeItem (index) {
      console.log('>>>-removeItem-index->',index)
      this.numbers.splice(index, 1)
    },
    beforeEnter (el) {
      console.log('>>>-beforeEnter-el->',el)
      this.elementWidth = 0
      el.style.width = this.elementWidth + 'px'
    },
    enter(el, done) {
      console.log('>>>-enter-el->', el)
      let round = 1
      const interval = setInterval(() => {
        el.style.width = (this.elementWidth + round * 10) + 'px'
        round++
        if (round > 20) {
          clearInterval(interval)
          done()
        }
      }, 20)
      
    },
    afterEnter (el) {
      console.log('>>>-afterEnter-el->',el)
    },
    enterCancelled (el) {
      console.log('>>>-enterCancelled-el->',el)
    },

    beforeLeave (el) {
      console.log('>>>-beforeLeave-el->',el)
      this.elementWidth = 200
      el.style.width = this.elementWidth + 'px'
    },
    leave(el, done) {
      console.log('>>>-leave-el->',el)
      let round = 1
      const interval = setInterval(() => {
        el.style.width = (this.elementWidth - round * 10) + 'px'
        round++
        if (round > 20) {
          clearInterval(interval)
          done()
        }
      }, 20)
     
    },
    afterLeave (el) {
      console.log('>>>-afterLeave-el->',el)
    },
    leaveCancelled (el) {
      console.log('>>>-leaveCancelled-el->',el)
    },
  },
  components: {
    appDangerAlert: DangerAlert,
    appSuccessAlert: SuccessAlert,
    appAnswer: Answer,
    appQuestion: Question
  }
}
</script>

<style>
  .flip-enter {
    /* transform: rotateY(0deg); */
  }
  .flip-enter-active {
    animation: flip-in 0.5s ease-out forwards;
  }
  .flip-leave {
    /* transform: rotateY(0deg); */
  }
  .flip-leave-active {
    animation: flip-out 0.5s ease-out forwards;
  }
  @keyframes flip-out {
    from {
      transform: rotateY(0deg);
    }
    to {
      transform: rotateY(90deg)
    }
  }
  @keyframes flip-in {
    from {
      transform: rotateY(90deg)
    }
    to {
      transform: rotateY(0deg)
    }
  }

  .fade-enter {
    opacity: 0;
  }
  .fade-enter-active {
    transition: opacity 1s;
  }
  .fade-leave {
    /* opacity: 1; */
  }
  .fade-leave-active {
    transition: opacity 1s;
    opacity: 0;
  }
  .slide-enter {
    /* transform: translateY(20px) */
  }
  .slide-enter-active {
    animation: slide-in 1s ease-out forwards;
    transition: opacity .5s;
  }
  .slide-leave {

  }
  .slide-leave-active {
    animation: slide-out 1s ease-out forwards;
    transition: opacity 1s;
    opacity: 0;
    position: absolute;
  }

  .slide-move {
    transition: tranform 1s;
  }

  @keyframes slide-in {
    from {
      transform: translateY(20px)
    }
    to {
      transform: translateY(0px)
    }
  }

  @keyframes slide-out {
    from {
      transform: translateY(0px)
    }
    to {
      transform: translateY(20px)
    }
  }
</style>
