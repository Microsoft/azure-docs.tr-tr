---
title: Erişim denetim günlükleri-Azure portal-MySQL için Azure veritabanı
description: Bu makalede, Azure portal MySQL için Azure veritabanı 'nda denetim günlüklerinin nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541666"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Azure portal MySQL için Azure veritabanı için Denetim günlüklerini yapılandırma ve erişme

[MySQL Için Azure veritabanı denetim günlükleri](concepts-audit-logs.md) ve tanılama ayarları Azure portal yapılandırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:

- [MySQL için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Denetim günlüğünü yapılandırma

>[!IMPORTANT]
> Sunucunuzun performansının yoğun bir şekilde etkilenmemesini sağlamak için, yalnızca denetim amaçlarıyla gerekli olay türlerini ve kullanıcıları günlüğe kaydetmek önerilir.

Denetim günlüğünü etkinleştirin ve yapılandırın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. MySQL için Azure veritabanı sunucunuzu seçin.

1. Kenar çubuğu 'ndaki **Ayarlar** bölümünde **sunucu parametreleri**' ni seçin.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Sunucu parametreleri":::

1. **Audit_log_enabled** parametresini açık olarak güncelleştirin.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Denetim günlüklerini etkinleştir":::

1. **Audit_log_events** parametresini güncelleştirerek günlüğe kaydedilecek [olay türlerini](concepts-audit-logs.md#configure-audit-logging) seçin.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Denetim günlüğü olayları":::

1. **Audit_log_exclude_users** ve **audit_log_include_users** parametrelerini güncelleştirerek günlük kaydıyla dahil edilecek veya hariç tutulacak MySQL kullanıcılarını ekleyin. Kullanıcıları MySQL Kullanıcı adlarını girerek belirtin.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Denetim günlüğü kullanıcıları hariç tut":::

1. Parametreleri değiştirildikten sonra **Kaydet**' e tıklayabilirsiniz. Ya da değişikliklerinizi **atabilirsiniz** .
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Kaydet":::

## <a name="set-up-diagnostic-logs"></a>Tanılama günlükleri ayarlama

1. Kenar çubuğu 'ndaki **izleme** bölümünde **Tanılama ayarları**' nı seçin.

1. "+ Tanılama ayarı Ekle" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Tanılama ayarı Ekle"::: ' ye tıklayın.

1. Bir tanılama ayarı adı belirtin.

1. Denetim günlüklerinin (depolama hesabı, Olay Hub 'ı ve/veya Log Analytics çalışma alanı) gönderileceği veri havuzlarını belirtin.

1. Günlük türü olarak "MySqlAuditLogs" öğesini seçin.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Tanılama ayarını Yapılandır":::

1. Denetim günlüklerini için veri havuzlarını yapılandırdıktan sonra **Kaydet**' e tıklayabilirsiniz.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Tanılama ayarını kaydet":::

1. Bunları yapılandırdığınız veri havuzları içinde inceleyerek denetim günlüklerine erişin. Günlüklerin görünmesi 10 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

- MySQL için Azure veritabanı 'nda [Denetim günlükleri](concepts-audit-logs.md) hakkında daha fazla bilgi edinin
- [Azure CLI](howto-configure-audit-logs-cli.md) 'de denetim günlüklerini yapılandırma hakkında bilgi edinin