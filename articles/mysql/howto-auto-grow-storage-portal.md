---
title: Depolamayı otomatik Büyüt-Azure portal-MySQL için Azure veritabanı
description: Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı için otomatik büyüme depolamayı nasıl etkinleştirebileceğinizi açıklamaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: afa62d6e3ec93c18596ee5870361bf3b657619d1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365034"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı 'nda depolamayı otomatik olarak büyüt
Bu makalede, bir MySQL için Azure veritabanı sunucusu depolama alanını, iş yükünü etkilemeden nasıl artırabileceğinizi açıklar.

Sunucu ayrılmış depolama sınırına ulaştığında, sunucu salt okunurdur olarak işaretlenir. Ancak, depolama otomatik büyümeye olanak belirtirseniz, sunucu depolaması büyüyen verileri kapsayacak şekilde artar. 100 GB 'den az kullanılabilir depolama alanı olan sunucularda, ücretsiz depolama alanı sağlanan depolamanın en fazla 1 GB veya %10 ' u altına düşdükten sonra sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun 10 GB 'ı altındaysa, sağlanan depolama boyutu %5 oranında artar. [Burada](./concepts-pricing-tiers.md#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:
- [MySQL Için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Depolama otomatik büyümesini etkinleştir 

MySQL Server depolama alanının otomatik büyümesini ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), var olan MySQL Için Azure veritabanı sunucunuzu seçin.

2. MySQL sunucusu sayfasında, **Ayarlar** başlığı altında **fiyatlandırma** katmanı ' nı tıklatarak Fiyatlandırma Katmanı sayfasını açın.

3. Otomatik büyüme bölümünde, depolama otomatik büyümesini etkinleştirmek için **Evet** ' i seçin.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="MySQL için Azure veritabanı-Settings_Pricing_tier-otomatik büyüme":::

4. Değişiklikleri kaydetmek için **Tamam**’a tıklayın.

5. Bir bildirim otomatik büyüme 'nın başarıyla etkinleştirildiğini doğrulayacaktır.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="MySQL için Azure veritabanı-otomatik büyüme başarılı":::

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
