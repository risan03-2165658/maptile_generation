# maptile_generation
<!DOCTYPE html>
<html>

<head>
    <title>Map Design</title>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="https://api.mapbox.com/mapbox-gl-js/v2.8.1/mapbox-gl.css" rel="stylesheet">

    <style>
        body {
            margin: 0;
            padding: 0;
        }

        #map {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
        }

        /* the style of the layer control menu */
        #menu {
            background: #fff;
            position: absolute;
            z-index: 1;
            top: 10px;
            right: 10px;
            border-radius: 3px;
            width: 120px;
            border: 1px solid rgba(0, 0, 0, 0.4);
            font-family: 'Open Sans', sans-serif;
        }

        /* each layer on the menu */
        #menu a {
            font-size: 13px;
            color: #404040;
            display: block;
            margin: 0;
            padding: 0;
            padding: 10px;
            text-decoration: none;
            border-bottom: 1px solid rgba(0, 0, 0, 0.25);
            text-align: center;
        }

        /* the last item of the layer items will not include a seperator */
        #menu a:last-child {
            border: none;
        }

        /* change color and background color while a pointer is on an hybrid link. */
        #menu a:hover {
            background-color: #f8f8f8;
            color: #404040;
        }

        #menu a.active {
            background-color: #3887be;
            color: #ffffff;
        }

        #menu a.active:hover {
            background: #3074a4;
        }
    </style>
    <script src="https://api.mapbox.com/mapbox-gl-js/v2.8.1/mapbox-gl.js"></script>
</head>

<body>
    <nav id="menu"></nav>
    <div id='map'></div>
    <script type="text/javascript">
        mapboxgl.accessToken =
            'pk.eyJ1IjoiamFrb2J6aGFvIiwiYSI6ImNpcms2YWsyMzAwMmtmbG5icTFxZ3ZkdncifQ.P9MBej1xacybKcDN_jehvw';
        let map = new mapboxgl.Map({
            container: 'map', // container ID
            style: 'mapbox://styles/mapbox/dark-v10',
            zoom: 12, // starting zoom
            center: [-122.2559435, 47.6002614] // starting center
        });

        map.on('load', () => { //simplifying the function statement: arrow with brackets to define a function

            map.addSource('uw-tiles', {
                'type': 'raster',
                'tiles': [
                    'assets/uw/{z}/{x}/{y}.png'
                ],
                'tileSize': 256,
                'attribution': 'Map tiles designed by Bo Zhao</a>'
            });

            map.addSource('lgbtq-tiles', {
                'type': 'raster',
                'tiles': [
                    'assets/lgbtq/{z}/{x}/{y}.png'
                ],
                'tileSize': 256,
                'attribution': 'Map tiles designed by Bo Zhao'
            });

            map.addLayer({
                'id': 'uw',
                'type': 'raster',
                'layout': {
                    'visibility': 'none'
                },
                'source': 'uw-tiles'
            });

            map.addLayer({
                'id': 'lgbtq',
                'type': 'raster',
                'layout': {
                    'visibility': 'none'
                },
                'source': 'lgbtq-tiles'
            });

        });


        // After the last frame rendered before the map enters an "idle" state.
        map.on('idle', () => {
            // If these two layers were not added to the map, abort
            if (!map.getLayer('uw') || !map.getLayer('lgbtq')) {
                return;
            }

            // Enumerate ids of the layers.
            const toggleableLayerIds = ['uw', 'lgbtq'];

            // Set up the corresponding toggle button for each layer.
            for (const id of toggleableLayerIds) {
                // Skip layers that already have a button set up.
                if (document.getElementById(id)) {
                    continue;
                }

                // Create a link.
                const link = document.createElement('a');
                link.id = id;
                link.href = '#';
                link.textContent = id;
                link.className = 'inactive';

                // Show or hide layer when the toggle is clicked.
                link.onclick = function (e) {
                    const clickedLayer = this.textContent;
                    // preventDefault() tells the user agent that if the event does not get explicitly handled, 
                    // its default action should not be taken as it normally would be.
                    e.preventDefault();
                    // The stopPropagation() method prevents further propagation of the current event in the capturing 
                    // and bubbling phases. It does not, however, prevent any default behaviors from occurring; 
                    // for instance, clicks on links are still processed. If you want to stop those behaviors, 
                    // see the preventDefault() method.
                    e.stopPropagation();

                    const visibility = map.getLayoutProperty(
                        clickedLayer,
                        'visibility'
                    );

                    // Toggle layer visibility by changing the layout object's visibility property.
                    // if it is currently visible, after the clicking, it will be turned off.
                    if (visibility === 'visible') {
                        map.setLayoutProperty(clickedLayer, 'visibility', 'none');
                        this.className = '';
                    } else { //otherise, it will be turned on.
                        this.className = 'active';
                        map.setLayoutProperty(
                            clickedLayer,
                            'visibility',
                            'visible'
                        );
                    }
                };

                // in the menu place holder, insert the layer links.
                const layers = document.getElementById('menu');
                layers.appendChild(link);
            }
        });
    </script>
</body>

</html>