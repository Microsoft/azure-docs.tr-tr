---
title: Sunucu Yönetme-Azure portal-MySQL için Azure veritabanı
description: Azure portal bir MySQL için Azure veritabanı sunucusunu yönetmeyi öğrenin.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897994"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı sunucusunu yönetme

Bu makalede MySQL sunucuları için Azure veritabanı 'nı nasıl yöneteceğiniz gösterilmektedir. Yönetim görevleri işlem ve depolama ölçeklendirmesi, yönetici parola sıfırlama ve sunucu ayrıntılarını görüntüleme içerir.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
>

## <a name="sign-in"></a>Oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-server"></a>Sunucu oluşturma

MySQL için Azure veritabanı sunucusu oluşturma ve kullanmaya başlama hakkında bilgi edinmek için [hızlı başlangıç](quickstart-create-mysql-server-database-using-azure-portal.md) sayfasını ziyaret edin.

## <a name="scale-compute-and-storage"></a>İşlem ve depolamayı ölçeklendirme

Sunucu oluşturulduktan sonra, gereksinimleriniz değiştikçe Genel Amaçlı ve bellek için Iyileştirilmiş katmanlar arasında ölçeklendirebilirsiniz. Ayrıca, sanal çekirdekleri artırarak veya azaltarak işlem ve belleği da ölçeklendirebilirsiniz. Depolama alanı yukarı ölçeklendirilebilir (ancak, depolamayı ölçeklendirmezsiniz).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Genel Amaçlı ve bellek için Iyileştirilmiş katmanlar arasında ölçeklendirme

Genel Amaçlı En Iyi duruma getirilmiş ve tam tersi yönde ölçeklendirebilirsiniz. Sunucu oluşturulduktan sonra temel katmandan ve arasında geçiş yapmak desteklenmez.

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **fiyatlandırma katmanını** seçin.

2. Ölçeklendirdiğinize göre **genel amaçlı** veya **bellek için iyileştirilmiş**' u seçin.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="MySQL için Azure veritabanı 'nda temel, Genel Amaçlı veya bellek için Iyileştirilmiş katmanı seçme Azure portal ekran görüntüsü":::

   > [!NOTE]
   > Katmanların değiştirilmesi, sunucunun yeniden başlatılmasına neden olur.

3. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.

### <a name="scale-vcores-up-or-down"></a>Sanal çekirdekleri yukarı veya aşağı ölçeklendirme

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **fiyatlandırma katmanını** seçin.

2. Kaydırıcıyı istediğiniz değere taşıyarak **Vcore** ayarını değiştirin.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="MySQL için Azure veritabanı 'nda vCore seçeneğini seçme Azure portal ekran görüntüsü":::

    > [!NOTE]
    > Sanal çekirdekleri ölçeklendirmek, sunucunun yeniden başlatılmasına neden olur.

3. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.

### <a name="scale-storage-up"></a>Depolama ölçeğini artırma

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **fiyatlandırma katmanını** seçin.

2. Kaydırıcıyı istediğiniz değere taşıyarak **depolama** ayarını değiştirin.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="MySQL için Azure veritabanı 'nda depolama ölçeğini seçme Azure portal ekran görüntüsü":::

   > [!NOTE]
   > Depolama alanı aşağı ölçeklendirilmez.

3. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.

## <a name="update-admin-password"></a>Yönetici parolasını Güncelleştir

Azure portal kullanarak yönetici rolü parolasını değiştirebilirsiniz.

1. Azure portal sunucunuzu seçin. **Genel bakış** penceresinde **Parolayı Sıfırla**' yı seçin.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="MySQL için Azure veritabanı 'nda parolayı sıfırlamaya Azure portal ekran görüntüsü":::

2. Yeni bir parola girin ve parolayı onaylayın. Metin kutusu sizden parola karmaşıklığı gereksinimlerini ister.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Parola sıfırlama ve MySQL için Azure veritabanı 'na kaydetme Azure portal ekran görüntüsü":::

3. Yeni parolayı kaydetmek için **Tamam ' ı** seçin.
 

> [!IMPORTANT]
> Sunucu yönetici parolası sıfırlandığında sunucu yönetici ayrıcalıkları da otomatik olarak varsayılan değerlerine sıfırlanır. Sunucu Yöneticisi ayrıcalıklarından birini veya birkaçını yanlışlıkla iptal ederseniz Sunucu Yöneticisi parolanızı sıfırlamayı düşünün.
   
> [!NOTE]
> Sunucu Yöneticisi kullanıcısı varsayılan olarak aşağıdaki ayrıcalıklara sahiptir: seçme, ekleme, GÜNCELLEŞTIRME, SILME, oluşturma, BıRAKMA, yeniden yükleme, Işleme, BAŞVURULARı, DIZIN, DEĞIŞTIRME, VERITABANLARıNı gösterme, GEÇICI tablolar oluşturma, tablo KILITLEME, yürütme, çoğaltma BAĞıMLı, çoğaltma ISTEMCISI, görünüm oluşturma, görünümü gösterme, rutın oluşturma, yordamı DEĞIŞTIRME, Kullanıcı oluşturma, olay, TETIKLEYICI

## <a name="delete-a-server"></a>Sunucu silme

Artık gerekmiyorsa, sunucunuzu silebilirsiniz.

1. Azure portal sunucunuzu seçin. **Genel bakış** penceresinde **Sil**' i seçin.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="MySQL için Azure veritabanı 'nda sunucuyu silmek için Azure portal ekran görüntüsü":::

2. Bu sunucunun silmek istediğiniz sunucu olduğunu onaylamak için, giriş kutusuna sunucunun adını yazın.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="MySQL için Azure veritabanı 'nda sunucu silmeyi onaylamak için Azure portal ekran görüntüsü":::

   > [!NOTE]
   > Sunucu silindiğinde geri alınamaz.

3. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Yedeklemeler ve sunucu geri yükleme](howto-restore-server-portal.md) hakkında bilgi edinin
- [MySQL Için Azure veritabanı 'nda ayarlama ve izleme seçenekleri](concepts-monitoring.md) hakkında bilgi edinin
