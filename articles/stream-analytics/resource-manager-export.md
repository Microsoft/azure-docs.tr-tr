---
title: Azure Stream Analytics işi Azure Resource Manager şablonu dışarı aktarma
description: Bu makalede Azure Stream Analytics işiniz için Azure Resource Manager şablonunun nasıl dışarı aktarılacağı açıklanır.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015428"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure Stream Analytics işi Azure Resource Manager şablonu dışarı aktarma

[Azure Resource Manager şablonları](../azure-resource-manager/templates/overview.md) kod olarak altyapı uygulamanıza olanak tanır. Şablon, kaynaklarınızın altyapısını ve yapılandırmasını tanımlayan bir JavaScript Nesne Gösterimi (JSON) dosyasıdır. Dağıtılacak kaynakları ve bu kaynakların özelliklerini belirtirsiniz.

Azure Resource Manager şablonunu dışarı aktararak Azure Stream Analytics işini yeniden dağıtabilirsiniz.

## <a name="open-a-job-in-vs-code"></a>VS Code bir işi açın

Bir şablonu dışarı aktarmak için önce Visual Studio Code içinde mevcut bir Stream Analytics işini açmanız gerekir. 

Bir işi yerel bir projeye aktarmak için, Azure portal **Stream Analytics Gezgininde** dışarı aktarmak istediğiniz işi bulun. **Sorgu** sayfasından, **Visual Studio 'da aç**' ı seçin. **Visual Studio Code** öğesini seçin.

![Visual Studio Code Stream Analytics işi aç](./media/resource-manager-export/open-job-vs-code.png)

Stream Analytics işleri yönetmek için Visual Studio Code kullanma hakkında daha fazla bilgi için [Visual Studio Code hızlı başlangıç](quick-create-visual-studio-code.md)başlığına bakın.

## <a name="compile-the-script"></a>Betiği derle 

Bir sonraki adım, iş betiğini bir Azure Resource Manager şablonuna derlemedir. Betiği derlemeden önce, işinizin en az bir giriş ve yapılandırılmış bir çıktı içerdiğinden emin olun. Giriş veya çıkış yapılandırılmamışsa, önce girişi ve çıktıyı yapılandırmanız gerekir.

1. Visual Studio Code ' de, işinizin *dönüştürme. aşama QL* dosyasına gidin.

   ![Visual Studio Code dönüştürme. aşama QL dosyası](./media/resource-manager-export/transformation-asaql.png)

1. *Transformation. aşama QL* dosyasına sağ tıklayıp **asa: menüden betiği derle** öğesini seçin.

1. Stream Analytics iş çalışma alanınızda bir **dağıtım** klasörü göründüğünü unutmayın.

1. Dosyası üzerinde, dağıtmak için kullanılan Azure Kaynak Yönetimi şablonu olan *JobTemplate.js* keşfedelim.

## <a name="complete-the-parameters-file"></a>Parametreler dosyasını doldurun

Ardından, Azure Kaynak Yönetimi şablon parametreleri dosyasını doldurun.

1. Visual Studio Code ' de Stream Analytics iş çalışma alanınızın **Dağıt** klasöründe bulunan dosya *JobTemplate.parameters.js* açın.

1. Giriş ve çıkış anahtarlarının NULL olduğuna dikkat edin. Null değerleri, giriş ve çıkış kaynaklarınızın gerçek erişim anahtarlarıyla değiştirin.

1. Parametreler dosyasını kaydedin.

## <a name="deploy-using-templates"></a>Şablonları kullanarak dağıtma

Önceki bölümde oluşturduğunuz Azure Resource Manager şablonları kullanarak Azure Stream Analytics işinizi dağıtmaya hazırsınız.

Bir PowerShell penceresinde aşağıdaki komutu çalıştırın. *Resourcegroupname*, *TemplateFile* ve *templateparameterfile* dosyalarını gerçek kaynak grubu adınızla ve *JobTemplate.jsile ilgili* tüm dosya yollarını ve iş çalışma alanınızın **dağıtım klasöründeki** dosyalar *üzerindeJobTemplate.parameters.js* .

Azure PowerShell yapılandırılmamışsa, [ınstall Azure PowerShell Module](/powershell/azure/install-Az-ps)' daki adımları izleyin.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio Code kullanarak canlı giriş ile Azure Stream Analytics işleri yerel olarak test etme](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code Azure Stream Analytics işleri keşfet (Önizleme)](visual-studio-code-explore-jobs.md)