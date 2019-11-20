---
title: Hızlı Başlangıç-düğüm Web uygulaması kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma | Microsoft Docs
description: Bu hızlı başlangıçta, düğüm Web uygulaması kullanarak Azure Key Vault bir gizli anahtar ve alma
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 02b9c439a932a4b35700871e68bdad7f03451110
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003498"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Hızlı Başlangıç: Düğüm Web uygulaması kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma 

Bu hızlı başlangıç, bir gizli dizi Azure Key Vault ve bir Web uygulaması kullanarak nasıl alınacağını gösterir. Key Vault kullanmak, bilgilerin güvenli kalmasına yardımcı olur. Gizli değeri görmek için bu hızlı başlangıcı Azure 'da çalıştırmanız gerekir. Bu hızlı başlangıçta, Azure kaynakları için yönetilen kimlikler ve Node.js kullanılmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

* Bir anahtar kasası oluşturma.
* Anahtar kasasına bir gizli dizi depolama.
* Anahtar kasasından bir gizli dizi alma.
* Azure web uygulaması oluşturma.
* Web uygulaması için [yönetilen kimliği](../active-directory/managed-service-identity/overview.md) etkinleştirin.
* Web uygulamasının anahtar kasasından verileri okuması için gereken izinleri verme.

Devam etmeden önce [Key Vault için temel kavramları](basic-concepts.md)öğrendiğinizden emin olun.

> [!NOTE]
> Key Vault, gizli dizilerin program aracılığıyla depolandığı merkezi bir depodur. Ancak bunun için uygulamaların ve kullanıcıların bir Key Vault'a kimlik doğrulaması yapması, yani gizli dizi sunması gerekir. En iyi güvenlik uygulamaları ile, bu ilk parolanın düzenli aralıklarla döndürülmesi gerekir. 
>
> Azure [kaynakları için yönetilen hizmet kimlikleri](../active-directory/managed-identities-azure-resources/overview.md)sayesinde Azure 'da çalışan uygulamalar, Azure 'un otomatik olarak yönettiği bir kimlik alır. Bu, *gizli dizi belirleme sorununu* çözerek kullanıcıların ve uygulamaların en iyi yöntemleri uygulayabilmesini ve ilk gizli diziyi döndürme konusunda endişelenmemesini sağlar.

## <a name="prerequisites"></a>Önkoşullar

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 veya üzeri. Bu hızlı başlangıç, Azure CLı 'yı yerel olarak çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. CLI’yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 2.0’ı yükleme](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure CLI'yi kullanarak Azure'da oturum açmak için, şunları girin:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az-group-create) komutunu kullanarak bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Kaynak grubu adı seçin ve yer tutucuyu doldurun.
Aşağıdaki örnek Doğu ABD konumunda bir kaynak grubu oluşturur.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Az önce oluşturduğunuz kaynak grubu bu makale boyunca kullanılır.

## <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Daha sonra, önceki adımda oluşturduğunuz kaynak grubunu kullanarak bir Anahtar Kasası oluşturursunuz. Bu makalede ad olarak "Contosokeykasası" kullanılsa da benzersiz bir ad kullanmanız gerekir. Şu bilgileri belirtin:

* Anahtar Kasası adı.
* Kaynak grubu adı. Ad 3-24 karakterden oluşan bir dize olmalıdır ve yalnızca 0-9, a-z, A-Z ve tire (-) içermelidir.
* Konumuna **Doğu ABD**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Bu noktada Azure hesabınız, bu yeni anahtar kasasında herhangi bir işlemi gerçekleştirmeye yetkili olan tek hesaptır.

## <a name="add-a-secret-to-the-key-vault"></a>Anahtar kasasına gizli dizi ekleme

Bunun nasıl çalıştığını göstermemize yardımcı olması için bir gizli dizi ekliyoruz. Güvenle korumanız ancak uygulamanıza da sağlamanız gereken bir SQL bağlantı dizesini veya başka bir bilgiyi depoluyor olabilirsiniz. Bu öğreticide, parola **AppSecret** olarak adlandırılır ve içinde **MySecret** değeri depolanır.

