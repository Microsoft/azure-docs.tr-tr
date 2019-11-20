---
title: Azure Stream Analytics CI/CD NPM paketini kullanın
description: Bu makalede bir sürekli tümleştirme ve dağıtım işlemi ayarlamak için Azure Stream Analytics CI/CD NPM paketinin nasıl kullanılacağı açıklanır.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173339"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Stream Analytics CI/CD NPM paketini kullanın
Bu makalede bir sürekli tümleştirme ve dağıtım işlemi ayarlamak için Azure Stream Analytics CI/CD NPM paketinin nasıl kullanılacağı açıklanır.

## <a name="build-the-vs-code-project"></a>VS Code projeyi oluşturma

**Asa-streamanalytics-cicd** NPM paketini kullanarak Azure Stream Analytics işleri için sürekli tümleştirme ve dağıtım sağlayabilirsiniz. NPM paketi, [Stream Analytics Visual Studio Code projelerinin](quick-create-vs-code.md)Azure Resource Manager şablonlarını oluşturmak için araçlar sağlar. Visual Studio Code yüklenmeden Windows, macOS ve Linux 'ta kullanılabilir.

[Paketi indirdikten](https://www.npmjs.com/package/azure-streamanalytics-cicd)sonra, Azure Resource Manager şablonlarının çıktısını almak için aşağıdaki komutu kullanın. **ScriptPath** bağımsız değişkeni, projenizdeki **aşama QL** dosyasının mutlak yoludur. Asaproj. JSON ve JobConfig. JSON dosyalarının betik dosyası ile aynı klasörde olduğundan emin olun. **OutputPath** belirtilmemişse, şablonlar projenin **bin** klasörü altındaki **Dağıt** klasörüne yerleştirilir.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Örnek (macOS 'ta)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Bir Stream Analytics Visual Studio Code projesi başarıyla oluşturulduğunda, **bin/[Debug/Retail]/Deploy** klasörü altında aşağıdaki iki Azure Resource Manager şablon dosyasını oluşturur: 

*  Şablon dosyası Kaynak Yöneticisi

       [ProjectName].JobTemplate.json 

*  Kaynak Yöneticisi Parameters dosyası

       [ProjectName].JobTemplate.parameters.json   

Parameters. JSON dosyasındaki varsayılan parametreler Visual Studio Code projenizdeki ayarlardan alınır. Başka bir ortama dağıtmak istiyorsanız, parametreleri uygun şekilde değiştirin.

> [!NOTE]
> Tüm kimlik bilgileri için varsayılan değerler null olarak ayarlanır. Buluta dağıtmadan önce değerleri ayarlamanız **gerekir** .

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Kaynak Yöneticisi Şablon dosyası ve Azure PowerShell dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)hakkında daha fazla bilgi edinin. Bir [nesnenin kaynak yöneticisi şablonunda parametre olarak nasıl kullanılacağı](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)hakkında daha fazla bilgi edinin.

Azure Data Lake Store Gen1 için yönetilen kimliği çıkış havuzu olarak kullanmak için, Azure 'a dağıtılmadan önce PowerShell kullanarak hizmet sorumlusuna erişim sağlamanız gerekir. [Kaynak Yöneticisi şablonuyla yönetilen kimlik ile ADLS 1. dağıtma](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)hakkında daha fazla bilgi edinin.
## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı Başlangıç: Visual Studio Code Azure Stream Analytics bulut işi oluşturma (Önizleme)](quick-create-vs-code.md)
* [Sorguları Visual Studio Code ile yerel olarak test Stream Analytics (Önizleme)](vscode-local-run.md)
* [Visual Studio Code ile Azure Stream Analytics araştırma (Önizleme)](vscode-explore-jobs.md)
