<template>
  <v-app v-show="userList">
    <v-layout style="width: 60vw" shrink justify-space-around>
      <v-text-field
        v-model="searchKey"
        @click:append="loadUserList"
        @keyup.enter.native="loadUserList"
        style="width: 30vw"
        hide-details
        placeholder="用户名/昵称"
        append-icon="search"
        rounded
      ></v-text-field>
      <v-spacer></v-spacer>
      <SpaceMenu v-if="spaceInfo" :space-info="spaceInfo"></SpaceMenu>
    </v-layout>
    <v-divider></v-divider>
    <v-layout wrap shrink>
      <v-flex
        v-for="userInfo in userList"
        :key="userInfo.userId"
        xs4
        md4
        lg3
        style="max-height: 75px; max-width: 358px"
        class="ma-4 mb-9"
      >
        <UserCard
          :user-info="userInfo"
          :action-event="removeFromSpace"
          :action-title="
            '将用户从 ' + (spaceInfo && spaceInfo.spaceName) + ' 移除'
          "
          :action-confirm="true"
          action-icon="mdi-close"
          confirm-msg="确定将该成员从空间中移除吗?"
        ></UserCard>
      </v-flex>
    </v-layout>
    <InfoDialog
      :msg="[
        '移除成功',
        (removeResult.resp && removeResult.resp.msg) ||
          '移除失败, 用户可能已被移除'
      ]"
      :succeed="removeResult.resp != null && removeResult.resp.succeed"
      :dialog="removeResult.dialog"
      @update:dialog="removeResult.dialog = $event"
    >
    </InfoDialog>
  </v-app>
</template>
<script>
import UserCard from '../../components/userCard/UserCard'
import InfoDialog from '../../components/dialog/InfoDialog'
import SpaceMenu from '../../components/space/SpaceMenu'
export default {
  components: { SpaceMenu, UserCard, InfoDialog },
  data: () => ({
    userList: null,
    searchKey: null,
    spaceInfo: null,
    removeResult: {
      resp: null,
      dialog: false,
      loading: false
    },
    page: {
      current: 1,
      size: 100
    },
    loadMore: {
      isLoading: false,
      noMore: false
    }
  }),
  created() {
    this.loadUserList()
    this.loadSpaceInfo()
  },
  mounted() {
    this.scroll()
  },
  methods: {
    scroll() {
      window.onscroll = () => {
        if (!/\/space\/userRemove/.test(this.$route.path)) {
          return false
        }
        // 距离底部200px时加载一次
        const bottomOfWindow =
          document.documentElement.offsetHeight -
            document.documentElement.scrollTop -
            window.innerHeight <=
          200
        if (
          bottomOfWindow &&
          !this.loadMore.isLoading &&
          !this.loadMore.noMore
        ) {
          this.loadMore.isLoading = true
          this.$axios
            .$post('/spaceUser/getSpaceMembers', {
              current: ++this.page.current,
              size: this.page.size,
              searchKey: this.searchKey,
              spaceId: this.$route.query.spaceId
            })
            .then((resp) => {
              this.loadMore.isLoading = false
              if (resp.succeed) {
                this.userList = this.userList.concat(resp.data.records)
              } else {
                this.loadMore.noMore = true
              }
            })
            .catch((e) => {
              this.loadMore.isLoading = false
            })
        }
      }
    },
    loadUserList() {
      this.$axios
        .$post('/spaceUser/getSpaceMembers', {
          current: this.page.current,
          size: this.page.size,
          searchKey: this.searchKey,
          spaceId: this.$route.query.spaceId
        })
        .then((resp) => {
          if (resp.succeed) {
            this.userList = resp.data.records
          } else {
            this.userList = []
          }
        })
    },
    loadSpaceInfo() {
      this.$axios
        .$post('/spaceInfo/getSpaceInfo', {
          spaceId: this.$route.query.spaceId
        })
        .then((resp) => {
          this.spaceInfo = resp.data
        })
    },
    removeFromSpace(_userId) {
      this.removeResult.loading = true
      this.$axios
        .$post('/spaceUser/removeUser', {
          spaceId: this.$route.query.spaceId,
          removeUserId: _userId
        })
        .then((resp) => {
          this.removeResult.resp = resp
          this.removeResult.loading = false
          this.removeResult.dialog = true
          if (resp.succeed) {
            this.loadUserList()
          }
        })
        .catch((e) => {
          this.removeResult.loading = false
        })
    }
  }
}
</script>
