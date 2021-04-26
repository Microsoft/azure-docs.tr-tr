---
title: Azure Kaynak taşıyıcısı ile bölgeler arasında Azure SQL kaynaklarını taşıma desteği.
description: Azure Kaynak taşıyıcısı ile bölgeler arasında Azure SQL kaynaklarını taşıma desteğini gözden geçirin.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452094"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure bölgeleri arasında Azure SQL kaynaklarını taşıma desteği

Bu makale Azure Kaynak taşıyıcısı ile Azure bölgeleri arasında Azure SQL kaynaklarını taşımaya yönelik destek ve önkoşulları özetler.

## <a name="requirements"></a>Gereksinimler

Gereksinimler aşağıdaki tabloda özetlenmiştir.

**Özellik** | **Desteklenir/desteklenmez** | **Ayrıntılar**
--- | --- | ---
**Azure SQL veritabanı hiper ölçek** | Desteklenmez | Kaynak taşıyıcısı ile Azure SQL hiper ölçek hizmeti katmanında veritabanları taşınamaz.
**Bölge yedekliliği** | Desteklenir |  Desteklenen taşıma seçenekleri:<br/><br/> -Bölge artıklığı destekleyen bölgeler arasında.<br/><br/> -Bölge yedekliliği desteklemeyen bölgeler arasında.<br/><br/> -Bölge yedekliliği destekleyen bölge arasında bölge yedekliliği desteklemeyen bir bölge arasında.<br/><br/> -Bölge yedekliliği desteklemeyen bir bölge arasında bölge artıklığı destekleyen bir bölge arasında. 
**Veri eşitleme** | Merkez/eşitleme veritabanı: desteklenmiyor<br/><br/> Eşitleme üyesi: destekleniyor. | Bir eşitleme üyesi taşınmışsa, yeni hedef veritabanına veri eşitlemesi ayarlamanız gerekir.
**Mevcut coğrafi çoğaltma** | Desteklenir | Mevcut coğrafi çoğaltmalar, hedef bölgedeki yeni birincil ile eşleştirilir.<br/><br/> Taşıma işleminden sonra dengeli dağıtım başlatılmalıdır. [Daha fazla bilgi edinin](../azure-sql/database/active-geo-replication-configure-portal.md)
**Kendi Anahtarını Getir (BYOK) ile Saydam Veri Şifrelemesi (TDE)** | Desteklenir | Anahtar kasalarını bölgeler arasında taşıma hakkında [daha fazla bilgi edinin](../key-vault/general/move-region.md) .
**Hizmet tarafından yönetilen anahtarla TDE** | Destekleniyor. |  Anahtar kasalarını bölgeler arasında taşıma hakkında [daha fazla bilgi edinin](../key-vault/general/move-region.md) .
**Dinamik veri maskeleme kuralları** | Destekleniyor. | Kurallar, taşımanın bir parçası olarak hedef bölgeye otomatik olarak kopyalanır. [Daha fazla bilgi edinin](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Gelişmiş veri güvenliği** | Desteklenmez. | Geçici çözüm: hedef bölgedeki SQL Server düzeyinde ayarlanır. [Daha fazla bilgi edinin](../azure-sql/database/azure-defender-for-sql.md).
**Güvenlik duvarı kuralları** | Desteklenmez. | Geçici çözüm: hedef bölgede SQL Server için güvenlik duvarı kuralları ayarlayın. Veritabanı düzeyinde güvenlik duvarı kuralları, kaynak sunucudan hedef sunucuya kopyalanır. [Daha fazla bilgi edinin](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**Denetim ilkeleri** | Desteklenmez. | Taşıma işleminden sonra ilkeler varsayılana sıfırlanır. Sıfırlamayı [öğrenin](../azure-sql/database/auditing-overview.md) .
**Yedekleme dosyası saklama** | Destekleniyor. | Kaynak veritabanının yedekleme bekletme ilkeleri hedef veritabanına taşınır. Taşıma işleminden sonra ayarları değiştirmeyi [öğrenin](../azure-sql/database/long-term-backup-retention-configure.md) .
**Otomatik ayarlama** | Desteklenmez. | Geçici çözüm: taşıdıktan sonra otomatik ayarlama ayarlarını ayarlayın. [Daha fazla bilgi edinin](../azure-sql/database/automatic-tuning-enable.md).
**Veritabanı uyarıları** | Desteklenmez. | Geçici çözüm: taşıdıktan sonra uyarıları ayarlayın. [Daha fazla bilgi edinin](../azure-sql/database/alerts-insights-configure-portal.md).
**Azure SQL Server Esnetme Veritabanı** | Desteklenmiyor | SQL Server Esnetme veritabanlarını kaynak taşıyıcısı ile taşıyamıyor.
**Azure Synapse Analytics** | Desteklenmiyor | Azure SYNAPSE Analytics, kaynak taşıyıcısı ile taşınamaz.
## <a name="next-steps"></a>Sonraki adımlar

[Azure SQL kaynaklarını](tutorial-move-region-sql.md) kaynak taşıyıcısı ile başka bir bölgeye deneyin.