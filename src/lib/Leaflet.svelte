




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
  
        
  

        const icon2 = L.divIcon({
          className: '',
          html: '<div class="circle"></div>',
          iconSize: [5, 5],
          iconAnchor: [10, 10] // center of square
        });

        if(markers.length != 0){

          const pos = markers[markers.length-1].getLatLng();
        const marker3 = L.marker(moveCor(pos.lat,pos.lng,1500, 1500), {
          icon: icon2,
          draggable: true
          
        }).addTo(map);
        const index3 = markers.length 
        marker3.on('drag',() => calculateSmallPoint(index3));
        markers.push(marker3);
        // console.log(marker3)

        const marker2 = L.marker(moveCor(e.latlng.lat,e.latlng.lng,-1500, -1500), {
          icon: icon2,
          draggable: true
          
        }).addTo(map);
        const index2 = markers.length 
        marker2.on('drag',() => calculateSmallPoint(index2));
        markers.push(marker2);
        // console.log(marker2)

       

      }


        const marker = L.marker(e.latlng, {
          icon: icon,
          draggable: true
          
        }).addTo(map);
        marker.on('drag',calculateBeziere);
        markers.push(marker);
        calculateBeziere();
        // console.log(marker)
      });
  
  });
  


  function calculateSmallPoint(index){
    // console.log(index)
    // console.log(markers.length - 2) 
    if(index != 1 && index != markers.length - 2){
      
      let x = index % 3;
      console.log(x)
      if (x === 1){
        // console.log(1)
        //prev
        const sLat = markers[index].getLatLng().lat
        const sLng = markers[index].getLatLng().lng

        const mLat = markers[index - 1].getLatLng().lat
        const mLng = markers[index - 1].getLatLng().lng

        const dLat = mLat - sLat
        const dLng = mLng - sLng

        

        markers[index - 2].setLatLng([mLat + dLat, mLng + dLng]);

      }
      if (x === 2){
        //next

        const sLat = markers[index].getLatLng().lat
        const sLng = markers[index].getLatLng().lng

        const mLat = markers[index + 1].getLatLng().lat
        const mLng = markers[index + 1].getLatLng().lng

        const dLat = mLat - sLat
        const dLng = mLng - sLng

        

        markers[index + 2].setLatLng([mLat + dLat, mLng + dLng]);
      }
    }
    calculateBeziere()
  }
  // function addCircleMArker(point){
  //   const latlng = L.Projection.SphericalMercator.unproject(point);
  //   const icon = L.divIcon({
  //         className: '',
  //         html: '<div class="rotated-square"></div>',
  //         iconSize: [5, 5],
  //         iconAnchor: [10, 10] // center of square
  //       });
  
  //       const marker = L.marker(latlng, {
  //         icon: icon,
  //         draggable: true
          
  //       }).addTo(map);
  //       marker.on('dragend',calculateBeziere);
  //       markers.push(marker);
  //       // calculateBeziere();
  //       // console.log(marker)
  



      
    
  // }
  
   function calculateBeziere(){
    // alert("hoi")
    let markerposes = []
    
    markers.forEach(marker => {
      let startCoordinate =  marker.getLatLng();
    let startCoordinateMeter = L.Projection.SphericalMercator.project(startCoordinate);
    markerposes.push(startCoordinateMeter);
    }) 
  



  
    let endcoordsRaw = [];
    
    if (markerposes.length >= 4){
      
      for (let z = 0; z < (markerposes.length / 3)-1; z++){

        let w = (z * 3);
        for (let t = 0; t < 100; t++){
          let t1 = lerp(markerposes[w],markerposes[w + 1],t / 100)
          let t2 = lerp(markerposes[w+1],markerposes[w+2],t / 100)
          // console.log(w )
          // console.log(markerposes[w+3]);
          let t3 = lerp(markerposes[w+2],markerposes[w+3],t / 100)
         
          let t4 = lerp(t1,t2,t / 100);
          let t5 = lerp(t2,t3,t / 100);
          endcoordsRaw.push(lerp(t4,t5,t / 100))
        }
             
      }
    }
   
   
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
  



    // let yx = [];
    
    if (markerposes.length >= 4){
      
      for (let z = 0; z < (markerposes.length / 3)-1; z++){

        let w = (z * 3);
        for (let t = 0; t < 100; t++){
          // console.log(markerposes[w])

          let p0 = markerposes[w];
let p1 = markerposes[w + 1];
let p2 = markerposes[w + 2];
let p3 = markerposes[w + 3];


       
          let xDerivitive = 
    3 * (1 - t/100)**2 * (p1.x - p0.x) +
    6 * (1 - t/100) * (t/100) * (p2.x - p1.x) +
    3 * (t/100)**2 * (p3.x - p2.x);

let yDerivitive = 
    3 * (1 - t/100)**2 * (p1.y - p0.y) +
    6 * (1 - t/100) * (t/100) * (p2.y - p1.y) +
    3 * (t/100)**2 * (p3.y - p2.y);


          // y = ax + b 
          // b + ax = y
          // b = y - ax
         

          // let xDiff = 0;

        

          
            let x = endcoordsRaw[t].x
          let y = endcoordsRaw[t].y



          // let Xmin = x - 100
          // let Xmax = x + 100

          // let Ymin = a * Xmin + b
          // let Ymax = a * Xmax + b

          let array = calculateOffsetVar(x,y,xDerivitive,yDerivitive,100)

          // let array = [L.point(Xmin,Ymin),L.point(Xmax,Ymax)]
          let endArray = unprojectArray(array);

           L.polyline(endArray, {
            color: 'green',
            weight: 2,
           className: 'flat-line'
        }).addTo(map);
  

      


          



        
             
      }
    }
    
   
   }
  }
   function calculateRoad(){

   }
  
   function calculateOffsetVar(xs, ys, dx,dy,distance){

     let rc = dy / dx
     if (Math.abs(dy) < Math.abs(dx)){
       rc = dx / dy
     }
          // yx.push(rc)
          let a = -1 / rc
          // let a = c
          

     
      // yˆ2 + (wˆ2 -2wz + zˆ2)


          // let b = ys - a * xs

      //ABC formule
          //a:  1 + aˆ2  
          //b:  a * b
          //c:  bˆ2 - dˆ2
          // D = bˆ2 - 4 * a * c
          // x1 = (-60 + wortel(D)) / (2 * a))    
          // x2 = (-60 + wortel(D)) / (2 * a))
          // y1 = a * x1 + b
          // y2 = a * x2 + b
    let A = 1 + a**2
    let B = a 
    let C = -1 * distance**2
    let D = B**2 - 4 * A * C
    let x1 = (-B + Math.sqrt(D))/(2*A)
    let x2 = (-B - Math.sqrt(D))/(2*A)
    let y1 = a * x1 + B
    let y2 = a * x2 + B


   if (Math.abs(dy) < Math.abs(dx)){
      const z1 = x1
      const z2 = x2
      x1 = y1
      x2 = y2
      y1 = z1
      y2 = z2
    }


console.log("A:"+A)
console.log("B:"+B)
console.log("C:"+C)
    console.log("D:"+D)

    let P1 = L.point(xs + x1, ys + y1)
    let P2 = L.point(xs + x2, ys + y2)

    return [P1,P2]


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
  function moveCor(sLat, sLng, dx,dy){
            const startLatLng = L.latLng(sLat, sLng); 
            const projectedPoint = L.Projection.SphericalMercator.project(startLatLng);
            // console.log(projectedPoint)
            // Move 1500 meters east (x) and 1500 meters north (y)
            const newPoint = L.point(
            projectedPoint.x + dx,
            projectedPoint.y + dy
            );

            // Convert back to lat/lng
            const newLatLng = L.Projection.SphericalMercator.unproject(newPoint);
            return [newLatLng.lat,newLatLng.lng]
            
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