---
title: Hızlı başlangıç-Java için Azure Key Vault anahtar istemci kitaplığı
description: Java için Azure Key Vault Keys istemci kitaplığı için hızlı başlangıç sağlar.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: ecebeb6d14a00a92ef845001cf04a59f10345e23
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815585"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Hızlı başlangıç: Java için Azure Key Vault anahtar istemci kitaplığı
Java için Azure Key Vault anahtar istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin.

Ek kaynaklar:

* [Kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [API başvuru belgeleri](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Ürün belgeleri](index.yml)
* [Örnekler](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Önkoşullar
- Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) sürüm 8 veya üstü
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Bu hızlı başlangıçta, bir Linux Terminal penceresinde [Azure CLI](/cli/azure/install-azure-cli) ve [Apache Maven](https://maven.apache.org) çalıştırdığınız varsayılmaktadır.

## <a name="setting-up"></a>Ayarlanıyor
Bu hızlı başlangıçta, Azure 'da kullanıcının kimliğini doğrulamak için Azure CLı ile Azure Identity kitaplığı kullanılıyor. Geliştiriciler, aramalarını doğrulamak için Visual Studio veya Visual Studio Code de kullanabilir. daha fazla bilgi için bkz. [Azure Identity istemci kitaplığı ile Istemci kimlik doğrulaması](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Azure'da oturum açma
1. `login` komutunu çalıştırın.

    ```azurecli-interactive
    az login
    ```

   CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

   Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin.

2. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

### <a name="create-a-new-java-console-app"></a>Yeni bir Java konsol uygulaması oluşturma
Konsol penceresinde, `mvn` adıyla yeni bir Java konsol uygulaması oluşturmak için komutunu kullanın `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Projenin üretilme çıktısı şuna benzer şekilde görünür:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Dizininizi yeni oluşturulan `akv-keys-java/` klasöre değiştirin.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Paketi yükler
*pom.xml* dosyasını metin düzenleyicinizde açın. Aşağıdaki bağımlılık öğelerini bağımlılıklar grubuna ekleyin.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin
Anahtar kasanız için Kullanıcı hesabınıza anahtar izinleri veren bir erişim ilkesi oluşturun.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme
Bu uygulama, Anahtar Kasası adınızı adlı bir ortam değişkeni olarak kullanıyor `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS veya Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Nesne modeli
Java için Azure Key Vault anahtar istemci kitaplığı, anahtarları yönetmenizi sağlar. [Kod örnekleri](#code-examples) bölümünde, bir istemci oluşturma, anahtar oluşturma, anahtar alma ve bir anahtarı silme işlemlerinin nasıl yapılacağı gösterilir.

Konsol uygulamasının tamamı [aşağıda](#sample-code)verilmiştir.

## <a name="code-examples"></a>Kod örnekleri
### <a name="add-directives"></a>Yönergeler ekleme
Aşağıdaki yönergeleri kodunuzun en üstüne ekleyin:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma
Bu hızlı başlangıçta, oturum açan bir Kullanıcı, yerel geliştirme için tercih edilen yöntem olan Key Vault kimlik doğrulaması yapmak için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen bir kimlik App Service veya sanal makineye atanmalıdır. Daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, kimlik sağlamak için farklı seçeneklere sahip farklı ortamlarda aynı kodun kullanılmasına izin veren [' DefaultAzureCredential () '](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) sınıfını kullanıyor. Daha fazla bilgi için bkz. [varsayılan Azure kimlik bilgisi kimlik doğrulaması](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Bir anahtar oluşturma
Uygulamanızın kimliği doğrulandığına göre, yöntemi kullanarak anahtar kasanızda bir anahtar oluşturabilirsiniz `keyClient.createKey` . Bu, anahtar ve anahtar türü için bir ad gerektirir; "myKey" değerini değişkene atadık `keyName` ve bu örnekte BIR RSA kullanıyorsunuz `KeyType` .

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Anahtarın, [az keykasakey Show](/cli/azure/keyvault/key?#az_keyvault_key_show) komutuyla ayarlandığını doğrulayabilirsiniz:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Anahtar alma
Artık, daha önce oluşturulmuş anahtarı yöntemiyle elde edebilirsiniz `keyClient.getKey` .

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Artık alınan anahtarın ayrıntılarına, vb. gibi işlemlerle erişebilirsiniz `retrievedKey.getProperties` `retrievedKey.getKeyOperations` .

### <a name="delete-a-key"></a>Bir anahtarı silme
Son olarak, anahtar kasanızdan anahtarı yöntemiyle birlikte silelim `keyClient.beginDeleteKey` .

Anahtar silme işlemi uzun süren bir işlemdir. Bu, ilerleme durumunu yoklayabilmeniz veya işlemin tamamlanmasını beklemeniz için bekleyebilir.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Anahtarın, [az keykasakey Show](/cli/azure/keyvault/key?#az_keyvault_key_show) komutuyla silindiğini doğrulayabilirsiniz:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli değilse, anahtar Kasanızı ve ilgili kaynak grubunu kaldırmak için Azure CLı veya Azure PowerShell kullanabilirsiniz.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Örnek kod
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, bir anahtar oluşturdunuz, aldı ve sonra silmiş olursunuz. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu okuyun
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Anahtar kasasına erişimi güvenli hale getirme](../general/security-features.md)