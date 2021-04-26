---
title: Azure Cloud Services için sık sorulan sorular (genişletilmiş destek)
description: Azure Cloud Services için sık sorulan sorular (genişletilmiş destek)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 33bafac9247f007978fef568469d643f1a1098df
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383595"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Azure Cloud Services (genişletilmiş destek) hakkında sık sorulan sorular
Bu makalede, Azure Cloud Services ile ilgili sık sorulan sorular (genişletilmiş destek) yer almaktadır.

## <a name="general"></a>Genel

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Cloud Services (klasik) & Cloud Services (genişletilmiş destek) için kaynak adı nedir?
- Cloud Services (klasik): `microsoft.classiccompute/domainnames`
- Cloud Services (genişletilmiş destek): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Cloud Services dağıtmak için hangi konumlar kullanılabilir (genişletilmiş destek)?
Cloud Services (genişletilmiş destek) tüm genel bulut bölgelerinde kullanılabilir.

### <a name="how-does-my-quota-change"></a>Kotam nasıl değişir? 
Müşterilerin, diğer Azure Resource Manager ürünle aynı süreçler kullanarak kota istemesi gerekecektir. Azure Resource Manager kota bölgesel ve her bölge için ayrı bir kota talebi gerekecektir.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Artık üretim & hazırlama yuvasını neden görmüyorum?
Cloud Services (genişletilmiş destek), iki yuva (üretim & hazırlama) dahil olmak üzere barındırılan bir hizmetin mantıksal kavramını desteklemez. Her dağıtım bağımsız bir bulut hizmeti (genişletilmiş destek) dağıtımdır. Bir bulut hizmetinin yeni bir sürümünü test etmek ve hazırlamak için bir bulut hizmeti dağıtın (genişletilmiş destek) ve başka bir bulut hizmeti ile VIP swapolarak etiketleyin (genişletilmiş destek)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Artık boş bir bulut hizmeti neden oluşturamıyorum?
Barındırılan hizmet adları kavramı artık yok, boş bir bulut hizmeti (genişletilmiş destek) oluşturamazsınız.

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Cloud Services (genişletilmiş destek) Kaynak Durumu denetimini (RHC) destekliyor mu?
Hayır, Cloud Services (genişletilmiş destek) Kaynak Durumu denetimini (RHC) desteklemiyor.

### <a name="how-are-role-instance-metrics-changing"></a>Rol örneği ölçümleri nasıl değişiyor?
Rol örneği ölçümlerinde değişiklik yok. 

### <a name="how-are-web--worker-roles-changing"></a>Web & çalışan rolleri nasıl değişiyor?
Web ve çalışan rollerinin tasarımında, mimarisinde veya bileşenlerinde değişiklik yoktur. 

### <a name="how-are-role-instances-changing"></a>Rol örnekleri nasıl değişiyor?
Rol örneklerinin tasarımında, mimarisinde veya bileşenlerinde değişiklik yoktur. 

### <a name="how-will-guest-os-updates-change"></a>Konuk işletim sistemi güncelleştirmeleri nasıl değiştirilir?
 Dağıtım yönteminde değişiklik yok. Cloud Services (klasik) ve Cloud Services (genişletilmiş destek) aynı güncelleştirmeleri alacak.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Cloud Services (genişletilmiş destek) durdurulmuş ve durdurulmuş serbest bırakılmış durumları destekler mi?

Cloud Services (genişletilmiş destek) dağıtımı yalnızca Azure portal "durduruldu" olarak görünen durdurulmuş ayrılmış durumu destekler. Durduruldu-serbest bırakıldı durumu desteklenmiyor. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Do Cloud Services (genişletilmiş destek) dağıtımları kümeler, kullanılabilirlik alanları ve bölgeler genelinde ölçeklendirmeyi destekler mi?
Cloud Services (genişletilmiş destek) dağıtımları birden çok kümede, kullanılabilirlik alanında ve bölgede ölçeklendiremez. 

