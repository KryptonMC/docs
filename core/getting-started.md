# Getting Started

So, you've checked out the project and want to crack on making your first plugin? Great! This guide should show you how.

You can depend on the API through your build system of choice:

* Gradle Groovy:

```groovy
repositories {
    mavenCentral()
	maven { url 'https://libraries.minecraft.net }
	maven { url 'https://repo.bristermitten.me/repository/maven-public/ }
}

dependencies {
    compileOnly 'org.kryptonmc:krypton-api:LATEST'
}
```

* Gradle Kotlin:

```kotlin
repositories {
    mavenCentral()
	maven("https://libraries.minecraft.net")
	maven("https://repo.bristermitten.me/repository/maven-public/")
}

dependencies {
    compileOnly("org.kryptonmc:krypton-api:LATEST")
}
```

* Maven:

```xml
<repositories>
    <repository>
	    <id>minecraft</id>
		<url>https://libraries.minecraft.net</url>
	</repository>
	<repository>
	    <id>bristermitten</id>
		<url>https://repo.bristermitten.me/repository/maven-public/</url>
	</repository>
</repositories>

<dependencies>
	<dependency>
		<groupId>org.kryptonmc</groupId>
		<artifactId>krypton-api</artifactId>
		<version>LATEST</version>
	</dependency>
</dependencies>
```

The KDocs for the API can be found at https://docs.kryptonmc.org/krypton-api