<template>
  <v-app v-if="spaceInfo" justify-center align-start shrink>
    <v-layout column shrink>
      <v-card-title>空间管理</v-card-title>
      <v-divider></v-divider>
    </v-layout>
    <v-layout justify-space-around shrink class="mt-6">
      <v-flex hidden-sm-and-down md5>
        <v-card class="pa-8">
          <v-form ref="form">
            <v-layout justify-space-around column class="mt-3">
              <v-layout class="mt-4">
                <v-text-field
                  v-model="spaceInfo.spaceName"
                  :counter="20"
                  :rules="[rules.min, rules.max]"
                  label="空间名"
                  outlined
                  required
                ></v-text-field>
              </v-layout>
              <v-layout class="mt-4">
                <v-textarea
                  v-model="spaceInfo.description"
                  :counter="200"
                  :rules="[rules.max200]"
                  label="空间描述"
                  outlined
                  required
                  no-resize
                ></v-textarea>
              </v-layout>
              <v-layout justify-end class="mt-4">
                <v-btn
                  :loading="updateResult.loading"
                  @click="updateSpace"
                  outlined
                  accent
                  depressed
                  min-width="150px"
                  >更新空间信息</v-btn
                >
              </v-layout>
            </v-layout>
          </v-form>
        </v-card>
      </v-flex>
      <v-flex md4>
        <v-card class="pa-2">
          <v-toolbar dense flat class="mb-1">
            <span>空间详情</span>
            <v-row justify="end">
              <v-tooltip top>
                <template v-slot:activator="{ on }">
                  <v-btn
                    :to="'/space/userAdd?spaceId=' + spaceInfo.spaceId"
                    v-on="on"
                    icon
                    ><v-icon>mdi-account-plus-outline</v-icon></v-btn
                  >
                </template>
                <span>添加成员</span>
              </v-tooltip>
              <v-tooltip top>
                <template v-slot:activator="{ on }">
                  <v-btn
                    :to="'/space/userRemove?spaceId=' + spaceInfo.spaceId"
                    v-on="on"
                    icon
                    ><v-icon>mdi-account-minus-outline</v-icon></v-btn
                  >
                </template>
                <span>移除成员</span>
              </v-tooltip>
              <v-tooltip top>
                <template v-slot:activator="{ on }">
                  <v-btn v-on="on" @click="batchAdd.dialog = true" icon
                    ><v-icon>mdi-account-multiple-plus-outline</v-icon></v-btn
                  >
                </template>
                <span>批量添加成员</span>
              </v-tooltip>
            </v-row>
          </v-toolbar>
          <v-list subheader>
            <v-list-item>
              <v-list-item-avatar>
                <v-icon>mdi-account-group-outline</v-icon>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-layout>
                  空间成员：<router-link
                    :to="'/space/userView?spaceId=' + spaceInfo.spaceId"
                    class="d-inline"
                    >{{ spaceInfo.memberNum }}</router-link
                  >
                  &nbsp;&nbsp;人</v-layout
                >
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-list-item-avatar>
                <v-icon>mdi-comment-question-outline</v-icon>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-layout>
                  发布问题：<router-link :to="'/space/' + spaceInfo.spaceId">{{
                    spaceInfo.questionNum
                  }}</router-link>
                  &nbsp;&nbsp;个
                </v-layout>
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-list-item-avatar>
                <v-icon>mdi-comment-check-outline</v-icon>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-layout>
                  解决问题：<router-link :to="'/space/' + spaceInfo.spaceId">{{
                    spaceInfo.solvedNum
                  }}</router-link>
                  &nbsp;&nbsp;个
                </v-layout>
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-list-item-avatar>
                <v-icon>mdi-file-document-box-outline</v-icon>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-layout>
                  发布博文：<router-link :to="'/space/' + spaceInfo.spaceId">{{
                    spaceInfo.blogNum
                  }}</router-link>
                  &nbsp;&nbsp;个
                </v-layout>
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-list-item-avatar>
                <span>ID</span>
              </v-list-item-avatar>
              <v-list-item-content title="空间ID"
                ><v-layout class="primary--text" align-center>
                  <span>{{ spaceInfo.spaceId }}</span
                  ><v-btn
                    v-clipboard:copy="spaceInfo.spaceId"
                    style="position: relative; top: -6px"
                    icon
                    small
                    title="复制空间ID"
                    ><v-icon small>mdi-content-copy</v-icon></v-btn
                  ></v-layout
                >
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-list-item-avatar>
                <v-icon>mdi-key-star</v-icon>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-layout
                  ><strong class="private--text" title="邀请码">{{
                    spaceInfo.inviteCode
                  }}</strong
                  ><span style="position:relative; top:-10px; cursor: pointer">
                    <v-tooltip top>
                      <template v-slot:activator="{ on }">
                        <v-icon
                          v-on="on"
                          @click="refreshCode"
                          small
                          class="ml-2"
                          >mdi-refresh</v-icon
                        ></template
                      >
                      <span>刷新邀请码</span>
                    </v-tooltip>
                  </span></v-layout
                >
              </v-list-item-content>
              <v-list-item-action>
                <v-row align="center">
                  <v-tooltip bottom>
                    <template v-slot:activator="{ on }">
                      <v-checkbox
                        v-model="spaceInfo.allowInvite"
                        v-on="on"
                        @click="toggleInvite"
                        color="private"
                      ></v-checkbox>
                    </template>
                    <span
                      >勾选启用邀请模式，其他用户将可以通过该邀请码及上方空间ID主动加入该空间</span
                    >
                  </v-tooltip>
                  <v-btn
                    v-clipboard:copy="
                      '[' +
                        spaceInfo.ownerUser.nickname +
                        '] 邀请您加入 DEVA - (aid.dev)：' +
                        spaceInfo.spaceName +
                        ' -- 空间ID: ' +
                        spaceInfo.spaceId +
                        '，邀请码：' +
                        spaceInfo.inviteCode
                    "
                    small
                    color="private"
                    text
                    ><span>复制邀请链</span></v-btn
                  >
                </v-row>
              </v-list-item-action>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-btn
                @click="confirmDelete.dialog = true"
                width="100vw"
                color="red"
                style="color: white"
                >释放该空间</v-btn
              >
            </v-list-item>
          </v-list>
        </v-card>
      </v-flex>
    </v-layout>
    <InfoDialog
      :msg="['更新成功', '更新失败']"
      :succeed="updateResult.resp != null && updateResult.resp.succeed"
      :dialog="updateResult.dialog"
      @update:dialog="updateResult.dialog = $event"
    >
    </InfoDialog>
    <InfoDialog
      :msg="[
        batchAdd.resp && '成功添加' + batchAdd.resp.data + '个成员',
        '添加失败'
      ]"
      :succeed="batchAdd.resp != null && batchAdd.resp.succeed"
      :dialog="batchAdd.resultDialog"
      @update:dialog="batchAdd.resultDialog = $event"
    >
    </InfoDialog>
    <v-dialog v-model="batchAdd.dialog" width="500px" persistent>
      <v-card>
        <v-card-text class="pb-0">
          <v-container class="pb-0">
            <v-form ref="batchAddForm">
              <v-layout align-center>
                <v-textarea
                  v-model="batchAdd.usernames"
                  :rules="[rules.minOne]"
                  rows="30"
                  clearable
                  no-resize
                  hint="输入多个用户名, 每行一个, 最多100个, 系统将自动过滤掉超过的用户名, 筛选有效且不属于空间成员的用户名。"
                  persistent-hint
                >
                </v-textarea>
              </v-layout>
            </v-form>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <div class="flex-grow-1"></div>
          <v-btn @click="batchAdd.dialog = false" color="sub" text small
            >关闭</v-btn
          >
          <v-btn
            :loading="batchAdd.loading"
            @click="batchAddUser"
            color="primary"
            small
            text
            >添加</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="confirmDelete.dialog" width="500px" persistent>
      <v-card>
        <v-card-title style="font-size: 1rem" class="red--text"
          >请确认</v-card-title
        >
        <v-card-text class="pb-0">
          <v-text-field
            ref="confirmCode"
            v-model="confirmDelete.inviteCode"
            :rules="[
              rules.rightCode(confirmDelete.inviteCode, spaceInfo.inviteCode)
            ]"
            placeholder="当前邀请码"
            persistent-hint
            hint="释放后，所有成员将解散，所有信息删除且无法恢复。如果确认释放，请输入当前邀请码确认"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <div class="flex-grow-1"></div>
          <v-btn @click="confirmDelete.dialog = false" color="sub" text small
            >取消</v-btn
          >
          <v-btn
            :loading="confirmDelete.loading"
            @click="deleteSpace"
            color="red"
            small
            text
            >确认</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <InfoDialog
      :msg="['释放成功', '释放失败']"
      :succeed="confirmDelete.resp != null && confirmDelete.resp.succeed"
      :dialog="confirmDelete.resultDialog"
      @update:dialog="
        confirmDelete.resultDialog = $event
        $router.push('/')
      "
    >
    </InfoDialog>
  </v-app>
