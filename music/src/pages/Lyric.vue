<template>
  <div class="song-lyric">
    <h1 class="lyric-title">歌词</h1>
    <transition-group name="lyric-fade">
      <!--有歌词-->
      <ul :style="{top:lrcTop}" class="has-lyric" v-if="lyr.length" key="has-lyric">
        <li v-for="(item, index) in lyr" v-bind:key="index">
          {{ item[1] }}
        </li>
      </ul>
      <!--没歌词-->
      <div v-else class="no-lyric" key="no-lyric">
        <span>暂无歌词</span>
      </div>
    </transition-group>
    <comment :playId="id" :type="0"></comment>
  </div>
</template>

<script>
import {mixin} from '../mixins'
import { mapGetters } from 'vuex'
import Comment from '../components/Comment'

export default {
  name: 'lyric',
  components: {
    Comment
  },
  mixins: [mixin],
  data () {
    return {
      lrcTop: '200px', // 歌词滑动
      showLrc: false, // 切换唱片和歌词
      lyr: [], // 当前歌曲的歌词
    }
  },
  computed: {
    ...mapGetters([
      'curTime',
      'id', // 歌曲ID
      'lyric', // 歌词
      'listOfSongs', // 存放的音乐
      'listIndex' // 当前歌曲在歌曲列表的位置
    ])
  },
  watch: {
    id: function () {
      this.lyr = this.parseLyric(this.listOfSongs[this.listIndex].lyric)
    },
    // 播放时间的开始和结束
    curTime: function () {
      // 处理歌词位置及颜色
      if (this.lyr.length !== 0) {
        for (let i = 0; i < this.lyr.length; i++) {
          if (this.curTime >= this.lyr[i][0]) {
            for (let j = 0; j < this.lyr.length; j++) {
              document.querySelectorAll('.has-lyric li')[j].style.color = '#000'
              document.querySelectorAll('.has-lyric li')[j].style.fontSize = '15px'
            }
            if (i >= 0) {
              document.querySelectorAll('.has-lyric li')[i].style.color = '#1abc9c'
              document.querySelectorAll('.has-lyric li')[i].style.fontSize = '25px'
              if(i > 5) {
                document.documentElement.scrollTop = 50*(i - 5)
              }
            }
          }
        }
      }
    }
  },
  created () {
    this.lyr = this.lyric
  },
}
</script>

<style scoped>
ul {
  list-style: none;
}
.song-lyric {
    margin: auto;
    margin-top: 90px;
    width: 700px;
    background-color: #fff;
    border-radius: 12px;
    padding: 0 20px 50px 20px;
    font-family: Lato, Helvetica Neue For Number, -apple-system, BlinkMacSystemFont,Segoe UI, Roboto, PingFang SC, Hiragino Sans GB, Microsoft YaHei,Helvetica Neue, Helvetica, Arial, sans-serif;
}

.lyric-title {
    text-align: center;
    height: 60px;
    line-height: 60px;
    border-bottom: 2px solid #2e3c50;
  }
.has-lyric {
    font-size: 18px;
    padding: 30px 0;
    width: 100%;
    min-height: 170px;
    text-align: center;
}
.has-lyric li {
    width: 100%;
    height: 40px;
    line-height: 40px;
}
.no-lyric {
    margin: 200px 0;
    width: 100%;
    text-align: center;
}
.no-lyric span {
    font-size: 18px;
    text-align: center;
}

.lyric-fade-enter,.lyric-fade-leave-to {
  transform: translateX(30px);
  opacity: 0;
}

.lyric-fade-enter-active,.lyric-fade-leave-active {
  transition: all 0.3s ease;
}
</style>