Anahtar kasasında **AppSecret** adlı bir gizli dizi oluşturmak için aşağıdaki komutları yazın. Bu gizli dizi **MySecret** değerini depolar.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Gizli dizi içindeki değeri düz metin olarak görüntülemek için:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Bu komut, URI dahil olmak üzere gizli dizi bilgilerini gösterir. Bu adımları tamamladıktan sonra, bir anahtar kasasında gizli dizi URI'sine sahip olmanız gerekir. Bu bilgileri not alın. Daha sonraki bir adımda gerekecektir.

## <a name="clone-the-repo"></a>Depoyu kopyalama

Kaynağı düzenleyebileceğiniz yerel bir kopya oluşturmak için depoyu kopyalayın. Şu komutu çalıştırın:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Bağımlılıkları yükleme

Bağımlılıkları yüklemek için aşağıdaki komutları çalıştırın:

```
cd key-vault-node-quickstart
npm install
```

Bu proje iki düğüm modülü kullanır: [MS-Rest-Azure](https://www.npmjs.com/package/ms-rest-azure) ve [Azure-keykasası](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Web uygulamasını Azure’da yayımlama

[Azure App Service](https://azure.microsoft.com/services/app-service/) planı oluşturun. Bu planda birden fazla web uygulaması depolayabilirsiniz.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Sonra, bir Web uygulaması oluşturun. Aşağıdaki örnekte, öğesini genel olarak `<app_name>` benzersiz bir uygulama adıyla değiştirin (geçerli karakterler a-z, 0-9 ve-). Çalışma zamanı NODE|6.9 olarak ayarlanmıştır. Desteklenen tüm çalışma zamanlarını görmek için öğesini `az webapp list-runtimes`çalıştırın.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Web uygulaması oluşturulduğunda Azure CLI aşağıda yer alan çıktıdaki gibi bilgiler gösterir:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Yeni oluşturulan Web uygulamanıza gidin ve çalıştığını görmeniz gerekir. Değiştirin `<app_name>` benzersiz bir uygulama adına sahip.

    ```
    http://<app name>.azurewebsites.net
    ```
Yukarıdaki komut ayrıca yerel git deponuzdan Azure 'a dağıtmanızı sağlayan git özellikli bir uygulama oluşturur. Yerel git deposu Şu URL ile yapılandırıldı: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`.

Yukarıdaki komutu tamamladıktan sonra yerel git deponuza bir Azure uzak ekleyebilirsiniz. Git `<url>` deposunun URL 'siyle değiştirin.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Web uygulaması için yönetilen kimliği etkinleştirme

Azure Key Vault kimlik bilgilerini ve diğer anahtarlarla gizli dizileri güvenle depolamak için bir yol sağlar, ama bunları alabilmek için kodunuzun Key Vault'ta kimlik doğrulaması yapması gerekir. [Azure kaynakları için yönetilen kimliklere genel bakış](../active-directory/managed-identities-azure-resources/overview.md), Azure hizmetlerine Azure Active Directory (Azure AD) üzerinde otomatik olarak yönetilen bir kimlik vererek bu soruna daha basit bir çözüm getirir. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri bulunmasına gerek kalmadan kimlik doğrulaması yapabilirsiniz.

Bu uygulamanın kimliğini oluşturmak için assign-identity komutunu çalıştırın:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Bu komut, portala gidip web uygulaması özelliklerinde **Kimlik/Sistem tarafından atanan** ayarını **Açık** duruma getirmekle eşdeğerdir.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Key Vault gizli dizilerini okumak için uygulamanıza izin atama

Önceki komutun çıkışını bir yere getirin. Şu biçimde olmalıdır:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ardından, anahtar kasanızın adını ve **PrincipalId**değerini kullanarak aşağıdaki komutu çalıştırın:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Düğüm uygulamasını Azure 'a dağıtma ve gizli değeri alma

Uygulamayı Azure 'a dağıtmak için aşağıdaki komutu çalıştırın:

```
git push azure master
```

Bundan sonra, öğesine `https://<app_name>.azurewebsites.net`gözattığınızda gizli değeri görebilirsiniz. Adı `<YourKeyVaultName>` kasa adınızla değiştirdiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup bir gizli dizi depoladınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](key-vault-overview.md) okuyun
- [Azure Key Vault geliştirici kılavuzuna](key-vault-developers-guide.md) bakın
- [Anahtarlar, gizli diziler ve sertifikalar](about-keys-secrets-and-certificates.md) hakkında bilgi edinin
- [En iyi uygulamaları](key-vault-best-practices.md) gözden geçirin Azure Key Vault