### <a name="how-can-i-get-the-deployment-id-for-my-cloud-service-extended-support"></a>Bulut hizmetimin dağıtım KIMLIĞINI nasıl alabilirim (genişletilmiş destek)
Dağıtım KIMLIĞI asıka özel KIMLIĞINE [Cloudserviceınstanceview](https://docs.microsoft.com/rest/api/compute/cloudservices/getinstanceview#cloudserviceinstanceview) API kullanılarak erişilebilir. Bulut hizmetinin rol ve örnekler dikey penceresindeki Azure portal de mevcuttur (genişletilmiş destek)

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Cloud Services (klasik) ve Cloud Services (genişletilmiş destek) arasında herhangi bir fiyatlandırma farkı var mı?
Cloud Services (genişletilmiş destek) Azure Key Vault ve temel (ARM) genel IP adreslerini kullanır.Sertifikaların sertifika yönetimi için Azure Key Vault kullanmasına ihtiyaç duyan müşterilerin Azure Key Vault fiyatlandırması hakkında[daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/key-vault/) .   Cloud Services (genişletilmiş destek) için genel IP adresleri ayrı olarak ücretlendirilir (genel IP adresi fiyatlandırması hakkında[daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/ip-addresses/) .) 
## <a name="resources"></a>Kaynaklar 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Bir Cloud Services (genişletilmiş destek) dağıtımına bağlı olan kaynakların aynı kaynak grubunda canlı olması gerekir mi?
Yük dengeleyiciler, ağ güvenlik grupları ve yol tablolarının aynı bölgede ve kaynak grubunda dinamik olması gerekir. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Bir Cloud Services (genişletilmiş destek) dağıtımına bağlı olan kaynakların aynı bölgede olması gerekir mi?
Key Vault, sanal ağ, genel IP adresleri, ağ güvenlik grupları ve yol tablolarının aynı bölgede canlı olması gerekir.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Bir Cloud Services (genişletilmiş destek) dağıtımına bağlı olan kaynakların aynı sanal ağ içinde canlı olması gerekir mi?
Genel IP adresleri, yük dengeleyiciler, ağ güvenlik grupları ve yol tablolarının aynı sanal ağda etkin olması gerekir. 

## <a name="deployment-files"></a>Dağıtım dosyaları 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Dağıtımımı dağıtmak veya yönetmek için bir şablonu nasıl kullanabilirim?
Şablon ve parametre dosyaları REST, PowerShell ve CLı kullanarak bir parametre olarak geçirilebilir. Azure portal kullanarak da karşıya yüklenebilir.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Dört dosyayı şimdi sürdürmem gerekir mi? (şablon, parametre, csdef, cscfg)
Şablon ve parametre dosyaları yalnızca dağıtım otomasyonu için kullanılır. Cloud Services (klasik) gibi, önce bağımlı kaynakları ve ardından PowerShell, CLı komutlarını kullanarak veya mevcut csdef, cscfg ile Portal aracılığıyla bir Cloud Services (genişletilmiş destek) dağıtımını el ile oluşturabilirsiniz.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Uygulama kodumun Cloud Services nasıl değiştirileceği (genişletilmiş destek)
Cspkg 'da paketlenmiş uygulama kodunuz için gereken değişiklik yok. Mevcut uygulamalarınız daha önce olduğu gibi çalışmaya devam edecektir. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Cloud Services (genişletilmiş destek) CTP paket biçimine izin veriyor mu?
CTP paket biçimi Cloud Services desteklenmiyor (genişletilmiş destek). Ancak, 800 MB 'lik gelişmiş bir paket boyutu sınırına izin verir

## <a name="migration"></a>Geçiş

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Cloud Services (genişletilmiş destek), ayırma hatalarından kaynaklanan hataların etkisini azaltır mı?
Hayır, bulut hizmeti (genişletilmiş destek) dağıtımları Cloud Services (klasik) gibi bir kümeye bağlıdır. Bu nedenle, küme doluysa ayırma hatalarının mevcut olmaya devam etmesi gerekir. 

### <a name="when-do-i-need-to-migrate"></a>Ne zaman geçiş yapmam gerekir? 
Gerekli süreyi tahmin etme ve karmaşıklık geçişi, bir dizi değişkene bağlıdır. Planlama, çalışma kapsamını, engelleyiciler ve geçişin karmaşıklığını anlamak için en etkili adımdır.

## <a name="networking"></a>Ağ 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Sanal ağ olmadan neden bir dağıtım oluşturamıyorum?
Sanal ağlar, Azure Resource Manager dağıtım için gerekli bir kaynaktır. Cloud Services (genişletilmiş destek) dağıtımı bir sanal ağ içinde canlı olmalıdır. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Şimdi çok sayıda ağ kaynağını görüyorum mi? 
Azure Resource Manager, Cloud Services (genişletilmiş destek) dağıtımınızın bileşenleri, daha iyi görünürlük ve geliştirilmiş denetim için bir kaynak olarak sunulur. Cloud Services (klasik) ' de aynı kaynak türü kullanılmıştır, ancak bunlar yalnızca gizleniyor. Bu tür bir kaynağa bir örnek, artık platform tarafından otomatik olarak oluşturulan açık bir ' salt okuma ' kaynağı olan genel Load Balancer.

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Cloud Services (genişletilmiş destek) karşılık gelen bir alt ağ için hangi kısıtlamalar uygulanır?
Cloud Services (genişletilmiş destek) dağıtımlarını içeren bir alt ağ, sanal makineler, sanal makineler ölçek kümeleri, Service Fabric vb. gibi diğer işlem ürünlerinden dağıtımlar ile paylaşılamaz.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Bulut hizmetlerinde hangi IP ayırma yöntemlerinin desteklendiği (genişletilmiş destek)?
Cloud Services (genişletilmiş destek) dinamik & statik IP ayırma yöntemlerini destekler. Statik IP adreslerine, cscfg dosyasında ayrılmış IP 'Ler olarak başvurulur.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>IP adresleri için neden ücretlendirildim?
Müşteriler, sanal makinelerle ilişkili IP adresleri için faturalandırıldikleri gibi Cloud Services (genişletilmiş destek) üzerinde IP adresi kullanımı için faturalandırılır. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Cloud Services bir DNS adı kullanabilir miyim (genişletilmiş destek)? 
Evet. Cloud Services (genişletilmiş destek), DNS adı da verilebilir. Azure Resource Manager, DNS etiketi, bulut hizmetine atanan genel IP adresinin isteğe bağlı bir özelliğidir. Azure Resource Manager tabanlı dağıtımlar için DNS adının biçimi `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>Var olan bir bulut hizmeti (genişletilmiş destek) için sanal ağ başvurusunu güncelleştirebilir veya değiştirebilir miyim? 
Hayır. Sanal ağ başvurusu, bir bulut hizmetinin oluşturulması sırasında zorunludur. Mevcut bir bulut hizmeti için, sanal ağ başvurusu değiştirilemez. Sanal ağ adres alanının kendisi VNet API 'Leri kullanılarak değiştirilebilir. 

## <a name="certificates--key-vault"></a>Sertifikalar & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Neden Cloud Services Sertifikalarımı yönetmem gerekiyor (genişletilmiş destek)?
Cloud Services (genişletilmiş destek), sertifikaların müşterinin yönettiği Anahtar kasaları dahilinde bulunduğu diğer işlem teklifleriyle aynı süreci benimsemiştir. Bu, müşterilerin gizli dizileri & sertifikaları üzerinde tamamen denetime sahip olmasını sağlar. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Tüm bölgelerdeki dağıtımlarım için bir Key Vault kullanabilir miyim?
Hayır. Key Vault bölgesel bir kaynaktır ve müşterilerin her bölgede bir Key Vault olması gerekir. Ancak, belirli bir bölgedeki tüm dağıtımlar için bir Key Vault kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
Cloud Services (genişletilmiş destek) kullanmaya başlamak için bkz. [PowerShell kullanarak bulut hizmeti dağıtma (genişletilmiş destek)](deploy-powershell.md)
