# Form/Base.js

## FCBase

(作成中...)  
`HTMLFormElement`を管理するクラスです。詳細な機能は、子クラスの　[`FCForm`](fcform.md)や[`FCConfirm`](confirm.md)で実装しています。

## Table of contents

* [Overview](#Overview)
* [Static properties](#Static_properties)
	+ [`regexTypeCR` (get)](#regexTypeCR)
* [Constructor](#Constructor)
* [Properties](#Properties)
 	+ [`form` (get)](#form)
	+ [`parentNode` (get)](#parentNode)
	+ [`enabledSubmit` (get/set)](#enabledSubmit)
	+ [`callbackFnView` (set)](#callbackFnView)
	+ [`callbackFnHide` (set)](#callbackFnHide)
	+ [`promiseTimeout` (get/set)](#promiseTimeout)
	+ [`promiseTimeoutMiriSec` (get)](#promiseTimeoutMiriSec)
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

## Static properties<span id="Properties"></span>

## regexTypeCR (get)<span id="regexTypeCR"></span>

要削除検討

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

## promiseTimeoutMiriSec (get)<span id="promiseTimeoutMiriSec"></span>

要削除検討

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

#### hide<span id="hide"></span>

[`callbackFnHide`](#callbackHide)で設定したコールバック関数を実行します。

### Syntax

``` JavaScript
fc.hide();
```

### Return value

`undefined`

---

#### getFormData<span id="getFormData"></span>

`HTMLFormElement`に属するFormコントロールの値を`FormData`オブジェクトで取得します。

### Syntax

``` JavaScript
fc.getFormData();
```

### Return value

`FormData`オブジェクト

---

#### addItem<span id="addItem"></span>

`HTMLFormElement`に内包するFormコントロールを登録します。 

### Syntax

``` JavaScript
f.caddItem(element);
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
| `<input type="(email,number,password,range,search,tel,text,url)">` | keydown, input |

`FCBase`オブジェクトを生成する時点で本メソッドと同様の処理を実行するため通常は使用しません。  
`FCBase`オブジェクトを生成した後に追加したFormコントロールがあるときに使用します。  
対象要素のクラス属性に"exclude"が含まれる場合は登録しません。

---

#### getItem<span id="getItem"></span>

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

#### keys<span id="keys"></span>

登録したFormコントロールの`name`属性値で構成した`Iterator`オブジェクトを返します。

### Syntax

``` JavaScript
fn.keys();
```

### Return value

`Iterator`オブジェクト

---

#### values<span id="values"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### entries<span id="entries"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### has<span id="has"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### forEach<span id="forEach"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### getValues<span id="getValues"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### isEmpty<span id="isEmpty"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### clearValue<span id="clearValue"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### clearValues<span id="clearValues"></span>


### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### setValue<span id="setValue"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### setValues<span id="setValues"></span>


### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### getCollectedFormData<span id="getCollectedFormData"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### querySelector<span id="querySelector"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### querySelectorAll<span id="querySelectorAll"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### addEventList<span id="addEventList"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### removeEventList<span id="removeEventList"></span>

### Syntax

``` JavaScript
```

### Parameters

### Return value

---

#### addEventFormItem<span id="addEventFormItem"></span>


### Syntax

``` JavaScript
```

### Parameters

### Return value