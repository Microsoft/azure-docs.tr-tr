---
title: Azure Cloud Services 'e geçiş sırasında sık karşılaşılan hatalar ve bilinen sorunlar (genişletilmiş destek)
description: Cloud Services (klasik) ile bulut hizmeti arasında geçiş yaparken yaygın hatalara genel bakış (genişletilmiş destek)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287038"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Azure Cloud Services 'e geçiş sırasında sık karşılaşılan hatalar ve bilinen sorunlar (genişletilmiş destek)

Bu makalede Cloud Services (klasik) ' den Cloud Services (genişletilmiş destek) ' den geçiş yaparken karşılaşabileceğiniz bilinen sorunlar ve sık karşılaşılan hatalar ele alınmaktadır. 

## <a name="known-issues"></a>Bilinen sorunlar
Aşağıdaki sorunlar bilinmektedir ve ele alınıyor.

| Bilinen sorunlar | Risk azaltma | 
|---|---|
| Başarılı işlemeden sonra, rol örnekleri ud tarafından yeniden başlatılıyor. | Yeniden başlatma işlemi, aylık Konuk işletim sistemi piyasaya çıkarma ile aynı yöntemi izler. Bulut hizmetlerinin geçişini tek rol örneği ile veya yeniden başlatma tarafından etkilenmeyin.| 
| Azure portal, tarayıcı yeniledikten sonra geçiş durumunu okuyamıyor. | Özgün geçiş durumuna geri dönmek için Validate ve Prepare işlemini yeniden çalıştırın. | 
| Sertifika, Anahtar Kasası 'nda gizli kaynak olarak gösterilir. | Geçişten sonra, Cloud Services güncelleştirme işlemini basitleştirmek için sertifikayı bir sertifika kaynağı olarak yeniden yükleyin (genişletilmiş destek). | 
| Dağıtım etiketleri, geçişin bir parçası olarak Etiketler olarak kaydedilmez. | Bu bilgileri korumak için geçişten sonra etiketleri el ile oluşturun.
| Kaynak grubu adı tümü büyük. | Etkilenmeyen. Çözüm henüz kullanılamıyor. |
| Cloud Services kilidinin (genişletilmiş destek) kilidinin adı yanlış. | Etkilenmeyen. Çözüm henüz kullanılamıyor. | 
| Cloud Services (genişletilmiş destek) portalı dikey penceresinde IP adresi adı yanlış. | Etkilenmeyen. Çözüm henüz kullanılamıyor. | 
| Geçirilen bir bulut hizmetindeki güncelleştirme işleminden sonra sanal IP adresi için geçersiz DNS adı gösteriliyor. | Etkilenmeyen. Çözüm henüz kullanılamıyor. | 
| Başarılı bir şekilde hazırladıktan sonra yeni bir Cloud Services (genişletilmiş destek) dağıtımını takas özellikli olarak bağlamaya izin verilmez. | Yeni bir bulut hizmetini hazırlanan bir bulut hizmetine takas olarak bağlamayın. | 
| Hata iletilerinin güncellenmesi gerekiyor. | Etkilenmeyen. | 

## <a name="common-migration-errors"></a>Sık karşılaşılan geçiş hataları
Sık karşılaşılan geçiş hataları ve risk azaltma adımları. 

