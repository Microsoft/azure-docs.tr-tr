---
title: Sunucu Yönetme-Azure portal-MariaDB için Azure veritabanı
description: MariaDB sunucusu için Azure veritabanı 'nı Azure portal yönetme hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: ade70c884a3ef01db45273e4789f34b629473661
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664410"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Azure portal kullanarak bir MariaDB sunucusu için Azure veritabanı 'nı yönetme
Bu makalede, MariaDB sunucuları için Azure veritabanınızı nasıl yöneteceğiniz gösterilmektedir. Yönetim görevleri işlem ve depolama ölçeklendirmesi, yönetici parola sıfırlama ve sunucu ayrıntılarını görüntüleme içerir.

## <a name="sign-in"></a>Oturum açın
[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-server"></a>Sunucu oluşturma
MariaDB sunucusu için Azure veritabanı oluşturma ve kullanmaya başlama hakkında bilgi edinmek için [hızlı başlangıç](quickstart-create-mariadb-server-database-using-azure-portal.md) sayfasını ziyaret edin.

## <a name="scale-compute-and-storage"></a>İşlem ve depolamayı ölçeklendirme

Sunucu oluşturulduktan sonra, gereksinimleriniz değiştikçe Genel Amaçlı ve bellek için Iyileştirilmiş katmanlar arasında ölçeklendirebilirsiniz. Ayrıca, sanal çekirdekleri artırarak veya azaltarak işlem ve belleği da ölçeklendirebilirsiniz. Depolama alanı yukarı ölçeklendirilebilir (ancak, depolamayı ölçeklendirmezsiniz).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Genel Amaçlı ve bellek için Iyileştirilmiş katmanlar arasında ölçeklendirme

Genel Amaçlı En Iyi duruma getirilmiş ve tam tersi yönde ölçeklendirebilirsiniz. Sunucu oluşturulduktan sonra temel katmandan ve arasında geçiş yapmak desteklenmez. 

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **fiyatlandırma katmanını** seçin.

2. Ölçeklendirdiğinize göre **genel amaçlı** veya **bellek için iyileştirilmiş**' u seçin. 

    ![Ekran görüntüsünde, fiyatlandırma katmanının seçildiği Azure portal ve bellek için Iyileştirilmiş bir değer gösterilir.](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Katmanların değiştirilmesi, sunucunun yeniden başlatılmasına neden olur.

4. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.


### <a name="scale-vcores-up-or-down"></a>Sanal çekirdekleri yukarı veya aşağı ölçeklendirme

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **fiyatlandırma katmanını** seçin.

2. Kaydırıcıyı istediğiniz değere taşıyarak **Vcore** ayarını değiştirin.

    ![ölçek-işlem](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Sanal çekirdekleri ölçeklendirmek, sunucunun yeniden başlatılmasına neden olur.

3. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.


### <a name="scale-storage-up"></a>Depolama ölçeğini artırma

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **fiyatlandırma katmanını** seçin.

2. Kaydırıcıyı istediğiniz değere taşıyarak **depolama** ayarını değiştirin.

    ![ölçek depolama](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Depolama alanı aşağı ölçeklendirilmez.

3. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.


## <a name="update-admin-password"></a>Yönetici parolasını Güncelleştir
Azure portal kullanarak yönetici rolü parolasını değiştirebilirsiniz.

1. Azure portal sunucunuzu seçin. **Genel bakış** penceresinde **Parolayı Sıfırla**' yı seçin.

   ![genel bakış](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Yeni bir parola girin ve parolayı onaylayın. Metin kutusu sizden parola karmaşıklığı gereksinimlerini ister.

   ![Ekran görüntüsü parola ile Parolayı Sıfırla iletişim kutusunu gösterir ve parolayı onaylayın.](./media/howto-create-manage-server-portal/reset-password.png)

3. Yeni parolayı kaydetmek için **Tamam ' ı** seçin.


## <a name="delete-a-server"></a>Sunucu silme

Artık gerekmiyorsa, sunucunuzu silebilirsiniz. 

1. Azure portal sunucunuzu seçin. **Genel bakış** penceresinde **Sil**' i seçin.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Bu sunucunun silmek istediğiniz sunucu olduğunu onaylamak için, giriş kutusuna sunucunun adını yazın.

    ![Ekran görüntüsü, geri alınamaz bir veritabanını silmek isteyip istemediğinizi doğrulayan bir iletişim kutusu gösterir.](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Sunucu silindiğinde geri alınamaz.

3. **Sil**’i seçin.


## <a name="next-steps"></a>Sonraki adımlar
- [Yedeklemeler ve sunucu geri yükleme](howto-restore-server-portal.md) hakkında bilgi edinin
- [MariaDB Için Azure veritabanı 'nda ayarlama ve izleme seçenekleri](concepts-monitoring.md) hakkında bilgi edinin
