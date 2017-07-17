# これは何？
コンポーネントファイル(HTML, CSS, JS)をフラットに配置するフロントエンド開発環境の構成案です。  
なるべく既存の技術を活用して、シンプルに問題解決できるよう留意しています。

# 問題意識
意識した問題点は、主に下記となります。
- 共通
  - プロダクションコードから作業対象のソースファイルを見つけにくい。
- HTML
  - コンポーネントのDOMを直接ページに記述した結果、各所コードがバラバラになった。
- CSS
  - CSS設計を導入しても解釈(宗教論)が微妙にズレている。その矯正に伴うロス。
  - コンポーネントの分類変更によるリファクタと全体的なデグレチェック。
- JS
  - SCSSの命名規則にハイフンが使われているために、JS Class名が合わせにくい。

# 前提条件
- 画面の構成要素は全てコンポーネントとして扱います。(要デザイナ相談)
- 全てのコンポーネントファイル(HTML, CSS, JS)を、フラットに管理します。
- Component毎にHTML, CSS, JSファイルを一元管理します。
- テンプレートエンジン, CSSプリプロセッサ、JSトランスパイラを使います。
- CSS, JSに関しては`cssnano`や`uglify`等の最適化ツールを導入します。

# ディレクトリ構成
あるコンポーネント`SomeComponent`が`example`というページに表示される構成例です。  
`SomeComponent`以外のコンポーネントもフラットに配置します。
```bash
# 説明上必要な構成のみ抜粋します。
project/
  src/
    view/
      components/
        SomeComponent.twig
        ExtendedComponent.twig
        ...
      pages/
        example.twig
    scss/
      components/
        SomeComponent.scss
        ExtendedComponent.scss
        ...
      pages/
        example.scss
    js/
      components/ # JSはComponentの必要に応じて作成してください。
        SomeComponent.js
        ExtendedComponent.js
        ...
      pages/
        example.js
```
コンポーネントファイル(HTML, CSS, JS)をフラットに配置するのが重要です。  

これにより、コンポーネントの重複を物理的に排除することができます。  
さらに、ファイル名をCSSセレクタ名に適用することで、衝突回避を確実に保証できます。  

# ファイル名サンプル
```bash
project/src/view/components/SomeComponent.twig
project/src/scss/components/SomeComponent.scss
project/src/js/components/SomeComponent.js
```
HTML, CSS, JSのファイル名をコンポーネント名で統一します。  
CSSセレクタ名とJS Class名も統一するため、必然的にJS Class名(PascalCase)に揃えます。

# HTMLサンプル
Twigの例となります。  
他のテンプレートエンジンを導入する場合は、適宜読み替えてください。
```twig
{# SomeComponent.twig #}
<div class="SomeComponent {{ modifier }}">
  <span class="SomeComponent_childElement">{{ text }}</span>
<div>

{# 実装例: example.twig
{% include 'SomeComponent.twig' %}
#}
```

# SCSSサンプル
`@mixin`記法でComponentを定義します。  
Componentの上位階層から引数でコンテキストを渡すことにより、実装側から分岐制御させることができます。
```scss
// SomeComponent.scss
@mixin SomeComponent($context: null) {  // mixin名は`PascalCase`
  // ...
  &_childElement { /*...*/ }            // 子要素は`&_camelCase`
  &.modifier { /*...*/ }                // Modifierは`&.camelCase`

  @if $context == 'foo' { /*...*/ }     // 実装側から引数で分岐制御させる
  @elseif $context == 'bar' { /*...*/ }
  @else { /*...*/ }
}

/* 実装例: example.scss
@import 'components/SomeComponent.scss';
.SomeComponent { @include SomeComponent($context: true); }
*/

/* 拡張例: ExtendedComponent.scss
@mixin ExtendedComponent() {
  &_childElement {           // 拡張側の子要素とする
    @include SomeComponent;
    // ...                   // 拡張スタイルの追記
  }
}
*/
```

# JSサンプル
基本的に、ES2015のClass定義で問題ありません。  

コンポーネント名とClass名が同一になっていればOKですので、各プロジェクトのルールや、  
フレームワークの設計に沿って自由に開発してください。
```js
// SomeComponent.js
class SomeComponent {            // Class名は`PascelCase`
  constructor() { /* ... */ }
  _privateMethod() { /* ... */ }
  publicMethod() { /* ... */ }
  // ...
}
export default SomeComponent
```

# プロジェクト例
- TODO: 準備中

# 参考URL
- [DoCSSa](https://github.com/mlarcher/docssa)
- [SUIT CSS](https://github.com/suitcss/suit)

# ライセンス
MIT
