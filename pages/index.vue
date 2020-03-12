<template>
  <v-card outlined fill-height>
    <v-card-title>日報の提出</v-card-title>
    <v-form ref="form" v-model="valid" :lazy-validation="lazy">
      <v-container>
        <v-row>
          <v-col cols="12">
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              :return-value.sync="date"
              transition="scale-transition"
              offset-y
              min-width="290px"
            >
              <template v-slot:activator="{ on }">
                <v-text-field
                  v-model="date"
                  label="日付"
                  :rules="textRules"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker v-model="date" no-title scrollable>
                <v-spacer></v-spacer>
                <v-btn text color="primary" @click="menu = false">Cancel</v-btn>
                <v-btn text color="primary" @click="$refs.menu.save(date)"
                  >OK</v-btn
                >
              </v-date-picker>
            </v-menu>
            <v-card-subtitle><h2>実績</h2></v-card-subtitle>
            <v-card-text>
              {{ performance }}
            </v-card-text>
            <v-text-field
              v-model="performance"
              label="追加"
              :rules="textRules"
              required
              outlined
            ></v-text-field>
            <v-card-subtitle><h2>次営業日予定</h2></v-card-subtitle>
            <v-card-text>{{ plans }}</v-card-text>
            <v-text-field
              v-model="plans"
              label="追加"
              :rules="textRules"
              required
              outlined
            ></v-text-field>
            <v-card-subtitle><h2>所感</h2></v-card-subtitle>
            <v-textarea
              v-model="description"
              label="所感"
              outlined
            ></v-textarea>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <v-card-actions>
      <v-btn :disabled="!valid" color="success" class="mr-4" @click="validate">
        送信
      </v-btn>
      <v-btn color="error" class="mr-4" @click="reset">入力リセット</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    lazy: false,
    date: new Date().toISOString().substr(0, 10),
    menu: false,
    performance: '',
    plans: '',
    description: '',
    textRules: [(v) => !!v || '入力が必要です。'],
    posts: []
  }),
  methods: {
    validate() {
      if (this.$refs.form.validate() === true) {
        const axios = require('axios')
        const createMessage = (token, roomId, body) =>
          axios({
            method: 'post',
            url: `https://api.chatwork.com/v2/rooms/${roomId}/messages`,
            headers: { 'X-ChatWorkToken': token },
            data: `body=${body}`
          })
        createMessage(
          process.env.TOKEN,
          process.env.ROOMID,
          process.env.BODY
        ).catch(console.log)
      }
    },
    reset() {
      this.$refs.form.reset()
    }
  }
}
</script>
