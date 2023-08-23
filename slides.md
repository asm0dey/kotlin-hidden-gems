---
theme: dracula
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

# `whoami`

<v-clicks>

- <div v-after>Pasha Finkelshteyn</div>
- Dev <emojione-monotone-avocado /> at <simple-icons-jetbrains />
- ≈10 years in JVM. Mostly <la-java /> and <simple-icons-kotlin />
- Started <simple-icons-kotlin /> in pre-1.0 releases
- <simple-icons-twitter /> asm0di0
- <simple-icons-mastodon /> @asm0dey@fosstodon.org

</v-clicks>

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

# kotlin-compile-testing

https://github.com/tschuchortdev/kotlin-compile-testing
