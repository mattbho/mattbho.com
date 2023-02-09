---
title: Create a Simple GIPHY App!
date: '2023-02-09'
tags: ['Front-End']
draft: false
summary: 'A simple GIPHY APP with Debounced Search'
---

Today we'll be making a GIPHY instant search app. This is a great app for beginners because you will encounter search components like this everywhere, and more importantly, you'll learn the concept of debouncing!

# Prerequisites

- NodeJS v16+
- NPM v6+

_NOTE: If you are using Yarn like me, you will need 0.25+_

## Set Up a GIPHY API key

For this project, you'll need to be registered on [Giphy's developer portal](https://developers.giphy.com/)

Once you register, you will be prompted to "Create an App". Just choose the API option, and then you will recieve an API key for that app.

# Getting Started

We'll start by bootstrapping together a react application with `create-react-app`.

```
yarn create react-app my-app
```

Lets go ahead and remove all the unnecessary files and remove any code that references them.

Find these files and the references:

1. App.css + Index.css
2. App.test.js
3. Logo.svg
4. reportWebVitals.js
5. setupTests.js

In a production environment, we'll definitely need these but for the sake of this simple project, it won't be necessary.

Your resulting file structure should look like this.

```bash
src
├── App.jsx
└── index.jsx
```

Ant that's almost everything to get started! There's one more thing to do here, lets save that API key you just got earlier into a `.env` file.

```
// .env
REACT_APP_GIPHY_API_KEY="<Your Key>"
```

Awesome. Now we're ready to get started.

# Create a Helper Function to Fetch the GIPHY API

We'll start by defining our API fetch utility file under `src/api/searchGiphy.js`.

```javascript
const URL = 'https://api.giphy.com/v1/gifs/search?'

const DEFAULT_PARAMS = {
  api_key: process.env.REACT_APP_GIPHY_API_KEY,
  limit: 25,
  offset: 0,
  lang: 'en',
  rating: 'g',
}

const searchGiphy = async (params) => {
  const searchParams = { ...DEFAULT_PARAMS, ...params }
  const searchUrl = URL + new URLSearchParams(searchParams).toString()

  return fetch(searchUrl)
}

export default searchGiphy
```

We'll store the search endpoint in a URL constant and we'll be utilizing the default params that are provided in the documentation. We'll include the environment variable we defined earlier in `.env` file.

If we need to change or add any parameters, we can use the ES6 spread syntax to construct a new parameter object. In fact, that's what this helper function will do. We'll define a `searchGiphy` function that will append any parameters to the default params and make the fetch call to the GIPHY API. The parameter we'll be most concerned with is the `q` parameter which is used to query the API with a given string.

# Calling the API

We'll be handling all of our API calls at the top level of the app in `app.jsx`. Our file will look like this.

```javascript
import { useState } from 'react'
import searchGiphy from './api/searchGiphy'
import styles from './styles/base.module.css'
import Loader from './components/Loader/Loader'
import SearchInput from './components/SearchInput/SearchInput'
import SearchResults from './components/SearchResults/SearchResults'

function App() {
  const [searchResults, setSearchResults] = useState(null)
  const [loading, setLoading] = useState(false)

  const fetchResults = async (term) => {
    try {
      setLoading(true)
      const response = await searchGiphy({ q: term })
      const { data } = await response.json()
      setSearchResults(data)
    } catch (_error) {
      /*
       * Redacted
       * */
    } finally {
      setLoading(false)
    }
  }
  return (
    <div className={styles.app}>
      <div className={styles.main}>
        <SearchInput handleSearch={handleSearch} />
        {loading ? <Loader /> : <SearchResults searchResults={searchResults} />}
      </div>
    </div>
  )
}

export default App
```

We'll save our data inside of a `searchResults` state that we will leverage when we create our search results component. Don't worry about the other components for now, we will define them later.

Lets also define a global css module under `src/styles/base.module.css`.

```css
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen', 'Ubuntu',
    'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.app {
  display: flex;
  justify-content: center;
}

.main {
  width: 1100px;
}
```

## Grokking the Data

The structure of the data that is returned from the API will look like this

```json
{
  data : [{type: "gif", id: "QU4O9hWy4f3FAlEshA",…}, {type: "gif", id: "9eCgHU7u8I9qOhTjEN",…},…]
  meta: {status: 200, msg: "OK", response_id: "gz5fpqpqfb3vm9tfq2zjhs8ohq2qmaxfzgpwsiah"}
  pagination :  {total_count: 30501, count: 25, offset: 0}
}
```

Inside of data will be an array of data objects. We can access the image url inside of this data object by calling `data[0].images.downsized.url`

# Defining the Rest Of Our Components

We'll need a component that takes in the user input and then calls our search function. `App.jsx` will pass the search function as a prop to this component, which will get called every time the input changes!

```javascript
// SearchInput.jsx
import styles from './SearchInput.module.css'

function SearchInput({ handleSearch }) {
  return (
    <div className={styles.searchContainer}>
      <input
        type="text"
        placeholder="Seach for your GIFS"
        onChange={(e) => handleSearch(e.target.value)}
      />
    </div>
  )
}

export default SearchInput
```

```css
/* SearchInput.module.css */
.searchContainer {
  margin-top: 25px;
  display: flex;
  justify-content: center;
  height: 50px;
}

.searchContainer input {
  flex: 1;
  width: 50%;
  border-radius: 5px;
  font-size: 16px;
  padding-left: 10px;
}
```

Then, well take the data that we retrieved in `App` and then pass it to the SearchResults component, wrapping them in an `<img/>` tag

```javascript
// SearchResults.jsx
import styles from './SearchResults.module.css'

function SearchResults({ searchResults }) {
  return (
    <div className={styles.container}>
      {searchResults &&
        searchResults.map((giphy) => (
          <img
            src={giphy.images.downsized.url}
            alt={giphy.title}
            key={giphy.images.downsized.url}
          />
        ))}
    </div>
  )
}

export default SearchResults
```

```css
/* SearchResults.module.css */
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: auto;
  grid-gap: 10px;
}

.container img {
  height: 100%;
  width: 100%;
  object-fit: scale-down;
}
```

This is a nice to have. A Loader component that displays when we are fetching the data. That way, there is an indicator that the data is coming and the user isn't just presented with a blank screen. This will be implemented with CSS.

```javascript
// Loader.jsx
import styles from './Loader.module.css'

function Loader() {
  return (
    <div className={styles.loaderContainer}>
      <div className={styles.loader} />
    </div>
  )
}

export default Loader
```

```css
/* Loader.module.css */
.loaderContainer {
  margin-top: 15px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.loader {
  height: 50px;
  width: 50px;
  border: 5px #d3d3d3 solid;
  border-top: 5px #121212 solid;
  border-radius: 50%;

  animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
```

# Debouncing

You probably have noticed that our application has been making an API request for every single key stroke that we have been typing. This is a huge problem. Lets imagine that our search query is an infinitely long piece of text. We would essentially be making an infinite amount of API requests, which is not ideal, and GIPHY wouldn't be happy with us DDOSing them either. In order to avoid this, we'll introduce something called a debounce.

## What is debouncing?

Debouncing is a technique to restrict the frequent calling of a time consuming function, by delaying the execution of said function until a specified time. This is particularly useful for us because this way, we won't be calling GIPHY's API infinitetly, bogging their API and our app performance.

The way our debounce function will work is simple. Lets create a file called `src/utils/debounce.js`

```javascript
const debounce = (fn, delay) => {
  let timerId
  return (...args) => {
    clearTimeout(timerId)
    timerId = setTimeout(() => fn(...args), delay)
  }
}

export default debounce
```

This debounce function will take in our search function, set a new timeout, and clear the old timeout every time our search is called, which happens on every key stroke. Once the user stops typing, our timeout will finally have a chance to run out and then our search will be called. This way, GIPHY will only get called once we've stopped typing.

Lets wrap our API call with this function like so.

```javascript
import { useState } from 'react'
import searchGiphy from './api/searchGiphy'
import styles from './styles/base.module.css'
import Loader from './components/Loader/Loader'
import SearchInput from './components/SearchInput/SearchInput'
import SearchResults from './components/SearchResults/SearchResults'
import debounce from './utils/debounce'

function App() {
  const [searchResults, setSearchResults] = useState(null)
  const [loading, setLoading] = useState(false)

  const debouncedFetchResults = debounce(async (term) => {
    try {
      setLoading(true)
      const response = await searchGiphy({ q: term })
      const { data } = await response.json()
      setSearchResults(data)
    } catch (_error) {
      /*
       * Redacted
       * */
    } finally {
      setLoading(false)
    }
  }, 1000)

  const handleSearch = (term) => {
    debouncedFetchResults(term)
  }

  return (
    <div className={styles.app}>
      <div className={styles.main}>
        <SearchInput handleSearch={handleSearch} />
        {loading ? <Loader /> : <SearchResults searchResults={searchResults} />}
      </div>
    </div>
  )
}

export default App
```

# Final App!

Your final app structure should look like this.

```bash
src
├── api
│   └── searchGiphy.js
├── App.jsx
├── components
│   ├── Loader
│   │   ├── Loader.jsx
│   │   └── Loader.module.css
│   ├── SearchInput
│   │   ├── SearchInput.jsx
│   │   └── SearchInput.module.css
│   └── SearchResults
│       ├── SearchResults.jsx
│       └── SearchResults.module.css
├── index.jsx
├── styles
│   └── base.module.css
└── utils
    └── debounce.js
```

That's it! Congrats, you've successfully built a GIPHY Search App with an instant debounced search. Here's a screenshot of how it looks!

<img src="https://i.ibb.co/QCkptGN/simplegif.png" alt="simplegif" border="0"/>
