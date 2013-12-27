# Gruntタスク：SVGからWebフォントを作成する

[![Build Status](https://travis-ci.org/sapegin/grunt-webfont.png)](https://travis-ci.org/sapegin/grunt-webfont)
[![Built with Grunt](https://cdn.gruntjs.com/builtwith.png)](http://gruntjs.com/)

Gruntを利用して、SVGやEPSからWebフォントを生成することができます。ベースには[Font Custom](http://endtwist.github.com/fontcustom/)を利用しています。

このタスクを利用することで、Webサイトで必要な全ての（フォントで表現可能な）アイコンを作成することが出来ます。様々なフォーマットに対応しておりCSS/SASS/LESS/Stylusを使うことが出来ます。またHTMLのでもページも生成されます。

## 特徴

* とても柔軟な設定ができます
* セマンティック: [Unicode private use area](http://bit.ly/ZnkwaT)を利用します
* [Cross-browser](http://www.fontspring.com/blog/further-hardening-of-the-bulletproof-syntax/): IE8+.
* BEMやBootstrap形式のCSSスタイル
* CSSプリプロセッサに対応
* DataURIでのエンコーディング
* 文字バインディング
* HTMLプレビュー
* カスタムテンプレート


## インストール

このプラグインはGrunt0.4が必要です。

### OS X

```
brew install fontforge ttfautohint
npm install grunt-webfont --save-dev
```

環境によって、brewコマンドにsudoが必要な場合があります。

### Linux

```
sudo apt-get install fontforge ttfautohint
npm install grunt-webfont --save-dev
```

※注意 もしあなたのディストリビューションで`ttfautohint`が利用出来ない場合、生成結果が正しくない場合があります*


## 設定

`Gruntfile.js`に以下を追加してください:

```javascript
grunt.loadNpmTasks('grunt-webfont');
```

`Gruntfile.js`ファイル内に、`webfont`をセクションを追加してください。詳細は以下のパラメーターを参照ください。


### パラメータ

#### src

Type: `string|array`

Webフォントに使うSVGまたはEPSファイルを指定します。String形式かArray形式をサポートします。ワイルドカードが利用可能です。

#### dest

Type: `string`

生成結果のファイルを出力するディレクトリ。

#### destCss

Type: `string` Default値: _`dest`_

生成されるCSSファイルの出力先ディレクトリ（fontディレクトリと別にしたい場合に指定してください）。
Directory for resulting CSS files (if different than font directory).

#### オプション（Options）

#### font

Type: `string` Default: `icons`
フォント名、ならびにフォントファイル名

#### hashes

Type: `boolean` Default: `true`

フォントファイル名に一意な文字列を付けるか否かを指定します。一意な名前をつけることでブラウザキャッシュを行うことが出来ます。

#### styles

Type: `string|array` Default: `'font,icon'`

CSSファイルに出力されるスタイルのリストを指定します: `font` (`font-face`の宣言), `icon` (`.icon`クラス), `extra` (Bootstrap向けの追加要素 (only for `syntax` = `'bootstrap'`).

#### types

Type: `string|array` Default: `'eot,woff,ttf'`

生成するフォントファイルの種類

#### order

Type: `string|array` Default: `'eot,woff,ttf,svg'`

`@font-face`の`src`属性に記載する順序

#### syntax

Type: `string` Default: `bem`

Iconクラスのシンタックス。
Icon classes syntax. `bem` for double class names: `icon icon_awesome` or `bootstrap` for single class names: `icon-awesome`.


#### template

Type: `string` Default: `null`

CSSテンプレートのファイルパス（サンプルに`tasks/templates`を参照）。`syntax`の代わりに利用してください。（`htmlDemoTemplate`オプションも指定する必要がおそらくあります）。
テンプレートは、同名のCSSとJSONファイルから構成されます。

Gruntfile.js記載例:

```js
options: {
  template: 'my_templates/tmpl.css'
}
```

`my_templates/tmpl.css`:

```css
@font-face {
  font-family:"<%= fontBaseName %>";
  ...
}
...
```

`my_templates/tmpl.json`:

```json
{
  "baseClass": "icon",
  "classPrefix": "icon_"
}
```

#### templateOptions

Type: `object` Default: `{}`

CSSテンプレートやシンタックスを指定するJSONファイルを拡張（または上書き）します。デフォルトのCSSテンプレートからカスタマイズしたクラス名に変更することが出来ます。

``` javascript
options: {
	templateOptions: {
		baseClass: 'glyph-icon',
		classPrefix: 'glyph_',
		mixinPrefix: 'glyph-'
	}
}
```

#### stylesheet

Type: `string` Default: `'css'`

スタイルシートのタイプを指定します。css, sass, scss, lessなどが指定可能です。もしsassかscssを利用している場合、`_`がファイル名の先頭に追加されます（その結果、ファイルを部分ファイル（端切れ）として利用出来ます）。

#### relativeFontPath

Type: `string` Default: `null`

フォントファイルのパス。Will be used instead of `destCss` *in* CSS file. Useful with CSS preprocessors.

#### htmlDemo

Type: `boolean` Default: `true`

If `true`, an HTML file will be available (by default, in `destCSS` folder) to test the render.

#### htmlDemoTemplate

Type: `string` Default: `null`

Custom demo HTML template path (see `tasks/templates/demo.html` for an example) (requires `htmlDemo` option to be true).

#### destHtml

Type: `string` Default: _`destCss` value_

Custom demo HTML demo path (requires `htmlDemo` option to be true).

#### embed

Type: `string|array` Default: `false`

If `true` embeds WOFF (*only WOFF*) file as data:uri.

IF `ttf` or `woff` or `ttf,woff` embeds TTF or/and WOFF file.

If there’re more file types in `types` option they will be included as usual `url(font.type)` CSS links.

#### ligatures

Type: `boolean` Default: `false`

If `true` the generated font files and stylesheets will be generated with opentype ligature features. The character sequences to be replaced by the ligatures are determined by the file name (without extension) of the original SVG or EPS.

For example, you have a heart icon in `love.svg` file. The HTML `<h1>I <span class="ligature-icons">love</span> you!</h1>` will be rendered as `I ♥ you!`.

#### rename

Type: `function` Default: `path.basename`

You can use this function to change how file names translates to class names (the part after `icon_` or `icon-`). By default it’s a name of a file.

For example you can group your icons into several folders and add folder name to class name:

```js
options: {
  rename: function(name) {
    // .icon_entypo-add, .icon_fontawesome-add, etc.
    return [path.basename(path.dirname(name)), path.basename(name)].join('-');
  }
}
```

#### skip

Type: `boolean` Default: `false`

If `true` task will not be ran. In example, you can skip task on Windows (becase of difficult installation):

```javascript
skip: require('os').platform() === 'win32'
```

### Config Examples

#### Simple font generation

```javascript
webfont: {
  icons: {
    src: 'icons/*.svg',
    dest: 'build/fonts'
  }
}
```

#### Custom font name, fonts and CSS in different folders

```javascript
webfont: {
  icons: {
    src: 'icons/*.svg',
    dest: 'build/fonts',
    destCss: 'build/fonts/css',
    options: {
      font: 'ponies'
    }
  }
}
```

#### Custom CSS classes

```js
webfont: {
  icons: {
    src: 'icons/*.svg',
    dest: 'build/fonts',
    syntax: 'bem',
    templateOptions: {
        baseClass: 'glyph-icon',
        classPrefix: 'glyph_',
        mixinPrefix: 'glyph-'
    }
  }
}
```

#### To use with CSS preprocessor

```javascript
webfont: {
  icons: {
    src: 'icons/*.svg',
    dest: 'build/fonts',
    destCss: 'build/styles',
    options: {
      stylesheet: 'styl',
      relativeFontPath: '/build/fonts'
    }
  }
}
```

#### Embedded font file

```javascript
webfont: {
  icons: {
    src: 'icons/*.svg',
    dest: 'build/fonts',
    options: {
      types: 'woff',
      embed: true
    }
  }
}
```

## CSS Preprocessors Caveats

You can change CSS file syntax using `stylesheet` option (see above). It change file extension (so you can specify any) with some tweaks. Replace all comments with single line comments (which will be removed after compilation).

### SASS

If `stylesheet` option is `sass` or `scss`, `_` will prefix the file (so it can be a used as a partial).

### LESS

If `stylesheet` option is `less`, regular CSS icon classes will be expanded with corresponding LESS mixins.

The LESS mixins then may be used like so:

```css
.profile-button {
  .icon-profile;
}
```

## Changelog

The changelog can be found in the `Changelog.md` file.

---

## License

The MIT License, see the included `License.md` file.
