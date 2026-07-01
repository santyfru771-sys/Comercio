<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Mapa Comercio Mundial con D3.js</title>
  <style>
    body {
      background-color: #0b0c10;
      color: #ffffff;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    svg {
      width: 100%;
      height: 600px;
      background-color: #1f2833;
    }
    .country {
      fill: #2c3e50;
      stroke: #ffffff;
      stroke-width: 0.5;
    }
    .link {
      stroke-width: 2.5;
      fill: none;
      opacity: 0.8;
    }
    .tooltip {
      position: absolute;
      background: rgba(0,0,0,0.8);
      color: #fff;
      padding: 6px;
      border-radius: 4px;
      pointer-events: none;
      font-size: 14px;
    }
  </style>
</head>
<body>
  <h1>Mapa de Comercio Mundial</h1>
  <svg id="map"></svg>
  <div id="tooltip" class="tooltip" style="opacity:0;"></div>

  <!-- Librerías -->
  <script src="https://d3js.org/d3.v7.min.js"></script>
  <script src="https://d3js.org/topojson.v3.min.js"></script>

  <script>
    const svg = d3.select("#map");
    const width = 1000, height = 600;

    const projection = d3.geoMercator()
      .scale(150)
      .translate([width/2, height/1.5]);

    const path = d3.geoPath().projection(projection);

    const tooltip = d3.select("#tooltip");

    // Colores por categoría
    const categoryColor = {
      "Alimentos": "#ff4444",
      "Tecnología": "#44ff44",
      "Energía": "#4488ff"
    };

    // Datos de ejemplo
    const trades = [
      {from: [-58, -34], to: [116, 39], category: "Alimentos", volumen: "2.3M toneladas", valor: "USD 5.4B"},
      {from: [-58, -34], to: [116, 39], category: "Tecnología", volumen: "1.1M unidades", valor: "USD 3.2B"}
    ];

    // Cargar mapa mundial
    d3.json("https://cdn.jsdelivr.net/npm/world-atlas@2/countries-110m.json").then(world => {
      const countries = topojson.feature(world, world.objects.countries);

      svg.attr("viewBox", [0,0,width,height]);

      svg.append("g")
        .selectAll("path")
        .data(countries.features)
        .join("path")
        .attr("class", "country")
        .attr("d", path);

      // Dibujar conexiones
      svg.append("g")
        .selectAll("line")
        .data(trades)
        .join("line")
        .attr("class", "link")
        .attr("x1", d => projection(d.from)[0])
        .attr("y1", d => projection(d.from)[1])
        .attr("x2", d => projection(d.to)[0])
        .attr("y2", d => projection(d.to)[1])
        .attr("stroke", d => categoryColor[d.category])
        .on("mouseover", (event, d) => {
          tooltip.style("opacity", 1)
                 .html(`<strong>${d.category}</strong><br>Volumen: ${d.volumen}<br>Valor: ${d.valor}`)
                 .style("left", (event.pageX+10)+"px")
                 .style("top", (event.pageY-20)+"px");
        })
        .on("mouseout", () => tooltip.style("opacity", 0));
    });
  </script>
</body>
</html>
