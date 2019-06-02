javascript, redux, reducer, store, state, applicationstate, statemanagement, saga, devtools, reduxdevtools, localstorage

# Reducers

We can use combineReducer at a granular level to provide defaults

    import { combineReducers } from 'redux'

    const signingUp = (state = false, action) =>
      action.type === 'signup'
      ? true
      : action.type === 'loggedIn'
      ? false
      : state

    const token = (state = null, action) =>
      action.type === 'loggedIn'
      ? action.token
      : action.type === 'logout'
      ? null
      : state

    const signupError = (state = null, action) =>
      action.type === 'signupError'
      ? 'invalidUserInfo'
      : action.type === 'loggedIn'
      ? null
      : state

    const loggingIn = (state = false, action) =>
      action.type === 'login'
      ? true
      : action.type === 'loggedIn'
      ? false
      : state

    const loginError = (state = null, action) =>
      action.type === 'loginError'
      ? 'invalidEmailPassword'
      : action.type === 'loggedIn'
      ? null
      : state

    export default combineReducers({
      signingUp,
      token,
      signupError,
      loggingIn,
      loginError,
    })

# Setting up store with devtools

Setting up store with root reducer and root saga with optional dev tools

    import { Provider } from 'react-redux'
    import { createStore, applyMiddleware, compose } from 'redux'
    import createSagaMiddleware from 'redux-saga'

    import rootReducer from './reducers/root'
    import rootSaga from './sagas'

    const sagaMiddleware = createSagaMiddleware()
    const middleware = [ sagaMiddleware ]

    const enhancers = []
    const devToolsExtension = window.__REDUX_DEVTOOLS_EXTENSION__
    if (typeof devToolsExtension === 'function') {
      enhancers.push(devToolsExtension())
    }

    const composedEnhancers = compose(
      applyMiddleware(...middleware),
      ...enhancers
    )

    const store = createStore(
      rootReducer,
      // preloadedInitialState, // optional
      composedEnhancers
    )

    sagaMiddleware.run(rootSaga)

    ReactDOM.render(
      <Provider store={store}>
        ...
      </Provider>
    )


# Using local storage

We can save _partial_ state on local storage

    const loadStateFromStorage = () => {
      let state, item
      try {
        item = localStorage.getItem('state')
      } catch(e) {
        console.log(copy['errLoadingState'])
      }
      if (item !== null) {
        state = JSON.parse(item)
      }
      return state
    }

    const makeTokenListener = () => {
      let token = getToken(store.getState())
      return () => {
        if (token === getToken(store.getState())) {
          return
        }
        token = getToken(store.getState())
        try {
          if (token === null) {
            localStorage.removeItem('state')
          } else {
            localStorage.setItem('state', JSON.stringify(
              { ui: { token } }
            ))
          }
        } catch(e) {
          console.log(copy['errSavingState'])
        }
      }
    }

    const store = createStore(
      rootReducer,
      loadStateFromStorage(),
    )

    store.subscribe(makeTokenListener())

# Custom middlewhare for redirection

Saves the browser history object and uses it for redirecting when a new route is provided

    const makeRedirectMiddleware = () => {
      let lastHistory
      return store => next => action => {
        if (action.history !== undefined) {
          lastHistory = action.history
        }
        const result = next(action)
        if (action.goTo !== undefined && lastHistory !== undefined) {
          lastHistory.push(action.goTo)
          lastHistory = undefined
        }
        return result
      }
    }