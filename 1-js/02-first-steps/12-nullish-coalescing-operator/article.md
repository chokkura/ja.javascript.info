# NULL合体演算子(Nullish coalescing operator) '??'

[recent browser="new"]

NULL合体演算子は２つの疑問符 `??` で記述されます。

これは `null` と `undefined` を同じように扱うので、この記事では特別な用語を使用します。`null` でも `undefined` でも無いときは、"定義済み" である、と言います。

`a ?? b` の結果は:
- `a` が `null` あるいは `undefined` でなければ `a`,
- それ以外の場合は `b`.

つまり、`??` は `null/undefined` でなければ最初の引数を返し、それ以外の場合は2つ目を返します。

NULL合体演算子はまったく新しいものではありません。2つのうちから、最初の "定義済み" の値を取得するには良い構文です。

既に知っている演算子を使用して `result = a ?? b` を書き直すことができます:

```js
result = (a !== null && a !== undefined) ? a : b;
```

これで、`??` がすることがなにか明確ですね。これがどこで役立つが見ていきましょう。

`??` の一般的なユースケースは、潜在的に未定義の変数のデフォルト値を提供することです。

例えば、ここでは定義済みであれば `user` を、そうでなければ `Anonymous` を表示します:

```js run
let user;

alert(user ?? "Anonymous"); // Anonymous (user は未定義)
```

こちらは名前が割り当てられた `user` の例です:

```js run
let user = "John";

alert(user ?? "Anonymous"); // John (user は定義済み)
```

`??` のシーケンスを使用して、`null/undefined` ではないリストから最初の値を選択することも可能です。

変数 `firstName`, `lastName` or `nickName` にユーザデータがあるとしましょう。ユーザが値を入れなかった場合、未定義かもしれません。

これらの変数の1つを使用してユーザ名を表示、あるいはすべて未定義の場合には "Anonymous" と表示したいです。

そのために `??` 演算子を使用しましょう:

```js run
let firstName = null;
let lastName = null;
let nickName = "Supercoder";

// 最初の null/undefined でない値を表示します
*!*
alert(firstName ?? lastName ?? nickName ?? "Anonymous"); // Supercoder
*/!*
```

## || との比較

[前のチャプター](info:logical-operators#or-finds-the-first-truthy-value) で説明したように、OR `||` 演算子は `??` と同じ方法で利用することができます。

例えば、上のコードで `??` を `||` に置き換えることができ、同じ結果を得ることができます:

```js run
let firstName = null;
let lastName = null;
let nickName = "Supercoder";

// 最初の真値を表示
*!*
alert(firstName || lastName || nickName || "Anonymous"); // Supercoder
*/!*
```

歴史的には、OR `||` 演算子が最初にありました。JavaScript の登場以来存在しているため、開発者は長い間そのような目的で使用していました。

一方、NULL合体演算子 `??` が JavaScript に追加されたのは最近のことで、その理由は人々が `||` にあまり満足していなかったためです。

重要な違いは次の通りです:
- `||` は最初の *真* の値を返します。
- `??` は最初の *定義済み* の値を返します。

言い換えると、`||` は `false`, `0`, 空文字 `""` と `null/undefined` を区別しません。それらはすべて同じ偽値です。これらのいずれかが `||` の引数の最初にある場合は、2つ目の引数が結果として取得されます。

ただし、実際には、変数が `null/undefined` の場合にのみデフォルト値を使用したい場合があります。つまり、値が本当に未知/設定されていない場合です。

例として次を考えましょう:

```js run
let height = 0;

alert(height || 100); // 100
alert(height ?? 100); // 0
```

- `height || 100` は `height` が偽値になるかをチェックし、それは `0` であり偽です。
    - なので、`||` の結果は2つ目の引数である `100` です。
- `height ?? 100` は `height` が `null/undefined` かをチェックしますが、そうではありません。
    - なので、結果は `height` の "まま"、つまり `0` です。

実践では、高さゼロはしばしば有効な値であり、デフォルト値で置き換えられるべきではありません。こういった場合、`??` が正しい動作をします。

## 優先順位

`??` の優先順位は、おおよそ `||` と同じですが、少しだけ低いです。[MDN テーブル](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#Table) で `5` です(`||` は `6`)。

つまり、`||` と同様に NULL合体演算子 `??` は `=` と `?` の前に評価されますが、`+` や `*` などの他のほとんどの演算子の後に評価されます。

したがって、他の演算子を含む式で `??` で値を選択したい場合は、括弧を追加することを検討してください:

```js run
let height = null;
let width = null;

// 重要: 括弧を使用します
let area = (height ?? 100) * (width ?? 50);

alert(area); // 5000
```

そうでない場合、括弧を省略すると `*` は `??` よりも優先度が高いため、最初に実行され、正しくない結果になるでしょう。

```js
// 括弧なし
let area = height ?? 100 * width ?? 50;

// ...これと同じように動作します (恐らくしたいことではないはずです):
let area = height ?? (100 * width) ?? 50;
```

###  && や || と一緒に ?? を使用する

安全上の理由により、JavaScript は優先順位が括弧で明示的に指定されていない限り、`&&` や `||` 演算子と一緒に `??` を用いることを禁止しています。

次のコードは構文エラーになります:

```js run
let x = 1 && 2 ?? 3; // Syntax error
```

この制限には当然議論の余地がありますが、人々が `||` から `??` に切り替え始めるときに、プログラミングのミスを避ける目的で言語仕様に追加されました。

回避するには明示的に括弧を使用します:

```js run
*!*
let x = (1 && 2) ?? 3; // 動作します
*/!*

alert(x); // 2
```

## サマリ

- Null合体演算子 `??` は一覧から "定義済み" の値を選択するための簡単な方法を提供します。

    変数にデフォルト値を代入するために使用されます:

    ```js
    // height が null あるいは undefined であれば height=100 を設定します
    height = height ?? 100;
    ```

- 演算子 `??` は優先度が低く、`?` や `=` よりも少し高い程度です。そのため、式の中で使用する際には括弧を追加することを検討してください。
- 明示的な括弧なしに  `||` や `&&` と一緒に利用することは禁止されています。
