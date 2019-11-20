---
title: 'Hızlı başlangıç: veri ambarı oluşturma ve sorgulama-Azure portal'
description: Azure portal Azure SQL veri ambarı ile bir veri ambarı oluşturun ve sorgulayın.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9a68e9d71324ba05e24ba98802863211a569689
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839933"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal Azure SQL veri ambarı oluşturma ve sorgulama

Azure portal kullanarak bir Azure SQL veri ambarını hızlıca oluşturun ve sorgulayın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!NOTE]
> Bir SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir. Ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="before-you-begin"></a>Başlamadan önce

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)’nun (SSMS) en yeni sürümünü indirin ve yükleyin.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-data-warehouse"></a>Veri ambarı oluşturma

Azure SQL veri ambarı, tanımlı bir [işlem kaynakları](memory-concurrency-limits.md)kümesiyle oluşturulur. Veritabanı bir [Azure kaynak grubu](../azure-resource-manager/resource-group-overview.md) ve bir [Azure SQL mantıksal sunucusu](../sql-database/sql-database-logical-servers.md) içinde oluşturulur. 

AdventureWorksDW örnek verilerini içeren bir SQL veri ambarı oluşturmak için aşağıdaki adımları izleyin. 

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.

2. **Yeni** sayfasından **Veritabanları**’nı seçin ve **Yeni** sayfasında **Öne Çıkan** altından **SQL Veri Ambarı**’nı seçin.

    ![boş veri ambarı oluşturma](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. SQL Veri Ambarı formunu aşağıdaki bilgilerle doldurun:

    | Ayar | Önerilen değer | Açıklama |
    | :------ | :-------------- | :---------- |
    | **Veritabanı adı** | mySampleDataWarehouse | Geçerli veritabanı adları için bkz. [Veritabanı Tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). Veri ambarının bir veritabanı türü olduğuna dikkat edin.|
    | **Abonelik** | Aboneliğiniz | Abonelikleriniz hakkında daha ayrıntılı bilgi için bkz. [Abonelikler](https://account.windowsazure.com/Subscriptions). |
    | **Kaynak grubu** | myResourceGroup | Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Kaynak seçme** | Örnek | Örnek bir veritabanı yüklemek için belirtir. Veri ambarının bir veritabanı türü olduğuna dikkat edin. |
    | **Örnek seçme** | AdventureWorksDW | AdventureWorksDW örnek veritabanını yüklemeyi belirtir. |
    ||||

    ![veri ambarı oluşturma](media/create-data-warehouse-portal/select-sample.png)

4. Yeni veritabanınız için yeni bir sunucu oluşturup yapılandırmak üzere **Sunucu**’ya tıklayın. **Yeni sunucu formu**’nu aşağıdaki bilgilerle doldurun: 

    | Ayar | Önerilen değer | Açıklama |
    | :------ | :-------------- | :---------- |
    | **Sunucu adı** | Genel olarak benzersiz bir ad | Geçerli sunucu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Sunucu yöneticisi oturum açma bilgileri** | Geçerli bir ad | Geçerli oturum açma adları için bkz. [Veritabanı Tanımlayıcıları](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
    | **Parola** | Geçerli bir parola | Parolanızda en az 8 karakter bulunmalı ve parolanız şu üç kategoriden karakterler içermelidir: büyük harf karakterler, küçük harf karakterler, sayılar ve alfasayısal olmayan karakterler. |
    | **Konum** | Geçerli bir konum | Bölgeler hakkında bilgi için bkz. [Azure Bölgeleri](https://azure.microsoft.com/regions/). |
    ||||

    ![veritabanı oluşturma](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. **Seç**'e tıklayın.

6. Veri ambarının performans yapılandırmasını belirtmek için **performans düzeyi** ' ne tıklayın.

7. Bu öğretici için **Gen2**öğesini seçin. Kaydırıcı, varsayılan olarak **DW1000c**olarak ayarlanır. Nasıl çalıştığını görmek için yukarı ve aşağı taşımayı deneyin. 

    ![performansı yapılandırma](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. **Apply (Uygula)** düğmesine tıklayın.

9. SQL veri ambarı formunu tamamladığınıza göre, veritabanını sağlamak için **Oluştur** ' a tıklayın. Sağlama birkaç dakika sürer.

    ![oluştur’a tıklayın](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Araç çubuğunda **Bildirimler**’e tıklayarak dağıtım işlemini izleyin.
    
     ![bildirim](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma

SQL veri ambarı hizmeti, sunucu düzeyinde bir güvenlik duvarı oluşturur. Bu güvenlik duvarı, dış uygulamaların ve araçların sunucuya veya sunucudaki herhangi bir veritabanına bağlanmasını engeller. Bağlantıyı etkinleştirmek için, belirli IP adresleri için bağlantıyı etkinleştiren güvenlik duvarı kuralları ekleyebilirsiniz. İstemcinizin IP adresine yönelik bir [sunucu düzeyi güvenlik duvarı kuralı](../sql-database/sql-database-firewall-configure.md) oluşturmak için bu adımları izleyin.

> [!NOTE]
> SQL Veri Ambarı 1433 numaralı bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda BT departmanınız 1433 numaralı bağlantı noktasını açmadığı sürece Azure SQL Veritabanı sunucunuza bağlanamazsınız.

1. Dağıtım tamamlandıktan sonra, sol taraftaki menüden **tüm hizmetler** ' i seçin. **Veritabanları**' nı seçin, sık KULLANıLANLARıNıZA SQL veri ambarları eklemek için **SQL veri ambarları** ' nın yanındaki yıldızı seçin.
1. Sol taraftaki menüden **SQL veri ambarları** ' nı seçin ve ardından **SQL veri ambarları** sayfasında **mysampledatawarehouse** ' ı tıklatın. Veritabanınızın genel bakış sayfası açılır ve tam sunucu adı (örneğin, **MyNewServer-20180430.Database.Windows.net**) görüntülenerek daha fazla yapılandırma seçeneği sunulur.
1. Bu tam sunucu adını, bu ve diğer hızlı başlangıçtaki sunucunuza ve veritabanlarına bağlanmak için kullanmak üzere kopyalayın. Sunucu ayarlarını açmak için sunucu adına tıklayın.

   ![sunucu adını bulma](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

1. **Güvenlik duvarı ayarlarını göster**’e tıklayın.

   ![sunucu ayarları](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

1. SQL Veritabanı sunucusu için **Güvenlik duvarı ayarları** sayfası açılır.

   ![sunucu güvenlik duvarı kuralı](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

1. Geçerli IP adresinizi yeni bir güvenlik duvarı kuralına eklemek için araç çubuğunda **İstemci IP’si Ekle** öğesine tıklayın. Güvenlik duvarı kuralı, 1433 numaralı bağlantı noktasını tek bir IP adresi veya bir IP adresi aralığı için açabilir.

1. **Kaydet** düğmesine tıklayın. Geçerli IP adresiniz için mantıksal sunucuda 1433 numaralı bağlantı noktası açılarak sunucu düzeyinde güvenlik duvarı kuralı oluşturulur.

1. **Tamam**’a tıklayın ve sonra **Güvenlik duvarı ayarları** sayfasını kapatın.

Şimdi bu IP adresini kullanarak SQL sunucusuna ve veri ambarlarına bağlanabilirsiniz. Bağlantı SQL Server Management Studio’dan veya seçtiğiniz diğer bir araçtan çalışır. Bağlandığınızda, daha önce oluşturduğunuz ServerAdmin hesabını kullanın.

> [!IMPORTANT]
> Varsayılan olarak, SQL Veritabanı güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için etkindir. Tüm Azure hizmetleri için güvenlik duvarını kapatmak üzere bu sayfada **KAPALI**’ya ve ardından **Kaydet**’e tıklayın.

## <a name="get-the-fully-qualified-server-name"></a>Tam sunucu adını alma

SQL sunucunuzun tam sunucu adını Azure portalından alabilirsiniz. Daha sonra sunucuya bağlanırken tam adı kullanırsınız.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Sol taraftaki menüden **SQL veri ambarları** ' nı seçin ve **SQL veri ambarları** sayfasında veri ambarınıza tıklayın.
3. Veritabanınızın Azure portal sayfasındaki **Temel Bilgiler** bölmesinde, **Sunucu adını** bulup kopyalayın. Bu örnekte, tam adı mynewserver-20180430.database.windows.net ' dir.

    ![bağlantı bilgileri](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Sunucu yöneticisi olarak sunucuya bağlanma

Bu bölümde Azure SQL sunucunuzla bağlantı kurmak için [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) kullanılmaktadır.

1. SQL Server Management Studio’yu açın.

2. **Sunucuya Bağlan** iletişim kutusuna şu bilgileri girin:

   | Ayar | Önerilen değer | Açıklama |
   | :------ | :-------------- | :---------- |
   | Sunucu türü | Veritabanı altyapısı | Bu değer gereklidir |
   | Sunucu adı | Tam sunucu adı | Örnek: **MyNewServer-20180430.Database.Windows.net**. |
   | Kimlik Doğrulaması | SQL Server Kimlik Doğrulaması | Bu öğreticide yapılandırılan tek kimlik doğrulaması türü SQL Kimlik Doğrulamasıdır. |
   | Oturum Aç | Sunucu yöneticisi hesabı | Sunucuyu oluştururken belirttiğiniz hesap. |
   | Parola | Sunucu yöneticisi hesabınızın parolası | Sunucuyu oluştururken belirttiğiniz parola. |
   ||||

    ![sunucuya bağlan](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. **Bağlan**'a tıklayın. SSMS’te Nesne Gezgini penceresi açılır. 

4. Nesne Gezgini’nde, **Veritabanları**’nı genişletin. Daha sonra yeni veritabanınızdaki nesneleri görüntülemek için **mySampleDatabase**’i genişletin.

    ![veritabanı nesneleri](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Sorgular çalıştırma

SQL Veri Ambarı sorgu dili olarak T-SQL kullanır. Bir sorgu penceresi açıp T-SQL sorguları çalıştırmak için, aşağıdaki adımları kullanın:

1. **mySampleDataWarehouse**’a sağ tıklayıp **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.
2. Sorgu penceresinde, veritabanlarının listesini görmek için aşağıdaki komutu girin.

    ```sql
    SELECT * FROM sys.databases
    ```

3. **Yürüt**’e tıklayın. Sorgu sonuçları iki veritabanı gösterir: **master** ve **mySampleDataWarehouse**.

    ![Sorgu veritabanları](media/create-data-warehouse-portal/query-databases.png)

4. Verilere bakmak için, Adams soyadına sahip ve üç çocuğu olan müşteri sayısını görmek için aşağıdaki komutu kullanın. Sonuçlarda altı müşteri listelenir. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![dbo.dimCustomer’ı sorgulama](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Veri ambarı birimleri ve veri ambarınızdan depolanan veriler için ücret ödersiniz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır.

- Verileri depoda tutmak istiyorsanız, veri ambarını kullanmadığınız zamanlarda işlemi duraklatabilirsiniz. İşlem duraklatıldığında yalnızca veri depolama alanı için ücret ödersiniz. Verilerle çalışmaya her seferinde işlem yapabilirsiniz.
- Gelecekteki ücretlendirmeleri kaldırmak istiyorsanız, veri ambarını silebilirsiniz.

Artık ihtiyacınız olmayan kaynakları temizlemek için bu adımları izleyin.

1. [Azure portalında](https://portal.azure.com) oturum açıp veri ambarınıza tıklayın.

    ![Kaynakları temizleme](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlemi duraklatmak için, **Duraklat** düğmesine tıklayın. Veri ambarı duraklatıldığında **, bir geri** düğmesi görürsünüz. İşlem işlemine geri dönmek için, geri ' **ye tıklayın.**

3. İşlem veya depolama için ücretlendirilmemek üzere veri ambarını kaldırmak için **Sil**' e tıklayın.

4. Oluşturduğunuz SQL Server 'ı kaldırmak için önceki görüntüde **MyNewServer-20180430.Database.Windows.net** ' a tıklayın ve ardından **Sil**' e tıklayın. Sunucuyu silmek sunucuyla ilişkili tüm veritabanlarını da sileceğinden bu silme işlemini gerçekleştirirken dikkatli olun.

5. Kaynak grubunu kaldırmak için, **myResourceGroup**’a tıklayıp daha sonra **Kaynak grubunu sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir veri ambarı oluşturdunuz, bir güvenlik duvarı kuralı oluşturdunuz, veri ambarınıza bağlanmış ve birkaç sorgu çalıştırdınız. Azure SQL Veri Ambarı hakkında daha fazla bilgi edinmek için, veri yükleme öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Verileri bir SQL veri ambarına yükleme](load-data-from-azure-blob-storage-using-polybase.md)
