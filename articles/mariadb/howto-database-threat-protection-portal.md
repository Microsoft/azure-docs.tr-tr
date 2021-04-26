---
title: Gelişmiş tehdit koruması-Azure portal-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı tehdit koruması, veritabanında olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665115"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı için Gelişmiş tehdit koruması

MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com)aracılığıyla erişilebilir ve yönetebilirsiniz.

> [!IMPORTANT]
> Gelişmiş tehdit koruması genel önizlemede. Bu özellik, Azure 'un, MariaDB için Azure veritabanı 'nın Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için dağıtıldığı tüm Azure bölgelerinde kullanılabilir.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği şu Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US gov Teksas, US gov Arizona, US gov Iowa, US, gov Virginia, US DOD Doğu, US DOD orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.

## <a name="set-up-threat-detection"></a>Tehdit algılamayı ayarlama
1. Üzerinde Azure portal başlatın [https://portal.azure.com](https://portal.azure.com) .
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

* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mariadb/)