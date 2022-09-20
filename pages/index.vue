<template>
  <!-- <input type="text" id="search" placeholder="Enter The Pin " /> -->
  <input type="file" id="file" @change="upload" />
  <!-- <center><button @click="upload">Upload</button></center> -->
  <select id="layer-change">
    <option selected value="mapbox://styles/mapbox/streets-v11">Dark</option>
    <option value="mapbox://styles/mapbox/outdoors-v11">outdoors</option>
    <option value="mapbox://styles/mapbox/light-v10">light</option>
    <option value="mapbox://styles/mapbox/dark-v10">dark</option>
    <option value="mapbox://styles/mapbox/satellite-v9">satellite</option>
    <option value="mapbox://styles/mapbox/satellite-streets-v11">
      satellite-streets
    </option>
    <option value="mapbox://styles/mapbox/navigation-day-v1">
      navigation-day
    </option>
    <option value="mapbox://styles/mapbox/navigation-night-v1">
      navigation-night
    </option>
  </select>

  <main class="w-screen h-screen">
    <v-map class="w-full h-full" :options="data.options" @loaded="onMapLoaded">
    </v-map>
  </main>
</template>
<script setup lang="ts">
import mapboxgl from "mapbox-gl";
import VMap from "v-mapbox";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import "@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css";
let data1 = "";
async function upload() {
  let file = document.getElementById("file");
  console.log(file);
  let file1 = { file: file };
  const respons = await $fetch("http://localhost:3001/map/upload", {
    method: "POST",
    body: JSON.stringify(file1),
  })
    .then(() => {
      alert("file upload succefully");
    })
    .catch((err) => {
      alert("file Not upload" + err);
    });
}

let res: any = await $fetch("http://localhost:3001/map").catch((err) =>
  alert(err)
);

const data = reactive({
  options: {
    accessToken:
      "pk.eyJ1IjoibWF5dXJ3YWtpa2FyIiwiYSI6ImNsNmdjdGxwbjBiNGMzY282bWh0dng2c2kifQ.y-m4-zQKOeOOnDG5I1u6ng",
    style: "mapbox://styles/mapbox/outdoors-v11",
    center: [-284.8618412017822, 20.253581321936355],
    zoom: 11,
    maxZoom: 22,
    crossSourceCollisions: false,
    failIfMajorPerformanceCaveat: false,
    attributionControl: false,
    preserveDrawingBuffer: true,
    hash: false,
    minPitch: 0,
    maxPitch: 60,
    container: "map",
  } as mapboxgl.MapboxOptions,
  map: {} as mapboxgl.Map,
});

async function onMapLoaded(map: mapboxgl.Map) {
  let colorSet = 0;
  let color = [
    "051E10",
    "#F92929",
    "#21F728",
    "#D4F721",
    "#F76821",
    "#F721BE",
    "#5321F7",
    "#21F77E",
    "#051E10",
  ];

  console.log(map);
  const marker = new mapboxgl.Marker({
    draggable: true,
    color: "#000000",
  });
  marker.setLngLat([75.70610046386717, 18.333587971760853]);
  marker.addTo(map);

  map.on("click", (e) => {
    colorSet++;
    console.log(e.lngLat.lat, e.lngLat.lng);
    if (colorSet === color.length) {
      colorSet = 0;
      new mapboxgl.Marker({
        draggable: true,
        color: `${color[colorSet]}`,
      })
        .setLngLat([e.lngLat.lng, e.lngLat.lat])
        .addTo(map);
    }
    new mapboxgl.Marker({
      draggable: true,
      color: `${color[colorSet]}`,
    })
      .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .addTo(map);
  });

  const setStyle: any = document.getElementById("layer-change");
  setStyle.addEventListener("change", (event) => {
    console.log(event);
    map.setStyle(event.target.value);
  });

  //

  map.flyTo({
    center: [75.70610046386717, 18.333587971760853],
    zoom: 9,
    speed: 0.9,
    curve: 1,
    easing(t) {
      return t;
    },
  });

  if (res == !"") {
    res.forEach((element) => {
      new mapboxgl.Marker({
        color: getRandomColor(),
      })
        .setLngLat([element.Long, element.Lat])
        .addTo(map);
    });
  }

  function getRandomColor() {
    var letters = "0123456789ABCDEF";
    var color = "#";
    for (var i = 0; i < 6; i++) {
      color += letters[Math.floor(Math.random() * 16)];
    }
    return color;
  }

  (mapboxgl.accessToken =
    "pk.eyJ1IjoibWF5dXJ3YWtpa2FyIiwiYSI6ImNsNmdjdGxwbjBiNGMzY282bWh0dng2c2kifQ.y-m4-zQKOeOOnDG5I1u6ng"),
    map.addControl(
      new MapboxGeocoder({
        accessToken: mapboxgl.accessToken,
        mapboxgl: mapboxgl,
      })
    );
  map.addControl(new mapboxgl.NavigationControl());

  map.addSource("barshi1", {
    type: "geojson",
    data: {
      type: "Feature",
      geometry: {
        type: "Polygon",
        coordinates: [
          [
            [75.66352844238281, 18.213535155446717],
            [75.67623138427734, 18.21810079910634],
            [75.68944931030273, 18.219405246731313],
            [75.71159362792969, 18.22299242729397],
            [75.71159362792969, 18.228862199630495],
            [75.71674346923828, 18.240927220776484],
            [75.7148551940918, 18.24940484288711],
            [75.71090698242188, 18.25445861314665],
            [75.70747375488281, 18.25755601257177],
            [75.7009506225586, 18.260653356758375],
            [75.69494247436523, 18.261794469637948],
            [75.69116592407227, 18.258697145804437],
            [75.68532943725586, 18.25886016422559],
            [75.68103790283203, 18.259186200608813],
            [75.66936492919922, 18.25657789240474],
            [75.66438674926758, 18.252665356656156],
            [75.6624984741211, 18.247448505257417],
            [75.65820693969727, 18.237177371422433],
            [75.6573486328125, 18.228536106362327],
            [75.65631866455078, 18.22723172717752],
            [75.66352844238281, 18.213535155446717],
          ],
        ],
      },
      properties: {},
    },
  });

  map.addLayer({
    id: "barshi",
    source: "barshi1",
    type: "fill",
    layout: {},
    paint: { "fill-color": "pink", "fill-opacity": 0.5 },
  });
}
</script>
<style>
#layer-change {
  position: fixed;
  top: 10px;
  left: 10px;
  z-index: 1;
}
#file {
  width: 185px;
  position: fixed;
  top: 40px;
  right: 5px;
  z-index: 1;
}
#search {
  position: fixed;
  top: 10px;
  right: 10px;
  z-index: 1;
}
html,
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.w-screen {
  width: 100vw;
}
.h-screen {
  height: 100vh;
}
.h-full {
  height: 100%;
}
.w-full {
  width: 100%;
}
</style>
