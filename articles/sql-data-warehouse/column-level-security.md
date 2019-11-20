---
title: Sütun düzeyi güvenlik
description: Sütun düzeyinde güvenlik (CLS), müşterilerin, Kullanıcı yürütme bağlamına veya grup üyeliğine göre veritabanı tablo sütunlarına erişimi denetlemesine olanak sağlar. CLS, uygulamanızdaki güvenliğin tasarımını ve kodlamasını basitleştirir. CLS, sütun erişimi üzerinde kısıtlamalar uygulamanıza olanak sağlar.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 85f705022a0ff5970d30c61206d4f2631254b7ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077099"
---
# <a name="column-level-security"></a>Sütun düzeyinde güvenlik
Sütun düzeyinde güvenlik (CLS), müşterilerin, Kullanıcı yürütme bağlamına veya grup üyeliğine göre veritabanı tablo sütunlarına erişimi denetlemesine olanak sağlar.
Aşağıdaki videoya Güncelleştir-Bu videonun gönderildiği [satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) , SQL Data Warehouse 'da da kullanılabilir. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS, uygulamanızdaki güvenliğin tasarımını ve kodlamasını basitleştirir. CLS, hassas verileri korumak için sütun erişimiyle ilgili kısıtlamalar uygulamanıza olanak sağlar. Örneğin, belirli kullanıcıların departmanıyla ilgili bir tablonun yalnızca belirli sütunlarına erişebilmesini sağlama. Erişim kısıtlama mantığı, başka bir uygulama katmanındaki verilerden uzakta değil, veritabanı katmanında bulunur. Veri erişimi herhangi bir katmandan her denendiğinde, veritabanı erişim kısıtlamalarını uygular. Bu kısıtlama, genel güvenlik sisteminizin yüzey alanını azaltarak güvenlik sisteminizi daha güvenilir ve sağlam hale getirir. Ayrıca, CLS, kullanıcıların erişim kısıtlamalarını kullanıcılara sağlamak için sütunları filtrelemeye yönelik görüntüleme gereksinimini ortadan kaldırır.

[Ver](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL ifadesiyle CLS 'yi uygulayabilirsiniz. Bu mekanizmaya, hem SQL hem de Azure Active Directory (AAD) kimlik doğrulaması desteklenir.

![CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sözdizimi

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Örnek
Aşağıdaki örnek, ' TestUser ' tablosunun ' SSK ' sütununa erişimini nasıl kısıtlayabileceğini gösterir:

Sosyal güvenlik numaralarını depolamak için kullanılan SSK sütunuyla ' Membership ' tablosu oluşturun:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

' TestUser ' öğesine, gizli verileri olan SSCOLUMN hariç tüm sütunlara erişmesine izin ver:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

' TestUser ' olarak yürütülen sorgular, SSN sütununu içeriyorsa başarısız olur:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Kullanım Örnekleri
Şu anda CLS 'nin nasıl kullanıldığı hakkında bazı örnekler:
- Finans hizmetleri firması, yalnızca hesap yöneticilerinin müşteri sosyal güvenlik numaralarına (SSN), telefon numaralarına ve diğer kişisel olarak tanımlanabilen bilgilere (PII) erişmesine izin verir.
- Sağlık hizmetleri sağlayıcısı, yalnızca doktorlarla ve nur'lerin, faturalandırma departmanı üyelerinin bu verileri görüntülemesine izin vermeyerek gizli tıbbi kayıtlarına erişmesine izin verir.
