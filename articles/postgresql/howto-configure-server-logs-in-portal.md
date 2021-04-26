---
title: Günlükleri yönetme-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Azure portal 'den PostgreSQL için Azure veritabanı 'nda sunucu günlüklerinin (. log dosyaları) nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3e44377ecb734f0036d05a347596f1ff003ae28a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604337"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>PostgreSQL için Azure veritabanı-tek sunuculu Günlükler Azure portal yapılandırma ve erişme

[PostgreSQL Için Azure veritabanı günlüklerini](concepts-server-logs.md) Azure Portal yapılandırabilir, listeleyebilir ve indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makaledeki adımlarda [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)gerekir.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Sorgu günlüklerine ve hata günlüklerine erişimi yapılandırın. 

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. PostgreSQL için Azure Veritabanı sunucunuzu seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Sunucu günlüğü seçeneklerinin ekran görüntüsü":::

4. Sunucu parametrelerini görmek için, **günlükleri etkinleştirmek ve günlük parametrelerini yapılandırmak için buraya tıklayın ' ı** seçin.

5. Ayarlamanız gereken parametreleri değiştirin. Bu oturumda yaptığınız tüm değişiklikler mor renkle vurgulanır.

   Parametreleri değiştirdikten sonra **Kaydet**' i seçin. Ya da değişikliklerinizi atabilirsiniz. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Sunucu parametreleri seçeneklerinin ekran görüntüsü":::

**Sunucu parametreleri** sayfasında, sayfayı kapatarak Günlükler listesine geri dönebilirsiniz.

## <a name="view-list-and-download-logs"></a>Listeyi görüntüle ve günlükleri indir
Günlüğe kaydetme başladıktan sonra, kullanılabilir günlüklerin bir listesini görüntüleyebilir ve günlük dosyalarını tek tek indirebilirsiniz. 

1. Azure portalını açın.

2. PostgreSQL için Azure Veritabanı sunucunuzu seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. Sayfa, günlük dosyalarınızın bir listesini gösterir.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Günlük listesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü":::

   > [!TIP]
   > Günlüğün adlandırma kuralı **PostgreSQL-yyyy-aa-dd_hh0000. log**' dır. Dosya adında kullanılan tarih ve saat, günlüğün verildiği zaman. Günlük dosyaları her saat veya 100 MB döndürülür, hangisi önce gelir.

4. Gerekirse, tarih ve saate göre belirli bir günlüğe hızlıca daraltmak için arama kutusunu kullanın. Arama günlüğün adı üzerinde.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="Arama kutusuyla sonuçları vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü":::

5. Ayrı günlük dosyalarını indirmek için tablo satırındaki her bir günlük dosyasının yanındaki aşağı ok simgesini seçin.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="Aşağı ok simgesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar
- Günlüklerin programlı olarak nasıl indirileceği hakkında bilgi edinmek için bkz. [CLI 'daki sunucu günlüklerine erişim](howto-configure-server-logs-using-cli.md) .
- PostgreSQL için Azure veritabanı 'nda [sunucu günlükleri](concepts-server-logs.md) hakkında daha fazla bilgi edinin. 
- Parametre tanımları ve PostgreSQL günlüğü hakkında daha fazla bilgi için bkz. [hata raporlama ve günlüğe kaydetme](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)hakkında PostgreSQL belgeleri.

