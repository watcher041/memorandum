
# 備忘録（DNS）

## ブラウザのみ名前解決できない場合の対処法

```
　DNSを自前で設定していても、ブラウザはキャッシュしていたDNSを用いてしまうため

　chromeの場合：chrome://net-internals/#dns にアクセスして clear host cache とする。

　パソコンの方もキャッシュされているので、コマンドラインを開いてipconfig /flushdns を実行

　DNS設定も自前のDNSだけを指定しておく（外部のDNSを2つ目に設定すると、少しでも応答が遅いと
　自前のものと外部のもののDNSに応答してもらうようになるため、必然的に外部からの応用が来てしまい、
　それがキャッシュとなって残る）
```