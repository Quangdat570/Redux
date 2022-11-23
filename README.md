# Redux là gì?
- Redux là một vùng chứa trạng thái có thể dự đoán được cho các ứng dụng JavaScript

- Nó giúp bạn viết các ứng dụng hoạt động nhất quán, chạy trong các môi trường 
khác nhau (máy khách, máy chủ và bản địa) và dễ kiểm tra. Trên hết, nó cung cấp trải nghiệm tuyệt vời cho nhà phát triển, chẳng hạn như chỉnh sửa mã trực tiếp kết hợp với traveling debugger


![Markdown](https://images.viblo.asia/3eca7a19-82be-4c9f-8bfc-cbeac838106b.png)

## Khi nào cần sử dụng Redux
- Dữ liệu được sử dụng ở nhiều nơi

- Bạn có thể sử dụng Redux cùng với React hoặc với bất kỳ thư viện  nào khác
 
1. Store gồm có 
- State: dữ liệu hiện tại được lưu trên state
- Reducer: là hàm biến đổi state cũ sang state mới
- Dispatcher: quản lý middlewares và chuyển dữ liệu xuống reducer.

2. Action = plain javascript object mô tả hành động.

## Ví dụ
``` php
import { createStore } from 'redux'
// Step 1: Định nghĩa reducer
// là 1 hàm js
// biến đổi state cũ thành state mới
// dựa trên action.type
function counter(state = 0, action) {
switch (action.type) {
case 'INCREMENT':
return state + 1
case 'DECREMENT':
return state - 1
default:
return state
}
}
// Step 2: Tạo store với reducer
// Its API is { subscribe, dispatch, getState }. ( 3 hàm của store )
// subscribe - Đăng ký một hàm chạy khi mà trạng thái thay đổi
// getState - lấy state hiện tại
// dispatch - tạo/ thực hiện ra những Action
let store = createStore(counter)
// Step 3: Subscribe to state changes to update UI
store.subscribe(() => console.log(store.getState()))
// Step 4: Dispatch action to update redux state
// The only way to mutate the internal state is to dispatch an action.
store.dispatch({ type: 'INCREMENT' }) // 1
store.dispatch({ type: 'INCREMENT' }) // 2
store.dispatch({ type: 'DECREMENT' }) // 1
```

## Các bước setup trong ReactJS
1. Cài đặt package react-redux và redux
``` php
npm install --save redux react-redux

```

``` php
src
|__ reducers
| |__ hobby.js
| |__ todo.js
| |__ ... (one reducer per file)
| |__ index.js (root reducer)
|
|__ actions
| |__ hobby.js
| |__ todo.js
| |__ ...
|
|__ pages
| |__ HomePage/index.jsx (connect to redux)
|
|__ store.js (reducers, init state, middlewares)
|__ index.js (setup Store Provider)
```

2. Setup reducers và rootreducer
```php
// reducers/hobby.js
const initialState = {
list: ['Listening to music'],
selectedId: null,
}
const hobbyReducer = (state = initialState, action) => {
switch (action.type) {
case 'ADD_HOBBY': {
const newList = [...state.list];
newList.push(action.payload);
return {
...state,
list: newList,
}
}
default:
return state;
}
};
export default hobbyReducer;
```

```php
// reducers/index.js (ROOT)
const rootReducer = combineReducers({
hobby: hobbyReducer,
})
export default rootReducer;
```

3. Setup redux store
```php
// src/store.js
const store = createStore(rootReducer);
export default store;
```

4. Setup Store Provider cho toàn app src/index.js
```php
const Main = () => (
<Provider store={store}>
<App />
</Provider>
)
```

5. Connect vào redux từ reactjs component

- Với class component: dùng HOC connect()
- Với functional component: dùng hooks useSelector() và useDispatch()
```php
function HomePage(props) {
    const hobbyList = useSelector(state => state.hobby.list);
    const dispatch = useDispatch();
    const handleAddHobbyClick = () => {
    const newHobby = 'Coding';
    dispatch({
    type: 'ADD_HOBBY',
    payload: newHobby,
    });
    }
    return (
    <div className="home-page">
    <HobbyList data={hobbyList} />
    <button onClick={handleAddHobbyClick}>Add new hobby</button>
    </div>
    );
}
export default HomePage;
```

# Reudx Toolkit
- RTK là một thư viện giúp mình viết Redux tốt hơn, dễ hơn và đơn giản hơn. (tiêu chuẩn để viết Redux)

- Ba vấn đề làm nền tảng ra đời RTK:
1. Cấu hình (config) Redux quá phức tạp
2. Phải cài đặt thủ công nhiều packgage để Redux có thể hoạt động hiệu quả
3. Redux yêu cầu rất nhiều `boilerplate code`

## RTK bao gồm những gì?
`configureStore()`
- Có sẵn Redux DevTools
- Có sẵn redux-thunk để thực hiện async actions

```php
// Khi chưa có Redux Toolkit
// store.js
import { createStore, applyMiddleware, compose } from 'redux';
import thunkMiddleware from 'redux-thunk';
import rootReducer from './reducers';
// Enable to use redux dev tool in development mode
const composeEnhancers = 'development' === process.env.NODE_ENV
? (window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose)
: compose;
// Use redux-thunk as a redux middleware
const enhancer = composeEnhancers(applyMiddleware(thunkMiddleware));
const store = createStore(rootReducer, {}, enhancer);
export default store;
```

```php
// Khi đã có redux toolkit 🤣
// store.js
import { configureStore } from '@reduxjs/toolkit'
import rootReducer from './reducers'
const store = configureStore({ reducer: rootReducer })
```

`createReducer()`
```php
// Không có Redux Toolkit
function counterReducer(state = 0, action) {
switch (action.type) {
case 'increment':
return state + action.payload
case 'decrement':
return state - action.payload
default:
return state
}
}
```

```php
// Có Redux Toolkit
// - Mỗi key là một case
// - Không cần handle default case
const counterReducer = createReducer(0, {
increment: (state, action) => state + action.payload,
decrement: (state, action) => state - action.payload
})
```

`createAction()`
```php
// Không có redux toolkit
const INCREMENT = 'counter/increment'
function increment(amount) {
return {
type: INCREMENT,
payload: amount
}
}
const action = increment(3)
// { type: 'counter/increment', payload: 3 }
```


```php
// Có redux toolkit
const increment = createAction('counter/increment')
const action = increment(3)
// returns { type: 'counter/increment', payload: 3 }
console.log(increment.toString())
// 'counter/increment'
```

## Setup một ví dụ đơn giản sử dụng RTK
```php
// 1. Setup todo slice
// todoSlice.js
const todoSlice = createSlice({
name: 'todos',
initialState: [],
reducers: {
addPost(state, action) {
state.push(action.payload);
},
removePost(state, action) {
state.splice(action.payload, 1)
}
}
});

const { actions, reducer } = todoSlice;
export const { addPost, removePost } = actions;
export default reducer;
```

```php
// 2. Setup redux store
// store.js
import { configureStore } from '@reduxjs/toolkit';
import todoSlice from 'features/todos/todoSlice';
const store = configureStore({
reducer: {
todos: todoSlice
},
})
```

```php
// 3. Bind Redux Provider to App
// src/index.js
import { Provider } from 'react-redux';
import store from './store';
import App from './App';
function Main() {
return (
<Provider store={store}>
<App />
</Provider>
)
}
```


```php
// 4. Using redux in component
// todo.jsx
import { useDispatch, useSelector } from 'react-redux';
import { removeTodo } from 'features/todos/todoSlice';
function Todo() {
const dispatch = useDispatch();
const todoList = useSelector(state => state.todos);
const handleTodoClick = (todo, idx) => {
const action = removeTodo(idx);
dispatch(action);
}
return (
<ul>
{todoList.map((todo, idx) => (
<li key={todo.id} onClick={() => handleTodoClick(todo, idx)}>
{todo.title}
</li>
))}
</ul>
)
}
```


# Example
```PHP
import { createSlice, configureStore } from '@reduxjs/toolkit'

const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    incremented: state => {
      // Redux Toolkit allows us to write "mutating" logic in reducers. It
      // doesn't actually mutate the state because it uses the Immer library,
      // which detects changes to a "draft state" and produces a brand new
      // immutable state based off those changes
      state.value += 1
    },
    decremented: state => {
      state.value -= 1
    }
  }
})

export const { incremented, decremented } = counterSlice.actions

const store = configureStore({
  reducer: counterSlice.reducer
})

// Can still subscribe to the store
store.subscribe(() => console.log(store.getState()))

// Still pass action objects to `dispatch`, but they're created for us
store.dispatch(incremented())
// {value: 1}
store.dispatch(incremented())
// {value: 2}
store.dispatch(decremented())
// {value: 1}
```

