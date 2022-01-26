<template>
<main>
  <h1>TOP</h1>
  <article class="large">
    <h2>リンク一覧</h2>
    <p>
      <p href="/admission">入場制限システム</p>
    </p>
  </article>
  <article class="middle">
    <img src="~/assets/img/top/3年演劇.jpg" alt="">
    <h2>3年演劇</h2>
  </article>
  <article class="middle">
    <img src="~/assets/img/top/後夜祭.png" alt="">
    <h2>後夜祭</h2>
  </article>
  <article class="middle" v-for="content in contents" :key="content.id">
    <nuxt-link :to="`/${content.id}`">
      <img v-if="content.thumbnail" :src="`${content.thumbnail.url}`" alt="">
      <h2>{{content.title}}</h2>
      <p>{{content.description}}</p>
    </nuxt-link>
  </article>
</main>
</template>

<script>
import axios from 'axios'
export default {
  async asyncData({$config}) {
    const { data } = await axios.get(
      'https://fes3.microcms.io/api/v1/lists?depth=0',
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
  display: grid;
  gap: 20px;
  grid-template-columns: [left] repeat(6,minmax(0, 1fr)) [right];
  grid-template-rows: [title] 70px [top] auto [bottom];
}

h1{
  color: var(--on-bg);
  grid-column: left / right;
}

article a{
  text-decoration: none;
  color: var(--on-sfv);
}
article{
  background: var(--sfv);
  border-radius: 12px;
  color: var(--on-sfv);
  padding-bottom: 10px;
}
article:hover{
  background-image: var(--on-sf-5p);
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.3), 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
}

article img{
  width: 100%;
  border-radius: 12px;
}
article h2{
  padding: 5px 20px 0;
}
article p{
  padding: 0 20px 0;
  overflow-wrap: break-word;
}

article.large{
  grid-column: span 4;
}
article.middle{
  grid-column: span 2;
}
</style>
