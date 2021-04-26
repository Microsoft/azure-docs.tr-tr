---
title: Gelişmiş Tehdit Koruması'nı yapılandırma
titleSuffix: Azure SQL Managed Instance
description: Gelişmiş tehdit koruması, Azure SQL yönetilen örneği 'nde veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 69bebcf872f55055117acf5cef410d1f89eafe34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446894"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'nde Gelişmiş tehdit koruması yapılandırma
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL yönetilen örneği](sql-managed-instance-paas-overview.md) Için [Gelişmiş tehdit koruması](../database/threat-detection-overview.md) , veritabanları için olağandışı ve olası zararlı girişimleri gösteren anormal etkinlikleri algılar. Gelişmiş tehdit koruması, **OLASı SQL ekleme**, **olağan dışı konum veya veri merkezinden erişim**, **bilinmeyen bir sorumlu veya zararlı uygulamadan ERIŞIM** ve **deneme yanılma SQL kimlik bilgilerini** belirleyebilir. [Gelişmiş tehdit koruması uyarıları](../database/threat-detection-overview.md#alerts)hakkında daha fazla ayrıntı için bkz..

Algılanan tehditler hakkında [e-posta bildirimleri](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) veya [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal) ile ilgili bildirimler alabilirsiniz

[Gelişmiş tehdit koruması](../database/threat-detection-overview.md) , gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir paket olan [SQL teklifi için Azure Defender](../database/azure-defender-for-sql.md)  'ın bir parçasıdır. Gelişmiş tehdit korumasına, SQL Portal için merkezi Azure Defender aracılığıyla erişilebilir ve yönetebilirsiniz.

##  <a name="azure-portal"></a>Azure portalı

1. [Azure Portal](https://portal.azure.com)oturum açın. 
2. Korumak istediğiniz SQL yönetilen örneği örneğinin yapılandırma sayfasına gidin. **Güvenlik** altında **Güvenlik Merkezi**' ni seçin.
3. SQL için Azure Defender yapılandırma sayfasında
   - SQL **için Azure Defender 'ı açın** .
   - Anormal veritabanı etkinliklerinin algılanması üzerine güvenlik uyarıları almak için uyarıları e-posta adresine **Gönder** ' i yapılandırın.
   - Anormal tehdit denetim kayıtlarının kaydedildiği **Azure Storage hesabını** seçin.
   - Yapılandırmak istediğiniz **Gelişmiş tehdit koruması türlerini** seçin. [Gelişmiş tehdit koruması uyarıları](../database/threat-detection-overview.md)hakkında daha fazla bilgi edinin.
4. Yeni veya güncelleştirilmiş SQL için Azure Defender ilkesini kaydetmek için **Kaydet** ' e tıklayın.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Gelişmiş tehdit koruması ayarlama":::

## <a name="next-steps"></a>Sonraki adımlar

- [Gelişmiş tehdit koruması](../database/threat-detection-overview.md)hakkında daha fazla bilgi edinin.
- Yönetilen örnekler hakkında bilgi edinin, bkz. [Azure SQL yönetilen örneği nedir](sql-managed-instance-paas-overview.md).
- [Azure SQL veritabanı Için Gelişmiş tehdit koruması](../database/threat-detection-configure.md)hakkında daha fazla bilgi edinin.
- [SQL yönetilen örnek denetimi](./auditing-configure.md)hakkında daha fazla bilgi edinin.
- [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)hakkında daha fazla bilgi edinin.