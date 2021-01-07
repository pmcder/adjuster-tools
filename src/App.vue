<template>
  <div>
  <div id="app">
    <section class="left">
      <Insurance />
            <div id="about">
        <h2>About this page</h2>
        <p>
          I built these tools based on my experience working
          as a claims adjuster. 
        </p>
      </div>
    </section>
    <section>
      <prorata />
       <negotiator/>
    </section>

  </div>
   
  </div>
</template>

<script>

import prorata from "./components/prorata.vue";
import negotiator from "./components/negotiator.vue";
export default {
  name: "App",
  components: {
    
    prorata,
    negotiator,
  },
};
</script>

<style>
#app {
  background-color: #219ebc;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  overflow: scroll;
  height: 100vh;
  width: 100vw;
}

.errorArea {
  background-color:#d16056;
  color: white;
}

.widget{
  background-color: aliceblue;
  color: #2c3e50;
  margin: 10px;
  padding: 10px;
  overflow: scroll;
}

#about{
  margin-top: 20px;
  padding: 15px;
  background-color: aliceblue;
  color: #2c3e50;
  margin: 10px;
  padding: 10px;
}
h1{
    color: #2c3e50;
}

section{
  text-align: left;
}
section.left{
  color: white;
}

footer {
  text-align: center;
  grid-column-start: 1;
  grid-column-end: 2;
  color: white;
}

header{
  text-align: center;
  grid-column-start: 1;
  grid-column-end: 2;
  background-color:#1D3557;
  color: white;
}
body{
  background-color: #2c3e50;
}
</style>
