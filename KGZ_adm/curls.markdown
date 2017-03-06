### map 1 ###


shp2json KGZ_adm1.shp -o kg.json

geoproject 'd3.geoConicEqualArea().parallels([75, 41.5]).rotate([-80, 0]).fitSize([960, 960], d)'< kg.json > kg-d3.json

ndjson-split 'd.features' \
  < kg-d3.json \
  > kg.ndjson


ndjson-cat oblast_info.json \
  | ndjson-split 'd.slice(1)' \
  | ndjson-map '{name: d[1], density: d[4]}' \
  > oblast_info.ndjson


ndjson-join \
  kg.ndjson \
  oblast_info.ndjson \
  > kg-join.ndjson


ndjson-map 'd[0].properties = {density: d[1].density, name: d[1].name}, d[0]' \
  < kg-join.ndjson \
  > kg-join-density.ndjson




ndjson-map -r d3 \
  '(d.properties.fill = d3.scaleSequential(d3.interpolateInferno).domain([0, 100])(d.properties.density), d)' \
  < kg-join-density.ndjson \
  > kg-color.ndjson

OR

  ndjson-map -r d3 \
  '(d.properties.fill = d3.scaleSequential(d3.interpolateViridis).domain([0, 100])(d.properties.density), d)' \
  < kg-join-density.ndjson \
  > kg-color.ndjson

OR
  ndjson-map -r d3 \
  '(d.properties.fill = d3.scaleSequential(d3.interpolateCubehelixDefault).domain([0, 100])(d.properties.density), d)' \
  < kg-join-density.ndjson \
  > kg-color.ndjson


  geo2svg -n --stroke none -p 1 -w 960 -h 960 \
  < kg-color.ndjson \
  > kg-color.svg

    geo2svg -n --stroke none -p 1 -w 960 -h 960 \
  < kg.ndjson \
  > kg.svg




# Insert the legend.

5/10/20/30/40/1500/5500



tail -n +4 \
  < legend.svg \
  >> topo.svg





# MAP 2 #
shp2json KGZ_adm2.shp -o kg2.json

geoproject 'd3.geoConicEqualArea().parallels([75, 41.5]).rotate([-80, 0]).fitSize([960, 960], d)'< kg2.json > kg2-d3.json

ndjson-split 'd.id' \
  < kg2.json \
  > kg2.ndjson














ndjson-map -r d3 \
  '(d.properties.fill = d3.scaleSequential(d3.interpolateViridis).domain([0, 500])(d.properties.density), d)' \
  < kg-join-density.ndjson \
  > kg-color.ndjson


  geo2svg -n --stroke none -p 1 -w 960 -h 960 \
  < kg-color.ndjson \
  > kg-color.svg
