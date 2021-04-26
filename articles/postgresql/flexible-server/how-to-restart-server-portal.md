---
title: Yeniden başlatma-Azure portal-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede, Azure portal aracılığıyla PostgreSQL için Azure veritabanı 'nda yeniden başlatma işlemlerinin nasıl gerçekleştirileceği açıklanır.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941007"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nı yeniden başlatın-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu makalede esnek sunucunun yeniden başlatılmasını gerçekleştirmeye yönelik adım adım yordamlar sunulmaktadır. Bu işlem, veritabanı sunucusunun yeniden başlatılmasını gerektiren herhangi bir statik parametre değişikliğini uygulamak için yararlıdır. Yordam, bölgesel olarak yedekli yüksek kullanılabilirliğe sahip sunucular için de aynıdır. 

> [!IMPORTANT]
> Yüksek kullanılabilirliğiyle yapılandırıldığında, hem birincil hem de bekleme sunucuları aynı anda yeniden başlatılır.

## <a name="pre-requisites"></a>Ön koşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

-   Esnek bir sunucunuz olmalıdır.

## <a name="restart-your-flexible-server"></a>Esnek sunucunuzu yeniden başlatın

Esnek sunucunuzu yeniden başlatmak için aşağıdaki adımları izleyin.

1.  [Azure Portal](https://portal.azure.com/), yeniden başlatmak istediğiniz esnek sunucunuzu seçin.

2.  Sol panelde **Genel Bakış ' a** tıklayın ve **Yeniden Başlat**' a tıklayın.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Seçimi yeniden Başlat":::

3.  Açılır bir onay iletisi görüntülenir.

4.  Devam etmek istiyorsanız **Evet** ' e tıklayın.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Yeniden başlatma Onayla":::
 
6.  Yeniden başlatma işleminin başlatıldığı bir bildirim gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

-   [İş sürekliliği](./concepts-business-continuity.md) hakkında bilgi edinin
-   [Bölge yedekli yüksek kullanılabilirlik](./concepts-high-availability.md) hakkında bilgi edinin
