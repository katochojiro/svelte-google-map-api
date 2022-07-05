<script>
  import { onMount, afterUpdate } from "svelte";
  import { loadGooglePlacesLibrary } from "./loader.js";
  import GooglePlacesAutocomplete from "./GooglePlacesAutocomplete.svelte";
  import Slider from "@smui/slider";
  let distance = 10;
  let map = null,
    myCity = null,
    marker = null;

  export const apiKey = "AIzaSyB5HYrOwNUgeMxMWUx3QGp8fev-PWjFoYw"; // MAP API KEY

  onMount(() => {
    loadGooglePlacesLibrary(apiKey, () => {
      function loadMap() {
        var mapOptions = {
          center: new google.maps.LatLng(17.609993, 83.221436),
          zoom: 10,
          mapTypeId: google.maps.MapTypeId.ROADMAP,
        };
        map = new google.maps.Map(
          document.getElementById("sample"),
          mapOptions
        );
        myCity = new google.maps.Circle({
          center: new google.maps.LatLng(17.609993, 83.221436),
          radius: distance * 1000,
          strokeColor: "#1890FF",
          strokeOpacity: 0.6,
          strokeWeight: 2,
          fillColor: "#1890FF",
          fillOpacity: 0.6,
        });
        marker = new google.maps.Marker({
          position: new google.maps.LatLng(17.609993, 83.221436),
          title: "My Location",
        });
        marker.setMap(map);
        myCity.setMap(map);
      }
      window.addEventListener("load", loadMap);
    });
  });
  afterUpdate(() => {
    if (myCity) myCity.setRadius(distance * 1000);
  });
</script>

<main style="padding: 40px;">
  <div class="row">
    <div class="col-md-3">
      Advertise near an address
      <GooglePlacesAutocomplete
        {apiKey}
        placeholder="Enter a location."
        on:place_changed={(data) => {
          if (data.detail.place.geometry.location) {
            map.setCenter(data.detail.place.geometry.location);
            marker.setPosition(data.detail.place.geometry.location);
            myCity.setCenter(data.detail.place.geometry.location);
          }
        }}
      />
      <div class="slider-wrapper" style="margin-top: 20px;">
        <Slider
          bind:value={distance}
          min={5}
          max={65}
          step={1}
          discrete
          tickMarks
          input$aria-label="Range Slider"
        />
        <div
          style="display: flex; justify-content: space-between; width: 100%;"
        >
          <span class="label-left"> 5km </span>
          <span class="label-right"> 65km </span>
        </div>
      </div>
    </div>
    <div class="col-md-9">
      <div id="sample" style="width:100%; height:600px;" />
    </div>
  </div>
  <div class="row" style="margin-top: 30px;">
    <h3 style="text-align: center;">People interested in these locations.</h3>
    <div class="col-md-12" />
  </div>
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  }
</style>
