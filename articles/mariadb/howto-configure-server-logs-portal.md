---
title: Azure portal MariaDB için Azure veritabanı 'nda sunucu günlüklerini yapılandırma ve erişme
description: Bu makalede, Azure portal MariaDB için Azure veritabanı 'nda sunucu günlüklerinin nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c7e95521e503ad2d92bf13ae7e410b71ed6dfcd3
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844731"
---
# <a name="configure-and-access-server-logs-from-the-azure-portal"></a>Azure portal sunucu günlüklerini yapılandırma ve erişme

[MariaDB yavaş sorgu günlükleri Için Azure veritabanı](concepts-server-logs.md) 'nı Azure Portal yapılandırabilir, listeleyebilir ve indirebilirsiniz.

## <a name="prerequisites"></a>Prerequisites
Bu makaledeki adımlarda, [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-portal.md)'nın olması gerekir.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Yavaş sorgu günlüğüne erişimi yapılandırın. 

1. [Azure Portal](https://portal.azure.com/)oturum açın.

2. MariaDB sunucusu için Azure veritabanınızı seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. 
   ![ sunucu günlüğü seçeneklerinin @ ekran görüntüsü @ no__t-1

4. Sunucu parametrelerini görmek için, **günlükleri etkinleştirmek ve günlük parametrelerini yapılandırmak için buraya tıklayın ' ı**seçin.

5. Ayarlamanız gereken parametreleri değiştirme de dahil olmak üzere **slow_query_log** özelliğini **on**olarak değiştirin. Bu oturumda yaptığınız tüm değişiklikler mor renkle vurgulanır. 

   Parametreleri değiştirdikten sonra **Kaydet**' i seçin. Ya da değişikliklerinizi atabilirsiniz.

   ![Sunucu parametreleri seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-portal/3-save-discard.png)

**Sunucu parametreleri** sayfasında, sayfayı kapatarak Günlükler listesine geri dönebilirsiniz.

## <a name="view-list-and-download-logs"></a>Listeyi görüntüle ve günlükleri indir
Günlüğe kaydetme başladıktan sonra, kullanılabilir yavaş sorgu günlüklerinin bir listesini görüntüleyebilir ve günlük dosyalarını tek tek indirebilirsiniz. 

1. Azure portal açın.

2. MariaDB sunucusu için Azure veritabanınızı seçin.

3. Kenar çubuğu 'ndaki **izleme** bölümünde **sunucu günlükleri**' ni seçin. Sayfa, günlük dosyalarınızın bir listesini gösterir.

   ![Günlük listesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Günlüğün adlandırma kuralı **MySQL-yavaş-sunucu adınızın >-yyyymmddhh. log <** . Dosya adında kullanılan tarih ve saat, günlüğün verildiği zaman. Günlük dosyaları her 24 saatte bir veya 7,5 GB döndürülür, hangisi önce gelir.

4. Gerekirse, tarih ve saate göre belirli bir günlüğe hızlıca daraltmak için arama kutusunu kullanın. Arama günlüğün adı üzerinde.

5. Ayrı günlük dosyalarını indirmek için tablo satırındaki her bir günlük dosyasının yanındaki aşağı ok simgesini seçin.

   ![Aşağı ok simgesi vurgulanmış şekilde sunucu günlükleri sayfasının ekran görüntüsü](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Tanılama günlüklerini ayarlama

1. Kenar çubuğu 'ndaki **izleme** bölümünde **Tanılama ayarları** > **Tanılama ayarı Ekle**' yi seçin.

   ![Tanılama ayarları seçeneklerinin ekran görüntüsü](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Bir tanılama ayarı adı belirtin.

1. Yavaş sorgu günlüklerinin (depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanı) hangi veri havuzlarını gönderileceğini belirtin.

1. Günlük türü olarak **Mysqlyavaşlogs** ' u seçin.
![Tanılama ayarları yapılandırma seçeneklerinin @ no__t-1 ekran görüntüsü

1. Yavaş sorgu günlüklerini kanala yönelten veri havuzlarını yapılandırdıktan sonra **Kaydet**' i seçin.
@no__t-{Diagnostic Settings yapılandırma seçeneklerinin, vurgulanmış olan @ no__t-1 ile

1. Bunları yapılandırdığınız veri havuzları içinde inceleyerek yavaş sorgu günlüklerine erişin. Günlüklerin görünmesi 10 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
- Yavaş sorgu günlüklerinin programlama yoluyla nasıl indirileceği hakkında bilgi edinmek için bkz. [CLI 'deki yavaş sorgu günlüklerine erişme](howto-configure-server-logs-cli.md) .
- MariaDB için Azure veritabanı 'nda [yavaş sorgu günlükleri](concepts-server-logs.md) hakkında daha fazla bilgi edinin.
- Parametre tanımları ve günlüğe kaydetme hakkında daha fazla bilgi için [günlüklere](https://mariadb.com/kb/en/library/slow-query-log-overview/)yönelik MariaDB belgelerine bakın.