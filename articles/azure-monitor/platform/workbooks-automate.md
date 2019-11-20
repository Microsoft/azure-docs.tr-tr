---
title: Program aracılığıyla Azure Resource Manager şablonlarıyla Azure Izleyici çalışma kitaplarını yönetme | Microsoft docs
description: Azure Resource Manager şablonları aracılığıyla dağıtılan önceden oluşturulmuş ve özel parametreli Azure Izleyici çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d5e22093fa796a9fbd60dc2bc242f37a6cac7cf0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166115"
---
# <a name="programmatically-manage-workbooks"></a>Program aracılığıyla çalışma kitaplarını yönetme

Kaynak sahipleri, Kaynak Yöneticisi şablonları aracılığıyla çalışma kitaplarını oluşturma ve yönetme seçeneğine sahiptir. 

Bu, aşağıdaki senaryolarda yararlı olabilir:
* Kaynak dağıtımlarıyla birlikte kuruluş veya etki alanına özgü analiz raporları dağıtma. Örneğin, yeni uygulamalarınız veya sanal makineleriniz için kuruluş 'e özgü performans ve hata çalışma kitapları dağıtabilirsiniz.
* Mevcut kaynaklar için çalışma kitaplarını kullanarak standart raporları veya panoları dağıtma.

Çalışma kitabı istenen alt/kaynak grubu içinde ve Kaynak Yöneticisi şablonlarında belirtilen içerikle oluşturulur.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Çalışma kitaplarını dağıtmak için Azure Resource Manager şablonu
1. Programlı olarak dağıtmak istediğiniz bir çalışma kitabını açın.
2. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
3. Araç çubuğundaki _</>_ düğmesini kullanarak _Gelişmiş Düzenleyici_ açın.
4. Düzenleyicide, _şablon türünü_ _Kaynak Yöneticisi şablona_değiştirin.
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
| `workbookType` | Çalışma kitabının altında gösterilecek Galeri. Desteklenen değerler çalışma kitabı, `tsg`, Azure Izleyici vb. içerir. |
| `workbookSourceId` | Çalışma kitabının ilişkilendirileceği kaynak örneğinin KIMLIĞI. Yeni çalışma kitabı bu kaynak örneğiyle ilgili görünür. Örneğin, _çalışma kitabı_altındaki içeriğin kaynak tablosu. Çalışma kitabınızın Azure Izleyici 'deki çalışma kitabı galerisinde görünmesini istiyorsanız, kaynak KIMLIĞI yerine _Azure izleyici_ dize kullanın. |
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
Teknik bir nedenle, bu mekanizma Application Insights çalışma _kitapları_ galerisinde çalışma kitabı örnekleri oluşturmak için kullanılamaz. Bu sınırlamayı ele almak için çalışıyoruz. Bu arada, ilgili çalışma kitaplarını Application Insights dağıtmak için sorun giderme kılavuzu galerisini (workbookType: `tsg`) kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Yeni [Azure Izleyicisini depolama deneyimi için](../insights/storage-insights-overview.md)desteklemek üzere çalışma kitaplarının nasıl kullanıldığını keşfedebilirsiniz.

