
<template>
  <transition name="modal-1">
    <div
      v-if="showBg" class="modal-bg"
      @transitionend="onBgTransitionEnd"
    >
      <transition name="modal-2">
        <div v-if="showContent" class="modal-content" @transitionend="onContentTransitionEnd">
          <div class="modal-header">
            <div class="modal-title">{{ title }}</div>
            <div>
              <t-button label="X" small-size @clicked="onCloseClicked"/>
            </div>
          </div>
          <div class="modal-body">
            <slot></slot>
          </div>
          <div class="modal-footer">
            <t-button :label="okButtonLabel" small-size @clicked="onOkClicked"/>
            <t-button :label="cancelButtonLabel" small-size @clicked="onCancelClicked"/>
          </div>
        </div>
      </transition>
    </div>
  </transition>
</template>


<script>
import TButton from './TButton.vue'
export default {
  name: 'TModal',
  props: {
    title: {
      type: String
    },
    okButtonLabel: {
      type: String,
      default: 'OK'
    },
    cancelButtonLabel: {
      type: String,
      default: 'cancel'
    },
    show: Boolean
  },
  data () {
    return {
      showContent: false,
      showBg: false
    }
  },
  watch: {
    show (nv) {
      if (nv) {
        this.showBg = true
      } else {
        this.showContent = false
      }
    }
  },
  methods: {
    onCloseClicked () {
      this.$emit('close-me')
    },
    onOkClicked () {
      this.$emit('ok-clicked')
    },
    onCancelClicked () {
      this.$emit('cancel-clicked')
    },
    onBgTransitionEnd (e) {
      e.stopPropagation()
      if (this.show) {
        this.showContent = true
      }
    },
    onContentTransitionEnd (e) {
      e.stopPropagation()
      if (!this.show) {
        this.showBg = false
      }
    }
  },
  components: { TButton }
}

</script>

<style lang="stylus" scoped>
.modal-bg
  background: rgba(0, 0, 0, 0.5)
  position: fixed
  width: 100vw
  height: 100vh
  top: 0
  left: 0
  z-index: 1
.modal-content
  width: 60%
  min-width: 300px
  max-width: 700px
  min-height: 200px
  background: white
  margin: 2rem auto
  box-shadow: 0px 10px 20px 0px rgba(0, 0, 0, 0.5)
  border-radius: 10px
  display: flex
  flex-direction: column
.modal-header
  border-bottom: 1px solid #cdcdcd
  justify-content: space-between
.modal-footer
  border-top: 1px solid #cdcdcd
  justify-content: flex-end
  gap: 1rem
.modal-header, .modal-footer
  height: 50px
  display: flex
  align-items: center
  padding: 0 1rem
.modal-body
  flex-grow: 1
  padding: 1rem
  text-align: left
.modal-title
  font-size: 1.2rem
  font-weight: bold

.modal-1-enter-from, .modal-1-leave-to
  // transform: translateY(-100px)
  opacity: 0
.modal-1-enter-to, .modal-1-leave-from
  // transform: translateY(0)
  opacity: 1
.modal-1-enter-active, .modal-1-leave-active
  // transition: transform .5s ease
  // transform-origin: top
  transition: opacity 0.3s ease
.modal-2-enter-from, .modal-2-leave-to
  transform: translateY(-200px)
.modal-2-enter-to, .modal-2-leave-from
  transform: translateY(0)
.modal-2-enter-active, .modal-2-leave-active
  transition: transform 0.3s ease
  transform-origin: top
</style>