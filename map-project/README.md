**Original Request:** (from [StackOverflow](https://stackoverflow.com/questions/45355513/how-would-i-make-it-so-that-props-match-params-id-can-be-accessed-to-any-of-the#)

Hello everyone, 

I'm currently trying to wrap my head around how to structure a ReactJS application. Here's the relevant information: 

**Dev Environment**: npm, webpack, babel

**Dependencies**: React, ReactDOM, React Router, React Router DOM

**Code**: (Cleaned up a bit as to not reveal more sensitive information.)

```
/***********
TABLE OF CONTENTS
01.0.0 - Imports
02.0.0 - Data
03.0.0 - Components
03.1.0 -- Base
03.2.0 -- Location
03.3.0 -- Main
03.3.2 --- Map
04.0.0 - Render
***********/

/* 01.0 Imports */
import React from 'react';
import ReactDOM from 'react-dom';
import $ from 'jquery';
import {
  HashRouter,
  Route,
  Switch,
  Link
} from 'react-router-dom';

console.log("Set-up successful");
/* 02.0 Data */
const locationDataArray = {
  locations: [
    {
      "location": "Scenario A",
      "slug": "lipsum",
      "googleUrl": "https://www.google.com/maps/place/Central+Park/@40.7828687,-73.9675438,17z/data=!3m1!4b1!4m5!3m4!1s0x89c2589a018531e3:0xb9df1f7387a94119!8m2!3d40.7828647!4d-73.9653551",
      "mapUrl": "locations/scenA/main-map.png",
      "areaMaps": [
        {
          "name": "Overall",
          "url": "inner/map-overall.html"
        }, {
          "name": "A",
          "url": "inner/map-a.html"
        }, {
          "name": "B",
          "url": "inner/map-b.html"
        }
      ],
      "overallPDF": "diagram.pdf",
      "mapKey": "mapkey.txt",
      "mapImagemap": "imagemap.txt"
    } // list truncated for your convenience.
  ],
  all: function() {return this.locations},
  get: function(id) {
    const isLocation = q => q.slug === id
    return this.locations.find(isLocation)
  }
}

/* 03.0 Components */
/* 03.1 Base */
class App extends React.Component {
  constructor() {
    super();
    console.log("<App /> constructor locationDataArray: " + locationDataArray);
    this.state = {
      locationData: locationDataArray,
      test: 'testing!'
    };
  }
  render() {
    console.log("<App /> locationDataArray: " + locationDataArray);
    console.log("<App /> state, testing: " + this.state.test);
    return(
      <div>
        <Switch>
          <Route exact path='/' component={LocationGrid} />
          <Route path='/locations/:id' component={MainMap}/>
        </Switch>
      </div>
    );
  }
}

/* 03.2.0 -- Location */

class LocationGrid extends React.Component {
  constructor() {
    super();
  }
  render() {
    return(
      <div>
        <h2>Location Grid</h2>
        <div>
          <Link to="/locations">Main Map</Link>
        </div>
        <div>
          <ul>
            { // this part works with extra items in locationDataArray.
              locationDataArray.all().map(q => (
                <li key={q.slug}>
                  <Link to={`locations/${q.slug}`}>{q.location}</Link>
                </li>
              ))
            }
          </ul>
        </div>
      </div>
    );
  }
}

/* 03.3.0 -- Main */

class MainMap extends React.Component {
  constructor(props) {
    super();
    console.log("Main map is on");
    const location = locationDataArray.get(
      props.match.params.id
    );
    if (!location) {
      console.log("No such location!");
    }
    if (location) {
      console.log("These tests show that the locationDataArray is accessible from this component.");
      console.log("A: props.match.params.id is: " + props.match.params.id);
      console.log("B: Location is: " + location.location);
      console.log("C: Map URL: " + location.mapUrl);
      console.log("State is: " + location);
    }
  }
  render() {
    return (
      <div>
        <MapHolder />
      </div>
    );
  }
}

/* 03.3.2 --- Map */
//var mapUrl = require(location.mapUrl);
class MapHolder extends React.Component {
  render() {
    console.log("And these tests show that the locationDataArray is NOT accessible from this component.");
    console.log("1. Map location test, mapUrl: " + location.mapUrl);
    console.log("2. Map location test, slug: " + location.slug);
    return <div>
      <img id="imagemap" className="map active" src={location.mapUrl} width="1145" height="958" useMap="#samplemap" alt="" />
      <map name="samplemap" id="mapofimage">
         <area target="" alt="1" title="1" href="inner/01.html" coords="288,356,320,344,347,403,315,420" shape="poly"></area>
         {/* Additional imagemap areas. Truncated for your conveinence.*/}
      </map>
      <p>A map would be here if I could figure out the bug.</p>
      </div>
  }
}

/* 04.0.0 -- Render */

ReactDOM.render((
  <HashRouter>
    <App />
  </HashRouter>
), document.getElementById('container'));
```

`package.json` and `webpack.config.js` are all in the git repository, if needed.

**Overall Goal**: I want to dynamically serve information to a main component and children components via a React Router route param and JSON object. Essentially, I want to be able to associate a param with an item in the object, so that the component can just refer to something like `arrayItem.itemProperty` and it can work dynamically. 

I am trying to stay away from Redux as much as possible, since I'm already slightly overwhelmed by React now.

**The Current Problem**:

The array (on lines 26-55) is in the code, but anything outside of 1 component (`<MainMap />`, lines 113-138) can't access it.

An example of one of these child Components would be `<MapHolder />`, which is on lines 142-156. (I get `ReferenceError: props is not defined` in those cases.)

How would I make it so that `props.match.params.id` (or any other thing that would make this work) can be accessed to any of the children Components? 

(*Note:* I'm well aware that the code isn't perfect. But this is still in early production stages, so I'd like to just resolve that one issue and hopefully the rest will clear up as a result of that.)