<template>
<main>
  <h1>{{title}}</h1>
  <div class="content" v-html="content"></div>
</main>
</template>

<script>
import axios from 'axios'
export default {
  async asyncData({$config, params}) {
    const { data } = await axios.get(
      `https://fes3.microcms.io/api/v1/contents/${params.content}`,
      {
        headers: { 'X-MICROCMS-API-KEY': $config.microcmsApiKey }
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
