react, router, reactrouter, reactrouterdom, redux

# Shallow integrate with redux

    import createBrowserHistory from 'history/createBrowserHistory'
    import rootReducer from 'reducers/root'

    // Redux, command redirects with any action
    // for redirecting when a new route is provided
    const makeRedirectMiddleware = history => store => next => action => {
      const result = next(action)
      const { redirectTo } = action
      if (redirectTo) history.push(redirectTo)
      return result
    }

    const history = createBrowserHistory({basename: process.env.PUBLIC_URL})

    // ALTERNATIVELY handle redirects in Saga!
    const sagaMiddleware = createSagaMiddleware({ context: { history }})
    const middleware = [ sagaMiddleware, makeRedirectMiddleware(history) ]

    const enhancers = []
    const devToolsExtension = window.__REDUX_DEVTOOLS_EXTENSION__
    if (typeof devToolsExtension === 'function') {
      enhancers.push(devToolsExtension())
    }
    
    const store = createStore(
      rootReducer,
      compose(applyMiddleware(...middleware), ...enhancers)
    )

    // Have Reducers be aware of redirects
    history.listen(({pathname}, action) => {
      // location is an object like window.location
      store.dispatch({ type: 'navigation', pathname, action })
    })

    ReactDOM.render(
      <Router history={history}>
        <Provider store={store}>
          <App />
        </Provider>
      </Router>,
      document.getElementById('root')
    )
