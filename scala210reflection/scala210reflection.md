!SLIDE
## Scala 2.10のリフレクション

<br/>

Scala 2.9までと比べると大幅に整理、強化されている。

<br/>

1. 今までScalaSignatureで取得していたような情報は、TypeTagを使ってアクセスできる。
2. メソッド呼び出しなどの操作は、Mirrorを使って行うことができる。
3. 構文木を組み立てることで、任意のコードを実行することができる。

<br/>

3番目はリフレクションとしては特徴的。構文木の作成はマクロと共通の機能になっている。

!SLIDE
## ClassTagとTypeTag

<br/>

- [Scaladoc](http://www.scala-lang.org/archives/downloads/distrib/files/nightly/docs/library/index.html#scala.reflect.base.TypeTags "Scala Standard Library API - scala.reflect.base.TypeTags") に詳しい説明がある
- Manifestの置き換えらしい。
- Implicit Parameterで取得するのは同じ。
- ClassTagはほとんどClassManifestと同じ。つまりJavaのクラスを取得するだけ。
- TypeTagはもはやManifestとは別物と思われる。

!SLIDE
## TypeTagとType

- TypeTagはTypeオブジェクトを取得できる。
- TypeオブジェクトはScalaSignatureで取得できたような情報が入っている。
- implicitかどうかなど、Javaのリフレクションでは取得できなかった情報も取得できる。

<br/>

```scala
scala> import scala.reflect.runtime.universe._
import scala.reflect.runtime.universe._

scala> val t = typeTag[List[_]].tpe
t: reflect.runtime.universe.Type = scala.List[_]

scala> val m = t.member(newTermName("filter"))
m: reflect.runtime.universe.Symbol = method filter

scala> m.asMethod.isImplicit
res0: Boolean = false
```

!SLIDE
## Universe

<br/>

- CAKEパターンで作られたリフレクションAPIの集合体。
- 細かく階層が分かれている。
- ただのクラス設計かと思いきや、どの階層でコードを書いているかを意識することが重要。

!SLIDE
## Universe

![Reflection universes](scala210reflection/reflection-hierarchy.jpg)

!SLIDE
## Universe

<br/>

- マクロはreflect.macro.Universeを使う。
- リフレクションはreflect.api.JavaUniverseを使う。
- 両方から使えるものを作りたい場合はreflect.api.Universeを使う。
- ちなみにGlobalというのはコンパイラで使われるもので、コンパイラプラグインを作るときに出てくる。
- マクロではContextでもAPIが提供されており、階層を行ったり来たりすると、どこで何が定義されているのか、何が使えるのかで頭がこんがらがる。

!SLIDE
## Mirror

<br/>

- Symbolはクラスやメソッド、変数などの定義に対応している。
- Symbolが表わしている対象をメタレベルで操作するときにはMirrorを使う
- トップレベルのMirrorはJavaのクラスローダに対応おり、そこからオブジェクトのMirrorやメソッドのMirrorを作る。

```scala
scala> import scala.reflect.runtime.universe._
import scala.reflect.runtime.universe._

scala> import scala.reflect.runtime.{currentMirror => cm}
import scala.reflect.runtime.{currentMirror=>cm}

scala> val m = typeOf[List[_]].member(newTermName("head"))
m: reflect.runtime.universe.Symbol = method head

scala> cm.reflect(List(1,2,3)).reflectMethod(m.asMethod)()
res0: Any = 1
```
