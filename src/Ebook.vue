<template>
  <div class='ebook'>
    <TitleBar :visible ="visible"></TitleBar>
    <div class='read-wrapper'>
      <div id='read'></div>
      <div class='mask'>
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleVisible"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <MenuBar
      :visible='visible'
      :fontSizeList='fontSizeList'
      :defaultFontSize='defaultFontSize'
      :themeList='themeList'
      :defaultTheme='defaultTheme'
      :bookAvailable='bookAvailable'
      :navigation='navigation'
      @jumpTo='jumpTo'
      @onProgressChange='onProgressChange'
      @setTheme='setTheme'
      @setFontSize='setFontSize'
      ref='menuBar'>
    </MenuBar>
  </div>
</template>

<script>
import Epub from 'epubjs'
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
export default {
  components: {
    TitleBar,
    MenuBar
  },
  data(){
    return{
      visible: false,
      fontSizeList : [
        {fontSize: 12},
        {fontSize: 14},
        {fontSize: 16},
        {fontSize: 18},
        {fontSize: 20},
        {fontSize: 22},
        {fontSize: 24}
      ],
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000', 'background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000', 'background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff', 'background': '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              'color': '#000', 'background': 'rgb(241,236,226)'
            }
          }
        }
      ],
      defaultFontSize: 16,
      defaultTheme: 0,
      bookAvailable: false,
      navigation: {},
    }
  },
  methods: {
    showEpub () {
      // 生成Ebook对象
      this.book = new Epub(DOWNLOAD_URL)
      console.log('book', this.book)
      // 生成Rendition
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过Rendition.display方法渲染电子书
      this.rendition.display()
      this.themes = this.rendition.themes
      this.setFontSize(this.defaultFontSize)
      this.registerTheme()
      this.setTheme(this.defaultTheme)
      // 获取locations对象
      //通过epubjs钩子函数实现
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        return this.book.locations.generate()
      }).then(result => {
        this.locations = this.book.locations
        this.bookAvailable = true

        // this.onProgressChange(100)
      })
    },
    prevPage () {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    nextPage () {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    toggleVisible () {
      this.visible = !this.visible
      if (!this.visible) {
        this.$refs.menuBar.hideSetting()
      }
    },
    hideTitleAndMenu () {
      this.visible = false
      this.$refs.menuBar.hideSetting()
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    registerTheme () {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    setTheme (index) {
      console.log('this.themeList[0].name',this.themeList[0].name)
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    onProgressChange (progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    // 根据链接跳转至指定位置
    jumpTo (href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
  },
  mounted () {
    this.showEpub()
  }
}
</script>
<style lang='scss' scoped>
@import '/assets/styles/global';
.ebook{
  position: relative;
  .read-wrapper{
    .mask{
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 100;
      display: flex;

      .left{
        flex: 0 0 px2rem(100);
      }
      .right{
        flex: 0 0 px2rem(100);
      }
      .center{
        flex: 1;
      }
    }
  }
}
</style>
