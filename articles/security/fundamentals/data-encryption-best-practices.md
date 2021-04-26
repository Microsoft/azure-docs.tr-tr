---
title: Veri güvenliği ve şifreleme en iyi uygulamaları-Microsoft Azure
description: Bu makalede, yerleşik Azure özellikleri kullanılarak veri güvenliği ve şifreleme için en iyi uygulamalar sağlanır.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: 414445f90c3be4c55166f6a0ecead25074d6ed74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98874122"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Azure veri güvenliği ve şifreleme için en iyi yöntemler
Bu makalede veri güvenliği ve şifreleme için en iyi uygulamalar açıklanmaktadır.

En iyi uygulamalar, görüşlerden oluşan yarışmaları temel alır ve geçerli Azure platformu özellikleri ve özellik kümeleriyle çalışır. Zamanla ve teknolojiler zaman içinde değişir ve bu makale, bu değişiklikleri yansıtacak şekilde düzenli olarak güncelleştirilir.

## <a name="protect-data"></a>Veri koruma
Buluttaki verilerin korunmasına yardımcı olmak için, verilerinizin gerçekleşebileceği olası durumları ve bu durum için hangi denetimlerin kullanılabileceğini hesaba getirmeniz gerekir. Azure veri güvenliği ve şifreleme için en iyi uygulamalar aşağıdaki veri durumlarıyla ilgilidir:

- Bekleyen: buna, manyetik veya optik disk olmasına bakılmaksızın fiziksel medyada statik olarak bulunan tüm bilgi depolama nesneleri, kapsayıcıları ve türleri dahildir.
- Aktarım olarak: bileşenler, konumlar veya programlar arasında veriler aktarıldığında aktarım sürecinde olur. Örnekler, bir Service Bus (Şirket içinden buluta veya ExpressRoute gibi karma bağlantılar da dahil olmak üzere) üzerinden veya bir giriş/çıkış sürecinde ağ üzerinden aktarım yapılır.

## <a name="choose-a-key-management-solution"></a>Anahtar yönetimi çözümü seçin

Anahtarlarınızı korumak, verileri bulutta korumak için gereklidir.

[Azure Key Vault](../../key-vault/general/overview.md), bulut uygulamalarının ve hizmetlerinin kullandığı şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Key Vault, anahtar yönetimi işlemini kolaylaştırır ve verilerinize erişen ve bunları şifreleyen anahtarları denetiminizde tutmanıza olanak sağlar. Geliştiriciler, geliştirme ve test için dakikalar içinde anahtar oluşturabilir ve sonra bunları üretim anahtarlarına geçirebilir. Güvenlik yöneticileri gerektiğinde anahtarlara izin verebilir (ve iptal edebilir).

Kasa olarak adlandırılan birden fazla güvenli kapsayıcı oluşturmak için Key Vault kullanabilirsiniz. Bu kasalar, HSM 'ler tarafından desteklenir. Kasalar, uygulama gizli dizilerinin depolanmasını merkezi hale getirerek güvenlik bilgilerini kazayla kaybetme olasılığını azaltmaya yardımcı olur. Anahtar kasaları ayrıca içlerinde depolanmış her şeye erişimi denetler ve günlüğe kaydeder. Azure Key Vault, Aktarım Katmanı Güvenliği (TLS) sertifikalarını isteği ve yenilemeyi işleyebilir. Sertifika yaşam döngüsü yönetimi için güçlü bir çözüme yönelik özellikler sağlar.

Azure Key Vault, uygulama anahtarlarını ve gizli dizileri desteklemek için tasarlanmıştır. Key Vault, kullanıcı parolaları için bir mağaza olmak üzere tasarlanmamıştır.

Key Vault kullanmak için en iyi güvenlik uygulamaları aşağıda verilmiştir.

**En iyi yöntem**: belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara erişim izni verin.   
**Ayrıntı**: Azure RBAC önceden tanımlı rollerini kullanın. Örneğin, anahtar kasalarını yönetmek üzere bir kullanıcıya erişim izni vermek için, önceden tanımlanmış rolü bu kullanıcıya belirli bir kapsamda [katkıda bulunan Key Vault](../../role-based-access-control/built-in-roles.md) atamalısınız. Bu durumda kapsam bir abonelik, kaynak grubu veya yalnızca belirli bir anahtar kasası olabilir. Önceden tanımlanmış roller gereksinimlerinize uygun değilse, [kendi rollerinizi tanımlayabilirsiniz](../../role-based-access-control/custom-roles.md).

