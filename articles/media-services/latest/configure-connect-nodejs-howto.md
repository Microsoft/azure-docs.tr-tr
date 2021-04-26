---
title: Azure Media Services v3 API 'sine bağlanma-Node.js
description: Bu makalede, Node.js ile Media Services v3 API 'sine nasıl bağlanacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: fcb9fd9f0539b42d9253db783fd5da840f358e66
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960733"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API 'sine bağlanma-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede hizmet sorumlusu oturum açma yöntemi kullanılarak Azure Media Services v3 node.js SDK 'sına nasıl bağlanabilmeniz gösterilmektedir. *Media-Services-v3-node-öğreticiler* örnek deposundaki dosyalarla çalışacaksınız. *HelloWorld-ListAssets* örneği, bağlanmak için kodu içerir ve ardından hesaptaki varlıkları listeler.

## <a name="prerequisites"></a>Önkoşullar

- Visual Studio Code yüklemesi.
- [Node.js](https://nodejs.org/en/download/)'i yükler.
- [TypeScript](https://www.typescriptlang.org/download)'i yükler.
- [Media Services hesabı oluşturun](./account-create-how-to.md). Kaynak grubu adını ve Media Services hesap adını unutduğunuzdan emin olun.
- Uygulamanız için bir hizmet sorumlusu oluşturun. Bkz. [erişim API 'leri](./access-api-howto.md).<br/>**Pro İpucu!** Bu pencereyi açık tutun veya JSON sekmesindeki her şeyi not defteri 'ne kopyalayın. 
- [JavaScript Için Azudüzeltici Aservices SDK 'sının](https://www.npmjs.com/package/@azure/arm-mediaservices)en son sürümünü aldığınızdan emin olun.

> [!IMPORTANT]
> Varlıklarda önemli adlandırma kısıtlamalarını anlamak için Azure Media Services [adlandırma kurallarını](media-services-apis-overview.md#naming-conventions) gözden geçirin.

## <a name="clone-the-nodejs-samples-repo"></a>Node.JS örnekleri deposunu kopyalayın

Azure örnekleri 'nde bazı dosyalarla çalışacaksınız. Node.JS örnekleri deposunu kopyalayın.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Paketleri yükler

### <a name="install-azurearm-mediaservices"></a>Yükleyeceğiniz @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Yükleyeceğiniz @azure/ms-rest-nodeauth

Lütfen en düşük sürüm olan " @azure/ms-rest-nodeauth ": "^ 3.0.0" sürümünü yüklemelisiniz.

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

Bu örnekte, dosyasında aşağıdaki paketleri kullanacaksınız `package.json` .

|Paket|Description|
|---|---|
|`@azure/arm-mediaservices`|SDK Azure Media Services. <br/>En son Azure Media Services paketini kullandığınızdan emin olmak için [NPM yüklemesini @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices)denetleyin.|
|`@azure/ms-rest-nodeauth` | Hizmet sorumlusu veya yönetilen kimlik kullanılarak AAD kimlik doğrulaması için gereklidir|
|`@azure/storage-blob`|Depolama SDK 'Sı. Dosyalar varlıklara yüklenirken kullanılır.|
|`@azure/ms-rest-js`| Oturum açmak için kullanılır.|
|`@azure/storage-blob` | Kodlama için Azure Media Services içindeki varlıklara dosya yüklemek ve bunları indirmek için kullanılır.|
|`@azure/abort-controller`| Uzun süre çalışan indirme işlemleri için depolama istemcisiyle birlikte kullanılır|

### <a name="create-the-packagejson-file"></a>Dosyada package.jsoluşturma

1. En `package.json` sevdiğiniz düzenleyiciyi kullanarak bir dosya oluşturun.
1. Dosyayı açın ve aşağıdaki kodu yapıştırın:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>TypeScript kullanarak Node.js istemciye bağlanma



### <a name="sample-env-file"></a>Sample *. env* dosyası

Bu dosyanın içeriğini *. env* adlı bir dosyaya kopyalayın. Çalışma deponuzun kökünde depolanmalıdır. Bunlar, portalda Media Services hesabınızın API erişimi sayfasından aldığınız değerlerdir.

*. Env* dosyasını oluşturduktan sonra örneklerle çalışmaya başlayabilirsiniz.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Örnek uygulama *HelloWorld-ListAssets* ' i çalıştırın

1. Dizini *AMSv3Samples* klasörüne değiştirme

```bash
cd AMSv3Samples
```

2. *packages.jsdosya üzerinde* kullanılan paketleri yükler.

```
npm install 
```

3. Dizini *HelloWorld-ListAssets* klasörü olarak değiştirin.

```bash
cd HelloWorld-ListAssets
```

4. AMSv3Samples klasöründen Visual Studio Code başlatın. Bu, ". vscode" klasörünün ve dosyalardaki tsconfig.jsbulunduğu klasörden başlatmak için gereklidir

```dotnetcli
cd ..
code .
```

*HelloWorld-ListAssets* klasörünü açın ve *index. TS* dosyasını Visual Studio Code düzenleyicisinde açın.

*İndex. TS* dosyasında, hata ayıklayıcıyı başlatmak için F5 tuşuna basın. Hesapta zaten bir varlık varsa, görünen varlıkların bir listesini görmeniz gerekir. Hesap boşsa boş bir liste görürsünüz.  

Listelenen varlıkları hızlıca görmek için Portal 'ı kullanarak birkaç video dosyasını karşıya yükleyin. Varlıklar her biri otomatik olarak oluşturulur ve bu betiği yeniden çalıştırmak daha sonra adlarını döndürür.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>*HelloWorld-ListAssets* örneğine daha yakından bakış

*HelloWorld-ListAssets* örneği, Media Services Istemcisine bir hizmet sorumlusu ile nasıl bağlanabileceklerini ve hesaptaki varlıkları listelemenizi gösterir. Ne işe yönelik ayrıntılı bir açıklama için koddaki açıklamalara bakın.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Daha fazla örnek

[Depoda](https://github.com/Azure-Samples/media-services-v3-node-tutorials) aşağıdaki örnekler mevcuttur

|Proje adı|Kullanım Örneği|
|---|---|
|Canlı/dizin. TS| Temel canlı akış örneği. **Uyarı**, canlı kullanırken tüm kaynakların temizlenip artık portalda faturalandırılırsınız emin olun|
|Streamfilesörnek/dizin. TS| Kaynak URL 'den yerel bir dosya veya kodlama yüklemeye yönelik temel örnek. Örnek, içerik indirmek için depolama SDK 'sının nasıl kullanılacağını gösterir ve bir oyuncunun nasıl akışa alınacağını gösterir |
|StreamFilesWithDRMSample/index. TS| Widevine ve PlayReady DRM kullanarak kodlama ve akışın nasıl yapılacağını gösterir |
|VideoIndexerSample/index. TS| Video ve ses çözümleyici ön ayarlarını kullanarak video veya ses dosyasından meta veriler ve Öngörüler oluşturma örneği |

## <a name="see-also"></a>Ayrıca bkz.

- [Node.jsiçin Azure Media Services modülleriyle ilgili başvuru belgeleri ](/javascript/api/overview/azure/media-services)
- [JavaScript & için Azure Node.js geliştiriciler](/azure/developer/javascript/)
- [Git hub deposunda Media Services kaynak kodu @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js geliştiricileri için Azure paket belgeleri](/javascript/api/overview/azure/)
- [Media Services kavramlar](concepts-overview.md)
- [NPM yüklemesi @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [JavaScript & için Azure Node.js geliştiriciler](/azure/developer/javascript/)
- [Depodaki Media Services kaynak kodu @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Sonraki adımlar

Media Services [Node.js başvuru](/javascript/api/overview/azure/mediaservices/management) belgelerini inceleyin ve node.js ile Media Services API 'sinin nasıl kullanılacağını gösteren [örneklere](https://github.com/Azure-Samples/media-services-v3-node-tutorials) göz atın.
