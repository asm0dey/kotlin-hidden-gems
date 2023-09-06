---
theme: default
css: unocss
background: https://source.unsplash.com/collection/94734566/1920x1080
lineNumbers: true
colorSchema: 'dark'
layout: intro
highlighter: shiki
canvasWidth: 850
aspectRatio: '16/9'
routerMode: 'history'
selectable: true
remoteAssets: true
download: true
titleTemplate: "Kotlin's Hidden Arsenal"
info: Pasha Finkelshteyn
drawings:
  enabled: true
  persist: false
  presenterOnly: false
  syncAll: true
export:
  format: pdf
  timeout: 30000
  withClicks: true
  withToc: false
---

# Unleashing Kotlin's Hidden Arsenal

## <logos-kotlin-icon />

---
layout: two-cols
clicks: 7
---

# `whoami`

<v-clicks>

- <div v-after>Pasha Finkelshteyn</div>
- Dev <emojione-monotone-avocado /> at <simple-icons-jetbrains />
- ≈10 years in JVM. Mostly <la-java /> and <simple-icons-kotlin />
- Started <simple-icons-kotlin /> in pre-1.0 releases
- <span v-click=6><simple-icons-twitter /> asm0di0</span>
- <simple-icons-mastodon /> @asm0dey@fosstodon.org

</v-clicks>

::right::

<div v-click=5>

![](/ganesha.png)

</div>

---

# What you already know <span v-click>(hopefully)</span>

<v-after>

- Type-level null-safety
- Coroutines
- Operator overloading
- Building DSLs
- Awesome <la-java /> interop

</v-after>

---

# Compatible with

- Spring
- Ktor
- Vert.x
- Jackson
- JPA
- younameit

---

# Has an established and not _hidden_ ecosystem

- Ktor<span v-click>. Web framework</span>
- kotlinx.serialization<span v-click>. <em>Serialization, obviously</em></span>
- kotlinx.datetime<span v-click>. Like Java DateTime API, but multiplatform</span>
- kotlinx.coroutines<span v-click> Like loom, but better</span>
- Exposed<span v-click>. ORM/query builder</span>
- Arrow<span v-click>. Functional things</span>

---
layout: image
image: /16th.png
---

## But how do we start using it?

---

# Tests

- Learn the syntax
- Learn the best practices
- Understand the power

---

# Do we just use JUnit as usual?

You can!

<v-click>

But why limit yourself?

</v-click>

---
layout: statement
---

# Let's start with libraries for testing!

---

# Mocking with `mockk`

#### https://mockk.io

```kotlin {all|1|2|3|4|5|all}
val car = mockk<Car>()
every { car.drive(Direction.NORTH) } returns Outcome.OK
car.drive(Direction.NORTH) // returns OK
verify { car.drive(Direction.NORTH) }
confirmVerified(car)
```

---

# Mocking with `mockk`

#### https://mockk.io

```kotlin {all|1-9|1,9|2|3-8|3|4-7|4|5|6|10|11-12|13} {lines:true}
val repository = mockk<Repository>() {
  every { getUsers() } returns listOf(User("Pasha"), User("J. P. Morgan"))
  every { roles() } returns listOf(
    mockk {
      every { name } returns "admin"
      every { rights } returns listOf("delete", "read", "write")
    }
  )
}
val service = Service(repository)
service.findAllUsers()
// checks
confirmVerified(repository) // throws exception because roles were not accessed
```

---
layout: statement
---

# What do we do after mocking?

<v-click>

## We assert!

</v-click>

---
routeAlias: assertik
---

# AssertiK: basics

