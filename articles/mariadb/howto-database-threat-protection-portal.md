---
title: Gelişmiş tehdit koruması-MariaDB için Azure veritabanı | Microsoft Docs
description: Tehdit koruması, veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: f77d9f105d5c2ff10753f2b4b1ecc962c84eee6e
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869657"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı için Gelişmiş tehdit koruması

MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com)aracılığıyla erişilebilir ve yönetebilirsiniz.

> [!IMPORTANT]
> Gelişmiş tehdit koruması genel önizlemede. Bu özellik, Azure 'un, MariaDB için Azure veritabanı 'nın Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için dağıtıldığı tüm Azure bölgelerinde kullanılabilir.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği, aşağıdaki Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US Gov Teksas, US Gov Arizona, US Gov Iowa, US, gov Virginia, US DoD Doğu, US DoD Orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.

## <a name="set-up-threat-detection"></a>Tehdit algılamayı ayarlama
1. Üzerinde [https://portal.azure.com](https://portal.azure.com)Azure Portal başlatın.
2. Korumak istediğiniz MariaDB sunucusu için Azure veritabanı 'nın yapılandırma sayfasına gidin. Güvenlik ayarları ' nda **Gelişmiş tehdit koruması (Önizleme)** öğesini seçin.
3. **Gelişmiş tehdit koruması (Önizleme)** yapılandırma sayfasında:

   - Sunucuda Gelişmiş tehdit koruması 'nı etkinleştirin.
   - **Gelişmiş tehdit koruması ayarları**' nda, **uyarıları gönder** metin kutusunda, anormal veritabanı etkinliklerinin algılanması üzerine güvenlik uyarılarını alacak e-postaların listesini belirtin.
  
   ![Tehdit algılamayı ayarlama](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Anormal veritabanı etkinliklerini keşfet

Anormal veritabanı etkinliklerinin algılanmasıyla bir e-posta bildirimi alırsınız. E-posta, anormal etkinlikler, veritabanı adı, sunucu adı, uygulama adı ve olay saatinin doğası dahil şüpheli güvenlik olayı hakkında bilgi sağlar. Buna ek olarak, e-posta olası nedenler ve veritabanı için olası tehdidi araştırmak ve azaltmak için önerilen eylemler hakkında bilgi sağlar.
 
1. Azure portal başlatmak ve SQL veritabanında algılanan etkin tehditlere genel bir bakış sağlayan Azure Güvenlik Merkezi uyarıları sayfasını görüntülemek için e-postadaki **son Uyarıları görüntüle** bağlantısına tıklayın.
    
    ![Anormal etkinlik raporu](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Etkin tehditleri görüntüle:

    ![Etkin tehditler](./media/howto-database-threat-protection-portal/active-threats.png)

2. Bu tehdidi araştırmaya ve gelecekteki tehditleri incelemeye yönelik ek ayrıntılar ve eylemler almak için belirli bir uyarıya tıklayın.
    
    ![Özel uyarı](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Tehdit algılama uyarılarını keşfet

SQL veritabanı tehdit algılama, uyarılarını [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile tümleştirir. Veritabanı içindeki canlı bir SQL tehdit algılama kutucukları ve Azure portal SQL ATP dikey pencereleri, etkin tehditler durumunu izler.

**Tehdit algılama uyarısı** ' na tıklayarak Azure Güvenlik Merkezi uyarıları sayfasını başlatın ve veritabanında ALGıLANAN etkin SQL tehditleri hakkında genel bir bakış alın.

   ![Tehdit algılama uyarısı](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-intro) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mariadb/)  
