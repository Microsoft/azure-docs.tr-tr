---
title: PCI-DSS v 3.2.1 şema örnek denetimi eşlemesi
description: Ödeme kartı sektör verileri güvenliği standart v 3.2.1 şema örneğinin Azure ilkesi ve RBAC 'ye eşlenmesini denetleme.
ms.date: 06/24/2019
ms.topic: conceptual
ms.openlocfilehash: d3e72f923ea3d752d829731d1f741bda090ae9fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037276"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI DSS v 3.2.1 şema örneğinin denetim eşlemesi

Aşağıdaki makalede, Azure şemaları PCI-DSS v 3.2.1 şema örneğinin PCI-DSS v 3.2.1 denetimleriyle nasıl eşleştiği açıklanır. Denetimler hakkında daha fazla bilgi için bkz. [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Aşağıdaki eşlemeler **PCI-DSS v 3.2.1:2018** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Daha sonra,\[önizlemeyi bulun ve seçin **\] PCI v 3.2.1:2018 denetimleri ve belırlı VM uzantılarını dağıtarak denetim gereksinimleri** yerleşik ilke girişimi ' ni destekler.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ve bir veya daha fazla ilke arasında genellikle bir 1:1 veya bir eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md)bakın.

## <a name="132-and-134-boundary-protection"></a>1.3.2 ve 1.3.4 sınır koruması

Bu şema, ağ güvenlik gruplarını, izin veren kurallarla izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ağları yönetmenize ve denetlemenize yardımcı olur. Çok izin verilen kurallar istenmeyen ağ erişimine izin verebilir ve incelenmelidir. Bu şema, korumasız uç noktaları, uygulamaları ve depolama hesaplarını izleyen bir Azure ilke tanımı atar. Bir güvenlik duvarı tarafından korunmayan uç noktalar ve uygulamalar ve Kısıtlanmamış erişimi olan depolama hesapları, bilgi sisteminde bulunan bilgilere istenmeden erişime izin verebilir.

- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme
- Internet 'e yönelik uç nokta ile erişim kısıtlı olmalıdır

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h ve 6.5.3 şifreleme koruması

Bu şema, belirli bir cryptograph denetimi ve zayıf şifreleme ayarları kullanımını zorlayan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak, ilkenizi cryptograph denetimleri ile zorlamanıza yardımcı olur. Azure kaynaklarınızın en iyi durumda olmayan şifreleme yapılandırmalarının nerede olabileceğini anlamak, kaynakların bilgi güvenliği ilkenize uygun şekilde yapılandırıldığından emin olmak için düzeltici eylemler almanıza yardımcı olabilir. Özellikle, bu şema tarafından atanan ilkeler SQL veritabanlarında saydam veri şifrelemesi gerektirir; depolama hesaplarında eksik şifrelemeyi denetleyin ve Otomasyon hesabı değişkenleri. Ayrıca, depolama hesaplarına, Işlev uygulamalarına, WebApp, API Apps ve Redis Cache güvenli olmayan bağlantıları denetlemeye yönelik ilkeler de vardır ve şifrelenmemiş Service Fabric iletişimini denetleyebilir.

- İşlev uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir
- Disk şifrelemesi sanal makinelere uygulanmalıdır
- Otomasyon hesabı değişkenleri şifrelenmelidir
- Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir
- Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir
- Service Fabric kümelerinde ClusterProtectionLevel özelliği EncryptAndSign olarak ayarlanmalıdır
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir
- SQL DB saydam veri şifrelemesini dağıtma

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 ve 11.2.1 güvenlik açığı taraması ve sistem güncelleştirmeleri

Bu şema, Azure Güvenlik Merkezi 'nde eksik sistem güncelleştirmelerini, işletim sistemi güvenlik açıklarını, SQL güvenlik açıklarını ve sanal makine güvenlik açıklarını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi güvenlik açıklarını yönetmenize yardımcı olur. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumu hakkında gerçek zamanlı Öngörüler elde etme olanağı sunan raporlama özellikleri sağlar.

- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme
- Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt
- SQL Server 'Lar üzerinde tehdit algılamayı dağıtma
- Sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 ve 7.1.3 ayrımı

Yalnızca bir Azure aboneliğinin sahibi, yönetici artıklığına izin vermez. Bunun tersine, çok fazla sayıda Azure aboneliği sahibi, güvenliği aşılmış bir sahip hesabı aracılığıyla ihlal olasılığını artırabilir. Bu şema, Azure abonelikleri için sahip sayısını denetim altına alarak Azure [ilke](../../../policy/overview.md) tanımlarını atayarak uygun sayıda Azure abonelik sahibini korumanıza yardımcı olur. Abonelik sahibi izinlerinin yönetilmesi, görevlerin uygun bir şekilde ayrılmasını sağlamanıza yardımcı olabilir.

- Aboneliğinize birden fazla sahip atanmalıdır
- Aboneliğiniz için en fazla 3 sahip belirtilmelidir 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a ve 8.3.1. b ayrıcalıklı erişim haklarının yönetimi

Bu şema, sahip olan dış hesapları, sahip ve/veya Okuma izinlerini ve/veya çok faktörlü kimlik doğrulaması etkinleştirilmemiş yazma izinleri ile çalışan hesaplarını denetlemek için [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ayrıcalıklı erişim haklarını kısıtlayıp denetlemenize yardımcı olur. Azure, Azure kaynaklarına kimlerin erişebileceğini yönetmek için rol tabanlı erişim denetimi (RBAC) uygular. Özel RBAC kurallarının hangi noktada uygulanacağını anlamak, özel RBAC kuralları hata durumunda olduğundan, gereksinimi ve uygun uygulamayı doğrulamanıza yardımcı olabilir. Bu şema Ayrıca, SQL sunucuları için Azure Active Directory kimlik doğrulamasının kullanımını denetlemek üzere [Azure ilke](../../../policy/overview.md) tanımları atar. Azure Active Directory kimlik doğrulaması kullanmak, izin yönetimini basitleştirir ve veritabanı kullanıcılarının ve diğer Microsoft kimlik yönetimini merkezileştirir  
servislere.
 
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde yazma izinleri olan hesaplar etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir
- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır
- Özel RBAC kurallarının kullanımını denetleme

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 sürümlü ve 8.1.5 en az ayrıcalık ve Kullanıcı erişim haklarının Incelenmesi

Azure, Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olmak için rol tabanlı erişim denetimi (RBAC) uygular. Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema, yönetici [Hesap tanımlarını,](../../../policy/overview.md) hesap ve yükseltilmiş izinlere sahip harici hesaplar dahil olmak üzere, gözden geçirilmek üzere önceliklendirilmek zorunda olan denetim hesaplarına atar.

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 kaldırma veya erişim haklarını ayarlama

Azure, Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olmak için rol tabanlı erişim denetimi (RBAC) uygular. Azure Active Directory ve RBAC kullanarak, Kullanıcı rollerini kurumsal değişiklikleri yansıtacak şekilde güncelleştirebilirsiniz. Gerektiğinde, hesapların oturum açması (veya kaldırılması) engellenebilir ve bu da Azure kaynaklarına erişim haklarını hemen kaldırır. Bu şema, kaldırma için göz önünde bulundurulmaları için [Azure ilke](../../../policy/overview.md) tanımlarını denetim altına alınır

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b ve 8.2.5 parola tabanlı kimlik doğrulaması

Bu şema, en düşük güç ve diğer parola gereksinimlerini zorlayamama Windows VM 'Leri denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak güçlü parolalar zorlamanıza yardımcı olur. Parola gücü ilkesini ihlal eden VM 'lerin farkında, tüm VM Kullanıcı hesaplarının parolalarının ilkeyle uyumlu olduğundan emin olmak için düzeltici eylemler almanıza yardımcı olur.

- \[Preview\]: en fazla 70 gün parola yaşı olmayan Windows VM 'Leri denetleyin
- \[Preview\]: en fazla 70 gün parola yaşı olmayan Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[Preview\]: en az parola uzunluğu 14 karakter olan Windows VM 'lerini denetleyin
- \[Preview\]: en az parola uzunluğu 14 karakter olan Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[Preview\]: önceki 24 parolanın yeniden kullanılmasına izin veren Windows VM 'lerini denetleyin
- \[Preview\]: önceki 24 parolanın yeniden kullanılmasına izin veren Windows sanal makinelerini denetlemek için gereksinimleri dağıtın

## <a name="103-and-1054-audit-generation"></a>10,3 ve 10.5.4 denetim oluşturma

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen [Azure ilke](../../../policy/overview.md) tanımları atanarak sistem olaylarının günlüğe kaydedildiğinden emin olmanıza yardımcı olur.
Tanılama günlükleri, Azure kaynakları içinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Azure günlükleri, kaynak genelinde olayların zaman bağıntılı bir kaydını oluşturmak için eşitlenmiş iç saatleri kullanır.

- SQL Server üzerindeki gelişmiş veri güvenliği ayarlarında denetim etkinleştirilmelidir
- Tanılama ayarını denetle
- SQL Server Level denetim ayarlarını denetleme
- SQL Server 'lar üzerinde denetim dağıtma
- Depolama hesaplarının yeni Azure Resource Manager kaynaklarına geçirilmesi gerekir
- Sanal makinelerin yeni Azure Resource Manager kaynaklara geçirilmesi gerekir

## <a name="1236-and-1237-information-security"></a>12.3.6 ve 12.3.7 Information Security

Bu şema, kabul edilebilir ağ konumlarını ve ortamda izin verilen onaylanan şirket ürünlerini denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ağınızı yönetmenize ve denetlemenize yardımcı olur. Bunlar, her bir şirket tarafından bu ilkelerin her biri içindeki ilke parametreleri aracılığıyla özelleştirilebilir.

- İzin verilen konumlar
- Kaynak grupları için izin verilen konumlar

## <a name="next-steps"></a>Sonraki adımlar

PCI-DSS v 3.2.1 Blueprint denetim eşlemesini gözden geçirdiğinize göre, genel bakış ve bu örneği dağıtma hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 şema-genel bakış](./index.md)
> [PCI-DSS v 3.2.1 şema-Deploy adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.