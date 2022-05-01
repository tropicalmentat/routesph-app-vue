# Routes.ph Web App

Prototype bicycle route planning web app front-end. Most navigation and route planning apps serve car-centric routes. As a cyclist and bike commuter myself, the main concerns of planning routes are safe bike lanes and end-of-trip facilities (primarily secure bike parking, bonus if there are showers!). 

In this app, I leverage the volunteer-driven Openstreetmap data for bike parking locations and bike lane networks. I use [Graphhopper](https://github.com/graphhopper/graphhopper) for the route-planning engine as it has custom vehicle routing profiles (cars, scooter, bicycle). 

For the UI/UX I attempted to clone the Origin-Destination selection feature in [Komoot](https://www.komoot.com/plan) using Vue and Leaflet. This is still a work-in-progress, but I hope to get this production ready for the public when time permits :) .  


https://user-images.githubusercontent.com/8053617/164217529-f5795d9f-26bd-4fb4-8cca-6f200fce38f7.mp4


## Project setup
```

npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
