javascript, redux, saga, sagas

https://redux-saga.js.org/docs/introduction/BeginnerTutorial.html

Install

    yarn add redux-saga

Working example

    import * as effects from 'redux-saga/effects'
    import { login } from '../api.js'
    import { getPreference } from './selectors.js'
    import { loggedIn } from './actions.js'

    export const loginSaga = function* () {
      const callApi = function* (action) {
        const preference = yield effects.select(getPreference)
        const loginResult = yield effects.call(login, action.user, action.pass, preference)
        yield effects.put(loggedIn())
      }
      yield effects.takeLatest(['login'], callApi)
      yield effects.fork(callApi)
    }


Setup within project

    import createSagaMiddleware from 'redux-saga'
    import rootReducer from './reducers.js'
    import rootSaga from './sagas.js'

    const sagaMiddleware = createSagaMiddleware()
    const store = createStore(
      rootReducer,
      composeEnhancers(applyMiddleware(thunkMiddleware, sagaMiddleware))
    )

    sagaMiddleware.run(sagas[key])

The root saga

    export default function* () {
      yield effects.all([
        loginSaga(),
        // ...
      ])
    }

# Context

Throw it

    createSagaMiddleware({ context: { history }})
    
Catch it

    const history = yield getContext('history')

# Flows

Just an action matching pattern

    const loginError = ({type, messageType, key}) => // pattern
      type === 'addMessage' && messageType === 'error' &&
          (key === 'login' || key === 'network_slow')

Flow

    const loginFlow = function* () {
      yield fork(passwordForgot)
      yield fork(passwordReset)
      let task
      while (true) {
        const token = yield select(getToken)
        if (!token) {
          const { email, password } = yield take('login')
          // fork return a Task object
          task = yield fork(authorize, { email, password })
        }
        yield fork(authenticatedFlows)
        const action = yield take(['logout', loginError])
        if (action.type === 'logout')
          yield cancel(task)
      }
    }

# Custom effects

Throttle special does not call the trailing action after time has elapsed

    export const throttleSpecial = (pattern, task, ...args) =>
      fork(function*() {
        while (true) {
          const action = yield take(pattern)
          yield fork(task, ...args, action)
          yield delay(parseInt(process.env.REACT_APP_THROTTLE))
        }
      })

Use it on other sagas

    yield fork(function* () { yield throttleSpecial('FETCH', fetchAll)})
