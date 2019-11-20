---
title: Azure portal kullanarak MySQL için Azure veritabanı 'nda depolamayı otomatik olarak büyüt
description: Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı için otomatik büyüme depolamayı nasıl etkinleştirebileceğinizi açıklamaktadır.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 864bfaefba783d93e795e8780cc02dcf991e38f1
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142019"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı 'nda depolamayı otomatik olarak büyüt
Bu makalede, bir MySQL için Azure veritabanı sunucusu depolama alanını, iş yükünü etkilemeden nasıl artırabileceğinizi açıklar.

Sunucu ayrılmış depolama sınırına ulaştığında, sunucu salt okunurdur olarak işaretlenir. Ancak, depolama otomatik büyümeye olanak belirtirseniz, sunucu depolaması büyüyen verileri kapsayacak şekilde artar. 100 GB 'den az kullanılabilir depolama alanı olan sunucularda, ücretsiz depolama alanı sağlanan depolamanın en fazla 1 GB veya% 10 ' u altına düşdükten sonra sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun% 5 ' inden az olduğunda sağlanan depolama boyutu% 5 oranında artar. [Burada](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda tamamlanması gerekir:
- [MySQL Için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Depolama otomatik büyümesini etkinleştir 

MySQL Server depolama alanının otomatik büyümesini ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), var olan MySQL Için Azure veritabanı sunucunuzu seçin.

2. MySQL sunucusu sayfasında, **Ayarlar** başlığı altında **fiyatlandırma** katmanı ' nı tıklatarak Fiyatlandırma Katmanı sayfasını açın.

3. Otomatik büyüme bölümünde, depolama otomatik büyümesini etkinleştirmek için **Evet** ' i seçin.

    ![MySQL için Azure veritabanı-Settings_Pricing_tier-otomatik büyüme](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Değişiklikleri kaydetmek için **Tamam** 'a tıklayın.

5. Bir bildirim otomatik büyüme 'nın başarıyla etkinleştirildiğini doğrulayacaktır.

    ![MySQL için Azure veritabanı-otomatik büyüme başarılı](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
