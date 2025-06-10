




<script>

    import L, { marker } from "leaflet";
    import 'leaflet/dist/leaflet.css';
    import { onMount } from 'svelte';
    
    let map;
    let markers = [];
    
      onMount(() => {
     map = L.map('map').setView([51.6900, 5.2940], 13);
    
    
        const osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
          maxZoom: 19,
          attribution: '© OpenStreetMap contributors'
        }).addTo(map);
    
       
    
        const satelliteLayer = L.tileLayer('https://{s}.google.com/vt/lyrs=s&x={x}&y={y}&z={z}', {
          maxZoom: 20,
          subdomains: ['mt0', 'mt1', 'mt2', 'mt3'],
          attribution: 'Imagery © Google'
        });
    
        const googleLayerMap = L.tileLayer('https://{s}.google.com/vt/lyrs=m&x={x}&y={y}&z={z}', {
          maxZoom: 20,
          subdomains: ['mt0', 'mt1', 'mt2', 'mt3'],
          attribution: 'Imagery © Google'
        });
        
        const googleLayerHybrid = L.tileLayer('https://{s}.google.com/vt/lyrs=s,h&x={x}&y={y}&z={z}', {
          maxZoom: 20,
          subdomains: ['mt0', 'mt1', 'mt2', 'mt3'],
          attribution: 'Imagery © Google'
        });
    
        const googleTerrain = L.tileLayer('https://{s}.google.com/vt/lyrs=p,h&x={x}&y={y}&z={z}', {
          maxZoom: 20,
          subdomains: ['mt0', 'mt1', 'mt2', 'mt3'],
          attribution: 'Imagery © Google'
        });
        const satelliteLayer2 = L.tileLayer('https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
          maxZoom: 20,
          attribution: 'Imagery © Esri'
        });
    
        const cartoLight = L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://carto.com/">CARTO</a>',
            subdomains: 'abcd',
            maxZoom: 19
        });
    
        
    
        const heightmap = L.tileLayer(`https://s3.amazonaws.com/elevation-tiles-prod/terrarium/{z}/{x}/{y}.png`,{ //style URL
            tileSize: 512,
            zoomOffset: -1,
            minZoom: 1,
            attribution: "\u003ca href=\"https://www.maptiler.com/copyright/\" target=\"_blank\"\u003e\u0026copy; MapTiler\u003c/a\u003e \u003ca href=\"https://www.openstreetmap.org/copyright\" target=\"_blank\"\u003e\u0026copy; OpenStreetMap contributors\u003c/a\u003e",
            crossOrigin: true,
            maxZoom: 19
          });
    
    
    
        
        const baseMaps = {
          "OpenStreetMap": osmLayer,
          "Google Satellite": satelliteLayer,
          "Google Maps": googleLayerMap,
          "Google Hybrid": googleLayerHybrid,
          "Google Terrain": googleTerrain,
          "Esri Satellite": satelliteLayer2,
          "CartoLight": cartoLight,
          "height map": heightmap
        };
    
    
        const openrailwaymap = L.tileLayer('http://{s}.tiles.openrailwaymap.org/standard/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/copyright">© OpenStreetMap contributors</a>, Style: <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA 2.0</a> <a href="http://www.openrailwaymap.org/">OpenRailwayMap</a> and OpenStreetMap',
        minZoom: 2,
        maxZoom: 19,
        tileSize: 256
    });
    
    
    const overlayMaps = {
        "OpenRailwayMap" : openrailwaymap,
    }
    
        
        L.control.layers(baseMaps,overlayMaps).addTo(map);
        // test()
    
    
        map.on('moveend', () => {
        //   fetchLevelCrossings(map.getBounds());
          // Drawline();
    
        // fetchRailways(map.getBounds())
        });
    
    map.on('click', function(e) {
          const icon = L.divIcon({
            className: '',
            html: '<div class="rotated-square"></div>',
            iconSize: [5, 5],
            iconAnchor: [10, 10] // center of square
          });
    
          const marker = L.marker(e.latlng, {
            icon: icon,
            draggable: true
            
          }).addTo(map);
          marker.on('dragend',calculateBeziere);
          markers.push(marker);
          calculateBeziere();
          console.log(marker)
    
        });
    
    });
    
    function addCircleMArker(point){
      const latlng = L.Projection.SphericalMercator.unproject(point);
      const icon = L.divIcon({
            className: '',
            html: '<div class="rotated-square"></div>',
            iconSize: [5, 5],
            iconAnchor: [10, 10] // center of square
          });
    
          const marker = L.marker(latlng, {
            icon: icon,
            draggable: true
            
          }).addTo(map);
          marker.on('dragend',calculateBeziere);
          markers.push(marker);
          // calculateBeziere();
          console.log(marker)
    
        
      
    }
    
     function calculateBeziere(){
      // alert("hoi")
      let markerposes = []
      let oldmarkerposes = []
    
      markers.forEach(marker => {
        let startCoordinate =  marker.getLatLng();
      let startCoordinateMeter = L.Projection.SphericalMercator.project(startCoordinate);
      oldmarkerposes.push(startCoordinateMeter);
      }) 
      for(let oldocount = 0; oldocount < oldmarkerposes.length; oldocount++){
      // oldmarkerposes.forEach(oldo => {
        const oldo = oldmarkerposes[oldocount]
  
        markerposes.push(oldo)
        // console.log("ol"+oldmarkerposes.length+"oldo:"+oldocount);
        if(oldmarkerposes.length != oldocount + 1){
          const p = L.point(oldo.x + 150, oldo.y + 150)
          markerposes.push(p)
          addCircleMArker(p)
          // console.log(oldocount)
          // console.log(ol)
        }
      
        if(markerposes.length > 2){
          const p = L.point(oldo.x - 150, oldo.y - 150)
          markerposes.push(p)
          addCircleMArker(p);
        }
      
     
      }
  
  
  
    
      let endcoordsRaw = [];
      if (markerposes.length == 2){
        for (let t = 0; t < 100; t++){
          endcoordsRaw.push(lerp(markerposes[0],markerposes[1],t / 100))
        }
      }
    
      if (markerposes.length == 3){
        for (let t = 0; t < 100; t++){
         let t1 = lerp(markerposes[0],markerposes[1],t / 100)
         let t2 = lerp(markerposes[1],markerposes[2],t / 100)
         endcoordsRaw.push(lerp(t1,t2,t / 100))
        }
      }
      if (markerposes.length == 4){
        for (let t = 0; t < 100; t++){
         let t1 = lerp(markerposes[0],markerposes[1],t / 100)
         let t2 = lerp(markerposes[1],markerposes[2],t / 100)
         let t3 = lerp(markerposes[2],markerposes[3],t / 100)
          let t4 = lerp(t1,t2,t / 100);
          let t5 = lerp(t2,t3,t / 100);
          endcoordsRaw.push(lerp(t4,t5,t / 100))
        }
      }
      // if (markerposes.length > 5){
      //   let a = markerposes.length - 1
      //   for (let t = 0; t < 100; t++){
      //     let f = [];
      //     for (let z = 0; t < a; z++){
      //       f.push(lerp(markerposes[z],markerposes[z+1],t /100));
      //     }
      //   }
      // }
    
    
      // let endcoords = [];
      // endcoordsRaw.forEach(endcoord => {
      //   console.log(endcoord)
      //   const newLatLng = L.Projection.SphericalMercator.unproject(endcoord);
      //   endcoords.push(newLatLng)
      // });
     
      let endcoords = unprojectArray(endcoordsRaw);
      
    
    
    
     
    
      map.eachLayer(layer => {
        if (layer._center != undefined) {
          map.removeLayer(layer);
        }else{
          // console.log(layer)
        }
      });
      L.polyline(endcoords, {
          color: 'red',
          weight: 4,
          className: 'flat-line'
      }).addTo(map);
    
      L.polyline(unprojectArray(markerposes), {
          color: 'blue',
          weight: 2,
          className: 'flat-line'
      }).addTo(map);
    
     
     }
    
     function unprojectArray(array){
      let newArr = []
      array.forEach(arr => {
        // console.log(endcoord)
        const newLatLng = L.Projection.SphericalMercator.unproject(arr);
        newArr.push(newLatLng)
      });
      return newArr;
     }
     function lerp (start, end, amt){
      
      const x = (1-amt)*start.x+amt*end.x;
      const y = (1-amt)*start.y+amt*end.y;
      
      return L.point(x,y);
    }
    </script>
    
    
    <div style="z-index: 0;" id="map"></div>
    
    
    <style>
       #map {
        height: 100vh;
        width: 100vw;
        background: lightgray; /* just for testing */
      }
      body{
        padding: 0;
        margin:0;
      }
      
      
    </style>