<template>
  <div class="wrap">
    <div class="header-bar">
      <router-link to="/drama">
        <i class="fa fa-arrow-left"></i>
      </router-link>
      <span>ac{{season_id}}</span>
      <i class="fa fa-ellipsis-v"></i>
    </div>
    <div>
      <video controls='controls' :src="videoSrc" class="video"></video>
    </div>
    <div class="play-desc">
      <ul>
        <li class="info" @click="toggleInfo">简介</li>
        <li class="comment" @click="toggleComment">评论({{comment.length}})</li>
      </ul>
    </div>
    <div class="content">
      <div class="show-info" v-show="isInfoShow">
        <h4 class="title">{{dramaTitle}}</h4>
        <p class="desc">【简介】</p>
        <p class="about">【标签相关】</p>
        <div class="author">
          <img src="https://ooo.0o0.ooo/2017/06/30/595605552345a.png" alt="">
          <h4>UP主:</h4>
          <p class="author-name">tancky</p>
          <div class="follow" :style="follow" @click="following">
            <span>{{message}}</span>
          </div>
        </div>
      </div>
      <div class="show-comment" v-show="isCommentShow">
        <ul class="cmt-list">
          <li v-for="(cmt, index) in comment">
            <img :src="cmt.member.avatar" alt="" class="avatar">
            <span class="name">{{cmt.member.uname}}</span>
            <span class="ico" >
              <i class="fa fa-thumbs-o-up"></i>{{cmt.like}}
            </span>
            <p class="comment-msg">{{cmt.content.message}}</p>
          </li>
        </ul>
        <div class="talk">
          <img src="../assets/img/emoji.png" alt="" class="emoji">
          <input type="text" v-model="cmtContent" @keypress.enter="submitCmt" placeholder="说点什么把">
          <img src="../assets/img/send.png" alt="" class="send" @click="submitCmt">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        isInfoShow: true,
        isCommentShow: false,
        season_id: this.$route.params.season_id,
        videoSrc: '',
        comment: '',
        cmtContent: '',
        info: '',
        message: '+ 关 注',
        follow: {
          color: '#fff',
          backgroundColor: '#fd4c5d'
        }
      }
    },
    computed: {
      dramaList() {
        return this.$store.state.dramaList
      },
      dramaTitle () {
        return this.$store.state.dramaTitle
      },

    },
    created() {
      let season_id =  this.$route.params.season_id
      this.axios.get('https://api.imjad.cn/bilibili/?get=comments&season_id=' + season_id)
        .then((res) => {
//          console.log(res.data.data.replies)
          this.comment = res.data.data.replies;
        })
        .catch((error) => {
          console.log(error)
        })
    },
    mounted() {
      let season_id =  this.$route.params.season_id
      this.axios.post('https://api.imjad.cn/bilibili/?season_id=' + season_id + '&page=1&quality=2&index=1').then((res) => {
        this.videoSrc = res.data.durl[0].url
      }).catch((error) => {
        console.log(error)
      })
      this.axios.post('https://api.imjad.cn/bilibili/?get=comments&season_id=' + season_id ).then((res) => {
        this.comment = res.data.data.replies
      }).catch((error) => {
        console.log(error)
      })
    },
    methods: {
      toggleInfo() {
        this.isInfoShow = true;
        this.isCommentShow = false;
      },
      toggleComment() {
        this.isInfoShow = false;
        this.isCommentShow = true;
      },
      submitCmt() {
        let content =this.cmtContent;
        if(content === '') {
          this.util.cmtPop()
        }else {
          this.comment.unshift({
            like: 0,
            member: {
              uname: "tancky",
              avatar: "https://ooo.0o0.ooo/2017/06/30/595605552345a.png",
            },
            content: {
              message: `${content}`,
              plat: 1,
              device: "",
              members: []
            },
            replies: []
          })
        }
        this.cmtContent = ''
      },
      following() {
        this.message= '已关注',
          this.follow= {
            color: '#000',
            backgroundColor: '#fff',
            border: '1px solid #999',
            transition: 'all .2s'
          },
          this.util.following();
      }
    }
  }
</script>

<style lang="less" scoped>
  @import '../assets/css/recommendPlay.less';
</style>
