<template>
<header>
    <div class="container">
        <h1>国高祭</h1>
        <nav>
            <ul>
                <li><nuxt-link to="/">HOME</nuxt-link></li>
                <li><a href="">1,2年展示</a></li>
                <li><a href="">3年演劇</a></li>
                <li><a href="">部活/同好会</a></li>
                <li><a href="">後夜祭</a></li>
            </ul>
        </nav>
    </div>
</header>
</template>

<style scoped>
.container {
    width: 40%;
    min-width: 40%;
    max-width: 80%;
    margin:  10vh auto;
    text-align: center;
}
ul{
    list-style: none;
    margin: 0;
    padding: 0;
}
li{
    display: block;
    height: 50px;
    line-height: 50px;
    border-radius: 25px;
}
li.a:hover{
    background: var(--sfv);
}
li:hover{
    background-image: var(--on-bg-16p);
}
li>a{
    text-decoration: none;
    color: var(--on-sfv);
}
</style>