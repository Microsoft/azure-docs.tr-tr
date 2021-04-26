---
title: Azure Stream Analytics işinde SQL veritabanı başvuru verilerini kullanma
description: Bu makalede, bir SQL veritabanının Azure portal ve Visual Studio 'da bir Azure Stream Analytics işi için başvuru verileri girişi olarak nasıl kullanılacağı açıklanır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016414"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Azure Stream Analytics işi için bir SQL veritabanındaki başvuru verilerini kullanma

Azure Stream Analytics, Azure SQL veritabanını başvuru verileri için giriş kaynağı olarak destekler. SQL veritabanını, Azure portal ve Visual Studio 'da Stream Analytics araçlarla Stream Analytics işiniz için başvuru verileri olarak kullanabilirsiniz. Bu makalede her iki yöntemin de nasıl yapılacağı gösterilmektedir.

## <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak Azure SQL veritabanını başvuru giriş kaynağı olarak eklemek için aşağıdaki adımları kullanın:

### <a name="portal-prerequisites"></a>Portal önkoşulları

1. Stream Analytics işi oluşturma.

2. Stream Analytics işi tarafından kullanılacak bir depolama hesabı oluşturun.

3. Stream Analytics işi tarafından başvuru verileri olarak kullanılacak bir veri kümesiyle Azure SQL veritabanınızı oluşturun.

### <a name="define-sql-database-reference-data-input"></a>SQL veritabanı başvuru verileri girişini tanımlayın

