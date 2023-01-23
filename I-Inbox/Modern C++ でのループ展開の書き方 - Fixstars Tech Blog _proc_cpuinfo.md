---
url: https://proc-cpuinfo.fixstars.com/2022/12/loop-unroll-in-modern-cpp/
title: Modern C++ でのループ展開の書き方 - Fixstars Tech Blog /proc/cpuinfo
date: 2023-01-23 09:21:09
tag: 
summary: この記事は C++ Advent Calendar 2022 の 9 日目の記事です。
---
## 手法の解説

### 古典的なループ展開の書き方

ループ展開とは、1 回の反復の中で 1 要素ずつではなく N 要素ずつ処理することで高速化する、古くからよく知られた一般的な技法です。

ここではループ展開の効果や高速化される理由・原理については触れません（詳しく知りたい方には[計算科学技術特論 A](https://www.slideshare.net/RCCSRENKEI/20210408-katagiri) の[片桐先生の講義資料 p.65 あたりから](https://www.slideshare.net/RCCSRENKEI/20210408-katagiri/65)がオススメです）。簡単な例を挙げると

```
template<std::size_t... unrollIndices>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n,
	std::index_sequence<unrollIndices...>) {
	for(std::size_t i = 0; i < n; i += sizeof...(unrollIndices))
	{
		const auto x_ = std::array{(x[i + unrollIndices])...};
		const auto y_ = std::array{(y[i + unrollIndices])...};
		const auto z_ = std::array{(x_[unrollIndices] + y_[unrollIndices])...};
		((z[i + unrollIndices] = z_[unrollIndices]), ...);
	}
}
template<std::size_t UnrollN>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	vectoradd(z, x, y, n, std::make_index_sequence<UnrollN>());
}
```

のような n 次元ベクトル x と y を足して z に格納するような処理`vectoradd`があった時、例えば 2 要素ずつ処理する「2 段ループ展開」した関数`vectoradd_unroll2`は以下のようになります。

```
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	for(std::size_t i = 0; i < n; ++i)
	{
		const auto x_i = x[i];
		const auto y_i = y[i];
		const auto z_i = x_i + y_i;
		z[i] = z_i;
	}
}
```

さて、このような高速化コードを書く時、困るのは以下の 2 点です。

*   何段が適しているのかを**理論的に求めることは難しい**です。多すぎるとレジスタ数や命令キャッシュなどの問題で遅くなるので、適切な段数 N が存在しているのですが、それはループの中身の処理やコンパイラや実行ハードウェアによって決まるため、実際には試しに実行してみないと分からないことがほとんどです。
*   試しに実行しようとした時、または環境によって動的に切り替えようとした時、上記のようなコードを書くと N=1,2,3,4,… と全ての N に対して別々の関数 (_unroll3, _unroll4,…) を**コピペで大量に用意**しなければいけませんが、それはとても大変です。

特に 2 番目については、手動コピペは番号の打ち間違いなど小さなミスで気づきにくいバグを埋め込む原因となります。そのため、この解決方法として、従来は

*   別のスクリプト (bash,python) などで数字だけを変えたコピペコードを機械的に生成する
*   プリプロセッサ・マクロを使ってなんとか共通化して自動生成する

などの方法が取られていました（コンパイラに任せる方法などは記事末尾の付録を参照）。しかし前者はソースコードを他で生成するため使用するツール・言語が増えてしまっており、また後者は原理的に段数 N に上限値が存在するという欠点がありました。また、どちらにしても、コードが実際に書かれている場所（スクリプト中の雛形やマクロ関数の定義）と、実際の関数の定義場所（ファイルやマクロの呼び出し元）が異なってしまい、保守性（特にデバッグのしやすさ）が悪くなるという弱点がありました。

このような弱点を克服した書き方が、今回紹介する方法です。

### 使用する C++ 機能の紹介

今回の方法は、最近の C++、特に C++17 の機能をしっかり使っています（業務での実用性を考慮して C++20 以降についてはここでは使いません）。そのため、C++03/11 までしかキャッチアップできてない方や、ベター C としてしか C++ を使ってない方にはいきなりは理解が難しいかもしれません。そこで、まずどんな機能を使っているのかを軽く解説したいと思います。

なお、ここでは初心者向けの平易な解説のために、厳密さは求めず説明を省略している部分もあります。厳密や詳細な説明が必要な方は、規格書を読むか [cpprefjp](https://cpprefjp.github.io/) などのしっかりとしたリファレンスサイトを参照してください。

#### 非型テンプレート引数

まず大前提として、昔から C++ にあった**テンプレート引数に型以外を与えることができる**機能を用います。

よく見るテンプレートは、以下のように型を可変にするものだと思います。

```
void vectoradd_unroll2(double z[], const double x[], const double y[], const std::size_t n) {
	for(std::size_t i = 0; i < n; i += 2)
	{
		const auto x_i0 = x[i + 0];
		const auto x_i1 = x[i + 1];
		const auto y_i0 = y[i + 0];
		const auto y_i1 = y[i + 1];
		const auto z_i0 = x_i0 + y_i0;
		const auto z_i1 = x_i1 + y_i1;
		z[i + 0] = z_i0;
		z[i + 1] = z_i1;
	}
}
```

このテンプレート引数 T の部分ですが、整数も入れることができます。

```
template<typename T>
T twice(const T x) {
	return 2*x;
}
```

重要な性質として、このようにテンプレート引数に渡された値は**コンパイル時定数**であることが保証されます。普通の引数で渡した時には、基本的に実行時引数となりコンパイラはその入力された数字がいくつであるかを知ることができないことと対照的です。

この機能は、今回はループ展開の段数をコンパイル時定数として指定するため等に使います。

#### 可変長テンプレート引数

次に、[variadic template](https://cpprefjp.github.io/lang/cpp11/variadic_templates.html) を紹介します。

この機能は、**テンプレート引数で任意の個数を受け取れる**ものです。これ自体は C++11 の機能ではありますが、汎用ライブラリを作ろうとしないと自分で書く機会があまりないため、特にアプリ開発者には馴染みが薄いかと思います。

例えば以下のように、前後処理を入れて別の関数を呼び出したい時に使ったりします。

```
template<typename T, T multiplier>
T times(const T x) {
	return multiplier*x;
}
```

可変長テンプレート引数が実応用の中で使われているのをよく見かける場所としては、[完全転送](https://proc-cpuinfo.fixstars.com/2016/03/c-html/)などがありますね。

#### 可変長引数に対する展開・演算

渡された可変長テンプレート引数は、**… を使って展開**したり演算したりすることができます。

以下にいくつかの例を示します。

```
template<typename... Args>
void ExecuteWithLog(Args... args) {
	log << "Start";
	func(args...);
	log << "End";
}
// ExecuteWithLog(0, "hi");と呼ぶと、ログに開始終了が記録されてfunc(0, "hi");が呼ばれる
```

ここで、例えば、`func<2, 0, 1>({0.1, 0.2, 0.3});`と呼ばれたとすると、A,B,C,D はそれぞれ以下のようになります

*   A は、一番単純で`可変長引数...`という形を取っています。こうすると、単純に引数の中身がそのまま**展開**されます。つまり、例では`indices...`は`2,0,1`に展開されます。つまり、i は`{2, 0, 1}`というコンパイル時配列になっています
*   B は、`(可変長引数を含む式)...`という形です。このようにすると、引数の値それぞれに式が適用されてから展開（つまり**拡張**）されます。なので、`(src[indices]*i[indices])...`は`src[2]*i[2],src[0]*i[0],src[1]*i[1]`となり、g はそれらを要素に持つ配列となります。
*   C は、`(可変長引数を含む式 演算子 ...)`という形です。これは [C++17 の機能](https://cpprefjp.github.io/lang/cpp17/folding_expressions.html)で**畳み込み式**と呼ばれます。効果は、値それぞれに式が適用されてから、演算子で連結して展開されます。畳み込み式にはいくつかの種類がありますが、今回は + 演算子で単項右畳み込みを使っています。そのため、`sum=g[2]+g[0]+g[1]`となり、総和を計算していることになります。
*   D は少し毛色が違い、可変長引数の個数を取得しています。そのために、新しい`sizeof...`演算子を使っています。使う時は… の位置に注意してください。

このように使うことで、ループ展開するときに使う配列の要素番号（インデックス）等をコンパイル時に渡せるようになります。

#### コンパイル時連続列

先述の可変長テンプレート引数を使うと要素番号を外部から指定できることがわかりました。しかし、「N 段」を展開したい時に、0,1,2,…,N-1 という番号を手動で指定していては N 毎のコピペコードから抜け出せません。

それを解決するのが [std::make_index_sequence](https://cpprefjp.github.io/reference/utility/make_index_sequence.html) です。

使い方は以下のような感じになります。

```
template<std::size_t... indices>
void func(double src[]) {
	constexpr std::size_t i[] = {indices...}; // A
	double g[] = {(src[indices]*i[indices])...}; // B
	double sum = ((g[indices]) + ...); // C
	constexpr std::size_t N = sizeof...(indices); // D
}
```

func の実行時引数に入っている`std::index_sequence`は型推論に使うだけで実際には使わない引数です（そのため仮引数名がありません）。これで、N 個の連続した数列をコンパイル時に生成することができました。

#### ラムダ式（の即時実行）

C++11 から導入された [lambda expression](https://cpprefjp.github.io/lang/cpp11/lambda_expressions.html) ですが、これは関数オブジェクトをその場で直接定義できるようにしたものでした。

```
template<std::size_t... indices>
void func(std::index_sequence<indices...>) {
	// indicesに0,1,2,...の数列が入っている
}
template<std::size_t N>
void func() {
	func(std::make_index_sequence<N>()); // Nを指定して0,1,2,...,N-1を生成する
}
```

このラムダ式ですが、1 回しか呼ばれないのであれば、わざわざ関数名をつけなくても、定義したその場で実行することができます。

```
const auto getMin = [](const double x[], const std::size_t n)
{
	auto ret = x[0];
	for(std::size_t i = 1; i < n; ++i)
	{
		ret = std::min(x[i], ret);
	}
	return ret
};
const auto m = getMin(x, 10); // 10次元配列xの最小値
```

このようにした時、見かけ上は関数の実行が入るように見えますが、コンパイラからは、即時実行されていて他から呼ばれていない関数であることが自明です。そのため、最近の C++ が使えるようなまともな品質のコンパイラ（gcc, clang, MSCV など）であれば、実際にはインライン展開され関数呼び出しはなくなります。

このラムダ式の即時実行を今回は、複雑な処理をループ展開する場合に、複雑な処理を 1 つの式とみなすように変形する時に使います。

#### クラスの引数型推論

今回の技法に必須ではないのですが、記法を簡潔にするために使っているのが C++17 の機能である [CTAD](https://cpprefjp.github.io/lang/cpp17/type_deduction_for_class_templates.html) です。

これは、簡単に言えば「コンストラクタの引数から、クラスのテンプレート引数を推論してくれる」機能です。例えば以下のようになります

```
const auto m = [](const double x[], const std::size_t n)
{
	auto ret = x[0];
	for(std::size_t i = 1; i < n; ++i)
	{
		ret = std::min(x[i], ret);
	}
	return ret
}(x, 10); // 10次元配列xの最小値
```

## ループ展開の作り方

上記で使う機能・道具は揃いました。これを使って、N 段のループ展開の書き方を、いくつかの例題を見ながら段階的にに紹介します。

なお、説明簡略化のため、以下では配列長（反復回数）n は、ループ展開数 N で割り切れて余りがないという前提を置きます。実用上は端数処理を入れてください。

### 最初の一歩：twice

まずは、最も簡単な例として、単に配列を 2 倍にするだけの以下のような処理を考えてみます。

```
auto a = std::array<double, 4>{0.1, 0.2, 0.3, 0.4}; // 従来では、double, 4を指定しなければいけなかった
auto a = std::array{0.1, 0.2, 0.3, 0.4}; // 引数を見て自動でdouble, 4だと指定してくれる
```

これに今回の手法を適用する手順としては以下のようになります。

1.  `std::make_index_sequence`で N 個の連続数列を作成
2.  N 個の連続数列を可変長テンプレート引数で受け取る
3.  反復回数を n 回ではなく n/N 回にする
4.  畳み込み式を使って N 個の式に展開

実際にコードにしてみると[以下のようになります](https://wandbox.org/permlink/BsXLF0mxgDj1lXRo)（コメントの番号が上記の手順番号に対応しています）

```
void twice(double x[], const std::size_t n) {
	for(std::size_t i = 0; i < n; ++i)
	{
		x[i] *= 2;
	}
}
```

最内の 4 は、式`(x[i + unrollIndices] *= 2)`をカンマ演算子, で連結しています。なので、結局、最内は

```
template<std::size_t... unrollIndices>
void twice(double x[], const std::size_t n,
	std::index_sequence<unrollIndices...>) // 2 {
	for(std::size_t i = 0; i < n; i += sizeof...(unrollIndices)) // 3
	{
		((x[i + unrollIndices] *= 2), ...); // 4
	}
}
template<std::size_t UnrollN>
void twice(double x[], const std::size_t n) {
	twice(x, n, std::make_index_sequence<UnrollN>()); // 1
}
```

と展開されたことになります。カンマ演算子 a,b は a を実行した後にその結果を捨てて b を評価する演算子ですから、これはそれぞれの式を順番に上から実行することになります。実際、これを [x64 向けに Clang で N=2 としてコンパイルしてみる](https://godbolt.org/z/55WMrdvjP)と

```
x[i + 0] *= 2,
x[i + 1] *= 2,
x[i + 2] *= 2,
（以下略）
```

```
twice_naive(double*, unsigned long):                     # @twice_naive(double*, unsigned long)
        test    rsi, rsi
        je      .LBB0_3
        xor     eax, eax
.LBB0_2:                                # =>This Inner Loop Header: Depth=1
        movsd   xmm0, qword ptr [rdi + 8*rax]   # xmm0 = mem[0],zero
        addsd   xmm0, xmm0
        movsd   qword ptr [rdi + 8*rax], xmm0
        inc     rax
        cmp     rsi, rax
        jne     .LBB0_2
.LBB0_3:
        ret
```

となっていて、内側の命令`movsd`（メモリ読み書き）と`addsd`（足し算＝2 倍処理）が 2 つずつに増えており、ループカウンタ`rax`が`inc`（1 増やす）から`add 2`（2 増やす）になったことがわかります。

### 少し複雑な処理：vectoradd

twice では内側の式が 1 つしかありませんでしたが、複数の式から構成されている場合を考えましょう。

例として、先にも出した以下のような vectoradd を考えます。

```
void twice<2ul>(double*, unsigned long):                     # @void twice<2ul>(double*, unsigned long)
	test    rsi, rsi
	je      .LBB1_3
	xor     eax, eax
.LBB1_2:                                # =>This Inner Loop Header: Depth=1
	movsd   xmm0, qword ptr [rdi + 8*rax]   # xmm0 = mem[0],zero
	movsd   xmm1, qword ptr [rdi + 8*rax + 8] # xmm1 = mem[0],zero
	addsd   xmm0, xmm0
	movsd   qword ptr [rdi + 8*rax], xmm0
	addsd   xmm1, xmm1
	movsd   qword ptr [rdi + 8*rax + 8], xmm1
	add     rax, 2
	cmp     rax, rsi
	jb      .LBB1_2
.LBB1_3:
	ret
```

これをループ展開すると[以下のようになります](https://wandbox.org/permlink/A0eufsHmQU6zaBzh)。

```
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	for(std::size_t i = 0; i < n; ++i)
	{
		const auto x_i = x[i];
		const auto y_i = y[i];
		const auto z_i = x_i + y_i;
		z[i] = z_i;
	}
}
```

元々の処理のループの内側が複数の式で構成されているため、これをまとめるためにラムダ式（の即時実行）にしました。実行時引数はないため省略されています。実際に、先と同様に [N=2 に対して x64 Clang でコンパイルする](https://godbolt.org/z/Th9ecETzK)と、以下のようになりました。

```
template<std::size_t... unrollIndices>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n,
	std::index_sequence<unrollIndices...>) {
	for(std::size_t i = 0; i < n; i += sizeof...(unrollIndices))
	{
		([x, y, z, n, ii = i] {
			const auto i = ii + unrollIndices;
			const auto x_i = x[i];
			const auto y_i = y[i];
			const auto z_i = x_i + y_i;
			z[i] = z_i;
		}(), ...);
	}
}
template<std::size_t UnrollN>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	vectoradd(z, x, y, n, std::make_index_sequence<UnrollN>());
}
```

```
vectoradd_naive(double*, double const*, double const*, unsigned long):           # @vectoradd_naive(double*, double const*, double const*, unsigned long)
        test    rcx, rcx
        je      .LBB0_3
        xor     eax, eax
.LBB0_2:                                # =>This Inner Loop Header: Depth=1
        movsd   xmm0, qword ptr [rsi + 8*rax]   # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax]
        movsd   qword ptr [rdi + 8*rax], xmm0
        inc     rax
        cmp     rcx, rax
        jne     .LBB0_2
.LBB0_3:
        ret
```

しっかり、mov/add 命令が 2 つに展開されていることが確認できます。

### メモリアクセス順序を意識する：vectoradd の別の書き方

先の書き方では、ループの中すべてをラムダ式で包んで展開しました。その書き方でも良いことも多いですが、欠点もあります。

具体的には、依存関係をしっかり記述することが難しいです。先の手動展開の時には、展開方法は

```
void vectoradd<2ul>(double*, double const*, double const*, unsigned long):           # @void vectoradd<2ul>(double*, double const*, double const*, unsigned long)
        test    rcx, rcx
        je      .LBB1_3
        xor     eax, eax
.LBB1_2:                                # =>This Inner Loop Header: Depth=1
        movsd   xmm0, qword ptr [rsi + 8*rax]   # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax]
        movsd   qword ptr [rdi + 8*rax], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 8] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 8]
        movsd   qword ptr [rdi + 8*rax + 8], xmm0
        add     rax, 2
        cmp     rax, rcx
        jb      .LBB1_2
.LBB1_3:
        ret
```

と、先に x,y の読み込み→演算→z への書き込みの順にしていました。このように記述するのは、コンパイラに「x,y の読み込みは最初にやってよい」（z の書き込みを待たなくて良い）ことを示して、メモリ読み書きのレイテンシを他の命令の裏に隠蔽するようにうまく並び替えてもらうためで、よく使われる高速化技法です。

しかし、先の全体をラムダ式で包む方法では、読み込み→演算→書き込み全体を展開しているので

```
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	for(std::size_t i = 0; i < n; i += 2)
	{
		const auto x_i0 = x[i + 0];
		const auto x_i1 = x[i + 1];
		const auto y_i0 = y[i + 0];
		const auto y_i1 = y[i + 1];
		const auto z_i0 = x_i0 + y_i0;
		const auto z_i1 = x_i1 + y_i1;
		z[i + 0] = z_i0;
		z[i + 1] = z_i1;
	}
}
```

という手動展開に等しくなります。これでは、x[1], y[1] の読み込みは、z[0] の書き込みが終わるまで待たないといけなくなります。せっかくループ展開の効果に「レイテンシの隠蔽」があるのに、それをうまく活用できていないことになります。実際、先の N=2 の x64 の命令列を見ても mov+add+mov が 2 つ連続しています。もっと分かりやすいのは、展開数 N をもっと増やして、例えば [N=16 とかにしてコンパイルした結果](https://godbolt.org/z/WbrGKK6vs)を見てみると

```
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	for(std::size_t i = 0; i < n; i += 2)
	{
		const auto x_i0 = x[i + 0];
		const auto y_i0 = y[i + 0];
		const auto z_i0 = x_i0 + y_i0;
		z[i + 0] = z_i0;

		const auto x_i1 = x[i + 1];
		const auto y_i1 = y[i + 1];
		const auto z_i1 = x_i1 + y_i1;
		z[i + 1] = z_i1;
	}
}
```

と、ひたすらに mov+add+mov が塊として繰り返されていてレジスタも常に 0 番 (xmm0) しか使用されない命令列が出てきます。

このような状況を回避するには、ループの内側を丸ごとではなく、しっかりと 1 文毎に（最低でもメモリ読み書きの単位で）ループ展開してやる必要があります。そのような展開は、[以下のように](https://wandbox.org/permlink/uk6PSYVIhBHGqBwn)すれば実現できます。

```
movsd   xmm0, qword ptr [rsi + 8*rax]   # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax]
        movsd   qword ptr [rdi + 8*rax], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 8] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 8]
        movsd   qword ptr [rdi + 8*rax + 8], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 16] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 16]
        movsd   qword ptr [rdi + 8*rax + 16], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 24] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 24]
        movsd   qword ptr [rdi + 8*rax + 24], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 32] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 32]
        movsd   qword ptr [rdi + 8*rax + 32], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 40] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 40]
        movsd   qword ptr [rdi + 8*rax + 40], xmm0
        movsd   xmm0, qword ptr [rsi + 8*rax + 48] # xmm0 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax + 48]
        movsd   qword ptr [rdi + 8*rax + 48], xmm0
（以下略）
```

x,y を固定長の配列 (`std::array`) に先に入れておくように変更されています。これは見た目上は配列（メモリ）への読み書きが発生しそうですが、コンパイラからは不要なことが自明なため、無駄なアクセスは省略されレジスタで済ませてくれることが期待されます。実際に [x64 Clang での結果](https://godbolt.org/z/fc7xsfosM)を見ると、

```
template<std::size_t... unrollIndices>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n,
	std::index_sequence<unrollIndices...>) {
	for(std::size_t i = 0; i < n; i += sizeof...(unrollIndices))
	{
		const auto x_ = std::array{(x[i + unrollIndices])...};
		const auto y_ = std::array{(y[i + unrollIndices])...};
		const auto z_ = std::array{(x_[unrollIndices] + y_[unrollIndices])...};
		((z[i + unrollIndices] = z_[unrollIndices]), ...);
	}
}
```

となって、想定通り、mov が 2 回→add が 2 回→mov が 2 回となっていて、レジスタも 0,1 番を使って、それぞれ先に読み込もうとしているのが確かめられます。展開数を 16 にしても[レジスタを 15 番（最大数）までしっかり使います](https://godbolt.org/z/P9qacq768)。

この方法について補足しておくと、例えばポインタに（標準規格外ですが）restrict などをつけてエイリアシングがないことを指定してやれば、ラムダ式のような包んだ状態でもある程度は回避が可能な場合もあります。しかし、ループ展開をするようなレベルの高速化コードを書くのであれば、ソースコード上から自明にメモリアクセス順序が分かってコンパイラに（ついでに人間にも）分かりやすいコードにしておいたほうが良いと思います。

### 水平演算：dot

これまでの例は、いずれも配列に対して要素毎にそれぞれ独立した処理でした。最後に、もう少し複雑な処理として、配列を水平方向に処理する演算、いわゆるリダクションをどう書くのか紹介します。

例として、以下のような内積計算 r=x ・ y を考えます。

```
void vectoradd<2ul>(double*, double const*, double const*, unsigned long):           # @void vectoradd<2ul>(double*, double const*, double const*, unsigned long)
        test    rcx, rcx
        je      .LBB1_3
        xor     eax, eax
.LBB1_2:                                # =>This Inner Loop Header: Depth=1
        movsd   xmm0, qword ptr [rsi + 8*rax]   # xmm0 = mem[0],zero
        movsd   xmm1, qword ptr [rsi + 8*rax + 8] # xmm1 = mem[0],zero
        addsd   xmm0, qword ptr [rdx + 8*rax]
        addsd   xmm1, qword ptr [rdx + 8*rax + 8]
        movsd   qword ptr [rdi + 8*rax], xmm0
        movsd   qword ptr [rdi + 8*rax + 8], xmm1
        add     rax, 2
        cmp     rax, rcx
        jb      .LBB1_2
.LBB1_3:
        ret
```

この水平演算を展開するのは、ここまでの内容を少し応用するだけです。[以下のようになります](https://wandbox.org/permlink/aNVacC9L3WpbDtGA)。

```
auto dot(const double x[], const double y[], const std::size_t n) {
	auto r = double{0};
	for(std::size_t i = 0; i < n; ++i)
	{
		const auto x_i = x[i];
		const auto y_i = y[i];
		const auto r_i = r + x_i * y_i;
		r = r_i;
	}
	return r;
}
```

少し複雑なところとして、展開した中でのローカルの和を求めるのに一段ラムダ式（の即時実行）を挟んでいるところでしょうか。畳み込み式の中身自体の書き方は前と同じで、結果への足し込みも含めてそのまま書くだけです。

これまでと同じように[コンパイルしてみた結果](https://godbolt.org/z/jM6hfv6E9)は以下の通りで、しっかり fma 命令が 2 回実行されていますね。

```
template<std::size_t... unrollIndices>
auto dot(const double x[], const double y[], const std::size_t n,
	std::index_sequence<unrollIndices...>) {
	auto r = double{0};
	for(std::size_t i = 0; i < n; i += sizeof...(unrollIndices))
	{
		const auto x_ = std::array{(x[i + unrollIndices])...};
		const auto y_ = std::array{(y[i + unrollIndices])...};
		const auto r_i = [&]
		{
			auto r_i = r;
			((r_i += x_[unrollIndices]*y_[unrollIndices]), ...);
			return r_i;
		}();
		r = r_i;
	}
	return r;
}
template<std::size_t UnrollN>
auto dot(const double x[], const double y[], const std::size_t n) {
	return dot(x, y, n, std::make_index_sequence<UnrollN>());
}
```

このような少し複雑な水平演算を畳み込み式で書く方法は、他には [Nifty Fold Expression Tricks](https://www.foonathan.net/2020/05/fold-tricks/) にまとまっているので、実際に自分の手元のコードを展開するには参考になると思います。

なお、少し補足しておくと、今回はあえて少し複雑な書き方をしており、ただの内積であれば実際には以下のような書き方のほうが簡潔です。

*   畳み込み式に + 演算子を直接使って、`const auto r_i = (r + ... + (x_[unrollIndices]*y_[unrollIndices]));`と書くことができます ([wandbox](https://wandbox.org/permlink/am40cgeexIo0T2h1), [godbolt](https://godbolt.org/z/7baWz5j59))。
*   ローカルの和を求めずとも直接、`((r += x_[unrollIndices]*y_[unrollIndices]), ...);`と書くこともできます ([wandbox](https://wandbox.org/permlink/7Pu2kVKzr1r43C4F), [godbolt](https://godbolt.org/z/ecd89Tzqr))。

どの書き方をしても、結果・命令列に大きな変化はありません。お好みの書き方で良いと思います。個人的には、min/max などのリダクションにも統一的に書けるので、最初のラムダ式を使った書き方が好みです。

## まとめ

というわけで、**しっかりと C++ を使うことで、煩雑になりがちなループ展開のコードを分かりやすく速度劣化なしに書ける技法**の紹介でした。

お気軽スクリプト言語（Python とか）や、古代言語（C や FORTRAN とか）ではなく、処理速度と機能の両面を考慮して進化し続けている C++ ならではの方法だと思います。みなさんも、ハードウェアの性能をソフトウェアで最大限に引き出す（もちろんバグも少なく）ために、ぜひ最新の C++ を使ってみてください。

## 付録

### コンパイラの自動ループ展開は？

途中の godbolt での出力で、O1 でコンパイルしているのを見つけた方もいるかもしれません。なぜ O1 にしたかというと、O3 などではコンパイラ最適化によって書いたコードから大きく変わってしまって説明がしづらかったからです。

具体的には、最近のまともなコンパイラであれば、しっかり最適化オプションを O3 などつけてやれば

*   自動ループ展開
*   自動 SIMD 化

あたりを**ある程度やってくれます**。完全自動でなくても、コンパイラ拡張で、例えば[`#pragma unroll (N)`](https://clang.llvm.org/docs/AttributeReference.html#pragma-unroll-pragma-nounroll)で N 段展開を指示することができます。なので、それで満足できるのであれば、今回のようにわざわざループ展開のコードを書く必要はありません。

しかし、それでどんなコードがどう出てくるかは結局コンパイラのご機嫌次第です。あまりループ内が長すぎたり複雑すぎたりすると、（書いてるプログラマ本人には自明でも）諦めたり、余計なことをして却って遅くなるなんてこともあります。ソフトウェアでしっかり高速化を突き詰めるのであれば、高度な機能のご機嫌伺いするより、今回ぐらいのコードは四の五の言わずにさっさと書いてしまったほうが最終的には早い＆速いと思います。

また、古典的なループ展開方法として、インライン関数＆再帰関数で書く手法が紹介されていることもありますが、再帰関数の展開は少し複雑な機能で、どこまで複雑な文がどこまでなら展開してくれるかは確実性が高くないため、ここで紹介した自動展開系とあまり事情は変わりません。

### 展開した番号の型を size_t 以外にしたい

今回、ループ展開した番号は`std::size_t`であることを前提で直にハードコードして書きました。ハードコードをやめたい場合はどうしたら良いでしょうか？

まず一般論として、展開した番号は、コンパイル時に展開されてしまう整数なので、型をあまり真剣に考える必要はありません。3 という番号が int だろうと unsigned long だろうと、コンパイラが型をあわせてくれます。

しかし、汎用ライブラリを作る場合、場合によってはどうしても違う型を使って指定したい場合もあると思います。

そんな場合は、例えば[以下のように](https://wandbox.org/permlink/nTkMn8GE2xPLtjk2)できます。

```
auto dot<2ul>(double const*, double const*, unsigned long):                  # @auto dot<2ul>(double const*, double const*, unsigned long)
        test    rdx, rdx
        je      .LBB1_1
        vxorpd  xmm1, xmm1, xmm1
        xor     eax, eax
.LBB1_3:                                # =>This Inner Loop Header: Depth=1
        vmovsd  xmm0, qword ptr [rsi + 8*rax]   # xmm0 = mem[0],zero
        vmovsd  xmm2, qword ptr [rsi + 8*rax + 8] # xmm2 = mem[0],zero
        vfmadd132sd     xmm0, xmm1, qword ptr [rdi + 8*rax] # xmm0 = (xmm0 * mem) + xmm1
        vfmadd231sd     xmm0, xmm2, qword ptr [rdi + 8*rax + 8] # xmm0 = (xmm2 * mem) + xmm0
        add     rax, 2
        vmovapd xmm1, xmm0
        cmp     rax, rdx
        jb      .LBB1_3
        ret
.LBB1_1:
        vxorps  xmm0, xmm0, xmm0
        ret
```

詳細は解説しませんが、それほど難しい機能は使っていないので、cpprefjp 等で調べてみてください。

### 未来の C++ ではどうなりますか

将来、規格がしっかりして、今の C++17 程度には多くのコンパイラに次期規格がしっかり実装されてその品質がよくなった時、業務で使える規格のバージョンを上げることができれば[ラムダ式にテンプレート](https://cpprefjp.github.io/lang/cpp20/familiar_template_syntax_for_generic_lambdas.html)を使っても良くなります。なので、それを即時実行すれば[以下のように](https://wandbox.org/permlink/dAybJDoiERuISmL2)、多重定義せず 1 関数にまとめられることが期待されます。

```
template<auto... unrollIndices>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n,
	std::integer_sequence<decltype((unrollIndices,...)), unrollIndices...>) {
	for(std::size_t i = 0; i < n; i += sizeof...(unrollIndices))
	{
		const auto x_ = std::array{(x[i + unrollIndices])...};
		const auto y_ = std::array{(y[i + unrollIndices])...};
		const auto z_ = std::array{(x_[unrollIndices] + y_[unrollIndices])...};
		((z[i + unrollIndices] = z_[unrollIndices]), ...);
	}
}
template<auto UnrollN>
void vectoradd(double z[], const double x[], const double y[], const std::size_t n) {
	vectoradd(z, x, y, n, std::make_integer_sequence<decltype(UnrollN), UnrollN>());
}
```

ただし、先述の通り C++ は今後も進化し続けていく（はずな）ので、もっと将来ではもっと便利で分かりやすいループ展開技法が使えるようになるかもしれません。今後の C++ にご期待ください！！