---
title: Azure Cloud Services geçişine yönelik teknik ayrıntılar ve gereksinimler (genişletilmiş destek)
description: Azure Cloud Services (klasik) Azure Cloud Services (genişletilmiş destek) ile geçiş için teknik ayrıntılar ve gereksinimler sağlar
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286997"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Azure Cloud Services 'a geçme teknik ayrıntıları (genişletilmiş destek)   

Bu makalede, Cloud Services (klasik) ile ilgili geçiş aracı hakkında teknik ayrıntılar ele alınmaktadır. 

> [!IMPORTANT]
> Geçiş aracını kullanarak Cloud Services (klasik) Cloud Services (genişletilmiş destek) ' e geçiş Şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Geçiş için desteklenen özellik/senaryolar hakkında ayrıntılar 

### <a name="extensions-and-plugin-migration"></a>Uzantılar ve eklenti geçişi 
- Tüm etkin ve desteklenen uzantılar geçirilecek. 
- Devre dışı bırakılmış uzantılar geçirilmez. 
- Eklentiler eski bir kavramdır ve geçişten önce kaldırılmalıdır. Bunlar geçiş için desteklenir, ancak geçişten sonra uzantının etkinleştirilmesi gerekiyorsa, uzantı yüklenmeden önce eklentinin kaldırılması gerekir. Bu, uzak masaüstü eklentileri ve uzantıları tarafından en çok etkilendi.   
 
### <a name="certificate-migration"></a>Sertifika geçişi
- Cloud Services (genişletilmiş destek) içinde, sertifikalar bir Key Vault depolanır. Geçişin bir parçası olarak, bulut hizmeti adına sahip müşteriler için bir Key Vault oluşturacağız ve Azure Service Manager tüm sertifikaları Key Vault 'e aktardık. 
- Bu Key Vault başvurusu şablonda belirtilmiştir veya PowerShell veya Azure CLı aracılığıyla geçirilir. 

### <a name="service-configuration-and-service-definition-files"></a>Hizmet yapılandırma ve hizmet tanımı dosyaları
- . Cscfg ve. csdef dosyalarının küçük değişikliklerle Cloud Services (genişletilmiş destek) için güncelleştirilmesi gerekir. 
- Sanal ağ ve VM SKU 'SU gibi kaynakların adları farklıdır. Bkz. [kaynakların ve adlandırma kuralının geçiş sonrası çevirisi](#translation-of-resources-and-naming-convention-post-migration)
- Müşteriler [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) ve [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/get)aracılığıyla yeni dağıtımlarını alabilir. 

### <a name="cloud-service-and-deployments"></a>Bulut hizmeti ve dağıtımları
- Her Cloud Services (genişletilmiş destek) dağıtımı bağımsız bir bulut hizmetidir. Dağıtım artık yuvaları kullanarak bir bulut hizmetinde gruplandırılmaz.
- Bulut hizmetinizde (klasik) iki yuva varsa, bir yuva (hazırlama) silmeniz ve diğer (üretim) yuvasını Azure Resource Manager taşımak için geçiş aracını kullanmanız gerekir. 
- Bulut hizmeti dağıtımındaki genel IP adresi, Azure Resource Manager geçişten sonra aynı kalır ve temel bir SKU IP (dinamik veya statik) kaynağı olarak gösterilir. 
- Geçirilen bulut hizmeti için DNS adı ve etki alanı (cloudapp.azure.net) aynı kalır. 

