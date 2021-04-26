---
title: Microsoft Azure haritaları ile kimlik doğrulama
titleSuffix: Azure Maps
description: "Azure haritalar 'da isteklerin kimlik doğrulamasının iki yolu hakkında bilgi edinin: paylaşılan anahtar kimlik doğrulaması ve Azure Active Directory (Azure AD) kimlik doğrulaması."
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094083"
---
# <a name="authentication-with-azure-maps"></a>Azure Haritalar ile kimlik doğrulaması

Azure haritalar isteklerin kimliğini doğrulamak için iki yolu destekler: paylaşılan anahtar kimlik doğrulaması ve [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) kimlik doğrulaması. Bu makalede, Azure haritalar Hizmetleri uygulamanıza kılavuzluk eden kimlik doğrulama yöntemlerinin her ikisi de açıklanmaktadır.

> [!NOTE]
> Azure haritalar ile güvenli iletişim geliştirmek için artık Aktarım Katmanı Güvenliği (TLS) 1,2 ' i destekliyoruz ve TLS 1,0 ve 1,1 desteğini devre dışı sunuyoruz. Şu anda TLS 1. x kullanıyorsanız, TLS 1,2 hazırlığınızı değerlendirin ve [tls 1,0 sorununun çözümünde](/security/solving-tls1-problem)açıklanan sınamayla bir geçiş planı geliştirin.

## <a name="shared-key-authentication"></a>Paylaşılan anahtar kimlik doğrulaması

 Birincil ve ikincil anahtarlar, Azure Maps hesabı oluşturulduktan sonra oluşturulur. Azure haritalar 'ı paylaşılan anahtar kimlik doğrulamasıyla çağırırken abonelik anahtarı olarak birincil anahtarı kullanmanız önerilir. Paylaşılan anahtar kimlik doğrulaması, bir Azure Maps hesabı tarafından oluşturulan bir anahtarı Azure Maps hizmetine geçirir. Azure haritalar hizmetlerine yönelik her istek için, URL 'ye bir parametre olarak *abonelik anahtarını* ekleyin. İkincil anahtar, kayan anahtar değişiklikleri gibi senaryolarda kullanılabilir.  

