---
title: "Öğretici: Azure HDInsight 'ta Data Factory ile isteğe bağlı kümeler"
description: Öğretici-Azure Data Factory kullanarak HDInsight 'ta isteğe bağlı Apache Hadoop kümeleri oluşturmayı öğrenin.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: c61ad4d26c4a03889d9ac80332335543ec4140b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868990"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Öğretici: Azure Data Factory kullanarak HDInsight 'ta isteğe bağlı Apache Hadoop kümeleri oluşturma

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Bu öğreticide, isteğe bağlı olarak Azure HDInsight 'ta Azure Data Factory kullanarak [Apache Hadoop](../hdinsight/hdinsight-overview.md#cluster-types-in-hdinsight) kümesi oluşturmayı öğreneceksiniz. Daha sonra Azure Data Factory ' de veri işlem hatlarını kullanarak Hive işlerini çalıştırabilir ve kümeyi silebilirsiniz. Bu öğreticinin sonuna kadar, `operationalize` küme oluşturma, iş çalıştırma ve küme silme işlemlerinin zamanlamaya göre yapıldığı büyük bir veri işinin nasıl yapılacağını öğrenirsiniz.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Azure depolama hesabı oluşturma
> * Azure Data Factory etkinliğini anlama
> * Azure portal kullanarak veri fabrikası oluşturma
> * Bağlı hizmetler oluşturma
> * İşlem hattı oluşturma
> * İşlem hattını tetikleme
> * İşlem hattını izleme
> * Çıktıyı doğrulama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

* PowerShell [az Module](/powershell/azure/) yüklendi.

