# Form/Base.js

## FCBase

(作成中...)  
`HTMLFormElement`を管理するクラスです。詳細な機能は、子クラスの　[`FCForm`](fcform.md)や[`FCConfirm`](confirm.md)で実装しています。

### Table of contents

* [Overview](#Overview)
* [Static properties](#Static_properties)
	+ [`regexTypeCR` (get)](#regexTypeCR)
* [Constructor](#Constructor)
* [Instance properties](#Instance_properties)
 	+ [`form` (get)](#form)
	+  [`parentNode` (get)](#parentNode)
	+  [`enabledsubmit` (get/set)](#enabledsubmit)
	+   [`callbackFnView` (set)](#callbackFnView)
	+  [`callbackFnHide` (set)](#callbackFnHide)
	+   [`promiseTimeout` (get/set)](#promiseTimeout)
	+  [promiseTimeoutMiriSec (get)](#promiseTimeoutMiriSec)
* [Instance methods](#Instance_methods)
	+  [view](#view)
	+  [hide](#hide)
	+  [getFormData](#getFormData)
	+  [addItem](#addItem)
	+  [getItem](#getItem)
	+  [keys](#keys)
	+  [values](#values)
	+  [entries](#entries)
	+  [has](#has)
	+  [forEach](#forEach)
	+ [getValues](#getValues)
	+  [isEmpty](#isEmpty)
	+  [clearValue](#clearValue)
	+  [clearValues](#clearValues)
	+  [getCollectedFormData](#getCollectedFormData)
	+  [querySelector](#querySelector)
	+  [querySelectorAll](#querySelectorAll)
	+  [addEventList](#addEventList)
	+  [removeEventList](#removeEventList)
	+  [addEventFormItem](#addEventFormItem)

### Overview<span id="Overview"></span>
  
FCBaseはインスタンス生成時受け取ったHTMLFormElementとその親要素、内包するFormコントロールに対して次の様な管理を行います。

* フォームの表示、非表示。表示/非表示の前後でコールバック関数を実行することができます。([`view`](#view), [`hide`](#hide), [`addEventList`](#addEventList))
* Formコントロールの値へのアクセスを容易にします。()
* 各Formコントロールに共通するイベントハンドラ(`blur`, `click`, `input`, `keydown`)を容易に追加できます。
  
### Static properties<span id="Static_properties"></span>
 
 #### regexTypeCR (get)<span id="regexTypeCR"></span>
 
 ### Constructor<span id="Constructor"></span>
 
 ### Instance properties<span id="Instance_properties"></span>
 
 #### form (get)<span id="form"></span>
 
 #### parentNode (get)<span id="parentNode"></span>
 
#### enabledsubmit (get/set)<span id="enabledsubmit"></span>

####  callbackFnView (set)<span id="callbackFnView"></span>

#### callbackFnHide (set)<span id="callbackFnHide"></span>

####  promiseTimeout (get/set)<span id="promiseTimeout"></span>

#### promiseTimeoutMiriSec (get)<span id="promiseTimeoutMiriSec"></span>

### Instance methods<span id="Instance_methods"></span>

#### view<span id="view"></span>

#### hide<span id="hide"></span>

#### getFormData<span id="getFormData"></span>

#### addItem<span id="addItem"></span>

#### getItem<span id="getItem"></span>

#### keys<span id="keys"></span>

#### values<span id="values"></span>

#### entries<span id="entries"></span>

#### has<span id="has"></span>

#### forEach<span id="forEach"></span>

#### getValues<span id="getValues"></span>

#### isEmpty<span id="isEmpty"></span>

#### clearValue<span id="clearValue"></span>

#### clearValues<span id="clearValues"></span>

#### getCollectedFormData<span id="getCollectedFormData"></span>

#### querySelector<span id="querySelector"></span>

#### querySelectorAll<span id="querySelectorAll"></span>

#### addEventList<span id="addEventList"></span>

#### removeEventList<span id="removeEventList"></span>

#### addEventFormItem<span id="addEventFormItem"></span>
