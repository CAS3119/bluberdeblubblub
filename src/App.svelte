<script>
  // import svelteLogo from './assets/svelte.svg'
  // import viteLogo from '/vite.svg'
  import Leaflet from './lib/Leaflet.svelte'
  import L from "leaflet";
import 'leaflet/dist/leaflet.css';
import { onMount } from 'svelte';

</script>

<main>
 <Leaflet/>
</main>

<style>
 body{
  padding: 0;
  margin:0;
  
  
 }
 main{
  padding: 0;
  margin:0;
 }
</style>
