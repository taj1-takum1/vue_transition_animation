<template>
  <div class="main">
    <button @click="show = !show">切り替え</button>
    <br><br>
    <button @click="add">追加</button>
    <ul style="width: 200px; margin: auto;">
      <transition-group name="fade">
        <li style="cursor: pointer;" @click="remove(index)" v-for="(number, index) in numbers" :key="number">{{ number }}</li>
      </transition-group>
    </ul>
    <br>
    <transition name="fade">
      <p v-if="show">hello</p>
    </transition>
    <transition name="slide" type="animation">
      <p v-if="show">bye</p>
    </transition>

    <transition
      :css="false"
      @before-enter="beforeEnter"
      @enter="enter"
      @after-enter="afterEnter"
      @enter-cancelled="enterCancelled"
      @before-leave="beforeLeave"
      @leave="leave"
      @after-leave="afterLeave"
      @leave-cancelled="leaveCancelled"
    >
      <div class="circle" v-if="show"></div>
    </transition>
    <br><br>

    <button @click="myComponent = 'ComponentA'">ComponentA</button>
    <button @click="myComponent = 'ComponentB'">ComponentB</button>
    <transition name="fade" mode="out-in">
      <component :is="myComponent"></component>
    </transition>

    <transition name="fade" mode="out-in">
      <p v-if="show" key="bye">さよなら</p>
      <p v-if="!show" key="hello">こんにちは</p>
    </transition>
  </div>
</template>

<script>
import ComponentA from "./components/ComponentA.vue";
import ComponentB from "./components/ComponentB.vue";

export default {
  data() {
    return {
      numbers: [0, 1, 2],
      nextNumber: 3,
      show: true,
      myComponent: "ComponentA"
    };
  },
  components: {
    ComponentA,
    ComponentB
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.numbers.length);
    },
    add() {
      this.numbers.splice(this.randomIndex(), 0, this.nextNumber);
      this.nextNumber += 1;
    },
    remove(index) {
      this.numbers.splice(index, 1);
    },
    beforeEnter(el) {
      /* 現れる前 */
      el.style.transform = 'scale(0)'
    },
    enter(el, done) {
      /* 現れる時 */
      let scale = 0;
      const interval = setInterval(() => {
        el.style.transform = `scale(${scale})`;
        scale += 0.1;
        if ( scale > 1 ) {
          clearInterval(interval);
          done();
        }
      }, 200);
    },
    leave(el, done) {
      /* 消える時 */
      let scale = 1;
      const interval = setInterval(() => {
        el.style.transform = `scale(${scale})`;
        scale -= 0.1;
        if ( scale < 0 ) {
          clearInterval(interval);
          done();
        }
      }, 200);
    }
  }
};
</script>

<style scoped>
.circle {
  width: 200px;
  height: 200px;
  margin: auto;
  background-color: deeppink;
  border-radius: 100px;
}

.fade-move {
  transition: transform 1s;
}
.fade-enter {
  /* 現れる時の最初の状態 */
  opacity: 0;
}
.fade-enter-active {
  /* 現れる時のトランジションの状態 */
  transition: opacity 2s;
}
.fade-enter-to {
  /* 現れる時の最後の状態 */
  opacity: 1;
}
.fade-leave {
  /* 消える時の最初の状態 */
  opacity: 1;
}
.fade-leave-active {
  /* 消える時のトランジションの状態 */
  transition: opacity 2s;
  position: absolute;
  width: 200px;
}
.fade-leave-to {
  /* 消える時の最後の状態 */
  opacity: 0;
}

.slide-enter, .slide-leave-to {
  opacity: 0;
}
.slide-enter-active {
  animation: slide-in 0.5s;
  transition: opacity 0.5s;
}
.slide-leave-active {
  animation: slide-in 0.5s reverse;
  transition: opacity 0.5s;
}

@keyframes slide-in {
  from {
    transform: translateX(100px);
  }
  to {
    transform: translateX(0);
  }
}

.main {
  width: 70%;
  margin: auto;
  padding-top: 5rem;
  text-align: center;
}
</style>
