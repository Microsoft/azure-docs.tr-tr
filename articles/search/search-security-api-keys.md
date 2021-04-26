---
title: API anahtarı kimlik doğrulaması
titleSuffix: Azure Cognitive Search
description: Bir API anahtarı, hizmet uç noktasına gelen erişimi denetler. Yönetici anahtarları yazma erişimi verir. Salt okuma erişimi için sorgu anahtarları oluşturulabilir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 6954ce289cb3cf219f8c4024a112411fd60d70e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310674"
---
# <a name="create-and-manage-api-keys-for-authentication-to-azure-cognitive-search"></a>Azure Bilişsel Arama kimlik doğrulaması için API anahtarları oluşturma ve yönetme

Bir arama hizmetine bağlanırken, tüm isteklerin hizmetinize özel olarak oluşturulan salt okunurdur bir API anahtarı içermesi gerekir. API anahtarı, arama hizmeti uç noktanıza gelen isteklerin kimliğini doğrulamaya yönelik tek mekanizmadır ve her istekte gereklidir. 

+ [Rest çözümlerinde](search-get-started-rest.md), `api-key` genellikle bir istek üst bilgisinde belirtilir

+ [.Net çözümlerinde](search-howto-dotnet-sdk.md), genellikle bir anahtar yapılandırma ayarı olarak belirtilir ve sonra bir [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) olarak geçirilir