### <a name="virtual-network-migration"></a>Sanal ağ geçişi
- Bir Cloud Services dağıtımı sanal bir ağda ise, geçiş sırasında tüm Cloud Services ve ilişkili sanal ağ kaynakları birlikte geçirilir. 
- Geçişten sonra sanal ağ, bulut hizmetinden farklı bir kaynak grubuna yerleştirilir. 
- Birden çok Cloud Services sahip sanal ağlar için, her bir bulut hizmeti bundan sonra geçirilir. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Sanal ağda bulunmayan dağıtımlar geçişi
- 2017 ' de Azure, "varsayılan" sanal ağı oluşturulan bir platforma otomatik olarak Yeni dağıtımlar (müşteri tarafından belirtilen sanal ağ olmadan) oluşturmaya başladı. Bu varsayılan sanal ağlar müşterilerden gizlenir.   
- Geçişin bir parçası olarak, bu varsayılan sanal ağ Azure Resource Manager ' de müşterilere bir kez sunulur. Azure Resource Manager dağıtımı yönetmek veya güncelleştirmek için, müşterilerin bu sanal ağ bilgilerini. cscfg dosyasının NetworkConfiguration bölümüne eklemesi gerekir.    
- Azure Resource Manager 'e geçirildiğinde varsayılan sanal ağ, bulut hizmeti ile aynı kaynak grubuna yerleştirilir.
- Bu saatten önce oluşturulan Cloud Services hiçbir sanal ağda olmayacaktır ve araç kullanılarak geçirilemez. Bu Cloud Services doğrudan Azure Resource Manager ' de yeniden dağıtmanız göz önünde bulundurun. 
- Dağıtımın geçişe uygun olup olmadığını denetlemek için dağıtımda Validate API 'sini çalıştırın. API 'YI doğrula sonucu, bu dağıtımın geçişe uygun olup olmadığını açıkça içeren hata iletisi içerir.     

### <a name="load-balancer"></a>Load Balancer   
- Ortak uç nokta kullanan bir bulut hizmeti için, bulut hizmetiyle ilişkili bir platform tarafından oluşturulan yük dengeleyici, Azure Resource Manager ' deki müşterinin aboneliği içinde kullanıma sunulur. Yük dengeleyici salt okunurdur ve Güncelleştirmeler yalnızca hizmet yapılandırma (. cscfg) ve hizmet tanımı (. csdef) dosyaları ile kısıtlanır. 

### <a name="key-vault"></a>Key Vault
- Geçişin bir parçası olarak, Azure otomatik olarak yeni bir Key Vault oluşturur ve tüm sertifikaları buna geçirir. Araç mevcut bir Key Vault kullanmanıza izin vermez. 
- Cloud Services (genişletilmiş destek), aynı bölgede ve abonelikte bulunan bir Key Vault gerektirir. Bu Key Vault, geçişin bir parçası olarak otomatik olarak oluşturulur. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Kaynakların ve adlandırma kuralının çevrilmesi geçiş sonrası
Geçişin bir parçası olarak, kaynak adları değiştirilir ve birkaç Cloud Services özelliği Azure Resource Manager kaynak olarak sunulur. Tablo, Cloud Services geçişe özgü değişiklikleri özetler.

