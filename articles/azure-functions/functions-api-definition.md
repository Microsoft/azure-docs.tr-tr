---
title: Azure Işlevlerinde Openapı meta verileri
description: Azure Işlevlerinde Openapı desteğine genel bakış
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000223"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Işlevlerinde Openapı 2,0 meta veri desteği (Önizleme)
Azure Işlevlerinde Openapı 2,0 (eski adıyla Swagger) meta veri desteği, bir işlev uygulamasının içine Openapı 2,0 tanımını yazmak için kullanabileceğiniz bir önizleme özelliğidir. Ardından, işlev uygulamasını kullanarak bu dosyayı barındırabilirsiniz.

> [!IMPORTANT]
> OpenAPI önizleme özelliği şu anda yalnızca 1.x çalışma zamanında kullanılabilir. 1.x işlev uygulaması oluşturma hakkında bilgi [burada bulunabilir](./functions-versions.md#creating-1x-apps).

[Openapı meta verileri](https://swagger.io/) , bir REST API barındıran bir işlevin çok çeşitli diğer yazılımlar tarafından tüketilebilmesi için izin verir. Bu yazılım, PowerApps gibi Microsoft tekliflerini ve [Azure App Service API Apps özelliğini](../app-service/overview.md), [Postman](https://www.getpostman.com/docs/importing_swagger)gibi üçüncü taraf geliştirici araçlarını ve [diğer birçok paketi](https://swagger.io/tools/)içerir.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Belirli özellikler hakkında daha fazla bilgi edinmek için [kullanmaya başlama öğreticisiyle](./functions-openapi-definition.md) başlayıp bu belgeye geri dönmenizi öneririz.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Openapı tanımı desteğini etkinleştir
İşlev uygulamanızın **platform özelliklerinin** **API tanımı** sayfasındaki tüm openapı ayarlarını yapılandırabilirsiniz.

> [!NOTE]
> İşlev API 'SI tanım özelliği şu anda beta çalışma zamanı için desteklenmiyor.

Barındırılan bir Openapı tanımının ve hızlı başlangıç tanımının oluşturulmasını etkinleştirmek için **API tanımı kaynağını** **Function (Önizleme)** olarak ayarlayın. **Dış URL** , işlevinizin başka bir yerde barındırılan bir openapı tanımı kullanmasına izin verir.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>İşlevinizin meta verilerinden bir Swagger iskelet oluşturun
Şablon, ilk Openapı tanımınızı yazmaya başlamanıza yardımcı olabilir. Tanım şablonu özelliği, HTTP tetikleyici işlevlerinizin her biri için dosyadaki function.jstüm meta verileri kullanarak bir seyrek Openapı tanımı oluşturur. İstek ve yanıt şablonları gibi [openapı BELIRTIMINDEN](https://swagger.io/specification/)API 'niz hakkında daha fazla bilgi doldurmanız gerekir.

Adım adım yönergeler için bkz. [Başlangıç Öğreticisi](./functions-openapi-definition.md).

### <a name="available-templates"></a><a name="templates"></a>Kullanılabilir şablonlar

|Ad| Açıklama |
|:-----|:-----|
|Oluşturulan tanım|İşlevin mevcut meta verilerinden çıkarsanoluşturulabilecek en fazla bilgi miktarına sahip bir Openapı tanımı.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Oluşturulan tanımda eklenen meta veriler

Aşağıdaki tablo, oluşturulan Swagger iskelet ile eşlendiği için function.jsAzure portal ayarlarını ve bunlara karşılık gelen verileri temsil eder.

|Üzerinde Swagger.js|Portal Kullanıcı arabirimi|Üzerinde Function.js|
|:----|:-----|:-----|
|[Konak](https://swagger.io/specification/#fixed-fields-15)|**İşlev uygulaması ayarları**  >  **App Service ayarları**  >  **Genel bakış**  >  **URL**|*Yok*
|[Yollar](https://swagger.io/specification/#paths-object-29)|**Tümleştirin**  >  **SEÇILI http metotları**|Bağlamalar: Route
|[Yol öğesi](https://swagger.io/specification/#path-item-object-32)|**Tümleştirin**  >  **Rota şablonu**|Bağlamalar: Yöntemler
|[Güvenlik](https://swagger.io/specification/#security-scheme-object-112)|**Anahtarlar**|*Yok*|
|operationId|**Rota + Izin verilen fiiller**|Rota + Izin verilen fiiller|

\*İşlem KIMLIĞI yalnızca PowerApps ve Flow ile tümleştirme için gereklidir.
> [!NOTE]
> X-MS-Summary uzantısı, Logic Apps, PowerApps ve Flow 'da bir görünen ad sağlar.
>
> Daha fazla bilgi için bkz. [PowerApps Için Swagger tanımınızı özelleştirme](/connectors/custom-connectors/openapi-extensions).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Bir API tanımı ayarlamak için CI/CD kullanma

 Kaynak denetiminden API tanımınızı değiştirmek için kaynak denetimini etkinleştirmeden önce portalda API tanımı barındırmayı etkinleştirmeniz gerekir. Şu yönergeleri izleyin:

1. İşlev uygulaması ayarlarınızda **API tanımına (Önizleme)** gidin.
   1. **API tanımı kaynağını** **işlev** olarak ayarlayın.
   1. **API tanımı şablonu oluştur** ' a tıklayın ve ardından daha sonra değiştirmek üzere bir şablon tanımı oluşturmak için **kaydedin** .
   1. API tanımı URL 'nizi ve anahtarınızı aklınızda edin.
1. [Sürekli tümleştirme/sürekli dağıtım (CI/CD) ayarlayın](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. \Site\wwwrootazurefunctions\swagger\swagger.jskonumundaki kaynak denetimindeki swagger.jsdeğiştirin \. .

Şimdi, deponuzdaki swagger.jsdeğişiklikler, API tanım URL 'sindeki ve adım 1. c ' de not ettiğiniz anahtar üzerinde işlev uygulamanız tarafından barındırılır.

## <a name="next-steps"></a>Sonraki adımlar
* [Kullanmaya başlama öğreticisi](./functions-openapi-definition.md). Bir Openapı tanımını eylemde görmek için kılavuzumuzu deneyin.
* [Azure Işlevleri GitHub deposu](https://github.com/Azure/Azure-Functions/). API tanımı desteği önizlemesi hakkında geri bildirimde bulunmak için Işlevler deposuna göz atın. Güncelleştirilmesini istediğiniz her şey için bir GitHub sorunu oluşturun.
* [Azure işlevleri geliştirici başvurusu](functions-reference.md). Kodlama işlevleri ve Tetikleyicileri ve bağlamaları tanımlama hakkında bilgi edinin.
