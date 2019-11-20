---
title: Azure Data Factory kullanarak Azure SQL veritabanı kenarından verileri eşitleyin | Microsoft Docs
description: Verileri Azure SQL veritabanı Edge ve Azure Blob depolama arasında eşitleme hakkında bilgi edinin
keywords: SQL veritabanı Edge, SQL veritabanı kenarından veri eşitleme, SQL veritabanı Edge Veri Fabrikası
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501326"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Öğretici: Azure Data Factory kullanarak SQL veritabanı kenarından Azure Blob depolama 'ya veri eşitleme

Bu öğreticide, Azure SQL veritabanı Edge örneğindeki bir tablodaki verileri Azure Blob depolama alanına artımlı olarak eşitlemek için Azure Data Factory kullanırsınız.

## <a name="before-you-begin"></a>Başlamadan önce

Azure SQL veritabanı kenar dağıtımınızda henüz bir veritabanı veya tablo oluşturmadıysanız, bir tane oluşturmak için aşağıdaki yöntemlerden birini kullanın:

* SQL veritabanı kenarına bağlanmak ve veritabanı ve tablo oluşturmak için bir SQL betiği yürütmek üzere [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) veya [Azure Data Studio](/sql/azure-data-studio/download/) kullanın.
* SQL veritabanı Edge modülüne doğrudan bağlanarak [sqlcmd](/sql/tools/sqlcmd-utility/) kullanarak bir SQL veritabanı ve tablo oluşturun. Daha fazla bilgi için bkz. [sqlcmd kullanarak veritabanı altyapısına bağlanma](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* SQL veritabanı kenar kapsayıcısına bir dacpac dosyası dağıtmak için SQLPackage. exe ' yi kullanın. Bu, modüller istenen özellikler yapılandırması kapsamında SQLPackage dosya URI 'SI belirtilerek veya bir dacpac 'i SQL veritabanı ucuna dağıtmak için doğrudan SqlPackage. exe istemci Aracı kullanılarak otomatikleştirilebilir.

    SqlPackage 'i indirmek için bkz. [SqlPackage 'ı indirme ve yükleme](/sql/tools/sqlpackage-download/). SqlPackage. exe için aşağıdaki örnek komutlar sağlanır, ancak daha fazla bilgi için SqlPackage belgelerine bakın.

    **Dacpac oluştur**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Dacpac Uygula**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Filigran düzeylerini depolamak ve güncelleştirmek için bir SQL tablosu ve yordamı oluşturma

Filigran tablosu, verilerin Azure depolama ile zaten eşitlenmiş olduğu son zaman damgasını depolamak için kullanılır. Transact-SQL (T-SQL) saklı yordamı her eşitlemeden sonra filigran tablosunu güncelleştirmek için kullanılır. 

SQL veritabanı Edge örneğinde aşağıdaki komutları yürütün:

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
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Data Factory Iş akışı oluşturma

Bu bölümde, Azure SQL veritabanı Edge içindeki bir tablodaki verileri Azure Blob depolama alanına eşitlemek için bir Azure Data Factory işlem hattı oluşturacaksınız.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Data Factory Kullanıcı arabirimini kullanarak Data Factory oluşturma

Bu [öğreticideki](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)yönergeleri kullanarak bir Data Factory oluşturun.

### <a name="create-a-data-factory-pipeline"></a>Data Factory işlem hattı oluşturma

