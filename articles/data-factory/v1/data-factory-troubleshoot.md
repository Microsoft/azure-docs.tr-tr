---
title: Azure Data Factory sorunlarını giderme
description: Azure Data Factory kullanmayla ilgili sorunları nasıl giderebileceğinizi öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
robots: noindex
ms.openlocfilehash: 70c1aecd358b52a7b5b62ba7cb8f3badffef2998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779928"
---
# <a name="troubleshoot-data-factory-issues"></a>Data Factory'de sorun giderme
> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. 

Bu makalede Azure Data Factory kullanırken sorunlar için sorun giderme ipuçları sunulmaktadır. Bu makale, hizmeti kullanırken olası tüm sorunları listelemez, ancak bazı sorunlar ve genel sorun giderme ipuçları ele alınmaktadır.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Hata: Abonelik ‘Microsoft.DataFactory’ ad alanını kullanacak şekilde kaydedilmemiş
Bu hatayı aldıysanız Azure Data Factory kaynak sağlayıcısı makinenizde kayıtlı değildir. Şunları yapın:

1. Azure PowerShell’i çalıştırın.
2. Aşağıdaki komutu kullanarak Azure hesabınızda oturum açın.

   ```powershell
   Connect-AzAccount
   ```

3. Azure Data Factory sağlayıcısı 'nı kaydetmek için aşağıdaki komutu çalıştırın.

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
   ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Sorun: Data Factory cmdlet 'i çalıştırılırken yetkisiz hata
Azure PowerShell ile doğru Azure hesabını veya aboneliğini kullanmıyor olabilirsiniz. Azure PowerShell ile kullanılacak doğru Azure hesabını ve aboneliğini seçmek için aşağıdaki cmdlet’leri kullanın.

1. Connect-AzAccount-doğru kullanıcı KIMLIĞINI ve parolayı kullanın
2. Get-AzSubscription-hesaba ait tüm abonelikleri görüntüleyin.
3. Select-AzSubscription &lt; abonelik adı &gt; -doğru aboneliği seçin. Azure portal bir veri fabrikası oluşturmak için kullandığınız aynısını kullanın.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Sorun: Azure portal Veri Yönetimi ağ geçidi hızlı kurulumu başlatılamadı
Veri Yönetimi Ağ Geçidi Hızlı Kurulumu için Internet Explorer veya Microsoft ClickOnce uyumlu bir web tarayıcısı gerekir. Hızlı Kurulum başlatılamıyorsa, aşağıdakilerden birini yapın:

