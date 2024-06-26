# Cryptography

{% include headOScoverage.md %}

> Table Of Contents
>
> -   [Introduction](#introduction)
> -   [Configuration Methods](#configuration-methods)
> -   Available Cryptographers
>     1.  [AES Crypter](https://reglue4go.github.io/cryptography/crypterAES)
>     1.  [Digester](https://reglue4go.github.io/cryptography/digester)
>     1.  [ID Generator](https://reglue4go.github.io/cryptography/generatorID)
>     1.  [Argon2 Hasher](https://reglue4go.github.io/cryptography/hasherArgon2)
>     1.  [Bcrypt Hasher](https://reglue4go.github.io/cryptography/hasherBcrypt)
>     1.  [ID Transcoder](https://reglue4go.github.io/cryptography/transcoderID)
> -   [Unit Tests Matrix](#unit-tests-matrix)

## Introduction

The cryptography package provides a fluent interface for identity generation, data encrypting, decrypting, transcoding and more.

#### Digester

The cryptography digester can be used to protect the integrity of a piece of data by creating a one-way hash of a value. The following example uses the md5 and sha255 algoriths. Other supported message digest algoriths include sha1, sha3, sha512

```go
package main

import "github.com/reglue4go/cryptography"

func main() {
	secretValue, defaultAlgorithm := "secret", "md5"

	digester := cryptography.NewDigester(defaultAlgorithm)

	md5 := digester.Make(secretValue)

	fmt.Printf("%v\n", md5)

	md5Check := digester.Check(secretValue, md5)

	fmt.Printf("%v\n", md5Check)
	// true

	sha256 := digester.SetAlgorithm("sha256").Make(secretValue)

	fmt.Printf("%v\n", sha256)

	sha256Check := digester.Check(secretValue, sha256)

	fmt.Printf("%v\n", sha256Check)
	// true
}

```

### [Crypters](#introduction)

#### AES Crypter

Encrypting and decrypting text can be done using the AES-256 crypter. You must first set the key configuration option before before using the encrypter.
After instanciation, you can call the SetKey method to update the encryption key.

```go
package main

import "github.com/reglue4go/cryptography"

func main() {
	secretValue := "4012000033330026"
	encryptionKey := "6hdXj19qz9Nxaiu4CcVvtep3vPLhVfuL"

	crypter := cryptography.NewCrypterAES(encryptionKey)

	encryptedSecret:= crypter.Encrypt(secretValue)

	fmt.Printf("%v\n", encryptedSecret)

	decryptedSecret:= crypter.Decrypt(encryptedSecret)

	fmt.Printf("%v\n", decryptedSecret == secretValue)
	// true
}

```

### [Generators](#introduction)

#### ID Generator

The identity generator can be used to produce universally unique identifiers using UUID, ULID and KSUID algoriths. A ULID generator has been provided by default.

```go
package main

import "github.com/reglue4go/cryptography"

func main() {
	algorithm := "ulid"
	generator := cryptography.NewGeneratorID(algorithm)

	id1 := generator.Generate()

	fmt.Printf("%v\n", id1)

	id2 := generator.Generate()

	fmt.Printf("%v\n", id2)
}
```

### [Hashing](#introduction)

Hashing of values such as user passwords can be done using bcrypt and argon2 cryptographic algorithms. You may hash a password by calling the Make method on an instance of a hasher. The hasher Check method can be used to compare a password to a hashed value.

#### Argon2 Hasher

```go
package main

import "github.com/reglue4go/cryptography"

func main() {
	password := "secret"
	encryptionKey := "6hdXj19qz9Nxaiu4CcVvtep3vPLhVfuL"

	argon2 := cryptography.NewHasherArgon2(encryptionKey)

	passwordHashed := argon2.Make(password)

	fmt.Printf("%v\n", passwordHashed)

	passwordChecked := argon2.Check(password, passwordHashed)

	fmt.Printf("%v\n", passwordChecked)
	// true
}
```

#### [Bcrypt Hasher](#hashing)

```go
package main

import "github.com/reglue4go/cryptography"

func main() {
	password := "secret"
	cost := 12

	bcrypt := cryptography.NewHasherBcrypt(cost)

	passwordHashed2 := bcrypt.Make(password)

	fmt.Printf("%v\n", passwordHashed2)

	passwordChecked := bcrypt.Check(password, passwordHashed2)

	fmt.Printf("%v\n", passwordChecked)
	// true
}
```

### [Transcoding](#introduction)

#### ID Transcoder

You can generate short unique URL-safe identifiers from numbers using the identity transcoder. These are good for link shortening and securely hiding database generated keys.

```go
package main

import "github.com/reglue4go/cryptography"

func main() {
	transcoder := cryptography.NewTranscoderID()
	transcoder.SetConfig("alphabet", "FxnXM1kBN6cuhsAvjW3Co7l2RePyY8DwaU04Tzt9fHQrqSVKdpimLGIJOgb5ZE")
	transcoder.SetConfig("blocklist", "86Rf07")
	transcoder.UseDefaultTranscoder()

	id1 := transcoder.Generate()

	fmt.Printf("%v\n", id1)

	id2 := transcoder.Generate()

	fmt.Printf("%v\n", id2)
}
```

## Configuration Methods

The following methods are available to all cryptographers. You can call each of these methods after instantiating the cryptographer.

#### [SetAlgorithm](#configuration-methods)

```go
cryptographer.SetAlgorithm("sha1")
```

#### [GetAlgorithm](#configuration-methods)

```go
algo := cryptographer.GetAlgorithm() // "sha1"
```

#### [SetConfig](#configuration-methods)

```go
cryptographer.SetConfig("key", "value")
```

#### [GetConfig](#configuration-methods)

```go
cryptographer.GetConfig("key") // "value"
```

{% include footMatrixes.md %}
