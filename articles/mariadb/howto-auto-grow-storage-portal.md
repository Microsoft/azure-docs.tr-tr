---
title: Depolamayı otomatik Büyüt-Azure portal-MariaDB için Azure veritabanı
description: Bu makalede, Azure portal kullanarak MariaDB için Azure veritabanı için otomatik büyüme depolamayı nasıl etkinleştirebileceğinizi açıklamaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366122"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure portal kullanarak MariaDB için Azure veritabanı 'nda depolamayı otomatik olarak büyüt
Bu makalede, bir MariaDB sunucu depolaması için Azure veritabanı 'nı, iş yükünü etkilemeden büyümeden nasıl yapılandırabileceğiniz açıklanmaktadır.

Sunucu ayrılmış depolama sınırına ulaştığında, sunucu salt okunurdur olarak işaretlenir. Ancak, depolama otomatik büyümeye olanak belirtirseniz, sunucu depolaması büyüyen verileri kapsayacak şekilde artar. 100 GB 'den az kullanılabilir depolama alanı olan sunucularda, ücretsiz depolama alanı sağlanan depolamanın en fazla 1 GB veya %10 ' u altına düşdükten sonra sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun 10 GB 'ı altındaysa, sağlanan depolama boyutu %5 oranında artar. [Burada](concepts-pricing-tiers.md#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:
- [MariaDB sunucusu Için Azure veritabanı](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Depolama otomatik büyümesini etkinleştir 

MariaDB Server Storage otomatik büyümesini ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), var olan MariaDB sunucusu Için Azure veritabanınızı seçin.

2. MariaDB sunucusu sayfasında, **Ayarlar** başlığı altında **fiyatlandırma** katmanı ' nı tıklatarak Fiyatlandırma Katmanı sayfasını açın.

3. Otomatik büyüme bölümünde, depolama otomatik büyümesini etkinleştirmek için **Evet** ' i seçin.

    ![MariaDB için Azure veritabanı-Settings_Pricing_tier-otomatik büyüme](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Değişiklikleri kaydetmek için **Tamam**’a tıklayın.

5. Bir bildirim otomatik büyüme 'nın başarıyla etkinleştirildiğini doğrulayacaktır.

    ![MariaDB için Azure veritabanı-otomatik büyüme başarılı](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-metric.md)hakkında bilgi edinin.