[willowtreeapps/assertk](https://github.com/willowtreeapps/assertk)

AssertJ-inspired assertions

```kotlin {1|2,3|4,5|6,7|3,5,7}
val person = Person(name = "Bob", age = 18)
assertThat(person.name).isEqualTo("Alice")
// -> expected:<["Alice"]> but was:<["Bob"]>
assertThat(person.age, "age").isGreaterThan(20)
// -> expected [age] to be greater than:<20> but was:<18>
assertThat(person::name).isEqualTo("Alice")
// -> expected [name]:<["Alice"]> but was:<["Bob"]>
```

---

# AssertiK: null-safety

[willowtreeapps/assertk](https://github.com/willowtreeapps/assertk)

```kotlin {1|all}
val nullString: String? = null
assertThat(nullString).hasLength(4)
```

<p v-click> Won't compile! <twemoji-party-popper /></p>
<v-click>

```kotlin {1,2|all}
val nullString: String? = null
assertThat(nullString).isNotNull().hasLength(4)
// -> expected to not be null
```

</v-click>
<p v-after>Fails! <twemoji-party-popper /></p>

---

# AssertiK: soft assertions

[willowtreeapps/assertk](https://github.com/willowtreeapps/assertk)

<p v-click>What if you need to run multiple assertions to know EVERYTHING?</p>
<v-click>

```kotlin {1|2|3|4,5|7-9|all}
val string = "Test"
assertThat(string)
  .all {
    startsWith("L")
    hasLength(3)
  }
// -> The following 2 assertions failed:
//    - expected to start with:<"L"> but was:<"Test">
//    - expected to have length:<3> but was:<"Test"> (4)
```

</v-click>
<v-after>

Also possible with `assertAll {}`

</v-after>

---

# AssertiK: custom assertions

[willowtreeapps/assertk](https://github.com/willowtreeapps/assertk)

Thanks to Kotlin extension functions we can do native-looking custom assertions!

```kotlin {1|2|5|5-6}
fun Assert<Person>.hasAge(expected: Int) {
    prop(Person::age).isEqualTo(expected)
}

assertThat(person).hasAge(20)
// -> expected [age]:<2[0]> but was:<2[2]> (Person(age=22))
```

Because <simple-icons-kotlin/> is <simple-icons-awesomelists/>

---

# Strikt

https://strikt.io

Reminds of <Link to="assertik">AssertiK</Link>

```kotlin
val subject = "The Enlightened take things Lightly"
expectThat(subject)
  .hasLength(35)
  .matches(Regex("[\\w\\s]+"))
  .startsWith("T")
```

---

# Strikt: strong typing

https://strikt.io

```kotlin {all|2|3|4|5,6}
val subject: Any? = "The Enlightened take things Lightly"
expectThat(subject) // type: Assertion.Builder<Any?>
  .isNotNull()      // type: Assertion.Builder<Any>
  .isA<String>()    // type: Assertion.Builder<String>
  // only available on Assertion.Builder<CharSequence>
  .matches(Regex("[\\w\\s]+"))
```

---

# Strikt: custom mappings

```kotlin {all|1|2|3|4-6}
val Assertion.Builder<Pantheon>.realm: Assertion.Builder<String>
  get() = get { "$ruler to $underworldRuler" } // has access to Pantheon
val subject = Pantheon.NORSE
expectThat(subject)
  .realm
  .isEqualTo("Odin to Hel")
```

---

# Other assertion libs

<v-clicks>

1. [Kluent](https://markusamshove.github.io/Kluent/)
2. Atrium: https://docs.atriumlib.org
3. [Expekt](http://winterbe.github.io/expekt/)
4. More? 

</v-clicks>

---
layout: statement
---

# How do we check assertions?

<h2 v-click>With runners!</h2>

---

# Kotest

https://kotest.io

What if instead of this:
<v-click>

```kotlin {lines:false}
class MyTestClass {
  @Test fun `A is B`(){  }
  @Test fun `A is not C`(){  }
  @Test fun `A is sometimes D`(){  }
}
```

</v-click>
<v-click>

We could have this?

```kotlin {2|3|4|1|all} {lines:false}
class NestedTestExamples : DescribeSpec({
   describe("A") {
      it("is B") {  }
      it("an inner test too!") {  }
   }
})

```

</v-click>
---

# Kotest

https://kotest.io

Or even like this?

```kotlin {2|3|4,6|8|9,11|1|all}
class MyTests : BehaviorSpec({
    given("a broomstick") {
        `when`("I sit on it") {
            then("I should be able to fly") {
                // test code
            }
        }
        `when`("I throw it away") {
            then("it should come back") {
                // test code
            }
        }
    }
})
```

---

# Kotest: and also

https://kotest.io

<v-clicks>

1. Data-driven testing
2. Property-based testing
5. Non-deterministic Testing
3. Integration with TestContainers
4. Integration with WireMock
6. Many-many more!

</v-clicks>

---

# Kotest: testing styles
<v-clicks>
<div class="flex-justify-between display-flex">

[Fun Spec](https://kotest.io/docs/framework/testing-styles.html#fun-spec)

[Describe Spec](https://kotest.io/docs/framework/testing-styles.html#describe-spec) (JS + RSpec)

[Should Spec](https://kotest.io/docs/framework/testing-styles.html#should-spec)

</div>
<div class="flex-justify-between display-flex" v-clicks>


  [String Spec](https://kotest.io/docs/framework/testing-styles.html#string-spec)
  
  [Behavior Spec](https://kotest.io/docs/framework/testing-styles.html#behavior-spec) (BDD)
  
  [Free Spec](https://kotest.io/docs/framework/testing-styles.html#free-spec)

</div>
<div class="flex-justify-between display-flex">

[Word Spec](https://kotest.io/docs/framework/testing-styles.html#word-spec)

[Feature Spec](https://kotest.io/docs/framework/testing-styles.html#feature-spec) (Cucumber)
  
[Expect Spec](https://kotest.io/docs/framework/testing-styles.html#expect-spec)

</div>
<div class="flex-justify-around display-flex">

[Annotation Spec](https://kotest.io/docs/framework/testing-styles.html#annotation-spec) (Migrate from JUnit)

</div>
</v-clicks>

---
layout: statement
---

# Midway between testing and production

---

# skrape{it}

https://docs.skrape.it

Features

- Easy to use, idiomatic and type-safe DSL
- Not bind to a specific test-runner, framework
- Supports non-blocking fetching / Coroutine support

Capabilities:

- Parsing
- Http client
- Assertions

---

# skrape{it}: Parsing

https://docs.skrape.it

```kotlin
skrape {
  url = "https://skrape.it"
  extract {
    htmlDocument {
      // parsed document available
    }
  }
}
```

---

# skrape{it}: Parsing

https://docs.skrape.it

```kotlin {all|1|2-7|2|3|4|5|8-15|8|9|11|12|5,12}
htmlDocument(someHtml) {
  meta { 
    withAttribute = "name" to "foo"
    findFirst {
      attribute("content") toBe "bar" // can do any action here
    }
  }
  p {
    findAll {
      toBePresentTimes(4)
        forEach { 
          text toContain "paragraph" // and here
        }
    }
  }
}
```

---

# skrape{it}: Example

```kotlin {all|1|2-4|5|6|7|8|9|all}
val links: List<String> = skrape(HttpFetcher) {
  request {
    url = "https://kotlinlang.org/docs/reference/"
  }
  extract {
    htmlDocument {
      a {
        findAll {
          eachHref
        }
      }
    }
  }
}
```

<v-after>

Site is client-side rendered? Just replace `HttpFetcher` with `BrowserFetcher`!

</v-after>

---
layout: statement
---

# Closer to production!


---

# jaicf-kotlin

https://help.jaicf.com

Framework to interact with customers over _modern_ channels

```kotlin {all|1|2|3|4-6|9|10|11-13|all}
val HelloWorldScenario = Scenario {
    state("main") {
        activators {
            event(AlexaEvent.LAUNCH)
            intent(DialogflowIntent.WELCOME)
            regex("/start")
        }
        
        action {
            reactions.run {
                sayRandom("Hi!", "Hello there!")
                say("How are you?")
                telegram?.image("https://somecutecats.com/cat.jpg")
            }
        }
    }
}
```

---

# jaicf-kotlin: Features

<v-clicks>

1. Kotlin DSL
2. Integrates with
    1. Voice assistants
    2. Messengers
    3. Natural Language Understanding frameworks
    4. Telephony!
3. Integrates with Spring/Ktor

</v-clicks>

---

# jaicf-kotlin: Ktor Integration

```kotlin {all|1|2|3|4-7|8-10|12}
fun main() {
  embeddedServer(Netty, 8000) {
    routing {
      httpBotRouting(
        "/alexa" to AlexaChannel(gameClockBot),
        "/actions" to ActionsFulfillment.dialogflow(gameClockBot)
      )
      get("/") {
        call.respondText("Hi!")
      }
    }
  }.start(wait = true)
}
```

<p v-click="4">This is how an existing API is augmented</p>

---
layout: two-cols
---

<h1 class="grid-self-center">You all know exposed</h1>

<a href="https://github.com/JetBrains/Exposed" v-click="2">https://github.com/JetBrains/Exposed</a>
<div v-click="3">

DSL API

```kotlin
object StarWarsFilms : Table() {
  val id: Column<Int> = integer("id").autoIncrement()
  val sequelId: Column<Int> = integer("sequel_id").uniqueIndex()
  val name: Column<String> = varchar("name", 50)
  val director: Column<String> = varchar("director", 50)
}
```

</div>

::right::

<div v-click="1" v-click-hide="3">

![](/right.jpg)

</div>

---
layout: end
---

# That's all, friends
