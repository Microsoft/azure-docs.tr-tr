---
title: Hızlı başlangıç-Azure Key Vault JavaScript için sertifika istemci kitaplığı (sürüm 4)
description: JavaScript istemci kitaplığını kullanarak bir Azure anahtar kasasından sertifika oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3f933a564e62bf3aae1ec05c6dde048100c22967
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815855"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Hızlı başlangıç: JavaScript için Azure Key Vault sertifika istemci kitaplığı (sürüm 4)

JavaScript için Azure Key Vault sertifika istemci kitaplığı ile çalışmaya başlayın. [Azure Key Vault](../general/overview.md) , sertifikalar için güvenli bir mağaza sağlayan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta, JavaScript istemci kitaplığını kullanarak bir Azure anahtar kasasından sertifika oluşturmayı, almayı ve silmeyi öğreneceksiniz

Key Vault istemci kitaplığı kaynakları:

[API başvuru belgeleri](/javascript/api/overview/azure/key-vault-index)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Key Vault ve sertifikaları hakkında daha fazla bilgi için bkz.:
- [Key Vault genel bakış](../general/overview.md)
- [Sertifikalara genel bakış](about-certificates.md).

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

Konsol penceresinde, Node.js için Azure Key Vault [sertifikaları kitaplığını](https://www.npmjs.com/package/@azure/keyvault-certificates) yükler.

```azurecli
npm install @azure/keyvault-certificates
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

Anahtar kasanız için Kullanıcı hesabınıza sertifika izinleri veren bir erişim ilkesi oluşturun

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Kod örnekleri

Aşağıdaki kod örnekleri, bir istemci oluşturma, sertifika ayarlama, sertifika alma ve sertifikayı silme işlemlerinin nasıl yapılacağını gösterir. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) [' DefaultAzureCredential () '](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) sınıfını kullanarak kimlik sağlamak için farklı seçeneklerle aynı kodu farklı ortamlarda kullanmanıza olanak tanır. Anahtar Kasası kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Geliştirici Kılavuzu](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Aşağıdaki kodu ' Main () ' işlevine ekleyin

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Sertifika kaydetme

Uygulamanızın kimliği doğrulandığına göre, [begıntraıncertificate metodunu](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) kullanarak anahtar kasanıza bir sertifika koyabilirsiniz. Bu, sertifika için bir ad ve sertifika ilkesi [özelliklerine](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties) sahip sertifika ilkesi[sertifika](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) ilkesi gerektirir

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Sertifika adı varsa, yukarıdaki kod bu sertifikanın yeni bir sürümünü oluşturur.
### <a name="retrieve-a-certificate"></a>Sertifika alma

Artık [GetCertificate yöntemiyle](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption)önceden ayarlanan değeri alabilirsiniz.

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Sertifikayı silme

Son olarak, [beginDeleteCertificate] https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) ve [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) yöntemleriyle anahtar kasaınızdan sertifikayı silip temizlim.

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Örnek kod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Anahtar Kasası oluşturdunuz, bir sertifikayı depolı, ve bu sertifikayı almıştır. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Sertifikalara genel bakış](about-certificates.md) konusunu okuyun
- [App Service uygulama öğreticisindeki bir erişim Key Vault](../general/tutorial-net-create-vault-azure-web-app.md) görün
- [Sanal makine öğreticiden bir erişim Key Vault](../general/tutorial-net-virtual-machine.md) görün
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
