!SLIDE
## Scala 2.9までのリフレクション

<br/>

- Manifestを使ってJavaのクラスを取得
<br/>
→ Javaのリフレクションを使う。
- 裏技的にScalaSignatureを読み込み情報を取得する。

<br/>

要するにScalaとしてはリフレクションの手段をちゃんと用意していなかった。

!SLIDE
## ClassManifestとManifest

<br/>

- Implicit Parameterとして取得する。
- Javaのクラスにアクセスできる。

<br/>

```scala
def hoge[T: ClassManifest] = { 
  val c = implicitly[ClassManifest[T]].erasure
  ...
}
```

!SLIDE
## ClassManifestとManifest

<br/>

- 基本的にJavaのクラスを取得するためだけのものと考えてよい。
- 型の比較もできる。が、あまり使わないと思う…。
- JVMの型消去のため、型変数からクラスを取得できないために、こういう手段を使わざるをえない。
- ClassManifestはそのクラスの情報だけ持っているだけ。
- ManifestはClassManifestの機能に加えて、型引数のManifestも持っている。

!SLIDE
## ScalaSignature

<br/>

- Javaのクラスファイルに入っているScalaの情報。
- ScalaSigParserを使って情報を取得することができる。

<br/>

```scala
scala> import scala.tools.scalap.scalax.rules.scalasig._

scala> val scalaSig = ScalaSigParser.parse(classOf[List[_]]).get

scala> scalaSig.topLevelClasses(0).children foreach println

TypeSymbol(A, owner=0, flags=12100, info=19 )
MethodSymbol(<init>, owner=0, flags=200, info=47 ,None)
MethodSymbol(companion, owner=0, flags=220, info=50 ,None)
MethodSymbol(isEmpty, owner=0, flags=300, info=56 ,None)
MethodSymbol(head, owner=0, flags=300, info=62 ,None)
...
```

!SLIDE
## ScalaSignature

<br/>

- 型情報に加えてメソッドの情報などもわかる。
- しかし公式にAPIが公開されているわけではなく、利用者も少ない。
- xuwei_kさん曰く「積極的に使うべきものではない」とのこと。
