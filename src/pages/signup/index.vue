<template>
<main>
  <h1>アカウント登録</h1>
  <form id="form">
    <label>
      <input type="email" name="email" id="email" required>
      <span>メールアドレス</span>
    </label>
    <label class="no-meter">
      <input :type="type" name="password" v-model="password" @ required>
      <span>パスワード</span>
      <meter min="-1" max="4" :value="score"></meter>
      <p class="meter-text meter-warning" v-html="warning"></p>
      <p class="meter-text meter-suggest" v-html="suggest"></p>
    </label>
  </form>
</main>
</template>

<script>
import zxcvbn from 'zxcvbn';
export default {
  data: () => ({
    password: '',
    type: 'password',
    score: 0,
    warning: '',
    suggest: ''
  }),
  watch: {
    password(v){
      const result = zxcvbn(v);
      this.score = result.score;
      this.warning = result.feedback.warning;
      this.suggest = result.feedback.suggestions.join('<br>');
    }
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

label{
  display: block;
  position: relative;
  margin-top: 30px;
  border-bottom: 3px solid var(--ol);
  border-radius: 3px;
}
input{
  border: none;
  appearance: none;
  background: none;
  padding: .8rem .8rem .4rem;
  width: 100%;
  max-width: 30rem;
  font-size: 1rem;
}
input:focus{
  outline: none;
  border-color: var(--p);
}
input+span{
  position: absolute;
  pointer-events: none;
  left: .8rem;
  top: 1rem;
  font-size: 1rem;
}
input:focus+span,
input:valid+span{
  left: .4rem;
  top: -0.2rem;
  font-size: .8rem;
  color: var(--on-g);
}

.no-meter>meter,
.no-meter>.meter-text{
  display: none;
}

meter{
  display: block;
  width: 100%;
  height: .5em;
  opacity: 0.5;
  margin: 0;
  padding: 0;
  /* Applicable only to Firefox */
  background: none;
  background-color: rgba(0,0,0,0.1);
}
meter::-webkit-meter-bar {
  background: none;
  background-color: rgba(0,0,0,0.1);
  border: none;
  border-radius: 0;
  margin: 0;
  padding: 0;
}
meter[value="0"]::-webkit-meter-optimum-value { background: red; }
meter[value="1"]::-webkit-meter-optimum-value { background: #f56e00; }
meter[value="2"]::-webkit-meter-optimum-value { background: yellow; }
meter[value="3"]::-webkit-meter-optimum-value { background: green; }
meter[value="4"]::-webkit-meter-optimum-value { background: blue; }
meter[value="0"]::-moz-meter-bar { background: red; }
meter[value="1"]::-moz-meter-bar { background: #f56e00; }
meter[value="2"]::-moz-meter-bar { background: yellow; }
meter[value="3"]::-moz-meter-bar { background: green; }
meter[value="4"]::-moz-meter-bar { background: blue; }

p.meter-text{
  background: var(--e-c);
  color: var(--on-e-c);
  margin: 0;
  padding: .2rem;
  font-size: .8rem;
  line-height: .8rem;
}
meter[value="3"]~p.meter-text,
meter[value="4"]~p.meter-text{
  display: none;
}

</style>
