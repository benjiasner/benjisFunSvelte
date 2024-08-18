<script>
  import Nav from '../nav.svelte'
  let name = 'Benji';
</script>

<div>
  <Nav navProp="{name}'s Navbar"/>
  <h1>{name}'s Personal Development Site</h1>
  <div>
    <a href="/orienting">Orienting STL files in Threlte</a>
  </div>
</div>

<style>
  h1 {
    color: blue;
  }
  a {
    color: pink;
  }
</style>