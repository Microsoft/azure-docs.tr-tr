---
title: Hızlı başlangıç-JavaScript için Azure Key Vault anahtar istemci kitaplığı (sürüm 4)
description: JavaScript istemci kitaplığını kullanarak Azure Anahtar Kasası 'ndan anahtar oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 08de0045a19b0f07308778f8716fa48abded445b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815549"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Hızlı başlangıç: JavaScript için Azure Key Vault anahtar istemci kitaplığı (sürüm 4)

JavaScript için Azure Key Vault anahtar istemci kitaplığı ile çalışmaya başlayın. [Azure Key Vault](../general/overview.md) , şifreleme anahtarları için güvenli bir mağaza sağlayan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta, JavaScript anahtar istemci kitaplığını kullanarak Azure Anahtar Kasası 'ndan anahtar oluşturmayı, almayı ve silmeyi öğreneceksiniz

Key Vault istemci kitaplığı kaynakları:

[API başvuru belgeleri](/javascript/api/overview/azure/key-vault-index)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/keyvault-keys)

Key Vault ve anahtarları hakkında daha fazla bilgi için bkz.:
- [Key Vault genel bakış](../general/overview.md)
- [Anahtarlara genel bakış](about-keys.md).

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

```azurecli
mkdir key-vault-node-app
```

1. Yeni oluşturulan *Anahtar Kasası-node-App* dizinine geçin ve düğüm projesini başlatmak için ' init ' komutunu çalıştırın:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault paketlerini yükler

Konsol penceresinde, Node.js için Azure Key Vault [anahtarları kitaplığını](https://www.npmjs.com/package/@azure/keyvault-keys) yükler.

```azurecli
npm install @azure/keyvault-keys
```

Key Vault kimlik doğrulaması yapmak için [Azure. Identity](https://www.npmjs.com/package/@azure/identity) paketini yükler

```azurecli
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

Anahtar kasanız için Kullanıcı hesabınıza anahtar izinleri veren bir erişim ilkesi oluşturun

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Kod örnekleri

Aşağıdaki kod örnekleri, bir istemci oluşturma, anahtar ayarlama, anahtar alma ve bir anahtarı silme işlemlerinin nasıl yapılacağını gösterecektir. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) [' DefaultAzureCredential () '](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) sınıfını kullanarak kimlik sağlamak için farklı seçeneklerle aynı kodu farklı ortamlarda kullanmanıza olanak tanır. Anahtar Kasası kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Geliştirici Kılavuzu](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Aşağıdaki kodu ' Main () ' işlevine ekleyin

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Anahtar kaydetme

Uygulamanızın kimliği doğrulandığına göre, [CreateKey metodunu](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) kullanarak anahtar kasanıza bir anahtar koyabilirsiniz yöntemin parametreleri bir anahtar adı ve [anahtar türü](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype) kabul eder

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Anahtar alma

Artık, [GetKey yöntemiyle](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_)önceden ayarlanan değeri alabilirsiniz.

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Bir anahtarı silme

Son olarak, [Begindeletekey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) ve [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) yöntemleriyle anahtar kasaınızdan anahtarı silip temizlim.

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Örnek kod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Test ve doğrulama

Uygulamayı çalıştırmak için aşağıdaki komutları yürütün.

```azurecli
npm install
npm index.js
```

Aşağıdaki çıkışın bir varyasyonu görüntülenir:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, bir anahtar depo, ve bu anahtarı almıştır. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure Key Vault anahtarlarına genel bir bakış](about-keys.md) okuyun
- [Anahtar kasasına erişimi güvenli hale getirme](../general/security-features.md)
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
