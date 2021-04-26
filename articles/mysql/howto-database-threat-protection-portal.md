---
title: Gelişmiş tehdit koruması-Azure portal-MySQL için Azure veritabanı
description: Veritabanı için olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılamak için Gelişmiş tehdit koruması yapılandırma hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: b30bd36dca6f866b8f3e6e8a0b133a6dd61b239b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012672"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı için Gelişmiş tehdit koruması

MySQL için Azure Veritabanı Gelişmiş Tehdit Koruması, veritabanlarınıza erişme veya bunları kullanma konusunda olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri tespit eder.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com) aracılığıyla erişilebilir ve yönetilebilir ve şu anda önizleme aşamasındadır.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği şu Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US gov Teksas, US gov Arizona, US gov Iowa, US, gov Virginia, US DOD Doğu, US DOD orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.
>

> [!NOTE]
> Bu özellik, MySQL için Azure veritabanı 'nın Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için dağıtıldığı tüm Azure bölgelerinde kullanılabilir.

## <a name="set-up-threat-detection"></a>Tehdit algılamayı ayarlama
1. Üzerinde Azure portal başlatın [https://portal.azure.com](https://portal.azure.com) .
2. Korumak istediğiniz MySQL için Azure veritabanı sunucusunun yapılandırma sayfasına gidin. Güvenlik ayarları ' nda **Gelişmiş tehdit koruması (Önizleme)** öğesini seçin.
3. **Gelişmiş tehdit koruması (Önizleme)** yapılandırma sayfasında:

   - Sunucuda Gelişmiş tehdit koruması 'nı etkinleştirin.
   - **Gelişmiş tehdit koruması ayarları**' nda, **uyarıları gönder** metin kutusunda, anormal veritabanı etkinliklerinin algılanması üzerine güvenlik uyarılarını alacak e-postaların listesini belirtin.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Tehdit algılamayı ayarlama":::

## <a name="explore-anomalous-database-activities"></a>Anormal veritabanı etkinliklerini keşfet

Anormal veritabanı etkinliklerinin algılanmasıyla bir e-posta bildirimi alırsınız. E-posta, anormal etkinlikler, veritabanı adı, sunucu adı, uygulama adı ve olay saatinin doğası dahil şüpheli güvenlik olayı hakkında bilgi sağlar. Buna ek olarak, e-posta olası nedenler ve veritabanı için olası tehdidi araştırmak ve azaltmak için önerilen eylemler hakkında bilgi sağlar.
 
1. Azure portal başlatmak ve SQL veritabanında algılanan etkin tehditlere genel bir bakış sağlayan Azure Güvenlik Merkezi uyarıları sayfasını görüntülemek için e-postadaki **son Uyarıları görüntüle** bağlantısına tıklayın.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Anormal etkinlik raporu":::

    Etkin tehditleri görüntüle:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Etkin tehditler":::

2. Bu tehdidi araştırmaya ve gelecekteki tehditleri incelemeye yönelik ek ayrıntılar ve eylemler almak için belirli bir uyarıya tıklayın.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Özel uyarı":::

## <a name="explore-threat-detection-alerts"></a>Tehdit algılama uyarılarını keşfet

SQL veritabanı tehdit algılama, uyarılarını [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile tümleştirir. Canlı bir SQL tehdit algılama kutucuğu, veritabanındaki etkin tehditlerin durumunu ve Azure portal SQL ATP sayfalarını izler.

**Tehdit algılama uyarısı** ' na tıklayarak Azure Güvenlik Merkezi uyarıları sayfasını başlatın ve veritabanında ALGıLANAN etkin SQL tehditleri hakkında genel bir bakış alın.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Tehdit algılama uyarısı":::
   

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mysql/)