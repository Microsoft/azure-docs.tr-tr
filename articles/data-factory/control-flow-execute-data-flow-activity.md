---
title: Azure Data Factory 'de veri akışı etkinliği
description: Data Factory işlem hattının içinden veri akışları yürütme.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: 5623907346ee3882ad53a27695336ba4bc449db8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679940"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure Data Factory 'de veri akışı etkinliği

Veri akışı etkinliğini, veri akışları eşleme yoluyla dönüştürmek ve taşımak için kullanın. Veri akışlarınız için yeni başladıysanız bkz. [eşleme veri akışına genel bakış](concepts-data-flow-overview.md)

## <a name="syntax"></a>Sözdizimi

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
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
veri akışı | Yürütülen veri akışının başvurusu | DataFlowReference | Evet
ıntegrationruntime | Veri akışının çalıştığı işlem ortamı | IntegrationRuntimeReference | Evet
hazırlama. linkedService | Bir SQL DW kaynağı veya havuzu kullanıyorsanız, PolyBase hazırlama için kullanılan depolama hesabı | LinkedServiceReference | Yalnızca veri akışı bir SQL DW 'yi okuduğunda veya yazıyorsa
hazırlama. folderPath | Bir SQL DW kaynağı veya havuzu kullanıyorsanız, PolyBase hazırlama için kullanılan BLOB depolama hesabındaki klasör yolu | Dize | Yalnızca veri akışı bir SQL DW 'yi okuduğunda veya yazıyorsa

![Veri akışı yürütme](media/data-flow/activity-data-flow.png "Veri akışı yürütme")

### <a name="data-flow-integration-runtime"></a>Veri akışı tümleştirme çalışma zamanı

Veri akışı etkinliği yürütmesinde kullanılacak Integration Runtime seçin. Data Factory, varsayılan olarak, Azure tümleştirme çalışma zamanını dört çalışan çekirdekle ve yaşam süresi (TTL) ile birlikte kullanır. Bu IR genel amaçlı bir işlem türüne sahiptir ve fabrikanızın bulunduğu bölgede çalışır. Veri akışı etkinlik yürütmenizi için belirli bölgeleri, işlem türünü, çekirdek sayılarını ve TTL 'yi tanımlayan kendi Azure tümleştirme çalışma zamanlarını oluşturabilirsiniz.

İşlem hattı yürütmeleri için küme, yürütme başlamadan önce birkaç dakika süren bir iş kümesidir. TTL belirtilmemişse, bu başlangıç saati her işlem hattı çalıştırmasında gereklidir. Bir TTL belirtirseniz, son yürütmeden sonra belirtilen süre için bir sıcak küme havuzu etkin kalır ve daha kısa başlangıç süreleri elde edilir. Örneğin, 60 dakikalık bir TTL 'SI varsa ve bir veri akışını saatte bir kez çalıştırırsanız, küme havuzu etkin kalır. Daha fazla bilgi için bkz. [Azure tümleştirme çalışma zamanı](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> Veri akışı etkinliğinde Integration Runtime seçimi yalnızca işlem hattınızdaki *tetiklenmiş yürütmeler* için geçerlidir. Veri akışları ile işlem hattınızda hata ayıklama, hata ayıklama oturumunda belirtilen kümede çalışır.

### <a name="polybase"></a>PolyBase

Bir Azure SQL veri ambarını havuz veya kaynak olarak kullanıyorsanız, PolyBase Batch yüklemeniz için bir hazırlama konumu seçmeniz gerekir. PolyBase, verileri satır satır olarak yüklemek yerine toplu olarak yüklemeye izin verir. PolyBase, yükleme süresini büyük ölçüde SQL DW 'ye düşürür.

## <a name="parameterizing-data-flows"></a>Veri akışlarını parametrize etme

### <a name="parameterized-datasets"></a>Parametreli veri kümeleri

Veri akışınız parametreli veri kümeleri kullanıyorsa, **Ayarlar** sekmesinde parametre değerlerini ayarlayın.

![Veri akışı parametrelerini yürütme](media/data-flow/params.png "Parametreler")

### <a name="parameterized-data-flows"></a>Parametreli veri akışları

Veri akışınız parametreleştirilmiş ise, **Parametreler** sekmesindeki veri akışı parametrelerinin dinamik değerlerini ayarlayın. Dinamik veya değişmez değer parametre değerlerini atamak için ADF işlem hattı ifade dilini (yalnızca dize türleri için) veya veri akışı ifade dilini kullanabilirsiniz. Daha fazla bilgi için bkz. [veri akışı parametreleri](parameters-data-flow.md).

![Veri akışı parametre örneğini Yürüt](media/data-flow/parameter-example.png "Parametre örneği")

## <a name="pipeline-debug-of-data-flow-activity"></a>Veri akışı etkinliğinin işlem hattı hata ayıklaması

Bir veri akışı etkinliğiyle bir hata ayıklama işlem hattı çalıştırması yürütmek için, üst çubuktaki **veri akışı hata ayıklama** kaydırıcısının üzerinden veri akışı hata ayıklama moduna geçmeniz gerekir. Hata ayıklama modu, veri akışını etkin bir Spark kümesine karşı çalıştırmanızı sağlar. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

![Hata ayıklama düğmesi](media/data-flow/debugbutton.png "Hata ayıklama düğmesi")

Hata ayıklama ardışık düzeni, veri akışı etkinlik ayarlarında belirtilen tümleştirme çalışma zamanı ortamı değil, etkin hata ayıklama kümesine karşı çalışır. Hata ayıklama modunu başlatırken işlem ortamını hata ayıkla seçeneğini belirleyebilirsiniz.

## <a name="monitoring-the-data-flow-activity"></a>Veri akışı etkinliğini izleme

Veri akışı etkinliğinin bölümlemeyi, aşama süresini ve veri kökenini bilgilerini görüntüleyebileceğiniz özel bir izleme deneyimi vardır. **Eylemler**altında, gözlük simgesi aracılığıyla izleme bölmesini açın. Daha fazla bilgi için bkz. [veri akışlarını izleme](concepts-data-flow-monitoring.md).

## <a name="next-steps"></a>Sonraki adımlar

Bkz. Data Factory tarafından desteklenen denetim akışı etkinlikleri: 

- [If Koşulu Etkinliği](control-flow-if-condition-activity.md)
- [İşlem Hattı Yürütme Etkinliği](control-flow-execute-pipeline-activity.md)
- [Her etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web etkinliği](control-flow-web-activity.md)
- [Bitiş Etkinliği](control-flow-until-activity.md)
