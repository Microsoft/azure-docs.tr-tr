---
title: Bı Analytics araçlarını kullanarak Azure Cosmos DB bağlanma
description: Azure Cosmos DB ODBC sürücüsünü kullanarak tablo ve görünüm oluşturma hakkında bilgi edinin ve bu sayede, normalleştirilmiş verilerin bı ve Data Analytics yazılımında görüntülenmesini sağlayabilirsiniz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e8a982a100655934d4ae3ecd64564cf2da82dbbc
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035548"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>ODBC sürücüsü ile bı Analytics araçlarını kullanarak Azure Cosmos DB bağlanma

Azure Cosmos DB ODBC sürücüsü SQL Server Integration Services, Power BI Desktop ve Tableau gibi bı Analytics araçlarını kullanarak Azure Cosmos DB bağlanmanızı ve bu çözümlerde Azure Cosmos DB verilerinizin görselleştirmelerini çözümleyebilmeniz ve oluşturmanıza olanak sağlar.

Azure Cosmos DB ODBC sürücüsü ODBC 3.8 ile uyumludur ve ANSI SQL-92 söz dizimini destekler. Sürücü, verileri Azure Cosmos DB'de yeniden normalleştirmenize yardımcı olan zengin özellikler sunar. Sürücüyü kullanarak verilerinizi Azure Cosmos DB'de tablo ve görünüm olarak gösterebilirsiniz. Sürücü sorgulara, eklemelere, güncelleştirmelere ve silmelere göre gruplama dahil olmak üzere tablo ve görünümlerde SQL işlemleri gerçekleştirmenizi sağlar.

> [!NOTE]
> ODBC sürücüsüyle Azure Cosmos DB bağlanma Şu anda yalnızca Azure Cosmos DB SQL API hesapları için desteklenmektedir.

## <a name="why-do-i-need-to-normalize-my-data"></a>Neden verilerimi normalleştirmem gerekir?
Azure Cosmos DB, hızlı uygulama geliştirmeyi ve veri modellerini katı bir şemaya göre sınırlandırmadan yineleme özelliğini sağlayan, Şemasız bir veritabanıdır. Tek bir Azure Cosmos veritabanı, çeşitli yapıların JSON belgelerini içerebilir. Bu, hızlı uygulama geliştirme için idealdir, ancak veri analizi ve BI araçları kullanarak verilerinizin raporlarını çözümlemek ve oluşturmak istediğinizde, verilerin genellikle düzleştirilmesi ve belirli bir şemaya bağlı olması gerekir.

Bu, ODBC sürücüsünün geldiği yerdir. ODBC sürücüsünü kullanarak, Azure Cosmos DB verileri veri analizlerinizi ve raporlama ihtiyaçlarınıza uygun tablolara ve görünümlere yeniden dağıtabilirsiniz. Yeniden kullanılan şemaların temel alınan veriler üzerinde hiçbir etkisi yoktur ve geliştiricilerin bunları bunlara bağlı olarak sınırlamanıza izin vermez. Bunun yerine, verilere erişmek için ODBC uyumlu araçlardan yararlanmanızı sağlar. Bu nedenle, artık Azure Cosmos veritabanınız yalnızca geliştirme ekibiniz için sık kullanılanlara sahip olmayacaktır, ancak veri analistlerinizi de çok seveceksiniz.

Şimdi ODBC sürücüsünü kullanmaya başlayın.

## <a id="install"></a>1. Adım: Azure Cosmos DB ODBC sürücüsünü yükler

