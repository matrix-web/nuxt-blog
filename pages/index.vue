<template>
  <section>
    <component 
      v-if="story.content.component"
      :key="story.content._uid"
      :blok="story.content"
      :is="story.content.component"
    />
  </section>
</template>

<script>
export default {
  data () {
    return {
      story: {
        content: {}
      }
    }
  },
  mounted () {
    this.$storybridge(() => {
      const storyblokInstance = new StoryblokBridge()

      storyblokInstance.on('input', event => {
        console.log(this.story.content)

        if (event.story.id === this.story?.id) {
          this.story.content = event.story.content
        }
      })

      storyblokInstance.on(['published', 'change'], event => {
        this.$nuxt.$router.go({
          path: this.$nuxt.$router.currentRoute,
          force: true
        })
      })
    })
  },
  async fetch (ctx) {
    if (ctx.store.state.articles.loaded !== '1') {
      let articlesRefRes = await ctx.app.$storyapi.get(`cdn/stories`, {starts_with: 'articles/', version: 'draft'})
      ctx.store.commit('articles/setArticles', articlesRefRes.data.stories)
      ctx.store.commit('articles/setLoaded', '1')
    }
  },
  asyncData(ctx) {
    return ctx.app.$storyapi.get('cdn/stories/home', {
      version: 'draft'
    }).then(res => res.data)
      .catch(res => {
        if (!res.response) {
          console.error(res)
          ctx.error({statusCode: 404, message: 'Failed to receive content from api'})
        } else {
          console.error(res.response.data)
          ctx.error({statusCode: res.response.status, message: res.response.data})
        }
      })
  }
}
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
@apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