| Cloud Services (klasik) <br><br> Kaynak adı | Cloud Services (klasik) <br><br> Syntax| Cloud Services (genişletilmiş destek) <br><br> Kaynak adı| Cloud Services (genişletilmiş destek) <br><br> Syntax | 
|---|---|---|---|
| Bulut Hizmeti | `cloudservicename` | İlişkilendirilmemiş| İlişkilendirilmemiş |
| Dağıtım (portal oluşturuldu) <br><br> Dağıtım (Portal dışı oluşturuldu)  | `deploymentname` | Cloud Services (genişletilmiş destek) | `deploymentname` |  
| Sanal Ağ | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> İlişkilendirilmemiş |  Sanal ağ (portal oluşturulmadı) <br><br> Sanal ağ (portal oluşturuldu) <br><br> Sanal ağlar (varsayılan) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| İlişkilendirilmemiş | İlişkilendirilmemiş | Key Vault | `cloudservicename` | 
| İlişkilendirilmemiş | İlişkilendirilmemiş | Bulut hizmeti dağıtımları için kaynak grubu | `cloudservicename-migrated` | 
| İlişkilendirilmemiş | İlişkilendirilmemiş | Sanal ağ için kaynak grubu | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| İlişkilendirilmemiş | İlişkilendirilmemiş | Genel IP (dinamik) | `cloudservicenameContractContract` | 
| Ayrılmış IP adı | `reservedipname` | Ayrılmış IP (Portal dışı oluşturuldu) <br><br> Ayrılmış IP (portal oluşturuldu) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| İlişkilendirilmemiş| İlişkilendirilmemiş | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Geçiş sorunları ve bunların nasıl işleneceği. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Geçiş bir işlem için uzun bir süre takıldı. 
- Dağıtım sayısına bağlı olarak, COMMIT, Prepare ve Abort uzun zaman alabilir. İşlemler, 24 saat sonra zaman aşımına uğrar.   
- İşleme, hazırlama ve iptal işlemleri ıdempotent. Çoğu sorun yeniden denenerek düzeltilebilir. Birkaç dakika sonra da bir boşluk sonra yeniden denenmeye devam edebilen geçici hatalar olabilir. Azure portal kullanarak geçiş yaptıysanız ve işlem "sürmekte olan" durumunda takıldığında, işlemi yeniden denemek için PowerShell 'i kullanın. 
- Dağıtımı arka uca taşımaya veya geri almanıza yardımcı olması için desteğe başvurun. 

### <a name="migration-failed-in-an-operation"></a>Bir işlemde geçiş başarısız oldu. 
- Doğrulama başarısız olursa, bunun nedeni dağıtımın veya sanal ağın desteklenmeyen bir senaryo/Özellik/kaynak içermesi olabilir. Belgelerde bulunan çalışmayı bulmak için desteklenmeyen senaryolar listesini kullanın.  
- Hazırlama işlemi, bazı pahalı doğrulamalar (Validate 'te kapsanmayan) dahil olmak üzere ilk olarak doğrulaması yapar. Hazırlama hatası, desteklenmeyen bir senaryo nedeniyle olabilir. Ortak belgelerde senaryo ve iş çözümü bulun. Özgün duruma geri dönmek ve güncelleştirme ve silme işlemleri için dağıtımın kilidini açmak üzere iptal 'in çağrılması gerekir.
- İptal başarısız olursa, işlemi yeniden deneyin. Yeniden denemeler başarısız olursa, desteğe başvurun.
- İşleme başarısız olursa, işlemi yeniden deneyin. Yeniden deneme başarısız olursa, desteğe başvurun. Kayıt hatasında bile, dağıtımınıza bir veri düzlemi sorunu olmaması gerekir. Dağıtımınız herhangi bir sorun olmadan müşteri trafiğini işleyebilmelidir. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Hazırlanma sonrasında Portal yenilendi. Deneyim yeniden başlatıldı ve COMMIT veya Abort artık görünmez. 
- Portal geçiş bilgilerini yerel olarak depolar ve bu nedenle yenilemeden sonra bulut hizmeti hazırlama aşamasında olsa bile doğrulama aşamasından başlar.  
- Durdur ve Kaydet düğmesini kullanıma sunmak için Validate ve Prepare adımlarını yeniden getirmek üzere Portal 'ı kullanabilirsiniz. Hatalara neden olmaz.
- Müşteriler, durdurmak veya yürütmek için PowerShell veya REST API 'sini kullanabilir. 

### <a name="how-much-time-can-the-operations-takebr"></a>İşlemler ne kadar zaman alabilir?<br>
Doğrulama hızlı olacak şekilde tasarlanmıştır. Hazırlama en uzun çalışır ve geçirilmekte olan rol örneklerinin toplam sayısına bağlı olarak bir süre sürer. Abort ve COMMIT zaman alabilir, ancak hazırlanmaya kıyasla daha az zaman alır. Tüm işlemler 24 saat sonra zaman aşımına uğrar. 
