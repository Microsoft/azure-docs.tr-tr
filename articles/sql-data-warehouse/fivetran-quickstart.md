---
title: Fivetran hızlı başlangıç
description: Fivetran ve Azure SQL veri ambarı ile hızlıca çalışmaya başlayın.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 250df3e106ae65cafc84a412c155e3a27c535c79
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686108"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Fivetran ve SQL veri ambarı ile hızlıca çalışmaya başlayın

Bu hızlı başlangıçta, yeni bir Fivetran kullanıcısının Azure SQL veri ambarı ile çalışmaya nasıl ayarlanacağı açıklanır. Makalesinde SQL veri ambarı 'nın var olan bir örneğine sahip olduğunuz varsayılmaktadır.

## <a name="set-up-a-connection"></a>Bağlantı kurma

1. SQL Data Warehouse 'a bağlanmak için kullandığınız tam sunucu adını ve veritabanı adını bulun.
    
    Bu bilgileri bulmak için yardıma ihtiyacınız varsa bkz. [Azure SQL veri ambarı 'Na bağlanma](sql-data-warehouse-connect-overview.md).

2. Kurulum sihirbazında, veritabanınıza doğrudan veya bir SSH tüneli kullanarak bağlanıp bağlanmayacağını seçin.

   Doğrudan veritabanınıza bağlanmayı seçerseniz, erişime izin vermek için bir güvenlik duvarı kuralı oluşturmanız gerekir. Bu yöntem, en basit ve en güvenli yöntemdir.

   Bir SSH tüneli kullanarak bağlanmayı seçerseniz, Fivetran, ağınızdaki ayrı bir sunucuya bağlanır. Sunucu, veritabanınıza bir SSH tüneli sağlar. Veritabanınız bir sanal ağ üzerinde erişilemeyen bir alt ağdaysa bu yöntemi kullanmanız gerekir.

3. Fivetran 'dan SQL veri ambarı örneğinize gelen bağlantılara izin vermek için **52.0.2.4** IP adresini sunucu düzeyi güvenlik duvarınıza ekleyin.

   Daha fazla bilgi için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Kullanıcı kimlik bilgilerini ayarlama

1. SQL Server Management Studio veya tercih ettiğiniz aracı kullanarak Azure SQL veri ambarınıza bağlanın. Sunucu Yöneticisi kullanıcısı olarak oturum açın. Ardından, aşağıdaki SQL komutlarını çalıştırarak Fivetran için bir kullanıcı oluşturun:
    - Ana veritabanında: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - SQL veri ambarı veritabanında:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Fivetran kullanıcısına ambarınıza aşağıdaki izinleri verin:

    ```
    GRANT CONTROL to fivetran;
    ```

    Kullanıcı, PolyBase kullanarak Azure Blob depolamadan dosya yüklediğinde kullanılan veritabanı kapsamlı kimlik bilgileri oluşturmak için DENETIM izni gereklidir.

3. Fivetran kullanıcısına uygun bir kaynak sınıfı ekleyin. Kullandığınız kaynak sınıfı, bir columnstore dizini oluşturmak için gereken belleğe bağlıdır. Örneğin, Marketo ve Salesforce gibi ürünlerle tümleştirmeler, çok sayıda sütun ve ürünlerin kullanacağı daha büyük veri hacmi nedeniyle daha yüksek bir kaynak sınıfı gerektirir. Daha yüksek bir kaynak sınıfı, columnstore dizinleri oluşturmak için daha fazla bellek gerektirir.

    Statik kaynak sınıfları kullanmanızı öneririz. `staticrc20` kaynak sınıfıyla başlayabilirsiniz. `staticrc20` kaynak sınıfı, kullandığınız performans düzeyinden bağımsız olarak her kullanıcı için 200 MB ayırır. Columnstore dizin oluşturma ilk kaynak sınıfı düzeyinde başarısız olursa, kaynak sınıfını arttırın.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Daha fazla bilgi için [bellek ve eşzamanlılık limitleri] bellek-eşzamanlılık-limits.md) ve [kaynak sınıfları](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)hakkında bilgi edinin.


## <a name="sign-in-to-fivetran"></a>Fivetran 'da oturum açın

Fivetran 'da oturum açmak için SQL veri ambarı 'na erişmek üzere kullandığınız kimlik bilgilerini girin: 

* Ana bilgisayar (sunucunuzun adı).
* Bağ.
* Veritabanı.
* Kullanıcı (Kullanıcı adı **fivetran\@_sunucu_adı_**  olmalıdır; burada *SUNUCU_ADı* , Azure ana bilgisayar URI 'nizin bir parçasıdır: ***sunucu_adı *. Database. Windows. net**).
* Parolayı.
