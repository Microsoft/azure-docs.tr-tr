---
title: Gelişmiş tehdit koruması ile yönetilen örneği yapılandırma
description: Gelişmiş tehdit koruması, yönetilen bir örnekteki veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822561"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği 'nde Gelişmiş tehdit korumasını yapılandırma

[Yönetilen bir örnek](sql-database-managed-instance-index.yml) Için [Gelişmiş tehdit koruması](sql-database-threat-detection-overview.md) , veritabanları için olağan dışı ve olası zararlı girişimleri gösteren anormal etkinlikleri algılar. Gelişmiş tehdit koruması, **OLASı SQL ekleme**, **olağan dışı konum veya veri merkezinden erişim**, **bilmediğiniz sorumlu veya zararlı uygulamalardan ERIŞIM**ve **deneme yanılma SQL kimlik bilgilerini** belirleyebilir. bkz. [Gelişmiş tehdit koruması uyarıları](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)hakkında daha fazla bilgi.

Algılanan tehditler hakkında [e-posta bildirimleri](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) veya [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) ile ilgili bildirimler alabilirsiniz

Gelişmiş [tehdit koruması](sql-database-threat-detection-overview.md) , gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir paket olan [Gelişmiş veri güvenliği](sql-database-advanced-data-security.md) (ADS) sunumunun bir parçasıdır. Gelişmiş tehdit korumasına, merkezi SQL ADS portalı aracılığıyla erişilebilir ve yönetilebilir.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure portal Gelişmiş tehdit koruması ayarlama

1. [https://portal.azure.com](https://portal.azure.com)Azure Portal başlatın.
2. Korumak istediğiniz yönetilen örneğin yapılandırma sayfasına gidin. **Ayarlar** sayfasında **Gelişmiş veri güvenliği**' ni seçin.
3. Gelişmiş veri güvenliği yapılandırma sayfasında
   - Gelişmiş **veri** güvenliğini açın.
   - Anormal veritabanı etkinliklerinin algılanması üzerine güvenlik uyarıları almak için **e-posta listesini** yapılandırın.
   - Anormal tehdit denetim kayıtlarının kaydedildiği **Azure Storage hesabını** seçin.
   - Yapılandırmak istediğiniz **Gelişmiş tehdit koruması türlerini** seçin. [Gelişmiş tehdit koruması uyarıları](sql-database-threat-detection-overview.md)hakkında daha fazla bilgi edinin.
4. Yeni veya güncelleştirilmiş gelişmiş veri güvenliği ilkesini kaydetmek için **Kaydet** ' e tıklayın.

   ![Gelişmiş Tehdit Koruması](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Ekran görüntülerinin fiyatları her zaman geçerli fiyatı yansıtmaz ve bir örnektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Gelişmiş tehdit koruması](sql-database-threat-detection-overview.md)hakkında daha fazla bilgi edinin.
- Yönetilen örnekler hakkında bilgi edinin, bkz. [yönetilen örnek nedir](sql-database-managed-instance.md).
- [Tek veritabanı Için Gelişmiş tehdit koruması](sql-database-threat-detection.md)hakkında daha fazla bilgi edinin.
- [Yönetilen örnek denetimi](https://go.microsoft.com/fwlink/?linkid=869430)hakkında daha fazla bilgi edinin.
- [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-intro)hakkında daha fazla bilgi edinin.
