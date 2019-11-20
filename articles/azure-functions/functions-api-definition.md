---
title: Azure Işlevlerinde Openapı meta verileri | Microsoft Docs
description: Azure Işlevlerinde Openapı desteğine genel bakış
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 00dedd069914384a1883a54bfb433857286fb49c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097570"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Işlevlerinde Openapı 2,0 meta veri desteği (Önizleme)
Azure Işlevlerinde Openapı 2,0 (eski adıyla Swagger) meta veri desteği, bir işlev uygulamasının içine Openapı 2,0 tanımını yazmak için kullanabileceğiniz bir önizleme özelliğidir. Ardından, işlev uygulamasını kullanarak bu dosyayı barındırabilirsiniz.

> [!IMPORTANT]
> OpenAPI önizleme özelliği şu anda yalnızca 1.x çalışma zamanında kullanılabilir. 1\.x işlev uygulaması oluşturma hakkında bilgi [burada bulunabilir](./functions-versions.md#creating-1x-apps).

[Openapı meta verileri](https://swagger.io/) , bir REST API barındıran bir işlevin çok çeşitli diğer yazılımlar tarafından tüketilebilmesi için izin verir. Bu yazılım, PowerApps gibi Microsoft tekliflerini ve [Azure App Service API Apps özelliğini](../app-service/overview.md), [Postman](https://www.getpostman.com/docs/importing_swagger)gibi üçüncü taraf geliştirici araçlarını ve [diğer birçok paketi](https://swagger.io/tools/)içerir.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Belirli özellikler hakkında daha fazla bilgi edinmek için [kullanmaya](./functions-api-definition-getting-started.md) başlama öğreticisiyle başlayıp bu belgeye geri dönmenizi öneririz.

## <a name="enable"></a>Openapı tanımı desteğini etkinleştir
İşlev uygulamanızın **platform özelliklerinin** **API tanımı** sayfasındaki tüm openapı ayarlarını yapılandırabilirsiniz.

> [!NOTE]
> İşlev API 'SI tanım özelliği şu anda beta çalışma zamanı için desteklenmiyor.

Barındırılan bir Openapı tanımının ve hızlı başlangıç tanımının oluşturulmasını etkinleştirmek için **API tanımı kaynağını** **Function (Önizleme)** olarak ayarlayın. **Dış URL** , işlevinizin başka bir yerde barındırılan bir openapı tanımı kullanmasına izin verir.

## <a name="generate-definition"></a>İşlevinizin meta verilerinden bir Swagger iskelet oluşturun
Şablon, ilk Openapı tanımınızı yazmaya başlamanıza yardımcı olabilir. Tanım şablonu özelliği, HTTP tetikleyici işlevlerinizin her biri için function. JSON dosyasındaki tüm meta verileri kullanarak bir seyrek Openapı tanımı oluşturur. İstek ve yanıt şablonları gibi [openapı BELIRTIMINDEN](https://swagger.io/specification/)API 'niz hakkında daha fazla bilgi doldurmanız gerekir.

Adım adım yönergeler için bkz. [Başlangıç Öğreticisi](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Kullanılabilir şablonlar

|Name| Açıklama |
|:-----|:-----|
|Oluşturulan tanım|İşlevin mevcut meta verilerinden çıkarsanoluşturulabilecek en fazla bilgi miktarına sahip bir Openapı tanımı.|

### <a name="quickstart-details"></a>Oluşturulan tanımda eklenen meta veriler

Aşağıdaki tabloda, üretilen Swagger iskelet 'e eşlendiğinden, function. JSON dosyasındaki Azure portal ayarları ve karşılık gelen veriler temsil edilir.

|Swagger.json|Portal Kullanıcı arabirimi|Function. JSON|
|:----|:-----|:-----|
|[Ana Bilgisayar](https://swagger.io/specification/#fixed-fields-15)|**İşlev uygulaması ayarları** > **App Service ayarlara** > **genel bakış** > **URL 'si**|*Yok*
|[Yollar](https://swagger.io/specification/#paths-object-29)| > **Seçili http yöntemlerini** tümleştirin|Lara Rota
|[Yol öğesi](https://swagger.io/specification/#path-item-object-32)| > **Rota şablonunu** tümleştirin|Lara Yöntemler
|[Güvenlik](https://swagger.io/specification/#security-scheme-object-112)|**Belirlenmesine**|*Yok*|
|operationId|**Rota + Izin verilen fiiller**|Rota + Izin verilen fiiller|

\*İşlem KIMLIĞI yalnızca PowerApps ve Flow ile tümleştirme için gereklidir.
> [!NOTE]
> X-MS-Summary uzantısı, Logic Apps, PowerApps ve Flow 'da bir görünen ad sağlar.
>
> Daha fazla bilgi için bkz. [PowerApps Için Swagger tanımınızı özelleştirme](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Bir API tanımı ayarlamak için CI/CD kullanma

 Kaynak denetiminden API tanımınızı değiştirmek için kaynak denetimini etkinleştirmeden önce portalda API tanımı barındırmayı etkinleştirmeniz gerekir. Aşağıdaki yönergeleri izleyin:

1. İşlev uygulaması ayarlarınızda **API tanımına (Önizleme)** gidin.
   1. **API tanımı kaynağını** **işlev**olarak ayarlayın.
   1. **API tanımı şablonu oluştur** ' a tıklayın ve ardından daha sonra değiştirmek üzere bir şablon tanımı oluşturmak için **kaydedin** .
   1. API tanımı URL 'nizi ve anahtarınızı aklınızda edin.
1. [Sürekli Tümleştirme/sürekli dağıtım (CI/CD) ayarlayın](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. \Site\wwwroot\.azurefunctions\swagger\swagger.JSON. konumundaki kaynak denetiminde Swagger. json dosyasını değiştirme

Şimdi, deponuzdaki Swagger. json dosyasında yapılan değişiklikler, API tanım URL 'sindeki ve adım 1. c ' de not ettiğiniz anahtar üzerinde işlev uygulamanız tarafından barındırılır.

## <a name="next-steps"></a>Sonraki adımlar
* [Kullanmaya başlama öğreticisi](functions-api-definition-getting-started.md). Bir Openapı tanımını eylemde görmek için kılavuzumuzu deneyin.
* [Azure Işlevleri GitHub deposu](https://github.com/Azure/Azure-Functions/). API tanımı desteği önizlemesi hakkında geri bildirimde bulunmak için Işlevler deposuna göz atın. Güncelleştirilmesini istediğiniz her şey için bir GitHub sorunu oluşturun.
* [Azure işlevleri geliştirici başvurusu](functions-reference.md). Kodlama işlevleri ve Tetikleyicileri ve bağlamaları tanımlama hakkında bilgi edinin.
