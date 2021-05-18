---
layout: post
title:      "Refactoring a React App with Hooks, part 2"
date:       2021-05-18 18:40:03 +0000
permalink:  refactoring_a_react_app_with_hooks_part_2
---


In my last blog post, I explained how to refactor a React app with hooks and began refactoring my own project American Ancestries. In this post I describe my completion of the  refactoring, turning the the remaining class components  into functional components, and also gettting rid of container components (which the creator of React now considers obsolete dut to the Hooks API).

**Refactoring the Places component**

The first step in refactoring my class component Places into a functional component is to change the class declaration `class Places extends Component {}` into a function declaration `const Places = () => {}`. The render function `render() {}` can be removed, leaving only the `return()` within it. Instead of importing Component with React, I import useState: `import React, { useState } from "react";`. 

I then comment out the constructor method:
```
constructor(props) {
    super(props);
    this.state = {
      placeLikes: [],
    };
  }
```
The only state that is stored in the component is an array of booleans. These are used by the Like component, indicating whether or not the place has been "liked". This state is a good place to use the useState() hook, with the argument of the empty array to represent the initial state
```
const [placeLikes, setPlaceLikes] = useState([])
```
Then I remove references in the code to this.state, such as in the handleLike method:
```
  handleLike = (id, event) => {
    const newLikes = [...this.state.placeLikes];
    newLikes[id] = newLikes[id] ? !newLikes[id] : true;
    this.setState({
      placeLikes: newLikes,
    });
  };
```
With hooks, the method becomes
```
   const handleLike = (id, event) => {
    const newLikes = [...placeLikes];
    newLikes[id] = newLikes[id] ? !newLikes[id] : true;
    setPlaceLikes(newLikes);
  };
```
Note that you have to add const or it will throw an error. I remove the `this.` wherever the handleLike function is called. The props for the Like component `<Like liked={this.state.placeLikes[place.id] || false} />` become `<Like liked={placeLikes[place.id] || false} />`

Then I can deal with the incoming props in the component. Instead of initializing the props in the constructor, I can include the variable places as a function parameter: `const Places = ({ places }) => {}`. Wherever `this.props.places`is called in my function, for instance `if (this.props.places.length > 0)`, it can be replaced with the simple variable,  `if (places.length > 0)`

**Getting rid of the container components**

As it turned out, refactoring the container components did not actually require hooks, as advertised, because my components did nothing complicated with state. I had two container components, PlaceContainer and AncestryGroupsContainer. My container components contained actions imported to fetch my data, connections to Redux to store the data in props and state, and routes to the presentational components.

I first tried cutting and pasting all the routes from the components into the Switch component within App.
```
<Route exact path={`${this.props.match.path}/:ancestryGroupId`}>
              <AncestryGroup ancestryGroups={ancestryGroups} />
            </Route>
            <Route path={this.props.match.path}>
              <AncestryGroups ancestryGroups={ancestryGroups} />
            </Route>
            <Route path={`${this.props.match.path}/new`}>
              <PlaceInput
                initialGroups={placeAncestryGroups}
                key={this.props.placeAncestryGroups}
              />
            </Route>
            <Route path={`${this.props.match.path}/:placeId`}>
              <Place places={places} />
              <PlaceComparison places={places} />
            </Route>
            <Route path={this.props.match.path}>
              <Places places={places} />
            </Route>
```
and deleting the routes to the container components, which used routerProps:
```
<Route
              path="/places"
              render={(routerProps) => {
                return <PlacesContainer {...routerProps} />;
              }}
            />
            <Route
              path="/ancestry_groups"
              render={(routerProps) => {
                return <AncestryGroupsContainer {...routerProps} />;
              }}
            />
            />
```
Of course, this does not work, because `this.props.match` does not work without routerProps. Instead, you need to use a direct route for each component: for instance `<Route path="/places">` Note that the router send you to the first url that matches the pattern, so you need to order the routes appropriately; i.e. "/places/new" has to come before "/places/:placeId" and in turn "/places". 

Then I need to import all the actions and components that were originally imported in the components:
```
import PlacesContainer from "./containers/PlacesContainer";
import AncestryGroupsContainer from "./containers/AncestryGroupsContainer";

import AncestryGroups from "./components/ancestry_groups/AncestryGroups";
import AncestryGroup from "./components/ancestry_groups/AncestryGroup";
import Place from "./components/places/Place";
import PlaceComparison from "./components/places/PlaceComparison";
import PlaceInput from "./components/places/PlaceInput";
import Places from "./components/places/Places";
```
The App component also needs to be connected to Redux, as the container components originally used Redux for state:
```
export default connect(mapStateToProps, mapDispatchToProps)(App);
```
Then the mapStateToProps and mapDispatchToProps methods are used to fetch the necessary data and store it in state, as ancestryGroups, places, and placeAncestryGroups. All of this logic is taken from the container components.
```
const mapStateToProps = (state) => {
  return {
    ancestryGroups: state.ancestryGroups,
    places: state.places,
    placeAncestryGroups: state.placeAncestryGroups,
  };
};

const mapDispatchToProps = (dispatch) => {
  return {
    fetchPlaces: () => dispatch(fetchPlaces()),
    fetchAncestryGroups: () => dispatch(fetchAncestryGroups()),
  };
};
```
Finally, in the componentDidMount() method, I call both the fetch actions I imported:
```
componentDidMount() {
    this.props.fetchPlaces();
    this.props.fetchAncestryGroups();
 }
```
and then initialize the variables in the render method:
```
render() {
    const ancestryGroups = this.props.ancestryGroups;
    const places = this.props.places;
    const placeAncestryGroups = this.props.placeAncestryGroups;
		...
		}
```
Putting all the fetch requests in App component eliminates unnecessary and inefficient duplication of code and resources, as fetchAncestryGroups() had been called in both the PlacesContainer and AncestryGroupsContainer. For instance the dispatch method in PlacesContainer was originally:
```
const mapDispatchToProps = (dispatch) => {
  return {
    fetchPlaces: () => dispatch(fetchPlaces()),
    fetchAncestryGroups: () => dispatch(fetchAncestryGroups()),
  };
};
```
and in AncestryGroupsContainer:
```
const mapDispatchToProps = (dispatch) => {
  return {
    fetchAncestryGroups: () => dispatch(fetchAncestryGroups()),
  };
```

With all the code in the App component, the data is fetched more efficiently and the need for routerProps is eliminated. The componentDidMount method above could be refactored with useEffect if I wanted to. But this would probably introduce more complexity than it saves, since useEffect normally runs after each render and I just need to fetch upon mounting. Having one class component at the top of the hierarchy is acceptable, and sometimes necessary.

**References**
[Source code for American Ancestries](http://www.github.com/wkdewey/)
[Refactoring an app to use hooks--from LogRocket](https://blog.logrocket.com/practical-react-hooks-how-to-refactor-your-app-to-use-hooks-b1867e7b0a53/)

