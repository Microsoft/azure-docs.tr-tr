---
title: Stretch Database için Saydam Veri Şifrelemesi etkinleştir
description: Azure 'da SQL Server Stretch Database için Saydam Veri Şifrelemesi (TDE) etkinleştirme
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034006"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Azure 'da Stretch Database için Saydam Veri Şifrelemesi (TDE) etkinleştir
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Saydam Veri Şifrelemesi (TDE), uygulamada değişiklik yapılmasına gerek kalmadan veritabanı, ilişkili yedeklemeler ve bekleyen işlem günlüğü dosyalarının gerçek zamanlı şifrelemesini ve şifre çözmeyi gerçekleştirerek kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olur.

TDE, veritabanı şifreleme anahtarı adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler. Veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur. Yerleşik sunucu sertifikası her bir Azure sunucusu için benzersizdir. Microsoft bu sertifikaları en az 90 günde bir otomatik olarak döndürür. TDE genel bir açıklaması için, bkz. [Saydam veri şifrelemesi (tde)].

## <a name="enabling-encryption"></a>Şifrelemeyi etkinleştirme
Esnetme özellikli bir SQL Server veritabanından geçirilen verileri depolayan bir Azure veritabanı için TDE 'yı etkinleştirmek üzere aşağıdaki işlemleri yapın:

1. Veritabanını [Azure Portal](https://portal.azure.com) açın
2. Veritabanı dikey penceresinde **Ayarlar** düğmesine tıklayın.
3. **Saydam veri şifreleme** seçeneğini belirleyin ![][1]
4. **Açık** ayarını seçin ve ardından
   **Kaydet** ' i seçin ![][2]

## <a name="disabling-encryption"></a>Şifrelemeyi devre dışı bırakma
Esnetme özellikli bir SQL Server veritabanından geçirilen verileri depolayan bir Azure veritabanı için TDE ' ı devre dışı bırakmak için aşağıdaki işlemleri yapın:

1. Veritabanını [Azure Portal](https://portal.azure.com) açın
2. Veritabanı dikey penceresinde **Ayarlar** düğmesine tıklayın.
3. **Saydam veri şifreleme** seçeneğini belirleyin
4. **Kapalı** ayarını seçin ve ardından **Kaydet** ' i seçin.

<!--Anchors-->
[Saydam Veri Şifrelemesi (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
