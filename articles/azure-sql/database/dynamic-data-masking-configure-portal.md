---
title: 'Azure portal: dinamik veri maskeleme'
description: Azure portal Azure SQL veritabanı dinamik veri maskeleme ile çalışmaya başlama
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: e551925bbd1426256a81ac2ca3f245af7697245b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028021"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure portal ile SQL veritabanı dinamik veri maskeleme ile çalışmaya başlama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, Azure portal ile [dinamik veri maskeleme](dynamic-data-masking-overview.md) 'nin nasıl uygulanacağı gösterilmektedir. Ayrıca, [Azure SQL veritabanı cmdlet 'lerini](/powershell/module/az.sql/) veya [REST API](/rest/api/sql/)kullanarak dinamik veri maskeleme uygulayabilirsiniz.

> [!NOTE]
> Bu özellik SQL yönetilen örneği için Portal kullanılarak ayarlanamaz (PowerShell veya REST API kullanın). Daha fazla bilgi için bkz. [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure portal kullanarak veritabanınız için dinamik veri maskeleme ayarlayın

1. Üzerinde Azure portal başlatın [https://portal.azure.com](https://portal.azure.com) .
2. Maskelemek istediğiniz hassas verileri içeren veritabanının ayarlar sayfasına gidin.
3. Veritabanınızın **güvenlik** bölümü altındaki **dinamik veri maskeleme** dikey penceresine tıklayın.

   ![Dinamik veri maskeleme vurgulanmış güvenlik bölümünü gösteren ekran görüntüsü.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. **Dinamik veri maskeleme** yapılandırması sayfasında, öneriler altyapısının maskeleme için işaretlediğini belirten bazı veritabanı sütunlarını görebilirsiniz. Önerileri kabul etmek için, bir veya daha fazla sütun için **maske Ekle** ' ye tıklamanız yeterlidir ve bu sütunun varsayılan türüne göre bir maske oluşturulur. Maskeleme kuralına tıklayarak Maskeleme işlevini değiştirebilirsiniz ve maskeleme alanı biçimini istediğiniz farklı bir biçime düzenleyebilirsiniz. Ayarlarınızı kaydetmek için **Kaydet** ' e tıkladığınızdan emin olun.

    ![Dinamik veri maskeleme yapılandırma sayfasını gösteren ekran görüntüsü.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Veritabanınıza herhangi bir sütun için bir maske eklemek istiyorsanız, **dinamik veri maskeleme** yapılandırma sayfasının üst kısmında **maske Ekle** ' ye tıklayarak **maskeleme kuralı Yapılandırması Ekle** sayfasını açın.

    ![Maske kuralı Yapılandırması Ekle sayfasını gösteren ekran görüntüsü.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Maskeleme için belirlenen alanı tanımlamak için **Şemayı**, **tabloyu** ve **sütunu** seçin.
7. Gizli veri maskeleme kategorileri listesinden **maske oluşturmayı seçin** .

    ![Nasıl maske seçin bölümünün altında hassas veri maskeleme kategorilerini gösteren ekran görüntüsü.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Dinamik veri maskeleme ilkesindeki maskeleme kuralları kümesini güncelleştirmek için veri maskeleme kuralı sayfasında **Ekle** ' ye tıklayın.
9. Maskelemeden dışlanması gereken SQL kullanıcıları veya Azure Active Directory (Azure AD) kimliklerini yazın ve maskelenmemiş gizli verilere erişime sahip olun. Bu, noktalı virgülle ayrılmış bir kullanıcı listesi olmalıdır. Yönetici ayrıcalıklarına sahip olan kullanıcılar, her zaman ilk maskelenmemiş veriye erişebilir.

    ![Gezinti bölmesi](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Bunu yapmak için uygulama katmanının, uygulama ayrıcalıklı kullanıcıları için hassas verileri görüntülemesi amacıyla, uygulamanın veritabanını sorgulamak için kullandığı SQL kullanıcısını veya Azure AD kimliğini ekleyin. Hassas verilerin açıklanmasını en aza indirmek için bu listenin en az sayıda ayrıcalıklı kullanıcı içermesi önerilir.

10. Yeni veya güncelleştirilmiş maskeleme ilkesini kaydetmek için veri maskeleme yapılandırma sayfasında **Kaydet** ' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- Dinamik veri maskeleme 'ye genel bakış için bkz. [dinamik veri maskeleme](dynamic-data-masking-overview.md).
- Ayrıca, [Azure SQL veritabanı cmdlet 'lerini](/powershell/module/az.sql/) veya [REST API](/rest/api/sql/)kullanarak dinamik veri maskeleme uygulayabilirsiniz.
