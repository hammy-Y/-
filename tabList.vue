<template>
  <div class="tabList">
    <div class="tabList-nav">
      <slot name="tabList-nav-tab" />
      <div v-for="(item,key) in tabList" :key="item.id" :class="[{ 'tabList-nav-item-current': key===index }, 'tabList-nav-item']" @click="tabClick(key)">
        <div class="tabList-nav-item-con">
          <i :class="[{ 'tabList-nav-icon-current': key===index },'tabList-nav-icon']" />
          <slot :data="item" name="nav-item" />
        </div>
      </div>
    </div>
    <div class="tabList-list">
      <div v-for="item in tabList" :ref="'view'+item.id" :key="item.id" class="tabList-list-item">
        <slot :data="item" name="list-item" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    tabList: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      index: 0,
      clientHeight: document.body.clientHeight
    }
  },
  mounted() {
    // 监听滚动事件
    document.querySelector('.classObj').addEventListener('scroll', this.handleScroll)
  },
  destroyed() {
    // 离开该页面需要移除这个监听的事件
    document.querySelector('.classObj').removeEventListener('scroll', this.handleScroll)
  },
  methods: {
    // 获取当前元素的offsetTop
    getOffsetTop(obj) {
      // console.log(obj)
      let offsetTop = 0
      while (obj !== window.document.body && obj != null) {
        offsetTop += obj.offsetTop
        obj = obj.offsetParent
      }
      return offsetTop
    },
    // 锚点点击事件
    tabClick(index) {
      // 获取当前选中的index以便后面滚动高亮
      this.index = index
      // 获取当前选中元素的top值（给元素绑定对应的ref值）
      const offsetTop = this.getOffsetTop(this.$refs[`view${this.tabList[index].id}`][0])
      const scrollTop = offsetTop - this.clientHeight / 2
      document.querySelector('.classObj').scrollTo({
        top: scrollTop + 1,
        behavior: 'smooth'
      })
    },
    /** 滚动事件 */
    // 页面滚动要做的事情
    handleScroll() {
      const scrollTop = document.querySelector('.classObj').scrollTop
      /**
    * scrollTop 页面的滚动条的高度
    * offsetTop 当前选中的tab元素的offsetTop
    * offsetHeight 当前选中元素的高度
    * actuaTop 浏览器屏幕中间的高度
    */
      const offsetTop = this.getOffsetTop(this.$refs[`view${this.tabList[this.index].id}`][0])
      const offsetHeight = this.$refs[`view${this.tabList[this.index].id}`][0].offsetHeight
      const actuaTop = scrollTop + this.clientHeight / 2
      const scrollHeight = document.querySelector('.classObj').scrollHeight
      const length = this.tabList.length
      /**
    * 页面滚动中根据相应位置变换选中tab
    */
      console.log(scrollTop)
      if (actuaTop < offsetTop && this.index > 0) {
        this.index = this.index - 1
        console.log(this.index)
      } else if (this.index < length - 1 && actuaTop > offsetTop + offsetHeight) {
        this.index = this.index + 1
        console.log(this.index)
      } else if (this.index < length - 1 && actuaTop + this.clientHeight / 2 === scrollHeight) {
        this.index = length - 1
        console.log(this.index)
      } else if (scrollTop === 0 && this.index > 0) {
        this.index = 0
        console.log(this.index)
      }
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.tabList{
  width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: start;
  .tabList-nav{
    position: sticky;
    top: 0;
    background: #fff;
    width: 272px;
    .tabList-nav-item{
      position: relative;
      width: 272px;
      height: 76px;
      padding: 12px 30px;
      box-sizing: border-box;
      cursor: pointer;
      border-bottom: 1px solid #E3E8EE;
      display: flex;
      align-items: center;
      &:nth-last-child(){
        border: none;
      }
      .tabList-nav-item-con{
        font-size: 14px;
      }
      .tabList-nav-icon{
        width: 8px;
        height: 8px;
        background-color: #80848F;
        border-radius: 50%;
        display: inline-block;
        position: relative;
      }
      .tabList-nav-icon-current{
        width: 8px;
        height: 8px