1. Ortamınız için sürücüleri indirin:

    | Yükleyicinin | Desteklenen işletim sistemleri| 
    |---|---| 
    |64 bit Windows için [MICROSOFT Azure Cosmos DB ODBC 64-bit. msi](https://aka.ms/cosmos-odbc-64x64)| Windows 8.1 veya üzeri, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 ve Windows Server 2008 R2 'nin 64 bitlik sürümleri.| 
    |64-bit Windows üzerinde 32-bit için [ODBC 32x64-bit. msi Microsoft Azure Cosmos DB](https://aka.ms/cosmos-odbc-32x64)| Windows 8.1 veya üzeri, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ve Windows Server 2003 64 bit sürümleri.| 
    |32 bit Windows için [MICROSOFT Azure Cosmos DB ODBC 32-bit. msi](https://aka.ms/cosmos-odbc-32x32)|Windows 8.1 veya üzeri, Windows 8, Windows 7, Windows XP ve Windows Vista 'nın 32 bitlik sürümleri.|

    MSI dosyasını yerel olarak çalıştırın, bu, **MICROSOFT Azure Cosmos DB ODBC Sürücü Yükleme Sihirbazı**'nı başlatır. 

1. ODBC sürücüsünü yüklemek için varsayılan girişi kullanarak yükleme Sihirbazı 'nı doldurun.

1. Bilgisayarınızda **ODBC veri kaynağı Yöneticisi** uygulamasını açın. Windows Arama kutusuna **ODBC veri kaynakları** yazarak bunu yapabilirsiniz. 
    **Sürücüler** sekmesine TıKLAYıP **Microsoft Azure Cosmos DB ODBC sürücüsünün** listelendiğinden emin olmak için sürücünün yüklendiğini doğrulayabilirsiniz.

    ![ODBC veri kaynağı Yöneticisi Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>2. Adım: Azure Cosmos veritabanınıza bağlanma

1. [Azure Cosmos DB ODBC sürücüsünü](#install)yükledikten sonra, **ODBC veri kaynağı Yöneticisi** penceresinde **Ekle**' ye tıklayın. Bir kullanıcı veya sistem DSN 'SI oluşturabilirsiniz. Bu örnekte, bir Kullanıcı DSN 'SI oluşturuyorsunuz.

1. **Yeni veri kaynağı oluştur** penceresinde, **Microsoft Azure Cosmos DB ODBC sürücüsü**' nü seçin ve ardından **son**' a tıklayın.

1. **Azure Cosmos DB ODBC sürücüsü SDN kurulumu** penceresinde, aşağıdaki bilgileri girin: 

    ![Azure Cosmos DB ODBC sürücüsü DSN kurulum penceresi](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Veri kaynağı adı**: ODBC DSN için kendi kolay adınız. Bu ad Azure Cosmos DB hesabınızda benzersizdir, bu nedenle birden çok hesabınız varsa bunu uygun şekilde adlandırın.
    - **Açıklama**: veri kaynağının kısa bir açıklaması.
    - **Ana bilgisayar**: Azure Cosmos DB hesabınız için URI. Bunu, aşağıdaki ekran görüntüsünde gösterildiği gibi Azure portal Azure Cosmos DB Keys sayfasından alabilirsiniz. 
    - **Erişim anahtarı**: aşağıdaki ekran görüntüsünde gösterildiği gibi Azure Portal Azure Cosmos DB anahtarları sayfasından birincil veya ikincil, salt okuma veya salt yazılır anahtar. DSN salt okuma veri işleme ve raporlama için kullanılıyorsa, salt okunurdur anahtarını kullanmanızı öneririz.
    ![Azure Cosmos DB Keys sayfası @ no__t-1
    - **Için erişim anahtarını şifreleyin**: Bu makinenin kullanıcılarına göre en iyi seçimi seçin. 
    
1. Azure Cosmos DB hesabınıza bağlanabildiğinizden emin olmak için **Test** düğmesine tıklayın. 

1.  **Gelişmiş Seçenekler** ' e tıklayın ve aşağıdaki değerleri ayarlayın:
    *  **REST API sürüm**: işlemlerinizin [REST API sürümünü](https://docs.microsoft.com/rest/api/cosmos-db/) seçin. Varsayılan 2015-12-16. [Büyük bölüm anahtarları](large-partition-keys.md) olan kapsayıcınız varsa ve REST API sürüm 2018-12-31 ' yı gerekliyse:
        - REST API sürümü için **2018-12-31** yazın
        - **Kayıt defteri Düzenleyicisi** uygulamasını bulmak ve açmak için **Başlat** menüsünde "regedit" yazın.
        - Kayıt Defteri Düzenleyicisi 'nde Şu yola gidin: **Computer\hkey_local_machıne\software\odbc\odbc. INI**
        - DSN 'niz ile aynı ada sahip yeni bir alt anahtar oluşturun, örn. "contoso hesabı ODBC DSN".
        - "Contoso hesabı ODBC DSN" alt anahtarına gidin.
        - Yeni bir **dize** değeri eklemek için sağ tıklayın:
            - Değer adı: **ıgnoresessiontoken**
            - Değer verisi: **1**
             @ No__t-2Kayıt Defteri Düzenleyicisi ayarları @ no__t-3
    - **Sorgu tutarlılığı**: işlemlerinizin [tutarlılık düzeyini](consistency-levels.md) seçin. Varsayılan değer oturumdur.
    - **Yeniden deneme sayısı**: hizmet hızı sınırlaması nedeniyle ilk istek tamamlanmazsa bir işlemin yeniden denenme sayısını girin.
    - **Şema dosyası**: burada birkaç seçeneğiniz vardır.
        - Varsayılan olarak, bu girişi olduğu gibi bırakmak (boş), sürücü her kapsayıcının şemasını belirlemede tüm kapsayıcılar için verilerin ilk sayfasını tarar. Bu, kapsayıcı eşlemesi olarak bilinir. Şema dosyası tanımlanmadığında, sürücünün her sürücü oturumu için tarama yapması gerekir ve DSN kullanan bir uygulamanın daha yüksek bir başlatma süresine neden olabilir. Bir DSN için her zaman bir şema dosyası ilişkilendirmenizi öneririz.
        - Zaten bir şema dosyanız varsa (Belki de şema düzenleyicisini kullanarak oluşturduğunuz), git ' e tıklayabilir, dosyanıza gidebilir, **Kaydet**' e ve ardından **Tamam**' **a tıklayabilirsiniz.**
        - Yeni bir şema oluşturmak istiyorsanız **Tamam**' a ve ardından Ana penceredeki **şema Düzenleyicisi** ' ne tıklayın. Ardından şema Düzenleyicisi bilgilerine ilerleyin. Yeni şema dosyasını oluşturduktan sonra, yeni oluşturulan şema dosyasını eklemek için **Gelişmiş Seçenekler** penceresine geri dönüp unutmayın.

1. **Azure Cosmos DB ODBC sürücüsü DSN kurulum** penceresini tamamlayıp kapattıktan sonra, yenı Kullanıcı DSN 'SI Kullanıcı DSN sekmesine eklenir.

    ![Kullanıcı DSN sekmesinde yeni Azure Cosmos DB ODBC DSN 'SI](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>3. Adım: kapsayıcı eşleme yöntemini kullanarak bir şema tanımı oluşturma

Kullanabileceğiniz iki tür örnekleme yöntemi vardır: **kapsayıcı eşleme** veya **tablo sınırlayıcıları**. Örnekleme oturumu her iki örnekleme yönteminden yararlanabilir, ancak her kapsayıcı yalnızca belirli bir örnekleme yöntemi kullanabilir. Aşağıdaki adımlar, kapsayıcı eşleme yöntemi kullanılarak bir veya daha fazla kapsayıcıdaki veriler için bir şema oluşturur. Bu örnekleme yöntemi, verilerin yapısını belirleyebilmek için bir kapsayıcının sayfasındaki verileri alır. ODBC tarafındaki bir tabloya kapsayıcı atar. Bu örnekleme yöntemi, bir kapsayıcıdaki veriler hogenou olduğunda etkilidir ve hızlıdır. Kapsayıcıda heterojen türünde bir kapsayıcı varsa, kapsayıcıdaki veri yapılarını belirlemede daha sağlam bir örnekleme yöntemi sağladığından [tablo sınırlayıcıları eşleme yöntemini](#table-mapping) kullanmanızı öneririz. 

1. [Azure Cosmos veritabanınıza bağlanma](#connect)bölümündeki 1-4 adımlarını tamamladıktan sonra, **Azure Cosmos DB ODBC sürücüsü DSN kurulum** penceresinde **şema Düzenleyicisi** ' ne tıklayın.

    ![Azure Cosmos DB ODBC sürücüsü DSN kurulum penceresinde şema Düzenleyicisi düğmesi](./media/odbc-driver/odbc-driver-schema-editor.png)
1. **Şema Düzenleyicisi** penceresinde **Yeni oluştur**' a tıklayın.
    **Şema oluştur** penceresi Azure Cosmos DB hesabındaki tüm kapsayıcıları görüntüler. 

1. Örnek olarak bir veya daha fazla kapsayıcı seçin ve ardından **örnek**' e tıklayın. 

1. **Tasarım görünümü** sekmesinde veritabanı, şema ve tablo gösterilir. Tablo görünümünde tarama, sütun adlarıyla ilişkili özellikler kümesini (SQL adı, kaynak adı, vb.) görüntüler.
    Her sütun için, SQL adı, SQL türü, SQL uzunluğu (varsa), ölçek (varsa), duyarlık (varsa) ve null yapılabilir sütununu değiştirebilirsiniz.
    - Sütunu sorgu sonuçlarından dışlamak istiyorsanız, **sütunu** **true** olarak ayarlayabilirsiniz. Sütunu Gizle = TRUE olarak işaretlenmiş sütunlar, hala şemanın bir parçası olsalar da seçim ve projeksiyon için döndürülmez. Örneğin, "_" ile başlayan tüm Azure Cosmos DB sistem gereken özelliklerini gizleyebilirsiniz.
    - **Kimlik** sütunu, Normalleştirilmemiş şemada birincil anahtar olarak kullanıldığı için gizlenemediği tek alandır. 

1. Şemayı tanımlamayı tamamladıktan sonra **dosya** | **Kaydet**' e tıklayın, şemayı kaydetmek Için dizine gidin ve **Kaydet**' e tıklayın.

1. Bu şemayı bir DSN ile kullanmak için, **Azure Cosmos DB ODBC sürücü DSN kurulum penceresini** açın (ODBC veri kaynağı Yöneticisi aracılığıyla), **Gelişmiş Seçenekler**' e tıklayın ve ardından **şema dosyası** kutusunda, kaydedilen şemaya gidin. Bir şema dosyasını mevcut bir DSN 'ye kaydetmek, şemaya göre tanımlanan veri ve yapıya sahip DSN bağlantısını değiştirir.

## <a id="table-mapping"></a>4. Adım: tablo sınırlayıcıları eşleme yöntemini kullanarak bir şema tanımı oluşturma

Kullanabileceğiniz iki tür örnekleme yöntemi vardır: **kapsayıcı eşleme** veya **tablo sınırlayıcıları**. Örnekleme oturumu her iki örnekleme yönteminden yararlanabilir, ancak her kapsayıcı yalnızca belirli bir örnekleme yöntemi kullanabilir. 

Aşağıdaki adımlarda **tablo sınırlayıcıları** eşleme yöntemi kullanılarak bir veya daha fazla kapsayıcıda bulunan veriler için bir şema oluşturulur. Bu örnekleme yöntemini, kapsayıcılarınız heterojen veri türü içerdiğinde kullanmanızı öneririz. Bu yöntemi, örneklemeyi bir öznitelikler kümesi ve buna karşılık gelen değerlerle sınırlamak için kullanabilirsiniz. Örneğin, bir belge "tür" özelliği içeriyorsa, örneklemeyi bu özelliğin değerleriyle kapsamını belirleyebilirsiniz. Örneklemenin nihai sonucu, belirttiğiniz türe ilişkin her bir değer için bir tablo kümesi olacaktır. Örneğin, Type = düzlemi bir düzlem tablosu üretirken Type = otomobil bir otomobil tablosu oluşturacak.

1. [Azure Cosmos veritabanınıza bağlanma](#connect)bölümündeki 1-4 adımlarını tamamladıktan sonra, Azure Cosmos DB ODBC sürücüsü DSN kurulum penceresinde **şema Düzenleyicisi** ' ne tıklayın.

1. **Şema Düzenleyicisi** penceresinde **Yeni oluştur**' a tıklayın.
    **Şema oluştur** penceresi Azure Cosmos DB hesabındaki tüm kapsayıcıları görüntüler. 

1. **Örnek görünüm** sekmesinde bir kapsayıcı seçin, kapsayıcının **eşleme tanımı** sütununda, **Düzenle**' ye tıklayın. Ardından, **eşleme tanımı** penceresinde **tablo sınırlayıcıları** yöntemi ' ni seçin. Ardından şunları yapın:

    a. **Öznitelikler** kutusuna bir sınırlayıcı özelliğinin adını yazın. Bu, örneklemeyi, örneğin şehir için kapsamını atamak istediğiniz bir özelliktir ve ENTER tuşuna basın. 

    b. Örneklemeyi yukarıda girdiğiniz özniteliğe ilişkin belirli değerlere bağlamak istiyorsanız, seçim kutusunda özniteliği seçin, **değer** kutusuna bir değer girin (örn. Seattle) ve ENTER tuşuna basın. Öznitelikler için birden çok değer eklemeye devam edebilirsiniz. Yalnızca değer girerken doğru özniteliğin seçildiğinden emin olun.

    Örneğin, şehir olarak bir **öznitelik** değeri eklerseniz ve tablonuzu yalnızca New York ve Dubai City değeri olan satırları içerecek şekilde sınırlandırmak Istiyorsanız, Attributes kutusuna City ve New York ' a ve ardından **değerler** kutusuna Dubai yazın.

1. **Tamam**’a tıklayın. 

1. Örneklemek istediğiniz kapsayıcıların eşleme tanımlarını tamamladıktan sonra, **şema Düzenleyicisi** penceresinde **örnek**' e tıklayın.
     Her sütun için, SQL adı, SQL türü, SQL uzunluğu (varsa), ölçek (varsa), duyarlık (varsa) ve null yapılabilir sütununu değiştirebilirsiniz.
    - Sütunu sorgu sonuçlarından dışlamak istiyorsanız, **sütunu** **true** olarak ayarlayabilirsiniz. Sütunu Gizle = TRUE olarak işaretlenmiş sütunlar, hala şemanın bir parçası olsalar da seçim ve projeksiyon için döndürülmez. Örneğin, `_` ' dan başlayarak tüm Azure Cosmos DB sistem gereken özelliklerini gizleyebilirsiniz.
    - **Kimlik** sütunu, Normalleştirilmemiş şemada birincil anahtar olarak kullanıldığı için gizlenemediği tek alandır. 

1. Şemayı tanımlamayı tamamladıktan sonra **dosya** | **Kaydet**' e tıklayın, şemayı kaydetmek Için dizine gidin ve **Kaydet**' e tıklayın.

1. **Azure Cosmos DB ODBC sürücüsü DSN kurulum** penceresinde geri dönüp **Gelişmiş Seçenekler**' e tıklayın. Ardından, **şema dosyası** kutusunda, kaydedilmiş şema dosyasına gidin ve **Tamam**' a tıklayın. DSN 'yi kaydetmek için yeniden **Tamam** ' a tıklayın. Bu, oluşturduğunuz şemayı DSN 'ye kaydeder. 

## <a name="optional-set-up-linked-server-connection"></a>Seçim Bağlı sunucu bağlantısını ayarlama

Bağlı sunucu bağlantısı ayarlayarak, SQL Server Management Studio (SSMS) Azure Cosmos DB sorgulayabilirsiniz.

1. [Adım 2](#connect)' de açıklandığı gibi, örneğin `SDS Name` adlı bir sistem veri kaynağı oluşturun.

1. [SQL Server Management Studio yükleyip](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) sunucuya bağlanın. 

1. SSMS sorgu Düzenleyicisi 'nde, aşağıdaki komutlarla veri kaynağı için `DEMOCOSMOS` bağlantılı sunucu nesnesi oluşturun. @No__t-0 ' ı bağlı sunucunuzun adı ile ve `SDS Name` ' i, sistem veri kaynağınızın adıyla değiştirin.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Yeni bağlı sunucu adını görmek için bağlı sunucular listesini yenileyin.

![SSMS 'de bağlı sunucu](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Bağlantılı veritabanını sorgula

Bağlantılı veritabanını sorgulamak için bir SSMS sorgusu girin. Bu örnekte sorgu, `customers` adlı kapsayıcıdaki tablodan seçim yapar:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Sorguyu yürütün. Sonuç şuna benzer olmalıdır:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Bağlı Cosmos DB sunucusu dört bölümlü adlandırmayı desteklemiyor. Aşağıdaki iletiye benzer bir hata döndürülür:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>Seçim Görünümler oluşturma
Örnekleme sürecinin bir parçası olarak görünümleri tanımlayabilir ve oluşturabilirsiniz. Bu görünümler, SQL görünümleriyle eşdeğerdir. Bunlar salt okunurdur ve tanımlanan Azure Cosmos DB SQL sorgusunun seçim ve projeksiyonlarını kapsamdadır. 

Verilerinize yönelik bir görünüm oluşturmak için **şema Düzenleyicisi** penceresinde, **tanımları görüntüle** sütununda, kapsayıcının örneğine örnek olarak **Ekle** ' ye tıklayın. 
    ![Veri görünümünü oluşturma @ no__t-1


Ardından **tanımları görüntüle** penceresinde şunları yapın:

1. **Yeni**' ye tıklayın, görünüm için bir ad girin, örneğin, EmployeesfromSeattleView ve ardından **Tamam**' a tıklayın.

1. **Görünüm düzenleme** penceresinde bir Azure Cosmos DB sorgusu girin. Bu [Azure Cosmos DB BIR SQL sorgusu](how-to-sql-query.md)olmalıdır, örneğin `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` ve ardından **Tamam**' a tıklayın.

    ![Görünüm oluştururken sorgu Ekle](./media/odbc-driver/odbc-driver-create-view-2.png)


Dilediğiniz kadar çok görünüm oluşturabilirsiniz. Görünümleri tanımlamayı tamamladıktan sonra verileri örnekleyebilirsiniz. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5\. Adım: Power BI Desktop gibi bı araçlarında verilerinizi görüntüleme

Tüm ODBC uyumlu araçlarla Azure Cosmos DB bağlanmak için yeni DSN 'nizi kullanabilirsiniz-Bu adım, Power BI Desktop nasıl bağlanılacağını ve bir Power BI görselleştirmesi oluşturulacağını gösterir.

1. Power BI Desktop’ı açın.

1. **Veri al**' a tıklayın.

    ![Power BI Desktop veri al](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. **Veri al** penceresinde **diğer** | **ODBC** | **Connect**' e tıklayın.

    ![Veri Al Power BI ODBC veri kaynağını seçin](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. **ODBC** penceresinde, oluşturduğunuz veri kaynağı adını seçin ve ardından **Tamam**' a tıklayın. **Gelişmiş Seçenekler** girdilerini boş bırakabilirsiniz.

    ![Verileri al Power BI veri kaynağı adı (DSN) seçin](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. **BIR ODBC sürücüsü kullanarak bir veri kaynağına erişin** penceresinde, **varsayılan veya özel** ' i seçin ve ardından **Bağlan**' a tıklayın. **Kimlik bilgisi bağlantı dizesi özelliklerini**eklemeniz gerekmez.

1. **Gezgin** penceresinde, sol bölmede veritabanını, şemayı genişletin ve ardından tabloyu seçin. Sonuçlar bölmesi, oluşturduğunuz şemayı kullanarak verileri içerir.

    ![Veri Al Power BI tablo seçin](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Power BI masaüstündeki verileri görselleştirmek için tablo adının önündeki kutuyu işaretleyin ve ardından **Yükle**' ye tıklayın.

1. Power BI Desktop ' de, en solda, veri sekmesini seçin. ![Power BI Desktop veri sekmesi](./media/odbc-driver/odbc-driver-data-tab.png) verilerin içeri aktarıldığını onaylamak için.

1. Artık, Power BI Desktop @ no__t-1 ' deki rapor sekmesi ![Report sekmesine tıklayıp **yeni görsel**' e tıklayıp Kutucuğunuzu özelleştirerek Power BI kullanarak görseller oluşturabilirsiniz. Power BI Desktop görselleştirmeler oluşturma hakkında daha fazla bilgi için bkz. [Power BI Içindeki görselleştirme türleri](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Sorun giderme

Aşağıdaki hatayı alırsanız [Adım 2](#connect) ' de Azure Portal kopyaladığınız **ana bilgisayar** ve **erişim anahtarı** değerlerinin doğru olduğundan emin olun ve yeniden deneyin. Değer hatasını serbest bir şekilde kopyalamak için **konağın** sağ tarafındaki Kopyala düğmelerini ve Azure Portal **erişim anahtarı** değerlerini kullanın.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB'ye hoş geldiniz](introduction.md).
