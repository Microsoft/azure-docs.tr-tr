---
title: Sunucuyu yeniden Başlat-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Azure portal kullanarak bir PostgreSQL için Azure veritabanı 'nı nasıl yeniden başlatabileceğinizi açıklar.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: 1a1afabd606df70ec60cf4fa7c8530ff95a0564f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604864"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>PostgreSQL için Azure veritabanı 'nı yeniden başlatın-Azure portal kullanarak tek bir sunucu
Bu konuda, PostgreSQL için Azure veritabanı sunucusunu nasıl yeniden başlatabileceğinizi açıklar. Sunucu işlemi gerçekleştirdiğinden kısa süreli bir kesinti oluşmasına neden olan bakım nedenleriyle sunucunuzu yeniden başlatmanız gerekebilir.

Hizmet meşgulse sunucu yeniden başlatması engellenir. Örneğin, hizmet, sanal çekirdekleri ölçeklendirirken daha önce istenen bir işlemi işliyor olabilir.
 
> [!NOTE] 
> Yeniden başlatmayı tamamlaması için gereken süre, PostgreSQL kurtarma işlemine bağlıdır. Yeniden başlatma süresini azaltmak için, yeniden başlatmadan önce sunucuda gerçekleşen etkinlik miktarını en aza indirmenizi öneririz. Ayrıca, kontrol noktası sıklığını artırmak isteyebilirsiniz. Ayrıca dahil olmak üzere denetim noktası ilişkili parametre değerlerini de ayarlayabilirsiniz `max_wal_size` . Ayrıca `CHECKPOINT` , sunucuyu yeniden başlatmadan önce komutunu çalıştırmanız önerilir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:
- [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Sunucu yeniden başlatması gerçekleştir

Aşağıdaki adımlar PostgreSQL sunucusunu yeniden başlatır:

1. [Azure Portal](https://portal.azure.com/), PostgreSQL Için Azure veritabanı sunucunuzu seçin.

2. Sunucunun **genel bakış** sayfasının araç çubuğunda **Yeniden Başlat**' a tıklayın.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="PostgreSQL için Azure veritabanı-genel bakış-yeniden Başlat düğmesi":::

3. Sunucuyu yeniden başlatmayı onaylamak için **Evet** ' e tıklayın.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="PostgreSQL için Azure veritabanı-yeniden başlatma Onayla":::

4. Sunucu durumunun "yeniden başlatılıyor" olarak değiştiğini gözlemleyin.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="PostgreSQL için Azure veritabanı-yeniden başlatma durumu":::

5. Sunucu yeniden başlatma işleminin başarılı olduğunu onaylayın.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="PostgreSQL için Azure veritabanı-yeniden başlatma başarılı":::

## <a name="next-steps"></a>Sonraki adımlar

[PostgreSQL Için Azure veritabanı 'nda parametrelerin nasıl ayarlanacağı](howto-configure-server-parameters-using-portal.md) hakkında bilgi edinin
