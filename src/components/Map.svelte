<script lang="ts">
    import { onMount } from "svelte";
    import maplibregl, { type StyleSpecification } from "maplibre-gl";
    import 'maplibre-gl/dist/maplibre-gl.css';
    import MapToolbar from "./MapToolbar.svelte";

    let mapStyle: StyleSpecification = {
        "version": 8,
        "sources": {
            "osm": {
                "type": "raster",
                "tiles": [
                    "https://tile.openstreetmap.org/{z}/{x}/{y}.png"
                ],
                "tileSize": 256
            }
        },
        "layers": [
            {
                "id": "osm",
                "type": "raster",
                "source": "osm"
            }
        ]
    };
    let map: maplibregl.Map;

    onMount(() => {
        map = new maplibregl.Map({
            container: 'map',
            center: [0, 0],
            style: mapStyle,
            zoom: 1
        });
    })
</script>

<div class="skeleton-row">
    {#if map}
        <MapToolbar map={map} />
    {/if}
    <div id="map"> </div>
</div>

<style>
    .skeleton-row {
        height:100%;
    }
    #map {
        height: 100%;
    }
</style>