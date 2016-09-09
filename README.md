# Simplex
A simpler way to Flux.

## What is Flux

Flux is a pattern that encourages undirectional data flow throughout an application making it easy to reason about and manage application state change. Flux has 3 primary building blocks:

* Actions - A simple object that describes <b>what</b> happened (e.g. user interaction or server sent event) and carries some data related to the `action` that occured.
* Store - A class that manages either the `state` of the entire application or the state of a `particular domain` in the application. 
* Dispatcher - An application level entity that `dispatches` actions to stores which essentially calls a method on the store to change its state depending on the type of action and the data the action provides.

Commonly in a front-end applications, view components `subscribe` to changes in the state by registering themselves with the store. As a result, views always receive the latest version of the application state.

## Why Simplex

Cleaner code. Simplex provides useful libraries and abstractions which can be used in developing flux based applications. Since Simplex is written in typescript, static code analysis comes out of the box.

# Actions

Creating an action means creating a simple class that extends the `Action` interface. The `type` and `data` properties are required. Suitable defaults can be applied in the constructor.

```javascript
  @Action(Todo)
  class CreateTodo {
  
    constructor(todo: Todo) {
      super("TODO_CREATE", todo);
    }
  }
  
  // Usage
  const todo = {
    id: 1,
    task: 'Do some coding',
    completed: false
  };
  
  const createTodoAction = new CreateTodo(todo);
```

# Stores

Creating a store means creating a class that extends the `Store` interface. The store interface ensures that the implementation must return a `state`. The store changes state by implementing various methods that update the state. An automatic `notification` is sent to all listeners registered via the `subscribe` method whenever the state changes. The arguments to the listener are the `current and previous states`.

```javascript
  @Store
  class TodoStore implements {
    
    private _initialState = [];
    
    constructor() {
      super(this._initialState);
    }
    
    @OnAction("CREATE_TODO")
    addTodo(action) {
      this._state.push(action.data);
    }
    
    @OnAction("COMPLETE_TODO")
    completeTodo(action) {
      this._state = this.state.map(todo => {
        todo.completed = todo.id === action.data.id;
        return todo;
      });
    }
  }
  
  // Usage
  const todoStore = new TodoStore();
  
  todoStore.subscribe((currState, prevState) => console.log(currState, prevState));
```

## Dispatcher
There is one application level dispatcher for the entire application. The dispatcher takes all the different stores an application may have and dispatches actions to them.

```javascript

  @Dispatcher
  class AppDispatcher() {
    constructor(stores: Array<Store>) {
      super(stores);
    }
  }
  
  // Usage
  const appDispatcher = new AppDispatcher([todoStore]);
  
  appDispatcher.dispatch(createTodoAction);
```
