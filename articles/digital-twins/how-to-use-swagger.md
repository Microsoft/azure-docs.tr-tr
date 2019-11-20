---
title: Başvuru Swagger belgelerini kullanma-Azure dijital TWINS | Microsoft Docs
description: Azure Digital TWINS Swagger başvuru belgelerinin nasıl kullanılacağını anlama.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: ccea63e8edee739ce6743d7638b4e5300ad07f8f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009892"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure dijital TWINS Swagger başvuru belgeleri

Sağlanan her Azure dijital TWINS örneği, kendi otomatik olarak oluşturulan Swagger başvuru belgelerini içerir.

[Swagger](https://swagger.io/)veya [openapı](https://www.openapis.org/), karmaşık API bilgilerini etkileşimli ve dilden bağımsız bir başvuru kaynağına ayırır. Swagger, bir API 'ye karşı işlem gerçekleştirmek için kullanılacak olan JSON yükleri, HTTP yöntemleri ve belirli uç noktalar hakkında kritik başvuru malzemeleri sağlar.

## <a name="swagger-summary"></a>Swagger Özeti

Swagger, API 'nizin etkileşimli bir özetini sağlar ve şunları içerir:

* API ve nesne modeli bilgileri.
* Gerekli istek yüklerini, üstbilgileri, parametreleri, bağlam yollarını ve HTTP yöntemlerini belirten uç noktalar REST API.
* API işlevlerini test etme.
* HTTP yanıtlarını doğrulamak ve doğrulamak için kullanılan örnek yanıt bilgileri.
* Hata kodu bilgileri.

Swagger, Azure Digital TWINS yönetim API 'Lerine yapılan geliştirme ve test çağrılarına yardımcı olan kullanışlı bir araçtır.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Başvuru kaynakları

Otomatik olarak oluşturulan Swagger başvuru malzemeleri, önemli kavramlara, kullanılabilir yönetim API uç noktalarına ve geliştirme ve test etmeye yardımcı olmak için her nesne modelinin açıklamasına yönelik hızlı bir genel bakış sağlar.

Kısa bir Özet API 'YI tanımlar.

[![Swagger üst](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Yönetim API 'SI nesne modelleri de listelenir.

[Swagger modellerini ![](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Anahtar özniteliklerinin daha ayrıntılı bir özeti için listelenen her nesne modelini seçebilirsiniz.

[![Swagger modeli](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Oluşturulan Swagger nesne modelleri, tüm kullanılabilir Azure dijital TWINS [nesnelerini ve API 'leri](./concepts-objectmodel-spatialgraph.md)görmek için uygundur. Geliştiriciler, Azure dijital TWINS üzerinde çözümler oluştururken bu kaynağı kullanabilir.

## <a name="endpoint-summary"></a>Uç nokta Özeti

Swagger ayrıca yönetim API 'Lerini oluşturan tüm uç noktalara kapsamlı bir genel bakış sağlar.

Listelenen her uç nokta, aşağıdakiler gibi gerekli istek bilgilerini de içerir:

* Gerekli parametreler.
* Gerekli parametre veri türleri.
* Kaynağa erişmek için HTTP yöntemi.

[![Swagger uç noktaları](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Daha ayrıntılı bir genel bakış görmek için her bir kaynağı seçin.

## <a name="use-swagger-to-test-endpoints"></a>Uç noktaları test etmek için Swagger kullanın

Swagger, bir API uç noktasını doğrudan belgeler kullanıcı arabiriminden test edebilmesidir.

Belirli bir uç noktayı seçtikten sonra, **deneyin**seçeneğini görürsünüz.

[![Swagger TRY](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Gerekli ve isteğe bağlı her bir parametre için giriş alanlarını getirmek üzere bu bölümü genişletin. Doğru değerleri girip **Yürüt**' ü seçin.

[![Swagger çalıştı](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Testi yürütmeden sonra, yanıt verilerini doğrulayabilirsiniz.

## <a name="swagger-response-data"></a>Swagger yanıt verileri

Listelenen her uç nokta, geliştirme ve testlerinizi doğrulamak için yanıt gövdesi verilerini de içerir. Bu örnekler, başarılı HTTP istekleri için görmek istediğiniz durum kodlarını ve JSON 'yi içerir.

[![Swagger yanıtı](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Örnekler Ayrıca hata ayıklama veya başarısız testleri geliştirmek için hata kodlarını içerir.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2,0 yetkilendirmesi

> [!NOTE]
> * Azure dijital TWINS kaynağını oluşturan kullanıcı sorumlusunun bir alan Yöneticisi rol ataması olacak ve diğer kullanıcılar için ek rol atamaları oluşturabileceksiniz. Bu tür kullanıcılar ve rollerinin API 'Leri çağırma yetkisi bulunabilir.

1. Bir Azure AD uygulaması oluşturmak ve yapılandırmak için [Bu hızlı](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) başlangıçta bulunan adımları izleyin. Alternatif olarak, var olan bir uygulama kaydını yeniden kullanabilirsiniz.

1. Aşağıdaki yanıt URL 'sini uygulama kaydına ekleyin:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Ad  | Şununla değiştir | Örnek |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Portalda yönetim REST API belge URL 'SI bulundu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Azure AD uygulamanızın KIMLIĞINI kopyalayın.

Azure Active Directory kaydını tamamladıktan sonra:

1. Swagger sayfanızda **Yetkilendir** düğmesini seçin.

    [![Swagger Yetkilendir düğmesini seçin](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Uygulama KIMLIĞINI **client_id** alanına yapıştırın.

    [![Swagger client_id alanı](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Ardından, aşağıdaki başarılı kalıcı öğesine yönlendirilirsiniz.

    [![Swagger yeniden yönlendirme kalıcı](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

OAuth 2,0 tarafından korunan etkileşimli test istekleri hakkında daha fazla bilgi edinmek için [resmi belgelere](https://swagger.io/docs/specification/authentication/oauth2/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS nesne modelleri ve uzamsal zeka grafiği hakkında daha fazla bilgi edinmek için bkz. [Azure Digital TWINS nesne modellerini anlama](./concepts-objectmodel-spatialgraph.md).

- Yönetim API 'niz ile kimlik doğrulaması yapmayı öğrenmek için, [API 'Lerle kimlik doğrulamasını](./security-authenticating-apis.md)okuyun.