# React-Reminder
## プロジェクトの作成
- 好きなディレクトリを作成、そこに移動をして`create-react-app xxx(名前)`を行う

## Reduxを導入
- プロジェクト作成後、`npm install redux react-redux --save`を行う
- public/index.htmlにreact-bootstrapを貼り付ける

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/latest/css/bootstrap.min.css">
```

- src/以下を削除して新しく作成する
- src/index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';


ReactDOM.render(
  <div>Reminder Pro</div>, document.getElementById('root')
)
```
