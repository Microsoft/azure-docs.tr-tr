---
title: Denetim günlüklerini yapılandırma ve erişme-MySQL için Azure veritabanı
description: Bu makalede, Azure portal MySQL için Azure veritabanı 'nda denetim günlüklerinin nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350398"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Azure portal MySQL için Azure veritabanı için Denetim günlüklerini yapılandırma ve erişme

[MySQL Için Azure veritabanı denetim günlükleri](concepts-audit-logs.md) ve tanılama ayarları Azure portal yapılandırabilirsiniz.

> [!IMPORTANT]
> Denetim günlüğü işlevselliği Şu anda önizleme aşamasındadır.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:

- [MySQL için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Denetim günlüğünü yapılandırma

Denetim günlüğünü etkinleştirin ve yapılandırın.

1. [Azure Portal](https://portal.azure.com/) oturum açın.

1. MySQL sunucusu için Azure veritabanı'nı seçin.

1. Kenar çubuğu 'ndaki **Ayarlar** bölümünde **sunucu parametreleri**' ni seçin.
    ![Sunucu parametreleri](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. **Audit_log_enabled** parametresini on olarak güncelleştirin.
    ![ denetim günlüklerini etkinleştir @ no__t-1

1. **Audit_log_events** parametresini güncelleştirerek günlüğe kaydedilecek [olay türlerini](concepts-audit-logs.md#configure-audit-logging) seçin.
    ![ denetim günlüğü olayları @ no__t-1

1. **Audit_log_exclude_users** parametresini güncelleştirerek, günlüğe kaydetme dışında tutulacak MySQL kullanıcılarını ekleyin. Kullanıcıları MySQL Kullanıcı adlarını girerek belirtin.
    ![Denetim günlüğü kullanıcıları hariç tut @ no__t-1

1. Parametreleri değiştirildikten sonra **Kaydet**' e tıklayabilirsiniz. Ya da değişikliklerinizi **atabilirsiniz** .
    ![Kaydet](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Tanılama günlükleri ayarlama

1. Kenar çubuğu 'ndaki **izleme** bölümünde **Tanılama ayarları**' nı seçin.

1. "+ Tanılama ayarı Ekle" seçeneğine tıklayın ![Tanılama ayarı Ekle @ no__t-1

1. Bir tanılama ayarı adı belirtin.

1. Denetim günlüklerinin (depolama hesabı, Olay Hub 'ı ve/veya Log Analytics çalışma alanı) gönderileceği veri havuzlarını belirtin.

1. Günlük türü olarak "MySqlAuditLogs" öğesini seçin.
![tanılama ayarını yapılandırma @ no__t-1

1. Denetim günlüklerini için veri havuzlarını yapılandırdıktan sonra **Kaydet**' e tıklayabilirsiniz.
![Save Diagnostic Setting @ no__t-1

1. Bunları yapılandırdığınız veri havuzları içinde inceleyerek denetim günlüklerine erişin. Günlüklerin görünmesi 10 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

- MySQL için Azure veritabanı 'nda [Denetim günlükleri](concepts-audit-logs.md) hakkında daha fazla bilgi edinin.