<template>
<main>
  <h1>TOP</h1>
  <article class="dsc large">
    <a href="">
      <h2>日程</h2>
      <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. Rerum alias fugit voluptatibus necessitatibus cumque? Accusamus illum laudantium asperiores similique? Iste, aliquid reprehenderit. Blanditiis maiores deserunt consequatur cumque nobis ab impedit!</p>
    </a>
  </article>
  <article class="img middle">
    <img src="~/assets/img/top/3年演劇.jpg" alt="">
    <h2>3年演劇</h2>
  </article>
  <article class="img middle">
    <img src="~/assets/img/top/後夜祭.png" alt="">
    <h2>後夜祭</h2>
  </article>
</main>
</template>

<script>
export default {}
</script>

<style scoped>
main{
  padding: 50px;
  display: grid;
  gap: 20px;
  grid-template-columns: [left] repeat(6,1fr) [right];
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
}
article:hover{
  background-image: var(--on-sf-5p);
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.3), 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
}

article.dsc{
  padding: 20px;
}
article.img>img{
  width: 100%;
  border-radius: 12px;
}
article.img>h2{
  padding: 5px 20px 10px;
}

article.large{
  grid-column: span 4;
}
article.middle{
  grid-column: span 2;
}
</style>
