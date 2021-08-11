<template>
  <header class="site-header">
    <h1 class="site-title">Colors</h1>
    <nav class="site-nav">
      <a href="#">Info</a>
    </nav>
  </header>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.site-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 1rem;
  padding: 0 0 1rem;
  border-bottom: 1px solid black;
}
.site-title {
  margin: 0;
}
.site-nav {
  a {
    margin-left: 2rem;
  }
}
</style>
