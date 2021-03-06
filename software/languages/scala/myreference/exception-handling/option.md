# Options

- https://Ww.youtube.com/watch?v=ol2AB5UN1IA
- Option is a collection type in Scala
- Unlike other collection types, an Option contains a maximum of one element
- It represents one of two possible values: None and Some

- Scala programmers dont like 'null'
- Some contains answer to be extracted(someVal.get())
- Extracting the answer can be done by calling get(), getOrElse()

- Option can be used with pattern matching
- it also defines methods like map, filter and flatMap to be used easily in a for-comp

- Scala still uses null to inter-operate with Java

- Option implements the Null Object pattern
- Option is also a Monad

* Option[T] : Abstract
  * Some[T]
  * None[T]

- Pattern matching on Option is almost never necessary
- the most idiomatic way to use Option instance is to threat it as a collection
  or monad and use map, flatMap or foreach...
- a less-idiomatic way to use Option values is via pattern matching

```scala
someOption match {
   case Some(a) => foo(a)
   case None => bar
}

// can be written
someOption map foo getOrElse bar

// or even after 2.10
someOption.fold(bar)(foo) // more type-strict
```
```scala
val m = Map(1 -> "a", 2 -> "b")
m.get(1) // Some(1)
m.get(3) // None

var x : Option[String] = None

x.get                     // java.util.NoSuchElementException: None.get in

x.getOrElse("default")    // "default"

var x = Some("Now it has something")

x.get                     // "Now it has something"

x.getOrElse("default")    // "Now it has something"
```


```scala
def getTempDir(tmpArg: Option[String]) : java.io.File = {
  tmpArg.map(name => new java.io.File(name))
        .filter(_.isDirectory)
        .getOrElse(new java.io.File(System.getProperty("java.io.tmpDir")))
}

def sayHi(name: Option[String]) : Unit = {
  // if it is None, for wont run
  for (n <- name) {
    println("Hi, " + name)  
  }
}
```
