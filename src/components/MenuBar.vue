<template>
    <div class='menu-bar'>
      <transition name='slide-up'>
        <div class="menu-wrapper"
        v-show="visible"
        :class="{'hide-box-shadow': settingVisible || !visible}">
          <div class="icon-wrapper">
            <span class="icon-menu icon" @click="showSetting(3)"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-progress icon" @click="showSetting(2)"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-bright icon" @click="showSetting(1)"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-a icon" @click="showSetting(0)">A</span>
          </div>
        </div>
      </transition>
      <transition name='slide-up'>
        <div class="setting-wrapper" v-show="settingVisible">
          <div class="setting-font-size" v-if='showTag === 0'>
            <div class="preview" :style="{fontSize: fontSizeList[0].fontSize + 'px'}">A</div>
            <div class='select'>
              <div
              class="select-wrapper"
              v-for="(item, index) in fontSizeList"
              :key="index"
              @click="setFontSize(item.fontSize)"
              >
                <div class="line"></div>
                <div class="pointer-wrapper">
                  <div class="point" v-show="defaultFontSize === item.fontSize">
                    <div class="smallpoint"></div>
                  </div>
                </div>
                <div class="line"></div>
              </div>
            </div>
            <div class="preview" :style="{fontSize: fontSizeList[fontSizeList.length -1 ] + 'px'}">A</div>
          </div>
          <div class="setting-theme" v-else-if="showTag === 1">
            <div class="setting-theme-item"
            v-for="(item, index) in themeList"
            :key="index"
            @click="setTheme(index)"
            >
              <div class="preview" :style="{background: item.style.body.background}"
              :class="{'no-border': item.style.body.background != '#fff'}"></div>
              <div
              class="text"
              :class="{'selected' : index === defaultTheme}">{{item.name}}</div>
            </div>
          </div>
          <div class="setting-progress" v-else-if="showTag === 2">
            <div class="progress-wrapper">
              <input type="range" class="progress"
              max="100"
              min="0"
              step="1"
              @change="onProgressChange($event.target.value)"
              @input="onProgressInput($event.target.value)"
              :value="progress"
              :disabled="!bookAvailable"
              ref="progress"
              >
            </div>
            <div class="text-wrapper">
              <span>{{bookAvailable ? progress +"%" : "加载中..."}}</span>
            </div>
          </div>
        </div>
      </transition>
      <ContentView
        :ifShowContent = 'ifShowContent'
        v-show="ifShowContent"
        :navigation = 'navigation'
        :bookAvailable='bookAvailable'
        @jumpTo='jumpTo'
      >

      </ContentView>
      <transition name='fade'>
        <div
        class="content-mask"
        v-show="ifShowContent"
        @click="hideContent"
        >

        </div>
      </transition>
    </div>
</template>

<script>
import ContentView from '@/components/Content';
export default {
  components: {
    ContentView
  },
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    fontSizeList: Array,
    defaultFontSize: {
      type: Number,
      default: 16
    },
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: Boolean,
    navigation: Object,
  },
  data(){
    return {
      settingVisible: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false,
      ifShowSetting: false,
    }
  },
  methods: {
    hideContent () {
      this.ifShowContent = false
    },
    showSetting(tag){
      this.showTag = tag;
      if(this.showTag === 3){
        this.settingVisible = false
        this.ifShowContent = true
      }
      else{
        this.settingVisible = true
        this.ifShowContent = false
      }
    },
    hideSetting() {
      this.settingVisible = false;
    },
    setFontSize(fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    setTheme (index) {
      this.$emit('setTheme', index)
    },
    onProgressChange (progress) {
      this.$emit('onProgressChange', progress)
    },
    onProgressInput (progress) {
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    jumpTo (href) {
      this.$emit('jumpTo', href)
    }
  }
}
</script>
<style lang='scss' scoped>
@import '../assets/styles/global';
.menu-bar{
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: px2rem(48);
    z-index: 101;
    display: flex;
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0 ,0, 0.15);
    &.hide-box-shadow{
      box-shadow: none
    }
    .icon-wrapper{
      flex: 1;
      @include center;
      icon-progress {
        font-size: px2rem(28)
      }
    }
    &.slide-up-enter, &.slide-up-leave-to {
      transform: translate3d(0, 100% , 0)
    }
    &.slide-up-enter-to , &.slide-sp-leave{
      transform: translate3d(0, 0, 0)
      }
    &.slide-up-enter-active, &.slide-up-leave-active {
      transition: all .3s linear
    }
  }
  .setting-wrapper{
    position: absolute;
    z-index: 101;
    bottom: px2rem(48);
    left: 0;
    width: 100%;
    height: px2rem(60);
    background: white;
    box-shadow: 0 px2rem(-8) rgba(0,0,0,0.15);
    .setting-font-size{
      display: flex;
      height: 100%;
      .preview {
        flex: 0 0 px2rem(40);
        @include center
      }
      .select{
        display: flex;
        flex: 1;
        .select-wrapper{
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child{
            .line{
              &:first-child{
                border-top: none;
              }
            }
          }
          &:last-child{
            .line{
              &:last-child{
                border-top: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #cccccc;
          }
          .pointer-wrapper {
            position: relative;
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #cccccc;
            .point {
              position: absolute;
              top: px2rem(-8);
              left: px2rem(-10);
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              background: white;
              border: px2rem(1) sold #cccccc;
              box-shadow: 0 px2rem(4) px2rem(4) rgba( 0, 0, 0,0.15);
              @include center;
              .smallpoint{
                width: px2rem(5);
                height: px2rem(5);
                background: rgb(0, 0, 0);
                border-radius: 50%;
              }
            }
          }
        }
      }
    }
    .setting-theme {
      height: 100%;
      display: flex;
      .setting-theme-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: px2rem(5);
        box-sizing: border-box;
        .preview {
          flex: 1;
          box-sizing: border-box;
          border: px2rem(1) solid #ccc;
          &.no-border{
            border: none;
          }
        }
        .text {
          flex: 0 0 px2rem(20);
          font-size: px2rem(14);
          color: #ccc;
          @include center;
          &.selected{
            color: #333333
          }
        }
      }

    }
    .setting-progress {
      position: relative;
      height: 100%;
      width: 100%;
      .progress-wrapper{
        width: 100%;
        height: 80%;
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        .progress{
          width: 100%;
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          &::-webkit-slider-thumb{
            -webkit-appearance: none;
            width: px2rem(20);
            height: px2rem(20);
            border-radius: 50%;
            background: white;
            box-shadow: 0 4px 4px 0 rgba(0 , 0 , 0, 0.15 );
            border: px2rem(1) solid #ddd;
          }
        }
      }
      .text-wrapper {
        flex: 1;
        width: 100%;
        display: flex;
        @include center;
        font-size: px2rem(14);
      }
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, 0.8);
  }
}

</style>
