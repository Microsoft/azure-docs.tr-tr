---
title: Hızlı başlangıç-JavaScript için gizli istemci kitaplığı Azure Key Vault (sürüm 4)
description: JavaScript istemci kitaplığını kullanarak bir Azure Anahtar Kasası 'nda gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 90e505a4d505cc52920f1c7727afb321f4eda462
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814163"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Hızlı başlangıç: JavaScript için Azure Key Vault gizli istemci kitaplığı (sürüm 4)

JavaScript için Azure Key Vault gizli istemci kitaplığı ile çalışmaya başlayın. [Azure Key Vault](../general/overview.md) , gizli dizileri için güvenli bir mağaza sağlayan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta, JavaScript istemci kitaplığını kullanarak bir Azure Anahtar Kasası 'nda gizli dizileri oluşturmayı, almayı ve silmeyi öğreneceksiniz.

Key Vault istemci kitaplığı kaynakları:

[API başvuru belgeleri](/javascript/api/overview/azure/key-vault-index)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Key Vault ve gizli dizileri hakkında daha fazla bilgi için bkz.:
- [Key Vault genel bakış](../general/overview.md)
- [Gizli dizi genel bakış](about-secrets.md).

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- İşletim sisteminiz için geçerli [Node.js](https://nodejs.org) .
- [Azure CLI](/cli/azure/install-azure-cli)
- Key Vault- [Azure clı](../general/quick-create-cli.md) [Azure Portal](../general/quick-create-portal.md) veya [Azure PowerShell](../general/quick-create-powershell.md) kullanarak bir tane oluşturabilirsiniz.

Bu hızlı başlangıçta [Azure CLI](/cli/azure/install-azure-cli)çalıştırdığınız varsayılır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

1. `login` komutunu çalıştırın.

    ```azurecli-interactive
    az login
    ```

    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

    Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin.

2. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

## <a name="create-new-nodejs-application"></a>Yeni Node.js uygulaması oluştur

Ardından, buluta dağıtılabilecek bir Node.js uygulaması oluşturun. 

1. Bir komut kabuğunda adlı bir klasör oluşturun `key-vault-node-app` :

```terminal
mkdir key-vault-node-app
```

1. Yeni oluşturulan *Anahtar Kasası-node-App* dizinine geçin ve düğüm projesini başlatmak için ' init ' komutunu çalıştırın:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault paketlerini yükler

Konsol penceresinde, Node.js için Azure Key Vault gizli dizi [kitaplığı](https://www.npmjs.com/package/@azure/keyvault-secrets) ' nı yükler.

```terminal
npm install @azure/keyvault-secrets
```

Key Vault kimlik doğrulaması yapmak için [Azure. Identity](https://www.npmjs.com/package/@azure/identity) paketini yükler

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Bu uygulama, Anahtar Kasası adını adında bir ortam değişkeni olarak kullanıyor `KEY_VAULT_NAME` .

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

## <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin

Anahtar kasanız için Kullanıcı hesabınıza gizli izinler veren bir erişim ilkesi oluşturun

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Kod örnekleri

Aşağıdaki kod örnekleri, bir istemci oluşturma, gizli anahtar ayarlama, gizli anahtar alma ve gizli dizi silme işlemlerinin nasıl yapılacağını gösterir. 

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

1. Yeni metin dosyası oluştur ve ' index.js ' olarak kaydet

1. Azure ve Node.js modüllerini yüklemek için çağrı gerektir ekleme

1. Temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Yönergeler ekleme

Aşağıdaki yönergeleri kodunuzun en üstüne ekleyin:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) [' DefaultAzureCredential () '](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) sınıfını kullanarak kimlik sağlamak için farklı seçeneklerle farklı ortamlarda aynı kodu kullanmamızı sağlar. Key Vault kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Geliştirici Kılavuzu](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Aşağıdaki kodu ' Main () ' işlevine ekleyin

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Gizli dizi Kaydet

Uygulamanızın kimliği doğrulandığına göre, [setsecret metodunu](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_) kullanarak keykasanıza gizli anahtar koyabilirsiniz. Bu örnekte, gizli anahtar için bir ad gerekir. Bu örnekteki "MySecret" kullandık.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Gizli dizi alma

Artık [Getsecret yöntemiyle](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_)önceden ayarlanan değeri alabilirsiniz.

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Gizli anahtar artık olarak kaydedilir `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Gizli anahtarı silme

Son olarak, [Begindeletesecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) ve [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_) yöntemleriyle anahtar kasaınızdan parolayı silip temizlim.

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Örnek kod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Test ve doğrulama

1. Uygulamayı çalıştırmak için aşağıdaki komutları yürütün.

    ```terminal
    npm install
    node index.js
    ```

1. İstendiğinde, gizli bir değer girin. Örneğin, mySecretPassword.

    Aşağıdaki çıkışın bir varyasyonu görüntülenir:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, gizli dizi depolandı ve bu gizli dizi alındı. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure Key Vault gizliliklerin genel bakışını](about-secrets.md) okuyun
- [Anahtar kasasına erişimi güvenli hale getirme](../general/security-features.md)
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
