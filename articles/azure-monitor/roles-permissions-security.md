---
title: Azure Izleyici 'de roller, izinler ve güvenlik
description: İzleme kaynaklarına erişimi kısıtlamak için Azure Izleyici 'nin yerleşik rollerini ve izinlerini nasıl kullanacağınızı öğrenin.
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 64e770ea328516bdc9dbe1a2e17070c15a252fe7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490038"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Azure Izleyici 'de roller, izinler ve güvenlik

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Birçok ekibin izleme verilerine ve ayarlarına erişimi kesinlikle daha fazla düzenleyen olması gerekir. Örneğin, özellikle izleme üzerinde (destek mühendisleri, DevOps mühendisleri) çalışan ekip üyeleriniz varsa veya yönetilen hizmet sağlayıcısı kullanıyorsanız, bu kullanıcılara yalnızca izleme verilerine erişim vermek isteyebilirsiniz, bu da kaynakları oluşturma, değiştirme veya silme imkanlarını kısıtlamakla sınırlayabilirsiniz. Bu makalede, Azure 'daki bir kullanıcıya kolayca yerleşik bir izleme Azure rolü uygulama veya sınırlı izleme izinleri olan bir kullanıcı için kendi özel rolünüzü oluşturma işlemlerinin nasıl yapılacağı gösterilir. Daha sonra, Azure monitörünüzle ilgili kaynaklarınız için güvenlik konuları ve içerdikleri verilere erişimi nasıl sınırlayabileceğinizi anlatmaktadır.

## <a name="built-in-monitoring-roles"></a>Yerleşik izleme rolleri
Azure Izleyici 'nin yerleşik rolleri, bir abonelikteki kaynaklarla erişimi sınırlamaya yardımcı olmak üzere tasarlanmıştır. bu sayede, altyapının ihtiyaç duydukları verileri elde etmek ve yapılandırmak üzere izleme için sorumlu olmaya devam ediyor. Azure Izleyici, iki kullanıma hazır rol sağlar: Izleme okuyucu ve Izleme katılımcısı.

### <a name="monitoring-reader"></a>İzleme okuyucusu
Izleme okuyucusu rolünü atayan kişiler, bir abonelikteki tüm izleme verilerini görüntüleyebilir, ancak herhangi bir kaynağı değiştiremez veya izleme kaynaklarıyla ilgili herhangi bir ayarı düzenleyemez. Bu rol, bir kuruluştaki, destek veya operasyon mühendisleri gibi kullanıcıların şunları yapabilmesi gereken kullanıcılar için uygundur:

