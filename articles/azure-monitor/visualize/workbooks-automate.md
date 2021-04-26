---
title: Azure Izleyici çalışma kitapları ve Azure Resource Manager şablonları
description: Azure Resource Manager şablonları aracılığıyla dağıtılan önceden oluşturulmuş ve özel parametreli Azure Izleyici çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d4aac17ca823f4eaa0f52ab260b1daca3f52f94
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109756"
---
# <a name="programmatically-manage-workbooks"></a>Program aracılığıyla çalışma kitaplarını yönetme

Kaynak sahipleri, Kaynak Yöneticisi şablonları aracılığıyla çalışma kitaplarını oluşturma ve yönetme seçeneğine sahiptir.

Bu, aşağıdaki senaryolarda yararlı olabilir:
* Kaynak dağıtımlarıyla birlikte kuruluş veya etki alanına özgü analiz raporları dağıtma. Örneğin, yeni uygulamalarınız veya sanal makineleriniz için kuruluş 'e özgü performans ve hata çalışma kitapları dağıtabilirsiniz.
* Mevcut kaynaklar için çalışma kitaplarını kullanarak standart raporları veya panoları dağıtma.

Çalışma kitabı istenen alt/kaynak grubu içinde ve Kaynak Yöneticisi şablonlarında belirtilen içerikle oluşturulur.

