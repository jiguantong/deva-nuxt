<template>
  <v-app id="inspire">
    <v-app-bar
      :class="$store.getters.getTransparentHeader ? 'transparent-header' : ''"
      app
      style="background-color: #24292e; max-height: 64px;  z-index: 999;"
      class="text_primary--text justify-center"
    >
      <v-container
        row
        align-center
        justify-space-between
        fluid
        style="margin: 0 auto;"
      >
        <v-btn
          icon
          to="/search/find"
          small
          color="text_primary"
          class="d-sm d-md-none d-lg-none"
          style="padding: 18px"
        >
          <v-icon>search</v-icon>
        </v-btn>
        <v-flex justify-start align-center row hidden-sm-and-down shrink>
          <v-toolbar-title>
            <logo type="simple" class="ml-5"></logo>
          </v-toolbar-title>
          <v-text-field
            ref="searchRef"
            v-model="keywords"
            :class="'aelevation-' + searchElv"
            @focusin="searchElv = 1"
            @focusout="searchElv = 0"
            @click:append="search"
            @keyup.enter.native="search"
            translate="yes"
            flat
            dark
            class="ml-5 min-input search-input black--text"
            label="搜索"
            hide-details
            elavation
            style="min-height: 36px;"
            append-icon="search"
            solo
          ></v-text-field>
        </v-flex>
        <v-flex class="my_nav ml-7">
          <v-btn active-class="nav-active" text color="text_primary" to="/"
            >浏览</v-btn
          >
          <v-btn
            text
            color="text_primary"
            to="/question/ask"
            class="hidden-sm-and-down"
            active-class="nav-active"
            >提问</v-btn
          >
          <!--<v-btn-->
          <!--  text-->
          <!--  color="text_primary"-->
          <!--  to="/blog/postBlog"-->
          <!--  class="hidden-sm-and-down"-->
          <!--  >写文</v-btn-->
          <!--&gt;-->
          <!--<v-btn text color="text_primary" class="hidden-sm-and-down"-->
          <!--  >空间</v-btn-->
          <!--&gt;-->
          <v-btn
            text
            color="text_primary"
            to="/tag/tags"
            class="hidden-sm-and-down"
            active-class="nav-active"
            >标签</v-btn
          >
          <v-btn
            text
            color="text_primary"
            to="/user/users"
            class="hidden-sm-and-down"
            active-class="nav-active"
            >用户</v-btn
          >
        </v-flex>
        <v-flex>
          <v-layout justify-end align-center>
            <v-btn
              v-if="$store.state.userInfo"
              :to="'/user/' + $store.getters.getUserId + '?tab=message'"
              style="position: relative;margin-right: 10px"
              icon
              exact
              small
            >
              <v-icon small color="text_primary">notifications_none</v-icon>
            </v-btn>
            <svg
              v-show="$store.getters.getUnReadMessageCount > 0"
              class="icon unread-icon"
              style="width: 13px; height: 13px;"
            >
              <use xlink:href="#icon-unread"></use>
            </svg>
            <div v-if="$store.getters.getUserInfo">
              <v-menu
                v-model="userMenu"
                :close-on-content-click="true"
                open-on-hover
                nudge-width="100"
                offset-y
              >
                <template #activator="{ on }">
                  <v-chip
                    :to="'/user/' + $store.getters.getUserInfo.userId"
                    max-width="120px"
                    text
                    small
                    color="primary"
                    style="border-radius: 0; background-color: transparent !important;"
                    class="d-inline-block no-flex text-truncate text-left mr-1 text_primary__text"
                    >{{
                      $store.getters.getUserInfo.nickname ||
                        $store.getters.getUserInfo.username
                    }}</v-chip
                  >
                  <v-avatar
                    v-on="on"
                    color="grey"
                    size="35"
                    style="cursor:pointer"
                  >
                    <v-img :src="$store.getters.getUserInfo.avatar"></v-img>
                  </v-avatar>
                </template>
                <v-card min-width="180px" class="pa-0">
                  <v-list dense nav class="pa-0" shaped>
                    <v-list-item class="pa-0 mb-0">
                      <v-list-item-content class="pa-0">
                        <v-btn
                          :to="'/user/' + $store.getters.getUserId"
                          depressed
                          exact
                          text
                          class="text-left no-flex"
                          >我的主页</v-btn
                        >
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item class="pa-0 mb-0">
                      <v-list-item-content class="pa-0">
                        <v-btn
                          :to="'/user/' + $store.getters.getUserId + '?tab=ask'"
                          depressed
                          exact
                          text
                          class="text-left no-flex"
                          >我的问题</v-btn
                        >
                      </v-list-item-content>
                    </v-list-item>
                    <v-list-item class="pa-0 mb-0">
                      <v-list-item-content class="pa-0">
                        <v-btn
                          :to="
                            '/user/' +
                              $store.getters.getUserId +
                              '?tab=collection'
                          "
                          depressed
                          exact
                          text
                          class="text-left no-flex"
                          >我的收藏</v-btn
                        >
                      </v-list-item-content>
                    </v-list-item>
                    <v-divider></v-divider>
                    <v-list-item class="pa-0 mb-0">
                      <v-list-item-content class="pa-0">
                        <v-btn @click="logout" depressed text height="40px"
                          >退出</v-btn
                        >
                      </v-list-item-content>
                    </v-list-item>
                  </v-list>
                </v-card>
              </v-menu>
            </div>
            <div v-else>
              <v-btn text color="text_primary" depressed to="/user/login"
                >登录</v-btn
              >
              <v-btn
                depressed
                color="text_primary"
                outlined
                class="ml-1"
                to="/user/signUp"
                >注册</v-btn
              >
            </div>
          </v-layout>
        </v-flex>
      </v-container>
    </v-app-bar>
    <v-layout id="content_bg" justify-center>
      <v-flex
        style="max-width: 1032px;width: 100%"
        class="pb-12 mb-5 mt-3 px-2"
        justify-center
      >
        <v-content>
          <!--参考 https://github.com/nuxt/nuxt.js/issues/1706 nuxt缓存-->
          <div v-if="needSsr">
            <nuxt v-if="needKeepAlive" class="pa-2" keep-alive />
            <nuxt v-else class="pa-2" />
          </div>
          <client-only v-else>
            <nuxt v-if="needKeepAlive" class="pa-2" keep-alive />
            <nuxt v-else class="pa-2" />
          </client-only>
          <float-post></float-post>
          <FloatMenu></FloatMenu>
        </v-content>
      </v-flex>
    </v-layout>
    <v-footer
      v-show="$store.getters.getShowFooter"
      app
      tile
      style="z-index: 999;background-color: #FFFFFF"
    >
      <v-row justify="center" align="center" no-gutters>
        <v-flex class="py-2 text-center" style="color: #888; font-size: 13px">
          <span
            >Copyright&copy;2019-{{
              new Date().getFullYear()
            }}&nbsp;&nbsp;<router-link to="/">deva.wiki</router-link
            >&nbsp;&nbsp;<router-link to="/">aid.dev</router-link></span
          >
          <div>
            <a
              target="_blank"
              class="hover-line"
              style="color: #888"
              href="http://beian.miit.gov.cn"
            >
              沪ICP备19037749号-1 </a
            >&nbsp;
            <a
              target="_blank"
              class="hover-line"
              style="color: #888"
              href="http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=31011402008161"
            >
              沪公网安备 31011402008161号 </a
            >&nbsp;上海乔和科技有限公司版权所有
          </div>
        </v-flex>
      </v-row>
    </v-footer>
    <ErrorDialog
      :dialog="errorInfo.dialog"
      @update:dialog="errorInfo.dialog = $event"
    >
    </ErrorDialog>
    <!--<v-dialog v-model="joinSpace.dialog" width="500px" persistent>-->
    <!--  <v-card>-->
    <!--    <v-card-text class="pb-0">-->
    <!--      <v-container class="pb-0">-->
    <!--        <v-form ref="joinSpaceForm">-->
    <!--          <v-text-field-->
    <!--            v-model="joinSpace.spaceId"-->
    <!--            placeholder="空间ID"-->
    <!--            :rules="[rules.requireSpaceId]"-->
    <!--          >-->
    <!--          </v-text-field>-->
    <!--          <v-text-field-->
    <!--            v-model="joinSpace.inviteCode"-->
    <!--            placeholder="邀请码"-->
    <!--            :rules="[rules.requireInviteCode]"-->
    <!--          >-->
    <!--          </v-text-field>-->
    <!--          <span class="error&#45;&#45;text">{{ joinSpace.errMsg }}</span>-->
    <!--        </v-form>-->
    <!--      </v-container>-->
    <!--    </v-card-text>-->
    <!--    <v-card-actions>-->
    <!--      <div class="flex-grow-1"></div>-->
    <!--      <v-btn-->
    <!--        color="sub"-->
    <!--        text-->
    <!--        small-->
    <!--        @click="-->
    <!--          joinSpace.dialog = false-->
    <!--          joinSpace.errMsg = null-->
    <!--        "-->
    <!--        >关闭</v-btn-->
    <!--      >-->
    <!--      <v-btn color="primary" small text @click="joinToSpace">加入</v-btn>-->
    <!--    </v-card-actions>-->
    <!--  </v-card>-->
    <!--</v-dialog>-->

    <v-dialog v-model="viewNotice.dialog" persistent max-width="40vw">
      <v-card>
        <v-card-title>
          <!--<span class="headline">回复内容</span>-->
        </v-card-title>
        <v-card-text class="pb-0">
          <v-textarea
            :value="systemNotice.content"
            readonly
            rows="10"
          ></v-textarea>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn @click="viewNotice.dialog = false" text>关闭 </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <edit-phone></edit-phone>
  </v-app>
