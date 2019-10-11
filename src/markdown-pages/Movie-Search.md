---
path: "/blog/creating-this-blog"
date: "12-10-2019"
title: "Movie Search App"
tags: ["react"]
---

Using this tutorial i was able to make a simple react app with hooks. 
[Movie Search App](https://moviesearch-app.herokuapp.com/)

[How to build a movie search app using React Hooks](https://www.freecodecamp.org/news/how-to-build-a-movie-search-app-using-react-hooks-24eb72ddfaf7/)

## Tech Stack & Features

- React with Hooks
- create-react-app
- JSX
- CSS

App will be able to search for movies via the [OMDB API](http://www.omdbapi.com/) and render the results to the user.

Some things are needed before we get into building the app:

- Node(≥ 6)
- Text editor
- An Api key from [OMDB](https://www.omdbapi.com/apikey.aspx)

## Set up

Install node and then install create-react-app:

    npm install -g create-react-app
    
    create-react-app moviesearch

This will create a skeleton react project called moviesearch. 

Inside this projects directory we can run several commands:

`npm start` - Starts the development server
`npm run build` - Bundles the app into static files for production
`npm run eject` - Removes this tool and copies build dependencies, configuration files and scripts into the app directory

## App Structure

![](Untitled-c3e716f3-f477-489a-8126-f53c973ecd15.png)

There will be 4 components in this app:

- App.js - it will be the parent component for the other 3. It will also contain the function that handles the API request and it will have a function that calls the API during the component's initial render.
- Header.js - A simple component that renders the app header and accepts a title prop
- Movie.js - It renders each movie. The movie object is simply passed into it as props
- Search.js - Contains a form with the input element and the search button, contains functions that handle the input element and resets the field, and also contains a function that calls the search function which is passed as props to it.

## Components

Create a new folder in the `src` directory and name it `components`. This is where our components will be. We will then move the `App.js` file into that folder. 

### Header component

Then, we will create the `Header` component. Create a file called `Header.js` and add the following code to it:

    import React from "react";
    
    const Header = (props) => {
      return (
        <header className="App-header">
          <h2>{props.text}</h2>
        </header>
      );
    };
    
    export default Header;

The `header` component renders the `header` tag with the `text` props.

Now update the import in our `index.js` file:

    import React from 'react';
    import ReactDOM from 'react-dom';
    import './index.css';
    import App from './components/App'; // this changed
    import * as serviceWorker from './serviceWorker';

Also update our `App.css` with these styles:

    .App {
      text-align: center;
    }
    
    .App-header {
      background-color: #282c34;
      height: 70px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      font-size: calc(10px + 2vmin);
      color: white;
      padding: 20px;
      cursor: pointer;
    }
    
    .spinner {
      height: 80px;
      margin: auto;
    }
    
    .App-intro {
      font-size: large;
    }
    
    /* new css for movie component */
    
    * {
      box-sizing: border-box;
    }
    
    .movies {
      display: flex;
      flex-wrap: wrap;
      flex-direction: row;
    }
    
    .App-header h2 {
      margin: 0;
    }
    
    .add-movies {
      text-align: center;
    }
    
    .add-movies button {
      font-size: 16px;
      padding: 8px;
      margin: 0 10px 30px 10px;
    }
    
    .movie {
      padding: 5px 25px 10px 25px;
      max-width: 25%;
    }
    
    .errorMessage {
      margin: auto;
      font-weight: bold;
      color: rgb(161, 15, 15);
    }
    
    
    .search {
      display: flex;
      flex-direction: row;
      flex-wrap: wrap;
      justify-content: center;
      margin-top: 10px;
    }
    
    
    input[type="submit"] {
      padding: 5px;
      background-color: transparent;
      color: black;
      border: 1px solid black;
      width: 80px;
      margin-left: 5px;
      cursor: pointer;
    }
    
    
    input[type="submit"]:hover {
      background-color: #282c34;
      color: antiquewhite;
    }
    
    
    .search > input[type="text"]{
      width: 40%;
      min-width: 170px;
    }
    
    @media screen and (min-width: 694px) and (max-width: 915px) {
      .movie {
        max-width: 33%;
      }
    }
    
    @media screen and (min-width: 652px) and (max-width: 693px) {
      .movie {
        max-width: 50%;
      }
    }
    
    
    @media screen and (max-width: 651px) {
      .movie {
        max-width: 100%;
        margin: auto;
      }
    }

### Movie component

Create a file called Movie.js and add this code:

    import React from "react";
    
    const DEFAULT_PLACEHOLDER_IMAGE =
      "https://m.media-amazon.com/images/M/MV5BMTczNTI2ODUwOF5BMl5BanBnXkFtZTcwMTU0NTIzMw@@._V1_SX300.jpg";
    
    
    const Movie = ({ movie }) => {
      const poster =
        movie.Poster === "N/A" ? DEFAULT_PLACEHOLDER_IMAGE : movie.Poster;
      return (
        <div className="movie">
          <h2>{movie.Title}</h2>
          <div>
            <img
              width="200"
              alt={`The movie titled: ${movie.Title}`}
              src={poster}
            />
          </div>
          <p>({movie.Year})</p>
        </div>
      );
    };
    
    
    export default Movie;

This is a presentational component (it doesn’t have any internal state) that renders the movie title, image, and year. The reason for the DEFAULT_PLACEHOLDER_IMAGE is because some movies retrieved from the API do not have images.

### Search component

Using hooks we can have a functional component handle its own state (class components not required). Create `Search.js` and add:

    import React, { useState } from "react";
    
    
    const Search = (props) => {
      const [searchValue, setSearchValue] = useState("");
      
      const handleSearchInputChanges = (e) => {
        setSearchValue(e.target.value);
      }
    
      const resetInputField = () => {
        setSearchValue("")
      }
    
      const callSearchFunction = (e) => {
        e.preventDefault();
        props.search(searchValue);
        resetInputField();
      }
    
      return (
          <form className="search">
            <input
              value={searchValue}
              onChange={handleSearchInputChanges}
              type="text"
            />
            <input onClick={callSearchFunction} type="submit" value="SEARCH" />
          </form>
        );
    }
    
    export default Search;

#### `useState`

This lets us add React state to function components. The `useState` hook accepts one argument which is the initial state, and then it returns an array containing the current state and a function to update it. 

We are passing our current state as the value for the search input field. When the onChange event is called the `handleSearchInputChanges` function is called which calls the state update function with the new value. The resetInputField function basically called the state update function (setSearchValue) with an empty string in order to clear the input field

### Update `App.js`

Update App.js with the following code:

    import React, { useState, useEffect } from "react";
    import "../App.css";
    import Header from "./Header";
    import Movie from "./Movie";
    import Search from "./Search";
    
    
    const MOVIE_API_URL = "https://www.omdbapi.com/?s=man&apikey=d8e47c01"; // you should replace this with yours
    
    
    const App = () => {
      const [loading, setLoading] = useState(true);
      const [movies, setMovies] = useState([]);
      const [errorMessage, setErrorMessage] = useState(null);
    
        useEffect(() => {
        fetch(MOVIE_API_URL)
          .then(response => response.json())
          .then(jsonResponse => {
            setMovies(jsonResponse.Search);
            setLoading(false);
          });
      }, []);
    
        const search = searchValue => {
        setLoading(true);
        setErrorMessage(null);
    
        fetch(`https://www.omdbapi.com/?s=${searchValue}&apikey=d8e47c01`)
          .then(response => response.json())
          .then(jsonResponse => {
            if (jsonResponse.Response === "True") {
              setMovies(jsonResponse.Search);
              setLoading(false);
            } else {
              setErrorMessage(jsonResponse.Error);
              setLoading(false);
            }
          });
      	};
    
        
        return (
         <div className="App">
          <Header text="HOOKED" />
          <Search search={search} />
          <p className="App-intro">Sharing a few of our favourite movies</p>
          <div className="movies">
            {loading && !errorMessage ? (
             <span>loading...</span>
             ) : errorMessage ? (
              <div className="errorMessage">{errorMessage}</div>
            ) : (
              movies.map((movie, index) => (
                <Movie key={`${index}-${movie.Title}`} movie={movie} />
              ))
            )}
          </div>
        </div>
      );
    };
    
    
    export default App;

We are using 3 `useState` functions. The first is used to handle the loading state (it renders 'loading...' when loading is set to true. The second is used to handle the movies array that is gotten from the server. And finally the third is used to handle any errors that might occur when making the API request.

Then comes the second hooks API in this app: the `useEffect` hook. This hook lets you perform side effects in your function components. By side effects we mean things like data fetching, subscriptions, and manual DOM manipulations. 

If you’re familiar with React class lifecycle methods, you can think of `useEffect` Hook as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` combined.

#### `useReducer`

We can use the hook `useReducer` to combine the 3 useState functions.

    import React, { useReducer, useEffect } from "react";
    import "../App.css";
    import Header from "./Header";
    import Movie from "./Movie";
    import Search from "./Search";
    
    
    const MOVIE_API_URL = "https://www.omdbapi.com/?s=man&apikey=d8e47c01";
    
    
    const initialState = {
      loading: true,
      movies: [],
      errorMessage: null
    };
    
    
    const reducer = (state, action) => {
      switch (action.type) {
        case "SEARCH_MOVIES_REQUEST":
          return {
            ...state,
            loading: true,
            errorMessage: null
          };
        case "SEARCH_MOVIES_SUCCESS":
          return {
            ...state,
            loading: false,
            movies: action.payload
          };
        case "SEARCH_MOVIES_FAILURE":
          return {
            ...state,
            loading: false,
            errorMessage: action.error
          };
        default:
          return state;
      }
    };
    
    
    
    const App = () => {
      const [state, dispatch] = useReducer(reducer, initialState);
    
        useEffect(() => {
        
            fetch(MOVIE_API_URL)
                .then(response => response.json())
                .then(jsonResponse => {
            
                dispatch({
                    type: "SEARCH_MOVIES_SUCCESS",
                    payload: jsonResponse.Search
            	});
          	});
      	}, []);
    
        const search = searchValue => {
        	dispatch({
          	type: "SEARCH_MOVIES_REQUEST"
        	});
    	
            fetch(`https://www.omdbapi.com/?s=${searchValue}&apikey=d8e47c01`)
          	.then(response => response.json())
          	.then(jsonResponse => {
            	if (jsonResponse.Response === "True") {
              	dispatch({
                    type: "SEARCH_MOVIES_SUCCESS",
                    payload: jsonResponse.Search
              	});
            	} else {
              	dispatch({
                    type: "SEARCH_MOVIES_FAILURE",
                    error: jsonResponse.Error
              	});
              }
          	});
    	  };
    
        const { movies, errorMessage, loading } = state;
    
        return (
        <div className="App">
          <Header text="HOOKED" />
          <Search search={search} />
          <p className="App-intro">Sharing a few of our favourite movies</p>
          <div className="movies">
            {loading && !errorMessage ? (
              <span>loading... </span>
            ) : errorMessage ? (
              <div className="errorMessage">{errorMessage}</div>
            ) : (
              movies.map((movie, index) => (
                <Movie key={`${index}-${movie.Title}`} movie={movie} />
              ))
            )}
          </div>
        </div>
      );
    };
    
    export default App;

The `useReducer` hook takes 3 arguments, but we will only use 2. A typical `useReducer` hook looks like this:

    const [state, dispatch] = useReducer(
        reducer,
        initialState
    );

The reducer argument looks like this:

    const reducer = (state, action) => {
      switch (action.type) {
        case "SEARCH_MOVIES_REQUEST":
          return {
            ...state,
            loading: true,
            errorMessage: null
          };
        case "SEARCH_MOVIES_SUCCESS":
          return {
            ...state,
            loading: false,
            movies: action.payload
          };
        case "SEARCH_MOVIES_FAILURE":
          return {
            ...state,
            loading: false,
            errorMessage: action.error
          };
        default:
          return state;
      }
    };

The reducer takes in the initialState and the action, so based on the action type, the reducer returns a new state object. For example, if the type of action that is dispatched is SEARCH_MOVIES_REQUEST , the state is updated with the new object where the value for loading is true and errorMessage is null.

In our `useEffect` , we are now dispatching an action with the payload as the movies array we are getting from the server.

In our `search` function, we are dispatching three different actions. 

1. One action is the SEARCH_MOVIES_REQUEST action which updates our state object, making loading=true and errorMessage = null.
2. If the request is successful then we dispatch another action with the type SEARCH_MOVIES_SUCCESS that updates our state object making loading=false and movies = action.payload where the payload is the movies array gotten from OMDB.
3. If there is an error, we will instead dispatch a different action with the type SEARCH_MOVIES_FAILURE that updates our state object making loading=false and errorMessage = action.error where the action.error is the error message gotten from the server.

#### Resources

[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)

## Changing the colour scheme

In order to make this tutorial fit in with the rest of my projects i decided to use the colour scheme of this blog and my personal website. I first added a font folder with the [Inter](https://rsms.me/inter/) font and linked it to my `app.css`. 

    @font-face {
      font-family: "myFont";
      src: url(fonts/Inter/Inter-Regular.woff);
    }

Then i added css custom properties.

    :root {
      --bgColor: #282a36;
      --textColor: #f8f8f2;
      --headingColor: #50fa7b;
      --accentColor: #8be9fd;
      --pinkColor: #ff79c6;
      --regularFont: "myFont";
    }

Adding the appropriate colours to html elements.

    * {
      box-sizing: border-box;
      background-color: var(--bgColor);
    }
    
    .movies {
      display: flex;
      flex-wrap: wrap;
      flex-direction: row;
      background-color: var(--bgColor);
      color: var(--accentColor);
    }
    
    input[type="submit"] {
      padding: 5px;
      width: 80px;
      margin-left: 5px;
      cursor: pointer;
      color: var(--pinkColor);
    }
    
    input[type="submit"]:hover {
      color: var(--pinkColor);
    }
    
    .search > input[type="text"]{
      width: 40%;
      min-width: 170px;
      color: var(--textColor);
    }

I also added a new favicon. You can see the final website [here](https://www.notion.so/brap/Movie-Search-App-5d6ed8b9dd854cd495bd6b6d5697a252#41319fa301514279a21d5467e55fb570).