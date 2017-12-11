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

## 状態のマッピング App.jsxの修正
```js
render(){
  console.log('this.props',this.props);
  return(
    <div className="App">
      <div className="title">
        Reminder Pro
      </div>
      <div className="form-inline">
        <div className="form-group">
          <input className="form-control"
            placeholder="I have to..."
            onChange={event => this.setState({text: event.target.value})}
            />
        </div>
        <button type="button" className="btn btn-success" onClick={ () => this.addReminder()}>Add Reminder</button>
      </div>
    </div>
  )
}
}

//追加
function mapStateToProps(state) {
  return{
    reminders: state
  }
}
```

## リマインダーリストの表示
- App.jsを追記・変更
```js

//上省略

//新しい関数を作成
  renderReminders() {
    const { reminders } = this.props;
    return (
      <ul className="list-group col-sm-4">
        {
          reminders.map(reminder => {
            return(
              <li key={reminder.id} className="list-group-item">
                <div>{reminder.text}</div>
              </li>
            )
          })
        }
      </ul>
    )
  }

  render(){
    return(
      <div className="App">
        <div className="title">
          Reminder Pro
        </div>
        <div className="form-inline reminder-form">
          <div className="form-group">
            <input className="form-control"
              placeholder="I have to..."
              onChange={event => this.setState({text: event.target.value})}
              />
          </div>
          <button type="button" className="btn btn-success" onClick={ () => this.addReminder()}>Add Reminder</button>
        </div>
        //ここに埋め込むことで、clickイベントが作動したときに新しくrenderRemindersが走って新しいエレメントが作られる
          { this.renderReminders() }
      </div>
    )
  }
}


function mapStateToProps(state) {
  return{
    reminders: state
  }
}


export default connect(mapStateToProps, {addReminder})(App);


```
- src/index.cssを作成してスタリング
```css
.App{
  padding-top: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.title{
  text-align: center;
  font-size: 26px;
}

input{
  margin: 5px;
}

.reminder-form{
  padding: 5px;
}

```

## リマインダー削除の追加
- constance.jsに追記
```js
export const ADD_REMINDER = 'ADD_REMINDER';
//追記
export const DELETE_REMINDER = 'DELETE_REMINDER';
```

- App.jsの関数を編集
```js
renderReminders() {
  const { reminders } = this.props;
  return (
    <ul className="list-group col-sm-4">
      {
        reminders.map(reminder => {
          return(
            <li key={reminder.id} className="list-group-item">
              <div className="list-item">{reminder.text}</div>
              //ここに追加
              <div className="list-item delete-button">
                &#x2715;
              </div>
            </li>
          )
        })
      }
    </ul>
  )
}


```
- actions/index.jsを編集
```js
import { ADD_REMINDER, DELETE_REMINDER } from '../constance';

export const addReminder = (text) => {
  const action = {
    type: ADD_REMINDER,
    text
  }
  console.log('action in addReminder', action);
  return action;
}

export const deleteReminder = (id) => {
    const action = {
      type: DELETE_REMINDER,
      id
    }
    console.log('deleting in actions', action);
    return action;
}

```
- App.jsにインポート
```js
import { addReminder, deleteReminder } from '../actions';

  //新しく関数を設定
  deleteReminder(id) {
    console.log('deleting in application', id);
    console.log('this.props',this.props);
  }

  renderReminders() {
    const { reminders } = this.props;
    return (
      <ul className="list-group col-sm-4">
        {
          reminders.map(reminder => {
            return(
              <li key={reminder.id} className="list-group-item">
                <div className="list-item">{reminder.text}</div>
                //削除ようのパーツを設定
                <div className="list-item delete-button"
                  onClick={() => this.deleteReminder(reminder.id)}
                >
                  &#x2715;
                </div>
              </li>
            )
          })
        }
      </ul>
    )
  }


//deleteReminderを設定
export default connect(mapStateToProps, {addReminder, deleteReminder })(App);

//この段階でブラウザで挙動を確認すると、Xボタンが追加されており、クリックするとコンソールにdeleting in applicationが表示されている(削除されるidが表示されている)
```

- index.cssを編集
```html
//削除ようのパーツを作ったのでスタイリングしていく



.list-item{
  display: inline-block;
}

.delete-button{
  float: right;
  padding-left: 5px;
}

```

## リストコンポーネントの削除
- reducers/index.jsを修正
```js

import { ADD_REMINDER , DELETE_REMINDER } from '../constance';

const reminder = (action) => {
  return {
    text: action.text,
    id: Math.random()
  }
}

const removeById = (state = [], id) => {
  const reminders = state.filter(reminder => reminder.id !== id);
  console.log('new reduced reminders', reminders);
  return reminders;
}

const reminders = (state = [], action) => {
  let reminders = null;
  switch(action.type){
    case ADD_REMINDER:
    reminders = [...state, reminder(action)];
    console.log('reinders as tate', reminders);
    return reminders;
    case DELETE_REMINDER:
      reminders = removeById(state, action.id);
      return reminders;
    default:
    return state;
  }
}



export default reminders;


```

- App.jsを編集

```js

//deleteReminderの関数にthis.props.deleteReminder(id)を渡す
deleteReminder(id) {
  console.log('deleting in application', id);
  console.log('this.props',this.props);
  this.props.deleteReminder(id);
}

//これが昨日すると削除する機能が実装できている
```

##　日付入力フィールドの作成
- App.jsに新しくインプットタグを追加
```js

//constrctorも追記
constructor(props) {
  super(props);
  this.state = {
    text: '',
    //追記
    dueDate: ''
  }
}


<input
  className="form-control"
  type="datetime-local"
  onChange={event => this.setState({dueDate: event.target.value})}
  />

```

- actions/index.jsを編集
```js
//dueDateを追加
export const addReminder = (text, dueDate) => {
  const action = {
    type: ADD_REMINDER,
    text,
    //dueDateを追加
    dueDate
  }
  console.log('action in addReminder', action);
  return action;
}

```

- reduces/index.js
```js

//変更
const reminder = (action) => {
  let { text, dueDate } = action;
  return {
    text,
    dueDate,
    id: Math.random()
  }
}

```

- App.jsで取得できているか見る
```js
addReminder() {
  // console.log('this', this);
  console.log('this.state.dueDate',this.state.dueDate);
  this.props.addReminder(this.state.text,this.state.dueDate);
}

//これで取得できていると日付がdueDateに入ってくる
```

## Moment.jsを導入
- `npm install moment --save`を行う
- App.jsに記述
```js
import moment from 'moment'; //これを記述

reminders.map(reminder => {
  return(
    <li key={reminder.id} className="list-group-item">
      <div className="list-item">
        <div>{reminder.text}</div>
        //ここに追記
        <div><em>{moment(new Date(reminder.dueDate)).fromNow()}</em></div>
      </div>
      <div className="list-item delete-button"
        onClick={() => this.deleteReminder(reminder.id)}
      >
        &#x2715;
      </div>
    </li>
  )
```
