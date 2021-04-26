---
title: "Öğretici: SQLite veritabanınızı Azure SQL veritabanı sunucusuz 'a geçirme"
description: Azure Data Factory kullanarak SQLite 'tan Azure SQL veritabanı sunucusuz 'e çevrimdışı geçiş gerçekleştirmeyi öğrenin.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790415"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>SQLite veritabanınızı Azure SQL veritabanı sunucusuz 'a geçirme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Birçok kişi için, SQLite, veritabanlarının ve SQL programlamanın ilk deneyimini sağlar. Birçok işletim sistemine dahil değildir ve popüler uygulamalar, dünyanın en yaygın olarak dağıtılan ve kullanılan veritabanı altyapılarından biridir. Büyük olasılıkla çok sayıda kişinin kullandıkları ilk veritabanı altyapısı olduğundan, proje veya uygulamaların merkezi bir parçası olarak genellikle bu durum oluşabilir. Bu tür durumlarda, projenin veya uygulamanın ilk SQLite uygulamasını artmıştır, geliştiricilerin verilerini güvenilir, merkezi bir veri deposuna geçirilmesi gerekebilir.

Azure SQL veritabanı sunucusuz, iş yükü talebine göre işlemi otomatik olarak ölçeklendirilen tek veritabanlarına yönelik bir işlem katmandır ve saniye başına kullanılan işlem miktarına göre faturalandırılır. Ayrıca sunucusuz işlem katmanı etkinlik dışı dönemlerde veritabanını otomatik olarak duraklatır (bu dönemlerde yalnızca depolama faturalanır) ve etkinliğe dönüldüğünde veritabanlarını otomatik olarak sürdürür.

Aşağıdaki adımları izledikten sonra, veritabanınız Azure SQL veritabanı sunucusuz 'e geçirilir, böylece veritabanınızı buluttaki diğer Kullanıcı veya uygulamalar için kullanılabilir hale getirebilirsiniz ve yalnızca kullandığınız kadar ödeyerek en az sayıda uygulama kodu değişir.

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure Aboneliği
- Geçirmek istediğiniz SQLite2 veya SQLite3 veritabanı
- Bir Windows ortamı
  - Yerel bir Windows ortamınız yoksa, geçiş için Azure 'da bir Windows sanal makinesi kullanabilirsiniz. Azure dosyaları ve Depolama Gezgini kullanarak VM 'de SQLite veritabanı dosyanızı taşıyın ve mevcut yapın.

## <a name="steps"></a>Adımlar

1. Sunucusuz işlem katmanında yeni bir Azure SQL veritabanı sağlayın.

    ![Azure SQL veritabanı sunucusuz için sağlama örneğini gösteren Azure portal ekran görüntüsü](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. SQLite veritabanı Dosyanızı Windows ortamınızda kullanılabilir durumda olduğundan emin olun. Henüz bir tane yoksa (örneğin, açık kaynak bölümünde çok sayıda mevcuttur) bir SQLite ODBC sürücüsü yükleyebilirsiniz http://www.ch-werner.de/sqliteodbc/) .

3. Veritabanı için bir sistem DSN 'SI oluşturun. Sistem mimarinizle (32-bit vs 64 bit) eşleşen veri kaynağı yönetici uygulamasını kullandığınızdan emin olun. Sistem ayarlarınızda çalıştırdığınız sürümü bulabilirsiniz.

    - Ortamınızdaki ODBC veri kaynağı Yöneticisi 'ni açın.
    - Sistem DSN sekmesine tıklayın ve "Ekle" ye tıklayın
    - Yüklediğiniz SQLite ODBC bağlayıcısını seçin ve bağlantıya anlamlı bir ad verin, örneğin sqlitemigrationsource
    - Veritabanı adını. db dosyası olarak ayarla
    - Kaydet ve çık

4. Şirket içinde barındırılan tümleştirme çalışma zamanını indirin ve yükleyin. Bunu yapmanın en kolay yolu, belgelerde açıklandığı şekilde hızlı Install seçeneğidir. El ile yüklemeyi tercih ediyorsanız, uygulamayı Data Factory örneğiniz tarafından kullanılabilecek bir kimlik doğrulama anahtarı ile sağlamanız gerekir:

    - ADF (Azure portal hizmetten yazar ve Izleyici) başlatılıyor
    - Soldaki "yazar" sekmesine (mavi kurşun kalem) tıklayın
    - Bağlantılar ' a (alt sol), sonra tümleştirme çalışma zamanları
    - Yeni Self-Hosted Integration Runtime ekleyin, bir ad verin, *seçenek 2*' yi seçin.

5. Data Factory kaynak SQLite veritabanı için yeni bir bağlı hizmet oluşturun.

    ![Azure Data Factory 'de boş bağlı hizmetler dikey penceresini gösteren ekran görüntüsü](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. **Bağlantılar**' ın altında, **bağlantılı hizmet** altında **Yeni**' ye tıklayın.

7. "ODBC" bağlayıcısını arayıp seçin

   ![Azure Data Factory bağlı hizmetler dikey penceresinde ODBC bağlayıcı logosunu gösteren ekran görüntüsü](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Bağlı hizmete anlamlı bir ad verin, örneğin "sqlite_odbc". "Tümleştirme çalışma zamanı aracılığıyla Bağlan" açılan menüsünde Tümleştirme çalışma zamanı ' nı seçin. Ilk katalog değişkenini. db dosyası için FilePath ve DSN 'yi Sistem DSN bağlantısı adı ile değiştirerek bağlantı dizesine aşağıda girin:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Kimlik doğrulama türünü anonim olarak ayarla

10. Bağlantıyı test etme

    ![Azure Data Factory başarılı bağlantıyı gösteren ekran görüntüsü](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Sunucusuz SQL hedefi için başka bir bağlı hizmet oluşturun. Bağlı hizmet Sihirbazı 'nı kullanarak veritabanını seçin ve SQL kimlik doğrulama kimlik bilgilerini sağlayın.

    ![Azure Data Factory ' de seçili Azure SQL veritabanı 'nı gösteren ekran görüntüsü](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. CREATE TABLE deyimlerini SQLite veritabanından ayıklayın. Bunu, Veritabanı dosyanızda aşağıdaki Python betiğini yürüterek yapabilirsiniz.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. CREATE TABLE deyimlerini CreateTables. SQL dosyasından kopyalayarak ve Azure portal sorgu düzenleyicisinde SQL deyimlerini çalıştırarak, serverless SQL hedef ortamınızda giriş tabloları oluşturun.

14. Data Factory giriş ekranına dönün ve "Veri Kopyalama" ' a tıklayarak iş oluşturma Sihirbazı 'nda çalıştırın.

    ![Azure Data Factory Veri Kopyalama Sihirbazı logosunu gösteren ekran görüntüsü](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Onay kutularını kullanarak kaynak SQLite veritabanından tüm tablolar ' ı seçin ve bunları Azure SQL 'de hedef tablolarla eşleyin. İş çalıştırıldıktan sonra, verilerinizi SQLite 'tan Azure SQL 'e başarıyla geçirdiniz!

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [hızlı başlangıç: Azure SQL veritabanı 'nda Azure Portal kullanarak tek bir veritabanı oluşturma](single-database-create-quickstart.md).
- Kaynak sınırları için bkz. [sunucusuz işlem katmanı kaynak sınırları](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).