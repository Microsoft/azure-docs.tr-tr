---
title: Excel 'i tek bir veritabanına bağlama
description: Microsoft Excel 'i Azure SQL veritabanı 'nda tek bir veritabanına bağlamayı öğrenin. Raporlama ve veri araştırması için Excel'e veri aktarın.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827125"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Excel 'i Azure SQL veritabanı 'nda tek bir veritabanına bağlama ve rapor oluşturma

Excel 'i Azure SQL veritabanı 'nda tek bir veritabanına bağlayın ve verileri içeri aktarın ve veritabanındaki değerlere göre tablolar ve grafikler oluşturun. Bu öğreticide, Excel ile bir veritabanı tablosu arasındaki bağlantıyı ayarlayacak, Excel'e ilişkin verilerin ve bağlantı bilgilerinin depolandığı dosyayı kaydedecek ve ardından veritabanı değerlerini kullanarak bir özet grafik oluşturacaksınız.

Başlamadan önce tek bir veritabanı gerekir. Bir tane yoksa, birkaç dakika içinde örnek veriler içeren tek bir veritabanı almak için [tek bir veritabanı oluşturma](sql-database-single-database-get-started.md) ve [sunucu düzeyinde IP güvenlik duvarı oluşturma](sql-database-server-level-firewall-rule.md) makalesine bakın.

Bu makalede, bu makaleden Excel 'e örnek veri aktarırsınız, ancak kendi verilerinize benzer adımları izleyebilirsiniz.

