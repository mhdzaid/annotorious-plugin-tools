<script lang="ts">
  import { Editor, Handle } from '@annotorious/annotorious/src';
  import type { Shape, Transform, Polygon, PolygonGeometry } from '@annotorious/annotorious';
  import { onMount } from 'svelte';
  import { boundsFromPoints, ShapeType, type Polygon } from '@annotorious/annotorious';


  /** Props */
  export let shape: Polygon;
  export let computedStyle: string | undefined;
  export let transform: Transform;
  export let viewportScale: number = 1;

  $: geom = shape.geometry;

  // Function to handle editing logic
  const editor = (polygon: Shape, handle: string, delta: [number, number]) => {
    let points: [number, number][];

    const geom = (polygon.geometry) as PolygonGeometry;

    if (handle === 'SHAPE') {
      // Move the entire shape
      points = geom.points.map(([x, y]) => [x + delta[0], y + delta[1]]);
    }

    return {
      ...polygon,
      geometry: {
          bounds: boundsFromPoints(points),
          points: points,
        }
    };
  };

</script>

<Editor
  shape={shape}
  transform={transform}
  editor={editor}
  on:change
  on:grab
  on:release
  let:grab={grab}>

  <!-- Clickable area for selecting shape -->
  <polygon
    class="a9s-outer"
    style={computedStyle ? 'display:none;' : undefined}
    on:pointerdown={grab('SHAPE')}
    points={geom.points.map(xy => xy.join(',')).join(' ')} />

  <!-- Editable Polyline -->
  <polygon
    class="a9s-inner a9s-shape-handle"
    style={computedStyle}
    on:pointerdown={grab('SHAPE')}
    points={geom.points.map(xy => xy.join(',')).join(' ')} />

</Editor>
