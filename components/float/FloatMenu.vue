<template>
  <client-only>
    <div class="hidden-sm-and-down">
      <v-speed-dial
        v-model="fab"
        bottom
        right
        fixed
        direction="left"
        open-on-hover
        transition="slide-x-reverse-transition"
      >
        <template v-slot:activator>
          <!-- 黄色 #e9c31e-->
          <v-btn
            id="floatRoot"
            v-model="fab"
            color="private"
            fab
            style="color: white"
          >
            <a v-if="fab" @click.stop="toTop" style="color: white"
              ><v-icon title="回到顶部">vertical_align_top</v-icon></a
            >
            <v-icon v-else>scatter_plot</v-icon>
          </v-btn>
        </template>
        <v-tooltip top internal-activator>
          <template v-slot:activator="{ on }">
            <v-btn v-on="on" fab dark small color="blue">
              <a
                style="color: white; text-decoration: none"
                href="mailto:admin@aid.dev"
                ><v-icon>mdi-email</v-icon></a
              >
            </v-btn>
          </template>
          <span>联系我们：admin@aid.dev</span>
        </v-tooltip>
        <v-tooltip top fixed>
          <template v-slot:activator="{ on }">
            <a
              style="text-decoration: none"
              href="https://jq.qq.com/?_wv=1027&k=5rD73Wh"
              target="_blank"
              ><v-btn v-on="on" fab dark small color="green">
                <v-icon>mdi-help</v-icon>
              </v-btn></a
            >
          </template>
          <span>如果您需要帮助，点击加入我们的官方QQ群：930562002</span>
        </v-tooltip>
        <v-tooltip top fixed>
          <template v-slot:activator="{ on }">
            <v-btn
              @click="suggest.dialog = true"
              v-on="on"
              fab
              dark
              small
              color="orange"
            >
              <v-icon>mdi-pencil</v-icon>
            </v-btn>
          </template>
          <span>建议反馈</span>
        </v-tooltip>
      </v-speed-dial>
      <v-dialog v-model="suggest.dialog" persistent max-width="600px">
        <v-card>
          <v-card-text class="pt-3">
            <v-form ref="suggestForm">
              <v-textarea
                v-model="suggest.content"
                :rules="[rules.min10, rules.max1000]"
                :counter="1000"
                label="输入您的意见"
                rows="10"
                no-resize
              ></v-textarea>
            </v-form>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              @click="
                suggest.dialog = false
                suggest.resp = null
                suggest.content = null
              "
              text
              small
              >关闭
            </v-btn>
            <v-btn
              :loading="suggest.loading"
              @click="submitSuggest"
              text
              small
              color="primary"
              ><span>提交</span>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <InfoDialog
        :msg="[
          '提交成功，感谢！有你的帮助，我们会变得更好。',
          suggest.result.resp && suggest.result.resp.msg
        ]"
        :succeed="suggest.result.resp != null && suggest.result.resp.succeed"
        :dialog="suggest.result.dialog"
        @update:dialog="suggest.result.dialog = $event"
        close-txt="关闭"
      >
      </InfoDialog>
    </div>
  </client-only>
</template>
<script>
import InfoDialog from '../dialog/InfoDialog'
export default {
  components: { InfoDialog },
  data: () => ({
    fab: false,
    suggest: {
      content: null,
      loading: false,
      dialog: false,
      result: {
        resp: null,
        dialog: false
      }
    },
    rules: {
      max1000: (v) => (v && v.length <= 1000) || '不能超过1000个字符',
      min10: (v) => (v && v.length > 10) || '最少为10个字符哦'
    }
  }),
  methods: {
    toTop() {
      this.$vuetify.goTo(0, {
        duration: 245,
        offset: 0,
        easing: 'easeInCubic'
      })
    },
    submitSuggest() {
      if (!this.$refs.suggestForm.validate()) {
        return false
      }
      this.suggest.loading = true
      this.$axios
        .$post('/adminTask/createTask', {
          content: this.suggest.content
        })
        .then((resp) => {
          this.suggest.loading = false
          this.suggest.result.resp = resp
          this.suggest.result.dialog = true
          if (resp.succeed) {
            this.suggest.dialog = false
          }
        })
        .catch((e) => {
          this.suggest.loading = false
          this.suggest.dialog = false
        })
    }
  }
}
</script>
<style>
.v-speed-dial {
  bottom: 80px !important;
  right: 30px !important;
}
.v-btn-floating {
  position: relative;
}
</style>
