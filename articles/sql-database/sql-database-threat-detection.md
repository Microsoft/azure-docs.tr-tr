---
title: Gelişmiş tehdit koruması yapılandırma
description: Gelişmiş tehdit koruması, tek bir veritabanı veya elastik havuzdaki veritabanına yönelik olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822500"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Tek veya havuza alınmış veritabanları için Azure SQL veritabanı Gelişmiş tehdit koruması

Tek ve havuza alınmış veritabanları için [Gelişmiş tehdit koruması](sql-database-threat-detection-overview.md) , veritabanlarına erişmek veya veritabanına yararlanmak için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar. Gelişmiş tehdit koruması, **OLASı SQL ekleme**, **olağan dışı konum veya veri merkezinden erişim**, **bilmediğiniz sorumlu veya zararlı uygulamalardan ERIŞIM**ve **deneme yanılma SQL kimlik bilgilerini** belirleyebilir. bkz. [Gelişmiş tehdit koruması uyarıları](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)hakkında daha fazla bilgi.

Algılanan tehditler hakkında [e-posta bildirimleri](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) veya [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) ile ilgili bildirimler alabilirsiniz

Gelişmiş [tehdit koruması](sql-database-threat-detection-overview.md) , gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir paket olan [Gelişmiş veri güvenliği](sql-database-advanced-data-security.md) (ADS) sunumunun bir parçasıdır. Gelişmiş tehdit korumasına, merkezi SQL ADS portalı aracılığıyla erişilebilir ve yönetilebilir.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure portal Gelişmiş tehdit koruması ayarlama

1. [https://portal.azure.com](https://portal.azure.com)Azure Portal başlatın.
2. Korumak istediğiniz Azure SQL veritabanı sunucusunun yapılandırma sayfasına gidin. Güvenlik ayarları ' nda **Gelişmiş veri güvenliği**' ni seçin.
3. **Gelişmiş veri güvenliği** yapılandırması sayfasında:

   - Sunucuda gelişmiş veri güvenliğini etkinleştirin.
   - **Gelişmiş tehdit koruması ayarları**' nda, **uyarıları gönder** metin kutusunda, anormal veritabanı etkinliklerinin algılanması üzerine güvenlik uyarılarını alacak e-postaların listesini belirtin.
  
   ![Gelişmiş tehdit koruması ayarlama](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Ekran görüntülerinin fiyatları her zaman geçerli fiyatı yansıtmaz ve bir örnektir.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>PowerShell kullanarak Gelişmiş tehdit koruması ayarlama

Betik örneği için bkz. [PowerShell kullanarak denetim ve Gelişmiş tehdit korumasını yapılandırma](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Gelişmiş tehdit koruması](sql-database-threat-detection-overview.md)hakkında daha fazla bilgi edinin.
- [Yönetilen örnekteki Gelişmiş tehdit koruması](sql-database-managed-instance-threat-detection.md)hakkında daha fazla bilgi edinin.  
- [Gelişmiş veri güvenliği](sql-database-advanced-data-security.md)hakkında daha fazla bilgi edinin.
- [Denetim](sql-database-auditing.md) hakkında daha fazla bilgi edinin
- [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-intro) hakkında daha fazla bilgi edinin
- Fiyatlandırma hakkında daha fazla bilgi için bkz. [SQL Veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/sql-database/)  
