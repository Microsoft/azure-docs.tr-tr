---
title: Azure Resource Manager şablonu kullanarak Azure Data Factory oluşturma (ARM şablonu)
description: Bir Azure Resource Manager şablonu kullanarak örnek Azure Data Factory işlem hattı oluşturun (ARM şablonu).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: c579b6d723533e751e08a80a578195c03e945607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783345"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure Data Factory oluşturma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Güncel sürüm](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu hızlı başlangıçta, Azure Data Factory oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır. Bu veri fabrikasında oluşturduğunuz işlem hattı, verileri bir Azure Blob depolama alanındaki bir klasörden başka bir klasöre **kopyalar** . Azure Data Factory kullanarak verileri **dönüştürme** hakkında bir öğretici için bkz. [Öğretici: Spark kullanarak verileri dönüştürme](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Bu makale, Data Factory hizmetine ayrıntılı giriş bilgileri sağlamaz. Azure Data Factory hizmetine giriş bilgileri için bkz. [Azure Data Factory'ye giriş](introduction.md).

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

### <a name="azure-subscription"></a>Azure aboneliği

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

### <a name="create-a-file"></a>Dosya oluşturma

**Not defteri** gibi bir metin Düzenleyicisi açın ve aşağıdaki içeriğe sahip **emp.txt** adlı bir dosya oluşturun:

```emp.txt
John, Doe
Jane, Doe
```

Dosyayı **C:\ADFv2QuickStartPSH** klasörüne kaydedin. (Klasör zaten mevcut değilse, oluşturun.)

## <a name="review-template"></a>Şablonu gözden geçir

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Şablonda tanımlanmış Azure kaynakları var:

- [Microsoft. Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): bir depolama hesabı tanımlar.
- [Microsoft. DataFactory/Factory](/azure/templates/microsoft.datafactory/factories): Azure Data Factory oluşturun.
- [Microsoft. DataFactory/Factory/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Azure Data Factory bağlı bir hizmet oluşturun.
- [Microsoft. DataFactory/Factory/veri kümeleri](/azure/templates/microsoft.datafactory/factories/datasets): Azure Data Factory veri kümesi oluşturma.
- [Microsoft. DataFactory/Factory/işlem hatları](/azure/templates/microsoft.datafactory/factories/pipelines): Azure Data Factory işlem hattı oluşturma.

Daha fazla Azure Data Factory şablon örneği [hızlı başlangıç şablonu galerisinde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon Azure Data Factory bir hesap, bir depolama hesabı ve bir blob kapsayıcısı oluşturur.

    [![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="ADF ARM şablonunu dağıt":::

    Belirtilmedikçe, Azure Data Factory kaynaklarını oluşturmak için varsayılan değerleri kullanın:

    - **Abonelik**: bir Azure aboneliği seçin.
    - **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve **Tamam**' ı seçin.
    - **Bölge**: bir konum seçin.  Örneğin, *Doğu ABD*.
    - **Data Factory adı**: varsayılan değeri kullanın.
    - **Konum**: varsayılan değeri kullanın.
    - **Depolama hesabı adı**: varsayılan değeri kullanın.
    - **BLOB kapsayıcısı**: varsayılan değeri kullanın.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. **Kaynak grubuna git**' i seçin.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Kaynak Grubu":::

2.  Azure Data Factory oluşturulduğunu doğrulayın.
    1. Azure Data Factory adınız-DataFactory biçimindedir \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Örnek Data Factory":::

2. Depolama hesabınızın oluşturulduğunu doğrulayın.
    1. Depolama hesabı adı, depolama biçimindedir \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Depolama hesabı":::

3. Oluşturulan depolama hesabını seçin ve **kapsayıcılar**' ı seçin.
    1. **Kapsayıcılar** sayfasında, oluşturduğunuz blob kapsayıcısını seçin.
        1. Blob kapsayıcısı adı-blob biçimindedir \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Blob kapsayıcı":::

### <a name="upload-a-file"></a>Dosyayı karşıya yükleme

1. **Kapsayıcılar** sayfasında **karşıya yükle**' yi seçin.

2. Sağ bölmede, **dosyalar** kutusunu seçin ve ardından daha önce oluşturduğunuz **emp.txt** dosyasına gidip seçin.

3. **Gelişmiş** başlık ' ı genişletin.

4. **Klasöre yükle** kutusuna *giriş*' i girin.

5. **Karşıya Yükle** düğmesini seçin. Listede **emp.txt** dosyasını ve karşıya yükleme durumunu görmeniz gerekir.

6. **BLOB yükle** sayfasını kapatmak için **Kapat** simgesini (bir **X**) seçin.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Dosyayı giriş klasörüne yükle":::

Bu hızlı başlangıç sonunda çıktıyı doğrulamak için kullanabileceğiniz kapsayıcı sayfasını açık tutun.

### <a name="start-trigger"></a>Tetikleyiciyi Başlat

1. **Veri fabrikaları** sayfasına gidin ve oluşturduğunuz veri fabrikasını seçin. 

2. **Yazar & İzleyicisi** kutucuğunu seçin. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Yazar & Izleyicisi":::

2. **Yazar** sekmesini seçin :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: .

3. Oluşturulan işlem hattını seçin-ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM şablon işlem hattı":::

4. **Tetikleyici tetikleyicisi Ekle**' yi  >  **Şimdi** seçin.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Tetikleyici":::

5. İşlem **hattı çalıştırma** altındaki sağ bölmede **Tamam**' ı seçin.

### <a name="monitor-the-pipeline"></a>İşlem hattını izleme

1. **İzleyici** sekmesini seçin :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: .

2. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görürsünüz. Bu hızlı başlangıçta işlem hattı yalnızca bir etkinlik türü içerir: Kopyalama. Bu nedenle, söz konusu etkinlik için bir çalıştırma görürsünüz.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Başarılı çalıştırma":::

### <a name="verify-the-output-file"></a>Çıkış dosyasını doğrulama

İşlem hattı blob kapsayıcısında otomatik olarak bir çıkış klasörü oluşturur. Ardından, giriş klasöründeki emp.txt dosyasını çıktı klasörüne kopyalar. 

1. Azure portal, **kapsayıcılar** sayfasında, çıktı klasörünü görmek için **Yenile** ' yi seçin. 

2. Klasör listesinde **Çıkış ' ı** seçin.

3. **emp.txt** dosyasının output klasörüne kopyalandığını onaylayın. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Çıktı":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıç bölümünde oluşturduğunuz kaynakları iki şekilde temizleyebilirsiniz. Kaynak grubundaki tüm kaynakları içeren [Azure kaynak grubunu silebilirsiniz](../azure-resource-manager/management/delete-resource-group.md). Diğer kaynakları korumak istiyorsanız yalnızca bu öğreticide oluşturduğunuz veri kaynağını silin.

Bir kaynak grubunun silinmesi, içindeki veri fabrikaları dahil olmak üzere tüm kaynakları siler. Kaynak grubunun tamamını silmek için şu komutu çalıştırın: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Kaynak grubunun tamamını değil yalnızca veri fabrikasını silmek istiyorsanız aşağıdaki komutu çalıştırın: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir ARM şablonu kullanarak bir Azure Data Factory oluşturdunuz ve dağıtımı doğruladı. Azure Data Factory ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- [Azure Data Factory belgeleri](index.yml)
- [Azure Resource Manager](../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
- Diğer [Azure Data Factory ARM şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular) al