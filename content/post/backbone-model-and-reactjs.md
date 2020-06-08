---
title: "Backbone Model and Reactjs"
date: 2016-01-05T21:41:58+02:00
tags: ["reactjs", "backbone"]
category: "frontend"
draft: true
---
I want to write opinions after reading some articles about using [Backbone Model](http://backbonejs.org/#Model) in [ReactJs](https://facebook.github.io/react/) applications.  

# Why people still use the old "Backbone Model"?

 * In most scenario, the application was backbones-based (from start) and peoples want to migrate only the "View" layer to ReactJs. 
 * Some peoples choose Backbone Model because they are more familiar with. 

# Is this "duo" good or bad?

I didn't see any article telling the advantages of Backbone Model on a ReactJs applications in term of Perf / Testability / Maintainability...

From what I read, peoples usually: 

 1. store Backbone object in components state.
 2. change the backbone objects... (It means mutate the components state.)
 3. listen for changes and call `reactComponent.forceUpdate()` [sometimes with optimization](http://timecounts.github.io/backbone-react/#36)

It works, ReactJs as a View (greatly) improves the old Backbones-based applications. Make them better, cleaner than before.

**BUT**.. Backbone Model won't pull the best out of ReactJs rendering performance.

*"Normally you should try to avoid all uses of forceUpdate()..."* (Quote from [React docs](https://facebook.github.io/react/docs/component-api.html#forceupdate))

In order to get the best out of React. I will resume [this official guidelines](https://facebook.github.io/react/docs/advanced-performance.html) with  my understading and experiences: 

 * We should implement `shouldComponentUpdate()` on every components (at best!) or on performance-critical components (lists / big components)
 * `shouldComponentUpdate(nextProps, nextState)` compares the current state/props with the next state/props and tell React whether to render the components or not.
 * `shouldComponentUpdate()` is called frequently, it must to be really fast -> Deep-object comparison is not a choice here (neither Backbone Model) -> it leads to the uses of [`PureRenderMixin`](https://facebook.github.io/react/docs/pure-render-mixin.html) with [`immutable-js`](https://facebook.github.io/immutable-js/) and [cloning objects with ES6 `Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign#Cloning_an_object) 

**HOWEVER**.. I don't think Backbone Model is bad either. 
 
 * Both ReactJs and Backbone Model are desinged as unopinionated library. It is conceptually not wrong to combine them together.
 * Evens the rendering is not optimized to the best. The `render()` functions might be called more frequently than it should on the VirtualDOM.. But It is not the end of the world, because the `render()` functions are cheap (for the most part). The expensive updates on the real DOM happen only if there are real changes in the VirtualDOM. You might call `render()` a thousand times without triggering any update in the real DOM.
 * If you cannot use  [`PureRenderMixin`](https://facebook.github.io/react/docs/pure-render-mixin.html) with [`immutable-js`](https://facebook.github.io/immutable-js/) as in [the official guidelines](https://facebook.github.io/react/docs/advanced-performance.html):
   * Implement `toImmutable()` method to return Immutable versions of the data
   * Use the [`ReactImmutableRenderMixin`](https://github.com/jurassix/react-immutable-render-mixin). It compare state/props with `Immutable.is()` instead of the `===` strict reference.
 * `forceUpdate()` is not recommended to call, but it is not worse than `$digest()` and `$apply()` of **AngularJs** (and look how AngularJs works wonderfully)

### Conclusion

This duo is not the best, but it is OK. 

Nowadays (2015), modern frameworks (AngluarJs, VueJs..) leverage the uses of POJO object as model. In a ReactJs application, my first choice (for now) is [Redux](http://redux.js.org/).

# Model layer, Database and Application State

When we talk about the Model layer in a MV* pattern, peoples tends to match the Model with the database, and see the Model as a 1:1 binding with the database (as if the model is like a database in-memory cache). **It is a mistake**.

For me, **the Model stores the *Application States***. 

The *Application States* is in sync with the UI. So the Model will certainly store a piece of the database which is displaying.  The Model also stores *application states* which are not in the database: eg: `start_fetching` (display a progress), `fetch_success` (dispose the progress)...
