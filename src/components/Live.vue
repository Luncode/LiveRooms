<template>
  <div class="hello">
      <video id="videoElement"  class="flvplayer-app" controls autoplay muted></video>
      <!-- <div class="control">
          <button @click="play">
          播放
        </button>
        <button @click="pause">
            暂停
        </button>
        <button @click="fullscreen">
            全屏
        </button>
      </div> -->
      <div class="wsschat">
        <h1>Wss Chat</h1>
        <div id="login" class="login">
          <input v-model="username" type="text" placeholder="输入用户名">
          <button @click="login">登录</button>
        </div>
        <div id="chat" class="req">
          <input v-model="text_chat" type="text" placeholder="请输入内容">
          <button @click="send">发送</button>
        </div>
        收到的chat
        <div id="msg" class="rep">
        </div>
        <div>By：Luncode</div>
      <div style="font-size:20px">使用开源:Flv.js</div>
      </div>
  </div>
</template>
<script>
import flvplayer from 'flv.js'
export default {
  name: 'Live',
  data () {
    return {
      flvPlayer: null,
      text_chat: '',
      path: 'ws://172.20.217.222:233',
      username: '',
      msg_rec: null
    }
  },
  mounted () {
    this.msg_rec = document.getElementById('msg')
    if (flvplayer.isSupported()) {
      var videoElement = document.getElementById('videoElement')
      this.flvPlayer = flvplayer.createPlayer({
        url: 'http://172.20.217.222:8000/live/Luncode.flv',
        type: 'flv'
      })
      this.flvPlayer.attachMediaElement(videoElement)
      this.flvPlayer.load()
      this.flvPlayer.play()
    }
    this.init()
  },
  components: {
    flvplayer
  },
  methods: {
    play () {
      this.flvPlayer.play()
    },
    pause () {
      this.flvPlayer.pause()
    },
    send () {
      // alert(this.text_chat)
      var time = new Date()
      var data = []
      // eslint-disable-next-line no-new-object
      var json = new Object()
      json.username = this.username
      json.msg = this.text_chat
      json.date = time
      data.push(json)
      this.socket.send(JSON.stringify(data))
    },
    login () {
      this.socket.send(this.username)
      var login = document.getElementById('login')
      login.setAttribute('style', 'display:none')
      var chat = document.getElementById('chat')
      chat.setAttribute('style', 'display:block')
      // alert(this.username)
    },
    init: function () {
      if (typeof (WebSocket) === 'undefined') {
        alert('您的浏览器不支持socket')
      } else {
        // 实例化socket
        this.socket = new WebSocket(this.path)
        // 监听socket连接
        this.socket.onopen = this.open
        // 监听socket错误信息
        this.socket.onerror = this.error
        // 监听socket消息
        this.socket.onmessage = this.getMessage
      }
    },
    open: function () {
      console.log('socket连接成功')
    },
    error: function () {
      console.log('连接错误')
    },
    getMessage: function (msg) {
      // console.log(msg.data)
      // eslint-disable-next-line no-redeclare
      var temp = JSON.parse(msg.data)
      var p = document.createElement('p')
      p.innerHTML = '<p>' + temp[0].username + ':' + temp[0].msg + '</p>'
      this.msg_rec.appendChild(p)
      this.text_chat = ''
    },
    // send: function () {
    //   this.socket.send(params)
    // },
    close: function () {
      console.log('socket已经关闭')
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
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
#videoElement{
  display: block;
  margin-left: 0;
}
.wsschat{
  display: block;
  position: absolute;
  right:5em;
  top:0;
}
.control{
  position: relative;
  margin-top: -20px;
  z-index: 1;
}
.rep{
  height: 40em;
  scroll-behavior: unset;
}
.req{
  display:none
}
</style>
