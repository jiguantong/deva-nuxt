<template>
  <div>
    <v-layout
      class="my_gray--text d-sm-block d-md-none d-lg-none"
      style="font-size: 0.9rem"
      ><nuxt-link
        :to="'/user/' + user.userId"
        style="font-size: 14px !important;"
        class="d-xs-block hover-line  blue--text"
        >{{ user.nickname }}</nuxt-link
      >&nbsp;·&nbsp;{{ createTime | timeago }}</v-layout
    >
    <v-layout :justify-end="justifyEnd" class="d-md-block hidden-sm-and-down">
      <v-card :width="width" flat class="pa-2">
        <v-layout align-center>
          <v-flex shrink>
            <v-avatar color="grey">
              <v-img :src="user.avatar"></v-img>
            </v-avatar>
          </v-flex>
          <v-flex class="ml-3" grow>
            <v-layout align-center>
              <nuxt-link
                :to="'/user/' + user.userId"
                class="hover-line"
                style="text-decoration: none; font-size: 14px"
                >{{ user.nickname || user.username }}</nuxt-link
              ><b style="color: red">&nbsp;·&nbsp;</b
              ><span style="font-size: 12px;" title="声望">{{
                user.reputation
              }}</span>
            </v-layout>
            <v-layout>
              <small
                :title="$options.filters.moment(createTime)"
                style="font-size: 13px; color: #666"
              >
                {{ prefix }}于&nbsp; {{ createTime | timeago }}</small
              >
            </v-layout>
            <v-layout v-if="createTime !== modifiedTime">
              <small
                :title="$options.filters.moment(modifiedTime)"
                style="font-size: 13px; color: #666"
              >
                更新于&nbsp;
                {{ modifiedTime | timeago }}</small
              ></v-layout
            >
          </v-flex>
        </v-layout>
      </v-card>
    </v-layout>
  </div>
</template>
<script>
export default {
  props: {
    user: {
      required: true,
      type: Object
    },
    createTime: {
      required: true,
      type: Number
    },
    modifiedTime: {
      required: true,
      type: Number
    },
    prefix: {
      required: false,
      type: String,
      default: '创建'
    },
    justifyEnd: {
      required: false,
      type: Boolean,
      default: true
    },
    width: {
      required: false,
      type: String,
      default: ''
    }
  }
}
</script>
<style></style>
