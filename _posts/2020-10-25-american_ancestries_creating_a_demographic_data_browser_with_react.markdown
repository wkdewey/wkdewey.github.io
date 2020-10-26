---
layout: post
title:      "American Ancestries: Creating a Demographic Data Browser with React"
date:       2020-10-26 00:25:24 +0000
permalink:  american_ancestries_creating_a_demographic_data_browser_with_react
---


My project was called American Ancestries. The app is based on ancestry and ethnicity data provided by the US Census American Community Survey, and allows users to explore the cultural diversity of the United States and see how it varies across different. Users are able to input data on geographic places, including overall population and the population of individual ancestry groups. They can then browse place and see the percentages of the different ancestry groups, as well as how they compare to the national average. They can also browse ancestry groups and see where the group is most prominently represented. (See https://en.wikipedia.org/wiki/Race_and_ethnicity_in_the_United_States#Ancestry for more background on how ancestry groups are categorized, https://data.census.gov/cedsci/table?q=Minneapolis%20city,%20Minnesota%20B04006&g=1600000US2743000&tid=ACSDT1Y2019.B04006&hidePreview=false for an example of the data in question).

First I created a Places model with my Rails backend, using an API-only setup. I created index and show routes and used the fast_jsonapi gem to serialize the data with the attributes I wanted.
```
 def show
    place = Place.find_by(id: params[:id])
    render json: PlaceSerializer.new(place)
  end
```

I set up the frontend React with create-react-app and imported the basic dependencies, Redux and Thunk (for asyncronous actions) in index.js.
```
import { Provider } from "react-redux";
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
import reducer from "./reducers/reducer.js";
```
Then I created a store,
```
const store = createStore(
  reducer,
composeEnhancers(
    applyMiddleware(thunk)
  )
);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```
connected the App component to state and dispatch,
```
const mapStateToProps = (state) => {
  return {
    places: state.places,
    ancestryGroups: state.ancestryGroups,
    placeAncestryGroups: state.placeAncestryGroups,
  };
};
const mapDispatchToProps = (dispatch) => {
  return {
    fetchPlaces: () => dispatch(fetchPlaces()),
    fetchAncestryGroups: () => dispatch(fetchAncestryGroups()),
  };
};
export default connect(mapStateToProps, mapDispatchToProps)(App);

```
and created the action and reducer files with some basic outlined actions, including fetchPlaces() which I called in componentDidMount.


I created a fetch action that retrived the data from the API,
```
export const addPlace = (place) => {
  return (dispatch) => {
    dispatch({ type: "POSTING_PLACE" });
    fetch("http://localhost:3000/api/v1/places", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(place),
    })
      .then((response) => response.json())
      .then((json) => {
        dispatch({ type: "ADD_PLACE", place: json.data });
      });
  };
};
```
and used it to call the reducer to initialize places in the state of my app.
```
    case "ADD_PLACE":
      return {
        ...state,
        places: [...state.places, action.place],
        loading: false,
      };
```
The fetch method was much like the Javascript project.

I then started creating my components, a PlacesContainer and Places view page, a NavBar component with links to the different pages of the app, a Home component. PlacesContainer was a class component and Places Within the App component, I organized the router to direct users to different parts of the app based on the nav links. I then created nested routes within Creating components was straightforward, but the router gave me some trouble throughout the project. The first issue I ran into was that the router matches "/" before it matches "/places", which required rearranging the routes
```
      <Router>
        <div className="App">
          <NavBar />
          <Switch>
            <Route
              path="/places"
              render={(routerProps) => {
                return (
                  <PlacesContainer
                    {...routerProps}
                    places={this.props.places}
                    ancestryGroups={this.props.ancestryGroups}
                    placeAncestryGroups={this.props.placeAncestryGroups}
                  />
                );
              }}
            />
            <Route
              path="/ancestry_groups"
              render={(routerProps) => {
                return (
                  <AncestryGroupsContainer
                    {...routerProps}
                    ancestryGroups={this.props.ancestryGroups}
                  />
                );
              }}
            />
            <Route path="/">
              <Home />
            </Route>
          </Switch>
        </div>
      </Router>
```
. Nested routes also gave me trouble later on, because I wasn't properly importing the right methods from "react-router-dom".  (The correct ones were `import { Route, Switch } from "react-router-dom";`)The CORS (Cross-origin resource sharing) policy created another bug I had to fix. If it isn't configured properly, the frontend won't be able to communicate with the backend, it will be blocked because the requests are coming from a different server address.

I then moved on to create an input form to input a place. This necessitated another controller action on the Rails section, create that returns JSON. I created a controlled form, with onChange methods that update state as the user inputs in the different fields, then a handleSubmit method that calls addPlace in the dispatcher and creates a new place in state. Then I made a fetch request, posting a formData object that created new entries in the database on the backend, and returning JSON to create a new place object

The next part of the app dealt with the AncestryGroups model. It was more or less like the Places model, requiring a model and index and show actions on the Rails backend, with a serializer that renders JSON, and a container and AncestryGroups and AncestryGroup components on the React frontend. I made a fetch action, calling it in componentDidMount and sent the results to the backend.

I then moved on to the third and most difficult model of my app, the join model called PlaceAncestryGroups. At first I thought I didn't need a join model, since there was no need for AncestryGroups to "belong to" Places, they would just all be accessible in the database. However, in order to store data corresponding to the AncestryGroups in Places, ensure that Places always had access to the right AncestryGroups to hold this data, and easily compare data in the way I wanted,  it was necessary to create a join model, in a has_many_through relationship. Unlike with past projects, I would store all the data on the join model, under the population key (the population in a given place of a given ancestry group). I created some routes and used the serializer as before. Nested attributes from a join model are always a challenge to get right. In order to get the serializer to display them correctly on the Place model, I needed to add to it "accepts_nested_attributes_for :place_ancestry_groups" and 
```
  attribute :place_ancestry_groups do |place|
    PlaceAncestryGroupSerializer.new(place.place_ancestry_groups).as_json["data"]
  end
```

With the nested attributes available (and with the help of some custom attributes I wrote in the serializer), I could display more information in the Place component, including the percentage of each ancestry, and (in the new PlaceComparisons component) its representation in that place compared with the national average. The nested attributes meant I needed to refactor the PlaceInput form, pre-creating an array of placeAncestryGroups with blank data, iterating through all the associated ancestry groups and creating fields for the population of each.


Getting the nested form right was also a great challenge. On the frontend, the fields for the ancestry group needed their own change handler, where I used spread syntax to change the correct element of the groups array then assign it back into state:
```
handleGroupChange = (groups, id, event) => {
    // debugger;
    let group = { ...groups[id] };
    group.population = parseInt(event.target.value);
    groups[id] = group;
    this.setState({
      placeAncestryGroups: groups,
    });
  };
```

On the backend, I needed to permit the correct parameters as I sent the data back into the database, after trying many methods that didn't work (a problem I also ran into on the JavaScript project), this method got the necessary attributes to save:

```
 def place_params
    params.require(:place).permit(:name, :population, place_ancestry_groups_attributes: [:ancestry_group_id, :population])
end
```

In turn, this meant that I needed to set the formData from the front end to match the attributes that Rails was expecting:
```
place_ancestry_groups_attributes: this.state.placeAncestryGroups.map(
        (group) => {
          return {
            ancestry_group_id: parseInt(group.ancestryGroupId),
            population: group.population,
          };
        }
      ),
```

After completing the form with nested attributes and fixing a few remaining issues (like loading messages), it was time to move on to styling. CSS styling can be complicated but the reactstrap package makes it simple. There are Card components, Table components, NavBar, Form, and Jumbotron, all of which had uses in my project. There were even Spinners for the loading messages.
Here is an example of how I used reactstrap to render all the Places of my index view
```
    return (
      <CardColumns>
        {props.places.map((place) => {
          return (
            <div className="place" key={place.id}>
              <Card>
                <CardBody>
                  <CardTitle>
                    <h3>
                      <CardLink key={place.id} to={`/places/${place.id}`}>
                        {place.attributes.name}
                      </CardLink>
                    </h3>
                  </CardTitle>
                  <CardText>Population: {place.attributes.population}</CardText>
                </CardBody>
              </Card>
            </div>
          );
        })}
      </CardColumns>
    );
```





