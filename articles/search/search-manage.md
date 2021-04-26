---
title: Portalda hizmet yönetimi
titleSuffix: Azure Cognitive Search
description: Azure portal kullanarak Microsoft Azure barındırılan bir bulut arama hizmeti olan Azure Bilişsel Arama hizmetini yönetin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579801"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure portal Azure Bilişsel Arama için hizmet yönetimi

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Bilişsel Arama, özel uygulamalara zengin arama deneyimi oluşturmak için kullanılan, tam olarak yönetilen, bulut tabanlı bir arama hizmetidir. Bu makalede, önceden oluşturduğunuz bir arama hizmeti için [Azure Portal](https://portal.azure.com) gerçekleştirebileceğiniz yönetim görevleri ele alınmaktadır. Portal, hizmetle ilgili tüm [yönetim görevlerinin](#management-tasks) yanı sıra içerik yönetimi ve araştırmayı gerçekleştirmenize olanak tanır. Bu nedenle, Portal arama hizmeti işlemlerinin tüm yönlerine geniş bir spekme erişimi sağlar.

Her arama hizmeti tek başına bir kaynak olarak yönetilir. Aşağıdaki görüntüde, "demo-Search-svc" adlı tek bir ücretsiz arama hizmeti için portal sayfaları gösterilmektedir. Hizmet yönetimi için Azure PowerShell veya Azure CLı kullanmaya alışkın olabilirsiniz, ancak Portal sayfalarının sağladığı araçlar ve yetenekler hakkında bilgi sahibi olmak mantıklı olur. Bazı görevler portalda kod aracılığıyla daha kolay ve daha hızlı gerçekleştirilir. 

## <a name="overview-home-page"></a>Genel Bakış (giriş) sayfası

Genel Bakış sayfası, her bir hizmetin "giriş" sayfasıdır. Aşağıda, ekranda kırmızı kutular içine alınmış olan alanlarda, özellikle de hizmete yeni başladıysanız kullanabileceğiniz görevler, Araçlar ve kutucuklar gösterilir.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Arama hizmeti için portal sayfaları" border="true":::

| Alan | Açıklama |
|------|-------------|
| 1  | **Temel** bileşenler bölümünde, bağlantıları ayarlarken kullanılan uç nokta dahil hizmet özellikleri gösterilmektedir. Aynı zamanda katman, çoğaltma ve bölüm sayılarını bir bakışta gösterir. |
| 2 | Sayfanın üst kısmında etkileşimli araçları çağırmaya veya hizmeti yönetmeye yönelik bir dizi komut bulunur. Hem [Içeri aktarma verileri](search-get-started-portal.md) hem de [Arama Gezgini](search-explorer.md) , Prototipleme ve araştırma için yaygın olarak kullanılır. |
| 3 | **Essentials** bölümünün altında, kullanım istatistiklerine, hizmet durumu ölçümlerine ve var olan tüm dizinlerin, Dizin oluşturuculardan, veri kaynaklarına ve becerileri hizmetinize erişim için bir dizi sekmeli alt sayfaların serisi vardır. Bir dizin veya başka bir nesne seçerseniz, nesne kompozisyonu, ayarları ve durumu (varsa) göstermek için ek sayfalar kullanılabilir hale gelir. |
| 4 | Sol tarafta, bağlantılarda kullanılan API anahtarlarına erişmek, hizmeti yapılandırmak, güvenliği yapılandırmak, işlemleri izlemek, görevleri otomatikleştirmek ve destek almak için ek sayfalar açan bağlantılara erişebilirsiniz. |

### <a name="read-only-service-properties"></a>Salt okunurdur hizmet özellikleri

Bir arama hizmetinin birkaç yönü, hizmetin sağlandığı ve değiştirilemeyeceğini belirler:

* Hizmet adı (bir arama hizmetini yeniden adlandıramazsınız)
* Hizmet konumu (bozulmadan arama hizmetini başka bir bölgeye kolayca taşıyamazsınız. Bir şablon olmasına rağmen içeriğin taşınması el ile gerçekleştirilen bir işlemdir.
* Hizmet katmanı (örneğin, yeni bir hizmet oluşturmadan S1 'ten S2 'e geçiş yapılamaz)

## <a name="management-tasks"></a>Yönetim görevleri

Hizmet yönetimi, tasarıma göre hafif ve genellikle hizmete göre gerçekleştirebileceğiniz görevler tarafından tanımlanır:

* Çoğaltmaları ve bölümleri ekleyerek veya kaldırarak [kapasiteyi ayarlama](search-capacity-planning.md)
* Yönetici ve sorgu işlemleri için kullanılan [API anahtarlarını yönetme](search-security-api-keys.md)
* Rol tabanlı güvenlik aracılığıyla [yönetici işlemlerine erişimi denetleme](search-security-rbac.md)
* IP adresine göre erişimi kısıtlamak için [IP güvenlik duvarı kurallarını yapılandırma](service-configure-firewall.md)
* Azure özel bağlantısı ve özel bir sanal ağ kullanarak [özel bir uç nokta yapılandırma](service-create-private-endpoint.md)
* [Hizmet durumunu izleme](search-monitor-usage.md): depolama, sorgu birimleri ve gecikme

Ayrıca, hizmet üzerinde oluşturulan tüm nesneleri (dizinler, Dizin oluşturucular, veri kaynakları, becerileri vb.) sıralayabilirsiniz. Portalın genel bakış sayfası, hizmetinizde bulunan tüm içeriği gösterir. Bir arama hizmetindeki işlemlerin büyük çoğunluğu içerikle ilgilidir.

Portalda gerçekleştirilen aynı yönetim görevleri, [YÖNETIM REST API 'leri](/rest/api/searchmanagement/), [az. Search PowerShell modülü](search-manage-powershell.md), [az Azure CLI modülünü arama](search-manage-azure-cli.md)ve .net, Python, Java ve JavaScript için Azure SDK 'ları aracılığıyla programlı bir şekilde işlenebilir. Yönetim görevleri Portal ve tüm programlı arabirimler genelinde tamamen temsil edilir. Yalnızca tek bir modülüde kullanılabilen belirli bir yönetim görevi yoktur.

Bilişsel Arama daha derin izleme ve yönetim için diğer Azure hizmetlerinden yararlanır. Tek başına, arama hizmeti içinde depolanan veriler yalnızca nesne içeriktir (dizinler, Dizin Oluşturucu ve veri kaynağı tanımları ve diğer nesneler). Portal sayfalarına bildirilen ölçümler, 30 günlük bir döngüde iç günlüklerden çekilir. Kullanıcı denetimli günlük tutma ve ek olaylar için [Azure izleyici](../azure-monitor/index.yml)'ye ihtiyacınız olacaktır. Arama hizmeti için tanılama günlüğü ayarlama hakkında daha fazla bilgi için bkz. [günlük verilerini toplama ve analiz etme](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Yönetici izinleri

Arama hizmeti genel bakış sayfasını açtığınızda, hesabınıza atanan izinler size hangi sayfaların kullanılabildiğini belirlemektir. Makalenin başındaki genel bakış sayfası, yönetici veya katkıda bulunan portalın göreceği Portal sayfalarını gösterir.

Azure kaynağında, yönetici hakları rol atamaları aracılığıyla verilir. Azure Bilişsel Arama bağlamında, [rol atamaları](search-security-rbac.md) , Portal, [POWERSHELL](search-manage-powershell.md), [Azure CLI](search-manage-azure-cli.md)veya [Yönetim REST API 'leri](/rest/api/searchmanagement/search-howto-management-rest-api)kullanıp kullanmadıklarından bağımsız olarak, kimin çoğaltmaları ve bölümleri ayırabileceğini veya API anahtarlarını yönetebileceğini belirlemektir:

> [!TIP]
> Hizmetin kendisi için sağlanması veya kullanımdan kaldırılması, bir Azure abonelik Yöneticisi veya ortak yönetici tarafından yapılabilir. Azure genelinde mekanizmalar kullanarak, yönetici haklarına sahip kullanıcılar tarafından, arama hizmetinizin yanlışlıkla veya izinsiz olarak silinmesini engellemek için bir aboneliği veya kaynağı kilitleyebilir. Daha fazla bilgi için bkz. [beklenmeyen silme işlemini engellemek için kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Sonraki adımlar

* Portalda bulunan [izleme yeteneklerini](search-monitor-usage.md) gözden geçirme
* [PowerShell](search-manage-powershell.md) veya [Azure CLI](search-manage-azure-cli.md) ile otomatikleştirin
* İçerik ve işlemleri korumak için [güvenlik özelliklerini](search-security-overview.md) gözden geçirme
* Sorgu ve dizin oluşturma iş yüklerini izlemek için [Tanılama günlüğünü](search-monitor-logs.md) etkinleştirme