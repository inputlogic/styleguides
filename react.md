# React Style Guide

Just some React best practices, or guidances, that are preferred. However, any of these 'rules' can be challenged -- make a PR! As for general JavaScript style guide, just use [StandardJS](https://standardjs.com/).

## Table of Contents

  1. [Basic Rules](#basic-rules)
  1. [Class vs stateless](#class-vs-stateless)
  1. [Naming](#naming)
  1. [Organization](#organization)
  1. [Alignment](#alignment)
  1. [Props](#props)
  1. [Refs](#refs)
  1. [Parentheses](#parentheses)
  1. [Tags](#tags)
  1. [Methods](#methods)
  1. [Ordering](#ordering)

## Basic Rules

  - Only include one React component per file.
    - However, multiple [Stateless, or Pure Components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) are allowed per file.
  - Always use JSX syntax.
  - Do not use `React.createElement`.
  - Use Classes when you need lifecycle methods.
  - Do not use Hooks (for now).
  - [Do not use mixins](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)

## Class vs stateless

  - If you have internal state and/or refs, use a class: `class extends React.Component`.
    And if you don’t have state or refs,  use an arrow function: `const Listing = ({ hello }) => <div>{hello}</div>`

## Naming

  - **Filename**: Use kebab-case for filenames: `reservation-card.js`.
  - **Reference Naming**: Use PascalCase for React components and camelCase for their instances.

    ```jsx
    // bad
    import reservationCard from './reservation-card'

    // good
    import ReservationCard from './reservation-card'

    // bad
    const ReservationItem = <ReservationCard />

    // good
    const reservationItem = <ReservationCard />
    ```

  - **Component Naming**: Use the filename as the component name. For example, `reservation-card.js` should have a reference name of `ReservationCard`. However, for root components of a directory, use `index.jsx` as the filename and use the directory name as the component name:

    ```jsx
    // bad
    import Footer from './footer/footer'

    // bad
    import Footer from './footer/index'

    // good
    import Footer from './footer'
    ```

  - **Higher-order Component Naming**: Use a composite of the higher-order component’s name and the passed-in component’s name as the `displayName` on the generated component. For example, the higher-order component `withFoo()`, when passed a component `Bar` should produce a component with a `displayName` of `withFoo(Bar)`.

    > Why? A component’s `displayName` may be used by developer tools or in error messages, and having a value that clearly expresses this relationship helps people understand what is happening.

    ```jsx
    // bad
    export default function withFoo (WrappedComponent) {
      return function WithFoo (props) {
        return <WrappedComponent {...props} foo />
      }
    }

    // good
    export default function withFoo (WrappedComponent) {
      function WithFoo (props) {
        return <WrappedComponent {...props} foo />
      }

      const wrappedComponentName = WrappedComponent.displayName
        || WrappedComponent.name
        || 'Component'

      WithFoo.displayName = `withFoo(${wrappedComponentName})`
      return WithFoo
    }
    ```

  - **Props Naming**: Avoid using DOM component prop names for different purposes.

    > Why? People expect props like `style` and `className` to mean one specific thing. Varying this API for a subset of your app makes the code less readable and less maintainable, and may cause bugs.

    ```jsx
    // bad
    <MyComponent style='fancy' />

    // bad
    <MyComponent className='fancy' />

    // good
    <MyComponent variant='fancy' />
    ```


## Organization

  [Feature Folders](http://marisks.net/2016/02/16/feature-folders-vs-tech-folders/) over Tech Folders. This allows code that relies on other code to always live in adjacent files. It is encouraged to keep code in feature folders, unless it is generic enough that it is used across many 'features'. When some code is used widely throughout your app, it will need to live in a more general folder, E.g. `util/`.

  ```
  apps/     # In React Native, this may be called `screens/` - this is where 'feature' folders will exist
  elements/ # Generic, widely-used Components
  forms/    # Generic, widely-used Form Components (these should not include data/submission logic)
  hoc/      # Generic, widely-used Higher order Components
  modals/   # Generic, widely-used Modal Components
  nav/      # In React Native, your Navigation components 
  util/     # Little, helpful functions
  consts.js # Constants
  index.js  # The root of your app!
  store.js  # Where you initialize your store and export a store instance
  ```

## Alignment

  - Follow these alignment styles for JSX syntax.

    ```jsx
    // bad
    <Foo superLongParam='bar'
         anotherSuperLongParam='baz' />

    // good
    <Foo
      superLongParam='bar'
      anotherSuperLongParam='baz'
    />

    // if props fit in one line then keep it on the same line
    <Foo bar='bar' />

    // children get indented normally
    <Foo
      superLongParam='bar'
      anotherSuperLongParam='baz'
    >
      <Quux />
    </Foo>

    // bad
    {showButton &&
      <Button />
    }

    // bad
    {
      showButton &&
        <Button />
    }

    // good
    {showButton && (
      <Button />
    )}

    // good
    {showButton && <Button />}
    ```

## Props

  - Always use camelCase for prop names.

    ```jsx
    // bad
    <Foo
      UserName='hello'
      phone_number={12345678}
    />

    // good
    <Foo
      userName='hello'
      phoneNumber={12345678}
    />
    ```

  - Omit the value of the prop when it is explicitly `true`. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

    ```jsx
    // bad
    <Foo
      hidden={true}
    />

    // good
    <Foo
      hidden
    />

    // good
    <Foo hidden />
    ```

  - Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role='presentation'`.

    ```jsx
    // bad
    <img src='hello.jpg' />

    // good
    <img src='hello.jpg' alt='Me waving hello' />

    // good
    <img src='hello.jpg' alt=' />

    // good
    <img src='hello.jpg' role='presentation' />
    ```

  - Do not use words like 'image', 'photo', or 'picture' in `<img>` `alt` props.

    > Why? Screenreaders already announce `img` elements as images, so there is no need to include this information in the alt text.

    ```jsx
    // bad
    <img src='hello.jpg' alt='Picture of me waving hello' />

    // good
    <img src='hello.jpg' alt='Me waving hello' />
    ```

  - Use only valid, non-abstract [ARIA roles](https://www.w3.org/TR/wai-aria/#usage_intro).

    ```jsx
    // bad - not an ARIA role
    <div role='datepicker' />

    // bad - abstract ARIA role
    <div role='range' />

    // good
    <div role='button' />
    ```

  - Do not use `accessKey` on elements.

    > Why? Inconsistencies between keyboard shortcuts and keyboard commands used by people using screenreaders and keyboards complicate accessibility.

    ```jsx
    // bad
    <div accessKey='h' />

    // good
    <div />
    ```

  - Avoid using an array index as `key` prop, prefer a stable ID.

    > Why? Not using a stable ID [is an anti-pattern](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) because it can negatively impact performance and cause issues with component state.

    ```jsx
    // bad
    {todos.map((todo, index) =>
      <Todo
        {...todo}
        key={index}
      />
    )}

    // good
    {todos.map(todo => (
      <Todo
        {...todo}
        key={todo.id}
      />
    ))}
    ```

  - Use spread props sparingly.

    > Why? Otherwise you’re more likely to pass unnecessary props down to components. And for React v15.6.1 and older, you could [pass invalid HTML attributes to the DOM](https://reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html).

    Exceptions:

      - HOCs that proxy down props and hoist propTypes

      ```jsx
      function HOC (WrappedComponent) {
        return class Proxy extends React.Component {
          Proxy.propTypes = {
            text: PropTypes.string,
            isLoading: PropTypes.bool
          }

          render () {
            return <WrappedComponent {...this.props} />
          }
        }
      }
      ```

    - Spreading objects with known, explicit props. This can be particularly useful when testing React components with Mocha’s beforeEach construct.

    ```jsx
    export default function Foo {
      const props = {
        text: '',
        isPublished: false
      }

      return (<div {...props} />)
    }
    ```

    Notes for use:
    Filter out unnecessary props when possible.

    ```jsx
    // bad
    render () {
      const { irrelevantProp, ...relevantProps  } = this.props
      return <WrappedComponent {...this.props} />
    }

    // good
    render () {
      const { irrelevantProp, ...relevantProps  } = this.props
      return <WrappedComponent {...relevantProps} />
    }
    ```

## Refs

  - Always use [ref callbacks](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs).

    ```jsx
    // bad
    <Foo
      ref='myRef'
    />

    // good
    <Foo
      ref={(ref) => { this.myRef = ref }}
    />
    ```

## Parentheses

  - Wrap JSX tags in parentheses when they span more than one line.

    ```jsx
    // bad
    render () {
      return <MyComponent variant='long body' foo='bar'>
               <MyChild />
             </MyComponent>
    }

    // good
    render () {
      return (
        <MyComponent variant='long body' foo='bar'>
          <MyChild />
        </MyComponent>
      )
    }

    // good, when single line
    render () {
      const body = <div>hello</div>
      return <MyComponent>{body}</MyComponent>
    }
    ```

## Tags

  - Always self-close tags that have no children.

    ```jsx
    // bad
    <Foo variant='stuff'></Foo>

    // good
    <Foo variant='stuff' />
    ```

  - If your component has multi-line properties, close its tag on a new line.

    ```jsx
    // bad
    <Foo
      bar='bar'
      baz='baz' />

    // good
    <Foo
      bar='bar'
      baz='baz'
    />
    ```

## Methods

  - Use arrow functions to close over local variables.

    ```jsx
    function ItemList (props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={() => doSomethingWith(item.name, index)}
            />
          ))}
        </ul>
      )
    }
    ```

  - Bind event handlers for the render method in the constructor.

    > Why? A bind call in the render path creates a brand new function on every single render.

    ```jsx
    // bad
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />
      }
    }

    // good
    class extends React.Component {
      constructor(props) {
        super(props)

        this.onClickDiv = this.onClickDiv.bind(this)
      }

      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />
      }
    }
    ```
    
  - You could also use static arrow functions instead of binding a method:
  
    ```jsx
    class Statics extends React.Component {
      onClickDiv = () => {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />
      }
    }
    ```
  
    Note, this is not technically equivalent to a bound method. At the time of writing, Babel will compile a static arrow function as follow:
    
    ```jsx
    class Static extends React.Component {
      constructor(...args) {
        var _temp;

        return _temp = super(...args), this.onClickDiv = () => {
          // do stuff
        }, _temp;
      }
    ```
    
    In practical terms, this means the method will not exist on the instance prototype. This is only a problem if you are extending a Class (don't do it!), or hoping to mock a method in your tests. There could be other edge cases that I am not currently aware of.

  - Be sure to return a value in your `render` methods.

    ```jsx
    // bad
    render() {
      (<div />)
    }

    // good
    render() {
      return (<div />)
    }
    ```

## Ordering

  - Ordering for `class extends React.Component`:

    1. optional `static` methods
    1. `constructor`
    1. `getChildContext`
    1. `componentWillMount`
    1. `componentDidMount`
    1. `componentWillReceiveProps`
    1. `shouldComponentUpdate`
    1. `componentWillUpdate`
    1. `componentDidUpdate`
    1. `componentWillUnmount`
    1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
    1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
    1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
    1. `render`

  - How to define `propTypes`, `defaultProps`, `contextTypes`, etc...

    ```jsx
    import React from 'react'
    import PropTypes from 'prop-types'

    const propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    }

    const defaultProps = {
      text: 'Hello World',
    }

    class Link extends React.Component {
      static methodsAreOk() {
        return true
      }

      render() {
        return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
      }
    }

    Link.propTypes = propTypes
    Link.defaultProps = defaultProps

    export default Link
    ```