Azure portal anahtarlarınızı görüntüleme hakkında daha fazla bilgi için bkz. [kimlik doğrulamasını yönetme](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Güvenlik nedeniyle, birincil ve ikincil anahtarlarınız arasında döndürme yapmanız önerilir. Anahtarları döndürmek için uygulamanızı ikincil anahtarı kullanacak şekilde güncelleştirin, dağıtın ve ardından birincil anahtarın yanındaki döngüye/Yenile düğmesine basarak yeni bir birincil anahtar oluşturun. Eski birincil anahtar devre dışı bırakılacak. Anahtar döndürme hakkında daha fazla bilgi için bkz. [anahtar döndürme ve denetleme ile Azure Key Vault ayarlama](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Azure AD kimlik doğrulaması

Ayrıntılı erişim denetimi sağlamak için Azure abonelikleri bir Azure AD kiracısı ile sağlanır. Azure haritalar, Azure AD 'yi kullanarak Azure Maps Hizmetleri için kimlik doğrulaması sağlar. Azure AD, Azure AD kiracısında kayıtlı kullanıcılar ve uygulamalar için kimlik tabanlı kimlik doğrulaması sağlar.

Azure Maps, Azure Maps hesabı içeren bir Azure aboneliğiyle ilişkili Azure AD kiracılar için **OAuth 2,0** erişim belirteçleri kabul eder. Azure haritalar için belirteçleri de kabul eder:

* Azure AD kullanıcıları
* Kullanıcılar tarafından yetkilendirilen izinleri kullanan iş ortağı uygulamaları
* Azure kaynakları için yönetilen kimlikler

Azure Maps, her Azure Maps hesabı için *benzersiz bir tanımlayıcı (ISTEMCI kimliği)* oluşturur. Bu istemci KIMLIĞINI ek parametrelerle birleştirdiğinizde Azure AD 'den belirteç isteğinde bulunabilir.

Azure haritalar için Azure AD ve istek belirteçlerini yapılandırma hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md).

Azure AD ile kimlik doğrulama hakkında genel bilgi için bkz. [kimlik doğrulaması nedir?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure kaynakları ve Azure Maps için Yönetilen kimlikler

Azure [kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , Azure AD ile kimlik doğrulaması yapılabilecek otomatik olarak yönetilen uygulama tabanlı güvenlik sorumlusu ile Azure hizmetleri sağlar. Azure rol tabanlı erişim denetimi (Azure RBAC) sayesinde, yönetilen kimlik güvenlik sorumlusunun Azure haritalar hizmetlerine erişme yetkisi bulunabilir. Yönetilen kimliklerin bazı örnekleri şunlardır: Azure App Service, Azure Işlevleri ve Azure sanal makineleri. Yönetilen kimliklerin listesi için bkz. [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Uygulama Azure AD kimlik doğrulamasını yapılandırma

Uygulamalar, Azure AD tarafından desteklenen bir veya daha fazla senaryoyu kullanarak Azure AD kiracısıyla kimlik doğrulaması yapacak. Her Azure AD uygulama senaryosu, iş gereksinimlerine göre farklı gereksinimleri temsil eder. Bazı uygulamalar, Kullanıcı oturum açma deneyimleri gerektirebilir ve diğer uygulamalar da bir uygulama oturum açma deneyimi gerektirebilir. Daha fazla bilgi için bkz. [kimlik doğrulama akışları ve uygulama senaryoları](../active-directory/develop/authentication-flows-app-scenarios.md).

Uygulama bir erişim belirteci aldıktan sonra, SDK ve/veya uygulama diğer REST API HTTP üst bilgilerine ek olarak aşağıdaki gerekli HTTP üstbilgileri kümesiyle bir HTTPS isteği gönderir:

| Üst bilgi adı    | Değer               |
| :------------- | :------------------ |
| x-MS-istemci kimliği | 30d7cc.... 9F55        |
| Yetkilendirme  | Taşıyıcı eyJ0e.... HNIVN |

> [!NOTE]
> `x-ms-client-id` Azure haritalar kimlik doğrulaması sayfasında görüntülenen Azure Maps hesap tabanlı GUID 'dir.

Azure AD OAuth taşıyıcı belirteci kullanan bir Azure Maps Route isteği örneği aşağıda verilmiştir:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

İstemci KIMLIĞINIZI görüntüleme hakkında daha fazla bilgi için bkz. [kimlik doğrulama ayrıntılarını görüntüleme](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Rol tabanlı erişim denetimi ile yetkilendirme

Azure haritalar; Azure AD kullanıcıları, gruplar, uygulamalar, Azure kaynakları ve Azure tarafından yönetilen kimlikler dahil olmak üzere [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md) için tüm asıl türlere erişimi destekler. Asıl türlere, rol tanımı olarak da bilinen bir izin kümesi verilir. Rol tanımı REST API eylemlere izinler sağlar. Bir veya daha fazla Azure Maps hesabına erişim uygulamak, kapsam olarak bilinir. Bir sorumlusu, rol tanımını ve kapsamı uygularken bir rol ataması oluşturulur. 

Sonraki bölümlerde Azure RBAC tümleştirmesi ile Azure Maps tümleştirmesinin kavramları ve bileşenleri ele alınmaktadır. Azure haritalar hesabınızı ayarlama işleminin bir parçası olarak Azure AD dizini, Azure Maps hesabının bulunduğu Azure aboneliğiyle ilişkilendirilir. 

Azure RBAC 'yi yapılandırırken bir güvenlik sorumlusu seçer ve bir rol atamasına uygularsınız. Azure portal rol atamaları ekleme hakkında bilgi edinmek için bkz. [Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Rol tanımı seçme

Uygulama senaryolarını desteklemek için aşağıdaki rol tanımı türleri mevcuttur.

| Azure rol tanımı       | Description                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure haritalar veri okuyucu      | Sabit Azure haritaları REST API 'Lerine erişim sağlar.                                                       |
| Azure haritalar veri Katılımcısı | Kesilebilir Azure Maps REST API 'Lerine erişim sağlar. Değiştirici, eylemler tarafından tanımlanır: yazma ve silme. |
| Özel rol tanımı      | Azure haritalar REST API 'Lerine esnek kısıtlı erişimi etkinleştirmek için hazırlanmış bir rol oluşturun.                      |

Azure haritalar REST API 'Lerinde yazma veya silme eylemlerini gerçekleştirmek için bazı Azure haritalar Hizmetleri için yükseltilmiş ayrıcalıklar gerekebilir. Azure haritalar veri katılımcısı rolü, yazma veya silme eylemleri sağlayan hizmetler için gereklidir. Aşağıdaki tabloda, sağlanan Hizmette yazma veya silme eylemleri kullanılırken Azure Maps veri katılımcısı 'nın hangi hizmetler için geçerli olduğu açıklanmaktadır. Hizmette yalnızca okuma eylemleri kullanılıyorsa, Azure haritalar veri katılımcısı yerine Azure Maps veri okuyucu kullanılabilir.

| Azure haritalar hizmeti | Azure haritalar rol tanımı  |
| :----------------- | :-------------------------- |
| Veriler               | Azure haritalar veri Katılımcısı |
| Oluşturucu            | Azure haritalar veri Katılımcısı |
| Uzamsal            | Azure haritalar veri Katılımcısı |

Azure RBAC ayarlarınızı görüntüleme hakkında daha fazla bilgi için bkz. Azure [RBAC for Azure Maps nasıl yapılandırılır](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Özel rol tanımları

Uygulama güvenliğinin bir yönü, en az ayrıcalık ilkesini uygulamaktır. Bu ilke, güvenlik sorumlusu için yalnızca gerekli olan ve ek erişime sahip olmayan erişime izin verilmesi gerektiğini gösterir. Özel rol tanımları oluşturmak, erişim denetimine daha fazla ayrıntı düzeyi gerektiren kullanım örneklerini destekleyebilir. Özel bir rol tanımı oluşturmak için, tanım için dahil edilecek veya hariç tutulacak belirli veri eylemlerini seçebilirsiniz.

Özel rol tanımı daha sonra herhangi bir güvenlik sorumlusu için rol atamasında kullanılabilir. Azure özel rol tanımları hakkında daha fazla bilgi için bkz. [Azure özel rolleri](../role-based-access-control/custom-roles.md).

Özel rollerin uygulama güvenliğini iyileştirebileceği bazı örnek senaryolar aşağıda verilmiştir.

| Senaryo                                                                                                                                                                                                                 | Özel rol verileri eylemleri                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Temel harita kutucukları ve diğer REST API 'Leri içeren, herkese açık veya etkileşimli bir oturum açma Web sayfası.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Yalnızca ters coğrafi kodlama gerektiren ve diğer REST API 'Leri gerektirmeyen bir uygulama.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Azure Maps Oluşturucu tabanlı harita verilerini ve temel harita kutucuğu REST API 'Lerini okumayı isteyen güvenlik sorumlusu için bir rol.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Bir güvenlik sorumlusu için Oluşturucu tabanlı harita verilerini okumayı, yazmayı ve silmeyi gerektiren bir rol. Bu, bir harita veri Düzenleyicisi rolü olarak tanımlanabilir, ancak temel harita kutucukları gibi diğer REST API 'Lerine erişime izin vermez. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Kapsamı anlama

Rol ataması oluştururken, Azure kaynak hiyerarşisi içinde tanımlanır. Hiyerarşinin en üstünde bir [Yönetim grubu](../governance/management-groups/overview.md) ve en düşük değer Azure Maps hesabı gibi bir Azure kaynağıdır.
Bir kaynak grubuna rol ataması atamak, gruptaki birden çok Azure harita hesabına veya kaynağına erişimi etkinleştirebilir.

> [!TIP]
> Microsoft 'un genel önerisi, aynı Azure aboneliğinde mevcut olan **diğer Azure Maps hesaplarına istenmeden erişimi** önlediği Için Azure Maps hesap kapsamına erişim atacaktır.

## <a name="next-steps"></a>Sonraki adımlar

Azure RBAC hakkında daha fazla bilgi için bkz.
> [!div class="nextstepaction"]
> [Azure rol tabanlı erişim denetimi](../role-based-access-control/overview.md)

Azure AD ve Azure Maps ile bir uygulamanın kimliğini doğrulama hakkında daha fazla bilgi edinmek için bkz.
> [!div class="nextstepaction"]
> [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md)

Azure AD ile Azure Maps Harita Denetimi kimlik doğrulaması hakkında daha fazla bilgi edinmek için bkz.
> [!div class="nextstepaction"]
> [Azure haritalar 'ı kullanın Harita Denetimi](./how-to-use-map-control.md)