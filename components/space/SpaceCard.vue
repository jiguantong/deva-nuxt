<template>
  <v-card class="pa-2">
    <v-toolbar dense flat class="mb-1">
      <v-layout justify-space-between align-center>
        <router-link
          :to="'/space/' + spaceInfo.spaceId"
          class="private--text hover-line"
          >{{ spaceInfo.spaceName }}</router-link
        >
        <v-chip :to="'/user/' + spaceInfo.ownerUserId" small
          >创建人：{{ spaceInfo.ownerUser.nickname }}</v-chip
        >
      </v-layout>
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
          ><v-layout align-center>
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
      <v-textarea
        :value="spaceInfo.description"
        class="mt-1"
        hide-details
        solo
        readonly
        no-resize
        flat
      >
      </v-textarea>
      <v-layout
        style="font-size: 0.8rem"
        justify-end
        align-center
        class="my_gray--text mt-1"
      >
        <span>创建于&nbsp;</span>
        <span :title="$options.filters.moment(spaceInfo.createTime)">
          {{ spaceInfo.createTime | timeago }}</span
        >
      </v-layout>
    </v-list>
  </v-card>
</template>
<script>
export default {
  props: {
    spaceInfo: {
      required: true,
      type: Object
    }
  }
}
</script>
