---
title: Self-hosting geliştirici portalı için alternatifler
titleSuffix: Azure API Management
description: Azure API Management 'de bir geliştirici portalını barındırdığınızda kullanabileceğiniz alternatif yaklaşımlar hakkında bilgi edinin.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741868"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Self-Host geliştirici portalına alternatif yaklaşımlar

[Geliştirici Portalını kendinden barındırırken](developer-portal-self-host.md)keşfedebileceğiniz birkaç alternatif yaklaşım vardır:

* Tasarımcı ve yayımcının üretim yapılarını kullanın.

* Portalınızı yayımlamak için bir Azure İşlev Uygulaması kullanın.

* Sayfa yükleme sürelerini azaltmak için Content Delivery Network (CDN) ile portalınızın dosyalarını önedin.

Bu makalede, bu yaklaşımların her biri hakkında bilgi verilmektedir. 

Daha önce yapmadıysanız, geliştirici portalının en son sürümü için [yerel bir ortam](developer-portal-self-host.md#step-1-set-up-local-environment) ayarlayın.

## <a name="build-for-production"></a>Üretim için derleme

Portalın geliştirme ortamını işbirliği amaçlarıyla barındırmak istiyorsanız, tasarımcı ve yayımcının üretim yapılarını kullanın. Üretim derlemeleri dosyaları paketleyin, kaynak haritalarını hariç tutar vb.

Şu `./dist/designer` komutu çalıştırarak dizinde bir paket oluşturun:

```sh
npm run build-designer
```

Sonuç tek sayfalı bir uygulamadır, bu sayede Azure Blob Storage statik Web sitesi gibi bir statik Web ana bilgisayarına dağıtabilirsiniz.

Benzer şekilde, derlenmiş ve iyileştirilmiş bir yayımcıyı klasöre yerleştirin `./dist/publisher` :

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Portalı yayımlamak için İşlev Uygulaması kullanma

Yayımlama adımını bulutta yerel olarak yürütmeye alternatif olarak çalıştırın.

Azure İşlev Uygulaması yayımlamayı uygulamak için aşağıdaki önkoşulların olması gerekir:

- [Azure Işlevi oluşturun](../azure-functions/functions-create-first-azure-function.md). Işlevin bir JavaScript Language Işlevi olması gerekir.
- Azure Functions Core Tools yüklensin:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Adım 1: çıktı depolamayı yapılandırma

İçerik, yerel bir klasör yerine barındırma web sitesine (çıkış depolamanın "$web" kapsayıcısına) doğrudan yükleniyor. Dosyada bu değişikliği yapılandırın `./src/config.publish.json` :

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>2. Adım: İşlev Uygulaması oluşturma ve dağıtma

Klasörde örnek bir HTTP tetikleyici Işlevi vardır `./examples` . Oluşturmak ve içine yerleştirmek için `./dist/function` aşağıdaki komutu çalıştırın:

```sh
npm run build-function
```

Sonra, Azure CLı 'de oturum açın ve dağıtın:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Dağıtıldıktan sonra bir HTTP çağrısıyla çağırabilirsiniz:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Barındırma ve CDN

[Kendi kendine barındırma bir geliştirici portalında](developer-portal-self-host.md) , Web sitenizi barındırmak Için bir Azure depolama hesabı kullanmanız önerilir. Ancak, dosyaları barındırma sağlayıcılarının hizmetleri de dahil olmak üzere herhangi bir çözümle yayımlayabilirsiniz.

Ayrıca, sayfa yükleme sürelerini azaltmak için dosyaları bir Content Delivery Network (CDN) ile de önyükleyebilirsiniz. [Azure CDN](https://azure.microsoft.com/services/cdn/)kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)
