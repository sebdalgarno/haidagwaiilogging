<script>
  import { onMount, onDestroy } from "svelte";
  import { mapbox, key } from "../mapbox.js";
  import Button from "@smui/button";
  import {
    year_min,
    year_max,
    tileset_logging,
    // tileset_wsheds,
    // tileset_parks,
    // tileset_wsheds_lines,
    // tileset_parks_lines,
    coordinates,
    source_layer,
    mapbox_style,
    // tileset_base,
    // tileset_feature_labels,
    // tileset_parks,
    base_colors
  } from "../consts";

  export let year = 1900;
  export let single = false;
  export let secondgrowth = false;
  export let map_palette;
  export let map_palette_sg;
  export let map_palette_single;
  export let bounds;

  // let map_palette_sg = ["true", "white", "false", "black"];

  $: if (typeof map !== "undefined" && done) {
    filterLoggedAreas(year, single, secondgrowth);
  }

  let done = false;

  let fill_opacity = 0.75;

  let container;
  let map;
  let hoveredStateId = null;

  function filterSingle() {
    map.setFilter("logged_simp", ["==", "YearHarvested", year]);
    map.setFilter("logged", ["==", "YearHarvested", year]);
  }

  function filterAccumulate() {
    map.setFilter("logged_simp", ["<=", "YearHarvested", year]);
    map.setFilter("logged", ["<=", "YearHarvested", year]);
  }

  let paint_property_secondgrowth = year => {
    return [
      "match",
      ["string", ["get", "SecondGrowth"]],
      ...map_palette_sg,
      [
        "match",
        ["-", year, ["number", ["get", "YearHarvested"]]],
        ...map_palette,
        "#AAAAAA"
      ]
    ];
  };

  let paint_property_secondgrowth_single = year => {
    return [
      "match",
      ["string", ["get", "SecondGrowth"]],
      ...map_palette_sg,
      map_palette_single
    ];
  };

  let paint_property = year => {
    return [
      "match",
      ["-", year, ["number", ["get", "YearHarvested"]]],
      ...map_palette,
      "#AAAAAA"
    ];
  };

  function setPalette(year) {
    map.setPaintProperty("logged_simp", "fill-color", paint_property(year));
    map.setPaintProperty("logged", "fill-color", paint_property(year));
  }

  function setPaletteSingle(year) {
    map.setPaintProperty("logged_simp", "fill-color", map_palette_single);
    map.setPaintProperty("logged", "fill-color", map_palette_single);
  }

  function setPaletteSecondgrowth(year) {
    map.setPaintProperty(
      "logged_simp",
      "fill-color",
      paint_property_secondgrowth(year)
    );
    map.setPaintProperty(
      "logged",
      "fill-color",
      paint_property_secondgrowth(year)
    );
  }

  function setPaletteSecondgrowthSingle(year) {
    map.setPaintProperty(
      "logged_simp",
      "fill-color",
      paint_property_secondgrowth_single(year)
    );
    map.setPaintProperty(
      "logged",
      "fill-color",
      paint_property_secondgrowth_single(year)
    );
  }

  function filterLoggedAreas(year, single, secondgrowth) {
    if (single && secondgrowth) {
      setPaletteSecondgrowthSingle();
      filterSingle();
    } else if (single && !secondgrowth) {
      setPaletteSingle();
      filterSingle();
    } else if (!single && secondgrowth) {
      setPaletteSecondgrowth(year);
      filterAccumulate();
    } else {
      setPalette(year);
      filterAccumulate();
    }
  }
  // function filterSingle() {
  //   map.setFilter("logged", ["==", "year", year]);

  // }

  // function filterAccumulate() {
  //   map.setFilter("logged", ["<=", "year", year]);
  // }

  // let paint_property = year => {
  //   return [
  //       "match",
  //       ["-", year, ["number", ["get", "year"]]],
  //       ...map_palette,
  //       map_palette[1]
  //   ];
  // };

  // let paint_property_single = year => {
  //   return [
  //     map_palette_single
  //     ]
  // };

  // let paint_property_secondgrowth = year => {
  //   return [
  //     "match",
  //     ["string", ["get", "SecondGrowth"]],
  //     ...map_palette_sg,
  //     [
  //       "match",
  //       ["-", year, ["number", ["get", "YearHarvested"]]],
  //       ...map_palette,
  //       "#AAAAAA"
  //     ]
  //   ];
  // };

  // function setPalette(year) {
  //   map.setPaintProperty("logged", "fill-color", paint_property(year));
  // }

  // function setPaletteSingle(year) {
  //   map.setPaintProperty("logged", "fill-color", paint_property_single(year));
  // }

  // function filterLoggedAreas(year, single) {
  //   if (single) {
  //     setPaletteSingle();
  //     filterSingle();
  //   } else {
  //     setPalette(year);
  //     filterAccumulate();
  //   }
  // }

  onMount(async () => {
    map = new mapbox.Map({
      container,
      style: mapbox_style,
      center: coordinates,
      zoom: 5,
      bearing: 10,
      attributionControl: false,
      logoPosition: "bottom-right"
    });

    map.on("load", function() {
      // map.addSource("basemap", {
      //   type: "raster",
      //   url: tileset_base
      // });
      // map.addLayer({
      //   id: "overlay",
      //   source: "basemap",
      //   type: "raster",
      //   beforeLayer: "logged_simp",
      //   paint: {
      //     "raster-opacity": 1
      //   }
      // });

      map.addSource("logged", {
        type: "vector",
        url: tileset_logging,
        promoteId: "id"
      });
      map.addLayer({
        id: "logged_simp",
        source: "logged",
        "source-layer": source_layer[0],
        type: "fill",
        filter: ["==", "YearHarvested", year],
        paint: {
          "fill-color": [
            "match",
            ["-", year, ["number", ["get", "YearHarvested"]]],
            ...map_palette,
            "#AAAAAA"
          ]
        },
        "fill-opacity": fill_opacity
      });
      map.addLayer({
        id: "logged",
        source: "logged",
        "source-layer": source_layer[1],
        type: "fill",
        filter: ["==", "YearHarvested", year],
        paint: {
          "fill-color": [
            "match",
            ["-", year, ["number", ["get", "YearHarvested"]]],
            ...map_palette,
            "#AAAAAA"
          ]
        },
        "fill-opacity": fill_opacity
      });
     
//       map.addSource("logged", {
//         type: "vector",
//         url: tileset_logging,
//         promoteId: "id"
//       });
//       map.addLayer({
//         id: "logged",
//         source: "logged",
//         "source-layer": "logging",
//         type: "fill",
//         filter: ["==", "year", year],
//         paint: {
//           'fill-opacity': [
// 'case',
// ['boolean', ['feature-state', 'hover'], false],
// 0.5,
// 1]}
//       });
      map.addControl(new mapbox.AttributionControl(), "bottom-right");
      map.fitBounds(bounds);
      done = true;
    });

    var popup = new mapbox.Popup({
      closeButton: false,
      closeOnClick: false
    });

        /////// hover popups for logging areas
    map.on("mousemove", "logged", function(e) {
      // Change the cursor style as a UI indicator.
      map.getCanvas().style.cursor = "pointer";

      var coordinates = e.lngLat;

      var description = `
      <p class='inline-block'>${e.features[0].properties.description}</p>`;
      // Ensure that if the map is zoomed out such that multiple
      // copies of the feature are visible, the popup appears
      // over the copy being pointed to.
      // while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
      //   coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
      // }

      // Populate the popup and set its coordinates
      // based on the feature found.
      popup
        .setLngLat(coordinates)
        .setHTML(description)
        .addTo(map);
    });

    map.on('mousemove', 'logged', function (e) {
if (e.features.length > 0) {
if (hoveredStateId) {
map.setFeatureState(
{ source: 'logged', id: hoveredStateId, sourceLayer: 'logging' },
{ hover: false }
);
}
hoveredStateId = e.features[0].id;
map.setFeatureState(
{ source: 'logged', id: hoveredStateId, sourceLayer: 'logging'  },
{ hover: true }
);
}
});

    map.on("mouseleave", "logged", function() {
      map.getCanvas().style.cursor = "";
      popup.remove();
      if (hoveredStateId) {
map.setFeatureState(
{ source: 'logged', id: hoveredStateId, sourceLayer: 'logging'  },
{ hover: false }
);
}
hoveredStateId = null;
    });
  });
</script>

<style>
  .map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
    z-index: 0.1;
  }

  .mapbox-popup {
    z-index: 100;
    max-width: 400px;
    font: 12px/20px "Helvetica Neue", Arial, Helvetica, sans-serif;
  }
</style>

<div class="map bg-black" bind:this={container}>
  <slot />
</div>
