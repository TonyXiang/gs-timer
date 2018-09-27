<template>
  <div class="hello">
    <div class="time-outer">
      <h1 class="time">{{ showTime }}</h1>
      <box gap="10px 10px">
        <x-button type="primary" @click.native="clickBtn">{{btnStr}}</x-button>
      </box>
    </div>
    <div class="vux-1px-b" id="list">
      <div class="history">历史记录</div>
      <swipeout>
        <swipeout-item v-for="(item, index) in list" :key="item.gsTime.toString()" @on-close="handleEvents('on-close')"
                       @on-open="handleEvents('on-open')" transition-mode="follow">
          <div slot="right-menu">
            <swipeout-button @click.native="onButtonClick('fav')" type="primary">取消</swipeout-button>
            <swipeout-button @click.native="onButtonClick('delete', index)" type="warn">删除</swipeout-button>
          </div>
          <div slot="content" class="demo-content vux-1px-t">
            <div class="item">{{'时间:' + new Date(item.gsTime).Format('MM-dd hh:mm')}}</div>
            <div class="item">{{'时长:' + item.time.substring(0,2) + '分' + item.time.substring(3,5) + '秒'}}</div>
            <div class="item">{{'间隔:'}} {{item.gsTime | getJiange(list, index)}}</div>
          </div>
        </swipeout-item>
      </swipeout>
    </div>

  </div>
</template>

<script>
  import Vue from 'vue'
  import { Swipeout, SwipeoutItem, SwipeoutButton, XButton, Box } from 'vux'

  Vue.filter('getJiange', (val, list, index) => {
    if (!list[index + 1]) {
      return '--'
    } else {
      const d1 = new Date(val)
      const d2 = new Date(list[index + 1].gsTime)
      const ms = d1 - d2

      let m = parseInt(ms / (1000 * 60))
      let s = parseInt(ms % (1000 * 60) / 1000)

      if (m.toString().length === 1) {
        m = '0' + m
      }
      if (s.toString().length === 1) {
        s = '0' + s
      }
      return m + '分' + s + '秒'
    }
  })

  export default {
    name: 'hello',
    components: {
      Swipeout,
      SwipeoutItem,
      SwipeoutButton,
      XButton,
      Box
    },
    data() {
      return {
        msg: 'Welcome to Your Vue.js App',
        startTime: null,
        nowTime: null,
        showTime: '00:00:00',
        run: null,
        list: [],
        disabled: null,
        btnStr: '开始'
      }
    },
    created() {
      this.list = this.data('history') || []
      Date.prototype.Format = function(fmt) {
        if (!fmt) {
          fmt = 'yyyy-MM-dd hh:mm:ss'
        }
        var o = {
          'M+': this.getMonth() + 1, // 月份
          'd+': this.getDate(), // 日
          'h+': this.getHours(), // 小时
          'm+': this.getMinutes(), // 分
          's+': this.getSeconds(), // 秒
          'q+': Math.floor((this.getMonth() + 3) / 3), // 季度
          'S': this.getMilliseconds() // 毫秒
        }
        if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (this.getFullYear() + '').substr(4 - RegExp.$1.length))
        for (var k in o) {
          if (new RegExp('(' + k + ')').test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (('00' + o[k]).substr(('' + o[k]).length)))
        }
        return fmt
      }
    },
    methods: {
      $t(name) {
        return name
      },
      onButtonClick(type, index) {
        if (type === 'delete') {
          this.list.splice(index, 1)
          this.data('history', this.list)
        }
      },
      handleEvents(type) {
        //                console.log('event: ', type)
      },
      clickBtn() {
        if (!this.nowTime) {
          this.startTime = new Date()
          this.nowTime = this.startTime
          this.run = setInterval(() => {
            this.nowTime = new Date()
            const ms = this.nowTime - this.startTime
            let m = parseInt(ms / (1000 * 60))
            let s = parseInt(ms % (1000 * 60) / 1000)
            let d = parseInt(ms % (1000 * 60) % 1000 / 10)

            if (m.toString().length === 1) {
              m = '0' + m
            }
            if (s.toString().length === 1) {
              s = '0' + s.toString()
            }
            if (d.toString().length === 1) {
              d = '0' + d.toString()
            }
            this.showTime = m + ':' +
              s + ':' +
              d
          }, 10)
          this.btnStr = '结束'
        } else {
          clearInterval(this.run)
          this.nowTime = null
          this.save()
          this.btnStr = '开始'
        }
      },
      save() {
        this.list.unshift({
          gsTime: this.startTime,
          time: this.showTime
        })
        this.data('history', this.list)
      },
      data(name, value) {
        if (name === null && name === undefined) {
          return
        }
        if (value !== undefined) {
          localStorage.setItem(name, JSON.stringify(value))
        } else {
          return JSON.parse(localStorage.getItem(name))
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .time-outer {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 100;
    background: #fff;
    width: 100%;
  }

  .time {
    text-align: center;
    padding: 30px 0 10px 0;
    font-size: 50px;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }

  .demo-content {
    font-size: 12px;
    /*text-align: center;*/
    padding: 10px 10px;
    display: flex;
    justify-content: space-between;

  .item {
    flex: 1;
  }

  }

  .history {
    margin-top: 182px;
    padding-left: 10px;
  }
</style>
