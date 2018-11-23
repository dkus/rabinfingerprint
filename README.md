rabinfingerprint
================

A Java implementation of the rabin fingerprinting method. (http://en.wikipedia.org/wiki/Rabin_fingerprint)

Optimized for use on a stream including a sliding window fingerprint.

Includes arbitrary-precision-polynomial hashing as well as very fast long-based hashing implementations, which are best for most hashing uses.

### Generating a fingerprint ###

You can generate fingerprint via:
- fingerprint library (`rabinfingerprint-core` or `handprint-core`)
- fingerprint runnable application (`rabinfingerprint-cli`, see Command line section)
 
Add dependency to your project using Maven:
```xml
<dependencies>
  <dependency>
    <groupId>org.rabinfingerprint</groupId>
    <artifactId>rabinfingerprint-core</artifactId>
    <version>1.0</version>
  </dependency>
  <!-- guava library is needed in examples due to usage of ByteStreams -->
  <dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>16.0.1</version>
  </dependency>
</dependencies>
```

To add a dependency using Gradle:

```gradle
dependencies {
  implementation 'org.rabinfingerprint:rabinfingerprint-core:1.0'
  // guava library is needed in examples due to usage of ByteStreams
  implementation 'com.google.guava:guava:16.0.1'
}
```
Basic example:
```Java
// Create new random irreducible polynomial
// These can also be created from Longs or hex Strings
Polynomial polynomial = Polynomial.createIrreducible(53);

// Create a fingerprint object
RabinFingerprintLong rabin = new RabinFingerprintLong(polynomial);

// Push bytes from a file stream
rabin.pushBytes(ByteStreams.toByteArray(new FileInputStream("file.test")));

// Get fingerprint value and output
System.out.println(Long.toString(rabin.getFingerprintLong(), 16));
```

Example of generating a sliding-window fingerprint:
```Java
// Create new random irreducible polynomial
// These can also be created from Longs or hex Strings
Polynomial polynomial = Polynomial.createIrreducible(53);

// Create a windowed fingerprint object with a window size of 48 bytes.
RabinFingerprintLongWindowed window = new RabinFingerprintLongWindowed(polynomial, 48);
for (byte b : ByteStreams.toByteArray(new FileInputStream("file.test"))) {
	// Push in one byte. Old bytes are automatically popped.
	window.pushByte(b);
	// Output current window's fingerprint
	System.out.println(Long.toString(window.getFingerprintLong(), 16));
}
```

### Command line ###

[Full Usage](rabinfingerprint-cli/src/main/resources/usage.txt)

Generate a new irreducible polynomial
```
% ./bin/rabinfingerprint-cli -polygen 53
3DE9DD57CA448B
```

Fingerprint a file
```
% ./bin/rabinfingerprint-cli -p 3DE9DD57CA448B file.test
43A39C59491F /[path to file]/file.test
```

Fingerprint STDIN
```
% cat file.test | ./bin/rabinfingerprint-cli -p 3DE9DD57CA448B
43A39C59491F
```

### Building ###

This project uses Gradle for dependency management. 
To build this project's runnable jar, library jars with sources and javadoc, run this command:

```
% ./gradlew clean build
```

### Releasing ###

Project artifacts are released via [Gradle Bintray Plugin](https://github.com/bintray/gradle-bintray-plugin)

```
./gradlew -PreleaseVersion=<version> -PbintrayUser=<user> -PbintrayApiKey=<api key> -PbintrayPassphrase=<optional passphrase> clean build bintrayUpload
```