</template>
<script>
import InfoDialog from '../../components/dialog/InfoDialog'
export default {
  components: {
    InfoDialog
  },
  middleware: 'authenticated',
  data: () => ({
    spaceInfo: null,
    updateResult: {
      loading: false,
      resp: null,
      dialog: false
    },
    batchAdd: {
      dialog: false,
      usernames: '',
      resp: null,
      resultDialog: false,
      loading: false
    },
    confirmDelete: {
      dialog: false,
      resp: null,
      resultDialog: false,
      loading: false,
      inviteCode: null
    },
    rules: {
      min: (v) => (v && v.length >= 2) || '最少两个字符',
      max: (v) => !v || (v && v.length <= 20) || '最多20个字符',
      max200: (v) => !v || (v && v.length <= 200) || '最多200个字符',
      minOne: (v) => !!v || '请输入至少一个用户名',
      rightCode: (v, _code) => (v && v === _code) || '请输入正确的邀请码'
    }
  }),
  created() {
    this.loadSpaceInfo()
  },
  methods: {
    batchAddUser() {
      if (!this.$refs.batchAddForm.validate()) {
        return false
      }
      this.batchAdd.loading = true
      this.$axios
        .$post('/spaceUser/batchAddUser', {
          usernames: this.batchAdd.usernames.split('\n'),
          spaceId: this.spaceInfo.spaceId
        })
        .then((resp) => {
          this.batchAdd.resp = resp
          this.batchAdd.loading = false
          this.batchAdd.resultDialog = true
        })
        .catch((e) => {
          this.batchAdd.loading = false
        })
    },
    loadSpaceInfo() {
      this.$axios
        .$post('/spaceInfo/getSpaceDetail', {
          spaceId: this.$route.query.spaceId
        })
        .then((resp) => {
          if (resp.succeed) {
            this.spaceInfo = resp.data
          }
        })
    },
    updateSpace() {
      if (!this.$refs.form.validate()) {
        return false
      }
      this.updateResult.loading = true
      this.$axios
        .$post('/spaceInfo/updateSpaceInfo', {
          spaceId: this.spaceInfo.spaceId,
          spaceName: this.spaceInfo.spaceName,
          description: this.spaceInfo.description
        })
        .then((resp) => {
          this.updateResult.dialog = true
          this.updateResult.loading = false
          this.updateResult.resp = resp
        })
        .catch((e) => {
          this.updateResult.loading = false
        })
    },
    refreshCode() {
      this.$axios
        .$post('/spaceInfo/refreshCode', {
          spaceId: this.spaceInfo.spaceId
        })
        .then((resp) => {
          if (resp.succeed) {
            this.spaceInfo.inviteCode = resp.data
          }
        })
    },
    toggleInvite() {
      this.$axios
        .$post('/spaceInfo/toggleInvite', {
          spaceId: this.spaceInfo.spaceId,
          allowInvite: !this.spaceInfo.allowInvite
        })
        .then((resp) => {
          if (resp.succeed) {
            this.spaceInfo.allowInvite = resp.data
          }
        })
    },
    deleteSpace() {
      if (!this.$refs.confirmCode.validate()) {
        return false
      }
      this.confirmDelete.loading = true
      this.$axios
        .$post('/spaceInfo/deleteSpace', {
          spaceId: this.spaceInfo.spaceId
        })
        .then((resp) => {
          this.confirmDelete.dialog = false
          this.confirmDelete.loading = false
          this.confirmDelete.resultDialog = true
          this.confirmDelete.resp = resp
          if (resp.succeed) {
            this.$store.commit('needReloadSpaceList')
          }
        })
        .catch((e) => {
          this.confirmDelete.loading = false
        })
    }
  }
}
</script>

<style scoped></style>
