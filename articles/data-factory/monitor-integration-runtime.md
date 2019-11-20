---
title: Azure Data Factory tümleştirme çalışma zamanını izleme
description: Azure Data Factory farklı tümleştirme çalışma zamanı türlerini nasıl izleyeceğinizi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 874483540b8c92bbb8a6e37101191f5c867b76f1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684660"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Azure Data Factory tümleştirme çalışma zamanını izleme  
**Integration Runtime** , farklı ağ ortamlarında çeşitli veri tümleştirme özellikleri sağlamak için Azure Data Factory tarafından kullanılan işlem altyapısıdır. Data Factory tarafından sunulan üç tür tümleştirme çalışma zamanı vardır:

- Azure tümleştirme çalışma zamanı
- Kendinden konak tümleştirme çalışma zamanı
- Azure SSIS tümleştirmesi çalışma zamanı

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tümleştirme çalışma zamanı (IR) örneğinin durumunu almak için aşağıdaki PowerShell komutunu çalıştırın: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdlet 'i farklı tümleştirme çalışma zamanı türleri için farklı bilgiler döndürür. Bu makalede, her bir tümleştirme çalışma zamanı türünün özellikleri ve durumları açıklanmaktadır.  

## <a name="azure-integration-runtime"></a>Azure tümleştirme çalışma zamanı
Azure tümleştirme çalışma zamanı için işlem kaynağı, Azure 'da tam olarak yönetilen esnek. Aşağıdaki tabloda **Get-AzDataFactoryV2IntegrationRuntime** komutu tarafından döndürülen özellikler için açıklamalar verilmiştir:

### <a name="properties"></a>Özellikler
Aşağıdaki tabloda, bir Azure tümleştirme çalışma zamanı için cmdlet tarafından döndürülen özelliklerin açıklamaları verilmiştir:

