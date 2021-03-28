<template>
  <div class="profile-page">

    <div class="user-info">
      <div class="container">
        <div class="row">

          <div class="col-xs-12 col-md-10 offset-md-1">
            <img src="http://i.imgur.com/Qr71crq.jpg" class="user-img" />
            <h4>{{user.username}}</h4>
            <p>
              {{user.bio}}
            </p>
            <button class="btn btn-sm btn-outline-secondary action-btn">
              <i class="ion-plus-round"></i>
              &nbsp;
              Follow {{user.username}}
            </button>
          </div>

        </div>
      </div>
    </div>

    <div class="container">
      <div class="row">

        <div class="col-xs-12 col-md-10 offset-md-1">
          <div class="articles-toggle">
            <ul class="nav nav-pills outline-active">
              <li class="nav-item">
                <a :class="{
                  'nav-link': true,
                  'active': channel === 'my'
                }"  @click.prevent="selectMy" href="">My Articles</a>
              </li>
              <li class="nav-item">
                <a :class="{
                  'nav-link': true,
                  'active': channel === 'favorited'
                }"  @click.prevent="selectFavor"  href="">Favorited Articles</a>
              </li>
            </ul>
          </div>

          <div class="article-preview"
            v-for="article in articles"
            :key="article.slug"
          >
            <div class="article-meta">
              <a href=""><img :src="article.author.image" /></a>
              <div class="info">
                <a href="" class="author">{{article.author.username}}</a>
                <span class="date">{{article.updatedAt}}</span>
              </div>
              <button class="btn btn-outline-primary btn-sm pull-xs-right">
                <i class="ion-heart"></i> {{article.favoritesCount}}
              </button>
            </div>
            <a href="" class="preview-link">
              <h1>{{article.title}}</h1>
              <p>{{article.description}}</p>
              <span>Read more...</span>
              <ul class="tag-list">
                <li 
                  v-for="item in article.tagList"
                  :key="item"
                 class="tag-default tag-pill tag-outline">{{item}}</li> 
              </ul>
            </a>
          </div>


        </div>

      </div>
    </div>

    <nav>
      <ul class="pagination">
        <li
          class="page-item"
          :class="{
            active: item === page
          }"
          v-for="item in totalPage"
          :key="item"
        >
          <nuxt-link
            class="page-link"
            :to="{
              name: 'profile',
              query: {
                page: item,
                author: user.username
              }
            }"
          >{{ item }}</nuxt-link>
        </li>
      </ul>
    </nav>

  </div>
</template>

<script>
import { getOwnArticle } from '@/api/article'
import { mapState } from 'vuex'
export default {
  middleware: 'authenticated',
  name: 'UserProfile',
  data() {
    return {
      channel: 'my',
    }
  },
  async asyncData(context) {
    const { query, store } = context
    const page = Number.parseInt(query.page || 1)
    const limit = 5
    const { data: { articles, articlesCount } } = await getOwnArticle({
      limit,
      offset: (page - 1) *limit,
      author: store.state.user.username
    })
    return {
      articles,
      articlesCount,
      page,
      limit
    }
  },
  watchQuery: ['page'],
  computed: {
    ...mapState(['user']),
    totalPage () {
      return Math.ceil(this.articlesCount / this.limit)
    }
  },
  methods: {
    async selectMy() {
      this.channel = 'my'
      const { data: { articles, articlesCount } } = await getOwnArticle({
        limit: this.limit,
        offset: 0,
        author: this.user.username
      })
      this.articles = articles
      this.articlesCount = articlesCount
      this.page = 1
    },
    async selectFavor() {
      this.channel = 'favorited'
      const { data: { articles, articlesCount } } = await getOwnArticle({
        limit: this.limit,
        offset: 0,
        favorited: this.user.username
      })
      this.articles = articles
      this.articlesCount = articlesCount
      this.page = 1
    }
  }
}
</script>

<style>

</style>
