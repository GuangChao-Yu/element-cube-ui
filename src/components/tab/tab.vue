<template>
  <div class="tab">
    <cube-tab-bar
      :showSlider="true"
      :useTransition="false"
      v-model="selectedLabel"
      :data="tabs"
      ref="tabBar"
      class="border-bottom-1px"
    ></cube-tab-bar>
    <div class="slide-wrapper">
      <cube-slide
        :loop="false"
        :auto-play="false"
        :show-dots="false"
        :initial-index="currentIndex"
        @change="onChange"
        @scroll="onScroll"
        :options="slideOptions"
        ref="slide"
      >
        <cube-slide-item v-for="(tab,index) in tabs" :key="index">
          <component :is="tab.component" :data="tab.data" ref="component"></component>
        </cube-slide-item>
      </cube-slide>
    </div>
  </div>
</template>

<script>
export default {
  name: 'tab',
  props: {
    tabs: {
      type: Array,
      default: function () {
        return []
      }
    },
    initialIndex: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      currentIndex: this.initialIndex,
      slideOptions: {
        listenScroll: true,
        probeType: 3,
        directionLockThreshold: 0
      }
    }
  },
  mounted() {
    this.onChange(this.currentIndex)
  },
  methods: {
    onChange(current) {
      this.currentIndex = current
      const component = this.$refs.component[current]
      component.fetch && component.fetch()
    },
    onScroll(pos) {
      const tabBarWidth = this.$refs.tabBar.$el.clientWidth
      const slideWidth = this.$refs.slide.slide.scrollerWidth
      const tranform = -pos.x / slideWidth * tabBarWidth
      this.$refs.tabBar.setSliderTransform(tranform)
    }
  },
  computed: {
    selectedLabel: {
      get() {
        return this.tabs[this.currentIndex].label
      },
      set(newVal) {
        this.currentIndex = this.tabs.findIndex((value) => {
          return value.label === newVal
        })
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/variable';

.tab {
  display: flex;
  flex-direction: column;
  height: 100%;

  >>> .cube-tab {
    padding: 10px 0;
  }

  .slide-wrapper {
    flex: 1;
    overflow: hidden;
  }
}
</style>