Ayrıca, bir Excel kopyanızın olması gerekir. Bu makalede [Microsoft Excel 2016](https://products.office.com/) kullanılmıştır.

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Excel 'i bir SQL veritabanına bağlama ve verileri yükleme

1. Excel'i SQL veritabanına bağlamak için Excel'i açarak yeni bir çalışma kitabı oluşturun veya var olan bir Excel çalışma kitabını açın.
2. Sayfanın üstündeki menü çubuğunda, **veri** sekmesini seçin, **veri al**' ı seçin, Azure 'dan seçim YAPıN ve ardından **Azure SQL veritabanı**' nı seçin. 

   ![Veri kaynağı seçin: Excel'i SQL veritabanına bağlayın.](./media/sql-database-connect-excel/excel_data_source.png)

   Veri Bağlantı Sihirbazı açılır.
3. **Veritabanı Sunucusuna Bağlan** iletişim kutusunda, bağlanmak istediğiniz SQL Database **Sunucu adını** şu biçimde girin: <*sunucuadı*> **.database.windows.net**. Örneğin, **msftestserver.Database.Windows.net**. İsteğe bağlı olarak, veritabanınızın adını girin. **Tamam ' ı** seçerek kimlik bilgileri penceresini açın. 

   ![Veritabanı sunucusuna bağlan Iletişim kutusu](media/sql-database-connect-excel/server-name.png)

4. **SQL Server veritabanı** iletişim kutusunda, sol taraftaki **veritabanı** ' nı seçin ve ardından bağlanmak istediğiniz SQL veritabanı sunucusu Için **Kullanıcı adınızı** ve **parolanızı** girin. **Gezginini**açmak için **Bağlan** ' ı seçin. 

   ![Sunucu adını ve oturum açma kimlik bilgilerini girme](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Ağ ortamınıza bağlı olarak, SQL Veritabanı sunucusunun istemci IP adresinizden gelen trafiğe izin vermemesi halinde bağlanamayabilirsiniz veya mevcut bağlantınız kesilebilir. [Azure portalına](https://portal.azure.com/) gidip SQL sunucuları seçeneğine tıklayın, sunucunuza tıklayın ve ardından ayarlar altında bulunan güvenlik duvarı seçeneğine tıklayıp istemci IP adresinizi ekleyin. Ayrıntılı bilgi için bkz. [Güvenlik duvarı ayarlarını yapılandırma](sql-database-configure-firewall-settings.md).

5. **Gezgin**'de, çalışmak istediğiniz veritabanını listeden seçin, birlikte çalışmak istediğiniz tabloları veya görünümleri seçin ( **Vgetallcategories**' i seçtik) ve sonra verileri veritabanından Excel elektronik Tablonuzya taşımak için **Yükle** ' yi seçin.

    ![Bir veritabanı ve tablo seçin.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Excel'e veri aktarma ve özet grafik oluşturma

Artık bağlantıyı oluşturduğunuza göre, verilerin nasıl yükleneceği ile ilgili birkaç farklı seçeneğiniz vardır. Örneğin, aşağıdaki adımlarda SQL veritabanınızda bulunan verilere göre bir Özet Grafik oluşturulur. 

1. Önceki bölümdeki adımları izleyin, ancak bu kez Yükle ' **yi seçmek yerine** **yük açılır listesinden** **Yükle ' yi**seçin.
2. Sonra, bu verileri çalışma kitabınızda nasıl görüntülemek istediğinizi seçin. Biz **PivotChart** seçeneğini belirledik. Ayrıca, **Yeni çalışma sayfası** oluşturmayı veya **Bu verileri Veri Modeline ekle** seçeneğini belirlemeyi de tercih edebilirsiniz. Veri Modelleri hakkında daha fazla bilgi için bkz. [Excel'de veri modeli oluşturma](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Excel'de veri biçimini belirleme](./media/sql-database-connect-excel/import-data.png)

    Artık çalışma sayfasında boş bir özet grafiği ve grafik var.
3. **PivotTable Alanları** altında, görüntülemek istediğiniz alanların onay kutularını işaretleyin.

    ![Veritabanı raporunu yapılandırın.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Diğer Excel çalışma kitaplarını ve çalışma sayfalarını veritabanına bağlamak istiyorsanız, **veri** sekmesini seçin **ve son kaynaklar iletişim kutusunu** başlatmak için **son kaynaklar** ' ı seçin. Buradan, listeden oluşturduğunuz bağlantıyı seçin ve ardından **Aç**' a tıklayın.
> Son kaynakları ![iletişim kutusu](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>. Odc dosyası kullanarak kalıcı bağlantı oluşturma

Bağlantı ayrıntılarını kalıcı olarak kaydetmek için bir. odc dosyası oluşturabilir ve **var olan bağlantılar** iletişim kutusunda bu bağlantıyı seçilebilir bir seçenek haline getirebilirsiniz. 

1. Sayfanın üst kısmındaki menü çubuğunda, **veri** sekmesini seçin ve **var olan bağlantılar iletişim kutusunu** başlatmak için **mevcut bağlantılar** ' ı seçin. 
   1. **Daha fazla araştır** ' ı seçerek **veri kaynağı seç** iletişim kutusunu açın.   
   2. **+ Newsqlserverconnection. odc** dosyasını seçin ve ardından **Aç** ' ı seçerek **veri bağlantısı sihirbazını**açın.

      ![Yeni bağlantı iletişim kutusu](media/sql-database-connect-excel/new-connection.png)

2. **Veri bağlantısı sihirbazında**sunucu ADıNıZı ve SQL veritabanınızın kimlik bilgilerini yazın. **İleri**’yi seçin. 
   1. Açılan listeden verilerinizi içeren veritabanını seçin. 
   2. İlgilendiğiniz tabloyu veya görünümü seçin. VGetAllCategories seçtik.
   3. **İleri**’yi seçin. 

      ![Veri Bağlantısı Sihirbazı](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Veri bağlantısı sihirbazının sonraki ekranında dosyanızın konumunu, **dosya adını**ve **kolay adını** seçin. Ayrıca, verilerinizi dosyaya kaydetmeyi de tercih edebilirsiniz; ancak bu, verilerinizi istenmeyen erişime açabilir. Hazırsanız **son** ' u seçin. 

    ![Veri bağlantısını Kaydet](media/sql-database-connect-excel/save-data-connection.png)

4. Verilerinizi nasıl içeri aktarmak istediğinizi seçin. Bir PivotTable yapmayı seçtik. Ayrıca, **Özellikler**' i seçerek bağlantının özelliklerini de değiştirebilirsiniz. Hazırlanıyor **Tamam ' ı** seçin. Parolayı dosya ile kaydetmeyi seçmediyseniz, kimlik bilgilerinizi girmeniz istenir. 

    ![Verileri İçeri Aktarma](media/sql-database-connect-excel/import-data2.png)

5. **Veri** sekmesini genişleterek ve **var olan bağlantılar**' ı seçerek yeni bağlantınızın kaydedildiğini doğrulayın. 

    ![Mevcut bağlantı](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Sonraki adımlar

* Gelişmiş sorgulama ve analiz için [SQL Server Management Studio ile SQL Database'e bağlanma](sql-database-connect-query-ssms.md) işlemini nasıl gerçekleştireceğinizi öğrenin.
* [Esnek havuzların](sql-database-elastic-pool.md) avantajları hakkında bilgi edinin.
* [Arka uçta SQL Database'e bağlanan bir web uygulaması oluşturma](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md) hakkında bilgi edinin.
