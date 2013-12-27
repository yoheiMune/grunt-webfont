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

生成結果のファイルを出力するディレクトリを指定してください。

#### destCss

Type: `string` Default: _`dest` value_

Directory for resulting CSS files (if different than font directory).

#### Options

#### font

Type: `string` Default: `icons`
Name of font and base name of font files.

#### hashes

Type: `boolean` Default: `true`

Append font file names with unique string to flush browser cache when you update your icons.

#### styles

Type: `string|array` Default: `'font,icon'`

List of styles to be added to CSS files: `font` (`font-face` declaration), `icon` (base `.icon` class), `extra` (extra stuff for Bootstrap (only for `syntax` = `'bootstrap'`).

#### types

Type: `string|array` Default: `'eot,woff,ttf'`

Font files types to generate.

#### order

Type: `string|array` Default: `'eot,woff,ttf,svg'`

Order of `@font-face`’s `src` values in CSS file.

#### syntax

Type: `string` Default: `bem`

Icon classes syntax. `bem` for double class names: `icon icon_awesome` or `bootstrap` for single class names: `icon-awesome`.


#### template

Type: `string` Default: `null`

Custom CSS template path (see `tasks/templates` for some examples). Should be used instead of `syntax`. (You probably need to define `htmlDemoTemplate` option too.)

Template is a pair of CSS and JSON files with the same name.

For example, your Gruntfile:

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

Extends/overrides CSS template or syntax’s JSON file. Allows custom class names in default css templates.

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

Stylesheet type. Can be css, sass, scss, less... If `sass` or `scss` is used, `_` will prefix the file (so it can be a used as a partial).

#### relativeFontPath

Type: `string` Default: `null`

Custom font path. Will be used instead of `destCss` *in* CSS file. Useful with CSS preprocessors.

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