1. Stream Analytics işte, **iş topolojisi** altında **girişler** ' i seçin. **Başvuru girişi Ekle** ' ye tıklayın ve **SQL veritabanı**' nı seçin.

   ![Sol gezinti bölmesinde girişler seçilidir. Girişler ' de + başvuru girişi Ekle seçilidir ve BLOB depolama ve SQL veritabanı değerlerini gösteren bir açılan liste görüntülenir.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Stream Analytics giriş yapılandırmasını doldurun. Veritabanı adı, sunucu adı, Kullanıcı adı ve parola ' yı seçin. Başvuru verileri girişinizi düzenli aralıklarla yenilemeyi istiyorsanız, "açık" seçeneğini seçerek yenileme oranını gg: ss: dd olarak belirleyin. Kısa yenileme hızına sahip büyük veri kümeleriniz varsa, bir [Delta sorgu](sql-reference-data.md#delta-query)kullanabilirsiniz.

   ![SQL veritabanı seçildiğinde SQL veritabanı yeni giriş sayfası görüntülenir. Sol bölmede bir yapılandırma formu ve sağ bölmedeki bir anlık görüntü sorgusu bulunur.](./media/sql-reference-data/sql-input-config.png)

3. SQL sorgu Düzenleyicisi 'nde anlık görüntü sorgusunu test edin. Daha fazla bilgi için bkz [. Azure Portal SQL sorgu düzenleyicisini kullanarak bağlanma ve veri sorgulama](../azure-sql/database/connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Iş yapılandırmasında depolama hesabı belirtin

**Yapılandır** altında **depolama hesabı ayarları** ' na gidin ve **depolama hesabı ekle**' yi seçin.

   ![Sol bölmede depolama hesabı ayarları seçilidir. Sağ bölmede bir depolama hesabı Ekle düğmesi bulunur.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>İşi başlatma

Diğer girişleri, çıkışları ve sorguyu yapılandırdıktan sonra Stream Analytics işini başlatabilirsiniz.

## <a name="tools-for-visual-studio"></a>Visual Studio Araçları

Visual Studio kullanarak Azure SQL veritabanını başvuru giriş kaynağı olarak eklemek için aşağıdaki adımları kullanın:

### <a name="visual-studio-prerequisites"></a>Visual Studio önkoşulları

1. [Visual Studio için Stream Analytics araçları 'Nı yükler](stream-analytics-tools-for-visual-studio-install.md). Aşağıdaki Visual Studio sürümleri desteklenir:

   * Visual Studio 2015
   * Visual Studio 2019

2. Visual Studio Hızlı Başlangıç [Stream Analytics araçları](stream-analytics-quick-create-vs.md) hakkında bilgi sahibi olun.

3. Depolama hesabı oluşturma.

### <a name="create-a-sql-database-table"></a>SQL veritabanı tablosu oluşturma

Başvuru verilerinizi depolamak üzere bir tablo oluşturmak için SQL Server Management Studio kullanın. Ayrıntılar için bkz. [SSMS kullanarak Ilk Azure SQL veritabanınızı tasarlama](../azure-sql/database/design-first-database-tutorial.md) .

Aşağıdaki örnekte kullanılan örnek tablo aşağıdaki deyimden oluşturulmuştur:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Aboneliğinizi seçin

1. Visual Studio'nun **Görünüm** menüsünde **Sunucu Gezgini**'ni seçin.

2. **Azure**' a sağ tıklayın, **Microsoft Azure aboneliğine Bağlan**' ı seçin ve Azure hesabınızla oturum açın.

### <a name="create-a-stream-analytics-project"></a>Stream Analytics projesi oluşturma

1. **Dosya > Yeni Proje**'yi seçin. 

2. Sol taraftaki şablon listesinden **Stream Analytics**'i ve ardından **Azure Stream Analytics Uygulaması**'nı seçin. 

3. Proje **adı**, **konum** ve **çözüm adını** girip **Tamam**' ı seçin.

   ![Stream Analytics şablonu seçilidir, Azure Stream Analytics Uygulama seçilidir ve ad, konum ve çözüm adları kutuları vurgulanır.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>SQL veritabanı başvuru verileri girişini tanımlayın

1. Yeni bir giriş oluşturun.

   ![Yeni öğe Ekle ' de giriş seçilidir.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. **Çözüm Gezgini** **Input.js** çift tıklayın.

3. **Stream Analytics giriş yapılandırmasını** doldurun. Veritabanı adını, sunucu adını, yenileme türünü ve yenileme hızını seçin. Yenileme hızını biçimde belirtin `DD:HH:MM` .

   ![Stream Analytics girişi yapılandırması ' nda, açılan listelerden değerler girilir veya seçilir.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   "Yalnızca bir kez Yürüt" veya "düzenli aralıklarla Yürüt" seçeneğini belirlerseniz, **[Input Alias]** ADLı bir SQL codebehind dosyası, dosya düğümündeki **Input.js** altında projede oluşturulur.

   ![SQL CodeBehind dosyası Chemicals. Snapshot. SQL vurgulanır.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   "Delta ile düzenli aralıklarla Yenile" seçeneğini belirlerseniz iki SQL CodeBehind dosyası oluşturulur: **[Input Alias]. Snapshot. SQL** ve **[Input Alias]. Delta. SQL**.

   ![SQL CodeBehind dosyaları Chemicals. Delta. SQL ve Chemicals. Snapshot. SQL vurgulanır.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. SQL dosyasını düzenleyicide açın ve SQL sorgusunu yazın.

5. Visual Studio 2019 kullanıyorsanız ve SQL Server veri Araçları ' nı yüklediyseniz, **Çalıştır**' a tıklayarak sorguyu test edebilirsiniz. SQL veritabanına bağlanmanıza yardımcı olacak bir sihirbaz penceresi açılır ve sorgu sonucu alt kısımdaki pencerede görüntülenir.

### <a name="specify-storage-account"></a>Depolama hesabı belirtin

SQL başvuru anlık görüntülerini depolamaya yönelik depolama hesabını belirtmek için **üzerindeJobConfig.js** açın.

   ![Stream Analytics Işi yapılandırma yapılandırması varsayılan değerlerle gösterilir. Genel depolama ayarları vurgulanır.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Yerel olarak test edin ve Azure 'a dağıtın

İşi Azure 'a dağıtmaya başlamadan önce, canlı giriş verilerine karşı sorgu mantığını yerel olarak test edebilirsiniz. Bu özellik hakkında daha fazla bilgi için bkz. [Visual Studio için Azure Stream Analytics araçları 'nı kullanarak canlı verileri yerel olarak test etme (Önizleme)](stream-analytics-live-data-local-testing.md). Testi tamamladıktan sonra **Azure 'A Gönder ' e** tıklayın. İşi nasıl başlatacağınızı öğrenmek için [Visual Studio hızlı başlangıç Azure Stream Analytics araçlarını kullanarak Stream Analytics oluşturma](stream-analytics-quick-create-vs.md) ' ya başvurun.

## <a name="delta-query"></a>Delta sorgusu

Delta sorgu kullanılırken, [Azure SQL veritabanı 'nda](../azure-sql/temporal-tables.md) zamana bağlı tablolar önerilir.

1. Azure SQL veritabanı 'nda zamana bağlı bir tablo oluşturun.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Anlık görüntü sorgusunu yazar. 

   Stream Analytics çalışma zamanına, SQL veritabanı 'na yönelik başvuru verileri kümesinin, sistem saatinde geçerli olarak geçerli olduğunu bildirmek için **\@ snapshottime** parametresini kullanın. Bu parametreyi sağlamazsanız, saat eğrisinden dolayı yanlış bir temel başvuru veri kümesi elde edersiniz. Tam anlık görüntü sorgusuna örnek aşağıda verilmiştir:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Delta sorgusunu yazar. 
   
   Bu sorgu, başlangıç saati, **\@ deltastarttime** ve bitiş zamanı **\@ deltabitişsaati** içinde eklenen veya silinen SQL veritabanındaki tüm satırları alır. Delta sorgusunun anlık görüntü sorgusuyla aynı sütunları ve sütun **_işlemini_** döndürmesi gerekir. Bu sütun, **\@ deltastarttime** ve **\@ deltabitişsaati** arasında satırın eklendiğini veya silinip silinmediğini tanımlar. Kayıtlar eklenmişse, sonuç satırları **1** olarak işaretlenir veya silinirse **2** ' dir. Ayrıca, Delta dönemindeki tüm güncelleştirmelerin uygun şekilde yakalandığından emin olmak için sorgunun SQL Server kenarından **filigran** eklemesi gerekir. Delta sorgusunun **filigran** olmadan kullanılması yanlış başvuru veri kümesine neden olabilir.  

   Güncelleştirilmiş kayıtlar için, isteğe bağlı tablo, ekleme ve silme işlemini yakalayıp barındırmaz. Stream Analytics çalışma zamanı, başvuru verilerini güncel tutmak için Delta sorgusunun sonuçlarını önceki anlık görüntüye uygular. Delta sorgusuna örnek aşağıda gösterilmektedir:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Stream Analytics çalışma zamanının, denetim noktalarını depolamak için Delta sorgusuna ek olarak anlık görüntü sorgusunu da çalıştırabileceğini unutmayın.

## <a name="test-your-query"></a>Sorgunuzu test etme
   Sorgunuzun, Stream Analytics işin başvuru verileri olarak kullanacağı beklenen veri kümesini döndürdüğünü doğrulamak önemlidir. Sorgunuzu test etmek için, portalda Iş topolojisi bölümünde giriş bölümüne gidin. Daha sonra SQL veritabanı başvuru girişte örnek veriler ' i seçebilirsiniz. Örnek kullanılabilir hale geldikten sonra dosyayı indirebilir ve döndürülmekte olan verilerin beklendiği gibi olup olmadığını kontrol edebilirsiniz. Geliştirme ve test yinelemelerinizi iyileştirmek istiyorsanız, [Visual Studio için Stream Analytics araçları](./stream-analytics-tools-for-visual-studio-install.md)kullanmanız önerilir. Tercih ettiğiniz diğer araçlardan birini, önce sorgunun Azure SQL veritabanı 'ndan doğru sonuçları döndürdüğünü ve sonra bunu Stream Analytics işte kullanmasını sağlamak için de kullanabilirsiniz. 

### <a name="test-your-query-with-visual-studio-code"></a>Sorgunuzu Visual Studio Code ile test etme

   Visual Studio Code [Azure Stream Analytics araçları](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) ve [SQL Server (MSSQL)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) yükleyip asa projenizi ayarlayın. Daha fazla bilgi için bkz. [hızlı başlangıç: Visual Studio Code](./quick-create-visual-studio-code.md) ve [SQL Server (MSSQL) uzantı öğreticisinde](/sql/tools/visual-studio-code/sql-server-develop-use-vscode)Azure Stream Analytics işi oluşturma.

1. SQL Reference veri girişinizi yapılandırın.
   
   ![Visual Studio Code Düzenleyicisi (Tab) üzerinde ReferenceSQLDatabase.jsgösterir.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. SQL Server simgesini seçin ve **bağlantı ekle**' ye tıklayın.
   
   ![+ Bağlantı Ekle sol bölmede görünür ve vurgulanır.](./media/sql-reference-data/add-sql-connection.png)

3. Bağlantı bilgilerini girin.
   
   ![Veritabanı ve sunucu bilgileri için iki kutu vurgulanır.](./media/sql-reference-data/fill-connection-information.png)

4. SQL Başvurusu ' na sağ tıklayın ve **sorguyu Yürüt**' ü seçin.
   
   ![Execute sorgusu, bağlam menüsünde vurgulanır.](./media/sql-reference-data/execute-query.png)

5. Bağlantınızı seçin.
   
   ![İletişim kutusunda "aşağıdaki listeden bir bağlantı profili oluşturma" ve listenin bir girişi vardır ve bu, üst sınır anlamına gelir.](./media/sql-reference-data/choose-connection.png)

6. Sorgu sonucunu gözden geçirin ve doğrulayın.
   
   ![Sorgu arama sonuçları VS Code düzenleyici sekmesindedir.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>SSS

**Azure Stream Analytics 'de SQL Reference veri girişini kullanarak ek ücret ödemem gerekir mi?**

Stream Analytics işinde [akış birimi başına ek ücret](https://azure.microsoft.com/pricing/details/stream-analytics/) alınmaz. Ancak, Stream Analytics işin ilişkili bir Azure depolama hesabı olması gerekir. Stream Analytics işi, başvuru veri kümesini almak için SQL DB 'yi sorgular (iş başlatma ve yenileme aralığı sırasında) ve bu anlık görüntüyü depolama hesabında depolar. Bu anlık görüntülerin depolanması, Azure depolama hesabı için [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/storage/) ayrıntılı ek ücretler doğurur.

**Nasıl yaparım?, başvuru verileri anlık görüntüsünün SQL DB 'den sorgulanmakta olduğunu ve Azure Stream Analytics işinde kullanıldığını öğrensin mi?**

SQL veritabanı başvuru verileri girişinin sistem durumunu izlemek için kullanabileceğiniz, mantıksal ada (ölçümler Azure portal) göre filtrelenen iki ölçüm vardır.

   * Inputevents: Bu ölçüm, SQL veritabanı başvuru verileri kümesinden yüklenen kayıt sayısını ölçer.
   * Inputeventbytes: Bu ölçüm, Stream Analytics işinin belleğine yüklenen başvuru verileri anlık görüntüsünün boyutunu ölçer. 

Bu ölçümlerin her ikisinin birleşimi, işin SQL veritabanını başvuru verileri kümesini getirmek ve belleğe yüklemek üzere sorguladığını anlamak için kullanılabilir.

**Özel bir Azure SQL veritabanı türü isteyecek mıyım?**

Azure Stream Analytics, herhangi bir Azure SQL veritabanı türüyle çalışacaktır. Ancak, başvuru veri girişi için ayarlanan yenileme oranının sorgu yüklerinizi etkileyebileceğini anlamak önemlidir. Delta sorgu seçeneğini kullanmak için Azure SQL veritabanı 'nda zamana bağlı tablolar kullanılması önerilir.

**Azure Storage hesabında neden Azure Stream Analytics anlık görüntüleri depoluyız?**

Stream Analytics tam bir olay işlemesi ve en az bir olay teslimini garanti eder. Geçici sorunların işinizi etkilediği durumlarda, durumu geri yüklemek için az miktarda yeniden yürütme gerekir. Yeniden yürütmeyi etkinleştirmek için, bu anlık görüntülerin bir Azure depolama hesabında depolanması gerekir. Kontrol noktası yeniden yürütme hakkında daha fazla bilgi için, [Azure Stream Analytics Işlerinde denetim noktası ve yeniden yürütme kavramları](stream-analytics-concepts-checkpoint-replay.md)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream Analytics aramalar için başvuru verilerini kullanma](stream-analytics-use-reference-data.md)
* [Hızlı başlangıç: Visual Studio için Azure Stream Analytics araçlarını kullanarak bir Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)
* [Visual Studio için Azure Stream Analytics araçları 'nı kullanarak canlı verileri yerel olarak test etme (Önizleme)](stream-analytics-live-data-local-testing.md)