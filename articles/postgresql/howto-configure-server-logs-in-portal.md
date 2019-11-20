---
title: PostgreSQL için Azure veritabanı 'nda sunucu günlüklerini yapılandırma ve erişme-tek sunucu Azure portal
description: Bu makalede, Azure portal 'den PostgreSQL için Azure veritabanı-tek sunuculu sunucu günlüklerinin nasıl yapılandırılacağı ve erişebileceğiniz açıklanır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c77e708e14d34545754ca38095aedb63ff0172a1
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841514"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>PostgreSQL için Azure veritabanı-tek sunuculu Günlükler Azure portal yapılandırma ve erişme

[PostgreSQL Için Azure veritabanı günlüklerini](concepts-server-logs.md) Azure Portal yapılandırabilir, listeleyebilir ve indirebilirsiniz.

## <a name="prerequisites"></a>Prerequisites
Bu makaledeki adımlarda [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)gerekir.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Sorgu günlüklerine ve hata günlüklerine erişimi yapılandırın. 

1. [Azure Portal](https://portal.azure.com/)oturum açın.

2. PostgreSQL için Azure veritabanı sunucunuzu seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. 

   ![Sunucu günlüğü seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Sunucu parametrelerini görmek için, **günlükleri etkinleştirmek ve günlük parametrelerini yapılandırmak için buraya tıklayın ' ı**seçin.

5. Ayarlamanız gereken parametreleri değiştirin. Bu oturumda yaptığınız tüm değişiklikler mor renkle vurgulanır.

   Parametreleri değiştirdikten sonra **Kaydet**' i seçin. Ya da değişikliklerinizi atabilirsiniz. 

   ![Sunucu parametreleri seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

**Sunucu parametreleri** sayfasında, sayfayı kapatarak Günlükler listesine geri dönebilirsiniz.

## <a name="view-list-and-download-logs"></a>Listeyi görüntüle ve günlükleri indir
Günlüğe kaydetme başladıktan sonra, kullanılabilir günlüklerin bir listesini görüntüleyebilir ve günlük dosyalarını tek tek indirebilirsiniz. 

1. Azure portal açın.

2. PostgreSQL için Azure veritabanı sunucunuzu seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. Sayfa, günlük dosyalarınızın bir listesini gösterir.

   ![Günlük listesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Günlüğün adlandırma kuralı **PostgreSQL-yyyy-mm-dd_hh0000. log**' dır. Dosya adında kullanılan tarih ve saat, günlüğün verildiği zaman. Günlük dosyaları her saat veya 100 MB döndürülür, hangisi önce gelir.

4. Gerekirse, tarih ve saate göre belirli bir günlüğe hızlıca daraltmak için arama kutusunu kullanın. Arama günlüğün adı üzerinde.

   ![Arama kutusuyla sonuçları vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Ayrı günlük dosyalarını indirmek için tablo satırındaki her bir günlük dosyasının yanındaki aşağı ok simgesini seçin.

   ![Aşağı ok simgesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Sonraki adımlar
- Günlüklerin programlı olarak nasıl indirileceği hakkında bilgi edinmek için bkz. [CLI 'daki sunucu günlüklerine erişim](howto-configure-server-logs-using-cli.md) .
- PostgreSQL için Azure veritabanı 'nda [sunucu günlükleri](concepts-server-logs.md) hakkında daha fazla bilgi edinin. 
- Parametre tanımları ve PostgreSQL günlüğü hakkında daha fazla bilgi için bkz. [hata raporlama ve günlüğe kaydetme](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)hakkında PostgreSQL belgeleri.

