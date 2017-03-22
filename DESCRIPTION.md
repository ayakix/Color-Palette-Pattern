## 概要
iOSにおいて、定義したカラーパターンをInterface Builderからでもソースコードからでも利用する方法です。ソースコードからの利用には、[R.swift](https://github.com/mac-cain13/R.swift)を利用します。また、Interface Builderからの利用には、[ColorTools](https://github.com/ramonpoca/ColorTools)にあるHtml2Clrを利用します。

## 用語説明
### R.swiftとは？
AndroidにおけるRクラスのように、ViewやStoryboard, Stringsなどのリソースをソースコードから利用できるようにしたものです。Identifier等を文字列で使用する必要もなく、タイポによるバグを防ぎます。

### Html2Clrとは？
定義したカラーパターンをclrファイルに変換します。clrファイルはMacで読み込むことのできるカラーパターン定義用のバイナリファイルです。

## 導入手順
### 1. R.swiftのインストール
[R.swift](https://github.com/mac-cain13/R.swift)をインストールします。

### 2. Html2Clrのインストール
[ColorTools](https://github.com/ramonpoca/ColorTools)よりHtml2Clrをビルドし、プロジェクトのルートディレクトリ等に実行ファイルを置きます。もしくは、[Color-Palette-Pattern@github](https://github.com/ayakix/Color-Palette-Pattern)より導入してください。

### 3. カラーパターンの定義
1行につき、`16進数RGB 名前`の形でカラーパターンを定義し、Xcodeプロジェクトにインポートします。

![color](https://github.com/ayakix/Color-Palette-Pattern/raw/master/images/color.png)

### 4. R.swift用Run Scriptの定義
ターゲットの[Build Phases]-[左上の+]-[New Run Script Phase]より、ビルド時にR.swiftを起動するようにします。

![color](https://github.com/ayakix/Color-Palette-Pattern/raw/master/images/run_rswift.png)

下記はCocoaPodsを利用してR.swiftをインストールした場合のパスになります。パスは実行環境に合わせて変更してください。また、このRun Scriptは必ず`Compile Sources`よりも上に定義する（先に実行する）必要があります。

```sh
"$PODS_ROOT/R.swift/rswift" "$SRCROOT/$PROJECT_NAME"
```

### 5. clr出力用Run Scriptの定義
4で定義したRun Scriptよりも一つ上に（先に）カラーパターン変化用のRun Scriptを定義します。3で用意したカラーパターンファイルからclrファイルを出力し、`~/Library/Colors`にコピーします。Html2Clr等のパスは環境に合わせて書き換えてください。

![color](https://github.com/ayakix/Color-Palette-Pattern/raw/master/images/run_color.png)

```sh
FILE_NAME="mytheme"
# Generate color palette file
"./Html2Clr" "$SRCROOT/$PROJECT_NAME/$FILE_NAME.txt"
# Copy to User directory color palette
"cp" "$SRCROOT/$PROJECT_NAME/$FILE_NAME.clr" "$HOME/Library/Colors/"
```

### 6. ビルド
プロジェクトを一度ビルドし、`clrファイル`と`R.generated.swift`を出力します。

### 7. ファイルのインポート
出力されたclrファイルとR.generated.swiftをプロジェクトにインポートします。

![color](https://github.com/ayakix/Color-Palette-Pattern/raw/master/images/import_files.png)

### 8. ビルド
再度ビルドします。これにより、clrファイルを読み取り、R.generated.swiftにcolorの定義が自動生成されます。

### 9. カラーパターンの編集→ビルド→Xcode再起動
カラーパターンを編集した際には、ビルドをし、その都度Xcodeを再起動する必要があります。これは、Xcodeの起動時に、`~/Library/Colors`ディレクトリの中にあるclrファイルを読み込むためです。

## 使い方
### ソースコードから使う

`R.color.{clrファイル名}`の中で定義されています。

![color](https://github.com/ayakix/Color-Palette-Pattern/raw/master/images/use_from_code.png)

### Interface Builderから使う
Colorsより作成したテーマを選ぶと利用できます。

![color](https://github.com/ayakix/Color-Palette-Pattern/raw/master/images/use_from_xib.png)

## デメリット
ソースコードとInterface Builderから共通したカラーパターンを利用できるようになりましたが、結局のところInterface Builderでは変数のように利用できないため、カラーパターンを変更した場合には、再度Interface Builderから色を設定する必要があります。動的にアプリのカラーテーマを変えたい場合には、やはりソースコードから修正する必要があります。

## サンプル
[Color-Palette-Pattern@github](https://github.com/ayakix/Color-Palette-Pattern)に動作するプロジェクトがあります。
