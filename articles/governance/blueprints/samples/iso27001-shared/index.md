---
title: ISO 27001 Paylaşılan Hizmetler şema örneği - Genel Bakış
description: ISO 27001 Paylaşılan Hizmetler şema örneğinin genel bakış bilgileri ve mimarisi. Bu şema örneği, müşterilerin belirli ISO 27001 denetimlerini değerlendirmesine yardımcı olur.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: d4fe392cad669179c474c0a6ab12b29eef1f083d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184372"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 Paylaşılan Hizmetler şema örneğine genel bakış

ISO 27001 Paylaşılan Hizmetler şema örneği, ISO 27001 onayı almaya yardımcı olacak bir dizi uyumlu altyapı deseni ve ilke koruması sağlar. Bu şema müşterilerin akreditasyon ve uyumluluk gereksinimleri olan senaryolara çözüm sunan bulut tabanlı mimariler dağıtmasına yardımcı olur.

[ISO 27001 App Service Ortamı/SQL Veritabanı iş yükü](../iso27001-ase-sql-workload/index.md) şema örneği, bu örneği genişletir.

## <a name="architecture"></a>Mimari

ISO 27001 Paylaşılan Hizmetler şema örneği Azure'da temel bir altyapı dağıtır. Bu altyapı kuruluşlar tarafından Sanal Veri Merkezi (VDC) yaklaşımı temelinde birden çok iş yükünü barındırmak için kullanılabilir.
VDC, Microsoft'un en büyük kurumsal müşterileriyle kullandığı başarısı kanıtlanmış bir dizi referans mimarisi, otomasyon aracı ve etkileşim modelidir. Paylaşılan Hizmetler şema örneği aşağıda gösterilen tümüyle yerel bir Azure VDC ortamını temel alır.

![ISO 27001 Paylaşılan Hizmetler şema örneği tasarımı](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

Bu ortam, ISO 27001 standartlarında güvenli, tümüyle izlenen, kurumsal kullanıma hazır bir paylaşılan hizmetler altyapısı sağlamak için kullanılan çeşitli Azure hizmetlerinden oluşur. Bu ortam şunlardan oluşur:

- Denetim düzlemi açısından görevler ayrımı sağlamak için kullanılan [rol tabanlı erişim denetimi](../../../../role-based-access-control/overview.md) (RBAC) rolleri. Herhangi bir altyapı dağıtımından önce üç rol tanımlanır:
  - NetOps rolü güvenlik duvarı ayarları, NSG ayarları, yönlendirme ve diğer ağ işlevselliği de dahil olmak üzere ağ ortamını yönetme haklarına sahiptir
  - SecOps rolü [Azure Güvenlik Merkezi](../../../../security-center/security-center-intro.md)'ni dağıtmak ve yönetmek, [Azure İlkeleri](../../../policy/overview.md) tanımlamak için gereken haklara ve güvenlikle ilgili diğer haklara sahiptir
  - SysOps rolü, diğer işletme haklarının yanı sıra abonelik içinde [Azure İlkeleri](../../../policy/overview.md) tanımlamak, ortamın tamamı için [Log Analytics](../../../../azure-monitor/overview.md)'i yönetmek için gereken haklara sahiptir
- Güvenli dağıtımınıza başladığınız andan itibaren tüm eylemlerin ve hizmetlerin merkezi bir konumda günlüğe kaydedildiğinden emin olmak için, ilk Azure hizmeti olarak [Log Analytics](../../../../azure-monitor/overview.md) dağıtılır
- Şirket içi veri merkezine geri bağlantı için alt ağları destekleyen bir sanal ağ, İnternet bağlantısı için bir giriş ve çıkış yığını ve tam mikro ayrıma yönelik olarak NSG'lerin ve ASG'lerin kullanıldığı paylaşılan bir hizmet alt ağı. Şunları içerir:
  - Yönetim amacıyla kullanılan ve yalnızca giriş yığını alt ağında dağıtılmış [Azure Güvenlik Duvarı](../../../../firewall/overview.md) üzerinden erişilebilen bir atlama kutusu veya kale konağı
  - Yalnızca atlama kutusundan erişilebilen, yalnızca VPN veya [ExpressRoute](../../../../expressroute/expressroute-introduction.md) bağlantısı ve Active Directory Domain Services (ADDS) ile DNS çalıştıran iki sanal makine (şema tarafından dağıtılmaz)
  - [Azure Ağ İzleyicisi](../../../../network-watcher/network-watcher-monitoring-overview.md) ve standart DDoS korumasının kullanılması
- Paylaşılan hizmetler ortamına dağıtılmış VM'lerin gizli dizilerini barındırmak için kullanılan [Azure Key Vault](../../../../key-vault/key-vault-overview.md) örneği

Tam bu öğeler [Azure Mimari Merkezi - Referans Mimarileri](/azure/architecture/reference-architectures/)'nde yayımlanan kanıtlanmış uygulamalara dayanır.

> [!NOTE]
> ISO 27001 Paylaşılan Hizmetler altyapısı iş yükleri için bir mimari temeli oluşturur.
> Bu işlevsel mimarinin arkasında yine iş yüklerini dağıtmanız gerekir.

Daha fazla bilgi edinmek için bkz. [Sanal Veri Merkezi belgeleri](/azure/architecture/vdc/).

## <a name="next-steps"></a>Sonraki adımlar

ISO 27001 Paylaşılan Hizmetler şema örneğinin genel bakış bilgilerini ve mimarisini gözden geçirdiniz.
Şimdi, denetim eşlemesi ve bu örneğin nasıl dağıtılacağı hakkında bilgi edinmek için şu makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [ISO 27001 Paylaşılan Hizmetler şeması - Denetim eşlemesi](./control-mapping.md)
> [ISO 27001 Paylaşılan Hizmetler şeması - Dağıtım adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.