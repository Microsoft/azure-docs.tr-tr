---
title: Depolamayı otomatik Büyüt-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, PostgreSQL için Azure veritabanı 'nda Azure portal kullanarak depolama otomatik büyümenin nasıl yapılandırılacağı açıklanmaktadır-tek sunucu
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366139"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda Azure portal kullanarak depolamayı otomatik olarak Büyüt-tek sunucu
Bu makalede, bir PostgreSQL için Azure veritabanı sunucu depolaması, iş yükünü etkilemeden nasıl büyütüleceği açıklanır.

Sunucu ayrılmış depolama sınırına ulaştığında, sunucu salt okunurdur olarak işaretlenir. Ancak, depolama otomatik büyümeye olanak belirtirseniz, sunucu depolaması büyüyen verileri kapsayacak şekilde artar. 100 GB 'den az kullanılabilir depolama alanı olan sunucularda, ücretsiz depolama alanı sağlanan depolamanın en fazla 1 GB veya %10 ' u altına düşdükten sonra sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun 10 GB 'ı altındaysa, sağlanan depolama boyutu %5 oranında artar. [Burada](./concepts-pricing-tiers.md#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:
- [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Depolama otomatik büyümesini etkinleştir 

PostgreSQL Server Storage otomatik büyümesini ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/)var olan PostgreSQL Için Azure veritabanı sunucunuzu seçin.

2. PostgreSQL sunucusu sayfasında, **Ayarlar**' ın altında, Fiyatlandırma Katmanı sayfasını açmak için **fiyatlandırma katmanı** ' na tıklayın.

3. **Otomatik büyüme** bölümünde, depolama otomatik büyümesini etkinleştirmek için **Evet** ' i seçin.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="PostgreSQL için Azure veritabanı-Settings_Pricing_tier-otomatik büyüme":::

4. Değişiklikleri kaydetmek için **Tamam**’a tıklayın.

5. Bir bildirim otomatik büyüme 'nın başarıyla etkinleştirildiğini doğrulayacaktır.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="PostgreSQL için Azure veritabanı-otomatik büyüme başarılı":::

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
