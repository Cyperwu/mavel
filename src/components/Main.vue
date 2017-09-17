<template>
  <div id="map">
  </div>
</template>

<script>
export default {
  name: 'main',
  data () {
    return {
    }
  },
  mounted () {
    var map = new window.AMap.Map('map', {
      resizeEnable: true,
      zoom: 11,
      center: [116.397428, 39.90923]
    })

    window.map = map
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
  margin: auto;
  width: 100%;
  height: 400px;
}
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
