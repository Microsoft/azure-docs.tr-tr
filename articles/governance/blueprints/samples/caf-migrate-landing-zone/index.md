---
title: CAF Geçişi giriş bölgesi şema örneği - Genel bakış
description: CAF Geçişi giriş bölgesi şema örneğinin genel bakış bilgileri ve mimarisi.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: cba1eb6d4761ae8e236f22fa22ff08c129d9637e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038459"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Azure Geçişi giriş bölgesi şema örneği için Microsoft Bulutu Benimseme Çerçevesine genel bakış

Azure Geçişi giriş bölgesi şeması için Microsoft Bulutu Benimseme Çerçevesi (CAF), ilk iş yükünüzü geçirmek üzere ayarlamanıza ve bulut varlığınızı CAF ile uyumlu şekilde yönetmenize yardımcı olan bir altyapı kümesidir.

[CAF Foundation](../caf-foundation/index.md) şema örneği, bu örneği genişletir.

## <a name="architecture"></a>Mimari

CAF Geçişi giriş bölgesi şema örneği, kuruluşlar tarafından aboneliklerini sanal makinelerin geçişine hazırlamak amacıyla kullanılabilen Azure’daki temel altyapı kaynaklarını dağıtır. Ayrıca, bulut varlıklarını yönetmek için gerekli idari denetimleri belirlemelerine de yardımcı olur. Bu örnek, bir kuruluşun Azure’ı güvenle kullanmaya başlamasını sağlayacak kaynakları, ilkeleri ve şablonları dağıtıp uygular.

![CAF Geçişi giriş bölgesi görüntüsü, ilk giriş bölgesi için CAF rehberi kapsamında neyin yüklendiğini tanımlar ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Bu ortam, güvenli, tam olarak izlenen, kurumsal kullanıma hazır yönetim sağlamak amacıyla kullanılan çeşitli Azure hizmetlerinden oluşur. Bu ortam şunlardan oluşur:

- Paylaşılan hizmetler ortamına dağıtılmış Sertifikalar, Anahtarlar ve Gizli Dizilere yönelik gizli dizileri barındırmak üzere kullanılan [Azure Key Vault](../../../../key-vault/key-vault-overview.md) örneği
- Geçiş işleminize başladığınız andan itibaren tüm eylemlerin ve hizmetlerin merkezi bir konumda günlüğe kaydedildiğinden emin olmak amacıyla dağıtılan [Log Analytics](../../../../azure-monitor/overview.md) dağıtımı
- [Azure Güvenlik Merkezi](../../../../security-center/security-center-intro.md) (standart sürüm) dağıtımını yaparak, geçirilen iş yüklerinize yönelik tehdit koruması sağlayın.
- [Azure Sanal Ağı](../../../../virtual-network/virtual-networks-overview.md) dağıtımını yaparak, sanal makineniz için yalıtılmış ağ ve alt ağlar sağlayın.
- Keşif ve değerlendirme için [Azure Geçişi Projesi](../../../..//migrate/migrate-overview.md) dağıtımını yapın. Sunucu değerlendirmesine, Sunucu geçişine, Veritabanı değerlendirmesine ve Veritabanı geçişine yönelik araçları ekliyoruz.  


Tam bu öğeler [Azure Mimari Merkezi - Referans Mimarileri](/azure/architecture/reference-architectures/)'nde yayımlanan kanıtlanmış uygulamalara dayanır.

> [!NOTE]
> CAF Geçişi şeması, iş yüklerinize yönelik bir giriş bölgesi hazırlar. Yine de bu temel mimarinin en üstünde bulunan Sanal Makinelerinizin/Veritabanlarınızın değerlendirme ve geçiş işlemini yapmanız gerekir.

Daha fazla bilgi için bkz. [Azure için Microsoft Bulutu Benimseme Çerçevesi - Geçiş](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Sonraki adımlar

CAF Geçişi giriş bölgesi şema örneğinin genel bakış bilgilerini ve mimarisini gözden geçirdiniz.

> [!div class="nextstepaction"]
>  [CAF Geçişi giriş bölgesi şeması - Dağıtım adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.