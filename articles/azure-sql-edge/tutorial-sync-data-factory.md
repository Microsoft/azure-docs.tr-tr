---
title: Azure Data Factory kullanarak Azure SQL Edge 'ten veri eşitleme
description: Verileri Azure SQL Edge ve Azure Blob depolama arasında eşitleme hakkında bilgi edinin
keywords: SQL Edge, SQL Edge veri fabrikasından veri eşitleme
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b83201ae864d1f1eb9124af5268360bb1748f6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97507617"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Öğretici: Azure Data Factory kullanarak SQL Edge 'den Azure Blob depolama 'ya veri eşitleme

Bu öğreticide, Azure SQL Edge örneğindeki bir tablodan verileri artımlı olarak Azure Blob depolama ile eşitlemek için Azure Data Factory kullanacaksınız.

## <a name="before-you-begin"></a>Başlamadan önce

Azure SQL Edge dağıtımınızda henüz bir veritabanı veya tablo oluşturmadıysanız, bir tane oluşturmak için aşağıdaki yöntemlerden birini kullanın:

* SQL Edge 'e bağlanmak için [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) veya [Azure Data Studio](/sql/azure-data-studio/download/) kullanın. Veritabanı ve tablo oluşturmak için bir SQL betiği çalıştırın.
* SQL Edge modülüne doğrudan bağlanarak [sqlcmd](/sql/tools/sqlcmd-utility/) kullanarak bir veritabanı ve tablo oluşturun. Daha fazla bilgi için bkz. [sqlcmd kullanarak Database Engine 'e bağlanma](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Bir DAC paket dosyasını SQL Edge kapsayıcısına dağıtmak için SQLPackage.exe kullanın. Modülün istenen özellikler yapılandırmasının bir parçası olarak SqlPackage dosya URI 'sini belirterek, bu işlemi otomatikleştirebilirsiniz. Ayrıca, bir DAC paketini SQL Edge 'e dağıtmak için SqlPackage.exe istemci aracını doğrudan kullanabilirsiniz.

    SqlPackage.exe indirme hakkında daha fazla bilgi için bkz. [SqlPackage 'ı indirme ve yükleme](/sql/tools/sqlpackage-download/). SqlPackage.exe için bazı örnek komutlar aşağıda verilmiştir. Daha fazla bilgi için SqlPackage.exe belgelerine bakın.

    **DAC paketi oluşturma**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **DAC paketi uygulama**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Filigran düzeylerini depolamak ve güncelleştirmek için bir SQL tablosu ve yordamı oluşturma

Bir filigran tablosu, verileri Azure depolama ile eşitlenen son zaman damgasını depolamak için kullanılır. Bir Transact-SQL (T-SQL) saklı yordamı, her eşitlemede sonra filigran tablosunu güncelleştirmek için kullanılır.

Bu komutları SQL Edge örneğinde çalıştırın:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp
    WHERE [TableName] = @TableName

    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Data Factory işlem hattı oluşturma

Bu bölümde, Azure SQL Edge 'deki bir tablodan verileri Azure Blob depolama ile eşitlemek için bir Azure Data Factory işlem hattı oluşturacaksınız.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Data Factory Kullanıcı arabirimini kullanarak bir veri fabrikası oluşturma

[Bu öğreticideki](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)yönergeleri izleyerek bir veri fabrikası oluşturun.

### <a name="create-a-data-factory-pipeline"></a>Data Factory işlem hattı oluşturma

1. Data Factory Kullanıcı arabiriminin **Başlarken** sayfasında Işlem **hattı oluştur**' u seçin.

    ![Data Factory işlem hattı oluşturma](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. İşlem hattının **Özellikler** penceresinin **genel** sayfasında, ad için **dönemsiz eşitleme** girin.

3. Eski eşik değerini almak için arama etkinliğini ekleyin. **Etkinlikler** bölmesinde, **genel** ' i genişletin ve **arama** etkinliğini işlem hattı Tasarımcısı yüzeyine sürükleyin. Etkinliğin adını **Oldfiligrandan** değiştirin.

    ![Eski filigran aramasını ekleyin](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. **Ayarlar** sekmesine geçin ve **kaynak veri kümesi** için **Yeni** ' yi seçin. Artık filigran tablosundaki verileri temsil etmek için bir veri kümesi oluşturacaksınız. Bu tablo, önceki kopyalama işleminde kullanılan eski filigranı içerir.

5. **Yeni veri kümesi** penceresinde **Azure SQL Server**' yi seçin ve ardından **devam**' ı seçin.  

6. Veri kümesinin **Özellikler** penceresinde, **ad**' ın altına **sulu su** yazın.

7. **Bağlı hizmet** için **Yeni**' yi seçin ve ardından şu adımları uygulayın:

    1. **Ad** alanına **SQLDBEdgeLinkedService** girin.

    2. **Sunucu adı** bölümüne SQL Edge sunucu ayrıntılarınızı girin.

    3. Listeden **veritabanınızın adını** seçin.

    4. **Kullanıcı adınızı** ve **parolanızı** girin.

    5. SQL Edge örneğiyle bağlantıyı test etmek için **Bağlantıyı Sına**' yı seçin.

    6. **Oluştur**’u seçin.

    ![Bağlı hizmet oluşturma](media/tutorial-sync-data-factory/create-linked-service.png)

    7. **Tamam**’ı seçin.

8. **Ayarlar** sekmesinde **Düzenle**' yi seçin.

9. **Bağlantı** sekmesinde **[dbo] öğesini seçin. [ Tablo için su marktable]** .  Tablodaki verileri önizlemek istiyorsanız, **Verileri Önizle**' yi seçin.

10. En üstteki işlem hattı sekmesini seçerek veya soldaki ağaç görünümünde işlem hattının adını seçerek işlem hattı düzenleyicisine geçin. Arama etkinliğinin Özellikler penceresinde, **kaynak veri kümesi** listesinde **sulu markmarkdataset** ' in seçili olduğunu onaylayın.

11. **Etkinlikler** bölmesinde, **genel** ' i genişletin ve başka bir **arama** etkinliğini işlem hattı Tasarımcısı yüzeyine sürükleyin. Özellikler penceresinin **genel** sekmesinde adı **newfiligran** olarak ayarlayın. Bu arama etkinliği, kaynak verileri içeren tablodan, hedefe kopyalanabilmesi için yeni filigran değerini alır.

12. İkinci arama etkinliğinin Özellikler penceresinde, **Ayarlar** sekmesine geçin ve yeni eşik değerini içeren kaynak tabloya işaret eden bir veri kümesi oluşturmak için **Yeni** ' yi seçin.

13. **Yeni veri kümesi** penceresinde **SQL Edge örneği**' ni seçin ve ardından **devam**' ı seçin.

    1. **Özellikleri ayarla** penceresinde, **ad**' ın altında **sourceDataset**' i girin. **Bağlı hizmet** altında **SQLDBEdgeLinkedService** öğesini seçin.

    2. **Tablo**' nın altında, eşitlenmesini istediğiniz tabloyu seçin. Bu veri kümesi için, Bu öğreticinin ilerleyen kısımlarında açıklandığı gibi bir sorgu de belirtebilirsiniz. Sorgu, bu adımda belirttiğiniz tabloya göre önceliklidir.

    3. **Tamam**’ı seçin.

14. En üstteki işlem hattı sekmesini seçerek veya soldaki ağaç görünümünde işlem hattının adını seçerek işlem hattı düzenleyicisine geçin. Arama etkinliğinin Özellikler penceresinde, **kaynak veri kümesi** listesinde **sourceDataset** öğesinin seçili olduğunu doğrulayın.

15. Sorgu **kullan** altında **sorgu** ' yı seçin. Aşağıdaki sorgudaki tablo adını güncelleştirin ve ardından sorguyu girin. Tablodan yalnızca en yüksek değeri seçiyoruz `timestamp` . **Yalnızca ilk satırı** seçtiğinizden emin olun.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Sorgu Seç](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. **Etkinlikler** bölmesinde, **Taşı & Dönüştür** ' ü genişletin ve **kopyalama** etkinliğini **Etkinlikler** bölmesinden tasarımcı yüzeyine sürükleyin. Etkinliğin adını **IncrementalCopy** olarak ayarlayın.

17. Arama etkinliklerine bağlı yeşil düğmeyi Kopyalama etkinliğine sürükleyerek her iki Arama etkinliğini Kopyalama etkinliğine bağlayın. Kopyalama etkinliğinin kenarlık rengini mavi olarak değiştirip fare düğmesini bırakın.

18. Kopyalama etkinliğini seçin ve **Özellikler** penceresinde etkinliğin özelliklerini gördüğünüzü onaylayın.

19. **Özellikler** penceresinde **kaynak** sekmesine geçin ve şu adımları izleyin:

    1. **Kaynak veri kümesi** kutusunda **sourceDataset**' i seçin.

    2. **Sorgu kullan** altında **sorgu**' yı seçin.

    3. **Sorgu** kutusuna SQL sorgusunu girin. Örnek bir sorgu aşağıda verilmiştir:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. **Havuz** sekmesinde, **Havuz veri kümesi** altında **Yeni** ' yi seçin.

21. Bu öğreticide, havuz veri deposu bir Azure Blob depolama veri deposudur. **Azure Blob depolama**' yı seçin ve ardından **Yeni veri kümesi** penceresinde **devam** ' ı seçin.

22. **Biçim Seç** penceresinde verilerinizin biçimini seçin ve ardından **devam**' ı seçin.

23. **Özellikleri ayarla** penceresinde, **ad**' ın altında **sinkdataset** adını girin. **Bağlı hizmet** altında **Yeni**' yi seçin. Artık Azure Blob depolama alanına bir bağlantı (bağlı hizmet) oluşturacaksınız.

24. **Yeni bağlı hizmet (Azure Blob depolama)** penceresinde şu adımları uygulayın:

    1. **Ad** kutusuna **AzureStorageLinkedService** girin.

    2. **Depolama hesabı adı** altında Azure aboneliğiniz için Azure Depolama hesabını seçin.

    3. Bağlantıyı test edin ve ardından **son**' u seçin.

25. **Özellikleri ayarla** penceresinde, **AzureStorageLinkedService** **bağlı hizmet** altında seçili olduğunu doğrulayın. **Oluştur** ve **Tamam**' ı seçin.

26. **Havuz** sekmesinde **Düzenle**' yi seçin.

27. SinkDataset 'in **bağlantı** sekmesine gidin ve aşağıdaki adımları tamamlayın:

    1. **Dosya yolu**' nun altında *asdedatasync/incrementalcopy* girin; burada *asdedatasync* blob kapsayıcısı adıdır ve *incrementalcopy* klasör adıdır. Mevcut değilse kapsayıcıyı oluşturun veya var olan bir adın adını kullanın. Azure Data Factory, *incrementalcopy* çıktı klasörünü otomatik olarak oluşturur. Bir blob kapsayıcısındaki klasörlerden birine gitmek istiyorsanız **Dosya yolu** için **Gözat** düğmesini de kullanabilirsiniz.

    2. **Dosya yolunun** **Dosya** bölümü Için, **dinamik Içerik Ekle [alt + P]** öğesini seçin ve **@CONCAT (' artımlı-', işlem hattı () girin. RunId, '. txt ')** açılan pencerede. **Son**'u seçin. Dosya adı, ifade tarafından dinamik olarak oluşturulur. Her işlem hattı çalıştırması benzersiz bir kimliğe sahiptir. Kopyalama etkinliği, dosya adını oluşturmak için çalışma kimliğini kullanır.

28. En üstteki işlem hattı sekmesini seçerek veya soldaki ağaç görünümünde işlem hattının adını seçerek işlem hattı düzenleyicisine geçin.

29. **Etkinlikler** bölmesinde, **genel** ' i genişletin ve **saklı yordam** etkinliğini **Etkinlikler** bölmesinden işlem hattı Tasarımcısı yüzeyine sürükleyin. Kopyalama etkinliğinin yeşil (başarılı) çıkışını saklı yordam etkinliğine bağlayın.

30. Işlem hattı tasarımcısında **saklı yordam etkinliği** ' ni seçin ve adını **Sptoupdatesulu markactivity** olarak değiştirin.

31. **SQL hesabı** sekmesine geçin ve **bağlı hizmet** altında **_QLDBEdgeLinkedService_* öğesini seçin.

32. **Saklı yordam** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Saklı yordam adı** altında **[dbo] öğesini seçin. [ usp_write_watermark]**.

    2. Saklı yordam parametrelerinin değerlerini belirtmek için, **parametreyi Içeri aktar** ' ı seçin ve parametreler için şu değerleri girin:

    |Ad|Tür|Değer|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity (' Newfiligran '). Output. firstRow. Newsulu Markvalue}|
    |TableName|Dize|@ {Activity (' Oldfiligran '). Output. firstRow. TableName}|

33. İşlem hattı ayarlarını doğrulamak için araç çubuğunda **Doğrula** ' yı seçin. Doğrulama hatası olmadığından emin olun. İşlem **hattı doğrulama raporu** penceresini kapatmak için öğesini seçin **>>** .

34. **Tümünü Yayımla** düğmesini seçerek varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) Azure Data Factory hizmetine yayımlayın. Yayımlama işleminin başarılı olduğunu onaylayan bir ileti görene kadar bekleyin.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Bir zamanlamaya göre işlem hattı tetikleme

1. İşlem hattı araç çubuğunda **tetikleyici Ekle**' yi seçin, **Yeni/Düzenle**' yi seçin ve ardından **Yeni**' yi seçin.

2. Tetikleyicinizi **HourlySync** olarak adlandırın. **Tür** altında **zamanlama**' yı seçin. **Yinelemeyi** her 1 saat olarak ayarlayın.

3. **Tamam**’ı seçin.

4. **Tümünü Yayımla**.

5. **Şimdi Tetikle**' yi seçin.

6. Soldaki **İzleyici** sekmesine geçin. El ile tetikleme işlemi tarafından tetiklenen işlem hattı çalıştırmasının durumunu görebilirsiniz. Listeyi yenilemek için **Yenile**’yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticideki Azure Data Factory işlem hattı, bir SQL Edge örneğindeki bir tablodan verileri her saat bir kez Azure Blob depolama alanında bir konuma kopyalar. Diğer senaryolarda Data Factory kullanma hakkında bilgi edinmek için aşağıdaki [öğreticilere](../data-factory/tutorial-copy-data-portal.md)bakın.
