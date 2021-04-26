---
title: Azure altyapı izleme
description: Azure üretim ağı 'nın güvenlik açığı taraması gibi altyapı izleme yönleri hakkında bilgi edinin.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87542951"
---
# <a name="azure-infrastructure-monitoring"></a>Azure altyapı izleme   

## <a name="configuration-and-change-management"></a>Yapılandırma ve değişiklik yönetimi
Azure, donanım, yazılım ve ağ cihazlarının yapılandırma ayarlarını ve temel yapılandırmalarını gözden geçirir ve güncelleştirir. Geliştirme ve/veya test ortamından üretim ortamı girilmeden önce değişiklikler geliştirilir, test edilir ve onaylanır.

Azure tabanlı hizmetler için gerekli olan temel yapılandırma, Azure Güvenlik ve uyumluluk ekibi tarafından ve hizmet ekiplerine göre gözden geçirilir. Hizmet ekibi incelemesi, üretim hizmetleri dağıtımıyla önce gerçekleşen testin bir parçasıdır.

## <a name="vulnerability-management"></a>Güvenlik açığı yönetimi
Güvenlik güncelleştirme yönetimi, sistemleri bilinen güvenlik açıklarına karşı korumanıza yardımcı olur. Azure, Microsoft yazılımlarına yönelik güvenlik güncelleştirmelerinin dağıtımını ve yüklenmesini yönetmek için tümleşik dağıtım sistemleri kullanır. Azure, Microsoft Güvenlik Yanıt Merkezi (MSRC) kaynaklarını da çizebilir. MSRC, yılın her gününde saat etrafında güvenlik olaylarını ve bulut güvenlik açıklarını tanımlar, izler ve çözümler.

## <a name="vulnerability-scanning"></a>Güvenlik açığı taraması
Güvenlik açığı taraması, sunucu işletim sistemleri, veritabanları ve ağ cihazlarında gerçekleştirilir. Güvenlik açığı taraması, en az üç aylık olarak gerçekleştirilir. Azure sınırının sızma testini yapmak için bağımsız denetçiler olan Azure sözleşmeleri. Kırmızı takım alıştırmaları de düzenli olarak gerçekleştirilir ve sonuçlar güvenlik geliştirmeleri oluşturmak için kullanılır.

## <a name="protective-monitoring"></a>Koruyucu izleme
Azure güvenliği, etkin izleme için gereksinimleri tanımladı. Hizmet takımları, etkin izleme araçlarını bu gereksinimlere uygun olarak yapılandırır. Etkin izleme araçları Microsoft Monitoring Agent (MMA) ve System Center Operations Manager içerir. Bu araçlar, anında eylem gerektiren durumlarda Azure Güvenlik personeli için zaman uyarıları sağlamak üzere yapılandırılmıştır.

## <a name="incident-management"></a>Olay yönetimi
Microsoft, olaylara yönelik eşgüdümlü bir yanıt sağlamak için bir güvenlik olay yönetimi işlemi uygular, tek bir durum meydana gelir.

Microsoft, kendi donanımına veya tesislerinde depolanan müşteri verilerine yetkisiz erişimi fark edebiliyor ya da müşteri verilerinin kaybolması, açıklanması veya değişmesiyle ilgili olarak bu tür ekipmanlara veya tesislere yetkisiz erişimin farkında olur. Microsoft aşağıdaki işlemleri gerçekleştirir:

- Güvenlik olayının müşterisini hemen bilgilendirir.
- Güvenlik olayını hemen araştırır ve müşterilere güvenlik olayı hakkında ayrıntılı bilgi sağlar.
- , Etkileri azaltmak ve güvenlik olayından kaynaklanan hasarı en aza indirmek için makul ve istem adımları gerçekleştirir.

Rolleri tanımlayan ve sorumlulukları ayıran bir olay yönetimi çerçevesi oluşturulmuştur. Azure Güvenlik olay yönetimi ekibi, yükseltme dahil olmak üzere güvenlik olaylarını yönetmekten ve gerekli olduğunda uzman ekiplerinin katılımına karşı sorumludur. Azure Operations yöneticileri, güvenlik ve gizlilik olaylarının araştırmasını ve çözümlenmesini görmekten sorumludur.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yönelik daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)
