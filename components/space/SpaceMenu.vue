<template>
  <v-row>
    <v-menu :close-on-content-click="true" offset-y open-on-hover>
      <template v-slot:activator="{ on }">
        <v-chip
          v-on="on"
          color="private"
          small
          style="border-radius: 0; color: white"
          >{{ spaceInfo.spaceName }}</v-chip
        >
      </template>
      <v-list dense flat class="pt-0">
        <v-list-item class="pa-0 mb-0">
          <v-list-item-content class="pa-0">
            <v-btn
              :to="'/space/userView?spaceId=' + spaceInfo.spaceId"
              depressed
              text
              class="text-left no-flex"
              >查看所有成员</v-btn
            >
          </v-list-item-content>
        </v-list-item>
        <v-list-item
          v-if="spaceInfo.ownerUserId === $store.getters.getUserId"
          class="pa-0 mb-0"
        >
          <v-list-item-content class="pa-0">
            <v-btn
              :to="'/space/manageSpace?spaceId=' + spaceInfo.spaceId"
              depressed
              text
              class="text-left no-flex"
              >管理空间</v-btn
            >
          </v-list-item-content>
        </v-list-item>
        <v-list-item v-else class="pa-0 mb-0">
          <v-list-item-content class="pa-0">
            <v-btn
              @click="confirmExit.dialog = true"
              depressed
              text
              class="text-left no-flex"
              >退出空间</v-btn
            >
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-menu>
    <ConfirmDialog
      :dialog="confirmExit.dialog"
      :todo="exitSpace"
      @update:dialog="
        confirmExit.dialog = $event
        $router.push('/')
      "
      msg="确定退出该空间吗?"
    >
    </ConfirmDialog>
  </v-row>
</template>
<script>
import ConfirmDialog from '../dialog/ConfirmDialog'
export default {
  components: { ConfirmDialog },
  props: {
    spaceInfo: {
      required: true,
      type: Object
    }
  },
  data: () => ({
    confirmExit: {
      dialog: false,
      result: {
        dialog: false,
        resp: null
      }
    }
  }),
  methods: {
    exitSpace() {
      this.$axios
        .$post('/spaceUser/exitSpace', {
          spaceId: this.$route.params.id
        })
        .then((resp) => {
          this.confirmExit.dialog = false
          this.confirmExit.result.resp = resp
          this.confirmExit.result.dialog = true
          if (resp.succeed) {
            this.$store.commit('needReloadSpaceList')
          }
        })
    }
  }
}
</script>
<style></style>