**En iyi yöntem**: kullanıcıların ne erişimi olduğunu denetleyin.   
**Ayrıntı**: bir anahtar kasasına erişim, iki ayrı arabirim aracılığıyla denetlenir: yönetim düzlemi ve veri düzlemi. Yönetim düzlemi ve veri düzlemi erişim denetimleri birbirinden bağımsız olarak çalışır.

Kullanıcıların ne erişimi olduğunu denetlemek için Azure RBAC kullanın. Örneğin, bir uygulama için anahtar kasasındaki anahtarları kullanmak üzere erişim vermek istiyorsanız, yalnızca Anahtar Kasası erişim ilkelerini kullanarak veri düzlemi erişim izinleri vermeniz gerekir ve bu uygulama için hiçbir yönetim düzlemi erişimi gerekmez. Buna karşılık, bir kullanıcının kasa özelliklerini ve etiketlerini okuyabilmesini, ancak anahtarlar, gizlilikler veya sertifikalara erişimleri yoksa, Azure RBAC kullanarak bu kullanıcıya okuma erişimi verebilirsiniz ve veri düzlemi için erişim gerekmez.

**En iyi yöntem**: sertifikaları Anahtar Kasanızda depolayın. Sertifikalarınız yüksek değerde. Yanlış ellerde uygulamanızın güvenliği veya verilerinizin güvenliği tehlikeye girebilir.   
**Ayrıntı**: Azure Resource Manager, VM 'ler dağıtıldığında Azure Key Vault depolanan sertifikaları Azure VM 'lerine güvenli bir şekilde dağıtabilir. Anahtar kasası için uygun erişim ilkelerini ayarlayarak, sertifikanıza kimlerin erişebileceğini de denetleyebilirsiniz. Diğer bir avantaj ise tüm sertifikalarınızı Azure Key Vault’ta yönetmenizdir. Daha fazla bilgi için bkz. [müşteri tarafından yönetilen Key Vault VM 'Lere sertifika dağıtma](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault) .

**En iyi yöntem**: anahtar kasalarını veya Anahtar Kasası nesnelerini silme işlemini kurtarabileceğinizi doğrulayın.   
**Ayrıntı**: anahtar kasalarını veya Anahtar Kasası nesnelerini silme işlemi yanlışlıkla veya kötü amaçlı olabilir. Başta bekleyen verileri şifrelemek için kullanılan anahtarlar için olmak üzere, Key Vault’un yazılım silme ve temizleme koruması özelliklerini etkinleştirin. Bu anahtarların silinmesi veri kaybına eşdeğerdir; bu nedenle, gerekirse silinen kasaları ve kasa nesnelerini kurtarabilirsiniz. Kurtarma işlemlerini düzenli aralıklarla Key Vault uygulama.

> [!NOTE]
> Bir kullanıcının bir Anahtar Kasası yönetim düzleminde katkıda bulunan izinleri (Azure RBAC) varsa, bir Anahtar Kasası erişim ilkesi ayarlayarak kendilerine veri düzlemine erişim izni verebilir. Anahtar kasalarınıza, anahtar kasalarınıza, Anahtarlarınıza, sırlarına ve sertifikalara yalnızca yetkili kişilerin erişebildiğinden ve bunları yönetmesine emin olmak için, anahtar kasalarınıza katkıda bulunan kimlerin erişimi olduğunu sıkı bir şekilde denetlemenizi öneririz.
>
>

## <a name="manage-with-secure-workstations"></a>Güvenli iş istasyonları ile yönetme

> [!NOTE]
> Abonelik Yöneticisi veya sahibi, güvenli bir erişim iş istasyonu veya ayrıcalıklı erişim iş istasyonu kullanmalıdır.
>
>

Saldırıların büyük çoğunluğunun son kullanıcıyı hedeflemesini sağladığından, uç nokta birincil saldırı noktalarından biri haline gelir. Uç noktayı kapatan bir saldırgan, kullanıcının kimlik bilgilerini kullanarak kuruluşun verilerine erişim elde edebilir. Çoğu uç nokta saldırısı, kullanıcıların kendi yerel iş istasyonlarında yönetici oldukları gerçeden yararlanır.

**En iyi yöntem**: hassas hesapları, görevleri ve verileri korumak için güvenli bir yönetim iş istasyonu kullanın.   
**Ayrıntı**: İş istasyonlarındaki saldırı yüzeyini azaltmak için [ayrıcalıklı erişim iş istasyonu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) kullanın. Bu güvenli yönetim iş istasyonları, bu saldırıların bazılarını azaltmanıza ve verilerinizin daha güvenli olmasını sağlamanıza yardımcı olabilir.

**En iyi yöntem**: Endpoint Protection 'ı doğrulayın.   
**Ayrıntı**: veri konumundan (bulut veya şirket içi) bağımsız olarak verileri tüketmek için kullanılan tüm cihazlarda güvenlik ilkelerini zorunlu tutun.

## <a name="protect-data-at-rest"></a>Bekleyen verileri koruma

[Bekleyen veri şifrelemesi](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) , veri gizliliği, uyumluluk ve veri egemenlik 'e yönelik zorunlu bir adımdır.

**En iyi yöntem**: verilerinizin korunmasına yardımcı olmak için disk şifrelemeyi uygulayın.   
**Ayrıntı**: [Azure disk şifrelemesi](./azure-disk-encryption-vms-vmss.md)'ni kullanın. BT yöneticilerinin Windows ve Linux IaaS VM disklerini şifrelemesini sağlar. Disk şifrelemesi, işletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere sektör standardı Windows BitLocker özelliğini ve Linux dm-crypt özelliğini birleştirir.

Azure depolama ve Azure SQL veritabanı, varsayılan olarak bekleyen verileri şifreler ve birçok hizmet şifreleme seçeneğini bir seçenek olarak sunar. Verilerinize erişen ve bunları şifreleyen anahtarların denetimini ele almak için Azure Key Vault kullanabilirsiniz. [Daha fazla bilgi edinmek için bkz. Azure kaynak sağlayıcıları şifreleme modeli desteği](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**En iyi uygulamalar**: yetkisiz veri erişimiyle ilgili riskleri azaltmaya yardımcı olması için şifrelemeyi kullanın.   
**Ayrıntı**: hassas verileri bunlara yazmadan önce sürücülerinizi şifreleyin.

Veri şifrelemeyi zorlayamayan kuruluşlar, veri gizliliği sorunlarına daha fazla açıktır. Örneğin, yetkisiz veya standart dışı kullanıcılar verileri güvenliği aşılmış hesaplarda çalabilir veya düz biçimde kodlanmış verilere yetkisiz erişim elde edebilir. Şirketler aynı zamanda, sektör düzenlemelerine uymak üzere veri güvenliğini geliştirmek için doğru güvenlik denetimlerini kullandıklarından emin olmalıdır.

## <a name="protect-data-in-transit"></a>Aktarılan verileri koruma

Aktarımdaki verilerin korunması veri koruma stratejinizin temel parçalarından biri olmalıdır. Veriler birçok konum arasında gidip geldiğinden, farklı konumlar arasındaki veri alışverişinde her zaman SSL/TLS protokollerini kullanmanızı öneririz. Bazı durumlarda VPN kullanarak şirket içi ile bulut altyapılarınız arasındaki iletişim kanalının tamamını yalıtmak isteyebilirsiniz.

Şirket içi altyapınızla Azure arasında taşınan veriler için HTTPS veya VPN gibi uygun korumaları göz önünde bulundurun. Bir Azure sanal ağı ile şirket içi bir konum arasında, genel İnternet üzerinden şifrelenmiş trafik gönderirken [Azure VPN Gateway](../../vpn-gateway/index.yml)kullanın.

Azure VPN Gateway, SSL/TLS ve HTTPS kullanımına özgü en iyi uygulamalar aşağıda verilmiştir.

**En iyi yöntem**: şirket içinde bulunan birden çok Iş istasyonundan Azure sanal ağına güvenli erişim.   
**Ayrıntı**: [siteden siteye VPN](../../vpn-gateway/tutorial-site-to-site-portal.md)kullanın.

**En iyi yöntem**: şirket içinde bulunan bireysel bir Iş istasyonundan Azure sanal ağına güvenli erişim.   
**Ayrıntı**: [Noktadan siteye VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)kullanın.

**En iyi yöntem**: adanmış bir yüksek hızlı WAN bağlantısı üzerine daha büyük veri kümeleri taşıyın.   
**Ayrıntı**: [ExpressRoute](../../expressroute/expressroute-introduction.md)kullanın. ExpressRoute kullanmayı seçerseniz ek koruma için SSL/TLS veya başka protokoller kullanarak verileri uygulama düzeyinde de şifreleyebilirsiniz.

**En iyi yöntem**: Azure Portal üzerinden Azure depolama ile etkileşim kurun.   
**Ayrıntı**: tüm işlemler https üzerinden gerçekleşir. [Azure depolama](https://azure.microsoft.com/services/storage/)ile etkileşim kurmak için [depolama REST API](/rest/api/storageservices/) https üzerinden de kullanabilirsiniz.

Yoldaki verileri koruyamadığı kuruluşlar, [ortadaki adam saldırıları](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), [gizlice dinleme](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))ve oturum ele geçirme konusunda daha açıktır. Bu saldırılar gizli verilere erişim kazanmanın ilk adımı olabilir.

## <a name="secure-email-documents-and-sensitive-data"></a>Güvenli e-posta, belgeler ve hassas veriler

Şirketiniz dışında paylaştığınız e-posta, belge ve hassas verileri denetlemek ve güvenli hale getirmek istiyorsunuz. [Azure Information Protection](/azure/information-protection/), kuruluşların belgelerini ve e-postalarını sınıflandırmasına, etiketlemesine ve korumasına yardımcı olan bulut tabanlı bir çözümdür. Bu, kuralları ve koşulları tanımlayan yöneticiler tarafından otomatik olarak, kullanıcılar tarafından el ile veya kullanıcıların öneriler aldığı bir birleşimde otomatik olarak yapılabilir.

Verilerin nerede depolandığını veya paylaşıldığına bakılmaksızın sınıflandırma her zaman tanımlanabilir. Etiketler üst bilgi, alt bilgi veya filigran gibi görsel işaretler içerir. Dosyalara ve e-posta üst bilgilerine düz metin olarak meta veriler eklenir. Şifresiz metin, veri kaybını önleyen çözümler gibi diğer hizmetlerin sınıflandırmayı tanımlayabilir ve uygun eylemi gerçekleştirebilir.

Koruma teknolojisi Azure Rights Management (Azure RMS) kullanır. Bu teknoloji, Microsoft 365 ve Azure Active Directory gibi diğer Microsoft bulut hizmetleri ve uygulamalarıyla tümleşiktir. Bu koruma teknolojisinde şifreleme, kimlik ve yetkilendirme ilkeleri kullanılır. Azure RMS ile uygulanan koruma, "kuruluşunuzun, ağların, dosya sunucularınızın ve uygulamalarınızın içindeki veya dışındaki belge ve e-postalarla birlikte kalır.

Bu bilgi koruma çözümü, diğer kişilerle paylaşıldığında bile verilerinizi denetlemenize devam eder. Ayrıca, bu uygulamaların ve çözümlerin şirket içinde veya bulutta olup olmadığı yazılım satıcılarından kendi iş kolu uygulamalarınızı ve bilgi koruma Çözümlerinizi Azure RMS de kullanabilirsiniz.

Şunları yapmanızı öneririz:

- Kuruluşunuz için [Azure Information Protection dağıtın](/azure/information-protection/deployment-roadmap) .
- İş gereksinimlerinizi yansıtan Etiketler uygulayın. Örneğin: bu verileri sınıflandırmak ve korumak için en üst düzey verileri içeren tüm belgelere ve e-postalara "çok gizli" adlı bir etiket uygulayın. Daha sonra, yalnızca yetkili kullanıcılar bu verilere, belirttiğiniz tüm kısıtlamalara sahip erişebilir.
- Kuruluşunuzun koruma hizmetini nasıl kullandığını izleyebilmeniz [için Azure RMS için kullanım günlüğünü](/azure/information-protection/log-analyze-usage) yapılandırın.

Veri [sınıflandırması](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) ve dosya koruması zayıf kuruluşlar, veri sızıntısı veya veri kötüye kullanımı konusunda daha açıktır. Doğru dosya korumasıyla, işletmenizin içgörüleri sağlamak, riskli davranışları tespit etmek ve düzeltici önlemler almak, belgelere erişimi izlemek vb. için veri akışlarını analiz edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .

Aşağıdaki kaynaklar, Azure güvenliği ve ilgili Microsoft hizmetleri hakkında daha genel bilgiler sağlamak için kullanılabilir:
* Azure [güvenlik ekibi blogu](/archive/blogs/azuresecurity/) -Azure güvenliği ile ilgili en son bilgiler için
* [Microsoft Güvenlik](https://technet.microsoft.com/library/dn440717.aspx) açıkları, Azure ile ilgili sorunlar da dahil olmak üzere Microsoft güvenlik açıklarına göre bildirilebilir veya e-posta ile secure@microsoft.com