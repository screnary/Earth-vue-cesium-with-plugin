<template>
  <!-- <h1>world</h1> -->
  <section>
    <!-- <input type="file" @change="loadkml" /> -->
    <div id="cesiumContainer"></div>
  </section>
</template>

<script>
export default {
  name: "earth",
  data() {
    return {
      kmldata: null,
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      console.log("start");
      Cesium.Ion.defaultAccessToken =
        "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJkMzZlNDhkNi1kNzBkLTRkOTctYjNjNi1lOWMxMGQwMDNlZDMiLCJpZCI6NzIxMzksImlhdCI6MTYzNTgzMDY5M30.pcO7OsxlM3nSzKOl3AYsRhoT8UYqqDnmVUU2iu2MXQ8";
      var viewer = new Cesium.Viewer("cesiumContainer", {
        scene3DOnly: true,
        selectionIndicator: false,
        baseLayerPicker: false,
      });

      // // Remove default base layer
      // viewer.imageryLayers.remove(viewer.imageryLayers.get(0));
      // // Add Sentinel-2 imagery
      viewer.imageryLayers.addImageryProvider(
        new Cesium.IonImageryProvider({ assetId: 3954 })
      );
      // // Add ArcGIS imagery
      // viewer.imageryLayers.addImageryProvider( new Cesium.ArcGisMapServerImageryProvider({
      //       url : '//services.arcgisonline.com/ArcGIS/rest/services/World_Street_Map/MapServer'
      //   }));

      // Load Cesium World Terrain
      viewer.terrainProvider = Cesium.createWorldTerrain({
        requestWaterMask: true,
        requestVertexNormals: true,
      });
      // Enable depth testing so things behind the terrain disappear
      viewer.scene.globe.depthTestAgainstTerrain = true;
      // Enable lighting based on sun/moon positions
      viewer.scene.globe.enableLighting = true;

      // Create an initial camera view
      var initialPosition = new Cesium.Cartesian3.fromDegrees(
        -73.998114468289017509,
        40.674512895646692812,
        2631.082799425431
      );
      var initialOrientation = new Cesium.HeadingPitchRoll.fromDegrees(
        7.1077496389876024807,
        -31.987223091598949054,
        0.025883251314954971306
      );
      var homeCameraView = {
        destination: initialPosition,
        orientation: {
          heading: initialOrientation.heading,
          pitch: initialOrientation.pitch,
          roll: initialOrientation.roll,
        },
      };
      // Set the initial view
      viewer.scene.camera.setView(homeCameraView);

      // Add some camera flight animation options
      homeCameraView.duration = 2.0;
      homeCameraView.maximumHeight = 2000;
      homeCameraView.pitchAdjustHeight = 2000;
      homeCameraView.endTransform = Cesium.Matrix4.IDENTITY;
      // Override the default home button
      viewer.homeButton.viewModel.command.beforeExecute.addEventListener(
        function (e) {
          e.cancel = true;
          viewer.scene.camera.flyTo(homeCameraView);
        }
      );

      // Setup clock and timeline
      viewer.clock.shouldAnimate = true; // make the animation play when the viewer starts
      viewer.clock.startTime = Cesium.JulianDate.fromIso8601(
        "2021-11-02T16:00:00Z"
      );
      viewer.clock.stopTime = Cesium.JulianDate.fromIso8601(
        "2021-11-02T20:20:00Z"
      );
      viewer.clock.currentTime = Cesium.JulianDate.fromIso8601(
        "2021-11-02T16:00:00Z"
      );
      viewer.clock.multiplier = 2; // sets a speedup
      viewer.clock.clockStep = Cesium.ClockStep.SYSTEM_CLOCK_MULTIPLIER; //tick computation mode
      viewer.clock.clockRange = Cesium.ClockRange.LOOP_STOP; // loop at the end
      viewer.timeline.zoomTo(viewer.clock.startTime, viewer.clock.stopTime);

      // Load entities from a KML file
      var kmlOptions = {
        camera: viewer.scene.camera,
        canvas: viewer.scene.canvas,
        clampToGround: true,
      };
      // var geocachePromise = Cesium.KmlDataSource.load( // use function to load kml data
      //   // "../Source/SampleData/sampleGeocacheLocations.kml",
      //   this.kmldata,
      //   // new Blob([this.kmldata]),
      //   kmlOptions
      // );

      var geocachePromise = Cesium.KmlDataSource.load(
        "Source/SampleData/sampleGeocacheLocations.kml",
        kmlOptions
      );

      // Add geocache billboard entities to scene and style them
      geocachePromise.then(function (dataSource) {
        console.log(dataSource);
        // Add the new data as entities to the viewer
        viewer.dataSources.add(dataSource);
      });

      // var tileset = viewer.scene.primitives.add(
      //   new Cesium.Cesium3DTileset({
      //     url: Cesium.IonResource.fromAssetId(96188),
      //   })
      // );

      // var tileset2 = viewer.scene.primitives.add(
      //   new Cesium.Cesium3DTileset({
      //     url: Cesium.IonResource.fromAssetId(660861),
      //   })
      // );
      // viewer.zoomTo(tileset2);
    },

    loadkml(e) {
      // console.log(e);
      // convert blob as data url directly, using window tools
      let fileurl = window.URL.createObjectURL(e.target.files[0])
      console.log(fileurl);
      this.kmldata = fileurl;
      this.init();

      // let reader = new FileReader();
      // let file = reader.readAsDataURL(e.target.files[0]);
      // reader.onload = () => {
      //   // console.log(reader.result);
      //   let objblob = new Blob([reader.result]); // convert url as blob
      //   let objurl = window.URL.createObjectURL(objblob); // convert blob to data url (server)
      //   console.log(objurl); // This will cause 跨域错误!!!!!
      //   this.kmldata = objurl;
      //   this.init();
      // };
    },
  },
};
</script>

<style>
#cesiumContainer {
  width: 100%;
  height: 100vh;
}
</style>
