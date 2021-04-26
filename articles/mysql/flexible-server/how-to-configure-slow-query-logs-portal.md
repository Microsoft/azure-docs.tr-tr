---
title: Yavaş sorgu günlüklerini Yapılandırma-Azure portal-MySQL için Azure veritabanı-esnek sunucu
description: Bu makalede, Azure portal MySQL için Azure veritabanı esnek sunucusu 'ndaki yavaş sorgu günlüklerine nasıl yapılandırılacağı ve erişebileceğiniz açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540072"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı için yavaş sorgu günlüklerini yapılandırma ve erişme-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

MySQL için Azure veritabanı esnek sunucu [yavaş sorgu günlüklerini](concepts-slow-query-logs.md) Azure Portal yapılandırabilir, listeleyebilir ve indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makaledeki adımlarda [esnek sunucu](quickstart-create-server-portal.md)olması gerekir.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
MySQL yavaş sorgu günlüğüne erişimi yapılandırın. 

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Esnek sunucunuzu seçin.

1. Kenar çubuğu 'ndaki **Ayarlar** bölümünde **sunucu parametreleri**' ni seçin.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Sunucu parametreleri sayfası.":::

1. **Slow_query_log** parametresini **Açık** olarak güncelleştirin.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="Yavaş sorgu günlüklerini açın.":::

1. Gerekli diğer parametreleri değiştirin (örn. `long_query_time`, `log_slow_admin_statements`). Daha fazla parametre için [yavaş sorgu günlükleri](./concepts-slow-query-logs.md#configure-slow-query-logging) belgelerine bakın.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Yavaş sorgu günlüğü ile ilgili parametreleri güncelleştirin.":::

1. **Kaydet**’i seçin. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Yavaş sorgu günlüğü parametrelerini kaydedin.":::

**Sunucu parametreleri** sayfasında, sayfayı kapatarak Günlükler listesine geri dönebilirsiniz.

## <a name="set-up-diagnostics"></a>Tanılamayı ayarla

Yavaş sorgu günlükleri, günlüklerinizi Azure Izleyici günlüklerine, Event Hubs veya Azure depolama 'ya yönelterek Azure Izleyici tanılama ayarlarıyla tümleşiktir.

1. Kenar çubuğu 'ndaki **izleme** bölümünde **Tanılama ayarları**  >  **Tanılama Ayarları Ekle**' yi seçin.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="Tanılama ayarları seçeneklerinin ekran görüntüsü":::

1. Bir tanılama ayarı adı belirtin.

1. Yavaş sorgu günlüklerinin (depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanı) hangi hedeflere gönderileceğini belirtin.

1. Günlük türü olarak **Mysqlyavaşlogs** ' u seçin.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="Tanılama ayarları yapılandırma seçeneklerinin ekran görüntüsü":::

1. Yavaş sorgu günlüklerini kanala yönelten veri havuzlarını yapılandırdıktan sonra **Kaydet**' i seçin.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="Tanılama ayarları yapılandırma seçeneklerinin, kayıt vurgulanmış olarak ekran görüntüsü":::

1. Bunları yapılandırdığınız veri havuzları içinde inceleyerek yavaş sorgu günlüklerine erişin. Günlüklerin görünmesi 10 dakikaya kadar sürebilir.

Günlüklerinizi Azure Izleyici günlüklerine (Log Analytics) aldıysanız, analiz için kullanabileceğiniz bazı [örnek sorgulara](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) bakın. 

## <a name="next-steps"></a>Sonraki adımlar
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- [Yavaş sorgu günlükleri](concepts-slow-query-logs.md) hakkında daha fazla bilgi edinin
- Parametre tanımları ve MySQL günlüğü hakkında daha fazla bilgi için, [günlüklerdeki](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)MySQL belgelerine bakın.
- [Denetim günlükleri](concepts-audit-logs.md) hakkında bilgi edinin