| Özellik | Açıklama |
-------- | ------------- | 
| Ad | Azure tümleştirme çalışma zamanının adı. |  
| Durum | Azure tümleştirme çalışma zamanının durumu. | 
| Konum | Azure tümleştirme çalışma zamanının konumu. Bir Azure tümleştirme çalışma zamanının konumu hakkındaki ayrıntılar için bkz. [Integration Runtime 'A giriş](concepts-integration-runtime.md). |
| DataFactoryName | Azure tümleştirme çalışma zamanının ait olduğu veri fabrikasının adı. | 
| ResourceGroupName | Data Factory 'nin ait olduğu kaynak grubunun adı.  |
| Açıklama | Tümleştirme çalışma zamanının açıklaması.  |

### <a name="status"></a>Durum
Aşağıdaki tabloda bir Azure tümleştirme çalışma zamanının olası durumları verilmiştir:

| Durum | Açıklamalar/senaryolar | 
| ------ | ------------------ |
| Çevrimiçi | Azure tümleştirme çalışma zamanı çevrimiçi ve kullanılabilir hale gelmiştir. | 
| Offline | Azure Integration Runtime bir iç hata nedeniyle çevrimdışı. |

## <a name="self-hosted-integration-runtime"></a>Kendinden konak tümleştirme çalışma zamanı
Bu bölüm Get-AzDataFactoryV2IntegrationRuntime cmdlet 'i tarafından döndürülen özelliklerle ilgili açıklamalar sağlar. 

> [!NOTE] 
> Döndürülen özellikler ve durum, genel olarak barındırılan tümleştirme çalışma zamanı ve çalışma zamanındaki her düğüm hakkında bilgi içerir.  

### <a name="properties"></a>Özellikler

Aşağıdaki tabloda **her düğüm**Için izleme özelliklerinin açıklamaları verilmiştir:

| Özellik | Açıklama | 
| -------- | ----------- | 
| Ad | Şirket içinde barındırılan tümleştirme çalışma zamanının ve onunla ilişkili düğümlerin adı. Düğüm, şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu şirket içi bir Windows makinedir. |  
| Durum | Genel olarak barındırılan tümleştirme çalışma zamanının ve her düğümün durumu. Örnek: çevrimiçi/çevrimdışı/sınırlı/vb. Bu durumlar hakkında daha fazla bilgi için sonraki bölüme bakın. | 
| Sürüm | Şirket içinde barındırılan tümleştirme çalışma zamanının ve her düğümün sürümü. Şirket içinde barındırılan tümleştirme çalışma zamanının sürümü, gruptaki düğümlerin çoğunluğu sürümüne göre belirlenir. Şirket içinde barındırılan tümleştirme çalışma zamanı kurulumunda farklı sürümlere sahip düğümler varsa, yalnızca mantıksal şirket içinde barındırılan tümleştirme çalışma zamanı ile aynı sürüm numarasına sahip düğümler düzgün şekilde çalışır. Diğerleri sınırlı moddadır ve el ile güncelleştirilmesi gerekir (yalnızca büyük/küçük harfe otomatik güncelleştirme başarısız olur). | 
| Available memory | Şirket içinde barındırılan Integration runtime düğümü kullanılabilir bellek. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. | 
| CPU utilization | Şirket içinde barındırılan tümleştirme çalışma zamanı düğümünün CPU kullanımı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. |
| Networking (In/Out) | Şirket içinde barındırılan tümleştirme çalışma zamanı düğümünün ağ kullanımı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. | 
| Concurrent Jobs (Running/ Limit) | **Çalışıyor**. Her düğümde çalışan iş veya görev sayısı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. <br/><br/>**Sınır**. Limit her düğüm için en fazla eş zamanlı işi belirtir. Bu değer makine boyutuna göre tanımlanır. CPU, bellek veya ağ kullanımda olsa bile etkinlikler zaman aşımına uğradıkça, Gelişmiş senaryolarda eşzamanlı iş yürütülmesini ölçeklendirmeye yönelik sınırı artırabilirsiniz. Bu yetenek, tek düğümlü bir şirket içinde barındırılan tümleştirme çalışma zamanı ile de kullanılabilir. |
| Rol | Çok düğümlü bir şirket içinde barındırılan tümleştirme çalışma zamanı – dağıtıcı ve çalışan içinde iki tür rol vardır. Tüm düğümler çalışanlardır, yani işleri yürütmek için hepsi kullanılabilirler. Bulut hizmetlerinden görevleri/işleri çekmek ve bunları farklı çalışan düğümlerine göndermek için kullanılan yalnızca bir dağıtıcı düğümü vardır. Dağıtıcı düğümü de bir çalışan düğümüdür. |

Özelliklerin bazı ayarları, şirket içinde barındırılan tümleştirme çalışma zamanı 'nda (yani, bir genişleme senaryosunda) iki veya daha fazla düğüm olduğunda daha anlamlı hale getirir.

#### <a name="concurrent-jobs-limit"></a>Eşzamanlı işlerin sınırı

Eşzamanlı işler sınırının varsayılan değeri makine boyutuna göre ayarlanır. Bu değeri hesaplamak için kullanılan faktörler, RAM miktarına ve makinenin CPU çekirdekleri sayısına bağlıdır. Bu nedenle, daha fazla çekirdek ve daha fazla bellek, eşzamanlı işlerin varsayılan sınırının daha yüksektir.

Düğüm sayısını artırarak ölçeklendirebilirsiniz. Düğüm sayısını artırdığınızda, eşzamanlı işler sınırı, tüm kullanılabilir düğümlerin eşzamanlı iş sınırı değerlerinin toplamıdır.  Örneğin, bir düğüm en fazla on iki eşzamanlı iş çalıştırmanızı sağlar, daha sonra üç benzer düğüm eklemek en fazla 48 eşzamanlı iş (yani 4 x 12) çalıştırmanızı sağlar. Yalnızca her bir düğümdeki varsayılan değerlerle kaynak kullanımını düşük bir şekilde gördüğünüzde, eşzamanlı iş sınırını artırmanız önerilir.

Azure portal hesaplanan varsayılan değeri geçersiz kılabilirsiniz. > Bağlantıları > tümleştirme çalışma zamanları > > düğümlerini Düzenle > düğüm başına eşzamanlı iş değerini değiştir ' i seçin. PowerShell [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) komutunu da kullanabilirsiniz.
  
### <a name="status-per-node"></a>Durum (düğüm başına)
Aşağıdaki tabloda, şirket içinde barındırılan tümleştirme çalışma zamanı düğümünün olası durumları verilmiştir:

| Durum | Açıklama |
| ------ | ------------------ | 
| Çevrimiçi | Düğüm Data Factory hizmetine bağlandı. |
| Offline | Düğüm çevrimdışı. |
| Yükseltmenin | Düğüm otomatik olarak güncelleştiriliyor. |
| Sınırlı | Bir bağlantı sorunu nedeniyle. HTTP bağlantı noktası 8050 sorunu, Service Bus bağlantı sorunu veya bir kimlik bilgisi eşitleme sorunuyla kaynaklanıyor olabilir. |
| Olmadan | Düğüm, diğer çoğunluk düğümlerin yapılandırmasından farklı bir yapılandırmadır. |

Düğüm, diğer düğümlere bağlanamıyorsa devre dışı olabilir.

### <a name="status-overall-self-hosted-integration-runtime"></a>Durum (genel olarak barındırılan tümleştirme çalışma zamanı)
Aşağıdaki tabloda, şirket içinde barındırılan tümleştirme çalışma zamanının olası durumları verilmiştir. Bu durum, çalışma zamanına ait tüm düğümlerin durumlarına bağlıdır. 

| Durum | Açıklama |
| ------ | ----------- | 
| Kayıt gerekiyor | Henüz şirket içinde barındırılan tümleştirme çalışma zamanına hiçbir düğüm kaydedilmemiş. |
| Çevrimiçi | Tüm düğümler çevrimiçi. |
| Offline | Çevrimiçi düğüm yok. |
| Sınırlı | Bu şirket içinde barındırılan tümleştirme çalışma zamanındaki düğümlerin hepsi sağlıklı durumda değil. Bu durum, bazı düğümlerin kapatılmış olabileceğini belirten bir uyarıdır. Bu durum, dağıtıcı/çalışan düğümündeki bir kimlik bilgisi eşitleme sorunundan kaynaklanıyor olabilir. |

Ayrıntılı şirket içinde barındırılan tümleştirme çalışma zamanı özelliklerini ve cmdlet 'inin yürütülmesi sırasında anlık görüntü değerlerini içeren JSON yükünü getirmek için **Get-AzDataFactoryV2IntegrationRuntimeMetric** cmdlet 'ini kullanın.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Örnek çıktı (Bu şirket içinde barındırılan tümleştirme çalışma zamanı ile ilişkili iki düğüm olduğunu varsayar):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS tümleştirmesi çalışma zamanı
Azure-SSIS Integration Runtime, SSIS paketlerinizi çalıştırmak için adanmış, tam olarak yönetilen bir Azure sanal makine (veya düğümleri) kümesidir. Azure Data Factory başka hiçbir etkinlik çalıştırmaz. Sağlandıktan sonra, özelliklerini sorgulayabilir ve genel/düğüme özel durumlarını izleyebilirsiniz.

### <a name="properties"></a>Özellikler

| Özellik/durum | Açıklama |
| --------------- | ----------- |
| CreateTime | Azure-SSIS tümleştirme çalışma zamanının oluşturulduğu UTC saati. |
| Düğümler | Azure-SSIS tümleştirme çalışma zamanının ayrılan/kullanılabilir düğümleri, düğüme özgü durumlar (başlangıç/kullanılabilir/geri dönüştürme/kullanılamaz) ve eylem yapılabilir hatalar ile. |
| Diğer hatalar | Azure-SSIS tümleştirme çalışma zamanı 'nda düğüme özgü olmayan işlem yapılabilir hatalar. |
| LastOperation | Azure-SSIS tümleştirme çalışma zamanmağınızda son başlatma/durdurma işleminin sonucu başarısız olursa, işlem yapılabilir hata (ler). |
| Durum | Azure-SSIS tümleştirme çalışma zamanının genel durumu (ilk/başlangıç/başlatma/durdurma/durdurma). |
| Konum | Azure-SSIS tümleştirme çalışma zamanının konumu. |
| NodeSize | Azure-SSIS tümleştirme çalışma zamanının her bir düğümünün boyutu. |
| NodeCount | Azure-SSIS tümleştirme çalışma zamanının düğüm sayısı. |
| MaxParallelExecutionsPerNode | Azure-SSIS tümleştirme çalışma zamanının düğüm başına paralel yürütmelerinin sayısı. |
| CatalogServerEndpoint | SSıSDB barındırmak için mevcut Azure SQL veritabanı/yönetilen örnek sunucunuzun uç noktası. |
| CatalogAdminUserName | Mevcut Azure SQL veritabanı/yönetilen örnek sunucunuzun Yönetici Kullanıcı adı. Data Factory hizmet bu bilgileri, sizin adınıza SSıSDB hazırlamak ve yönetmek için kullanır. |
| CatalogAdminPassword | Mevcut Azure SQL veritabanı/yönetilen örnek sunucunuzun yönetici parolası. |
| CatalogPricingTier | Mevcut Azure SQL veritabanı sunucunuz tarafından barındırılan SSSıSDB fiyatlandırma katmanı.  SSıSDB barındıran Azure SQL veritabanı yönetilen örneği için geçerli değildir. |
| Vnetıd | Azure-SSIS tümleştirme çalışma zamanının katılması için sanal ağ kaynak KIMLIĞI. |
| Alt ağ | Azure-SSIS tümleştirme çalışma zamanının katılması için alt ağ adı. |
| Kimlik | Azure-SSIS tümleştirme çalışma zamanının kaynak KIMLIĞI. |
| Tür | Azure-SSIS tümleştirme çalışma zamanının türü (yönetilen/kendiliğinden konak). |
| ResourceGroupName | Veri Fabrikanızın ve Azure-SSIS tümleştirme çalışma zamanının oluşturulduğu Azure Kaynak grubunuzun adı. |
| DataFactoryName | Azure Data Factory 'nizin adı. |
| Ad | Azure-SSIS tümleştirme çalışma zamanının adı. |
| Açıklama | Azure-SSIS tümleştirme çalışma zamanının açıklaması. |

  
### <a name="status-per-node"></a>Durum (düğüm başına)

| Durum | Açıklama |
| ------ | ----------- | 
| Başlatılıyor | Bu düğüm hazırlanıyor. |
| Kullanılabilir | Bu düğüm, SSIS paketlerini dağıtmanız/yürütmeniz için hazırlayın. |
| 2 | Bu düğüm onarılıyor/yeniden başlatılıyor. |
| Kullanılamaz | Bu düğüm, SSIS paketlerini dağıtmanıza/yürütmeye yönelik değil ve giderebileceğiniz hata/sorunlar oluştu. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Durum (Genel Azure-SSIS tümleştirme çalışma zamanı)

| Genel durum | Açıklama | 
| -------------- | ----------- | 
| Başlatma | Azure-SSIS tümleştirme çalışma zamanının düğümleri ayrılmadı/hazırlandı. | 
| Başlatılıyor | Azure-SSIS tümleştirme çalışma zamanının düğümleri ayrılmakta/hazırlanmakta ve faturalandırma başladı. |
| başlama | Azure-SSIS tümleştirme çalışma zamanının düğümleri ayrıldı/hazırlandı ve SSIS paketlerini dağıtmanıza/yürütmeniz için hazır. |
| Durduruluyor  | Azure-SSIS tümleştirme çalışma zamanının düğümleri serbest bırakılıyor. |
| Durduruldu | Azure-SSIS tümleştirme çalışma zamanının düğümleri yayımlanmıştır ve faturalandırma durdurulur. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Azure portal Azure-SSIS tümleştirme çalışma zamanını izleme

Aşağıdaki ekran görüntüleri izlenecek Azure-SSIS IR seçme ve görüntülenen bilgilere bir örnek sağlama işlemlerinin nasıl yapılacağını gösterir.

![İzlenecek Azure-SSIS tümleştirme çalışma zamanını seçin](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Azure-SSIS tümleştirme çalışma zamanı hakkındaki bilgileri görüntüleme](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>PowerShell ile Azure-SSIS tümleştirme çalışma zamanını izleme

Azure-SSIS IR durumunu denetlemek için aşağıdaki örnekte olduğu gibi bir komut dosyası kullanın.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime hakkında daha fazla bilgi

Azure-SSIS Integration Runtime hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR dahil olmak üzere genel olarak tümleştirme çalışma zamanları hakkında kavramsal bilgiler verilmektedir. 
- [Öğretici: SSIS paketlerini Azure’a dağıtma](tutorial-create-azure-ssis-runtime-portal.md). Bu makale bir Azure-SSIS IR oluşturmaya ilişkin adım adım yönergeler sağlar ve SSIS kataloğunu barındırmak için bir Azure SQL veritabanı kullanır. 
- [Nasıl yapılır: Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticiye genişleterek Azure SQL veritabanı yönetilen örneği kullanımı ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar. 
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makale bir Azure-SSIS IR’yi durdurma, başlatma veya kaldırma işlemini gösterir. Ayrıca, IR’ye daha fazla düğüm ekleyerek Azure-SSIS IR’nizi ölçeklendirmeyi gösterir. 
- [Azure-SSIS IR’yi bir sanal ağa ekleyin](join-azure-ssis-integration-runtime-virtual-network.md). Bu makale Azure-SSIS IR’yi bir Azure sanal ağına ekleme hakkında kavramsal bilgiler sağlar. Ayrıca, Azure-SSIS IR sanal ağa katılabilmesi için sanal ağı yapılandırmak üzere Azure portal kullanma adımları da sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
İşlem hatlarını farklı yollarla izlemek için aşağıdaki makalelere bakın: 

- [Hızlı başlangıç: Veri Fabrikası oluşturma](quickstart-create-data-factory-dot-net.md).
- [Data Factory işlem hatlarını izlemek için Azure Izleyicisini kullanın](monitor-using-azure-monitor.md)