1. Data Factory Kullanıcı arabiriminin **Başlarken sayfasında Işlem** **hattı oluştur** kutucuğunu seçin.

    ![Data Factory-işlem hattı oluşturma](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. İşlem hattının **Özellikler** penceresinin **genel** sayfasında, **dönemsiz eşitleme** adı ' nı girin.

3. Eski eşik değerini almak için **arama** etkinliğini ekleyin. **Etkinlikler Araç kutusunda** **genel**' i genişletin & sürükleyin ve **arama** etkinliğini işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını *Oldfiligrandan*değiştirin.

    ![Eski filigran arama](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. **Ayarlar** sekmesine geçin ve **kaynak veri kümesi**için **+ Yeni** ' yi seçin. Bu adımda, su marktable içindeki verileri temsil eden bir veri kümesi oluşturacaksınız. Bu tablo, önceki kopyalama işleminde kullanılan eski filigranı içerir.

5. **Yeni veri kümesi** penceresinde **Azure SQL Server**' yi seçin ve **devam**' ı seçin.  

6. Veri kümesinin **Özellikler** penceresinde, ad Için *sulu markmarkdataset* girin.

7. **Bağlı hizmet**için **Yeni**' yi seçin ve ardından aşağıdaki adımları gerçekleştirin:

    1. **Ad**için *SQLDBEdgeLinkedService* girin.

    2. **Sunucu adı**Için SQL veritabanı uç sunucu ayrıntılarınızı girin.

    3. **Veritabanı adınızı** açılan listeden girin.

    4. **Kullanıcı adınızı** ve **parolanızı**girin.

    5. SQL veritabanı Edge örneğiyle bağlantıyı test etmek için **Bağlantıyı Sına**' yı seçin.

    6. **Oluştur**'u seçin.

    ![bağlı hizmet oluştur](media/tutorial-sync-data-factory/create-linked-service.png)

    7. **Tamam 'ı** seçin

8. **Ayarlar** sekmesinde **Düzenle**' yi seçin.

9. **Bağlantı** sekmesinde *[dbo] öğesini seçin. [ Tablo için su marktable]* . Tablodaki verileri önizlemek istiyorsanız, **Verileri Önizle**' yi seçin.

10. En üstteki işlem hattı sekmesini seçerek veya soldaki ağaç görünümünde işlem hattının adını seçerek işlem hattı düzenleyicisine geçin. **Arama etkinliğinin**Özellikler penceresinde, **kaynak veri kümesi** alanı Için **sulu markmarkdataset** ' in seçili olduğunu onaylayın.

11. **Etkinlikler** araç kutusunda **genel**' i genişletin, başka bir **arama** etkinliğini işlem hattı Tasarımcısı yüzeyine sürükleyip bırakın ve Özellikler penceresinin **genel** sekmesinde adı **newfiligran** olarak ayarlayın. Bu Arama etkinliği, kaynak verileri içeren tablodan hedefe kopyalanacak yeni filigran değerini alır.

12. İkinci **arama** etkinliğinin Özellikler penceresinde, **Ayarlar** sekmesine geçin **ve yeni ' yi seçerek yeni** filigran değerini içeren kaynak tabloya işaret eden bir veri kümesi oluşturun.

13. **Yeni veri kümesi** penceresinde SQL veritabanı Edge örneği ' ni seçin ve **devam**' ı seçin.

    1. **Özellikleri ayarla** penceresinde, **ad**için **sourceDataset** girin. Bağlı hizmet için *SQLDBEdgeLinkedService* seçin.

    2. Tablo için ***eşitlenmesini istediğiniz tabloyu*** seçin. Bu veri kümesi için öğreticide daha sonra bahsedildiği gibi bir sorgu de belirtebilirsiniz. Bu sorgu, bu adımda belirttiğiniz tablodan önceliklidir.

    3. **Tamam**’ı seçin.

14. En üstteki işlem hattı sekmesini seçerek veya soldaki ağaç görünümünde işlem hattının adını seçerek işlem hattı düzenleyicisine geçin. **Arama** etkinliğinin özellikler penceresinde **Kaynak Veri Kümesi** alanı için **SourceDataset** seçeneğinin belirlendiğinden emin olun.

15. Sorgu **kullan** alanı için **sorgu** ' yı seçin ve sorgudaki tablo adını güncelleştirdikten sonra aşağıdaki sorguyu girin: yalnızca tablodaki zaman damgasının en büyük değerini seçersiniz. Lütfen **yalnızca ilk satırı**da seçtiğinizden emin olun.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Sorgu Seç](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. **Etkinlikler** araç kutusunda, **Taşı & Dönüştür**' ü genişletin, etkinlikler araç kutusundan **kopyalama** etkinliğini sürükleyip bırakın ve adı **IncrementalCopy**olarak ayarlayın.

17. **Arama** etkinliklerine eklenen **yeşil düğmeyi** **kopyalama** etkinliğine sürükleyerek, her iki **arama** etkinliğini **kopyalama** etkinliğine bağlayın. Kopyalama etkinliğinin kenarlık rengini mavi olarak değiştirip fare düğmesini bırakın.

18. **Kopyalama** etkinliğini seçin ve **Özellikler** penceresinde etkinliğin özelliklerini görtığınızdan emin olun.

19. **Özellikler** penceresinde **Kaynak** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Kaynak Veri Kümesi** alanı için **SourceDataset**’i seçin.

    2. **Sorgu Kullan** alanı için **Sorgu**’yu seçin.

    3. **Sorgu** alanı için SQL sorgusunu girin. Aşağıdaki örnek sorgu

    4. SQL sorgusu:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. **Havuz** sekmesine geçin ve **Havuz veri kümesi** alanı için **+ Yeni** ' yi seçin.

21. Bu öğreticide, havuz veri deposu **Azure Blob depolama**türünde. **Azure Blob depolama**' yı seçin ve **Yeni veri kümesi** penceresinde **devam** ' ı seçin.

22. **Biçim Seç** penceresinde, verilerinizin biçim türünü seçin ve **devam**' ı seçin.

23. **Özellikleri ayarla** penceresinde ad Için **sinkdataset** girin. Bağlı hizmet için **+ Yeni**' yi seçin. Bu adımda, **Azure Blob Depolama**’nıza yönelik bir bağlantı (bağlı hizmet) oluşturursunuz.

24. **Yeni bağlı hizmet (Azure Blob depolama)** penceresinde aşağıdaki adımları uygulayın:

    1. Ad için *AzureStorageLinkedService* girin.

    2. Azure aboneliğiniz ile **depolama hesabı adı** Için Azure depolama hesabınızı seçin.

    3. **Bağlantıyı** test edin ve ardından **son**' u seçin.

25. **Özellikleri ayarla** penceresinde, **bağlı hizmet**için *AzureStorageLinkedService* ' nin seçili olduğunu doğrulayın. Sonra **Oluştur** ve **Tamam**' ı seçin.

26. **Havuz** sekmesinde **Düzenle**' yi seçin.

27. *Sinkdataset* 'in **bağlantı** sekmesine gidin ve aşağıdaki adımları uygulayın:

    1. **Dosya yolu** alanı için *asdedatasync/incrementalcopy*girin; burada **adföğreticisi** blob kapsayıcısı adı, **incrementalcopy** ise klasör adıdır. Henüz yoksa kapsayıcıyı oluşturun veya var olan bir kapsayıcının adına ayarlayın. *incrementalcopy* adlı çıktı dosyası mevcut değilse Azure Data Factory tarafından otomatik olarak oluşturulur. Bir blob kapsayıcısındaki klasörlerden birine gitmek istiyorsanız **Dosya yolu** için **Gözat** düğmesini de kullanabilirsiniz.

    2. **Dosya yolu** alanının **Dosya** bölümü Için, **dinamik Içerik Ekle [alt + P]** öğesini seçin ve ardından *@CONCAT(' artımlı-', işlem hattı () girin. RunId, '. txt ')* açılan pencerede. Ardından **Son**’u seçin. Dosya adı, ifade kullanılarak dinamik olarak oluşturulur. Her işlem hattı çalıştırması benzersiz bir kimliğe sahiptir. Kopyalama etkinliği, dosya adını oluşturmak için çalışma kimliğini kullanır.

28. En üstteki işlem hattı sekmesini seçerek veya soldaki ağaç görünümünde işlem hattının adını seçerek işlem **hattı** düzenleyicisine geçin.

29. **Etkinlikler** araç kutusunda **Genel**’i genişletin ve **Etkinlikler** araç kutusundan **Saklı Yordam** etkinliğini sürükleyip işlem hattı tasarımcısının yüzeyine bırakın. **Kopyalama** etkinliğinin yeşil (Başarılı) çıktısını **Saklı Yordam** etkinliğine **bağlayın**.

30. Işlem hattı tasarımcısında **saklı yordam etkinliği** ' ni seçin, adını *Sptoupdatesulu markactivity*olarak değiştirin.

31. **SQL hesabı** sekmesine geçin ve **bağlı hizmet**için *SQLDBEdgeLinkedService* öğesini seçin.

32. **Saklı Yordam** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Saklı yordam adı**için *[dbo] öğesini seçin. [ usp_write_watermark]* .

    2. Saklı yordam parametrelerinin değerlerini belirtmek için, parametreyi Içeri Aktar ' ı seçin ve parametreler için aşağıdaki değerleri girin:

    |Ad|Tür|Değer|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity (' Newfiligran '). Output. firstRow. Newsulu Markvalue}|
    |TableName|Dize|@ {Activity (' Oldfiligran '). Output. firstRow. TableName}|

33. İşlem hattı ayarlarını doğrulamak için araç çubuğunda **Doğrula** ' yı seçin. Doğrulama hatası olmadığından emin olun. İşlem **hattı doğrulama raporu** penceresini kapatmak için **>>** ' yi seçin.

34. **Tümünü Yayımla** düğmesine tıklayarak varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) Azure Data Factory hizmetinde yayımlayın. Yayımlamanın başarılı olduğunu belirten bir ileti görene kadar bekleyin.

## <a name="trigger-a-pipeline-on-schedule"></a>Zamanlamaya göre işlem hattı tetikleme

1. İşlem hattı araç çubuğunda **tetikleyici Ekle**' yi seçin ve **Yeni/Düzenle**' yi seçin ve **+ Yeni**' yi seçin

2. Tetikleyiciyi *HourlySync*ile adlandırın, zamanlama olarak **yazın** ' ı seçin ve **yinelenme** 'yi her 1 saat olarak ayarlayın.

3. **Tamam**’ı seçin.

4. **Tümünü Yayımla**.

5. **Şimdi Tetikle**' yi seçin.

6. Soldaki **İzleyici** sekmesine geçin. El ile tetikleme işlemi tarafından tetiklenen işlem hattı çalıştırmasının durumunu görebilirsiniz. Listeyi yenilemek için **Yenile** düğmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticideki Azure Data Factory işlem hattı, SQL veritabanı Edge örneğindeki bir tablodan verileri saatlik bir sıklıkta Azure Blob depolamada bir konuma kopyalar. Daha fazla senaryoda Data Factory kullanma hakkında daha fazla bilgi edinmek için bu [öğreticilerle](../data-factory/tutorial-copy-data-portal.md)gidin.
