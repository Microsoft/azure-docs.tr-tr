---
title: Yönetim ve sorgu API anahtarları oluşturma, yönetme ve güvenli hale getirme
titleSuffix: Azure Cognitive Search
description: API anahtarı, hizmet uç noktasına erişimi denetler. Yönetici anahtarları yazma erişimi verir. Salt okuma erişimi için sorgu anahtarları oluşturulabilir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794383"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Azure Bilişsel Arama hizmeti için API anahtarları oluşturma ve yönetme

Bir arama hizmetine yapılan tüm istekler için özel olarak hizmetiniz için üretilmiş bir salt okunurdur api anahtarı gerekir. API anahtarı, arama hizmeti uç noktanıza erişimin kimliğini doğrulamak için tek mekanizmadır ve her isteğe eklenmelidir. [Rest çözümlerinde](search-get-started-postman.md), API anahtarı genellikle bir istek üst bilgisinde belirtilir. [.Net çözümlerinde](search-howto-dotnet-sdk.md#core-scenarios), bir anahtar genellikle yapılandırma ayarı olarak belirtilir ve [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)üzerinde [kimlik bilgileri](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (yönetici anahtarı) veya [searchcredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (sorgu anahtarı) olarak geçirilir.

Anahtarlar, hizmet sağlama sırasında arama hizmetinize oluşturulur. [Azure Portal](https://portal.azure.com)anahtar değerlerini görüntüleyebilir ve elde edebilirsiniz.

![Portal sayfası, ayarlar, anahtarlar bölümü](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>API anahtarı nedir?

Bir API anahtarı rastgele oluşturulan rakamlardan ve harflerden oluşan bir dizedir. [Rol tabanlı izinler](search-security-rbac.md)aracılığıyla anahtarları silebilir veya okuyabilir, ancak bir anahtarı Kullanıcı tanımlı parolayla veya arama işlemlerine erişmek için birincil kimlik doğrulama yöntemi olarak Active Directory kullanamazsınız. 

Arama hizmetinize erişmek için iki tür anahtar kullanılır: yönetici (okuma/yazma) ve sorgu (salt okuma).

|Anahtar|Açıklama|Sınırlar|  
|---------|-----------------|------------|  
|Yöneticileri|Hizmeti yönetme, dizinler, Dizin oluşturucular ve veri kaynakları oluşturma ve silme gibi tüm işlemlere tam haklar verir.<br /><br /> Portalda *birincil* ve *İkincil* anahtarlar olarak adlandırılan iki yönetici anahtarı, hizmet oluşturulduğunda oluşturulur ve isteğe bağlı olarak tek tek yeniden oluşturulabilir. İki anahtara sahip olmak, hizmete devam etmek için ikinci anahtarı kullanırken bir anahtarın üzerine erişmenizi sağlar.<br /><br /> Yönetici anahtarları yalnızca HTTP istek üst bilgilerinde belirtilir. Bir URL 'ye yönetici API anahtarı yerleştirebilirsiniz.|Hizmet başına en fazla 2|  
|Sorgu|Dizinlere ve belgelere salt okuma erişimi verir ve genellikle arama istekleri veren istemci uygulamalarına dağıtılır.<br /><br /> Sorgu anahtarları isteğe bağlı olarak oluşturulur. Bunları portalda el ile veya [yönetim REST API](https://docs.microsoft.com/rest/api/searchmanagement/)aracılığıyla oluşturabilirsiniz.<br /><br /> Sorgu anahtarları, arama, öneri veya arama işlemi için bir HTTP isteği üst bilgisinde belirtilebilir. Alternatif olarak, bir URL 'ye parametre olarak bir sorgu anahtarı geçirebilirsiniz. İstemci uygulamanızın isteği nasıl formüllamasına bağlı olarak, anahtarı bir sorgu parametresi olarak geçirmek daha kolay olabilir:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|hizmet başına 50|  

 Görsel olarak, bir yönetici anahtarı veya sorgu anahtarı arasında ayrım yoktur. Her iki anahtar de 32 rasgele oluşturulan alfasayısal karakterlerden oluşan dizelerdir. Uygulamanızda hangi anahtar türünün belirtilme izini kaybederseniz, [portalda anahtar değerlerini denetleyebilir](https://portal.azure.com) veya değer ve anahtar türünü döndürmek için [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) kullanabilirsiniz.  

> [!NOTE]  
>  İstek URI 'sinde bir `api-key` gibi hassas verileri geçirmek için kötü bir güvenlik uygulaması olduğu kabul edilir. Bu nedenle, Azure Bilişsel Arama sorgu dizesinde bir `api-key` olarak yalnızca bir sorgu anahtarı kabul eder ve dizininizin içeriği herkese açık bir şekilde kullanılabilir olmadığı sürece bunu yapmaktan kaçınmalısınız. Genel bir kural olarak, `api-key` istek üst bilgisi olarak geçirilmesini öneririz.  

## <a name="find-existing-keys"></a>Mevcut anahtarları bul

Portalda veya [yönetim REST API](https://docs.microsoft.com/rest/api/searchmanagement/)erişim tuşları elde edebilirsiniz. Daha fazla bilgi için bkz. [yönetici ve sorgu API-anahtarlarını yönetme](search-security-api-keys.md).

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
2. Aboneliğiniz için [arama hizmetlerini](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) listeleyin.
3. Hizmeti seçin ve genel bakış sayfasında, yönetim ve sorgu anahtarlarını görüntülemek için **ayarlar** >**anahtarlar** ' a tıklayın.

   ![Portal sayfası, ayarlar, anahtarlar bölümü](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Sorgu anahtarları oluştur

Sorgu anahtarları bir belge koleksiyonunu hedefleyen işlemler için bir dizin içindeki belgelere salt okuma erişimi için kullanılır. Arama, filtre ve öneri sorguları, bir sorgu anahtarı alan tüm işlemlerdir. Bir dizin tanımı ya da Dizin Oluşturucu durumu gibi sistem verilerini veya nesne tanımlarını döndüren tüm salt okuma işlemleri bir yönetici anahtarı gerektirir.

İstemci uygulamalarında erişimi ve işlemleri kısıtlamak, hizmetinizdeki arama varlıklarının korunması için gereklidir. İstemci uygulamasından kaynaklanan herhangi bir sorgu için yönetici anahtarı yerine her zaman bir sorgu anahtarı kullanın.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
2. Aboneliğiniz için [arama hizmetlerini](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) listeleyin.
3. Hizmeti seçin ve genel bakış sayfasında **ayarlar** >**anahtarlar**' a tıklayın.
4. **Sorgu anahtarlarını Yönet**' e tıklayın.
5. Hizmetiniz için önceden oluşturulmuş olan sorgu anahtarını kullanın veya en çok 50 yeni sorgu anahtarı oluşturun. Varsayılan sorgu anahtarı adlandırılmamış, ancak yönetilebilirlik için ek sorgu anahtarları adlandırılmış olabilir.

   ![Sorgu anahtarı oluşturma veya kullanma](media/search-security-overview/create-query-key.png) 

> [!Note]
> Sorgu anahtarı kullanımını gösteren bir kod örneği, [içindeki C#bir Azure bilişsel arama dizininde sorgulama ](search-query-dotnet.md)içinde bulunabilir.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Yönetici anahtarlarını yeniden oluştur

Bir birincil anahtarı, iş sürekliliği için ikincil anahtarı kullanarak döndürebilmeniz için her bir hizmet için iki yönetici anahtarı oluşturulur.

1. **Ayarlar** >**anahtarlar** sayfasında, ikincil anahtarı kopyalayın.
2. Tüm uygulamalar için, API anahtarı ayarlarını ikincil anahtarı kullanacak şekilde güncelleştirin.
3. Birincil anahtarı yeniden oluşturun.
4. Tüm uygulamaları yeni birincil anahtarı kullanacak şekilde güncelleştirin.

Her iki anahtarı da aynı anda yeniden oluşturmanız durumunda, bu anahtarları kullanan tüm istemci istekleri HTTP 403 yasaklanmış ile başarısız olur. Ancak içerik silinmez ve kalıcı olarak kilitlenmemiştir. 

Hizmete portal veya Yönetim Katmanı ([REST API](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)veya Azure Resource Manager) üzerinden erişmeye devam edebilirsiniz. Yönetim işlevleri, hizmet API anahtarı olmayan bir abonelik KIMLIĞIYLE çalışır ve bu nedenle API anahtarlarınız olmasa bile yine de kullanılabilir. 

Portal veya yönetim katmanı aracılığıyla yeni anahtarlar oluşturduktan sonra, yeni anahtarlar ve istekler üzerinde bu anahtarlar sağladığınızda, erişim içeriğinize (dizinler, Dizin oluşturucular, veri kaynakları, eş anlamlı haritalar) geri yüklenir.

## <a name="secure-api-keys"></a>Güvenli API anahtarları
Ana güvenlik, portal veya Kaynak Yöneticisi arabirimleri (PowerShell veya komut satırı arabirimi) aracılığıyla erişimi kısıtlayarak bir şekilde yapılır. Belirtildiği gibi, abonelik yöneticileri tüm API anahtarlarını görüntüleyebilir ve yeniden oluşturabilir. Bir önlem olarak, yönetici anahtarlarına kimlerin erişebileceğini anlamak için rol atamalarını gözden geçirin.

+ Hizmet panosunda, hizmetinize ilişkin rol atamalarını görüntülemek için **erişim denetimi (IAM)** ve ardından **rol atamaları** sekmesini tıklatın.

Aşağıdaki rollerin üyeleri anahtarları görüntüleyebilir ve yeniden oluşturabilir: sahip, katkıda bulunan, [Arama hizmeti katkıda bulunanlar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Arama sonuçları üzerinden kimlik tabanlı erişim için, sonuçları kimliğe göre kırpmak, istek sahibinin erişimi olmaması gereken belgeleri kaldırmak için güvenlik filtreleri oluşturabilirsiniz. Daha fazla bilgi için bkz. [Güvenlik filtreleri](search-security-trimming-for-azure-search.md) ve [Active Directory Ile güvenli hale getirme](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure Bilişsel Arama rol tabanlı erişim denetimi](search-security-rbac.md)
+ [PowerShell’i kullanarak yönetme](search-manage-powershell.md) 
+ [Performans ve iyileştirme makalesi](search-performance-optimization.md)