[Azure Portal](https://portal.azure.com)veya [POWERSHELL](/powershell/module/az.search), [Azure CLI](/cli/azure/search)veya [REST API](/rest/api/searchmanagement/)aracılığıyla API anahtarlarını görüntüleyebilir ve yönetebilirsiniz.

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portal sayfası, ayarları alma, anahtarlar bölümü" border="false":::

## <a name="what-is-an-api-key"></a>API anahtarı nedir?

Bir API anahtarı, arama hizmetine yapılan her istekte geçirilen rastgele üretilmiş sayılardan ve harflerden oluşan benzersiz bir dizedir. Hem isteğin kendisi hem de anahtar geçerliyse, hizmet isteği kabul eder. 

Arama hizmetinize erişmek için iki tür anahtar kullanılır: yönetici (okuma/yazma) ve sorgu (salt okuma).

|Anahtar|Description|Sınırlar|  
|---------|-----------------|------------|  
|Yönetici|Hizmeti yönetme, dizinler, Dizin oluşturucular ve veri kaynakları oluşturma ve silme gibi tüm işlemlere tam haklar verir.<br /><br /> Portalda *birincil* ve *İkincil* anahtarlar olarak adlandırılan iki yönetici anahtarı, hizmet oluşturulduğunda oluşturulur ve isteğe bağlı olarak tek tek yeniden oluşturulabilir. İki anahtara sahip olmak, hizmete devam etmek için ikinci anahtarı kullanırken bir anahtarın üzerine erişmenizi sağlar.<br /><br /> Yönetici anahtarları yalnızca HTTP istek üst bilgilerinde belirtilir. Bir URL 'ye yönetici API anahtarı yerleştirebilirsiniz.|Hizmet başına en fazla 2|  
|Sorgu|Dizinlere ve belgelere salt okuma erişimi verir ve genellikle arama istekleri veren istemci uygulamalarına dağıtılır.<br /><br /> Sorgu anahtarları isteğe bağlı olarak oluşturulur.<br /><br /> Sorgu anahtarları, arama, öneri veya arama işlemi için bir HTTP isteği üst bilgisinde belirtilebilir. Alternatif olarak, bir URL 'ye parametre olarak bir sorgu anahtarı geçirebilirsiniz. İstemci uygulamanızın isteği nasıl formüllamasına bağlı olarak, anahtarı bir sorgu parametresi olarak geçirmek daha kolay olabilir:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|hizmet başına 50|  

 Görsel olarak, bir yönetici anahtarı veya sorgu anahtarı arasında ayrım yoktur. Her iki anahtar de 32 rasgele oluşturulan alfasayısal karakterlerden oluşan dizelerdir. Uygulamanızda hangi anahtar türünün belirtilme izini kaybederseniz, [portalda anahtar değerlerini kontrol](https://portal.azure.com)edebilirsiniz.  

> [!NOTE]  
> İstek URI 'sinde bir gibi hassas verileri geçirmek için kötü bir güvenlik uygulaması olduğu kabul edilir `api-key` . Bu nedenle, Azure Bilişsel Arama sorgu dizesinde yalnızca bir sorgu anahtarını kabul eder `api-key` ve dizininizin içeriği herkese açık bir şekilde kullanılabilir olmadığı sürece bunu yapmaktan kaçınmalısınız. Genel bir kural olarak, `api-key` isteğinizi istek üst bilgisi olarak geçirmeyi öneririz.  

## <a name="find-existing-keys"></a>Mevcut anahtarları bul

Portal 'da veya [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)veya [REST API](/rest/api/searchmanagement/)aracılığıyla erişim tuşları elde edebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Aboneliğiniz için [arama hizmetlerini](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) listeleyin.
1. Hizmeti seçin ve genel bakış sayfasında,   > Yönetim ve sorgu anahtarlarını görüntülemek için ayarlar **anahtarlar** ' a tıklayın.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Portal sayfası, görünüm ayarları, anahtarlar bölümü" border="false":::

## <a name="create-query-keys"></a>Sorgu anahtarları oluştur

Sorgu anahtarları bir belge koleksiyonunu hedefleyen işlemler için bir dizin içindeki belgelere salt okuma erişimi için kullanılır. Arama, filtre ve öneri sorguları, bir sorgu anahtarı alan tüm işlemlerdir. Bir dizin tanımı ya da Dizin Oluşturucu durumu gibi sistem verilerini veya nesne tanımlarını döndüren tüm salt okuma işlemleri bir yönetici anahtarı gerektirir.

İstemci uygulamalarında erişimi ve işlemleri kısıtlamak, hizmetinizdeki arama varlıklarının korunması için gereklidir. İstemci uygulamasından kaynaklanan herhangi bir sorgu için yönetici anahtarı yerine her zaman bir sorgu anahtarı kullanın.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Aboneliğiniz için [arama hizmetlerini](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  listeleyin.
3. Hizmeti seçin ve genel bakış sayfasında **Ayarlar**  > **anahtarlar**' a tıklayın.
4. **Sorgu anahtarlarını Yönet**' e tıklayın.
5. Hizmetiniz için önceden oluşturulmuş olan sorgu anahtarını kullanın veya en çok 50 yeni sorgu anahtarı oluşturun. Varsayılan sorgu anahtarı adlandırılmamış, ancak yönetilebilirlik için ek sorgu anahtarları adlandırılmış olabilir.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Sorgu anahtarı oluşturma veya kullanma" border="false":::

> [!Note]
> Sorgu anahtarı kullanımını gösteren bir kod örneği, [Dotnethowto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)içinde bulunabilir.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Yönetici anahtarlarını yeniden oluştur

Bir birincil anahtarı, iş sürekliliği için ikincil anahtarı kullanarak döndürebilmeniz için her bir hizmet için iki yönetici anahtarı oluşturulur.

1. **Ayarlar**  > **anahtarlar** sayfasında, ikincil anahtarı kopyalayın.
2. Tüm uygulamalar için, API anahtarı ayarlarını ikincil anahtarı kullanacak şekilde güncelleştirin.
3. Birincil anahtarı yeniden oluşturun.
4. Tüm uygulamaları yeni birincil anahtarı kullanacak şekilde güncelleştirin.

Her iki anahtarı da aynı anda yeniden oluşturmanız durumunda, bu anahtarları kullanan tüm istemci istekleri HTTP 403 yasaklanmış ile başarısız olur. Ancak içerik silinmez ve kalıcı olarak kilitlenmemiştir. 

Hizmete Portal üzerinden veya program aracılığıyla erişmeye devam edebilirsiniz. Yönetim işlevleri, bir hizmet API anahtarı olmayan bir abonelik KIMLIĞI üzerinden çalışır ve bu nedenle API anahtarlarınız olmasa bile yine de kullanılabilir. 

Portal veya yönetim katmanı aracılığıyla yeni anahtarlar oluşturduktan sonra, yeni anahtarlar ve istekler üzerinde bu anahtarlar sağladığınızda, erişim içeriğinize (dizinler, Dizin oluşturucular, veri kaynakları, eş anlamlı haritalar) geri yüklenir.

## <a name="secure-api-keys"></a>Güvenli API anahtarları

[Rol tabanlı izinler](search-security-rbac.md)aracılığıyla anahtarları silebilir veya okuyabilir, ancak bir anahtarı Kullanıcı tanımlı parolayla veya arama işlemlerine erişmek için birincil kimlik doğrulama yöntemi olarak Active Directory kullanamazsınız. 

Ana güvenlik, portal veya Kaynak Yöneticisi arabirimleri (PowerShell veya komut satırı arabirimi) aracılığıyla erişimi kısıtlayarak bir şekilde yapılır. Belirtildiği gibi, abonelik yöneticileri tüm API anahtarlarını görüntüleyebilir ve yeniden oluşturabilir. Bir önlem olarak, yönetici anahtarlarına kimlerin erişebileceğini anlamak için rol atamalarını gözden geçirin.

+ Hizmet panosunda, hizmetinize ilişkin rol atamalarını görüntülemek için **erişim denetimi (IAM)** ve ardından **rol atamaları** sekmesini tıklatın.

Aşağıdaki rollerin üyeleri anahtarları görüntüleyebilir ve yeniden oluşturabilir: sahip, katkıda bulunan, [Arama hizmeti katkıda bulunanlar](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure Bilişsel Arama güvenliği](search-security-overview.md)
+ [Azure Bilişsel Arama Azure rol tabanlı erişim denetimi](search-security-rbac.md)
+ [PowerShell kullanarak yönetme](search-manage-powershell.md) 