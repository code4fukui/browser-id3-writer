# ブラウザ ID3 writer

JavaScript ライブラリで、ブラウザおよび Node.js でMP3ファイルにID3 (v2.3) タグを書き込めます。タグの読み取りはできませんので、別のライブラリを使用してください。

**注意**: このライブラリは既存のID3 タグ(v2.2、v2.3、v2.4)を削除します。

## デモ
オンラインデモ: [egoroof.github.io/browser-id3-writer/](https://egoroof.github.io/browser-id3-writer/)

## 機能
- タグのフレームをカスタマイズ
- ジャケット画像の追加
- 歌詞の追加
- コメントの追加
- プライベートフレームの追加

## 使い方
### ブラウザ
1. 対象のMP3ファイルをArrayBufferで取得
2. ID3Writerインスタンスを作成し、タグを設定
3. ファイルを保存

### Node.js
1. 対象のMP3ファイルをBufferで読み込む
2. ID3Writerインスタンスを作成し、タグを設定
3. ファイルを書き出す

## ライセンス
MIT