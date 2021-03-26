# ReactJS Coding Standards

When applying an attribute that can have a character separated value, use array instead of managing the string manually.

```javascript
//good
var cssClasses = ['one'];

if(something === true) {
  cssClasses.push('two');
}

return (
  <div className={cssClasses.join(' ')}>...</div>
);

//bad
var cssClasses = 'one';

if(something === true) {
  cssClasses .= ' two';
}

return (
  <div className={cssClasses}>...</div>
);
```

While variables must only be PascalCase when they are a class and require the `new` keyword, react should always be required as `React` as that is what the JSX compiles to.

```javascript
//good
var React = require('react/addons');

//bad
var react = require('react/addons');
```

React elements must also be required with PascalCase since it is a requirment of react itself.

```javascript
//good
var AutoComplete = require('./auto-complete.component.jsx');

//bad
var autoComplete = require('./auto-complete.component.jsx');
```

Any method the returns a virtual dom must begin with `render`.

```javascript
//good
component.renderInput = function() {
  return (
    <input />
  );
},

//bad
component.getInput = function() {
  return (
    <input />
  );
},
```
Methods and properties of a component or mixin do not need leading underscore to note "private" since those methods and properties should only ever be used in the component itself.

```javascript
//good
component.onChange = function() {
  return (
    <input />
  );
},

//bad
component._onChange = function() {
  return (
    <input />
  );
},
```

You must specify the display name of the component.

```javascript
//good
var component = {};

component.displayName = 'Component';

module.export = React.createClass(component);

//bad
var component = {};

module.export = React.createClass(component);
```

Unless the component is only rendering 1 thing, the main render method should only contain JSX (and the allowable logic JSX can have), it should not contain pure JS code.

```javascript
//good
component.renderLabel = function() {
  var label = null;

  if(this.props.label) {
    label = (
      <label>{this.props.label}</label>
    )
  }

  return label;
};

component.renderInput = function() {
  var options = _.map(this.props.options, function(option) {
    var checked = this.props.value === option.value;

    if(option.displayPosition === 'left') {
      return (
        <label className="m-toggle" key={option.value}>
          {option.display}<input className="form-element__input m-radio m-left" type="radio" checked={checked} value={option.value} name={this.props.name} onChange={this.onChange} />
        </label>
      );
    } else {
      return (
        <label className="m-toggle" key={option.value}>
          <input className="form-element__input m-radio m-right" type="radio" value={option.value} checked={checked} name={this.props.name} onChange={this.onChange} />{option.display}
        </label>
      );
    }
  }.bind(this));

  return (
    <div className="form-element__radio-group">
      {options}
    </div>
  );
};

component.render = function() {
  return (
    <div className={this.getCssClasses().join(' ')}>
      {this.renderLabel()}
      <div className="form-element__input-container">
        {this.renderInput()}
      </div>
    </div>
  );
};

//bad
component.render = function() {
  var label = null;
  var options = _.map(this.props.options, function(option) {
    var checked = this.props.value === option.value;

    if(option.displayPosition === 'left') {
      return (
        <label className="m-toggle" key={option.value}>
          {option.display}<input className="form-element__input m-radio m-left" type="radio" checked={checked} value={option.value} name={this.props.name} onChange={this.onChange} />
        </label>
      );
    } else {
      return (
        <label className="m-toggle" key={option.value}>
          <input className="form-element__input m-radio m-right" type="radio" value={option.value} checked={checked} name={this.props.name} onChange={this.onChange} />{option.display}
        </label>
      );
    }
  }.bind(this));

  if(this.props.label) {
    label = (
      <label>{this.props.label}</label>
    )
  }
  
  return (
    <div className={this.getCssClasses().join(' ')}>
      {label}
      <div className="form-element__input-container">
        <div className="form-element__radio-group">
          {options}
        </div>
      </div>
    </div>
  );
};
```

This is the order that the component object properties must be defined in:

```javascript
var component = {};

//display name
component.displayName = 'Component';

//statics
components.statics = {...};

//mixins
component.mixins = [myMixin];

//prop type definations
component.propType = { ... };

//default prop types
component.getDefaultProps = function() { ... };

//initial state
component.getInitialState = function() { ... };

//lifecycle methods
component.componentDidMount: function() { ... };

//custom properties
component.myCustomData = 'test';

//custom methods
component.doSomething = function() { ... };

//custom event methods
component.onChange = function() { ... };

//render method with the main render being last
component.renderComplexItem = function() { ... };

component.render = function() { ... };

module.export = React.createClass(component);
```

Event methods must start with `on` to indicate it is attached to an event.

```javascript
//good
component.onChange = function() { ... };

//bad
component.change = function() { ... };
```

TODO: Event handlers with passable parameters 

Sometimes on a page component you are going to want to make sure that certain data is available before loading the page and you will use the `willTransitionTo` static method that the react router uses.  This type of data should be stored in an object called `preLoadedData` that is private to the file and is then used it to load the initial state of the page component.  Then the page component should only ever use the state and not the `preLoadedData` variable.

```javascript
//...
var preLoadedData = {};

pageComponent.statics = {
  willTransitionTo: function withResolvesComponentWillTransitionTo(transition, params, query, callback) {
    userStore.getUser(124).then(function(user) {
      preLoadedData.user = user;
      callback();
    });
  }
};

pageComponent.getInitialState = function pageComponentGetInitialState() {
  return {
    user: preLoadedData.user
  };
};

pageComponent.renderUserData = function pageComponentRenderUserData() {
  var userData = null;

  if (this.state.user) {
    userData = (
      <ul>
        <li className="user-id">{this.state.user.id}</li>
        <li className="user-username">{this.state.user.username}</li>
        <li className="user-first-name">{this.state.user.firstName}</li>
        <li className="user-last-name">{this.state.user.lastName}</li>
      </ul>
    );
  }

  return userData;
};
//...
```