Programlı olarak yönetilebilecek iki tür çalışma kitabı kaynağı vardır:
* [Çalışma kitabı şablonları](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Çalışma kitabı örnekleri](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Çalışma kitabı şablonu dağıtmak için Azure Resource Manager şablonu

1. Programlı olarak dağıtmak istediğiniz bir çalışma kitabını açın.
2. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
3. Araç çubuğundaki düğmesini kullanarak _Gelişmiş Düzenleyici_ açın _</>_ .
4. _Galeri şablonu_ sekmesinde olduğunuzdan emin olun.

    ![Galeri şablonu sekmesi](./media/workbooks-automate/gallery-template.png)
1. Galeri şablonundaki JSON öğesini panoya kopyalayın.
2. Aşağıda, Azure Izleyici çalışma kitabı galerisine bir çalışma kitabı şablonu dağıtan örnek bir Azure Resource Manager şablonu verilmiştir. Yerinde kopyaladığınız JSON 'yi yapıştırın `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . Bir çalışma kitabı şablonu oluşturan bir başvuru Azure Resource Manager şablonu [burada](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)bulunabilir.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. `galleries`Nesnesinde `name` ve `category` anahtarlarını değerlerinizle birlikte girin. Sonraki bölümde [Parametreler](#parameters) hakkında daha fazla bilgi edinin.
2. Bu Azure Resource Manager şablonunu [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), [komut satırı arabirimi](../../azure-resource-manager/templates/deploy-cli.md), [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)vb. kullanarak dağıtın.
3. Azure portal açın ve Azure Resource Manager şablonunda seçilen çalışma kitabı galerisine gidin. Örnek şablonda Azure Izleyici çalışma kitabı galerisine gidin:
    1. Azure portal açın ve Azure Izleyicisi ' ne gidin
    2. `Workbooks`İçindekiler tablosundan aç
    3. Şablonunuz kategorisi altındaki galeride bulun `Deployed Templates` (mor öğelerden biri olacaktır).

### <a name="parameters"></a>Parametreler

|Parametreler                |Açıklama                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure Resource Manager çalışma kitabı şablon kaynağının adı.                                  |
|`type`                    | Her zaman Microsoft. Insights/workbooktemplates                                                            |
| `location`               | Çalışma kitabının oluşturulacağı Azure konumu.                                               |
| `apiVersion`             | 2019-10-17 Önizleme                                                                                     |
| `type`                   | Her zaman Microsoft. Insights/workbooktemplates                                                            |
| `galleries`              | Bu çalışma kitabı şablonunu içinde göstermek için galeriler kümesi.                                                |
| `gallery.name`           | Galerideki çalışma kitabı şablonunun kolay adı.                                             |
| `gallery.category`       | Galerideki şablonun yerleştirileceği grup.                                                     |
| `gallery.order`          | Galerideki bir kategori içinde şablonu gösterme sırasına karar veren bir sayı. Daha düşük sıralama önceliği daha yüksektir. |
| `gallery.resourceType`   | Galeriye karşılık gelen kaynak türü. Bu genellikle kaynağa karşılık gelen kaynak türü dizedir (örneğin, Microsoft. operationalınsights/çalışma alanları). |
|`gallery.type`            | Çalışma kitabı türü olarak bahsedildiğinde, bu, galeriyi bir kaynak türü içinde ayıran benzersiz bir anahtardır. Örneğin, Application Insights `workbook` ve `tsg` farklı çalışma kitabı galerilerine karşılık gelen türler vardır. |

### <a name="galleries"></a>Galeriler

| Galeri                                        | Kaynak türü                                       | Çalışma kitabı türü |
|:-----------------------------------------------|:----------------------------------------------------|:--------------|
| Azure Izleyici 'de çalışma kitapları                     | `Azure Monitor`                                     | `workbook`    |
| Azure Izleyici 'de VM öngörüleri                   | `Azure Monitor`                                     | `vm-insights` |
| Log Analytics çalışma alanındaki çalışma kitapları           | `microsoft.operationalinsights/workspaces`          | `workbook`    |
| Application Insights çalışma kitapları              | `microsoft.insights/components`                     | `workbook`    |
| Application Insights 'de sorun giderme kılavuzu | `microsoft.insights/components`                     | `tsg`         |
| Application Insights kullanımı                  | `microsoft.insights/components`                     | `usage`       |
| Kubernetes hizmetindeki çalışma kitapları                | `Microsoft.ContainerService/managedClusters`        | `workbook`    |
| Kaynak gruplarındaki çalışma kitapları                   | `microsoft.resources/subscriptions/resourcegroups`  | `workbook`    |
| Azure Active Directory çalışma kitapları            | `microsoft.aadiam/tenant`                           | `workbook`    |
| Sanal makinelerde VM öngörüleri                | `microsoft.compute/virtualmachines`                 | `insights`    |
| Sanal makine ölçek kümelerinde VM öngörüleri      | `microsoft.compute/virtualmachinescalesets`         | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Çalışma kitabı örneği dağıtmak için Azure Resource Manager şablonu

1. Programlı olarak dağıtmak istediğiniz bir çalışma kitabını açın.
2. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
3. Araç çubuğundaki düğmesini kullanarak _Gelişmiş Düzenleyici_ açın _</>_ .
4. Düzenleyicide, _şablon türünü_ _Kaynak Yöneticisi şablona_ değiştirin.
5. Oluşturmak için Kaynak Yöneticisi şablonu düzenleyicide görüntülenir. İçeriği kopyalayın ve olduğu gibi kullanın ya da hedef kaynağı dağıtan daha büyük bir şablonla birleştirin.

    ![Çalışma kitabı Kullanıcı arabirimi içinden Kaynak Yöneticisi şablonunun nasıl alınacağını gösteren resim](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Örnek Azure Resource Manager şablonu
Bu şablon ' Merhaba Dünya! ' görüntüleyen basit bir çalışma kitabının nasıl dağıtılacağını gösterir
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Şablon parametreleri

| Parametre | Açıklama |
| :------------- |:-------------|
| `workbookDisplayName` | Galeri veya kayıtlı listede kullanılan çalışma kitabının kolay adı. Kaynak grubunun ve kaynağın kapsamında benzersiz olması gerekir |
| `workbookType` | Çalışma kitabının altında gösterilecek Galeri. Desteklenen değerler çalışma kitabı, `tsg` , Azure izleyici vb. içerir. |
| `workbookSourceId` | Çalışma kitabının ilişkilendirileceği kaynak örneğinin KIMLIĞI. Yeni çalışma kitabı bu kaynak örneğiyle ilgili görünür. Örneğin, _çalışma kitabı_ altındaki içeriğin kaynak tablosu. Çalışma kitabınızın Azure Izleyici 'deki çalışma kitabı galerisinde görünmesini istiyorsanız, kaynak KIMLIĞI yerine _Azure izleyici_ dize kullanın. |
| `workbookId` | Bu çalışma kitabı örneği için benzersiz GUID. Otomatik olarak yeni bir GUID oluşturmak için _[NewGuid ()]_ kullanın. |
| `kind` | Oluşturulan çalışma kitabının paylaşılıp paylaşıldığını veya özel olduğunu belirtmek için kullanılır. Paylaşılan çalışma kitapları ve _Kullanıcı_ için _paylaşılan_ değeri özel olanlar için kullanın. |
| `location` | Çalışma kitabının oluşturulacağı Azure konumu. Kaynak grubuyla aynı konumda oluşturmak için _[resourceGroup (). Location]_ kullanın |
| `serializedData` | Çalışma kitabında kullanılacak içeriği veya yükü içerir. Değeri almak için çalışma kitapları Kullanıcı arabirimindeki Kaynak Yöneticisi şablonunu kullanın |

### <a name="workbook-types"></a>Çalışma kitabı türleri
Çalışma kitabı türleri hangi çalışma kitabı galerisinin türünü yeni çalışma kitabı örneğinin gösterileceğini belirtir. Seçeneklere şunlar dahildir:

| Tür | Galeri konumu |
| :------------- |:-------------|
| `workbook` | Application Insights, Azure Izleyici vb. çalışma kitapları Galerisi de dahil olmak üzere çoğu raporda kullanılan varsayılan değer.  |
| `tsg` | Application Insights 'de sorun giderme kılavuzu Galerisi |
| `usage` | Application Insights _kullanımı_ altında _daha fazla_ Galeri |

### <a name="limitations"></a>Sınırlamalar
Teknik bir nedenle, bu mekanizma Application Insights çalışma _kitapları_ galerisinde çalışma kitabı örnekleri oluşturmak için kullanılamaz. Bu sınırlamayı ele almak için çalışıyoruz. Bu arada, `tsg` Application Insights ilgili çalışma kitaplarını dağıtmak Için sorun giderme kılavuzu galerisini (workbookType:) kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Yeni [Azure Izleyicisini depolama deneyimi için](../insights/storage-insights-overview.md)desteklemek üzere çalışma kitaplarının nasıl kullanıldığını keşfedebilirsiniz.
