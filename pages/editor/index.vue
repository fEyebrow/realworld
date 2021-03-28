<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-10 offset-md-1 col-xs-12">
          <form
          >
            <fieldset>
              <fieldset class="form-group">
                  <input v-model="title" type="text" class="form-control form-control-lg" placeholder="Article Title">
              </fieldset>
              <fieldset class="form-group">
                  <input v-model="about" type="text" class="form-control" placeholder="What's this article about?">
              </fieldset>
              <fieldset class="form-group">
                  <textarea v-model="content" class="form-control" rows="8" placeholder="Write your article (in markdown)"></textarea>
              </fieldset>
              <fieldset class="form-group">
                  <input @keyup.enter="addTag" v-model="tag" type="text" class="form-control" placeholder="Enter tags">
                  <div class="tag-list">
                    <span
                      v-for="(tag, index) in tagList"
                      :key="index"
                      class="tag-default tag-pill"
                    >
                      <i
                        class="ion-close-round"
                        @click="removeTag(index)"
                      />
                      {{ tag }}
                    </span>
                  </div>
              </fieldset>
              <button @click="publish" class="btn btn-lg pull-xs-right btn-primary" type="button">
                  Publish Article
              </button>
            </fieldset>
          </form>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { createArticle, updateArticle, getArticle } from '../../api/article.js'
export default {
  // 在路由匹配组件渲染之前会先执行中间件处理
  middleware: 'authenticated',
  name: 'EditorIndex',
  data() {
    return {
      tagList: [],
      title: '',
      content: '',
      about: '',
      tag: '',
      edit: false
    }
  },
  async asyncData ({ params }) {
    if (!params.slug) {
      return
    }
    const { data } = await getArticle(params.slug)
    const { article } = data
    return {
      article
    }
  },
  mounted() {
    if (this.article) {
        this.title = this.article.title
        this.about = this.article.description
        this.content = this.article.body
        this.tagList = this.article.tagList
        this.edit = true
      }
  },
  methods: {
    async publish(e) {
      e.preventDefault()
      const body = {
        "article": {
          "title": this.title,
          "description": this.about,
          "body": this.content,
          "tagList": this.tagList
        }
      }

      try {
        if (!this.edit) {
          const { data: { article } } = await createArticle(body)
          this.$router.push('/article/' + article.slug)
        } else {
          const { data: { article } } = await updateArticle(this.$route.params.slug ,body)
          this.$router.push('/article/' + article.slug)
        }
      } catch (error) {
        
      }
    },
    addTag(e) {
      this.tagList.push(e.target.value)
      this.tag = ''
    },
    removeTag(index) {
      this.tagList.splice(index, 1)
    }
  }
}
</script>

<style>

</style>
