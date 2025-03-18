<script lang="ts">
    import { onMount } from "svelte";
    import maplibregl, { type StyleSpecification } from "maplibre-gl";
    import 'maplibre-gl/dist/maplibre-gl.css';
    import MapToolbar from "./MapToolbar.svelte";

    let sessionId: string;
    let mapStyle: StyleSpecification;
    let map: maplibregl.Map;

    onMount(() => {
        if (!(document.cookie.split(';').filter(function(item) {
            return item.trim().indexOf('SessionId=') == 0
        }).length)) {
            let currentDate = new Date();
            let time = currentDate.getTime();
            let expireTime = time + 1000*3600;
            currentDate.setTime(expireTime)

            sessionId = crypto.randomUUID();
            document.cookie = `SessionId=${sessionId};expires=${currentDate.toUTCString()}`;
            console.log(`Set new SessionId: ${sessionId}`)
        }
        else {
            sessionId  = document.cookie.replace(
                /(?:(?:^|.*;\s*)SessionId\s*\=\s*([^;]*).*$)|^.*$/,
                "$1",
            ); 
        }
        console.log(`Current SessionId: ${sessionId}`)

        mapStyle = {
            "version": 8,
            "sources": {
                "osm": {
                    "type": "raster",
                    "tiles": [
                        `https://localhost:7196/TileProxy?z={z}&x={x}&y={y}&sessionId=${sessionId}`
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