# Form

Webフォームのクライアントサイドの制御を簡単に行うためのJavaScriptパッケージです。

* 複数の`form`要素を各フォーム画面と見立てたシングルページアプリケーションを実現します。
* いくつかのJavaScriptコーディングと、HTMLは`input`要素等の属性や、少しだけ用意されたルールに沿った`class`属性を追加するだけで、クライアントサイドの有効性検査と、検査結果を表示、"次へ"、"戻る"ボタンの基本的な機能を付与、確認画面の値の表示を行います。
* エラーメッセージは専用のテンプレートを調整して、項目に応じたメッセージを表示可能です。
* かっこいいスタイルシートは各自で用意してください。

## Instration

Form.jsと関連するFormフォルダをWebサーバに配置して、Form.jsを利用するJavaScriptから`import`してください。

## Example

``` html:example.html
<!-- example.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>example</title>
<script src="js/example.js" type="module"></script>
<style>
.hide { display:none; }
.view { display:inherit; }
</style>
</head>
<body class="hide"><!-- スタイルを一切気にしないでコーディングしています。 -->

<div class="form">
<form method="post" action="#">
<h2>フォーム</h2>
<div>
    <div><label for="i_username">ユーザー名</label></div>
    <div><input type="text" name="username" id="i_username" minlength="5" maxlength="15" pattern="^\w+$" required></div>
    <div class="warning username"></div><!-- ここに有効性検査結果が表示されます。 -->
</div>
<div>
    <div class="vmfor-hobby">趣味</div><!-- vmfor-[name] でエラーメッセージに名称を追加できます。 -->
    <ul>
        <li><label><input type="checkbox" name="hobby" value="1" class="required">買い物</label></li>
        <!-- class="required"で同じname属性のcheckboxを全て必須項目として扱います。 -->
        <li><label><input type="checkbox" name="hobby" value="2">映画</label></li>
        <li>
            <label><input type="checkbox" name="hobby" value="3">その他</label> 
            <label>その他の内容<input type="text" name="hobby-etc" maxlength="15">
        </li>
    </ul>
    <div class="warning"><span class="hobby"></span><span class="hobby-etc"></span></div>
    <!-- class="warning"に内包された要素のclass属性にnameをつけても各要素にエラーメッセージが表示されます。 -->
</div>
<ul>
    <li><button type="button" class="back">戻る</button></li><!-- 修正画面のときに表示します。 -->
    <li><button type="submit">次の画面へ</button></li>
</ul>
</form>
</div><!-- /.page.form -->

<div class="confirm">
<form method="post" action="#">
<h2>確認画面</h2>
<div>
    <div>ユーザー名</div>
    <div class="fcc-username"></div><!-- fcc-[name]で取得した値を挿入します。 -->
</div>
<div>
    <div>趣味</div>
    <ul>
        <li class="fccl-hobby v-1">買い物</li><!-- fccl-[name] v-[value]で取得した値と合致する要素を表示します。 -->
        <li class="fccl-hobby v-2">映画</li>
        <li class="fccl-hobby v-3">その他 (<span class="fcc-hobby-etc"></span>)</li>
    </ul>
</div>
<ul>
    <li><button type="button" class="modify form">修正する</button></li><!-- class="modify [画面名]" で各画面に戻ります。 -->
    <li><button type="submit">登録する</button></li>
</ul>
</form>
</div><!-- /.page.confirm -->

<div class="complete">
<h2>登録完了</h2>
<p>登録完了しました</p>
</div><!-- /.page.completed -->

<div class="error">
<h2>エラー</h2>
<p class="reason"></p> <!-- 「セッションタイムアウト」等の簡単なメッセージを表示します。 -->
</div><!-- /.page.completed -->

</body>
</html>
```
``` javascript:expample.js
/* js/example.js
 * js フォルダ内に必要なパッケージがある前提です。
 * 非対応のブラウザの処理は別途記述が必要です。 */
import { Form, FCType, VMType } from './Form.js';

const form = new Form('form1'); // startメソッド以外はチェーンメソッド可能です。
form.controllerSettings({ //　内包するコントローラクラスの設定
    expires : 30 // フォームの有効期限（秒）
})
.formSettings({ // FCType.Form の設定
    delayTime : 1000, // inputイベント発火の遅延時間設定（ミリ秒）
    marginMaxLength : 3, // maxlength以上に文字を入力できるようにするマージンの設定。電話番号の誤入力等に有効です。今のところ共通設定
    validityMessage : new VMType.Jp() // 日本語のテンプレートを使用（今はこれか、各ブラウザのメッセージを利用するクラスしかない）
})
.confirmSettings({ // FCType.COnfirm の設定
    onSubmit : async (event, fc) => {
        const ctr = form.controller;
        ctr.getAllData().forEach((value, key) => console.log(`${key} : ${value}`));
        ctr.next(fc); //次のページを表示します。
        ctr.clear(); // hiddenの値も消します
        /* // ctr.getAllData() はFormDataクラスを返すので、そのままxhrでsendできます。
            const xhr = new XMLHttpRequest();
            xhr.open("POST", '/apply');
            xhr.send(ctr.getAllData());
        */
    }
})
/* append('画面名', 画面のタイプ, formの親要素, オプション)。 原則追加した順に画面遷移します。 */
.append('form', FCType.Form, 'div.form') // フォーム
.append('confirm', FCType.Confirm, 'div.confirm') // 確認画面
.append('applied', FCType.NoForm, 'div.complete') // 登録完了画面
.append('error', FCType.Error, 'div.error', {noStat : true}) // エラー画面

.addInitTask(ctr => { // 各画面の初期処理の定義
    const fc = ctr.get('form'); //画面管理のクラスオブジェクトを取得
    fc.addCondRequire('hobby-etc', 'hobby', 3, 'その他の内容を入力してください。'); // 条件付き必須の定義
})
.start() // 機能開始。append -> addInitTaskの順にwindow.onloadイベントとして実行します。
.then(() => { document.body.classList.remove('hide'); }) // start()の戻り値はPromiseインスタンスなので、then catch が利用できます。
```