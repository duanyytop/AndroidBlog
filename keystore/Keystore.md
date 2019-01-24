# Android Keystore 

Android 从 7.0 开始支持安全硬件生成公私钥，并对用户数据进行加解密以及签名和验证，上层 API 封装集中在 Keystore 类中，Android 内部采用的非对称加密算法是[SECPxxxR1](https://developer.android.com/training/articles/keystore#SupportedKeyPairGenerators)。所谓的安全硬件包括可信执行环境（TEE）和安全元件（Secure Element SE）,
安全硬件都可以在芯片内部生成公私钥对，外部无法获取私钥，但是可以获得公钥。两种使用场景在生成公私钥对时需要配置不同的参数，下面会分别介绍加解密和签名验签的操作。

## Encrypt and Decrypt

当应用需要用安全硬件内部的私钥加密时，整个加密过程都在芯片内部完成，外部只能获得加密后的结果，当然公钥是可以进行解密的，同样的解密操作依然是在芯片内部完成，外部无法访问和干预。

下面分别展示生成私钥、查询私钥、签名和验签的代码

### Generate a new private key

```java
final KeyGenerator keyGenerator = KeyGenerator
                .getInstance(KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore");

keyGenerator.init(new KeyGenParameterSpec.Builder(alias,
        KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
        .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
        .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
        .build());

SecretKey secretKey = keyGenerator.generateKey();
```

### List entries

```java
private SecretKey getSecretKey(final String alias) throws NoSuchAlgorithmException,
            UnrecoverableEntryException, KeyStoreException {
    return ((KeyStore.SecretKeyEntry) keyStore.getEntry(alias, null)).getSecretKey();
}
```

### Encrypt

```java
final Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
cipher.init(Cipher.ENCRYPT_MODE, getSecretKey(alias));

iv = cipher.getIV();

encryption = cipher.doFinal(textToEncrypt.getBytes("UTF-8"));
```

### Decrypt

```java
final Cipher cipher = Cipher.getInstance("AndroidKeyStore");
final GCMParameterSpec spec = new GCMParameterSpec(128, encryptionIv);
cipher.init(Cipher.DECRYPT_MODE, getSecretKey(alias), spec);

return new String(cipher.doFinal(encryptedData), "UTF-8");
```

详细代码可以参考 [KeystoreDemo](https://github.com/duanyytop/KeyStoreDemo)

## Sign and Verify

当用户需要用安全硬件内部的私钥做签名操作时，整个签名过程全部在芯片内部完成，外部只能获得签名后的结果，对于验签也是类似的逻辑，所有的验签过程都在芯片
内部完成，外部只能获得验签成功还是失败。

下面分别展示生成私钥、查询私钥、签名和验签的代码

### Generate a new private key

```kotlin
/*
 * Generate a new EC key pair entry in the Android Keystore by
 * using the KeyPairGenerator API. The private key can only be
 * used for signing or verification and only with SHA-256 or
 * SHA-512 as the message digest.
 */
val kpg: KeyPairGenerator = KeyPairGenerator.getInstance(
        KeyProperties.KEY_ALGORITHM_EC,
        "AndroidKeyStore"
)
val parameterSpec: KeyGenParameterSpec = KeyGenParameterSpec.Builder(
        alias,
        KeyProperties.PURPOSE_SIGN or KeyProperties.PURPOSE_VERIFY
).run {
    setDigests(KeyProperties.DIGEST_SHA256, KeyProperties.DIGEST_SHA512)
    build()
}

kpg.initialize(parameterSpec)

val kp = kpg.generateKeyPair()   // kp's data type is PrivateKey which can't visit origin private key
```

### List entries

```kotlin
/*
 * Load the Android KeyStore instance using the
 * "AndroidKeyStore" provider to list out what entries are
 * currently stored.
 */
val ks: KeyStore = KeyStore.getInstance("AndroidKeyStore").apply {
    load(null)
}
val aliases: Enumeration<String> = ks.aliases()
```

### Sign data

```kotlin
/*
 * Use a PrivateKey in the KeyStore to create a signature over
 * some data.
 */
val ks: KeyStore = KeyStore.getInstance("AndroidKeyStore").apply {
    load(null)
}
val entry: KeyStore.Entry = ks.getEntry(alias, null)
if (entry !is KeyStore.PrivateKeyEntry) {
    Log.w(TAG, "Not an instance of a PrivateKeyEntry")
    return null
}
val signature: ByteArray = Signature.getInstance("SHA256withECDSA").run {
    initSign(entry.privateKey)
    update(data)
    sign()
}
```

### Verify data

```kotlin
/*
 * Verify a signature previously made by a PrivateKey in our
 * KeyStore. This uses the X.509 certificate attached to our
 * private key in the KeyStore to validate a previously
 * generated signature.
 */
val ks = KeyStore.getInstance("AndroidKeyStore").apply {
    load(null)
}
val entry = ks.getEntry(alias, null) as? KeyStore.PrivateKeyEntry
if (entry == null) {
    Log.w(TAG, "Not an instance of a PrivateKeyEntry")
    return false
}
val valid: Boolean = Signature.getInstance("SHA256withECDSA").run {
    initVerify(entry.certificate)
    update(data)
    verify(signature)
}
```

详细代码可以参考 Google 官方给出的例子，安全硬件签名和验证通常会和指纹识别结合起来使用。[AsymmetricFingerprintDialog](https://github.com/googlesamples/android-AsymmetricFingerprintDialog)