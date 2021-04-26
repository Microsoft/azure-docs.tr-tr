---
title: Sunucu Yönetme-Azure portal-PostgreSQL için Azure veritabanı-esnek sunucu
description: Azure portal bir PostgreSQL için Azure veritabanı 'nı yönetmeyi öğrenin-esnek sunucu.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91961417"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure portal kullanarak bir PostgreSQL için Azure veritabanı 'nı yönetme-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu makalede, PostgreSQL için Azure veritabanınızı yönetme (esnek sunucu) açıklanmaktadır. Yönetim görevleri işlem ve depolama ölçeklendirmesi, yönetici parola sıfırlama ve sunucu ayrıntılarını görüntüleme içerir.

## <a name="sign-in"></a>Oturum açın

[Azure portalında](https://portal.azure.com) oturum açın. Azure portal esnek sunucu kaynağına gidin.

## <a name="scale-compute-and-storage"></a>İşlem ve depolamayı ölçeklendirme

Sunucu oluşturulduktan sonra gereksinimlerinize göre çeşitli [fiyatlandırma katmanları](https://azure.microsoft.com/pricing/details/postgresql/) arasında ölçeklendirebilirsiniz. Ayrıca sanal çekirdek sayısını artırıp azaltarak işlem ve bellek kapasitesinin ölçeğini artırabilir veya azaltabilirsiniz.

> [!NOTE]
> Depolama alanı, daha düşük değere ölçeklendirilmez.

1. Azure portal sunucunuzu seçin. **Ayarlar** bölümünde bulunan **işlem + depolama**' yı seçin.
2. Daha yüksek işlem katmanını kullanarak sunucuyu ölçeklendirmek veya depolama ya da sanal çekirdekleri istediğiniz değere yükselterek aynı katmanda Ölçeği artırmak için **Işlem katmanını** , **sanal çekirdeği**, **depolamayı** değiştirebilirsiniz.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="depolama esnek sunucusunu ölçeklendirme":::

> [!Important]
> - Depolama alanı aşağı ölçeklendirilmez.
> - Sanal çekirdekleri ölçeklendirmek, sunucunun yeniden başlatılmasına neden olur.

3. Değişiklikleri kaydetmek için **Tamam ' ı** seçin.

## <a name="reset-admin-password"></a>Yönetici parolasını sıfırla

Azure portal kullanarak yönetici rolü parolasını değiştirebilirsiniz.

1. Azure portal sunucunuzu seçin. **Genel bakış** penceresinde **Parolayı Sıfırla**' yı seçin.
2. Yeni bir parola girin ve parolayı onaylayın. Metin kutusu sizden parola karmaşıklığı gereksinimlerini ister.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="esnek sunucu için parolanızı sıfırlayın":::

3. Yeni parolayı kaydetmek için **Kaydet** ' i seçin.

## <a name="delete-a-server"></a>Sunucu silme

Artık gerekmiyorsa, sunucunuzu silebilirsiniz.

1. Azure portal sunucunuzu seçin. **Genel bakış** penceresinde **Sil**' i seçin.
2. Sunucuyu silmek istediğinizi onaylamak için giriş kutusuna sunucunun adını yazın.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="esnek sunucuyu silme":::

   > [!IMPORTANT]
   > Sunucu silindiğinde geri alınamaz.

  > [!div class="mx-imgBorder"]
  > ![esnek sunucuyu silme](./media/howto-manage-server-portal/delete-server.png)  

3. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Yedekleme ve geri yükleme kavramlarını anlama](concepts-backup-restore.md)
- [Sunucuyu ayarlama ve izleme](concepts-monitoring.md)
