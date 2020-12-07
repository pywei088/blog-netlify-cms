<template>
  <div class="flex inline-flex items-center justify-center">
    <div class="p-3 -ml-3 cursor-pointer" @click="$emit('open-nav')">
      <div class="bar-1 mb-1" :class="iconClass('top')"></div>
      <div class="bar-2" :class="iconClass('bot')"></div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    isOpen: Boolean,
  },

  methods: {
    iconClass(bar) {
      return {
        'translate-1': this.isOpen && bar === 'top',
        'translate-2': this.isOpen && bar === 'bot',
        'translate-1-reverse': !this.isOpen && bar === 'top',
        'translate-2-reverse': !this.isOpen && bar === 'bot',
      }
    },
  },
}
</script>

<style>
.bar-1,
.bar-2 {
  transform-origin: 6px;
  width: 20px;
  height: 2px;
  background-color: rgb(0, 0, 0);
  transition: 500ms;
}

.translate-1 {
  animation: forwards;
  animation-name: burger-top;
  animation-duration: 0.4s;
}

.translate-2 {
  animation: forwards;
  animation-name: burger-bot;
  animation-duration: 0.4s;
}

.translate-1-reverse {
  animation: backwards;
  animation-direction: reverse;
  animation-name: burger-top-reverse;
  animation-duration: 0.4s;
}

.translate-2-reverse {
  animation: backwards;
  animation-direction: reverse;
  animation-name: burger-bot-reverse;
  animation-duration: 0.4s;
}

@keyframes burger-top {
  20% {
    transform: translateY(3px);
  }
  100% {
    transform: rotate(45deg);
  }
}

@keyframes burger-bot {
  20% {
    transform: translateY(-3px);
  }
  100% {
    transform: rotate(-45deg);
  }
}

@keyframes burger-top-reverse {
  50% {
    transform: translateY(3px);
  }
  100% {
    transform: rotate(45deg);
  }
}

@keyframes burger-bot-reverse {
  50% {
    transform: translateY(-3px);
  }
  100% {
    transform: rotate(-45deg);
  }
}
</style>
