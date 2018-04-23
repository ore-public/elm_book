#Elmで小さなアプリケーションを作ってみる。


次はElmで、ちょっとボタンがあったり動きがあったりする小さめのアプリケーションを作ってみましょう。

```

```

何かしら動作があるアプリケーションを作るには状態が必要になってきます。状態とは、アプリケーション内の時間とともに変化する部分をいいます。例えばボタンをクリックすると、画面のカウンターが加算されていくアプリなら、カウンターの数字を保持する部分があるわけです。

基本的に状態はバグと隣り合わせです。バグは状態が知らぬ間に変わっていたり、想定してない変化をするときにおこります。

Elmではアプリケーションの状態の更新はupdate関数の中に集中するようになっています。