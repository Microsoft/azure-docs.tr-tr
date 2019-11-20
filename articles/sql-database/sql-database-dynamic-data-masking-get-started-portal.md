---
title: 'Azure portal: dinamik veri maskeleme'
description: Azure portal SQL veritabanı dinamik veri maskelemeyi kullanmaya başlama
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: 0053958c0681e98ee225232249b050583d4e13b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826559"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure portal ile SQL veritabanı dinamik veri maskeleme ile çalışmaya başlama

Bu makalede, Azure portal ile [dinamik veri maskeleme](sql-database-dynamic-data-masking-get-started.md) 'nin nasıl uygulanacağı gösterilmektedir. Ayrıca, [Azure SQL veritabanı cmdlet 'lerini](https://docs.microsoft.com/powershell/module/az.sql/) veya [REST API](https://docs.microsoft.com/rest/api/sql/)kullanarak dinamik veri maskeleme uygulayabilirsiniz.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure portal kullanarak veritabanınız için dinamik veri maskeleme ayarlayın

1. [https://portal.azure.com](https://portal.azure.com)Azure Portal başlatın.
2. Maskelemek istediğiniz hassas verileri içeren veritabanının ayarlar sayfasına gidin.
3. **Dinamik veri maskeleme** yapılandırma sayfasını başlatan **dinamik veri maskeleme** kutucuğuna tıklayın.

   * Alternatif olarak, **işlemler** bölümüne kaydırabilir ve **dinamik veri maskeleme**' ye tıklayabilirsiniz.

     ![Gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. **Dinamik veri maskeleme** yapılandırması sayfasında, öneriler altyapısının maskeleme için işaretlediğini belirten bazı veritabanı sütunlarını görebilirsiniz. Önerileri kabul etmek için, bir veya daha fazla sütun için **maske Ekle** ' ye tıklamanız yeterlidir ve bu sütunun varsayılan türüne göre bir maske oluşturulur. Maskeleme kuralına tıklayarak Maskeleme işlevini değiştirebilirsiniz ve maskeleme alanı biçimini istediğiniz farklı bir biçime düzenleyebilirsiniz. Ayarlarınızı kaydetmek için **Kaydet** ' e tıkladığınızdan emin olun.

    ![Gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Veritabanınıza herhangi bir sütun için bir maske eklemek istiyorsanız, **dinamik veri maskeleme** yapılandırma sayfasının üst kısmında **maske Ekle** ' ye tıklayarak **maskeleme kuralı Yapılandırması Ekle** sayfasını açın.

    ![Gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Maskeleme için belirlenen alanı tanımlamak için **Şemayı**, **tabloyu** ve **sütunu** seçin.
7. Hassas veri maskeleme kategorileri listesinden bir **maskeleme alanı biçimi** seçin.

    ![Gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Dinamik veri maskeleme ilkesindeki maskeleme kuralları kümesini güncelleştirmek için veri maskeleme kuralı sayfasında **Kaydet** ' e tıklayın.
9. Maskelemeden dışlanması gereken SQL kullanıcıları veya AAD kimliklerini yazın ve maskelenmemiş gizli verilere erişime sahip olun. Bu, noktalı virgülle ayrılmış bir kullanıcı listesi olmalıdır. Yönetici ayrıcalıklarına sahip olan kullanıcılar, her zaman ilk maskelenmemiş veriye erişebilir.

    ![Gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Bunu yapmak için uygulama katmanının, uygulama ayrıcalıklı kullanıcıları için hassas verileri görüntülemesi amacıyla, uygulamanın veritabanını sorgulamak için kullandığı SQL kullanıcısını veya AAD kimliğini ekleyin. Hassas verilerin açıklanmasını en aza indirmek için bu listenin en az sayıda ayrıcalıklı kullanıcı içermesi önerilir.

10. Yeni veya güncelleştirilmiş maskeleme ilkesini kaydetmek için veri maskeleme yapılandırma sayfasında **Kaydet** ' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* Dinamik veri maskeleme 'ye genel bakış için bkz. [dinamik veri maskeleme](sql-database-dynamic-data-masking-get-started.md).
* Ayrıca, [Azure SQL veritabanı cmdlet 'lerini](https://docs.microsoft.com/powershell/module/az.sql/) veya [REST API](https://docs.microsoft.com/rest/api/sql/)kullanarak dinamik veri maskeleme uygulayabilirsiniz.
