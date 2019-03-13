# kintone-plugin-development

## ダウンロード
https://github.com/tkc49/kintone-plugin-development/releases/latest

## create-kintone-plugin のインストール
```
npm install --save-dev @kintone/create-plugin
```
※ 上記コマンドは1回のみでOK。

## 雛形作成
```
npx @kintone/create-plugin your-project-name
```
※ npxはローカルのnode_moduleを実行してくれるコマンド

対話型でいろいろ聞いてくるので回答する。<br>
以下のディレクトリーが作成されます。
```
your-project-name
├── node_modules/
├── scripts/
├── src/
├── .eslintrc.js
├── .npmignore
├── package-lock.json
├── package.json
└── private.ppk
```

もしタイポした場合など修正したい場合は、`./your-project-name/src/manifest.json`を修正してください。


## webpackでの開発環境の準備
```
cd your-project-name
```

## plugin.zip の出力先を作成
```
mkdir dist
``` 

## webpackの準備
```
npm install --save-dev webpack webpack-cli @kintone/webpack-plugin-kintone-plugin
```

### babelのインストール
```
npm install --save-dev @babel/cli @babel/core @babel/preset-env babel-loader
```
```
npm install --save @babel/polyfill
```


### webpack.config.js を準備する

```
const path = require("path");
const KintonePlugin = require("@kintone/webpack-plugin-kintone-plugin");

module.exports = {
	mode: 'development',
    entry: {
        desktop: './src/js/desktop.js',
        config: './src/js/config.js'
    },
    output: {
        path: path.resolve(__dirname, 'src', 'js', 'dist'),
        filename: '[name].js'
    },
    plugins: [
        new KintonePlugin({
            manifestJSONPath: './src/manifest.json',
            privateKeyPath: './private.ppk',
            pluginZipPath: './dist/plugin.zip'
        })
    ],
    module: {
        rules: [
            {
                // test: /\.js$/,
                // 公式ドキュメントにあわせる
                // https://github.com/babel/babel-loader
                test: /\.m?js$/,
                // exclude: /node_modules/,
                // 公式ドキュメントを参照
                // https://github.com/babel/babel-loader
                exclude: /(node_modules|bower_components)/,
                use: [
                    {
                        loader: "babel-loader",
                        options: {
                            // babel7を利用するので。
                            // presets: ['env']
                            presets: ["@babel/preset-env"]
                        }
                    }
                ]
            }
        ]
    }
};
```

### webpackでビルドされたソースファイルの出力先を作成
```
mkdir ./src/js/dist
```

## manifest.jsonを修正する

`js/desktop.js` -> `js/dist/desktop.js`<br>
`js/config.js` -> `js/dist/config.js`

```
{
  "manifest_version": 1,
  "version": 1,
  "type": "APP",
  "desktop": {
    "js": [
      "https://js.cybozu.com/jquery/3.3.1/jquery.min.js",
      "js/dist/desktop.js"
    ],
    "css": [
      "css/51-modern-default.css",
      "css/desktop.css"
    ]
  },
  "icon": "image/icon.png",
  "config": {
    "html": "html/config.html",
    "js": [
      "https://js.cybozu.com/jquery/3.3.1/jquery.min.js",
      "js/dist/config.js"
    ],
    "css": [
      "css/51-modern-default.css",
      "css/config.css"
    ],
    "required_params": [
      "message"
    ]
  },
  "name": {
    "en": "age-calculation",
    "ja": "年齢計算プラグイン"
  },
  "description": {
    "en": "日付から年齢の計算をします。",
    "ja": "任意の日付から年齢の計算をします。"
  },
  "mobile": {
    "js": [
      "https://js.cybozu.com/jquery/3.3.1/jquery.min.js",
      "js/mobile.js"
    ]
  }
}
```

## package.json の修正
package.json の scripts を修正する。

```
{
  "name": "age-calculation",
  "version": "0.1.0",
  "scripts": {
    "upload": "kintone-plugin-uploader dist/plugin.zip --watch --waiting-dialog-ms 3000",
    "watch": "webpack --mode development --watch",
    "dev": "webpack --mode development",
    "build": "webpack --mode production",
    "lint": "eslint src"
  },
  "devDependencies": {
    "@kintone/plugin-packer": "^1.0.1",
    "@kintone/plugin-uploader": "^2.2.0",
    "@kintone/webpack-plugin-kintone-plugin": "^1.0.10",
    "eslint": "^4.19.1",
    "eslint-config-kintone": "^1.3.0",
    "npm-run-all": "^4.1.3",
    "webpack": "^4.29.6",
    "webpack-cli": "^3.2.3"
  }
}
```

## ビルド実行
```
npm run watch
```
or
```
npm run dev
```
or
```
npm run build
```

## アップロード
```
npm run upload
```

# 参考
[webpackを利用するkintoneプラグイン開発の流れ](https://qiita.com/yamaryu0508/items/fa68fb83dabd04fae3cc)