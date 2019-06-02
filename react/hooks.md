react, hook, hooks, reacthooks

# Custom hook

Dispatch Redux action to request data. Access the contect and keep a reference variable between renders.

    import { useEffect, useContext,  useRef } from 'react'
    import { ReactReduxContext } from 'react-redux'
    import { isRequesting } from 'select/ui'

    export const useFetchData = request => {
      const { store: { dispatch }, storeState: state } = useContext(ReactReduxContext)
      const fetching = isRequesting(request)(state)
      const mountedRef = useRef(false)
      ...

We useEffect to request the data.

*Note: the ref can probably be replaced by a variable and a closure. By passing an empty array we can limit the effect to mount/unmount (see further down)*

      ...
      useEffect(() => {
        if (mountedRef.current) return
        mountedRef.current = true
        if (!fetching) dispatch({ type: 'fetchData', request })
      }, [fetching])
      ...
      
Hooks can have a return value

    ...
      return fetching || !mountedRef.current
    }

# Redux state/dispatch hooks

Bind to redux actions

    import { useContext } from 'react'
    import { ReactReduxContext } from 'react-redux'

    export let useActions = mappingFunc => {
      let { store: { dispatch } } = useContext(ReactReduxContext)
      return mappingFunc(dispatch)
    }

Map state to properties, memoized

    import { useContext, useState, useEffect } from 'react'
    import { ReactReduxContext } from 'react-redux'

    export let useStore = mappingFunc => {
      let { store } = useContext(ReactReduxContext)
      let previousProps = mappingFunc(store.getState())
      let [ props, setProps ] = useState(previousProps)

      useEffect(() => {
        let unsubscribe = store.subscribe(() => {
          let newProps = mappingFunc(store.getState())
          let someChanged = Object.entries(newProps).some(
            ([propKey, propValue]) => propValue !== previousProps[propKey]
          )
          if (someChanged) {
            setProps(newProps)
            previousProps = newProps
          }
        })
        return /* cleanup */ () => {
          unsubscribe()
        }
      }, [] /* effect only run on mount/unmount */ )
      return props
    }

## Usage

    import React from 'react'
    import { bindActionCreators } from 'redux'
    import { useStore, useActions } from 'hooks'
    import { getBaseUrl } from 'selectors'
    import { setBaseUrl } from 'actions'

    export let SettingsForm = () => {

      let { baseUrl } = useStore(
        state => ({ baseUrl: getBaseUrl(state) })
      )

      let { setBaseUrl } = useActions(
        dispatch => bindActionCreators({ setBaseUrl }, dispatch)
      )

      return <input value={baseUrl} onChange={setBaseUrl} />
    }

# Effects, state, reducer

State

    // View is a prop, the object we are modifying
    const pristine = view || newViewTemplate
    const [name, setName] = useState('')

Effect with memoized property

    useEffect(() => {
      setName(pristine.name)
    }, [pristine])
    
Effect that only triggers on mount and unmount

      useEffect(() => {
        console.log('mounted')
        return /* cleanup */ () => {
          console.log('unmounted')
        }
      }, [] /* effect only run on mount/unmount */ )

Reducer definition (no initial value)

    const touchedReducer = (
      state,
      { touch }
    ) => {
      if (touch) {
        return { ...state, [touch]: true }
      }
      return state
    }

useReducer hook with initial value

    const [
      touchedState,
      dispatch,
    ] = useReducer(touchedReducer, {
      name: false,
    })
