---
title: Azure Data Factory örnekleri
description: Azure Data Factory hizmetiyle birlikte gelen örneklerle ilgili ayrıntıları sağlar.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139255"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory örnekleri
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Azure kod örnekleri galerisinde Data Factory ve kod örnekleri](https://azure.microsoft.com/resources/samples/?service=data-factory) [içindeki PowerShell örnekleri](../samples-powershell.md) .


## <a name="samples-on-github"></a>GitHub’daki örnekler
[GitHub Azure-DataFactory deposu](https://github.com/azure/azure-datafactory) , Azure Data Factory hizmetine hızlıca başlamanıza yardımcı olan birkaç örnek içerir (veya) betikleri değiştirebilir ve kendi uygulamasında kullanabilirsiniz. Samples\JSON klasörü, yaygın senaryolar için JSON kod parçacıklarını içerir.

| Örnek | Açıklama |
|:--- |:--- |
| [ADF Izlenecek yol](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Bu örnek, içindeki günlük dosyalarından verileri öngörülere açmak için Azure Data Factory kullanarak günlük dosyalarını işlemeye yönelik bir uçtan uca izlenecek yol sağlar. <br/><br/>Bu kılavuzda, Data Factory işlem hattı örnek günlükleri toplar, verileri başvuru verileriyle kaydeder ve zenginleştirir ve son zamanlarda başlatılan bir pazarlama kampanyasının verimliliğini değerlendirmek üzere verileri dönüştürür. |
| [JSON örnekleri](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Bu örnek, yaygın senaryolar için JSON örnekleri sağlar. |
| [Http veri yükleyici örneği](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Bu örnek, özel .NET etkinliği kullanarak bir HTTP uç noktasından Azure Blob depolama alanına veri indirme örneklerini gösterir. |
| [Çapraz AppDomain nokta net etkinlik örneği](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Bu örnek, ADF başlatıcısı tarafından kullanılan derleme sürümleriyle sınırlı olmayan özel bir .NET etkinliği yazmanıza izin verir (örneğin, WindowsAzure. Storage v 4.3.0, Newtonsoft. JSON v 6.0. x, vb.). |
| [R betiğini Çalıştır](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Bu örnek, RScript. exe ' yi çağırmak için kullanılabilecek Data Factory özel etkinliğini içerir. Bu örnek yalnızca R 'nin yüklü olduğu (isteğe bağlı olmayan) HDInsight kümeniz ile birlikte kullanılabilir. |
| [HDInsight Hadoop kümesinde Spark işlerini çağırma](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Bu örnek, bir Spark programını çağırmak için MapReduce etkinliğinin nasıl kullanılacağını gösterir. Spark programı yalnızca bir Azure Blob kapsayıcısından diğerine veri kopyalar. |
| [Azure Machine Learning Batch puanlama etkinliği kullanarak Twitter Analizi](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Bu örnekte, Twitter yaklaşım analizi, Puanlama, tahmin vb. gerçekleştiren bir Azure Machine Learning modeli çağırmak için AzureMLBatchScoringActivity 'nin nasıl kullanılacağı gösterilmektedir. |
| [Özel etkinlik kullanan Twitter Analizi](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Bu örnekte, Twitter yaklaşım analizi, Puanlama, tahmin vb. gerçekleştiren bir Azure Machine Learning modeli çağırmak için özel bir .NET etkinliğinin nasıl kullanılacağı gösterilmektedir. |
| [Azure Machine Learning için parametreli işlem hatları](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Örnek, Puanlama için N işlem hattı dağıtmak ve C# her biri bölge listesinin bu örneğe dahil edilen Parameters. txt dosyasından geldiği farklı bir Region parametresi ile yeniden eğitim sağlamak için bir uçtan uca kod sağlar. |
| [Azure Stream Analytics işleri için başvuru verilerini yenileme](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Bu örnek, başvuru verileriyle sorguları çalıştırmak ve bir zamanlamaya göre başvuru verileri için yenilemeyi ayarlamak üzere Azure Data Factory ve Azure Stream Analytics birlikte nasıl kullanılacağını gösterir. |
| [Şirket içi Hortonçalışmalar Hadoop ile karma işlem hattı](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Örnek, bulutta bir HDInsight tabanlı Hadoop kümesi gibi diğer işlem hedeflerini de ekleyeceğiniz gibi Data Factory işleri çalıştırmak için bir şirket içi Hadoop kümesini bir işlem hedefi olarak kullanır. |
| [JSON dönüştürme aracı](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Bu araç, 2015-07-01-Preview öncesi sürüm 'den en güncel veya 2015-07-01-Önizleme (varsayılan) sürümüne kadar Jerze 'ı dönüştürmenizi sağlar. |
| [U-SQL örnek giriş dosyası](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Bu dosya bir U-SQL etkinliği tarafından kullanılan örnek bir dosyadır. |
| [Blob dosyasını Sil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Bu örnek, dosyalar kopyalandıktan C# sonra kaynak Azure Blob konumundan dosyaları sılmek için ADF özel .net etkinliğinin bir parçası olarak kullanılabilecek bir dosyayı gösterir.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları
GitHub üzerinde Data Factory için aşağıdaki Azure Resource Manager şablonları bulabilirsiniz.

| Şablon | Açıklama |
| --- | --- |
| [Azure Blob depolama alanından Azure SQL veritabanına kopyalama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Bu şablonu dağıtmak, belirtilen Azure Blob depolama alanından Azure SQL veritabanına veri kopyalayan bir işlem hattı ile bir Azure veri fabrikası oluşturur |
| [Salesforce 'tan Azure Blob depolamaya kopyalama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Bu şablonu dağıtmak, belirtilen Salesforce hesabından Azure Blob depolama alanına veri kopyalayan bir işlem hattına sahip bir Azure veri fabrikası oluşturur. |
| [Azure HDInsight kümesinde Hive betiği çalıştırarak verileri dönüştürme](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Bu şablonu dağıtmak, bir Azure HDInsight Hadoop kümesinde örnek Hive betiğini çalıştırarak verileri dönüştüren bir işlem hattı ile bir Azure Data Factory oluşturur. |

## <a name="samples-in-azure-portal"></a>Azure portal örnekleri
Veri fabrikanıza örnek işlem hatlarını ve bunlarla ilişkili varlıkları (veri kümeleri ve bağlı hizmetler) dağıtmak için veri fabrikanızın giriş sayfasında **örnek işlem hatları** kutucuğunu kullanabilirsiniz.

1. Bir veri fabrikası oluşturun veya var olan bir veri fabrikasını açın. Veri Fabrikası oluşturma adımları için [Data Factory kullanarak blob DEPOLAMADAN SQL veritabanına veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) konusuna bakın.
2. Data Factory için **Data Factory** dikey penceresinde **örnek işlem hatları** kutucuğuna tıklayın.

    ![Örnek işlem hatları kutucuğu](./media/data-factory-samples/SamplePipelinesTile.png)
3. Örnek işlem **hatları** dikey penceresinde, dağıtmak istediğiniz **örneğe** tıklayın.

    ![Örnek işlem hatları dikey penceresi](./media/data-factory-samples/SampleTile.png)
4. Örnek için yapılandırma ayarlarını belirtin. Örneğin, Azure depolama hesabınızın adı ve hesap anahtarınız, Azure SQL Server adı, veritabanı, Kullanıcı KIMLIĞI ve parola vb.

    ![Örnek dikey pencere](./media/data-factory-samples/SampleBlade.png)
5. Yapılandırma ayarlarını belirterek tamamladıktan sonra, işlem hatları tarafından kullanılan örnek işlem hatlarını ve bağlı hizmetleri/tabloları oluşturmak/dağıtmak için **Oluştur** ' a tıklayın.
6. **Örnek işlem hatları** dikey penceresinde daha önce tıklattığınız örnek kutucukta dağıtım durumunu görürsünüz.

    ![Dağıtım durumu](./media/data-factory-samples/DeploymentStatus.png)
7. Örnek için kutucukta **dağıtım başarılı** iletisini gördüğünüzde, **örnek işlem hatları** dikey penceresini kapatın.  
8. **Data Factory** dikey penceresinde bağlı hizmetler, veri kümeleri ve işlem hatları 'nın veri fabrikanıza eklendiğini görürsünüz.  

    ![Data Factory dikey penceresi](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Visual Studio 'da örnekler
### <a name="prerequisites"></a>Önkoşullar
Bilgisayarınızda şunların yüklü olması gerekir:

* Visual Studio 2013 veya Visual Studio 2015
* Visual Studio 2013 veya Visual Studio 2015 için Azure SDK’sını indirin. [Azure İndirme Sayfası](https://azure.microsoft.com/downloads/)’na gidin ve **.NET** bölümündeki **VS 2013** veya **VS 2015**’e tıklayın.
* Visual Studio için en son Azure Data Factory eklentisini indirin: [Vs 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) veya [vs 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Visual Studio 2013 kullanıyorsanız, aşağıdaki adımları uygulayarak da eklentiyi güncelleştirebilirsiniz: Menüde **Araçlar** -> **Uzantılar ve güncelleştirmeler** -> **çevrimiçi** **Visual Studio Galerisi Microsoft Azure Visual Studio için Data Factory**Araçları ' na tıklayın.  ->  ->  ->  **Güncelleştirin**.

### <a name="use-data-factory-templates"></a>Data Factory şablonları kullanma
1. Menüdeki **Dosya** ' ya tıklayın, **Yeni**' nin üzerine gelin ve **Proje**' ye tıklayın.
2. **Yeni Proje** iletişim kutusunda aşağıdaki adımları uygulayın:

   1. **Şablonlar**altında **DataFactory** ' yi seçin.
   2. Sağ bölmedeki **Data Factory şablonlar** ' ı seçin.
   3. Proje için bir **ad** girin.
   4. Proje için bir **konum** seçin.
   5. **Tamam**'ı tıklatın.

      ![Yeni Proje iletişim kutusu](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. **Data Factory şablonlar** iletişim kutusunda, **kullanım örneği şablonları** bölümünde örnek şablonu seçin ve **İleri**' ye tıklayın. Aşağıdaki adımlar, **Müşteri profil oluşturma** şablonunu kullanma işleminde size yol gösterir. Adımlar diğer örneklere benzerdir.

    ![Data Factory Şablonlar iletişim kutusu](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. **Data Factory yapılandırma** iletişim kutusunda, **Data Factory temel bilgiler** sayfasında **İleri** ' ye tıklayın.
5. **Data Factory 'Yi Yapılandır** sayfasında, aşağıdaki adımları uygulayın:
   1. **Yeni Data Factory oluştur**' u seçin. **Var olan veri fabrikasını kullan**seçeneğini de belirleyebilirsiniz.
   2. Veri Fabrikası için bir **ad** girin.
   3. Veri fabrikasının oluşturulmasını istediğiniz **Azure aboneliğini** seçin.
   4. Veri Fabrikası için **kaynak grubunu** seçin.
   5. **Bölge**için **Batı ABD**, **Doğu ABD**veya **Kuzey Avrupa** seçin.
   6. **İleri**'ye tıklayın.
6. **Veri depolarını Yapılandır** sayfasında, mevcut BIR **Azure SQL veritabanı** ve **Azure depolama hesabı** belirtin (veya) veritabanı/depolama alanı oluşturun ve ileri ' ye tıklayın.
7. **Işlem yapılandırma** sayfasında, Varsayılanlar ' ı seçin ve **İleri**' ye tıklayın.
8. **Özet** sayfasında tüm ayarları gözden geçirin ve **İleri**' ye tıklayın.
9. **Dağıtım durumu** sayfasında, dağıtım bitene kadar bekleyin ve **son**' a tıklayın.
10. Çözüm Gezgini’nde projeye sağ tıklayın ve ardından **Yayımla**’ya tıklayın.
11. **Microsoft hesabınızda oturum açın** iletişim kutusunu görmezseniz, Azure aboneliğindeki kimlik bilgilerini hesap için girin ve **oturum aç**’a tıklayın.
12. Aşağıdaki iletişim kutusunu göreceksiniz:

    ![Yayımla iletişim kutusu](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. **Data Factory’yi yapılandırma** sayfasında aşağıdaki adımları uygulayın:

    1. **Mevcut Data Factory seçeneğinin kullanılacağını** onaylayın.
    2. Şablonu kullanırken seçtiğiniz **veri fabrikasını** seçin.
    3. **Öğeleri Yayımla** sayfasına geçmek için **İleri**’ye tıklayın. (Ad alanından çıkmak için, **İleri** düğmesi devre dışıysa **SEKME** tuşuna basın.)
14. **Öğeleri Yayımla** sayfasında tüm Data Factory varlıklarının işaretli olmasını sağlayın ve **Özet** sayfasına geçmek için **İleri**’ye tıklayın.     
15. Özeti gözden geçirin, dağıtım işlemini başlatmak ve **Dağıtım Durumu**’nu görüntülemek için **İleri**’ye tıklayın.
16. **Dağıtım Durumu** sayfasında dağıtım sürecinin durumunu görmelisiniz. Dağıtımını gerçekleştirdikten sonra Son'a tıklayın.

Data Factory varlıkları yazmak ve bunları Azure 'Da yayımlamak için Visual Studio 'Yu kullanma hakkında ayrıntılı bilgi için bkz. [ilk veri fabrikanızı derleme (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) .          
