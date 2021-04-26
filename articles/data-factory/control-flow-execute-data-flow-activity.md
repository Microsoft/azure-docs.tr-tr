---
title: Veri akışı etkinliği
description: Data Factory işlem hattının içinden veri akışları yürütme.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/16/2021
ms.openlocfilehash: da8d193d140d96d9742666429ebc85672c71ad4e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567273"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure Data Factory 'de veri akışı etkinliği

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Veri akışı etkinliğini, veri akışları eşleme yoluyla dönüştürmek ve taşımak için kullanın. Veri akışlarınız için yeni başladıysanız bkz. [eşleme veri akışına genel bakış](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
veri akışı | Yürütülen veri akışının başvurusu | DataFlowReference | Yes
ıntegrationruntime | Veri akışının çalıştığı işlem ortamı. Belirtilmemişse, Otomatik Çözümle Azure tümleştirme çalışma zamanı kullanılacaktır. | IntegrationRuntimeReference | No
compute. coreCount | Spark kümesinde kullanılan çekirdek sayısı. Yalnızca Azure tümleştirme çalışma zamanı otomatik çözümle kullanılıyorsa belirtilebilir | 8, 16, 32, 48, 80, 144, 272 | No
compute. computeType | Spark kümesinde kullanılan işlem türü. Yalnızca Azure tümleştirme çalışma zamanı otomatik çözümle kullanılıyorsa belirtilebilir | "Genel", "ComputeOptimized", "Memoryoptimlanmış" | No
hazırlama. linkedService | Azure SYNAPSE Analytics kaynağı veya havuzu kullanıyorsanız, PolyBase hazırlama için kullanılan depolama hesabını belirtin.<br/><br/>Azure depolama alanı VNet hizmet uç noktası ile yapılandırıldıysa, depolama hesabında "Güvenilen Microsoft hizmeti 'ne izin ver" özelliği etkinleştirilmiş olarak yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. [Azure depolama Ile VNET hizmet uç noktaları kullanmanın etkileri](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Ayrıca, [Azure Blob](connector-azure-blob-storage.md#managed-identity) için gerekli konfigürasyonları ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#managed-identity) de öğrenin.<br/> | LinkedServiceReference | Yalnızca veri akışı bir Azure SYNAPSE analizlerini okuduğunda veya yazıyorsa
hazırlama. folderPath | Azure SYNAPSE Analytics kaynağı veya havuzu kullanıyorsanız, PolyBase hazırlama için kullanılan BLOB depolama hesabındaki klasör yolu | Dize | Yalnızca veri akışı Azure SYNAPSE Analytics 'i okuduğunda veya yazıyorsa
traceLevel | Veri akışı etkinlik yürütmesinin günlüğe kaydetme düzeyini ayarlama | İnce, kalın, hiçbiri | No

![Veri akışı yürütme](media/data-flow/activity-data-flow.png "Veri akışı yürütme")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Çalışma zamanında veri akışı işlem boyutunu dinamik olarak boyutlandır

Çekirdek sayısı ve Işlem türü özellikleri, çalışma zamanında gelen kaynak verilerinizin boyutuna ayarlanacak şekilde dinamik olarak ayarlanabilir. Kaynak veri kümesi verilerinin boyutunu bulmak için arama veya meta veri al gibi işlem hattı etkinliklerini kullanın. Ardından, veri akışı etkinlik özelliklerinde dinamik Içerik Ekle ' yi kullanın.

![Dinamik veri akışı](media/data-flow/dyna1.png "Dinamik veri akışı")

[Bu tekniği açıklayan kısa bir video öğreticisi aşağıda verilmiştir](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Veri akışı tümleştirme çalışma zamanı

Veri akışı etkinliği yürütmesinde kullanılacak Integration Runtime seçin. Data Factory, varsayılan olarak Azure tümleştirme çalışma zamanını dört çalışan çekirdeklerle Otomatik Çözümle ' yi kullanır. Bu IR genel amaçlı bir işlem türüne sahiptir ve fabrikanızın bulunduğu bölgede çalışır. Yürütülen işlem hatları için, veri akışı etkinlik yürütmenize yönelik belirli bölgeleri, işlem türünü, çekirdek sayılarını ve TTL 'yi tanımlayan kendi Azure tümleştirme çalışma zamanlarını oluşturmanız önemle tavsiye edilir.

En az bir Genel Amaçlı işlem türü (büyük iş yükleri için işlem iyileştirildi) 8 + 8 (16 Toplam v-çekirdek) yapılandırmasıyla ve 10 dakikalık en düşük düzeyde üretim iş yükleri için en düşük öneriden oluşur. Küçük bir TTL ayarlayarak Azure IR, soğuk bir küme için birkaç dakikalık başlangıç zamanı olmayacak bir sıcak küme tutabilir. Azure IR veri akışı yapılandırmalarında "hızlı yeniden kullan" seçeneğini belirleyerek, veri akışlarınızın yürütülmesini daha da hızlandırabilirsiniz. Daha fazla bilgi için bkz. [Azure tümleştirme çalışma zamanı](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Veri akışı etkinliğinde Integration Runtime seçimi yalnızca işlem hattınızdaki *tetiklenmiş yürütmeler* için geçerlidir. Veri akışları ile işlem hattınızda hata ayıklama, hata ayıklama oturumunda belirtilen kümede çalışır.

### <a name="polybase"></a>PolyBase

Bir Azure SYNAPSE analizinizi havuz veya kaynak olarak kullanıyorsanız, PolyBase Batch yüklemeniz için bir hazırlama konumu seçmeniz gerekir. PolyBase, verileri satır satır olarak yüklemek yerine toplu olarak yüklemeye izin verir. PolyBase, yükleme süresini büyük ölçüde Azure SYNAPSE Analytics 'e düşürür.

## <a name="logging-level"></a>Günlük kaydı düzeyi

Veri akışı etkinliklerinizin her işlem hattı yürütmesinin tüm ayrıntılı telemetri günlüklerini tam olarak günlüğe yazmasına gerek yoksa, isteğe bağlı olarak günlük düzeyini "temel" veya "hiçbiri" olarak ayarlayabilirsiniz. Veri akışlarınızı "ayrıntılı" modda yürütürken (varsayılan), veri dönüşümünüzün her bir bölüm düzeyinde, ADF 'yi tam olarak günlüğe kaydetmek için istek yapabilirsiniz. Bu, pahalı bir işlem olabilir, bu nedenle yalnızca sorun giderme sırasında ayrıntılandırma, genel veri akışınızı ve işlem hattı performansınızı iyileştirecek şekilde etkinleştiriliyor. "Temel" modu yalnızca bir süre Özeti sağlamaları durumunda yalnızca dönüştürme sürelerini günlüğe kaydeder.

![Günlük kaydı düzeyi](media/data-flow/logging.png "Günlüğe kaydetme düzeyini ayarla")

## <a name="sink-properties"></a>Havuz özellikleri

Veri akışlardaki gruplandırma özelliği, her ikisinin de aynı grup numarasını kullanarak gruplarınızın yürütülme sırasını ve grup havuzlarını ayarlamanıza olanak sağlar. Grupları yönetmeye yardımcı olmak için, ADF 'nin aynı grupta, paralel olarak havuz çalıştırmasını isteyebilirsiniz. Ayrıca, havuzdan biri bir hatayla karşılaştığında bile havuz grubunu devam edecek şekilde ayarlayabilirsiniz.

Veri akışı havuzlarının varsayılan davranışı, her bir havuzun ardışık olarak, seri bir biçimde yürütülmesi ve havuzda bir hata ile karşılaşıldığında veri akışının başarısız olması olabilir. Ayrıca, veri akışı özelliklerine gitmediğiniz ve havuzlar için farklı öncelikler ayarlamadığınız müddetçe Tüm havuzlar aynı gruba varsayılan olarak ayarlanır.

![Havuz özellikleri](media/data-flow/sink-properties.png "Havuz özelliklerini ayarla")

## <a name="parameterizing-data-flows"></a>Veri akışlarını parametrize etme

### <a name="parameterized-datasets"></a>Parametreli veri kümeleri

Veri akışınız parametreli veri kümeleri kullanıyorsa, **Ayarlar** sekmesinde parametre değerlerini ayarlayın.

![Veri akışı parametrelerini yürütme](media/data-flow/params.png "Parametreler")

### <a name="parameterized-data-flows"></a>Parametreli veri akışları

Veri akışınız parametreleştirilmiş ise, **Parametreler** sekmesindeki veri akışı parametrelerinin dinamik değerlerini ayarlayın. Dinamik veya değişmez değer parametre değerlerini atamak için ADF işlem hattı ifade dilini veya veri akışı ifade dilini kullanabilirsiniz. Daha fazla bilgi için bkz. [veri akışı parametreleri](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Parametreli işlem özellikleri.

Azure Integration Runtime 'ı otomatik çözümle ve COMPUTE. coreCount ve COMPUTE. computeType değerlerini belirttiğinizde çekirdek sayısı veya işlem türünü parametreleştirebilirsiniz.

![Veri akışı parametre örneğini Yürüt](media/data-flow/parameterize-compute.png "Parametre örneği")

## <a name="pipeline-debug-of-data-flow-activity"></a>Veri akışı etkinliğinin işlem hattı hata ayıklaması

Bir veri akışı etkinliğiyle bir hata ayıklama işlem hattı çalıştırması yürütmek için, üst çubuktaki **veri akışı hata ayıklama** kaydırıcısının üzerinden veri akışı hata ayıklama moduna geçmeniz gerekir. Hata ayıklama modu, veri akışını etkin bir Spark kümesine karşı çalıştırmanızı sağlar. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

![Hata ayıklama düğmesinin nerede olduğunu gösteren ekran görüntüsü](media/data-flow/debug-button-3.png)

Hata ayıklama ardışık düzeni, veri akışı etkinlik ayarlarında belirtilen tümleştirme çalışma zamanı ortamı değil, etkin hata ayıklama kümesine karşı çalışır. Hata ayıklama modunu başlatırken işlem ortamını hata ayıkla seçeneğini belirleyebilirsiniz.

## <a name="monitoring-the-data-flow-activity"></a>Veri akışı etkinliğini izleme

Veri akışı etkinliğinin bölümlemeyi, aşama süresini ve veri kökenini bilgilerini görüntüleyebileceğiniz özel bir izleme deneyimi vardır. **Eylemler** altında, gözlük simgesi aracılığıyla izleme bölmesini açın. Daha fazla bilgi için bkz. [veri akışlarını izleme](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Sonraki bir etkinliğin veri akışı etkinlik sonuçlarını kullanma

Veri akışı etkinliği, her bir kaynaktan okunan her bir havuza ve satıra yazılan satır sayısıyla ilgili ölçümleri çıktı olarak verir. Bu sonuçlar, `output` etkinlik çalıştırma sonucunun bölümünde döndürülür. Döndürülen ölçümler aşağıdaki JSON biçimindedir.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Örneğin, ' dataflowActivity ' adlı bir etkinlikte ' sink1 ' adlı bir havuza yazılan satır sayısına ulaşmak için kullanın `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Bu havuzda kullanılan ' source1 ' adlı bir kaynaktan okunan satır sayısını almak için kullanın `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Bir havuzda yazılmış sıfır satır varsa, ölçümler ' de gösterilmez. Varlık, işlevi kullanılarak doğrulanabilir `contains` . Örneğin, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` sink1 'e herhangi bir satırın yazılıp yazılmadığını kontrol eder.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. Data Factory tarafından desteklenen denetim akışı etkinlikleri: 

- [If Condition Etkinliği](control-flow-if-condition-activity.md)
- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [Her Bir Etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web Etkinliği](control-flow-web-activity.md)
- [Until Etkinliği](control-flow-until-activity.md)