* Portalda izleme panolarını görüntüleyin ve kendi özel izleme panoları oluşturun.
* [Azure uyarıları](alerts/alerts-overview.md) 'nda tanımlanan uyarı kurallarını görüntüle
* [Azure izleyici REST API](/rest/api/monitor/metrics), [PowerShell cmdlet 'leri](powershell-samples.md)veya [platformlar arası CLI](cli-samples.md)kullanarak ölçümleri sorgulayın.
* Portal, Azure Izleyici REST API, PowerShell cmdlet 'leri veya platformlar arası CLı kullanarak etkinlik günlüğünü sorgulayın.
* Bir kaynağın [tanılama ayarlarını](essentials/diagnostic-settings.md) görüntüleyin.
* Bir aboneliğin [günlük profilini](essentials/activity-log.md#legacy-collection-methods) görüntüleyin.
* Otomatik ölçeklendirme ayarlarını görüntüleyin.
* Uyarı etkinliğini ve ayarlarını görüntüleyin.
* Application Insights verilere erişin ve verileri AI analizinden görüntüleyin.
* Çalışma alanının kullanım verilerini içeren Log Analytics çalışma alanı verilerini arayın.
* Log Analytics yönetim gruplarını görüntüleyin.
* Log Analytics çalışma alanında arama şemasını alın.
* Log Analytics çalışma alanında izleme paketlerini listeleyin.
* Log Analytics çalışma alanında kaydedilmiş aramaları alın ve yürütün.
* Log Analytics çalışma alanı depolama yapılandırmasını alın.

> [!NOTE]
> Bu rol, bir olay hub 'ına akan veya depolama hesabında depolanan günlük verilerine okuma erişimi vermez. Bu kaynaklara erişimi yapılandırma hakkında daha fazla bilgi için [aşağıya bakın](#security-considerations-for-monitoring-data) .
> 
> 

### <a name="monitoring-contributor"></a>Katkıda bulunan izleniyor
Izleme katılımcısı rolünü atayan kişiler, bir abonelikteki tüm izleme verilerini görüntüleyebilir ve izleme ayarlarını oluşturabilir veya değiştirebilir, ancak başka bir kaynağı değiştiremezler. Bu rol, Izleme okuyucusu rolünün bir üst kümesidir ve bir kuruluşun izleme ekibinin veya yukarıdaki izinlerin yanı sıra, yukarıdaki izinlerle ilgili olarak da şunları yapabilmeleri için gerekli olan yönetilen hizmet sağlayıcılarının üyeleri için uygundur:

* İzleme panoları paylaşılan bir pano olarak yayımlayın.
* Bir kaynak için [tanılama ayarlarını](essentials/diagnostic-settings.md) belirleyin.\*
* Abonelik için [günlük profilini](essentials/activity-log.md#legacy-collection-methods) ayarlayın.\*
* Uyarı kuralları etkinliğini ve ayarlarını [Azure uyarıları](alerts/alerts-overview.md)aracılığıyla ayarlayın.
* Application Insights Web testleri ve bileşenleri oluşturun.
* Log Analytics çalışma alanı paylaşılan anahtarlarını listeleyin.
* Log Analytics çalışma alanında izleme paketlerini etkinleştirin veya devre dışı bırakın.
* Log Analytics çalışma alanında kaydedilmiş aramaları oluşturun ve silin ve yürütün.
* Log Analytics çalışma alanı depolama yapılandırmasını oluşturun ve silin.

\*Ayrıca, bir günlük profili veya tanılama ayarı ayarlamak için Kullanıcı hedef kaynakta (depolama hesabı veya Olay Hub 'ı ad alanı) ListKeys iznine sahip olmalıdır.

> [!NOTE]
> Bu rol, bir olay hub 'ına akan veya depolama hesabında depolanan günlük verilerine okuma erişimi vermez. Bu kaynaklara erişimi yapılandırma hakkında daha fazla bilgi için [aşağıya bakın](#security-considerations-for-monitoring-data) .
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>İzinleri ve Azure özel rollerini izleme
Yukarıdaki yerleşik roller takımınızın tam ihtiyaçlarını karşılamıyorsa, daha ayrıntılı izinlerle [bir Azure özel rolü oluşturabilirsiniz](../role-based-access-control/custom-roles.md) . Aşağıda, Azure Izleyici için genel Azure RBAC işlemleri açıklamalarıyla birlikte verilmiştir.

| İşlem | Açıklama |
| --- | --- |
| Microsoft. Insights/ActionGroups/[okuma, yazma, silme] |Okuma/yazma/silme eylemi grupları. |
| Microsoft. Insights/ActivityLogAlerts/[okuma, yazma, silme] |Etkinlik günlüğü uyarılarını okuma/yazma/silme. |
| Microsoft. Insights/AlertRules/[okuma, yazma, silme] |Okuma/yazma/silme uyarısı kuralları (klasik uyarılar). |
| Microsoft. Insights/AlertRules/olaylar/okuma |Uyarı kuralları için olayları listeleyin (tetiklenen uyarı kuralının geçmişi). Bu yalnızca Portal için geçerlidir. |
| Microsoft. Insights/oto Scalesettings/[okuma, yazma, silme] |Otomatik ölçeklendirme ayarlarını okuma/yazma/silme. |
| Microsoft. Insights/DiagnosticSettings/[okuma, yazma, silme] |Tanılama ayarlarını okuma/yazma/silme. |
| Microsoft. Insights/EventCategories/okuma |Etkinlik günlüğünde olabilecek tüm kategorileri numaralandırın. Azure portal tarafından kullanılır. |
| Microsoft. Insights/eventTypes/digestevents/Read |Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. |
| Microsoft. Insights/eventTypes/Values/Read |Bir abonelikteki etkinlik günlüğü olaylarını (yönetim olayları) listeleyin. Bu izin, etkinlik günlüğüne hem programlı hem de portala erişim için geçerlidir. |
| Microsoft. Insights/ExtendedDiagnosticSettings/[okuma, yazma, silme] | Ağ akışı günlükleri için tanılama ayarlarını okuma/yazma/silme. |
| Microsoft. Insights/LogDefinitions/okuma |Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. |
| Microsoft. Insights/LogProfiles/[okuma, yazma, silme] |Günlük profillerini okuma/yazma/silme (Olay Hub 'ına veya depolama hesabına akış etkinliği günlüğü). |
| Microsoft. Insights/MetricAlerts/[okuma, yazma, silme] |Neredeyse gerçek zamanlı ölçüm uyarılarını okuma/yazma/silme |
| Microsoft. Insights/MetricDefinitions/okuma |Ölçüm tanımlarını oku (bir kaynak için kullanılabilen ölçüm türlerinin listesi). |
| Microsoft. Insights/ölçümler/okuma |Bir kaynak için ölçümleri okuyun. |
| Microsoft. Insights/Register/ACTION |Azure Izleyici kaynak sağlayıcısını kaydedin. |
| Microsoft. Insights/ScheduledQueryRules/[okuma, yazma, silme] |Azure Izleyici 'de günlük uyarılarını okuma/yazma/silme. |



> [!NOTE]
> Bir kaynağın uyarılarına, tanılama ayarlarına ve ölçümlere erişim için, kullanıcının kaynak türü ve kaynak kapsamına okuma erişiminin olması gerekir. ("Yazma") oluşturma ("yazma") bir depolama hesabı veya Olay Hub 'larına Arşiv için Arşiv yapan bir tanılama ayarı veya günlük profili, kullanıcının hedef kaynakta ListKeys iznine de sahip olmasını gerektirir.
> 
> 

Örneğin, Yukarıdaki tabloyu kullanarak bir "etkinlik günlüğü okuyucusu" için şu şekilde bir Azure özel rolü oluşturabilirsiniz:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>İzleme verileri için güvenlik konuları
İzleme verileri — özellikle günlük dosyaları — IP adresleri veya Kullanıcı adları gibi gizli bilgiler içerebilir. Azure 'daki izleme verileri üç temel formda sunulur:

1. Azure aboneliğinizdeki tüm denetim düzlemi eylemlerini açıklayan etkinlik günlüğü.
2. bir kaynak tarafından oluşturulan Günlükler olan kaynak günlükleri.
3. Kaynaklar tarafından yayılan ölçümler.

Bu veri türlerinin üçü de bir depolama hesabında depolanabilir veya her ikisi de genel amaçlı Azure kaynakları olan olay hub 'ına akış yapılabilir. Bunlar genel amaçlı kaynaklar olduğundan, oluşturma, silme ve bunlara erişme, yönetici için ayrılmış ayrıcalıklı bir işlemdir. Kötüye kullanımı engellemek için izleme ile ilgili kaynaklar için aşağıdaki yöntemleri kullanmanızı öneririz:

* Verileri izlemek için tek, ayrılmış bir depolama hesabı kullanın. İzleme verilerini birden çok depolama hesabına ayırmanız gerekiyorsa, bu durum istenmeden izleme verileri (örneğin, üçüncü taraf SıEM) yalnızca izleme verilerine erişim ihtiyacı olan bir depolama hesabının kullanımını hiçbir şekilde vermeyin.
* Yukarıdaki tüm tanılama ayarlarında tek bir adanmış Service Bus veya Olay Hub 'ı ad alanı kullanın.
* İzleme ile ilgili depolama hesaplarına veya Olay Hub 'larına erişimi ayrı bir kaynak grubunda tutarak sınırlayın ve yalnızca bu kaynak grubuyla erişimi sınırlandırmak için izleme rollerinizin [kapsamını kullanın](../role-based-access-control/overview.md#scope) .
* Kullanıcı yalnızca izleme verilerine erişmesi gerektiğinde, abonelik kapsamındaki depolama hesapları veya Olay Hub 'ları için ListKeys iznini hiçbir zaman vermeyin. Bunun yerine, bu izinleri kullanıcıya bir kaynak veya kaynak grubunda (özel bir izleme kaynak grubunuz varsa) kapsamını verin.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>İzleme ile ilgili depolama hesaplarına erişimi sınırlandırma
Bir kullanıcı veya uygulamanın bir depolama hesabındaki izleme verilerine erişmesi gerektiğinde, depolama hesabında, blob depolamaya yönelik hizmet düzeyinde salt okuma erişimi olan izleme verilerini içeren [bir hesap SAS oluşturmalısınız](/rest/api/storageservices/create-account-sas) . PowerShell 'de bu şöyle görünebilir:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Daha sonra bu depolama hesabından okuması gereken varlığa belirteç verebilir ve bu depolama hesabındaki tüm Blobları listeleyebilir ve okuyabilir.

Alternatif olarak, Azure RBAC ile bu izni denetetmeniz gerekiyorsa, söz konusu depolama hesabında bu varlığa Microsoft. Storage/storageAccounts/ListKeys/Action izinleri verebilirsiniz. Bu, bir depolama hesabına arşivlemek üzere bir tanılama ayarı veya günlük profili ayarlayabilmesi gereken kullanıcılar için gereklidir. Örneğin, yalnızca bir depolama hesabından okuması gereken bir kullanıcı veya uygulama için aşağıdaki Azure özel rolünü oluşturabilirsiniz:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys izni, kullanıcının birincil ve ikincil depolama hesabı anahtarlarını listelemenize olanak sağlar. Bu anahtarlar kullanıcıya, bu depolama hesabındaki tüm imzalı hizmetler (blob, kuyruk, tablo, dosya) için tüm imzalı izinleri (okuma, yazma, blob oluşturma, blob silme vb.) verir. Mümkün olduğunda yukarıda açıklanan bir hesap SAS kullanmanızı öneririz.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>İzleme ile ilgili olay hub 'larına erişimi sınırlandırma
Benzer bir model, Olay Hub 'ları ile izlenebilir, ancak önce adanmış bir dinleme yetkilendirme kuralı oluşturmanız gerekir. Yalnızca izlemeyle ilgili olay hub 'larını dinlemek için gereken bir uygulamaya erişim vermek istiyorsanız, aşağıdakileri yapın:

1. Yalnızca dinleme talepleri ile akış izleme verileri için oluşturulan olay hub 'ları üzerinde bir paylaşılan erişim ilkesi oluşturun. Bu, portalda yapılabilir. Örneğin, bunu "monitoringReadOnly" olarak çağırabilirsiniz. Mümkünse, bu anahtarı doğrudan tüketiciye vermek ve sonraki adımı atlamak isteyeceksiniz.
2. Tüketicinin anahtarı geçici bir şekilde alabilmesi gerekiyorsa, kullanıcıya bu olay hub 'ı için ListKeys eylemini verin. Bu Ayrıca, bir tanılama ayarı veya günlük profilini Olay Hub 'larına akışa ayarlayabilmesi gereken kullanıcılar için de gereklidir. Örneğin, bir Azure RBAC kuralı oluşturabilirsiniz:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Güvenli bir Sanal Ağ içinde izleme

Azure Izleyici, etkinleştirdiğiniz hizmetleri sağlamak için Azure kaynaklarınıza erişmeniz gerekir. Azure kaynaklarınızı, hala genel Internet erişimi olan güvenli hale getirirken izlemek isterseniz, aşağıdaki ayarları etkinleştirebilirsiniz.

### <a name="secured-storage-accounts"></a>Güvenli depolama hesapları 

İzleme verileri genellikle bir depolama hesabına yazılır. Bir depolama hesabına kopyalanmış verilere yetkisiz kullanıcılar tarafından erişilemediğinden emin olmak isteyebilirsiniz. Ek güvenlik için, bir depolama hesabını "seçili ağları" kullanacak şekilde kısıtlayarak, yalnızca yetkili kaynaklarınızın ve güvenilir Microsoft hizmetlerinizin bir depolama hesabına erişmesine izin vermek üzere ağ erişimini sınırlayabilirsiniz.
![Azure depolama ayarları Iletişim kutusu ](./media/roles-permissions-security/secured-storage-example.png) Azure izleyici, bu "Güvenilen Microsoft hizmetlerinden" biri olarak kabul edilir. güvenilen Microsoft hizmetlerinin güvenli depolama hesabınıza erişmesine izin verirseniz Azure izleyici, güvenli depolama hesabınıza erişebilir; Azure izleyici kaynak günlüklerinin, Etkinlik günlüğünün ve ölçümlerin bu korumalı koşullarda depolama hesabınıza yazılmasını sağlar. Bu da Log Analytics, güvenli depolama alanından günlükleri okumasına olanak tanır.   


Daha fazla bilgi için bkz. [ağ güvenliği ve Azure depolama](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Sonraki adımlar
* [Kaynak Yöneticisi Azure RBAC ve izinler hakkında bilgi edinin](../role-based-access-control/overview.md)
* [Azure 'da izlemeye genel bakış konusunu okuyun](overview.md)

