<template>
  <section>
    <h2 class="py-10 text-center font-bold text-4xl">Articles Overview</h2>
    {{ articles }}
    <ul class="flex py-6 mb-6">
      <li 
        v-for="article in stories" :key="article._uuid"
        class="flex-auto px-6" style="min-width: 33%"
      >
        <article-teaser
          v-if="article.content"
          :article-link="article.full_slug"
          :article-content="article.content"
        />
        <p v-else
          class="px-4 py-2 text-white bg-red-700 text-center rounded"
        >This content loads on save. <strong>Save the entry & reload.</strong></p>
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  data () {
    return {
      stories: []
    }
  },
  asyncData (ctx) {
    return ctx.app.$storyapi.get('cdn/stories', {
      starts_with: 'articles/',
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