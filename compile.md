
# 備忘録（分割コンパイル）

## 問題

cal_avという名の、以下の様な、3個の引数をとるプログラムを作ります。実行時は、以下のようにタイプします。
  ```
  cal_av x y FileName
  ```
 プログラムの機能は、数値x,yから
  ```
  算術平均　(x+y)/2
  幾何平均　sqrt(x*y)
  ```
を計算して、パラメターFileNameで指定したファイルに書き出すものです。つまり
  ```
  cal_av 6 24 result.dat
  ```
としたら、”result.dat”というファイルに
  ```
  arith. av.　15.000
  geo.   av. 　12.000
  ```
という内容が書き込まれるという、内容的には簡単なものです。ただし、プログラムを主要な関数に分割して、分割コンパイルをしてください。

## 解答
端末（ターミナル）において、分割コンパイルを以下のように行った。
  ```
  root@watcher-SVE15135CJB:~/デスクトップ# make
  cc -c -Wall -O3 main.c
  cc -c -Wall -O3 get_arg.c
  cc -c -Wall -O3 calc.c
  cc -c -Wall -O3 output.c
  cc -o cal_av main.o get_arg.o calc.o output.o -lm
  ```
再度、分割コンパイルをした際には
  ```
  root@watcher-SVE15135CJB:~/デスクトップ# make all
  make clean
  make[1]: ディレクトリ `/home/watcher/デスクトップ' に入ります
  rm -f *.o core cal_av
  make[1]: ディレクトリ `/home/watcher/デスクトップ' から出ます
  make -k
  make[1]: ディレクトリ `/home/watcher/デスクトップ' に入ります
  cc -c -Wall -O3 main.c
  cc -c -Wall -O3 get_arg.c
  cc -c -Wall -O3 calc.c
  cc -c -Wall -O3 output.c
  cc -o cal_av main.o get_arg.o calc.o output.o -lm
  make[1]: ディレクトリ `/home/watcher/デスクトップ' から出ます
  ```
というようにして行った。

そして、分割コンパイルにより構築された実行ファイル（cal_av）を
./cal_av 6 24 result.dat
として動作させると、別紙にある”result.dat”が作成された。


## Makefileの中身
  ```bash
  #マクロの定義
  CC = cc
  CFLAGS  = -c -Wall -O3
  LIB     = -lm
  OBJS    = main.o get_arg.o calc.o output.o
  PROGRAM = cal_av

  #ターゲット一覧
  $(PROGRAM): $(OBJS)
  $(CC) -o $@ $(OBJS) $(LIB)
  .c.o:
  $(CC) $(CFLAGS) $<
  clean:
  rm -f *.o core $(PROGRAM)
  all:
  make clean
  make -k
  clear:
  rm *.dat *.o *.c~ *.h~ Makefile~
  ```

## def.hの中身
  ```C
  //ヘッダーファイルの指定
  #include<stdio.h>
  #include<stdlib.h>
  #include<string.h>
  #include<math.h>

  //構造体を定義
  typedef struct Date{
  double one;
  double two;
  char *FileName;
  } Data;

  //関数のプロトタイプ宣言
  void get_arg( int, char **, Data * );
  void calc( Data * );
  void output( Data );
  ```

## main.cの中身
  ```C
  //ヘッダーファイルを指定
  #include "def.h"

  int main( int argc, char *argv[] )
  {

  //構造体を宣言
  Data dat;

  //引数を取り込み、構造体 dat に渡す
  get_arg( argc, argv, &dat );

  //構造体 dat を読み、関数 calc で計算
  calc( &dat );

  //結果が書かれている構造体を読んで、結果を書き出す
  output( dat );

  exit(0);

  }
  ```

## get_arg.c
  ```C
  //引数を取り込み、構造体 dat に渡す
  #include"def.h"

  void get_arg( int total, char *program[], Data *dat )
  {
  
  (dat->one)=atof(program[1]);
  (dat->two)=atof(program[2]);
  (dat->FileName)=program[3];

  }
  ```

## calc.c
  ```
  //構造体 dat を読み、関数 calc で計算
  #include "def.h"

  void calc( Data *dat )
  {

  double x=(dat->one),y=(dat->two);

  (dat->one)=(x+y)/2.0;
  (dat->two)=sqrt(x*y);

  }
  ```

## output.c
  ```
  //結果が書かれている構造体を読んで、結果を書き出す
  #include "def.h"

  void output( Data dat )
  {

  FILE *result;

  result=fopen( ( dat.FileName ),"w" );

  fprintf( result,"arith. av. %f \n",(dat.one) );
  fprintf( result,"geo  . av. %f",(dat.two) );

  fclose(result);

  }
  ```
## result.dat
  ```
  arith. av. 15.000000
  geo  . av. 12.000000
  ```

## 補足

「解答のところでプロンプトが#になっている（ルート権限を有している）が、下手にシステムを弄るととんでもないことになるので、通常は$のままで作業をしたほうが良い。」

「デスクトップで作業すると見えにくくなるので、なるべく避けたほうが良い（動作が遅くなったりする等々…）。」
