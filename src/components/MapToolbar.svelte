<script lang="ts">
    import { AppBar } from '@skeletonlabs/skeleton-svelte';
    import { RectangleHorizontal, Dot, Waypoints, Circle, Pencil, Hand, Trash2 } from 'lucide-svelte';
    import { type MapMouseEvent } from 'maplibre-gl';
    import { onMount } from 'svelte';
    import { TerraDrawCircleMode, TerraDrawFreehandMode, TerraDrawLineStringMode, TerraDrawPointMode, TerraDrawRectangleMode, TerraDrawSelectMode, TerraDraw } from 'terra-draw';
    import { TerraDrawMapLibreGLAdapter } from 'terra-draw-maplibre-gl-adapter';

    let { map }  = $props();
    let draw: TerraDraw;

    let selectedFeatureId: string | number;

    onMount(() => {
        map.on('click', (event: MapMouseEvent) => {
            const snapshot = draw.getSnapshot();
            let currentFeature = snapshot.find((feature) => feature.properties.selected === true)?.id;

            if (currentFeature) {
                selectedFeatureId = currentFeature
            }
        })

        draw = new TerraDraw({
            adapter: new TerraDrawMapLibreGLAdapter({ map }),

            modes: [new TerraDrawPointMode(),
                    new TerraDrawLineStringMode(),
                    new TerraDrawRectangleMode(),
                    new TerraDrawCircleMode(),
                    new TerraDrawFreehandMode(),
                    new TerraDrawSelectMode({
                        allowManualDeselection: true, 
                        flags: {
                            point: {
                            feature: {
                                draggable: true,
                            },
                            },
                            polygon: {
                            feature: {
                                draggable: true,
                                coordinates: {
                                midpoints: true,
                                draggable: true,
                                deletable: true,
                                },
                            },
                            },
                            linestring: {
                            feature: {
                                draggable: true,
                                coordinates: {
                                midpoints: true,
                                draggable: true,
                                deletable: true,
                                },
                            },
                            },
                            freehand: {
                            feature: {
                                draggable: true,
                                coordinates: {
                                midpoints: true,
                                draggable: true,
                                deletable: true,
                                },
                            },
                            },
                            circle: {
                            feature: {
                                    draggable: true,
                                    coordinates: {
                                    midpoints: true,
                                    draggable: true,
                                    deletable: true,
                                    },
                                },
                            },
                            rectangle: {
                            feature: {
                                    draggable: true,
                                    coordinates: {
                                    midpoints: true,
                                    draggable: true,
                                    deletable: true,
                                    },
                                },
                            },
                        },
                        styles: {
                        },
                    })
                ],
        });
        draw.start();
        draw.setMode("rectangle");
        draw.on('select', (id) => {
            const snapshot = draw.getSnapshot()
            const polygon = snapshot.find((feature) => feature.id === id)
            console.log(polygon)
        })
    })
</script>
  

<AppBar>
    {#snippet lead()}
        <span>Geojson Drawer</span>
    {/snippet}
    {#snippet trail()}
        <nav class="btn-group">
            <button type="button" class="btn btn-sm" onclick={() => draw.setMode('point')}>
                <Dot size={20} />
            </button>
            <button type="button" class="btn btn-sm" onclick={() => draw.setMode('linestring')}>
                <Waypoints size={20} />
            </button>
            <button type="button" class="btn btn-sm" onclick={() => draw.setMode('rectangle')}>
                <RectangleHorizontal size={20} />
            </button>
            <button type="button" class="btn btn-sm" onclick={() => draw.setMode('circle')}>
                <Circle size={20} />
            </button>
            <button type="button" class="btn btn-sm" onclick={() => draw.setMode('freehand')}>
                <Pencil size={20} />
            </button>
            <button type="button" class="btn btn-sm" onclick={() => draw.setMode('select')}>
                <Hand size={20} />
            </button>
            <button type="button" class="btn btn-sm" onclick={() => draw.removeFeatures(new Array(selectedFeatureId))}>
                <Trash2 size={20} />
            </button>
        </nav>
    {/snippet}
</AppBar>