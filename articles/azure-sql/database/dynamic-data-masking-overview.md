---
title: Dinamik veri maskeleme
description: Dinamik veri maskeleme, Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics için ayrıcalıksız kullanıcılara maskeleyerek hassas veri pozlamasını kısıtlar
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/25/2021
tags: azure-synpase
ms.openlocfilehash: ccc648f06cad46b490ac73fe777066c9de2ff2b9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551654"
---
# <a name="dynamic-data-masking"></a>Dinamik veri maskeleme 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics, dinamik veri maskeleme 'yi destekler. Dinamik veri maskeleme, hassas verilerin görünürlüğünü ayrıcalık sahibi olmayan kullanıcılardan gizler. 

Dinamik veri maskeleme müşterilerin uygulama katmanını çok az etkileyerek hassas verilerin ne kadarının gösterileceğini belirlemelerini sağlar ve hassas verilere yetkisiz erişimin engellenmesine yardımcı olur. Bu özellik, hassas verileri belirlenen veritabanı alanlarına yapılan sorgunun sonuç kümesinde gizleyen ancak veritabanındaki verileri değiştirmeyen ilke tabanlı bir güvenlik özelliğidir.

Örneğin, bir çağrı merkezindeki bir hizmet temsilcisi, kendi e-posta adreslerinin birkaç karakterini onaylayan bir çağrıyı tanımlayabilir, ancak e-posta adresinin tamamı hizmet temsilcisine görmemelidir. Herhangi bir sorgunun sonuç kümesindeki tüm e-posta adreslerini maskele alan bir maskeleme kuralı tanımlanabilir. Başka bir örnek olarak, bir geliştiricinin uyumluluk düzenlemelerini ihlal etmeden üretim ortamlarını, sorun giderme amacıyla sorgulayabilmesi için, kişisel verileri korumak üzere uygun bir veri maskesi tanımlanabilir.

## <a name="dynamic-data-masking-basics"></a>Dinamik veri maskeleme temelleri

SQL veritabanı yapılandırma bölmesinizdeki **güvenlik** altında **dinamik veri maskeleme** dikey penceresini seçerek Azure Portal dinamik veri maskeleme ilkesi ayarlarsınız. Bu özellik SQL yönetilen örneği için Portal kullanılarak ayarlanamaz (PowerShell veya REST API kullanın). Daha fazla bilgi için bkz. [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking).

### <a name="dynamic-data-masking-policy"></a>Dinamik veri maskeleme ilkesi

* **Maskeden dışlanan SQL kullanıcıları** -SQL kullanıcıları veya Azure AD kimlikleri KÜMESI, SQL sorgusu sonuçlarında maskelenmemiş verileri alır. Yönetici ayrıcalıklarına sahip kullanıcılar her zaman maskelemeden çıkarılır ve herhangi bir maske olmadan özgün verileri görür.
* **Maskeleme kuralları** -maskelenecek belirlenen alanları ve kullanılan maskeleme işlevini tanımlayan bir kurallar kümesi. Belirlenen alanlar bir veritabanı şeması adı, tablo adı ve sütun adı kullanılarak tanımlanabilir.
* **Maskeleme işlevleri** -farklı senaryolara yönelik verilerin görünürlüğünü denetleyen bir yöntemler kümesi.

| Maskeleme işlevi | Maskeleme mantığı |
| --- | --- |
| **Varsayılanını** |**Belirlenen alanların veri türlerine göre tam maskeleme**<br/><br/>• Alanın boyutu dize veri türleri için 4 karakterden azsa XXXX veya daha az XS kullanın (nchar, ntext, nvarchar).<br/>• Sayısal veri türleri için sıfır değeri kullanın (BigInt, bit, Decimal, INT, Money, numeric, smallint, smallmoney, tinyint, float, Real).<br/>• Tarih/saat veri türleri için 01-01-1900 kullanın (Date, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• SQL değişkeni için geçerli türün varsayılan değeri kullanılır.<br/>• XML için belge \<masked/> kullanılır.<br/>• Özel veri türleri (timestamp tablosu, HierarchyID, GUID, binary, Image, varbinary uzamsal türler) için boş bir değer kullanın. |
| **Kredi kartı** |**Belirlenen alanların son dört basamağını sunan maskeleme yöntemi** ve bir kredi kartı biçiminde önek olarak bir sabit dize ekler.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-posta** |**İlk harfi kullanıma sunan maskeleme yöntemi ve** bir e-posta adresi biçiminde bir sabit dize öneki kullanarak etki alanını xxx.com ile değiştirir.<br/><br/>aXX@XXXX.com |
| **Rastgele sayı** |Maske yöntemi, seçilen sınırlara ve gerçek veri türlerine göre **rastgele bir sayı üretir** . Belirlenen sınırlar eşitse, maskeleme işlevi sabit bir sayıdır.<br/><br/>![Rastgele bir sayı oluşturmak için maskeleme yöntemini gösteren ekran görüntüsü.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Özel metin** |**İlk ve son karakterlerin ortaya** çıkardığı ve ortadaki özel bir doldurma dizesi ekleyen maskeleme yöntemi. Özgün dize, gösterilen önek ve sonek değerinden kısaysa, yalnızca doldurma dizesi kullanılır. <br/>önek [Padding] soneki<br/><br/>![Gezinti bölmesi](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Maskelenmesi önerilen alanlar

DDM öneriler altyapısı, veritabanınızdaki belirli alanları potansiyel olarak hassas alanlar olarak bayraklar ve bu, maskeleme için iyi adaylar olabilir. Portaldaki dinamik veri maskeleme dikey penceresinde veritabanınız için önerilen sütunları görürsünüz. Yapmanız gereken tek şey, bir veya daha fazla sütun için **maske Ekle** ' ye tıkladıktan sonra bu alanlar için bir maske uygulamak üzere **kaydedilir** .

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>PowerShell cmdlet 'lerini kullanarak veritabanınız için dinamik veri maskeleme ayarlama

### <a name="data-masking-policies"></a>Veri maskeleme ilkeleri

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Veri maskeleme kuralları

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>REST API kullanarak veritabanınız için dinamik veri maskeleme ayarlayın

Veri maskeleme ilkesini ve kurallarını programlı bir şekilde yönetmek için REST API kullanabilirsiniz. Yayımlanan REST API aşağıdaki işlemleri destekler:

### <a name="data-masking-policies"></a>Veri maskeleme ilkeleri

- [Oluştur veya Güncelleştir](/rest/api/sql/2014-04-01/datamaskingpolicies/createorupdate): veritabanı veri maskeleme ilkesi oluşturur veya güncelleştirir.
- [Get](/rest/api/sql/2014-04-01/datamaskingpolicies/get): bir veritabanı veri maskeleme ilkesi alır. 

### <a name="data-masking-rules"></a>Veri maskeleme kuralları

- [Oluştur veya Güncelleştir](/rest/api/sql/2014-04-01/datamaskingrules/createorupdate): bir veritabanı veri maskeleme kuralı oluşturur veya güncelleştirir.
- [Veritabanına göre Listele](/rest/api/sql/2014-04-01/datamaskingrules/listbydatabase): veritabanı veri maskeleme kurallarının bir listesini alır.

## <a name="permissions"></a>İzinler

Dinamik veri maskeleme, Azure SQL veritabanı yöneticisi, Sunucu Yöneticisi veya rol tabanlı erişim denetimi (RBAC) [SQL Güvenlik Yöneticisi](../../role-based-access-control/built-in-roles.md#sql-security-manager) rolü tarafından yapılandırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

[Dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking)
