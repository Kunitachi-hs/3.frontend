<template>
<main>
  <h1>{{title}}</h1>
  <div class="content" v-html="content"></div>
</main>
</template>

<script>
import axios from 'axios'
export default {
  async asyncData({params}) {
    const { data } = await axios.get(
      `https://fes3.microcms.io/api/v1/contents/${params.content}`,
      {
        headers: { 'X-MICROCMS-API-KEY': process.env.MICROCMS_API_KEY }
      }
    )
    return data
  }
}
</script>

<style scoped>
main{
  padding: 50px;
  color: var(--on-bg);
}

h1{
  color: var(--on-bg);
  grid-column: left / right;
}

.content {
  width: 100%;
}
</style>
