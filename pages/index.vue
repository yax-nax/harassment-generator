<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <v-card>
        <v-card-title class="headline"></v-card-title>
        <v-card-text>
          <p class="text-center">新たなハラスメントが生成されます。</p>
          <div class="text-center">
            <v-btn rounded color="primary" outlined @click="getWord">
              <label v-if="!isLoading">生成</label>
              <!-- 読み込み中表示 -->
              <v-progress-circular
                indeterminate
                color="primary"
                :width="1"
                :size="20"
                v-if="isLoading"
              ></v-progress-circular>
            </v-btn>
          </div>
          <br />
          <p class="text-center display-1">{{title}}</p>
          <div class="text-xs-right">
            <em>
              <small>{{text}}</small>
            </em>
          </div>
          <hr class="my-3" />
          <div class="text-center">
            <v-btn :href="twitterLink" target="_new" color="cyan" rounded outlined>
              <v-icon>mdi-twitter</v-icon>つぶやく
            </v-btn>
            <v-btn
              href="https://twitter.com/hashtag/%E3%83%8F%E3%83%A9%E3%82%B9%E3%83%A1%E3%83%B3%E3%83%88%E3%82%B8%E3%82%A7%E3%83%8D%E3%83%AC%E3%83%BC%E3%82%BF"
              target="_new"
              color="cyan"
              rounded
              outlined
            >
              <v-icon>mdi-twitter</v-icon>みんなのつぶやき
            </v-btn>
          </div>
        </v-card-text>
        <v-card-actions></v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from '@nuxtjs/axios'

export default {
  components: {},
  data() {
    return {
      isLoading: false,
      title: '',
      text: '',
      twitterLink:
        'https://twitter.com/intent/tweet?button_hashtag=ハラスメントジェネレーター&ref_src=twsrc%5Etfw&'
    }
  },

  methods: {
    async getWord() {
      this.isLoading = true
      const url =
        'https://ja.wikipedia.org/w/api.php?action=query&list=random&rnnamespace=0&rnlimit=1&format=json'
      let data = await this.$axios
        .$get(url)
        .then((res) => {
          let id = res.query.random[0].id
          let title = res.query.random[0].title

          this.$axios
            .$get(
              'https://ja.wikipedia.org/w/api.php?action=query&titles=' +
                title +
                '&format=json&prop=categories'
            )
            .then((res) => {
              this.isLoading = false
              let categories = res.query.pages[id].categories
              let idx = Math.floor(Math.random() * (categories.length - 0)) + 0
              let title = categories[idx].title
              this.title = this.formatTitle(title) + 'ハラスメント'
              this.twitterLink =
                'https://twitter.com/intent/tweet?button_hashtag=ハラスメントジェネレーター&ref_src=twsrc%5Etfw' +
                '&text=' +
                this.title
            })
        })
        .catch((error) => {
          console.log(error)
          this.isLoading = false
        })
    },
    formatTitle(text) {
      console.log(text)
      let res = text
      res = res.replace('Category:', '')
      res = res.replace('のスタブ', '')
      res = res.replace('のスタブ項目', '')
      res = res.replace('のスタブ記事', '')
      res = res.replace('のサブスタブ', '')
      res = res.replace('のサブスタブ項目', '')
      res = res.replace('のサブスタブ記事', '')
      res = res.replace('がある記事', '')
      res = res.replace('のある記事', '')
      res = res.replace('の記事', '')
      res = res.replace('に関するスタブ項目', '')
      res = res.replace('に関するスタブ記事', '')
      res = res.replace('に関するサブスタブ項目', '')
      res = res.replace('に関するサブスタブ記事', '')
      res = res.replace('識別子が指定されている記事', '')
      return res
    }
  }
}
</script>
