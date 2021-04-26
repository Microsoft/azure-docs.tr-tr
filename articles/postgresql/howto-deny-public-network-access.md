---
title: Ortak ağ erişimini reddetme-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı tek sunuculu Azure portal kullanarak genel ağ erişimini reddetme hakkında bilgi edinin
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727116"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Azure portal kullanarak PostgreSQL için Azure veritabanı 'nda genel ağ erişimini reddetme

Bu makalede, bir PostgreSQL için Azure veritabanı 'nı tüm genel yapılandırmaların reddedecek ve yalnızca özel uç noktalar aracılığıyla ağ güvenliğini artırmak üzere yalnızca bağlantılara izin verecek şekilde nasıl yapılandırabileceğiniz açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

* Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanı ile [PostgreSQL Için Azure veritabanı tek sunucu](quickstart-create-server-database-portal.md) .

## <a name="set-deny-public-network-access"></a>Ortak ağ erişimini reddet ayarla

PostgreSQL için tek sunuculu ortak ağ erişimini reddet ' i ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), mevcut PostgreSQL Için Azure veritabanı tek sunucu ' yı seçin.

1. PostgreSQL tek sunucusu sayfasında, **Ayarlar** altında bağlantı **güvenliği** ' ne tıklayarak bağlantı güvenliği yapılandırması sayfasını açın.

1. **Ortak ağ erişimini engelle**' de, PostgreSQL tek sunucunuz için genel erişimi Reddet ' i etkinleştirmek için **Evet** ' i seçin.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="PostgreSQL için Azure veritabanı tek sunucu ağ erişimini reddet":::

1. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

1. Bildirim, bağlantı güvenliği ayarının başarıyla etkinleştirildiğini onaylanır.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="PostgreSQL için Azure veritabanı tek sunuculu ağ erişimini reddetme başarılı":::

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
