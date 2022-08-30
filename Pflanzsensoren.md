# Pflanzsensoren

## Was benötige ich alles?

1. Pflanzsensor: https://amzn.to/3R2lta1
2. Plantcard: https://github.com/Yolandavdvegt/lovelace-flower-card

## Config Einträge

### Configuration.yaml
```
plant: !include plant.yaml
```

### plant.yaml 

```
Efeu:
  sensors:
    moisture: sensor.plant_2_moisture
    temperature: sensor.plant_2_temperature
    conductivity: sensor.plant_2_soil_conductivity
    brightness: sensor.plant_2_illuminance
  min_brightness: 500
  max_brightness: 45000
  min_temperature: 10
  max_temperature: 35
  min_moisture: 15
  max_moisture: 60
  min_conductivity: 350
  max_conductivity: 2000
```

## Youtube

https://www.youtube.com/watch?v=FvqQG4ZbYz0