![image](https://user-images.githubusercontent.com/47699362/186652791-6984a151-1fc0-4039-aaea-4ffae5dc564f.png)

# Abfallkalender

## Was benötige ich alles aus HACS?

1. Integration
  1. Waste Collection Schedule 
2. Frontends
  1. Button Card
  2. Banner Card
  3. Card Mod
  4. Layout Card

## iCal Datei

Diese Datei findest meistens auf der Internetseite deines Mülldienstes.
Falls du genau soviel Pech wie ich hast kannst du dir eine solche Datei relativ einfach auf 
https://ical.marudot.com/ selbst erstellen.

Zu speichern ist diese Datei unter /config/www/

## Config Einträge

### Configuration.yaml

Beachte hier die ```aliase``` diese müssen mit den Daten in deiner ICS übereinstimmen.

```
waste_collection_schedule:
  sources:
    - name: ics
      args:
        file: "www/muell.ics"
      customize:
        - type: Restabfall
          alias: Restabfall
          icon: mdi:trash-can
        - type: Papiertonne
          alias: Papiertonne
          icon: mdi:trash-can
        - type: Bioabfall
          alias: Bioabfall
          icon: mdi:trash-can
        - type: Plastikabfall
          alias: Plastikabfall
          icon: mdi:trash-can

  fetch_time: "04:00"
  day_switch_time: "10:00"
```

### Sensor.yaml 
(ich hoffe du hast dir diese erstellt ansonsten einfach erstellen und mit ```sensor: !include sensor.yaml```` in der configuration.yaml einbinden)

```
########################## Müllabfuhr ###########################

- platform: waste_collection_schedule
  name: Papierabfall_date
  value_template: '{{value.date.strftime("%d.%m.%Y")}}'
  types:
    - Papiertonne
- platform: waste_collection_schedule
  name: Papierabfall_collection
  value_template: "{{value.daysTo}}"
  types:
    - Papiertonne

- platform: waste_collection_schedule
  name: Restmuelltonne_date
  value_template: '{{value.date.strftime("%d.%m.%Y")}}'
  types:
    - Restabfall
- platform: waste_collection_schedule
  name: Restmuelltonne_collection
  value_template: "{{value.daysTo}}"
  types:
    - Restabfall

- platform: waste_collection_schedule
  name: Plastiktonne_date
  value_template: '{{value.date.strftime("%d.%m.%Y")}}'
  types:
    - Plastikabfall
- platform: waste_collection_schedule
  name: Plastikmuelltonne_collection
  value_template: "{{value.daysTo}}"
  types:
    - Plastikabfall

- platform: waste_collection_schedule
  name: Biotonne_date
  value_template: '{{value.date.strftime("%d.%m.%Y")}}'
  types:
    - Bioabfall
- platform: waste_collection_schedule
  name: Biotonne_collection
  value_template: "{{value.daysTo}}"
  types:
    - Bioabfall

- platform: waste_collection_schedule
  name: next_waste_collection_daysto
  details_format: upcoming
  value_template: '{{value.types|join(", ")}} in {{value.daysTo}} Tagen'

  #button-card#
- platform: waste_collection_schedule
  name: MyButtonCardSensor
  value_template: '{{value.types|join(", ")}}|{{value.daysTo}}|{{value.date.strftime("%d.%m.%Y")}}|{{value.date.strftime("%a")}}'
```

## Karte fürs Dashboard

```
type: entities
entities:
  - entity: sensor.restmuelltonne_date
    style: |
      :host {
        color: grey;
      }      
    icon: mdi:delete-empty
    show_state: false
    type: custom:multiple-entity-row
    name: Restmüll
    secondary_info: false
    entities:
      - entity: sensor.restmuelltonne_collection
        name: Abholung in
        unit: Tage(n)
      - entity: sensor.restmuelltonne_date
        name: Datum
  - entity: sensor.biotonne_date
    style: |
      :host {
        color: brown;
      }  
    icon: mdi:bio
    show_state: false
    type: custom:multiple-entity-row
    name: Bio Müll
    secondary_info: false
    entities:
      - entity: sensor.biotonne_collection
        name: Abholung in
        unit: Tage(n)
      - entity: sensor.biotonne_date
        name: Datum
  - entity: sensor.papierabfall_date
    style: |
      :host {
        color: turquoise
      }  
    icon: mdi:tree
    show_state: false
    type: custom:multiple-entity-row
    name: Papiertonne
    secondary_info: false
    entities:
      - entity: sensor.papierabfall_collection
        name: Abholung in
        unit: Tage(n)
      - entity: sensor.papierabfall_date
        name: Datum
  - entity: sensor.papierabfall_date
    style: |
      :host {
        color: yellow
      }  
    icon: mdi:recycle
    show_state: false
    type: custom:multiple-entity-row
    name: Plastik Sack
    secondary_info: false
    entities:
      - entity: sensor.plastikmuelltonne_collection
        name: Abholung in
        unit: Tage(n)
      - entity: sensor.plastiktonne_date
        name: Datum
  - entity: sensor.mybuttoncardsensor
    type: custom:button-card
    layout: icon_name_state2nd
    show_label: true
    label: |
      [[[
       var days_to = entity.state.split("|")[1]
       if (days_to == 0)
       { return "Heute" }
       else if (days_to == 1)
       { return "Morgen" }
       else
       { return "in " + days_to + " Tagen" }
      ]]]
    show_name: true
    name: |
      [[[
        return entity.state.split("|")[0]
      ]]]
    state:
      - color: red
        operator: template
        value: '[[[ return entity.state.split("|")[1] == 0 ]]]'
      - color: orange
        operator: template
        value: '[[[ return entity.state.split("|")[1] == 1 ]]]'
      - value: default
show_header_toggle: true
```