</template>

<script>
import Logo from '../components/Logo'
// https://github.com/nuxt/nuxt.js/issues/319
import config from '../nuxt.config.js'
import ErrorDialog from '../components/dialog/ErrorDialog'
import FloatMenu from '../components/float/FloatMenu'
import EditPhone from '../components/dialog/EditPhone'
import FloatPost from '../components/float/FloatPost'
export default {
  name: 'App',
  components: {
    EditPhone,
    FloatMenu,
    Logo,
    ErrorDialog,
    FloatPost
  },
  data: () => ({
    searchElv: 0,
    drawer: null,
    menuOpen: false,
    keepAliveRouters: [
      'question-ask',
      'blog-postBlog',
      'user-signUp',
      'user-login',
      'question-id',
      'blog-id',
      'index'
    ],
    ssrRouters: ['question-id', 'blog-id'],
    keywords: null,
    // spaceList: [
    //   {
    //     spaceId: 1,
    //     spaceName: '我的空间',
    //     children: []
    //   }
    // ],
    viewNotice: {
      dialog: false
    },
    userMenu: false,
    joinSpace: {
      dialog: false,
      resp: null,
      spaceId: null,
      inviteCode: null,
      errMsg: null
    },
    errorInfo: {
      dialog: false,
      msg: ''
    },
    systemNotice: {
      content: null
    },
    // rules: {
    //   requireSpaceId: (v) => (v && v.length >= 1) || '请输入空间ID',
    //   requireInviteCode: (v) => (v && v.length >= 1) || '请输入邀请码'
    // },
    currentNoticeInterval: null
  }),
  computed: {
    needKeepAlive() {
      return this.keepAliveRouters.includes(this.$route.name)
    },
    needSsr() {
      return this.ssrRouters.includes(this.$route.name)
    },
    // 监听vuex https://stackoverflow.com/questions/43270159/vuejs-2-how-to-watch-store-values-from-vuex
    needReloadSpaceList() {
      return this.$store.getters.getReloadSpaceFlag
    },
    needAlertError() {
      return this.$store.getters.getAlertErrorFlag
    },
    toggleUser() {
      return this.$store.getters.getUserId
    },
    isSmall() {
      return (
        this.$vuetify.breakpoint.name === 'sm' ||
        this.$vuetify.breakpoint.name === 'xs'
      )
    }
  },
  watch: {
    // 解决点进详情滚动条位置不变
    $route() {
      document.body.scrollTop = 0
      document.documentElement.scrollTop = 0
    },
    // 监听vuex https://stackoverflow.com/questions/43270159/vuejs-2-how-to-watch-store-values-from-vuex
    needReloadSpaceList() {
      this.loadSpaceList()
    },
    needAlertError(v) {
      if (v) {
        this.errorInfo.dialog = true
      }
    },
    toggleUser() {
      // 加载空间列表
      // this.loadSpaceList()
      // 加载未读消息数
      this.loadMessageCount()
      // 连接websocket
      this.connectWebsocket()
    }
  },
  created() {
    this.getSystemNotice()
  },
  mounted() {
    window.addEventListener('beforeunload', (e) => {
      this.disconnectWebsocket()
    })
    this.checkWebpFeature()
    // 请求桌面通知权限
    this.requestNotifyPermission()
    this.connectWebsocket()
    // 加载空间列表
    // this.loadSpaceList()
    // 加载未读消息数
    this.loadMessageCount()
  },
  methods: {
    checkWebpFeature() {
      const kTestImages = {
        lossy: 'UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA',
        lossless: 'UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==',
        alpha:
          'UklGRkoAAABXRUJQVlA4WAoAAAAQAAAAAAAAAAAAQUxQSAwAAAARBxAR/Q9ERP8DAABWUDggGAAAABQBAJ0BKgEAAQAAAP4AAA3AAP7mtQAAAA==',
        animation:
          'UklGRlIAAABXRUJQVlA4WAoAAAASAAAAAAAAAAAAQU5JTQYAAAD/////AABBTk1GJgAAAAAAAAAAAAAAAAAAAGQAAABWUDhMDQAAAC8AAAAQBxAREYiI/gcA'
      }
      const img = new Image()
      const _this = this
      img.onload = function() {
        const result = img.width > 0 && img.height > 0
        if (result) {
          _this.$store.commit('supportWebp')
        }
      }
      img.onerror = function() {}
      img.src = 'data:image/webp;base64,' + kTestImages.lossy
    },
    getSystemNotice() {
      if (!process.client) {
        return
      }
      const _this = this
      _this.$axios.$post('/systemNotice/getLast').then((resp) => {
        if (resp.succeed) {
          _this.systemNotice = resp.data
        }
      })
    },
    logout() {
      // 使外部api上的JWT Cookie失效
      this.disconnectWebsocket()
      this.$store.commit('setUserInfo', null)
      this.userMenu = false
      this.$router.push({
        path: '/user/login'
      })
    },
    search() {
      if (this.keywords) {
        this.$router.push({
          path: '/search/' + this.keywords
        })
      }
      // this.$refs.searchRef.blur()
    },
    loadSpaceList() {
      if (this.$store.state.userInfo) {
        this.$axios.$post('/spaceInfo/listSpace').then((resp) => {
          if (resp.succeed) {
            this.spaceList[0].children = resp.data
          }
        })
      } else {
        this.spaceList[0].children = []
      }
    },
    loadMessageCount() {
      if (this.$store.state.userInfo) {
        this.$axios.$post('/messageInfo/getUnreadCount').then((resp) => {
          if (resp.succeed) {
            this.$store.commit('setUnreadMessageCount', resp.data)
          }
        })
      }
    },
    joinToSpace() {
      if (!this.$refs.joinSpaceForm.validate()) {
        return false
      }
      this.$axios
        .$post('/spaceUser/joinSpace', {
          spaceId: this.joinSpace.spaceId,
          inviteCode: this.joinSpace.inviteCode
        })
        .then((resp) => {
          this.joinSpace.resp = resp
          if (resp.succeed) {
            this.joinSpace.dialog = false
            this.loadSpaceList()
            this.$router.push('/space/' + this.joinSpace.spaceId, () => ({}))
          } else {
            this.joinSpace.errMsg = resp.msg
          }
        })
    },
    connectWebsocket() {
      if (this.$store.getters.getUserId && process.client) {
        this.disconnectWebsocket()
        this.$connect(config.websocket.server + this.$store.getters.getUserId)
        this.listenSocket()
      }
    },
    listenSocket() {
      if (this.$store.getters.getUserId && process.client) {
        // 监听前删除已有监听器
        delete this.$options.sockets.onmessage
        this.$options.sockets.onmessage = (data) => {
          const _msg = JSON.parse(data.data)
          const _msgTitle = this.$options.filters.filterHtml(_msg.title)
          this.showInfoMsg({
            message: _msgTitle
          })
          this.desktopNotify('DEVA', {
            body: _msgTitle,
            icon: config.domain + '/icon.png',
            data: {
              ownQuestionId: _msg.ownQuestionId,
              anchor: _msg.anchor ? _msg.anchor : ''
            }
          })
          this.$store.commit('setUnreadMessageCount', 1)
        }
      }
    },
    disconnectWebsocket() {
      if (process.client) {
        this.$disconnect()
        delete this.$options.sockets.onmessage
      }
    },
    desktopNotify(title, options) {
      if (process.server) {
        return
      }
      let notice
      // 先检查浏览器是否支持
      if (!window.Notification) {
      } else {
        // 检查用户曾经是否同意接受通知
        if (Notification.permission === 'granted') {
          notice = new Notification(title, options) // 显示通知
        } else if (Notification.permission === 'default') {
          // 用户还未选择，可以询问用户是否同意发送通知
          Notification.requestPermission().then((permission) => {
            if (permission === 'granted') {
              notice = new Notification(title, options) // 显示通知
            } else if (permission === 'default') {
            } else {
              // denied
            }
          })
        } else {
          // denied 用户拒绝
        }
        if (notice) {
          notice.onclick = function(e) {
            const _link =
              config.domain +
              '/question/' +
              options.data.ownQuestionId +
              options.data.anchor
            window.open(_link, '_blank')
            notice.close()
          }
        }
      }
    },
    requestNotifyPermission() {
      if (Notification.permission === 'default') {
        // 用户还未选择，可以询问用户是否同意发送通知
        Notification.requestPermission().then((permission) => {
          if (permission === 'granted') {
          } else if (permission === 'default') {
          } else {
          }
        })
      }
    }
  },
  notifications: {
    showSuccessMsg: {
      type: 'success',
      title: 'Hello there',
      message: "That's the success!"
    },
    showInfoMsg: {
      type: 'info',
      title: 'Hey you',
      message: 'Here is some info for you'
    },
    showWarnMsg: {
      type: 'warn',
      title: 'Wow, man',
      message: "That's the kind of warning"
    },
    showErrorMsg: {
      type: 'error',
      title: 'Wow-wow',
      message: "That's the error"
    }
  }
}
</script>
<style>
.search-input.aelevation-1 .v-input__control .v-input__slot {
  background-color: white !important;
}
.search-input.aelevation-1 .v-input__control .v-input__slot .v-label {
  color: rgba(0, 0, 0, 0.54) !important;
}
.search-input.aelevation-1 .v-input__control .v-input__slot .v-icon,
.search-input.aelevation-1 .v-input__control .v-input__slot input {
  color: black !important;
  caret-color: black !important;
}
.theme--light .search-input .v-input__slot {
  background: #3f4448 !important;
}
.theme--dark .search-input .v-input__slot {
  background: #4b4b4b !important;
}
.v-list-item__action {
  position: relative;
  top: 1px;
  margin-right: 20px;
}
.v-treeview-node--leaf > .v-treeview-node__root {
  padding-left: 7px;
  height: 26px;
  min-height: 26px;
}

