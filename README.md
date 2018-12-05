# Hyperapp micro rewrite demo on React Native

(with deviation from Hyperapp API)

based on code generated by `react-native init`

by Christopher J. Brody aka @brodybits (Chris Brody)

LICENSE: ISC OR MIT

## About

Simple counter app demo on React Native with mini rewrite of Hyperapp state/action management, with support for side effects, inspired by Hyperapp demo in <https://github.com/hyperapp/hyperapp#getting-started>.

MOTIVATION:

- <https://github.com/hyperapp/hyperapp/issues/168> - desire to use Hyperapp API on React Native
- <https://medium.com/hyperapp/hyperapp-for-redux-refugees-2507c9dd1ddc>
- <https://github.com/hyperapp/hyperapp/issues/641> (desire to use Ultradom as a dependency), especially rejected idea in <https://github.com/hyperapp/hyperapp/issues/641#issuecomment-376445893> (remove VDOM from Hyperapp)
- <https://github.com/hyperapp/hyperapp/issues/672> - Hyperapp 2.0 API under discussion, desire to experiment on React Native as well

See also: <https://github.com/brodybits/hyperapp-api-demo-on-inferno-and-ultradom> - prototypic rewrite of Hyperapp state/action management working with functional (stateless) components on multiple VDOM APIs: Inferno (React API) and Ultradom

NOTE: This project no longer conforms to the Hyperapp API for specifying actions and views. Version with partial conformance to Hyperapp 1.x API is available in the [strict-api-1 branch](https://github.com/brodybits/hyperapp-micro-rewrite-demo-on-react-native/tree/strict-api-1).

## Build and run

First step: `npm install`

Android: `react-native run-android`

iOS: `react-native run-ios` or open `ios/hyperappMiniRewriteDemoOnReactNative.xcodeproj` and run from Xcode

## Run on codesandbox.io

Paste the contents of [App.js](./App.js) into `App.js` in <https://codesandbox.io/s/q4qymyp2l6>

(see <https://github.com/necolas/react-native-web#quick-start>)

## Quick tour

React Native App with initial state, actions, effects (side effects such as I/O, timers, I/O, other asynchronous operations, and other non-pure functions), and view in JSX (partially inspired by Hyperapp demo app in <https://github.com/hyperapp/hyperapp#getting-started>):

```jsx
const App = () => (
  <ManagedAppView
    state={{count: 0}}
    actions={{
      up: (state) => ({ count: state.count + 1 }),
      dn: (state) => ({ count: state.count - 1 }),
    }}
    effects = {{
      delayedUpAndDn: (actions, effects) => {
        setTimeout(effects.upAndDn, 500)
      },
      upAndDn: (actions, effects) => {
        actions.up()
        setTimeout(actions.dn, 500)
      }
    }}>
    <MyAppView />
  </ManagedAppView>
)

const MyAppView = ({state, actions, effects}) => (
  <View style={styles.container}>
    <Text style={styles.welcome}>
      Hyperapp micro rewrite demo on React Native
    </Text>
    <Button
      onPress={actions.up}
      title="Up (+1)"
    />
    <Text style={styles.welcome}>
      {state.count}
    </Text>
    <Button
      onPress={actions.dn}
      title="Down (-1)"
    />
    <Text>
      ...
    </Text>
    <Button
      onPress={effects.delayedUpAndDn}
      title="Up and down with delay"
    />
  </View>
)

export default App
```

Generic `ManagedView` component that supports the Hyperapp action/state/view API:

```js
const ManagedAppView = createReactClass({
  getInitialState() {
    const ac = {}
    const ef = {}
    const self = this
    for (let a in this.props.actions) ac[a] = () => {
      self.setState(prev => ({ac: ac, st: (this.props.actions[a](prev.st))}))
    }
    for (let e in this.props.effects) ef[e] = () => {
      this.props.effects[e](ac, ef)
    }
    return {ac: ac, st: this.props.state, ef: ef}
  },
  render() {
    return React.Children.map(this.props.children, ch => (
      React.cloneElement(ch, {state: this.state.st, actions: this.state.ac, effects: this.state.ef})
    ))
  }
})
```


## TODO

Near-term:

- Use `react-native-web` to support build and run on browser

Others:

- [(BREAKING) View API changes from "Hyperapp 2.0", hopefully closer to standard functional component API (brodybits/hyperapp-api-demo-on-inferno-and-ultradom#5)](https://github.com/brodybits/hyperapp-api-demo-on-inferno-and-ultradom/issues/5)
- Pass event data to action and effect functions
- [Make this even more functional (#7)](https://github.com/brodybits/hyperapp-micro-rewrite-demo-on-react-native/issues/7) ref: <https://www.bignerdranch.com/blog/destroy-all-classes-turn-react-components-inside-out-with-functional-programming/>
- [support browser (#1)](https://github.com/brodybits/hyperapp-micro-rewrite-demo-on-react-native/issues/1)
- Publish generic (common) functionality in one or more npm packages
- [CC0 (public domain) API specification (brodybits/hyperapp-api-demo-on-inferno-and-ultradom#1)](https://github.com/brodybits/hyperapp-api-demo-on-inferno-and-ultradom/issues/1)
- demo on <https://github.com/dabbott/react-native-web-player>
- TodoMVC app, likely based on <https://github.com/dangvanthanh/hyperapp-todomvc>
- better styling
- [other open issues](https://github.com/brodybits/hyperapp-micro-rewrite-demo-on-react-native/issues)
