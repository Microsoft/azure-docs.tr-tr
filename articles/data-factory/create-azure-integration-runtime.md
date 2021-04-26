---
title: Azure Data Factory 'de Azure tümleştirme çalışma zamanı oluşturma
description: Azure Data Factory ' de Azure tümleştirme çalışma zamanı oluşturmayı, verileri kopyalamak ve dönüştürme etkinliklerini göndermek için kullanılan hakkında bilgi edinin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: ecf2a74a16234084fbac4d1c26157d1703b56a13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373071"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure Integration Runtime’ı oluşturma ve yapılandırma
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR), farklı ağ ortamları genelinde veri tümleştirme özellikleri sağlamak için Azure Data Factory tarafından kullanılan işlem altyapısıdır. IR hakkında daha fazla bilgi için bkz. [Integration Runtime](concepts-integration-runtime.md).

Azure IR, veri hareketini yerel olarak gerçekleştirmek ve HDInsight gibi işlem hizmetlerine veri dönüştürme etkinliklerini göndermek için tam olarak yönetilen bir işlem sağlar. Azure ortamında barındırılır ve genel kullanıma açık uç noktalarla ortak ağ ortamındaki kaynaklara bağlanmayı destekler.

Bu belgede Azure Integration Runtime nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanır. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Varsayılan Azure IR
Varsayılan olarak, her veri fabrikasının, bulut veri depoları ve işlem hizmetleri ortak ağda işlemleri destekleyen arka uçta Azure IR vardır. Azure IR konumu, oto Çözümle ' dir. **Connectvia** özelliği bağlantılı hizmet tanımında belirtilmemişse, varsayılan Azure IR kullanılır. Yalnızca IR konumunu açıkça tanımlamak istediğinizde veya yönetim amacıyla farklı IRS üzerinde etkinlik yürütmelerini neredeyse gruplamak istediğinizde, açıkça bir Azure IR oluşturmanız gerekir. 

## <a name="create-azure-ir"></a>Azure IR oluştur

Azure IR oluşturup ayarlamak için aşağıdaki yordamları kullanabilirsiniz.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Azure PowerShell aracılığıyla Azure IR oluşturma
Integration Runtime, **set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet 'i kullanılarak oluşturulabilir. Bir Azure IR oluşturmak için, komutun adını, konumunu ve türünü belirtirsiniz. Konum "Batı Avrupa" olarak ayarlanmış bir Azure IR oluşturmak için örnek bir komut aşağıda verilmiştir:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR için türün **yönetilen** olarak ayarlanması gerekir. Bulutta tam olarak yönetilen bir esnek olduğundan işlem ayrıntılarını belirtmeniz gerekmez. Azure-SSIS IR oluşturmak istediğinizde düğüm boyutu ve düğüm sayısı gibi işlem ayrıntılarını belirtin. Daha fazla bilgi için [Azure-SSIS IR oluşturma ve yapılandırma](create-azure-ssis-integration-runtime.md)konusuna bakın.

Mevcut bir Azure IR, konumunu değiştirmek için Set-AzDataFactoryV2IntegrationRuntime PowerShell cmdlet 'ini kullanarak yapılandırabilirsiniz. Azure IR konumu hakkında daha fazla bilgi için bkz. [Integration Runtime 'A giriş](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Azure Data Factory Kullanıcı arabirimi aracılığıyla Azure IR oluşturma
Azure Data Factory Kullanıcı arabirimini kullanarak bir Azure IR oluşturmak için aşağıdaki adımları kullanın.

1. Azure Data Factory Kullanıcı arabiriminin **Başlarken** sayfasında, en soldaki bölmeden [Yönet sekmesini](./author-management-hub.md) seçin.

   ![Giriş sayfası Yönet düğmesi](media/doc-common-process/get-started-page-manage-button.png)

1. Sol bölmedeki **tümleştirme çalışma zamanları** ' nı seçin ve ardından **+ Yeni**' yi seçin.

   ![Sol bölmedeki ve + yeni düğmesindeki tümleştirme çalışma zamanlarını vurgulayan ekran görüntüsü.](media/doc-common-process/manage-new-integration-runtime.png)

1. **Tümleştirme çalışma zamanı kurulumu** sayfasında, **Azure, şirket içinde barındırılan**' ı seçin ve ardından **devam**' ı seçin. 

1. Aşağıdaki sayfada **Azure** ' u seçerek bir Azure IR oluşturun ve ardından **devam**' ı seçin.
   ![Tümleştirme çalışma zamanı oluşturma](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Azure IR için bir ad girin ve **Oluştur**' u seçin.
   ![Azure IR oluşturma](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Oluşturma işlemi tamamlandığında bir açılır bildirim görürsünüz. Tümleştirme çalışma **zamanları** sayfasında, listede yenı oluşturulan IR 'yi görtığınızdan emin olun.

## <a name="use-azure-ir"></a>Azure IR kullan

Azure IR oluşturulduktan sonra, bağlantılı hizmet tanımınızda buna başvurabilirsiniz. Aşağıda, Azure depolama bağlı hizmetinden yukarıda oluşturulan Azure Integration Runtime nasıl başvurulacağını gösteren bir örnek verilmiştir:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
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

- [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma](create-self-hosted-integration-runtime.md)
- [Azure-SSIS tümleştirmesi çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md)
