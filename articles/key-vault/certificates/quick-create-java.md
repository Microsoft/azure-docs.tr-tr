---
title: Azure Key Vault sertifikası istemci kitaplığı için hızlı başlangıç-Java
description: Bu hızlı başlangıçtaki adımlarla Java için Azure Key Vault sertifika istemci kitaplığı hakkında bilgi edinin.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 47b15c857f6f0c8c4d8f377ee4ecdcca74e88e0f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818582"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Hızlı başlangıç: Java için Azure Key Vault sertifika istemci kitaplığı (Sertifikalar)
Java için Azure Key Vault sertifika istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin.

Ek kaynaklar:

* [Kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [API başvuru belgeleri](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Ürün belgeleri](index.yml)
* [Örnekler](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

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
Konsol penceresinde, `mvn` adıyla yeni bir Java konsol uygulaması oluşturmak için komutunu kullanın `akv-certificates-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Projenin üretilme çıktısı şuna benzer şekilde görünür:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Dizininizi yeni oluşturulan `akv-certificates-java/` klasöre değiştirin.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>Paketi yükler
*pom.xml* dosyasını metin düzenleyicinizde açın. Aşağıdaki bağımlılık öğelerini bağımlılıklar grubuna ekleyin.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
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
Anahtar kasanız için Kullanıcı hesabınıza sertifika izinleri veren bir erişim ilkesi oluşturun.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
Java için Azure Key Vault sertifikası istemci kitaplığı, sertifikaları yönetmenizi sağlar. [Kod örnekleri](#code-examples) bölümünde, bir istemci oluşturma, sertifika oluşturma, sertifika alma ve sertifikayı silme işlemlerinin nasıl yapılacağı gösterilir.

Konsol uygulamasının tamamı [aşağıda](#sample-code)verilmiştir.

## <a name="code-examples"></a>Kod örnekleri
### <a name="add-directives"></a>Yönergeler ekleme
Aşağıdaki yönergeleri kodunuzun en üstüne ekleyin:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma
Bu hızlı başlangıçta, oturum açan bir Kullanıcı, yerel geliştirme için tercih edilen yöntem olan Key Vault kimlik doğrulaması yapmak için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen bir kimlik App Service veya sanal makineye atanmalıdır. Daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, kimlik sağlamak için farklı seçeneklere sahip farklı ortamlarda aynı kodun kullanılmasına izin veren [' DefaultAzureCredential () '](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) sınıfını kullanıyor. Daha fazla bilgi için bkz. [varsayılan Azure kimlik bilgisi kimlik doğrulaması](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Gizli dizi Kaydet
Uygulamanızın kimliği doğrulandığına göre, yöntemi kullanarak anahtar kasanızda bir sertifika oluşturabilirsiniz `certificateClient.beginCreateCertificate` . Bu, sertifika ve sertifika ilkesi için bir ad gerektirir; "myCertificate" değerini `certificateName` Bu örnekteki değişkene atadık ve varsayılan bir ilke kullanacak.

Sertifika oluşturma uzun süren bir işlemdir. Bu, ilerleme durumunu yoklayabilmeniz veya tamamlanmasını beklemeniz için bekleyebilir.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Oluşturma işlemi aşağıdaki çağrı aracılığıyla tamamlandığında sertifikayı elde edebilirsiniz:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Sertifika alma
Artık, yöntemi ile önceden oluşturulan sertifikayı alabilirsiniz `certificateClient.getCertificate` .

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Artık alınan sertifikanın ayrıntılarına, vb. gibi işlemler de erişebilirsiniz `retrievedCertificate.getName` `retrievedCertificate.getProperties` . Ya da içeriği `retrievedCertificate.getCer` .

### <a name="delete-a-certificate"></a>Sertifikayı silme
Son olarak, anahtar kasanızdan sertifikayı, `certificateClient.beginDeleteCertificate` Ayrıca uzun süren bir işlem olan yöntemiyle silerdelim.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, bir sertifika oluşturdunuz, alındı ve sonra silmiş olursunuz. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Anahtar kasasına erişimi güvenli hale getirme](../general/security-features.md)