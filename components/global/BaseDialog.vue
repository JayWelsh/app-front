<template>
  <div v-if="show" class="top-0 left-0 fixed h-screen w-full z-50" @click="showContent = false">
    <transition name="dialog-bg" mode="out-in" appear>
      <div v-if="showContent" class="absolute h-full w-full bg-black bg-opacity-50" />
    </transition>
    <div class="flex h-screen items-end sm:items-center justify-center">
      <transition
        name="content"
        mode="out-in"
        appear
        @after-leave="$emit('close')"
      >
        <div v-if="showContent" class="relative w-full" @click.stop>
          <slot />
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BaseDialog',

  props: {
    show: { type: Boolean, default: false }
  },

  data () {
    return {
      showContent: false
    }
  },

  watch: {
    show (newVal) {
      this.showContent = newVal
    }
  }
}
</script>

<style lang="css" scoped>
.content-enter-active {
  transition: all .2s ease-in-out;
}
.content-leave-active {
  transition: all .2s ease-in-out;
}
.content-enter, .content-leave-to {
  opacity: 0;
  transform: translateY(70%) scale(0.95);
}

.dialog-bg-enter-active {
  transition: all .3s ease-in-out;
}
.dialog-bg-leave-active {
  transition: all .3s ease-in-out;
}
.dialog-bg-enter, .dialog-bg-leave-to {
  background: transparent;
}
</style>
