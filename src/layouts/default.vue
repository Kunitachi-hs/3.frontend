<template>
<div class="container">
  <Header />
  <Nuxt />
</div>
</template>

<style scoped>
div.container {
  display: flex;
  flex-direction: row;
  width: 100%;
  min-height: 100vh;
  background: var(--bg);
}
header {
  flex: 1;
  background: var(--sf);
  color: var(--on-sf);
  border-right: 1px solid #777680;
}
main{
  flex: 2;
}
</style>

<style>
body{
  margin: 0;
}
*, *::after, *::before {
  box-sizing: border-box;
}

:root{
  --p: #6750A4;
  --on-p: #FFFFFF;
  --p-c: #EADDFF;
  --on-p-c: #21005D;
  --s: #625B71;
  --on-s: #FFFFFF;
  --s-c: #E8DEF8;
  --on-s-c: #1D192B;
  --t: #7D5260;
  --on-t: #FFFFFF;
  --t-c: #FFD8E4;
  --on-t-c: #31111D;
  --e: #B3261E;
  --on-e: #FFFFFF;
  --e-c: #F9DEDC;
  --on-e-c: #410E0B;

  --bg: #FFFBFE;
  --on-bg: #1C1B1F;
  --sf: #FFFBFE;
  --on-sf: #1C1B1F;
  --sfv: #E7E0EC;
  --on-sfv: #49454F;
  --ol: #79747E;

  --on-sf-5p: linear-gradient(#1C1B1F0d, #1C1B1F0d);
  --on-bg-16p: linear-gradient(#1C1B1F29, #1C1B1F29);
}
@media (prefers-color-scheme: dark) {
:root{
  --p: #D0BCFF;
  --on-p: #381E72;
  --p-c: #4F378B;
  --on-p-c: #EADDFF;
  --s: #CCC2DC;
  --on-s: #332D41;
  --s-c: #4A4458;
  --on-s-c: #E8DEF8;
  --t: #EFB8C8;
  --on-t: #492532;
  --t-c: #633B48;
  --on-t-c: #FFD8E4;
  --e: #F2B8B5;
  --on-e: #601410;
  --e-c: #8C1D18;
  --on-e-c: #F9DEDC;

  --bg: #1C1B1F;
  --on-bg: #E6E1E5;
  --sf: #1C1B1F;
  --on-sf: #E6E1E5;
  --sfv: #49454F;
  --on-sfv: #CAC4D0;
  --ol: #938F99;

  --on-sf-5p: linear-gradient(#e6e1e50d, #e6e1e50d);
  --on-bg-16p: linear-gradient(#e6e1e529, #e6e1e529);
}
}
</style>