.my_nav .v-btn__content {
}
#content_bg {
  /*background: url('/svg/star-bg.svg') center;*/
  background-size: cover;
}
/*.transparent-header {*/
/*  background-color: #454545c2 !important;*/
/*  max-height: 54px !important;*/
/*  transition: all 200ms linear;*/
/*}*/
/*.transparent-header .v-toolbar__content {*/
/*  height: 54px !important;*/
/*  border-bottom: none !important;*/
/*  transition: all 200ms linear;*/
/*}*/
/*.transparent-header .v-input__control {*/
/*  opacity: 0.7 !important;*/
/*}*/
.v-app-bar:not(.transparent-header) {
  max-height: 64px !important;
  transition: all 100ms linear;
}
.v-app-bar:not(.transparent-header) .v-toolbar__content {
  height: 64px !important;
  transition: all 100ms linear;
}
</style>
<style scoped>
header.float {
  box-shadow: none;
}
.unread-icon {
  width: 13px;
  position: relative;
  top: -8px;
  right: 22px;
}
.mdi-cards-outline {
  font-size: 16px !important;
}
.nav-active::before {
  opacity: 0;
}
.nav-active {
  border-bottom: #f5f5f5f0 2px solid !important;
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
}
.my_nav .v-btn:not(.nav-active) {
  border-bottom: transparent 2px solid !important;
}
</style>
