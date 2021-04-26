---
title: Gelişmiş tehdit koruması kullanma-PostgreSQL için Azure veritabanı-tek sunucu
description: Tehdit koruması, veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 5583e8423f0909936d9e55c6d87593835eded8f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489906"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı için Gelişmiş tehdit koruması-tek sunucu

PostgreSQL için Azure Veritabanı Gelişmiş Tehdit Koruması, veritabanlarınıza erişme veya bunları kullanma konusunda olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri tespit eder.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com) aracılığıyla erişilebilir ve yönetilebilir ve şu anda önizleme aşamasındadır.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği şu Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US gov Teksas, US gov Arizona, US gov Iowa, US, gov Virginia, US DOD Doğu, US DOD orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.
>

> [!NOTE]
> Bu özellik, Azure 'un PostgreSQL için Azure veritabanı 'nın Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için dağıtıldığı tüm Azure bölgelerinde kullanılabilir.

## <a name="set-up-threat-detection"></a>Tehdit algılamayı ayarlama
1. Üzerinde Azure portal başlatın [https://portal.azure.com](https://portal.azure.com) .
2. Korumak istediğiniz PostgreSQL için Azure veritabanı sunucusu 'nun yapılandırma sayfasına gidin. Güvenlik ayarları ' nda **Gelişmiş tehdit koruması (Önizleme)** öğesini seçin.
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

Gelişmiş tehdit koruması, uyarılarını [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile tümleştirir. 

Azure Güvenlik Merkezi uyarıları sayfasını başlatmak için **TEHDIT koruması** altında **güvenlik uyarıları** ' na tıklayın ve veritabanında algılanan etkin SQL tehditleri hakkında genel bir bakış alın.

  :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Tehdit koruması ASC":::

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/postgresql/)