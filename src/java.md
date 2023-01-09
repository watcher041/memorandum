
# 備忘録（Java）

```
・メソッド参照

・stream API を用いると、可読性を上げることができる。
　
　（ stream API を使用しない場合）
]　List<Integer> integerList = Arrays.asList(1, 2, 3, 4, 5);
　for (Integer i : integerList) {
   　 if (i % 2 == 0) {
      　  System.out.println(i);
   　 }
　}

　（ stream API を使用した場合）
　List<Integer> integerList = Arrays.asList(1, 2, 3, 4, 5);
　integerList.stream() // streamの取得
    　    .filter(i -> i % 2 == 0) // 中間操作
       　 .forEach(i -> System.out.println(i)); // 終端操作

・定数の定義には final を用いる
　final int CONST_VAR = ・・・

・小数計算を正確に行うには、Bigdecimal 型で取り扱う

・文字列の比較は == ではなく equals を用いる

・int a[][] = { {0,1},{2},{3,4,5,6}};・・ジャグ配列（歪な配列）

・拡張for文
for(int j : num_array) {
      System.out.println(j);
}

・FILENAME = "test.txt";
・File fi = new File("FILENAME); プログラム上でファイルを取り扱えるようにする。
・FileWriter fw = new FileWriter(FILENAME); ファイルを書き込み専用で読み込む
・FileReader fr = new FileReader(FILENAME);  ファイルを読み込み専用で読み込む
→ これらで読み書きする際、文字コードで読み書きされるため不便
→ Writer・Bufferクラスを用いて複数文字を変数に格納できるようにする
（読み書き同様、文字の入出力も同様になっている）
・FileReader(FILENAME) ⇔ InputStreamReader(System.in)
（BufferReaderは共通して使うことができる）

・package test; （testフォルダ内にあるクラスであることを明示するためのもの）
　そのクラスを使う際にはimportを用いる。

・アノテーション：メソッドなどの前につく@ 〜 の文で、