| Hata iletisi | Ayrıntılar | 
|---|---|
| `Microsoft.Compute`' 2020-10-01-Preview ' API sürümü için ad alanında kaynak türü bulunamadı. | Ortak önizlemeye erişmek için CloudServices için abonelik özelliği bayrağını [kaydedin](in-place-migration-overview.md#setup-access-for-migration) . | 
| Sunucu bir iç hatayla karşılaştı. İsteği yeniden deneyin. | İşlemi yeniden deneyin, [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) kullanın veya desteğe başvurun. | 
| Sunucu, bulut hizmeti için ağ kaynaklarını ayırmaya çalışırken beklenmeyen bir hatayla karşılaştı. İsteği yeniden deneyin. | İşlemi yeniden deneyin, [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) kullanın veya desteğe başvurun. | 
| Dağıtım dağıtımı-bulut hizmeti bulutu-hizmet adı, geçirilecek bir sanal ağ içinde olmalıdır. | Dağıtım bir sanal ağda yer alan değil. Daha fazla ayrıntı için [Bu](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) belgeye bakın. | 
| Dağıtım dağıtımını geçirme-bulut hizmeti bulutu-hizmet adı bölge adı bölgesinde olduğundan adı desteklenmiyor. İzin verilen bölgeler: [kullanılabilir bölgelerin listesi]. | Bölge, geçiş için henüz desteklenmiyor. | 
| Rol adı rolüyle ilişkili alt ağ olmadığından, bulut hizmeti bulutu-Service-Name dağıtım dağıtımı-adı geçirilemiyor. Tüm rolleri bir alt ağ ile ilişkilendirin, sonra bulut hizmeti geçişini yeniden deneyin. | Geçiş işleminden önce bir alt ağa yerleştirerek bulut hizmeti (klasik) dağıtımını güncelleştirin. |  
| Dağıtım dağıtımı-bulut hizmeti bulutu-Service-Name içindeki ad geçirilemiyor çünkü dağıtım, Azure Resource Manager abonelikte kayıtlı en az bir özellik gerektirdiğinden geçirilemez. Bu dağıtımı geçirmek için gerekli tüm özellikleri kaydedin. Eksik özellik: [eksik özellikler listesi]. | Özellik bayraklarını kayıtlı almak için desteğe başvurun. | 
| Dağıtımın bulut hizmetinde iki adet boş yuva olduğundan dağıtım geçirilemiyor. Bulut hizmetlerinin geçirilmesi yalnızca bulut hizmetindeki tek dağıtım olan dağıtımlar için desteklenir. Bu dağıtımın geçişine devam etmek için bulut hizmetindeki diğer dağıtımı silin. | Daha fazla ayrıntı için [Desteklenmeyen senaryo](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) listesine bakın. | 
| Dağıtım dağıtımı-HostedService bulutu-Service-Name içinde adı ara durumunda: State. Geçişe izin verilmiyor. | Dağıtım oluşturuluyor, silindi veya güncelleştirildi. İşlemin tamamlanmasını bekleyin ve yeniden deneyin. | 
| Dağıtım dağıtımı-barındırılan hizmet bulutu-Service-Name içindeki adı ayrılmış IP 'leri ve ayrılmış IP adı içermiyor. Bu sorunu çözmek için, ayrılmış IP adını güncelleştirin veya Microsoft Azure Service Desk ile iletişim kurun. | Bulut hizmeti dağıtımını güncelleştirin. | 
| Dağıtım dağıtımı-barındırılan hizmet bulutu-hizmet adı 'nda ayrılmış IP (lar) ayrılmış IP adı, ancak ayrılmış IP 'de uç nokta yok. Bu sorunu çözmek için, ayrılmış IP 'ye en az bir uç nokta ekleyin. | Uç noktayı ayrılmış IP 'ye ekleyin. | 
| HostedService 'e dağıtım geçişi {0} {1} , yürütme sürecinde ve başarıyla tamamlanana kadar değiştirilemez.  | Bekle veya yeniden dene işlemi. | 
| {0}HostedService 'Te dağıtımın geçirilmesi durduruluyor {1} ve işlem başarıyla tamamlanana kadar değiştirilemez. | Bekle veya yeniden dene işlemi. |
| HostedService 'te dağıtımdaki bir veya daha fazla VM {0} {1} , bir güncelleştirme işlemi yaşıyor. Önceki işlem başarıyla tamamlanana kadar geçirilemez. Bir süre sonra yeniden deneyin. | İşlemin tamamlanmasını bekleyin. | 
| {0}Şu özellikleri kullandığından HostedService dağıtımı için geçiş desteklenmiyor {1} : VNET olmayan dağıtım için henüz desteklenmeyen şu özellikleri kullanıyor.| Dağıtım bir sanal ağda yer alan değil. Daha fazla ayrıntı için [Bu](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) belgeye bakın. | 
| Sanal ağ adı null veya boş olamaz. | REST istek gövdesinde sanal ağ adı sağlayın | 
| Alt ağ adı null veya boş olamaz. | REST istek gövdesinde alt ağ adı sağlayın. | 
| DestinationVirtualNetwork şu değerlerden birine ayarlanmalıdır: Default, New veya Existing. | REST istek gövdesinde DestinationVirtualNetwork özelliği sağlayın. | 
| Varsayılan VNet hedefi seçeneği uygulanmadı. | REST istek gövdesinde DestinationVirtualNetwork özelliği için "default" değeri desteklenmez. | 
| {0}CSPKG kullanılamadığından dağıtım geçirilemiyor. | Dağıtımı yükseltin ve yeniden deneyin. | 
| ' ' KIMLIĞINE sahip alt ağ, barındırılan ' ' {0} hizmetindeki ' ' dağıtımından farklı bir konumda {1} {2} . Alt ağ konumu ' {3} ' ve barındırılan hizmetin konumu ' {4} '.  Dağıtımla aynı konumda bir alt ağ belirtin. | Geçiş işleminden önce, bulut hizmetini hem alt ağa hem de bulut hizmetine aynı konumda olacak şekilde güncelleştirin. | 
| {0}HostedService 'Te dağıtımın geçirilmesi durduruluyor {1} ve işlem başarıyla tamamlanana kadar değiştirilemez. | İptal işleminin tamamlanmasını bekleyin veya durdurmayı yeniden deneyin. [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) kullanın veya başka bir destek ekibiyle iletişime geçin. | 
| {0}HostedService 'te dağıtım {1} geçiş için hazırlanmadı. | İşleme işlemini çalıştırmadan önce bulut hizmeti üzerinde hazırla ' yı çalıştırın. | 
| Unknownexceptionınendexyürütüldüğünde: Contract. onaylama başarısız oldu: rgName null ya da boş: Endexlationiçinde RdfeException olmayan özel durum alındı. |   [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) kullanın veya desteğe başvurun. | 
| Unknownexceptionınendexsınılation: bir görev iptal edildi: Endexyürütüldüğünde bir RdfeException olmayan özel durum alındı. | [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) kullanın veya desteğe başvurun. | 
| XrpVirtualNetworkMigrationError: sanal ağ geçiş hatası. | [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) kullanın veya desteğe başvurun. | 
| {0}HostedService 'te dağıtım {1} sanal ağa aittir {2} . {2}Bu HostedService 'i geçirmek Için sanal ağ geçişini yapın {1} . | [Sanal ağ geçişi](in-place-migration-technical-details.md#virtual-network-migration)' ne bakın. | 
| Azure Resource Manager içindeki kaynak adı için geçerli kota, geçişi tamamlamaya yetecek kadar yetersiz. Geçerli kota, {0} gerekli ek {1} . Kotayı yükseltmek ve kota oluşturulduktan sonra geçişi yeniden denemek için bir destek isteği dosyası yapın.    | Kota artışı istemek için uygun kanalları izleyin: <br>[Ağ kaynakları için kota artışı](../azure-portal/supportability/networking-quota-requests.md) <br>[İşlem kaynakları için kota artışı](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Sonraki adımlar
Geçiş gereksinimleri hakkında daha fazla bilgi için bkz. [Azure 'a geçiş hakkında teknik ayrıntılar Cloud Services (genişletilmiş destek)](in-place-migration-technical-details.md)
