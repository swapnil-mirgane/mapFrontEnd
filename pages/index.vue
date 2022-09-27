<template>
  <!-- <input type="text" id="search" placeholder="Enter The Pin " /> -->
  <input
    id="file1"
    type="file"
    @change="upload"
    aria-label="upload image button"
  />
  <head>
    <link
      rel="stylesheet"
      href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-draw/v1.3.0/mapbox-gl-draw.css"
      type="text/css"
    />
  </head>

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
import MapboxDraw from "@mapbox/mapbox-gl-draw";

async function upload() {
  let formData = new FormData();
  var fileField = document.getElementById("file1") as HTMLInputElement | null;
  formData.append("csv", fileField.files[0]);
  await $fetch("http://localhost:3001/map/upload", {
    method: "POST",
    body: formData,
  })
    .then((res) => alert("file upload sucessfully" + res))
    .catch((err) => alert(err));
}
// async function upload(e) {

// console.log(event);

// let file1 = { file: e.target.files[0] };
// const file = e.target.files[0];
// if (!file) return;

// console.log(file);

// const readData = (f) =>
//   new Promise((resolve) => {
//     const reader = new FileReader();
//     reader.onloadend = () => resolve(reader.result);
//     reader.readAsDataURL(f);
//   });

// const data = { file: e.target.files[0] };

// // const instance = this.upload(data, {
// //   folder: "upload-examples",
// //   uploadPreset: "your-upload-preset",
// // });

// const respons = await $fetch("http://localhost:3001/map/upload", {
//   method: "POST",
//   body: data,
// })
//   .then(() => {
//     alert("file upload succefully");
//   })
//   .catch((err) => {
//     alert("file Not upload" + err);
//   });
// }

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

  /// add Marker for map click

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
        .setPopup(new mapboxgl.Popup().setHTML("<h1>Hello World!</h1>"))
        .addTo(map);
      marker.togglePopup();
    }
    new mapboxgl.Marker({
      draggable: true,
      color: `${color[colorSet]}`,
    })
      .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .setPopup(new mapboxgl.Popup().setHTML("<h1>Hello World!</h1>"))
      .addTo(map);
    marker.setOffset([0, 1]);
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

  res.forEach((element) => {
    new mapboxgl.Marker({
      color: getRandomColor(),
    })
      .setLngLat([element.Long, element.Lat])
      .addTo(map);
  });

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
      properties: {
        description: "<h2>swapnil</h2>",
      },
    },
  });
  map.addLayer({
    id: "barshi",
    source: "barshi1",
    type: "fill",
    layout: {},
    paint: { "fill-color": "pink", "fill-opacity": 0.5 },
  });

  // Add geolocate control to the map.
  map.addControl(
    new mapboxgl.GeolocateControl({
      positionOptions: {
        enableHighAccuracy: true,
      },
      // When active the map will receive updates to the device's location as it changes.
      trackUserLocation: true,
      // Draw an arrow next to the location dot to indicate which direction the device is heading.
      showUserHeading: false,
      // showAccuracyCircle: true,
    })
  );

  map.on("mouseover", () => {
    console.log("A mouseover event has occurred.");
  });

  ///////////////////////////////////////////////////////       Line Add //////////////////////////////////////////////////////////

  map.addSource("mandegaon", {
    type: "geojson",
    data: {
      type: "Feature",
      properties: {},
      geometry: {
        type: "LineString",
        coordinates: [
          [75.7245111465454, 18.32804764899319],
          [75.72592735290527, 18.330614144106967],
          [75.72571277618408, 18.3339138675723],
          [75.72386741638182, 18.336643220877654],
          [75.72150707244873, 18.33810972127523],
          [75.72039127349854, 18.338720759435702],
          [75.71996212005615, 18.337661625251446],
          [75.7203483581543, 18.337091319541262],
          [75.72047710418701, 18.336439539282953],
          [75.72073459625244, 18.33570628355506],
          [75.72004795074463, 18.33468786766262],
          [75.7126235961914, 18.33599143892991],
          [75.7073450088501, 18.331388158967503],
          [75.70674419403076, 18.32560333252606],
          [75.70910453796387, 18.32372933317727],
          [75.70996284484863, 18.32124421577696],
          [75.71129322052002, 18.32006275400308],
          [75.72399616241455, 18.327395834645976],
          [75.72429656982422, 18.32788469563667],
          [75.72412490844727, 18.327110665096683],
        ],
      },
    },
  });
  // Add a new layer to visualize the polygon.
  // data.map.addLayer({
  //     id: "pune",
  //     type: "fill",
  //     source: "pune1", // reference the data source
  //     layout: {},
  //     paint: {
  //         "fill-color": "red", //blue color fill
  //         "fill-opacity": 0.5,
  //     },
  // });
  const layer: mapboxgl.AnyLayer = {
    id: "mandegaon",
    type: "line",
    source: "mandegaon",
    layout: {
      "line-join": "round",
      "line-cap": "round",
    },
    paint: {
      "line-color": "red", //blue color fill
      "line-width": 5,
      "line-opacity": 1,
    },
  };
  map.addLayer(layer);

  ////////////////////////////////////////////////////// Draw tool                 //////////////////////

  var Draw = new MapboxDraw();
  map.addControl(Draw, "top-right");
}
</script>
<style>
#layer-change {
  position: fixed;
  top: 10px;
  left: 10px;
  z-index: 1;
}
#file1 {
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