* Bir Azure Active Directory hizmet sorumlusu. Hizmet sorumlusunu oluşturduktan sonra, bağlantılı makaledeki yönergeleri kullanarak **uygulama kimliği** ve **kimlik doğrulama anahtarını** almayı unutmayın. Bu öğreticinin ilerleyen kısımlarında bu değerlere ihtiyacınız vardır. Ayrıca, hizmet sorumlusunun aboneliğin veya kümenin oluşturulduğu kaynak grubunun *katkıda* bulunan rolünün bir üyesi olduğundan emin olun. Gerekli değerleri alma ve doğru rolleri atama yönergeleri için bkz. [Azure Active Directory hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Ön Azure nesneleri oluşturma

Bu bölümde, talep üzerine oluşturduğunuz HDInsight kümesi için kullanılacak çeşitli nesneler oluşturursunuz. Oluşturulan depolama hesabı, `partitionweblogs.hql` küme üzerinde çalışan bir örnek Apache Hive işinin benzetimini yapmak için kullandığınız örnek HiveQL betiğini içerir.

Bu bölümde, depolama hesabını oluşturmak ve depolama hesabı içinde gerekli dosyaların üzerine kopyalamak için bir Azure PowerShell betiği kullanılmaktadır. Bu bölümdeki Azure PowerShell örnek komut dosyası aşağıdaki görevleri yapar:

1. Azure 'da oturum açar.
2. Bir Azure Kaynak grubu oluşturur.
3. Azure Depolama hesabı oluşturur.
4. Depolama hesabında bir blob kapsayıcısı oluşturur
5. Örnek HiveQL betiğini (**partitionweblogs. HQL**) blob kapsayıcısını kopyalar. Betiği adresinde bulabilirsiniz [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) . Örnek betik zaten başka bir genel blob kapsayıcısında kullanılabilir. Aşağıdaki PowerShell betiği, oluşturduğu Azure depolama hesabına bu dosyaların bir kopyasını oluşturur.

### <a name="create-storage-account-and-copy-files"></a>Depolama hesabı oluşturma ve dosyaları kopyalama

> [!IMPORTANT]  
> Azure Kaynak grubu ve komut dosyası tarafından oluşturulacak Azure depolama hesabı için ad belirtin.
> Betik tarafından yazılan **kaynak grubu adı**, **depolama hesabı adı** ve **depolama hesabı anahtarını** yazın. Sonraki bölümde bunlar gerekecektir.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Depolama hesabını doğrula

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Soldan **tüm hizmetler**  >  **genel**  >  **kaynak grupları**' na gidin.
1. PowerShell betiğinizdeki oluşturduğunuz kaynak grubu adını seçin. Listelenmiş çok fazla kaynak grubunuz varsa filtreyi kullanın.
1. **Genel bakış** görünümünden, kaynak grubunu diğer projelerle paylaşmadığınız takdirde bir kaynağı listelenmiş görürsünüz. Bu kaynak, daha önce belirttiğiniz adı taşıyan depolama hesabıdır. Depolama hesabı adını seçin.
1. **Kapsayıcılar** kutucuğunu seçin.
1. **Adfgetstarted** kapsayıcısını seçin. Adlı bir klasör görürsünüz **`hivescripts`** .
1. Klasörünü açın ve, **partitionweblogs. HQL** örnek betik dosyasını içerdiğinden emin olun.

## <a name="understand-the-azure-data-factory-activity"></a>Azure Data Factory etkinliğini anlayın

[Azure Data Factory](../data-factory/introduction.md) , verilerin hareketini ve dönüşümünü düzenler ve otomatiklaştırır. Azure Data Factory, bir giriş veri dilimini işlemek için tam zamanında bir HDInsight Hadoop kümesi oluşturabilir ve işlem tamamlandığında kümeyi silebilir.

Azure Data Factory, veri fabrikasının bir veya daha fazla veri işlem hattı olabilir. Bir veri işlem hattının bir veya daha fazla etkinliği vardır. İki tür etkinlik vardır:

* [Veri taşıma etkinlikleri](../data-factory/copy-activity-overview.md). Veri taşıma etkinliklerini kullanarak bir kaynak veri deposundan hedef veri deposuna veri taşıyabilirsiniz.
* [Veri dönüştürme etkinlikleri](../data-factory/transform-data.md). Verileri dönüştürmek/işlemek için veri dönüştürme etkinliklerini kullanırsınız. HDInsight Hive etkinliği, Data Factory tarafından desteklenen dönüştürme etkinliklerinden biridir. Bu öğreticide Hive dönüştürme etkinliğini kullanın.

Bu makalede, Hive etkinliğini isteğe bağlı bir HDInsight Hadoop kümesi oluşturacak şekilde yapılandırırsınız. Etkinlik, verileri işlemek için çalıştığında şöyle olur:

1. An HDInsight Hadoop kümesi, dilimi işlemek için tam zamanında otomatik olarak oluşturulur.

2. Giriş verileri, kümede bir HiveQL betiği çalıştırılarak işlenir. Bu öğreticide, Hive etkinliğiyle ilişkili HiveQL betiği aşağıdaki eylemleri yapar:

    * , Başka bir tablo **Hivesampleout** oluşturmak için var olan tabloyu (*hivesampletable*) kullanır.
    * **Hivesampleout** tablosunu özgün *hivesampletable* içindeki yalnızca belirli sütunlarla doldurur.

3. HDInsight Hadoop kümesi, işlem tamamlandıktan sonra silinir ve küme yapılandırılan süre (timeToLive ayarı) için boşta kalır. Sonraki veri dilimi, bu timeToLive boşta kalma süresi içinde işleme için kullanılabiliyorsa, dilimi işlemek için aynı küme kullanılır.  

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. Sol menüden **`+ Create a resource`**  >  **analiz**  >  **Data Factory**' a gidin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png" alt-text="Portalda Azure Data Factory":::

3. **Yeni Data Factory** kutucuğu için aşağıdaki değerleri girin veya seçin:

    |Özellik  |Değer  |
    |---------|---------|
    |Ad | Veri Fabrikası için bir ad girin. Bu adın genel olarak benzersiz olması gerekir.|
    |Sürüm | **V2** adresinden ayrılın. |
    |Abonelik | Azure aboneliğinizi seçin. |
    |Kaynak grubu | PowerShell betiğini kullanarak oluşturduğunuz kaynak grubunu seçin. |
    |Konum | Konum, daha önce kaynak grubunu oluştururken belirttiğiniz konuma otomatik olarak ayarlanır. Bu öğreticide, konum **Doğu ABD** olarak ayarlanır. |
    |GIT 'i etkinleştir|Bu kutunun işaretini kaldırın.|

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png" alt-text="Azure portal kullanarak Azure Data Factory oluşturma":::

4. **Oluştur**’u seçin. Bir veri fabrikası oluşturmak 2 ila 4 dakika arasında bir süre sürebilir.

5. Data Factory oluşturulduktan sonra **Kaynağa Git** düğmesine sahip bir **dağıtım başarılı** bildirimi alırsınız.  Data Factory varsayılan görünümü açmak için **Kaynağa Git** ' i seçin.

6. Azure Data Factory yazma ve izleme portalını başlatmak için **yazar & İzleyicisi** ' ni seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png" alt-text="Azure Data Factory portalına genel bakış":::

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Bu bölümde, veri fabrikanızın içinde iki bağlı hizmet yazardınız.

* Bir Azure depolama hesabını veri fabrikasına bağlayan **Azure Depolama bağlı hizmeti**. Bu depolama alanı, isteğe bağlı HDInsight kümesi tarafından kullanılır. Ayrıca, kümede çalıştırılan Hive betiğini de içerir.
* **İsteğe bağlı HDInsight bağlı hizmeti**. Azure Data Factory otomatik olarak bir HDInsight kümesi oluşturur ve Hive betiğini çalıştırır. Daha sonra, küme önceden yapılandırılmış bir süre boyunca boşta kaldığında HDInsight kümesini siler.

### <a name="create-an-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma

1. **Başlarken** sayfasının sol bölmesinde **Yazar** simgesini seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png" alt-text="Azure Data Factory bağlı hizmeti oluşturma":::

2. Pencerenin sol alt köşesinden **Bağlantılar** ' ı seçin ve ardından **+ Yeni**' yi seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png" alt-text="Azure Data Factory bağlantı oluşturma":::

3. **Yeni bağlı hizmet** Iletişim kutusunda **Azure Blob depolama** ' yı seçip **devam**' ı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png" alt-text="Data Factory için Azure Storage bağlı hizmeti oluşturma":::

4. Depolama bağlı hizmeti için aşağıdaki değerleri sağlayın:

    |Özellik |Değer |
    |---|---|
    |Ad |`HDIStorageLinkedService` yazın.|
    |Azure aboneliği |Açılır listeden aboneliğinizi seçin.|
    |Depolama hesabı adı |PowerShell betiğinin bir parçası olarak oluşturduğunuz Azure Depolama hesabını seçin.|

    **Bağlantıyı Sına** ' yı seçin ve başarılı olursa **Oluştur**' u seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png" alt-text="Azure depolama bağlı hizmeti için ad belirtin":::

### <a name="create-an-on-demand-hdinsight-linked-service"></a>İsteğe bağlı bir HDInsight bağlı hizmeti oluşturma

1. Başka bir bağlı hizmet oluşturmak için **+ Yeni** düğmesini tekrar seçin.

2. **Yeni bağlı hizmet** penceresinde, **işlem** sekmesini seçin.

3. **Azure HDInsight**' ı seçin ve ardından **devam**' ı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png" alt-text="Azure Data Factory için HDInsight bağlı hizmetini oluşturun":::

4. **Yeni bağlı hizmet** penceresinde aşağıdaki değerleri girin ve geri kalanını varsayılan olarak bırakın:

    | Özellik | Değer |
    | --- | --- |
    | Ad | `HDInsightLinkedService` yazın.|
    | Tür | **İsteğe bağlı HDInsight '** ı seçin. |
    | Azure Storage Bağlı Hizmeti | `HDIStorageLinkedService` öğesini seçin. |
    | Küme türü | **Hadoop** seçin |
    | Yaşam süresi | HDInsight kümesinin otomatik olarak silinmeden önce kullanılabilir olmasını istediğiniz süreyi belirtin.|
    | Hizmet sorumlusu KIMLIĞI | Önkoşulların bir parçası olarak oluşturduğunuz Azure Active Directory hizmet sorumlusunun uygulama KIMLIĞINI sağlayın. |
    | Hizmet sorumlusu anahtarı | Azure Active Directory hizmet sorumlusu için kimlik doğrulama anahtarı sağlayın. |
    | Küme adı ön eki | Veri Fabrikası tarafından oluşturulan tüm küme türlerine ön eki eklenecek bir değer sağlayın. |
    |Abonelik |Açılır listeden aboneliğinizi seçin.|
    | Kaynak grubunu seçme | Daha önce kullandığınız PowerShell betiğinin bir parçası olarak oluşturduğunuz kaynak grubunu seçin.|
    | İşletim sistemi türü/kümesi SSH Kullanıcı adı | Genellikle bir SSH Kullanıcı adı girin `sshuser` . |
    | İşletim sistemi türü/kümesi SSH parolası | SSH kullanıcısı için bir parola belirtin |
    | İşletim sistemi türü/küme Kullanıcı adı | Genellikle bir küme Kullanıcı adı girin `admin` . |
    | İşletim sistemi türü/küme parolası | Küme kullanıcısı için bir parola belirtin. |

    Ardından **Oluştur**’u seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png" alt-text="HDInsight bağlı hizmeti için değerler sağlayın":::

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. **+**(Artı) düğmesini seçin ve ardından Işlem **hattı**' nı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png" alt-text="Azure Data Factory işlem hattı oluşturma":::

1. **Etkinlikler** araç kutusunda **HDInsight**' ı genişletin ve **Hive** etkinliğini ardışık düzen Tasarımcısı yüzeyine sürükleyin. **Genel** sekmesinde, etkinlik için bir ad belirtin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png" alt-text="Data Factory işlem hattına etkinlik ekleme":::

1. Hive etkinliğinin seçili olduğundan emin olun, **HDI kümesi** sekmesini seçin. **HDInsight bağlı hizmeti** açılan listesinden, daha önce **HDInsightLinkedService**, HDInsight için oluşturduğunuz bağlı hizmeti seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png" alt-text="İşlem hattı için HDInsight kümesi ayrıntılarını sağlama":::

1. **Betik** sekmesini seçin ve aşağıdaki adımları uygulayın:

    1. **Betiğe bağlı hizmet** için, açılan listeden **HDIStorageLinkedService** öğesini seçin. Bu değer, daha önce oluşturduğunuz depolama bağlı hizmetidir.

    1. **Dosya yolu** Için, **depolamayı inceleyin** ' i seçin ve örnek Hive betiğinin kullanılabildiği konuma gidin. PowerShell betiğini daha önce çalıştırdıysanız, bu konum olmalıdır `adfgetstarted/hivescripts/partitionweblogs.hql` .

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png" alt-text="İşlem hattı için Hive betik ayrıntıları sağlayın":::

    1. **Gelişmiş**  >  **Parametreler** altında öğesini seçin **`Auto-fill from script`** . Bu seçenek, Hive betiğindeki, çalışma zamanında değer gerektiren tüm parametreleri arar.

    1. **Değer** metin kutusunda, varolan klasörü biçiminde ekleyin `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` . Bu yol büyük/küçük harfe duyarlıdır. Bu yol, betiğin çıktısının depolanacağı yerdir. `wasbs`Depolama hesaplarında varsayılan olarak etkinleştirilmiş güvenli aktarım gerekli olduğundan şema gereklidir.

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png" alt-text="Hive betiği için parametreler sağlama":::

1. İşlem hattını doğrulamak için **Doğrula** ' yı seçin. **>>** Doğrulama penceresini kapatmak için (sağ ok) düğmesini seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png" alt-text="Azure Data Factory ardışık düzeni doğrulama":::

1. Son olarak, yapıAzure Data Factory tları yayımlamak için **Tümünü Yayımla** ' yı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png" alt-text="Azure Data Factory işlem hattını yayımlama":::

## <a name="trigger-a-pipeline"></a>İşlem hattını tetikleme

1. Tasarımcı yüzeyinde araç çubuğundan şimdi **tetikleyici tetikleyicisi Ekle**' yi seçin  >  .

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png" alt-text="Azure Data Factory işlem hattını tetikleme":::

2. Açılır taraftaki çubukta **Tamam ' ı** seçin.

## <a name="monitor-a-pipeline"></a>İşlem hattını izleme

1. Soldaki **İzleyici** sekmesine geçin. **İşlem Hattı Çalıştırmaları** listesinde bir işlem hattı çalıştırması görürsünüz. **Durum** sütununun altında çalıştırmanın durumuna dikkat edin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png" alt-text="Azure Data Factory işlem hattını izleme":::

1. Durumu yenilemek için **Yenile**’yi seçin.

1. Ardışık düzen ile ilişkili etkinlik çalıştırmasını görmek için **etkinlik çalıştırmalarını görüntüle** simgesini de seçebilirsiniz. Aşağıdaki ekran görüntüsünde, oluşturduğunuz işlem hattında yalnızca bir etkinlik olduğundan yalnızca bir etkinlik çalıştırması görürsünüz. Önceki görünüme geri dönmek için sayfanın üst kısmına doğru işlem **hatları** ' nı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png" alt-text="Azure Data Factory işlem hattı etkinliğini izleme":::

## <a name="verify-the-output"></a>Çıktıyı doğrulama

1. Çıktıyı doğrulamak için Azure portal, bu öğretici için kullandığınız depolama hesabına gidin. Aşağıdaki klasörleri veya kapsayıcıları görmeniz gerekir:

    * İşlem hattının parçası olarak çalıştırılan Hive betiğinin çıkışını içeren bir **adfgerstarted/outputfolder** görürsünüz.

    * Bir **\<linked-service-name> - \<timestamp> adfhdidatafactory-** Container görürsünüz. Bu kapsayıcı, işlem hattı çalıştırmasının bir parçası olarak oluşturulan HDInsight kümesinin varsayılan depolama konumudur.

    * Azure Data Factory iş günlüklerine sahip bir **adfjobs** kapsayıcısı görürsünüz.  

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png" alt-text="Azure Data Factory işlem hattı çıkışını doğrulama":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

İsteğe bağlı HDInsight kümesi oluşturma ile, HDInsight kümesini açıkça silmeniz gerekmez. Küme, işlem hattını oluştururken verdiğiniz yapılandırmaya göre silinir. Küme silindikten sonra bile, kümeyle ilişkilendirilen depolama hesapları var olmaya devam eder. Verilerinizi bozulmadan saklayabilmeniz için bu davranış tasarıma göre yapılır. Ancak, verileri kalıcı hale getirmek istemiyorsanız, oluşturduğunuz depolama hesabını silebilirsiniz.

Ya da, bu öğretici için oluşturduğunuz kaynak grubunun tamamını silebilirsiniz. Bu işlem, oluşturduğunuz depolama hesabını ve Azure Data Factory siler.

### <a name="delete-the-resource-group"></a>Kaynak grubunu silme

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol bölmedeki **kaynak grupları** ' nı seçin.
1. PowerShell betiğinizdeki oluşturduğunuz kaynak grubu adını seçin. Listelenmiş çok fazla kaynak grubunuz varsa filtreyi kullanın. Kaynak grubunu açar.
1. Kaynak grubunu diğer projelerle paylaşmadığınız takdirde, **kaynaklar** kutucuğunda, varsayılan depolama hesabı ve veri fabrikasının listelenmesi gerekir.
1. **Kaynak grubunu sil**'i seçin. Bunu yaptığınızda depolama hesabı ve depolama hesabında depolanan veriler silinir.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png" alt-text="Azure portal kaynak grubunu sil":::

1. Silmeyi onaylamak için kaynak grubunun adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, isteğe bağlı HDInsight kümesi oluşturmak ve Apache Hive işleri çalıştırmak için Azure Data Factory kullanmayı öğrendiniz. Özel yapılandırmayla HDInsight kümeleri oluşturmayı öğrenmek için bir sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Özel yapılandırmayla Azure HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md)