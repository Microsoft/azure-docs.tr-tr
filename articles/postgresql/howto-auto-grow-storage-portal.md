---
title: PostgreSQL için Azure veritabanı 'nda Azure portal kullanarak depolamayı otomatik olarak Büyüt-tek sunucu
description: Bu makalede, PostgreSQL için Azure veritabanı-tek sunucu içindeki Azure portal kullanarak otomatik büyüme depolamayı nasıl etkinleştirebileceğinizi açıklamaktadır.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 1d028093b030e1f2cf00ceae9297563c36c314c5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142861"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda Azure portal kullanarak depolamayı otomatik olarak Büyüt-tek sunucu
Bu makalede, bir PostgreSQL için Azure veritabanı sunucu depolaması, iş yükünü etkilemeden nasıl büyütüleceği açıklanır.

Sunucu ayrılmış depolama sınırına ulaştığında, sunucu salt okunurdur olarak işaretlenir. Ancak, depolama otomatik büyümeye olanak belirtirseniz, sunucu depolaması büyüyen verileri kapsayacak şekilde artar. 100 GB 'den az kullanılabilir depolama alanı olan sunucularda, ücretsiz depolama alanı sağlanan depolamanın en fazla 1 GB veya% 10 ' u altına düşdükten sonra sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun% 5 ' inden az olduğunda sağlanan depolama boyutu% 5 oranında artar. [Burada](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda tamamlanması gerekir:
- [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Depolama otomatik büyümesini etkinleştir 

PostgreSQL Server Storage otomatik büyümesini ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/)var olan PostgreSQL Için Azure veritabanı sunucunuzu seçin.

2. PostgreSQL sunucusu sayfasında, **Ayarlar**' ın altında, Fiyatlandırma Katmanı sayfasını açmak için **fiyatlandırma katmanı** ' na tıklayın.

3. **Otomatik büyüme** bölümünde, depolama otomatik büyümesini etkinleştirmek için **Evet** ' i seçin.

    ![PostgreSQL için Azure veritabanı-Settings_Pricing_tier-otomatik büyüme](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Değişiklikleri kaydetmek için **Tamam** 'a tıklayın.

5. Bir bildirim otomatik büyüme 'nın başarıyla etkinleştirildiğini doğrulayacaktır.

    ![PostgreSQL için Azure veritabanı-otomatik büyüme başarılı](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
