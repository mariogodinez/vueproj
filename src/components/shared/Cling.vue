<template>
  <div class="vue-cling">
    <div
      ref="slotContainer"
      :class="[isSticky ? 'vue-cling__sticky-container' : 'vue-cling__default-container']"
      :style="isSticky ? stickyContainerStyles : null"
    ><slot></slot></div>
    <div class="vue-cling__dummy" v-if="isSticky" :style="dummyStyles"></div>
  </div>
</template>

<script>
export default {
  props: ['top'],
  data () {
    return {
      isSticky: false,
      slotContainerRect: null,
      contentRect: null,
      containerRect: null,
      debug: {}
    }
  },
  computed: {
    stickyContainerStyles () {
      return {
        zIndex: 1000,
        position: 'fixed',
        top: `${this.top || 0}px`,
        left: `${this.containerRect.left}px`,
        width: `${this.containerRect.width}px`
      }
    },
    dummyStyles () {
      return {
        height: `${this.slotContainerRect.height || 0}px`,
        width: `${this.slotContainerRect.width || 0}px`
      }
    }
  },
  methods: {
    frameTick () {
      this.getFrame(this.frameTick)
      this.slotContainerRect = this.$refs.slotContainer.getBoundingClientRect()
      this.contentRect = this.$el.getBoundingClientRect()
      this.containerRect = this.$el.parentElement.getBoundingClientRect()
      const isAboveViewport = this.top !== false && this.contentRect.top <= 0 + this.top
      this.isSticky = isAboveViewport
    },
    getFrame (callback) {
      if (window.requestAnimationFrame) {
        return window.requestAnimationFrame(callback)
      }
      return window.setTimeout(callback, 1000 / 60)
    }
  },
  mounted () {
    this.getFrame(this.frameTick)
  }
}
</script>