* Internet Explorer veya Microsoft ClickOnce uyumlu bir Web tarayıcısı kullanın.

    Chrome kullanıyorsanız [Chrome Web deposuna](https://chrome.google.com/webstore/)gidin, "ClickOnce" anahtar sözcüğü ile arama yapın, ClickOnce uzantılarından birini seçin ve yükleyebilirsiniz.

    Firefox için aynısını yapın (eklenti yüklemesi). Araç çubuğundaki Menüyü Aç düğmesine tıklayın (sağ üst köşede yer alan üç yatay çizgi), Eklentiler’e tıklayın, “ClickOnce” araması yapın, ClickOnce uzantılarından birini seçin ve yükleyin.
* Portalda aynı dikey pencerede gösterilen **El Ile kurulum** bağlantısını kullanın. Yükleme dosyasını indirmek ve el ile çalıştırmak için bu yaklaşımı kullanın. Yükleme başarılı olduktan sonra Veri Yönetimi ağ geçidi yapılandırması iletişim kutusunu görürsünüz. Portal ekranındaki **anahtarı** kopyalayın ve ağ geçidini hizmete elle kaydetmek için yapılandırma yöneticisi içinde kullanın.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Sorun: SQL Server bağlantı başarısız oldu
Ağ Geçidi makinesinde **veri yönetimi ağ geçidi Configuration Manager** başlatın ve ağ geçidi makinesinden SQL Server bağlantısını test etmek Için **sorun giderme** sekmesini kullanın. Bağlantı/ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Sorun: giriş dilimleri sonsuza kadar bekliyor
Dilimler çeşitli nedenlerden dolayı **bekleme** durumunda olabilir. Yaygın nedenlerinden biri, **External** özelliğinin **true** olarak ayarlanmamalıdır. Azure Data Factory kapsamı dışında üretilen tüm veri kümeleri **External** özelliği ile işaretlenmelidir. Bu özellik, verilerin dış olduğunu ve Veri Fabrikası içindeki herhangi bir işlem hattı tarafından yedeklenmedi olduğunu gösterir. İlgili depoda veriler kullanılabilir duruma geldiğinde veri dilimleri **Hazır** olarak işaretlenir.

**External** özelliğinin kullanımı ile ilgili olarak aşağıdaki örneğe bakın. External ' i true olarak belirlediğinizde, isteğe bağlı olarak **Externaldata*** belirtebilirsiniz.

Bu özellikle ilgili daha ayrıntılı bilgiler için [Veri kümeleri](data-factory-create-datasets.md) makalesine bakın.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Hatayı gidermek için girdi tablosunun JSON tanımına **external** özelliğini ve isteğe bağlı **externalData** bölümünü ekleyin ve tabloyu yeniden oluşturun.

### <a name="problem-hybrid-copy-operation-fails"></a>Sorun: karma kopyalama işlemi başarısız oluyor
Veri Yönetimi ağ geçidini kullanarak şirket içi veri deposuna/üzerinden kopyalama ile ilgili sorunları gidermeye yönelik adımlar için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) adımları.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Sorun: isteğe bağlı HDInsight sağlama başarısız oluyor
HDInsightOnDemand türünde bağlantılı bir hizmet kullanırken bir Azure Blob Depolama’ya işaret eden linkedServiceName belirlemeniz gerekir. Data Factory hizmeti isteğe bağlı HDInsight kümeniz için günlükleri ve destekleyici dosyaları depolamak için bu depoyu kullanır.  Bazen aşağıdaki hatayla isteğe bağlı bir HDInsight kümesinin sağlanması başarısız olur:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Bu hata genellikle linkedServiceName içinde belirtilen depolama hesabı konumunun HDInsight sağlama işleminin gerçekleştiği veri merkezi konumu ile aynı olmadığını gösterir. Örnek: veri fabrikanızın Batı ABD ve Azure depolama Doğu ABD ise, isteğe bağlı sağlama Batı ABD başarısız olur.

Buna ek olarak, isteğe bağlı HDInsight içinde ek depolama hesaplarının belirlenebileceği ikinci bir JSON özelliği (additionalLinkedServiceNames) bulunmaktadır. Bu ek bağlı depolama hesapları, HDInsight kümesiyle aynı konumda olmalıdır veya aynı hata ile başarısız olur.

### <a name="problem-custom-net-activity-fails"></a>Sorun: özel .NET etkinliği başarısız oluyor
Ayrıntılı adımlar için bkz. [özel etkinlikle bir işlem hattının hatalarını ayıklama](data-factory-use-custom-activities.md#troubleshoot-failures) .

## <a name="use-azure-portal-to-troubleshoot"></a>Sorun gidermek için Azure portal kullanın
### <a name="using-portal-blades"></a>Portal Blade kullanma
Adımlar için bkz. [izleme işlem hattı](data-factory-monitor-manage-pipelines.md) .

### <a name="using-monitor-and-manage-app"></a>İzleme ve Yönetme Uygulamasını kullanma
Ayrıntılar için bkz. [izleme ve yönetme uygulamasını kullanarak Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) .

## <a name="use-azure-powershell-to-troubleshoot"></a>Sorun gidermek için Azure PowerShell kullanın
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Hata gidermek için Azure PowerShell kullanma
Ayrıntılar için [Azure PowerShell kullanarak izleme Data Factory işlem hatlarını](data-factory-monitor-manage-pipelines.md) inceleyin.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png