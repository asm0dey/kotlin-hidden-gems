---
theme: dracula
css: unocss
background: https://source.unsplash.com/collection/94734566/1920x1080
lineNumbers: true
colorSchema: 'dark'
layout: intro
highlighter: shiki
canvasWidth: 980
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
- Dev <noto-v1-avocado /> at <logos-jetbrains />
- ≈10 years in JVM. Mostly <logos-java /> and <logos-kotlin-icon />
- Started <logos-kotlin-icon /> in pre-1.0 releases
- <logos-twitter /> asm0di0
- <logos-mastodon-icon /> @asm0dey@fosstodon.org

</v-clicks>

---

# What you already know
### Hopefully

- Type-level null-safety
- Operator overloading
- Building DSLs
- Awesome <logos-java /> interop

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

# AssertiK

[willowtreeapps/assertk](https://github.com/willowtreeapps/assertk)

AssertJ-inspired assertions

```kotlin {1|2,3|4,5|6,7}
val person = Person(name = "Bob", age = 18)
assertThat(person.name).isEqualTo("Alice")
// -> expected:<["Alice"]> but was:<["Bob"]>
assertThat(person.age, "age").isGreaterThan(20)
// -> expected [age] to be greater than:<20> but was:<18>
assertThat(person::name).isEqualTo("Alice")
// -> expected [name]:<["Alice"]> but was:<["Bob"]>
```

---

# kotlin-compile-testing

https://github.com/tschuchortdev/kotlin-compile-testing
