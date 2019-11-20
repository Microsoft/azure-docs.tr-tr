---
title: Azure Data Factory 'de Azure tümleştirme çalışma zamanı oluşturma
description: Azure Data Factory ' de Azure tümleştirme çalışma zamanı oluşturmayı, verileri kopyalamak ve dönüştürme etkinliklerini göndermek için kullanılan hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 898290f70777ca442bb8885d83064231c5486a7c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677256"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure Integration Runtime oluşturma ve yapılandırma
Integration Runtime (IR), farklı ağ ortamları genelinde veri tümleştirme özellikleri sağlamak için Azure Data Factory tarafından kullanılan işlem altyapısıdır. IR hakkında daha fazla bilgi için bkz. [Integration Runtime](concepts-integration-runtime.md).

Azure IR, veri hareketini yerel olarak gerçekleştirmek ve HDInsight gibi işlem hizmetlerine veri dönüştürme etkinliklerini göndermek için tam olarak yönetilen bir işlem sağlar. Azure ortamında barındırılır ve genel kullanıma açık uç noktalarla ortak ağ ortamındaki kaynaklara bağlanmayı destekler.

Bu belgede Azure Integration Runtime nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanır. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Varsayılan Azure IR
Varsayılan olarak, her veri fabrikasının, bulut veri depoları ve işlem hizmetleri ortak ağda işlemleri destekleyen arka uçta Azure IR vardır. Azure IR konumu otomatik olarak çözümlenir. **Connectvia** özelliği bağlantılı hizmet tanımında belirtilmemişse, varsayılan Azure IR kullanılır. Yalnızca IR konumunu açıkça tanımlamak istediğinizde veya yönetim amacıyla farklı IRS üzerinde etkinlik yürütmelerini neredeyse gruplamak istediğinizde, açıkça bir Azure IR oluşturmanız gerekir. 

## <a name="create-azure-ir"></a>Azure IR oluştur
Integration Runtime, **set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet 'i kullanılarak oluşturulabilir. Bir Azure IR oluşturmak için, komutun adını, konumunu ve türünü belirtirsiniz. Konum "Batı Avrupa" olarak ayarlanmış bir Azure IR oluşturmak için örnek bir komut aşağıda verilmiştir:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR için türün **yönetilen**olarak ayarlanması gerekir. Bulutta tam olarak yönetilen bir esnek olduğundan işlem ayrıntılarını belirtmeniz gerekmez. Azure-SSIS IR oluşturmak istediğinizde düğüm boyutu ve düğüm sayısı gibi işlem ayrıntılarını belirtin. Daha fazla bilgi için [Azure-SSIS IR oluşturma ve yapılandırma](create-azure-ssis-integration-runtime.md)konusuna bakın.

Set-AzDataFactoryV2IntegrationRuntime PowerShell cmdlet 'ini kullanarak konumunu değiştirmek için mevcut bir Azure IR yapılandırabilirsiniz. Azure IR konumu hakkında daha fazla bilgi için bkz. [Integration Runtime 'A giriş](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Azure IR kullan

Azure IR oluşturulduktan sonra, bağlantılı hizmet tanımınızda buna başvurabilirsiniz. Aşağıda, Azure depolama bağlı hizmetinden yukarıda oluşturulan Azure Integration Runtime nasıl başvurulacağını gösteren bir örnek verilmiştir:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Sonraki adımlar
Diğer tümleştirme çalışma zamanları türlerini oluşturma hakkında aşağıdaki makalelere bakın:

- [Kendinden konak tümleştirme çalışma zamanı oluşturma](create-self-hosted-integration-runtime.md)
- [Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md)
 
