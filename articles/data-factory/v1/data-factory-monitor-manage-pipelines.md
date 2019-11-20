---
title: Azure portal ve PowerShell kullanarak işlem hatlarını izleme ve yönetme
description: Azure portal ve Azure PowerShell kullanarak oluşturduğunuz Azure veri fabrikalarını ve işlem hatlarını izleyip yönetme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666975"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure portal ve PowerShell 'i kullanarak Azure Data Factory işlem hatlarını izleme ve yönetme
> [!div class="op_single_selector"]
> * [Azure portal/Azure PowerShell kullanma](data-factory-monitor-manage-pipelines.md)
> * [Izleme ve yönetim uygulaması kullanma](data-factory-monitor-manage-app.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory işlem hatlarını izleme ve yönetme](../monitor-visually.md).

Bu makalede Azure portal ve PowerShell kullanarak işlem hatlarınızı izleme, yönetme ve hata ayıklama işlemlerinin nasıl yapılacağı açıklanır.

> [!IMPORTANT]
> İzleme & yönetimi uygulaması, veri işlem hatlarınızı izlemek ve yönetmek için ve sorunları gidermeye yönelik daha iyi bir destek sağlar. Uygulamayı kullanma hakkında ayrıntılı bilgi için bkz. [izleme ve yönetim uygulamasını kullanarak Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory sürüm 1 artık yeni [Azure izleyici uyarı altyapısını](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)kullanıyor. Eski uyarı altyapısı kullanımdan kaldırılmıştır. Sonuç olarak, sürüm 1 veri fabrikaları için yapılandırılmış mevcut uyarılarınız artık çalışmaz. V1 veri fabrikaları için mevcut uyarılarınız otomatik olarak geçirilmez. Bu uyarıları yeni uyarı altyapısında yeniden oluşturmanız gerekir. Sürüm 1 veri fabrikalarınız için Azure portal oturum açın ve ölçümler üzerinde yeni uyarılar (başarısız çalıştırmalar veya başarılı çalıştırmalar gibi) oluşturmak için **izleyici** ' yi seçin.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>İşlem hatlarını ve etkinlik durumlarını anlama
Azure portal kullanarak şunları yapabilirsiniz:

* Veri fabrikanızı diyagram olarak görüntüleyin.
* Etkinlikleri bir işlem hattında görüntüleyin.
* Giriş ve çıkış veri kümelerini görüntüleyin.

Bu bölümde ayrıca bir veri kümesi diliminin bir durumdan başka bir duruma nasıl geçirdiği açıklanmaktadır.   

### <a name="navigate-to-your-data-factory"></a>Veri fabrikanıza gitme
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Soldaki menüdeki **veri fabrikaları** ' na tıklayın. Bunu görmüyorsanız, **diğer hizmetler >** ' a tıklayın ve ardından **zeka + analiz** kategorisi altında **veri fabrikaları** ' na tıklayın.

   ![Tüm > veri fabrikalarını inceleyin](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. **Veri fabrikaları** dikey penceresinde ilgilendiğiniz veri fabrikasını seçin.

    ![Veri fabrikası seçme](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Data Factory için ana sayfayı görmeniz gerekir.

   ![Veri fabrikası dikey penceresi](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Veri fabrikanızın Diyagram görünümü
Bir veri fabrikasının **Diyagram** görünümü, veri fabrikasını ve varlıklarını izlemek ve yönetmek için tek bir cam bölmesi sağlar. Veri fabrikanızın **Diyagram** görünümünü görmek için, veri fabrikasının giriş sayfasında **Diyagram** ' a tıklayın.

![Diyagram görünümü](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Yakınlaştırabilir, yakınlaştırabilir, sığacak kadar yakınlaştırabilir, %100 Yakınlaştır, Diyagram düzenini kilitler ve işlem hatlarını ve veri kümelerini otomatik olarak konumlandırabilirsiniz. Data kökenini bilgilerini de görebilirsiniz (diğer bir deyişle, seçili öğelerin yukarı akış ve aşağı akış öğelerini gösterebilirsiniz).

### <a name="activities-inside-a-pipeline"></a>İşlem hattının içindeki etkinlikler
1. İşlem hattına sağ tıklayın ve sonra işlem hattındaki tüm etkinlikleri görüntülemek için işlem hattını **Aç** ' a tıklayın. Bu işlem, etkinliklerin giriş ve çıkış veri kümeleriyle birlikte. Bu özellik, işlem hatlarınız birden fazla etkinlik içerdiğinde ve tek bir işlem hattının işletimsel kökenini anlamak istediğinizde yararlıdır.

    ![İşlem hattı menüsünü açma](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. Aşağıdaki örnekte, bir giriş ve çıkış ile işlem hattında bir kopyalama etkinliği görürsünüz. 

    ![İşlem hattının içindeki etkinlikler](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Sağ üst köşedeki içerik haritası ' nda bulunan **Data Factory** bağlantısına tıklayarak veri fabrikasının giriş sayfasına geri gidebilirsiniz.

    ![Data Factory 'ye geri git](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Bir işlem hattının içindeki her etkinliğin durumunu görüntüleme
Etkinlik tarafından üretilen veri kümelerinin herhangi birinin durumunu görüntüleyerek bir etkinliğin geçerli durumunu görüntüleyebilirsiniz.

**Diyagramdaki** **outputblobtable** öğesine çift tıklayarak, bir işlem hattının içinde farklı etkinlik çalıştırmaları tarafından üretilen tüm dilimleri görebilirsiniz. Kopyalama etkinliğinin son sekiz saat boyunca başarıyla çalıştığını ve dilimleri **hazırlanma** durumunda üretmekte olduğunu görebilirsiniz.  

![İşlem hattının durumu](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Data Factory 'deki veri kümesi dilimleri aşağıdaki durumlardan birine sahip olabilir:

<table>
<tr>
    <th align="left">Durum</th><th align="left">Alt</th><th align="left">Açıklama</th>
</tr>
<tr>
    <td rowspan="8">Bekleniyor</td><td>ScheduleTime</td><td>Dilimin çalışması için geçen süre.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Yukarı akış bağımlılıkları için hazırlanma.</td>
</tr>
<tr>
<td>Bilgisayar ile kaynaklar</td><td>İşlem kaynakları kullanılamıyor.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Tüm etkinlik örnekleri diğer dilimleri çalıştırmakla meşgul.</td>
</tr>
<tr>
<td>Activityözgeçmişi</td><td>Etkinlik duraklatılır ve etkinlik sürdürülene kadar dilimleri çalıştıramıyorum.</td>
</tr>
<tr>
<td>Yeniden Dene</td><td>Etkinlik yürütmesi yeniden deneniyor.</td>
</tr>
<tr>
<td>Doğrulama</td><td>Doğrulama henüz başlatılmadı.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Doğrulama yeniden denenmek üzere bekliyor.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Ediyor</td><td>Doğrulamada</td><td>Doğrulama devam ediyor.</td>
</tr>
<td>-</td>
<td>Dilim işleniyor.</td>
</tr>
<tr>
<td rowspan="4">Başarısız</td><td>Zaman aşımına uğradı</td><td>Etkinlik yürütmesi etkinliğin izin verilenden daha uzun sürdü.</td>
</tr>
<tr>
<td>İptal edildi</td><td>Dilim Kullanıcı eylemi tarafından iptal edildi.</td>
</tr>
<tr>
<td>Doğrulama</td><td>Doğrulama başarısız oldu.</td>
</tr>
<tr>
<td>-</td><td>Dilim oluşturulamadı ve/veya doğrulanamadı.</td>
</tr>
<td>Hazır</td><td>-</td><td>Dilim, tüketim için hazırlayın.</td>
</tr>
<tr>
<td>Atlanmış</td><td>None</td><td>Dilim işlenmiyor.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Farklı bir durumla aynı şekilde kullanılan bir dilim, ancak sıfırlandı.</td>
</tr>
</table>



**Son güncellenen dilimler** dikey penceresinde bir dilim girişine tıklayarak bir dilimle ilgili ayrıntıları görüntüleyebilirsiniz.

![Dilim ayrıntıları](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Dilim birden çok kez yürütülürse, **etkinlik çalıştırmaları** listesinde birden çok satır görürsünüz. Etkinlik **çalıştırmaları** listesinde Çalıştır girişi ' ne tıklayarak bir etkinliğin çalışma hakkındaki ayrıntıları görüntüleyebilirsiniz. Liste, varsa, bir hata iletisiyle birlikte tüm günlük dosyalarını gösterir. Bu özellik, veri fabrikanızın ayrılmasına gerek kalmadan günlükleri görüntülemek ve hatalarını ayıklamak için faydalıdır.

![Etkinlik çalışma ayrıntıları](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Dilim, **Ready** durumunda değilse, etkin olmayan yukarı akış dilimlerini görebilir ve geçerli dilimi, **henüz kullanılamayan yukarı akış dilimlerinde** yürütmeyi engelliyor olarak izleyebilirsiniz. Bu özellik, dilim **bekleme** durumundaysa ve dilimin beklediği yukarı akış bağımlılıklarını anlamak istediğinizde faydalıdır.

![Kullanılabilir olmayan yukarı akış dilimleri](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Veri kümesi durumu diyagramı
Bir veri fabrikası dağıttıktan ve işlem hatları geçerli bir etkin döneme sahip olduktan sonra, veri kümesi dilimleri bir durumdan diğerine geçiş yapılır. Şu anda, dilim durumu aşağıdaki durum diyagramını izler:

![Durum diyagramı](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Data Factory 'deki veri kümesi durumu geçiş akışı şunlardır: bekleme-> sürüyor/devam ediyor (doğrulanıyor)-> Ready/Failed.

Dilim **bekleme** durumunda başlar ve yürütmeden önce önkoşulların karşılanmasını bekler. Sonra, etkinlik yürütülmeye başlar ve dilim **devam ediyor** durumuna geçer. Etkinlik yürütmesi başarılı veya başarısız olabilir. Dilim, yürütmenin sonucuna bağlı olarak, **Ready** veya **Failed**olarak işaretlenir.

Dilimi, **Ready** veya **başarısız** durumundan **bekleme** durumuna geri dönmek için sıfırlayabilirsiniz. Ayrıca, etkinliğin yürütülmesini ve dilimi işlemesini önleyen, **atlanacak**dilim durumunu da işaretleyebilirsiniz.

## <a name="pause-and-resume-pipelines"></a>İşlem hatlarını duraklatma ve devam ettirme
Azure PowerShell kullanarak işlem hatlarınızı yönetebilirsiniz. Örneğin, Azure PowerShell cmdlet 'lerini çalıştırarak işlem hatlarını duraklatabilir ve devam ettirebilirsiniz. 

> [!NOTE] 
> Diyagram görünümü, işlem hatlarını duraklatmayı ve sürdürmeyi desteklemez. Bir kullanıcı arabirimi kullanmak istiyorsanız, izleme ve yönetme uygulamasını kullanın. Uygulamayı kullanma hakkında ayrıntılı bilgi için bkz. [izleme ve yönetim uygulaması makalesini kullanarak Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) . 

**Suspend-AzDataFactoryPipeline** PowerShell cmdlet 'ini kullanarak işlem hatlarını duraklatabilir/askıya alabilirsiniz. Bu cmdlet, bir sorun düzeltilene kadar işlem hatlarınızı çalıştırmak istemediğinizde yararlıdır. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Örneğin:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Sorun ardışık düzen ile düzeltildikten sonra, aşağıdaki PowerShell komutunu çalıştırarak askıya alınmış işlem hattını sürdürebilirsiniz:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Örneğin:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Hata ayıklama ardışık düzenleri
Azure Data Factory, Azure portal ve Azure PowerShell kullanarak işlem hatlarında hata ayıklamanıza ve sorun gidermenize yönelik zengin yetenekler sağlar.

> [!NOTE] 
> Izleme & yönetimi uygulamasını kullanarak hatalara troubleshot çok daha kolay. Uygulamayı kullanma hakkında ayrıntılı bilgi için bkz. [izleme ve yönetim uygulaması makalesini kullanarak Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) . 

### <a name="find-errors-in-a-pipeline"></a>İşlem hattında hata bulma
Etkinlik bir işlem hattında başarısız olursa, işlem hattı tarafından üretilen veri kümesi hata nedeniyle hata durumunda olur. Aşağıdaki yöntemleri kullanarak Azure Data Factory hata ayıklama ve sorun giderme işlemleri yapabilirsiniz.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Hata ayıklamak için Azure portal kullanma
1. **Tablo** dikey penceresinde, **durumu** **başarısız**olarak ayarlanan sorun dilimine tıklayın.

   ![Sorun dilimiyle tablo dikey penceresi](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. **Veri dilimi** dikey penceresinde başarısız olan etkinlik çalıştırmasına tıklayın.

   ![Hata içeren veri dilimi](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. **Etkinlik çalışma ayrıntıları** dikey penceresinde, HDInsight işlemeyle ilişkili dosyaları indirebilirsiniz. Hata hakkındaki ayrıntıları içeren hata günlüğü dosyasını indirmek için durum/stderr için **İndir** ' e tıklayın.

   ![Etkinlik çalıştırma ayrıntıları dikey penceresi hata ile](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Hata ayıklamak için PowerShell 'i kullanma
1. **PowerShell**’i başlatın.
2. Dilimleri ve bunların durumlarını görmek için **Get-AzDataFactorySlice** komutunu çalıştırın. Durumu **başarısız**olan bir dilim görmeniz gerekir.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Örneğin:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   **StartDateTime** değerini işlem hattınızı başlangıç zamanına göre değiştirin. 
3. Şimdi, dilim için çalıştırılan etkinlik hakkındaki ayrıntıları almak için **Get-AzDataFactoryRun** cmdlet 'ini çalıştırın.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Örneğin:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    StartDateTime değeri, önceki adımdan not ettiğiniz hata/sorun diliminin başlangıç saati olur. Tarih-saat, çift tırnak içine alınmalıdır.
4. Aşağıdakine benzer hatalarla ilgili ayrıntıları içeren çıktıyı görmeniz gerekir:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. **Save-AzDataFactoryLog** cmdlet 'ini, çıkışta gördüğünüz kimlik değeriyle çalıştırabilir ve cmdlet için **-downloadlogsoption** kullanarak günlük dosyalarını indirebilirsiniz.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>İşlem hattında yeniden çalıştırma sorunları

> [!IMPORTANT]
> Hataları gidermek ve Izleme & yönetimi uygulamasını kullanarak başarısız dilimleri yeniden çalıştırmak daha kolay. Uygulamayı kullanma hakkında ayrıntılı bilgi için bkz. [izleme ve yönetim uygulamasını kullanarak Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Azure portalı kullanma
Bir işlem hattındaki hata giderme ve hata ayıklama gerçekleştirdikten sonra, hata dilimine giderek ve Komut çubuğundaki **Çalıştır** düğmesine tıklayarak hataları yeniden çalıştırabilirsiniz.

![Başarısız olan dilimi yeniden çalıştır](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Bir ilke hatası nedeniyle dilimin doğrulanmasına başarısız olması durumunda (örneğin, veriler yoksa), komut çubuğunda **Doğrula** düğmesine tıklayarak hatayı düzelmez ve yeniden doğrulayabilirsiniz.

![Hataları düzeltin ve doğrulayın](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell’i kullanma
**Set-AzDataFactorySliceStatus** cmdlet 'ini kullanarak başarısızlıklarını yeniden çalıştırabilirsiniz. Sözdizimi ve cmdlet ile ilgili diğer ayrıntılar için [set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) konusuna bakın.

**Örnek:**

Aşağıdaki örnek, ' Luwikiaggregdukdata ' tablosunun tüm dilimlerinin durumunu Azure Data Factory ' WikiADF ' içinde ' bekliyor ' olarak ayarlar.

' UpdateType ', ' UpstreamInPipeline ' olarak ayarlanır, bu da tablo ve tüm bağımlı (yukarı akış) tablolarının her bir diliminin durumlarının ' bekliyor ' olarak ayarlandığı anlamına gelir. Bu parametre için olası diğer değer ' bireysel '.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Azure portal uyarı oluşturma

1.  Azure portal oturum açın ve uyarılar sayfasını açmak için **izleyici > uyarılar** ' ı seçin.

    ![Uyarılar sayfasını açın.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Yeni bir uyarı oluşturmak için **+ Yeni uyarı kuralı** ' nı seçin.

    ![Yeni bir uyarı oluştur](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  **Uyarı koşulunu**tanımlayın. ( **Kaynak türüne göre filtrele** alanında **veri fabrikaları** ' nı seçtiğinizden emin olun.) Ayrıca, **Boyutlar**için değerler de belirtebilirsiniz.

    ![Uyarı koşulunu tanımlayın-hedef seçin](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Uyarı koşulunu tanımlayın-uyarı ölçütü ekleyin](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Uyarı koşulunu tanımlayın-uyarı mantığı ekleyin](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  **Uyarı ayrıntılarını**tanımlayın.

    ![Uyarı ayrıntılarını tanımlayın](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  **Eylem grubunu**tanımlayın.

    ![Eylem grubunu tanımlayın-Yeni bir eylem grubu oluşturun](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Eylem grup kümesi özelliklerini tanımlayın](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Eylem grubunu tanımlayın-oluşturulan yeni eylem grubu](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Bir veri fabrikasını farklı bir kaynak grubuna veya aboneliğe taşıma
Veri fabrikanızın giriş sayfasındaki komut çubuğunu **Taşı** düğmesini kullanarak, bir veri fabrikasını farklı bir kaynak grubuna veya farklı bir aboneliğe taşıyabilirsiniz.

![Veri fabrikasını taşıma](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Ayrıca, veri fabrikasının yanı sıra ilgili kaynakları da (Data Factory ile ilişkili uyarılar gibi) taşıyabilirsiniz.

![Kaynakları Taşı iletişim kutusu](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
