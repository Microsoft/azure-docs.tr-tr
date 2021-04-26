---
title: Yavaş sorgu günlüklerine erişme-Azure portal-MariaDB için Azure veritabanı
description: Bu makalede, Azure portal MariaDB için Azure veritabanı 'nda yavaş sorgu günlüklerinin nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: c5ee948daecafc061910f36d2ac95d15338bfb38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662517"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>Azure portal Maria DB yavaş sorgu günlükleri için Azure veritabanı 'nı yapılandırma ve erişme

[MariaDB yavaş sorgu günlükleri Için Azure veritabanı](concepts-server-logs.md) 'nı Azure Portal yapılandırabilir, listeleyebilir ve indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makaledeki adımlarda, [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-portal.md)'nın olması gerekir.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Yavaş sorgu günlüğüne erişimi yapılandırın. 

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. MariaDB sunucusu için Azure veritabanınızı seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. 
   ![Sunucu günlüğü seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Sunucu parametrelerini görmek için, **günlükleri etkinleştirmek ve günlük parametrelerini yapılandırmak için buraya tıklayın ' ı** seçin.

5. **Slow_query_log** **açın.**

6. **Log_output** kullanarak günlüklerin nereye çıkışını seçin. Günlükleri hem yerel depolama hem de Azure Izleyici tanılama günlüklerine göndermek için **Dosya**' yı seçin. 

7. Gerekli diğer parametreleri değiştirin. 

8. **Kaydet**’i seçin. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Yavaş sorgu günlüğü parametrelerinin ekran görüntüsü ve kaydetme.":::

**Sunucu parametreleri** sayfasında, sayfayı kapatarak Günlükler listesine geri dönebilirsiniz.

## <a name="view-list-and-download-logs"></a>Listeyi görüntüle ve günlükleri indir
Günlüğe kaydetme başladıktan sonra, kullanılabilir yavaş sorgu günlüklerinin bir listesini görüntüleyebilir ve günlük dosyalarını tek tek indirebilirsiniz. 

1. Azure portalını açın.

2. MariaDB sunucusu için Azure veritabanınızı seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. Sayfa, günlük dosyalarınızın bir listesini gösterir.

   ![Günlük listesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Günlüğün adlandırma kuralı **MySQL-yavaş-sunucu adınızın>-yyyymmddhh. log <**. Dosya adında kullanılan tarih ve saat, günlüğün verildiği zaman. Günlük dosyaları her 24 saatte bir veya 7,5 GB döndürülür, hangisi önce gelir.

4. Gerekirse, tarih ve saate göre belirli bir günlüğe hızlıca daraltmak için arama kutusunu kullanın. Arama günlüğün adı üzerinde.

5. Ayrı günlük dosyalarını indirmek için tablo satırındaki her bir günlük dosyasının yanındaki aşağı ok simgesini seçin.

   ![Aşağı ok simgesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Tanılama günlükleri ayarlama

1. Kenar çubuğu 'ndaki **izleme** bölümünde **Tanılama ayarları**  >  **Tanılama ayarı Ekle**' yi seçin.

   ![Tanılama ayarları seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Bir tanılama ayarı adı belirtin.

1. Yavaş sorgu günlüklerinin (depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanı) hangi veri havuzlarını gönderileceğini belirtin.

1. Günlük türü olarak **Mysqlyavaşlogs** ' u seçin.
![Tanılama ayarları yapılandırma seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Yavaş sorgu günlüklerini kanala yönelten veri havuzlarını yapılandırdıktan sonra **Kaydet**' i seçin.
![Tanılama ayarları yapılandırma seçeneklerinin, kayıt vurgulanmış olarak ekran görüntüsü](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Bunları yapılandırdığınız veri havuzları içinde inceleyerek yavaş sorgu günlüklerine erişin. Günlüklerin görünmesi 10 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
- Yavaş sorgu günlüklerinin programlama yoluyla nasıl indirileceği hakkında bilgi edinmek için bkz. [CLI 'deki yavaş sorgu günlüklerine erişme](howto-configure-server-logs-cli.md) .
- MariaDB için Azure veritabanı 'nda [yavaş sorgu günlükleri](concepts-server-logs.md) hakkında daha fazla bilgi edinin.
- Parametre tanımları ve günlüğe kaydetme hakkında daha fazla bilgi için [günlüklere](https://mariadb.com/kb/en/library/slow-query-log-overview/)yönelik MariaDB belgelerine bakın.