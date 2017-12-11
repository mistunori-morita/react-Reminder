# React-Reminder
## プロジェクトの作成
- 好きなディレクトリを作成、そこに移動をして`create-react-app xxx(名前)`を行う

## PORTを分けたい場合
- package.jsonに記述し直す
```html

"scripts": {
  //ここのPORT=XXXX　好きな番号でポート設定できる
  "start": "PORT=3007 react-scripts start",

```
- 変更後、起動しない場合は`npm install`し直すといけるっぽい

## Reduxを導入
- プロジェクト作成後、`npm install redux react-redux --save`を行う
- public/index.htmlにreact-bootstrapを貼り付ける


## プロジェクトの設定を行う(react-bootstrap等)
- `https://react-bootstrap.github.io/getting-started.html`
```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/latest/css/bootstrap.min.css">
```

## srcを整理して新しくプロジェクトを作成
- src/以下を削除して新しく作成する
- src/index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';


ReactDOM.render(
  <div>Reminder Pro</div>, document.getElementById('root')
)
```

- src/componentsのフォルダを作成する
- src/components/App.jsxを作成する
```js
import React, { Component } from 'react';

class App extends Component {
  render(){
    return(
      <div className="App">
        <div className="title">
          Reminder Pro
        </div>
        <div className="form-inline">
          <div className="form-group">
            <input className="form-control"
              placeholder="I have to..."/>
          </div>
          <button type="button" className="btn btn-success">Add Reminder</button>
        </div>
      </div>
    )
  }
}

export default App;
//作成後,index.jsにインポートする
```

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';

ReactDOM.render(
  //作成したAppコンポーネントに書き換える
  <App />, document.getElementById('root')
)

//できて入れば入力フォーム＋緑色のボタンが作成されている
```

## アクションとアクションクリエーター
- src/constance.jsを作成
```js
export const ADD_REMINDER = 'ADD_REMINDER';

```
- src/actions/index.jsを作成
```js
import { ADD_REMINDER } from '../constance';

#Action
//Actionは「何をする」という情報を持ったオブジェクトです。Actionはtypeプロパティを必ず持つ必要があります

export const addReminder = (text) => {
  const action = {
    type: ADD_REMINDER,
    text
  }
  console.log('action in addReminder', action);
  return action;
}

```

## Reduxの導入
- src/index.jsの修正
```js

import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';

//この２つをインポート
import { Provider } from 'react-redux';
import { createStore } from 'redux'

const store = createStore();

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
   document.getElementById('root')
)

```
- src直下にreducersフォルダを作成し、その中にindex.jsを作成
```js
import { ADD_REMINDER } from '../constance';

const reminder = (action) => {
  return {
    text: action.text,
    id: Math.random()
  }
}

const reminders = (state = [], action) => {
  let reminders = null;
  switch(action.type){
    case ADD_REMINDER:
    reminders = [...state, reminder(action)];
    console.log('reinders as state', reminders);
    return reminders;
    default:
    return state;
  }
}

export default reminders;

```
- src/components/App.jsxを修正

```js

import React, { Component } from 'react';
import { connect } from 'react-redux';
import { addReminder } from '../actions';


class App extends Component {
  //constrctorで初期化
  constructor(props) {
    super(props);
    this.state = {
      text: ''
    }
  }

  addReminder() {
    // console.log('this', this);
    this.props.addReminder(this.state.text);
  }

  render(){
    return(
      <div className="App">
        <div className="title">
          Reminder Pro
        </div>
        <div className="form-inline">
          <div className="form-group">
            <input className="form-control"
              placeholder="I have to..."
              //onChangeイベントで入力したものを取得
              onChange={event => this.setState({text: event.target.value})}
              />
          </div>
          //onClickでaddReminder()関数を走らせてthis.stateのtextに値を代入
          <button type="button" className="btn btn-success" onClick={ () => this.addReminder()}>Add Reminder</button>
        </div>
      </div>
    )
  }
}



export default connect(null, {addReminder})(App);


```
