# Getting Started

So, you've checked out the project and want to crack on making your first plugin? Great! This guide should show you how.

You can depend on the API through your build system of choice:

* Gradle Groovy:

```groovy
repositories {
    maven { url 'https://repo.kryptonmc.org/releases' }
}

dependencies {
    compileOnly 'org.kryptonmc:api:LATEST'
}
```

* Gradle Kotlin:

```kotlin
repositories {
    maven("https://repo.kryptonmc.org/releases")
}

dependencies {
    compileOnly("org.kryptonmc:api:LATEST")
}
```

* Maven:

```markup
<repositories>
    <repository>
        <id>krypton-repo</id>
        <url>https://repo.kryptonmc.org/releases</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>org.kryptonmc</groupId>
        <artifactId>api</artifactId>
        <version>LATEST</version>
    </dependency>
</dependencies>
```

The KDocs for the API can be found at [https://docs.kryptonmc.org/krypton-api](https://docs.kryptonmc.org/krypton-api)

