# Form/Base.js

## FCBase

(作成中...)  
`HTMLFormElement`を管理するクラスです。詳細な機能は、子クラスの　[`FCForm`](fcform.md)や[`FCConfirm`](confirm.md)で実装しています。

## Table of contents

* [Overview](#Overview)
* [Constructor](#Constructor)
* [Properties](#Properties)
 	+ [`form` (get)](#form)
	+ [`parentNode` (get)](#parentNode)
	+ [`enabledSubmit` (get/set)](#enabledSubmit)
	+ [`callbackFnView` (set)](#callbackFnView)
	+ [`callbackFnHide` (set)](#callbackFnHide)
	+ [`promiseTimeout` (get/set)](#promiseTimeout)
* [Methods](#Methods)
	+ [view](#view)
	+ [hide](#hide)
	+ [getFormData](#getFormData)
	+ [addItem](#addItem)
	+ [getItem](#getItem)
	+ [keys](#keys)
	+ [values](#values)
	+ [entries](#entries)
	+ [has](#has)
	+ [forEach](#forEach)
	+ [getValues](#getValues)
	+ [isEmpty](#isEmpty)
	+ [clearValue](#clearValue)
	+ [clearValues](#clearValues)
	+ [setValue](#setValue)
	+ [setValues](#setValue)
	+ [getCollectedFormData](#getCollectedFormData)
	+ [querySelector](#querySelector)
	+ [querySelectorAll](#querySelectorAll)
	+ [addEventList](#addEventList)
	+ [removeEventList](#removeEventList)
	+ [addEventFormItem](#addEventFormItem)

---

## Overview<span id="Overview"></span>
  
FCBaseはインスタンス生成時受け取ったHTMLFormElementとその親要素、内包するFormコントロールに対して次の様な管理を行います。

* フォームの表示、非表示。表示/非表示の前後でコールバック関数を実行することができます。([`view`](#view), [`hide`](#hide), [`addEventList`](#addEventList))
* Formコントロールの値へのアクセスを容易にします。([`getValues`](#getValues), [`setValue`](#setValue), [`setValues`](#setValues))
* Formコントロールを要素とするイテレータを利用できます。([`keys`](#keys), [`values`](#values), [`entries`](#entries), [`forEach`](#forEach))
* 各Formコントロールに共通するイベントハンドラ(`blur`, `click`, `input`, `keydown`)を容易に追加・削除できます。([`addEventList`](#addEventList), [`removeEventList`](#removeEventList))
* 本クラスを通して登録したsubmitイベントは全て`Promise`を利用して管理しています。
* submitイベント処理の期限時間を設定し、時間内に処理できない場合の処理ができるようになっています。([`promiseTimeout`](#promiseTimeout))

---

## Constructor<span id="Constructor"></span>

コンストラクタでFCBaseオブジェクトを生成します。

### Syntax

```JavaScript
new FCBase(parentNode)
```

### Parameters

`parentNode`

* 管理したいHTMLFormElementの親要素。

---

## Properties<span id="Properties"></span>

## form (読み取り専用)<span id="form"></span>

HTMLFormElementを取得します。

---

## parentNode (読み取り専用)<span id="parentNode"></span>

コンストラクタで渡したHTMLFormElementの親要素を取得します。

---

## enabledSubmit (get/set)<span id="enabledSubmit"></span>

submitイベント後にサーバーへ値の送信するかを設定、または状態の取得をします。  
値はBool値で、`True`は送信し、`False`は送信しません。  
初期値は`False`です。

---

##  callbackFnView (設定専用)<span id="callbackFnView"></span>

[`view`](#view)メソッドを呼び出された時の実行内容を設定します。  
初期値は`fc.parentNode.style.display = '';`です。

### Syntax

``` JavaScript
fc.callbackFnView = (parentNode) => { // 引数としてFormの親要素が渡されます。
	// 表示処理を記述。
};
```

---

## callbackFnHide (設定専用)<span id="callbackFnHide"></span>

[`hide`](#hide)メソッドを呼び出された時の実行内容を設定します。  
初期値は`fc.parentNode.style.display = 'noe';`です。

### Syntax

``` JavaScript
fc.callbackFnHide = (parentNode) => { // 引数としてFormの親要素が渡されます。
	// 非表示処理を記述
};
```

---

## promiseTimeout (get/set)<span id="promiseTimeout"></span>

`addEventList`より登録した`submit`イベントの処理期限を設定、取得します。  
値は秒数です。初期値は３０秒です。

---

## Methods<span id="Methods"></span>

## view<span id="view"></span>

[`callbackFnView`](#callbackFnView)で設定したコールバック関数を実行します。

### Syntax

``` JavaScript
fc.view();
```

### Return value

`undefined`

---

## hide<span id="hide"></span>

[`callbackFnHide`](#callbackHide)で設定したコールバック関数を実行します。

### Syntax

``` JavaScript
fc.hide();
```

### Return value

`undefined`

---

## getFormData<span id="getFormData"></span>

`HTMLFormElement`に属するFormコントロールの値を`FormData`オブジェクトで取得します。 `new FormData(form)`と等価です。

### Syntax

``` JavaScript
fc.getFormData();
```

### Return value

`FormData`オブジェクト

---

## addItem<span id="addItem"></span>

`HTMLFormElement`に内包するFormコントロールを登録します。 

### Syntax

``` JavaScript
fc.caddItem(element);
```

### Parameters

`element`

* Formコントロール。 `<input type="radio">`等、同一のname属性を持つ複数のFormコントロールがあっても一つだけ登録すれば良いです。

### Return value

Bool値。 `True`は登録成功、`False`は対象要素のクラス属性に"exclude"が含まれていて登録しなかった。

### Description

Formコントロールの登録と同時に[`addFormEvent`](#addFormEvent)で登録してある各種ハンドラーをFormコントロールに"適宜"登録します。要素の種類と登録されるイベントの種類は次の通りです。

| 要素の種類 | 登録されるイベントの種類 |
| --- | --- |
| 登録可能な要素共通 | blur |
| `<input type="(checkbox,radio)">`, <br>  `<select>` | click |
| `<input type="(email,number,password,range,search,tel,text,url)">`, <br> `<textarea>` | keydown, input |

`FCBase`オブジェクトを生成する時点で本メソッドと同様の処理を実行するため通常は使用しません。  
`FCBase`オブジェクトを生成した後に追加したFormコントロールがあるときに使用します。  
対象要素のクラス属性に"exclude"が含まれる場合は登録しません。

---

## getItem<span id="getItem"></span>

登録したFormコントロールの`NodeList`を返します。

### Syntax

``` JavaScript
fc.getItem(name);
```

### Parameters

`name`

* 登録したFormコントロールの`name`属性の値。

### Return value

`NodeList`オブジェクト

### Description

登録にないnameを引数に渡すと`FCNotExistsExeption`を`throw`します。

---

## keys<span id="keys"></span>

登録したFormコントロールの`name`属性値で構成した`Iterator`オブジェクトを返します。

### Syntax

``` JavaScript
fc.keys();
```

### Return value

`Iterator`オブジェクト

---

## values<span id="values"></span>

登録したFormコントロールのnodeListで構成した`Iterator`オブジェクト、または`getValue`メソッドの結果の集合で構成した`Iterator`オブジェクトを返します。

### Syntax

``` JavaScript
fc.values(getValue);
```

### Parameters

`getValue`

* Bool値。初期値は`False`。`True`は`getValue`メソッドの結果で構成した`Iterator`オブジェクト、`False`はnodeListで構成した`Iterator`オブジェクトを指定。

### Return value

`Iterator`オブジェクト。

---

## entries<span id="entries"></span>

name属性とnodeListまたは`getValue`メソッドの結果のペアで構成した`Iterator`オブジェクトを返します。

### Syntax

``` JavaScript
fc.entries(getValue);
```

### Parameters

`getValue`

* Bool値。初期値は`False`。`True`name属性と`getValue`メソッドの結果のペア、`False`はname属性とnodeListのペアを指定。

### Return value

`Iterator`オブジェクト。

---

## has<span id="has"></span>

Formコントロールの集合に指定

### Syntax

``` JavaScript
fc.has(name)
```

### Parameters

`name`

* name属性

### Return value

Bool値を返す。`True`は要素が存在し、`False`は存在しない。

---

## forEach<span id="forEach"></span>

### Syntax

``` JavaScript
fc.forEach(fbFn[, getValue]);
```

### Parameters

`cbFn`

* ループ時に実行するコールバック関数。コールバック関数にはname属性とnodeList、またはname属性とg`getValue`メソッドの結果が渡される。

`getValue`

* Bool値。初期値は`False`。`True`name属性と`getValue`メソッドの結果のペア、`False`はname属性とnodeListのペアを指定。

### Return value

`undefined`

###

``` Javascript
fc.forEach((name, nodeList) => {
	nodeList.forEach(node => { /* 処理 */});
}); // 第２引数をFalseか何も渡さないとコールバック関数への引数はname, nodeList
fc.forEach((naem, values) => {
	/* ループ処理 */
}, true); // 第２引数にTrueを渡すとコールバック関数への引数はname, value
```

---

## getValues<span id="getValues"></span>

指定したnameのFormコントロールの入力値、または選択された値を返します。存在しないnameを指定した場合は`FCNotExistsExeption`が`Throw`されます。

### Syntax

``` JavaScript
fc.getValues(name);
```

### Parameters

`name`

* 登録したFormコントロールのname属性

### Return value

`Array`オブジェクト。指定したnameの要素数に関わらず`Array`オブジェクトに値を格納して返します。

---

## isEmpty<span id="isEmpty"></span>

指定したnameのFormコントロールに値が入力されていない、選択されていないかの確認結果を返します。存在しないnameを指定した場合は`FCNotExistsExeption`が`Throw`されます。

### Syntax

``` JavaScript
fc.isEmpty(name);
```

### Parameters

`name`

* 登録したFormコントロールのname属性

### Return value

Bool値。`True`は未入力または未選択。`False`は入力済みまたは選択済み。  
（注意）値または選択の有無の確認だけであり、有効検査ではありません。

---

## clearValue<span id="clearValue"></span>

指定したnameのFormコントロールの入力値を削除、または選択状態を解除します。存在しないnameを指定した場合は`FCNotExistsExeption`が`Throw`されます。

### Syntax

``` JavaScript
fc.clearValue(name);
```

### Parameters

`name`

* 登録したFormコントロールのname属性

### Return value

`undefined`

---

## clearValues<span id="clearValues"></span>

登録しているすべてのFormコントロールの入力値を削除、または選択状態を解除します。  
（注意）`input type="hidden"`の値も削除します。ブラウザ等画面の目視では気づけないので考慮が必要です。

### Syntax

``` JavaScript
fc.clearValues();
```

### Return value

`undefiend`

---

## setValue<span id="setValue"></span>

指定したnameのFormコントロールのvlaue属性に値を代入またはcheckedにします。

### Syntax

``` JavaScript
fc.setValue(naem, value);
fc.setvalue(name, values);
```

### Parameters

`name`

* 登録したFormコントロールのname属性

`value`

* 代入したい値、またはcheckedにしたいFormコントロールのvalue属性値。

`values`

* checkedにしたいFormコントロールのvalue属性値で構成した`Array`オブジェクト。  
  テキストボックスや短項目選択式のFormコントロールでは配列の最後の要素が反映されます。

### Return value

`undefined`

---

## setValues<span id="setValues"></span>

登録したすべてのFormコントロールに対してvalue属性に値の代入またはcheckedにします。

### Syntax

``` JavaScript
fc.setValues(formData);
fc.setValues(map);
fc.setValues(obj);
```

### Parameters

`formData`

* `FormData`オブジェクト

`map`

* `Map`オブジェクト。

`obj`

* `Object`。

### Return value

`undefined`

### Description

引数の`Map`,`Object`の値は文字列または数値のプリミティブ値、またはその要素で構成された`Array`オブジェクトに対応しています。  
登録したFormコントロールに対しての情報が引数に入っていない場合、空値で更新します。

### Examples

``` JavaScript
console.dir([...fc.entries(true)]); // [ ["a", ["1"]], ["b", ["2"]], ["c", ["3"]] ] // 前提として'c'はcheckboxとします。
fc.setValues({a: 4, c : [5, 6]});
console.dir([...fc.entries(true)]); // [ ["a", ["4"]], ["b", [""]], ["c", ["5", "6"]] ]
```

---

## getCollectedFormData<span id="getCollectedFormData"></span>

登録した全てのFormコントロールの値で構成する`FormData`オブジェクトを返します。 FCBaseオブジェクトを生成したのちにFormコントロールを追加したが`addItem`で追加していない、Formコントロールのclass属性に"exclude"を持ちFCBaseオブジェクトに登録されていない場合は`FormData`に反映されません。

### Syntax

``` JavaScript
fm.getCollectedFormData();
```

### Return value

`FormData`オブジェクト。

---

## querySelector<span id="querySelector"></span>

HTMLFormElementの親要素の`querySelector`を実行します。

### Syntax

``` JavaScript
fc.querySelector(selectors);
```

### Parameters

`selectors`

* CSSセレクター文字列

### Return value

`selectors`に一致するする最初のElementオブジェクト。一致しない場合は`null`を返します。

---

## querySelectorAll<span id="querySelectorAll"></span>

HTMLFormElementの親要素の`querySelectorAll`を実行します。

### Syntax

``` JavaScript
fc.querySelectorAll(selectors);
```

### Parameters

`selectors`

* CSSセレクター文字列

### Return value

`selectors`に一致する要素を含む静的な`NodeList`。一致しない場合は空の`NodeList`。

---

## addEventList<span id="addEventList"></span>

FCBaseクラスで管理するイベントハンドラー内で実行する関数を登録します。

### Syntax

``` JavaScript
fc.addEventList(type, func);
```

### Parameters

`type`

イベントの種類を表す文字列。

`func`

指定したイベントが発生するときに実行する関数。

### Return value

`undefined`

### Description

登録可能なイベントの種類と関係する要素は次の通りです。

| イベントの種類 | 関係する要素、オブジェクト | 説明 |
| ---- | ---- | ---- |
| blur | Formコントロール |  |
| click | `<input type="(checkbox,radio)">`, <br> `<select>` |  |
| input | `<input type="(email,number,password,range,search,tel,text,url)">`, <br>, `<textarea>` |  |
| keydown | `<input type="(email,number,password,range,search,tel,text,url)">`, <br>, `<textarea>` |  |
| error | `window`オブジェクト |  |
| submit | `fc.form` |  |
| afterSubmit | `fc.form` | 上記submitイベント実行後に呼び出されます |
| beforeView | `fc.parentNode` | `fc.parentNode`が表示される直前に呼び出されます |
| afterView | `fc.parentNode` | `fc.parentNode`が表示された直後に呼び出されます |
| beforeHide | `fc.parentNode` | `fc.parentNode`が非表示になる直前に呼び出されます |
| afterHide | `fc.parentNode` | `fc.parentNode`が非表示になった直後に呼び出されます |

* `func`は`Event`オブジェクトと、`FCBase`オブジェクトを受け取れる関数にしてください。  
```JavaScript
fc.addEvent('blur', (event, fcObj) => { /* 何か処理 */ });
```
* `FCBase`は各種イベント毎に実行する関数を保持する配列を持ち、各種配列内の関数を全て実行するハンドラを関係する要素全てに登録します。
* あるFormコントロールのみにイベントを登録したい場合は`addEventListener`を使用してください。
* 全てのFCBaseオブジェクトで登録した`error`イベントは、一つの`window`オブジェクトの`error`イベント内で管理しています。`submit`イベント実行時のpromise timeout を除き、`error`イベントが発火したときは全てのFCBaseオブジェクトで登録した関数の実行を試みます。尚、各FCBaseオブジェクトに同一の関数を登録した場合はその内の一つのみを実行します。  
``` JavaScript
const func = () => { /* 何かの処理 */ };
fc1.addEvent('erorr', func);　// error イベントで実行されるのはこの一つのみ。
fc2.addEvent('erorr', func);　// promise timeout が発生したときは、発生した各FCBaseで登録した関数のみが実行されます。
fc3.addEvent('erorr', func);
```
``` JavaScript
const genFunc = () => { return () => { /* 何かの処理 */ }; };
fc1.addEvent('error', genFunc()); // 処理内容は一緒でもそれぞれ別の関数オブジェクトが登録されているため、
fc2.addEvent('error', genFunc()); // error イベントでは３つ全てを実行します。
fc3.addEvent('error', genFunc());
```

---

## removeEventList<span id="removeEventList"></span>

`addEventList`で登録した関数を削除します。

### Syntax

``` JavaScript
fc.removeEventList(type, func);
```

### Parameters

`type`

イベントの種類を表す文字列。

`func`

`addEventList`で登録した関数。

### Return value

`undefined`

### Description

`FCBase`オブジェクトで保持する配列中の関数の削除を試みます。 一致しない場合は特に何もしません。

---

## addEventFormItem<span id="addEventFormItem"></span>


### Syntax

``` JavaScript
```

### Parameters

### Return value