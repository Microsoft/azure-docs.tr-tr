---
title: Dinamik veri maskeleme
description: Dinamik veri maskeleme, SQL veritabanı ve veri ambarı için ayrıcalıklı olmayan kullanıcılarla maskeleyerek hassas veri pozlamasını sınırlar
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: 2ff2dfe6384acc8a56558e6e7ba0fc5cc05f7783
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819965"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Azure SQL veritabanı ve veri ambarı için dinamik veri maskeleme

SQL veritabanı dinamik veri maskeleme, hassas veri pozlamasını ayrıcalıklı olmayan kullanıcılarla maskeleyerek kısıtlar. 

Dinamik veri maskeleme müşterilerin uygulama katmanını çok az etkileyerek hassas verilerin ne kadarının gösterileceğini belirlemelerini sağlar ve hassas verilere yetkisiz erişimin engellenmesine yardımcı olur. Bu özellik, hassas verileri belirlenen veritabanı alanlarına yapılan sorgunun sonuç kümesinde gizleyen ancak veritabanındaki verileri değiştirmeyen ilke tabanlı bir güvenlik özelliğidir.

Örneğin, bir çağrı merkezindeki hizmet temsilcisi çağıranları kredi kartı numarasının birkaç basamağıyla tanımlayabilir, ancak bu veri öğeleri hizmet temsilcisine tam olarak gösterilmemelidir. Bir maskeleme kuralı, herhangi bir sorgu sonuç kümesinde herhangi bir kredi kartı numarasının son dört basamağının dışında bir maske kuralı tanımlanabilir. Başka bir örnek olarak, bir geliştiricinin uyumluluk düzenlemelerini ihlal etmeden üretim ortamlarını, sorun giderme amacıyla sorgulayabilmesi için, kişisel olarak tanımlanabilen bilgiler (PII) verilerini korumak üzere uygun bir veri maskesi tanımlanabilir.

## <a name="dynamic-data-masking-basics"></a>Dinamik veri maskeleme temelleri

SQL veritabanı yapılandırma dikey penceresinde veya ayarlar dikey penceresinde dinamik veri maskeleme işlemini seçerek Azure portal dinamik veri maskeleme ilkesi ayarlarsınız.

### <a name="dynamic-data-masking-permissions"></a>Dinamik veri maskeleme izinleri

Dinamik veri maskeleme, Azure SQL veritabanı yöneticisi, Sunucu Yöneticisi veya [SQL Güvenlik Yöneticisi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) rolleri tarafından yapılandırılabilir.

### <a name="dynamic-data-masking-policy"></a>Dinamik veri maskeleme ilkesi

* **Maskeden dışlanan SQL kullanıcıları** -SQL sorgu sonuçlarında maskelenmemiş verileri alan BIR dizi SQL KULLANıCıSı veya AAD kimliği. Yönetici ayrıcalıklarına sahip kullanıcılar her zaman maskelemeden çıkarılır ve herhangi bir maske olmadan özgün verileri görür.
* **Maskeleme kuralları** -maskelenecek belirlenen alanları ve kullanılan maskeleme işlevini tanımlayan bir kurallar kümesi. Belirlenen alanlar bir veritabanı şeması adı, tablo adı ve sütun adı kullanılarak tanımlanabilir.
* **Maskeleme işlevleri** -farklı senaryolara yönelik verilerin görünürlüğünü denetleyen bir yöntemler kümesi.

| Maskeleme Işlevi | Maskeleme mantığı |
| --- | --- |
| **Varsayılan** |**Belirlenen alanların veri türlerine göre tam maskeleme**<br/><br/>• Alanın boyutu dize veri türleri için 4 karakterden azsa XXXX veya daha az XS kullanın (nchar, ntext, nvarchar).<br/>• Sayısal veri türleri için sıfır değeri kullanın (BigInt, bit, Decimal, INT, Money, numeric, smallint, smallmoney, tinyint, float, Real).<br/>• Tarih/saat veri türleri için 01-01-1900 kullanın (Date, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• SQL değişkeni için geçerli türün varsayılan değeri kullanılır.<br/>• XML için \<maskelenmiş/> kullanılır.<br/>• Özel veri türleri (timestamp tablosu, HierarchyID, GUID, binary, Image, varbinary uzamsal türler) için boş bir değer kullanın. |
| **Kredi kartı** |**Belirlenen alanların son dört basamağını sunan maskeleme yöntemi** ve bir kredi kartı biçiminde önek olarak bir sabit dize ekler.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-posta** |**İlk harfi kullanıma sunan maskeleme yöntemi ve** bir e-posta adresi biçiminde bir sabit dize öneki kullanarak etki alanını xxx.com ile değiştirir.<br/><br/>aXX@XXXX.com |
| **Rastgele sayı** |Maske yöntemi, seçilen sınırlara ve gerçek veri türlerine göre **rastgele bir sayı üretir** . Belirlenen sınırlar eşitse, maskeleme işlevi sabit bir sayıdır.<br/><br/>![gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Özel metin** |**İlk ve son karakterlerin ortaya** çıkardığı ve ortadaki özel bir doldurma dizesi ekleyen maskeleme yöntemi. Özgün dize, gösterilen önek ve sonek değerinden kısaysa, yalnızca doldurma dizesi kullanılır. <br/>önek [Padding] soneki<br/><br/>![gezinti bölmesi](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Maskelenmesi önerilen alanlar

DDM öneriler altyapısı, veritabanınızdaki belirli alanları potansiyel olarak hassas alanlar olarak bayraklar ve bu, maskeleme için iyi adaylar olabilir. Portaldaki dinamik veri maskeleme dikey penceresinde veritabanınız için önerilen sütunları görürsünüz. Yapmanız gereken tek şey, bir veya daha fazla sütun için **maske Ekle** ' ye tıkladıktan sonra bu alanlar için bir maske uygulamak üzere **kaydedilir** .

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>PowerShell cmdlet 'lerini kullanarak veritabanınız için dinamik veri maskeleme ayarlama

Bkz. [Azure SQL veritabanı cmdlet 'leri](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>REST API kullanarak veritabanınız için dinamik veri maskeleme ayarlama

Bkz. [Azure SQL veritabanı işlemleri](https://docs.microsoft.com/rest/api/sql/).
