---
title: 'Hızlı başlangıç: Fivetran ve adanmış SQL Havuzu (eski adıyla SQL DW)'
description: Azure SYNAPSE Analytics 'te Fivetran ve adanmış SQL Havuzu (eski adıyla SQL DW) ile çalışmaya başlayın.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96456372"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Hızlı başlangıç: Azure SYNAPSE Analytics 'te özel SQL Havuzu (eskiden SQL DW) ile Fivetran 

Bu hızlı başlangıçta, yeni bir Fivetran kullanıcısının adanmış bir SQL Havuzu (eski adıyla SQL DW) ile çalışması için nasıl ayarlanacağı açıklanmaktadır. Makalesinde, ayrılmış bir SQL havuzuna (eski adıyla SQL DW) sahip olduğunuz varsayılır.

## <a name="set-up-a-connection"></a>Bağlantı kurma

1. Adanmış SQL havuzunuza (eski adıyla SQL DW) bağlanmak için kullandığınız tam sunucu adını ve veritabanı adını bulun.
    
    Bu bilgileri bulmak için yardıma ihtiyacınız varsa, bkz. [ADANMıŞ SQL havuzunuza bağlanma (eski ADıYLA SQL DW)](sql-data-warehouse-connection-strings.md).

2. Kurulum sihirbazında, veritabanınıza doğrudan veya bir SSH tüneli kullanarak bağlanıp bağlanmayacağını seçin.

   Doğrudan veritabanınıza bağlanmayı seçerseniz, erişime izin vermek için bir güvenlik duvarı kuralı oluşturmanız gerekir. Bu yöntem, en basit ve en güvenli yöntemdir.

   Bir SSH tüneli kullanarak bağlanmayı seçerseniz, Fivetran, ağınızdaki ayrı bir sunucuya bağlanır. Sunucu, veritabanınıza bir SSH tüneli sağlar. Veritabanınız bir sanal ağ üzerinde erişilemeyen bir alt ağdaysa bu yöntemi kullanmanız gerekir.

3. **52.0.2.4** IP adresini sunucu düzeyi güvenlik duvarınızdan, Özel SQL Havuzu (eskı ADıYLA SQL DW) örneğine gelen bağlantılara izin verecek şekilde ekleyin.

   Daha fazla bilgi için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Kullanıcı kimlik bilgilerini ayarlama

1. SQL Server Management Studio (SSMS) veya tercih ettiğiniz aracı kullanarak adanmış SQL havuzunuza (eski adıyla SQL DW) bağlanın. Sunucu Yöneticisi kullanıcısı olarak oturum açın. Ardından, aşağıdaki SQL komutlarını çalıştırarak Fivetran için bir kullanıcı oluşturun:

    - Ana veritabanında: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Adanmış SQL Havuzu (eski adıyla SQL DW) veritabanında:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Fivetran kullanıcısına adanmış SQL havuzunuza (eski adıyla SQL DW) aşağıdaki izinleri verin:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Kullanıcı, PolyBase kullanarak Azure Blob depolamadan dosya yüklediğinde kullanılan veritabanı kapsamlı kimlik bilgileri oluşturmak için DENETIM izni gereklidir.

3. Fivetran kullanıcısına uygun bir kaynak sınıfı ekleyin. Kullandığınız kaynak sınıfı, bir columnstore dizini oluşturmak için gereken belleğe bağlıdır. Örneğin, Marketo ve Salesforce gibi ürünlerle tümleştirmeler, çok sayıda sütun ve ürünlerin kullanacağı daha büyük veri hacmi nedeniyle daha yüksek bir kaynak sınıfı gerektirir. Daha yüksek bir kaynak sınıfı, columnstore dizinleri oluşturmak için daha fazla bellek gerektirir.

    Statik kaynak sınıfları kullanmanızı öneririz. `staticrc20`Kaynak sınıfıyla başlayabilirsiniz. `staticrc20`Kaynak sınıfı, kullandığınız performans düzeyinden bağımsız olarak her kullanıcı için 200 MB ayırır. Columnstore dizin oluşturma ilk kaynak sınıfı düzeyinde başarısız olursa, kaynak sınıfını arttırın.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Daha fazla bilgi için [bellek ve eşzamanlılık sınırları](memory-concurrency-limits.md) ve [kaynak sınıfları](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)hakkında bilgi edinin.


## <a name="connect-from-fivetran"></a>Fivetran 'dan Bağlan

Fivetran hesabınızdan adanmış SQL havuzunuza (eski adıyla SQL DW) bağlanmak için, adanmış SQL havuzunuza erişmek için kullandığınız kimlik bilgilerini girin (eski adıyla SQL DW): 

* Ana bilgisayar (sunucunuzun adı).
* Bağ.
* Veritabanınızı.
* Kullanıcı (Kullanıcı adı, *SERVER_NAME* Azure ana bilgisayar URI 'sinin bir parçası olduğu için **fivetran \@ _SERVER_NAME_** olmalıdır: **_sunucu \_ adı_. Database.Windows.net**).
* Parolayı.
