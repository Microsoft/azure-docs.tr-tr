---
title: Yönetim çözümlerinde görünümler | Microsoft Docs
description: 'Yönetim çözümleri, verileri görselleştirmek için genellikle bir veya daha fazla görünüm içerir.  Bu makalede, Görünüm Tasarımcısı tarafından oluşturulan bir görünümün nasıl dışarı aktarılacağı ve bir yönetim çözümüne dahil olduğu açıklanır. '
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 473d10bbec6ca056554f7223800a32e9ca93578e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553906"
---
# <a name="views-in-management-solutions-preview"></a>Yönetim çözümlerinde görünümler (Önizleme)
> [!NOTE]
> Bu, şu anda önizleme aşamasında olan yönetim çözümlerini oluşturmaya yönelik bir belgedir. Aşağıda açıklanan tüm şemalarla değişiklik yapılır.    


[Yönetim çözümleri](solutions.md) , verileri görselleştirmek için genellikle bir veya daha fazla görünüm içerir.  Bu makalede, [Görünüm Tasarımcısı](../../azure-monitor/platform/view-designer.md) tarafından oluşturulan bir görünümün nasıl dışarı aktarılacağı ve bir yönetim çözümüne dahil olduğu açıklanır.  

> [!NOTE]
> Bu makaledeki örneklerde, yönetim çözümlerinde gerekli veya ortak olan ve [Azure 'da bir yönetim çözümü tasarlama ve derleme](solutions-creating.md) konularında açıklanan parametreleri ve değişkenleri kullanır
>
>

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, [bir yönetim çözümü oluşturma](solutions-creating.md) ve bir çözüm dosyasının yapısı hakkında daha fazla bilgi sahibi olduğunuz varsayılır.

## <a name="overview"></a>Genel Bakış
Bir yönetim çözümüne bir görünüm eklemek için, [çözüm dosyasında](solutions-creating.md)bu kaynak için bir **kaynak** oluşturursunuz.  Görünümün ayrıntılı yapılandırmasını açıklayan JSON genellikle karmaşıktır, ancak tipik bir çözüm yazarının el ile oluşturabilebileceği bir şey değildir.  En yaygın yöntem Görünüm [tasarımcısını](../../azure-monitor/platform/view-designer.md)kullanarak görünümü oluşturmak, dışarı aktarmak ve ardından ayrıntılı yapılandırmasını çözüme eklemektir.

Bir çözüme görünüm eklemenin temel adımları aşağıdaki gibidir.  Her adım aşağıdaki bölümlerde daha ayrıntılı olarak açıklanmıştır.

1. Görünümü bir dosyaya dışarı aktarın.
2. Çözümde görünüm kaynağını oluşturun.
3. Görünüm ayrıntılarını ekleyin.

## <a name="export-the-view-to-a-file"></a>Görünümü bir dosyaya dışarı aktarma
Bir görünümü dosyaya aktarmak için [Log Analytics Görünüm Tasarımcısı](../../azure-monitor/platform/view-designer.md) ' nın yönergelerini izleyin.  Verilecek dosya, [çözüm dosyasıyla aynı öğelerle](solutions-solution-file.md)birlikte JSON biçiminde olacaktır.  

Görünüm dosyasının **Resources** öğesi, Log Analytics çalışma alanını temsil eden **Microsoft. operationalınsights/çalışma alanları** türünde bir kaynağa sahip olur.  Bu öğe, görünümü temsil eden ve ayrıntılı yapılandırmasını içeren bir **Görünüm** türünde bir alt öğeye sahip olacaktır.  Bu öğenin ayrıntılarını kopyalayıp çözümünüze kopyalayacaksınız.

## <a name="create-the-view-resource-in-the-solution"></a>Çözümde görünüm kaynağını oluşturma
Aşağıdaki görünüm kaynağını çözüm dosyanızın **Resources** öğesine ekleyin.  Bu, aşağıda açıklanan ve ayrıca eklemeniz gereken değişkenleri kullanır.  **Panonun** ve **overviewtile** özelliklerinin, içe aktarılmış görünüm dosyasındaki ilgili özelliklerle üzerine yazılacak yer tutucular olduğunu unutmayın.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Aşağıdaki değişkenleri çözüm dosyasının değişkenler öğesine ekleyin ve değerlerini çözümünüze göre değiştirin.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Tüm görünüm kaynağını, verdiğiniz görünüm dosyasından kopyalayabileceğinizi unutmayın, ancak çözümünüzde çalışması için aşağıdaki değişiklikleri yapmanız gerekir.  

* Görünüm kaynağı **türünün** **görünümlerden** **Microsoft. operationalınsights/Workspaces**olarak değiştirilmesi gerekir.
* Görünüm kaynağı için **ad** özelliğinin, çalışma alanı adını içerecek şekilde değiştirilmesi gerekir.
* Çalışma alanı kaynağı çözümde tanımlanmadığı için çalışma alanındaki bağımlılığın kaldırılması gerekir.
* **DisplayName** özelliğinin görünüme eklenmesi gerekiyor.  **ID**, **Name**ve **DisplayName** tüm eşleşmelidir.
* Parametre adlarının gerekli parametre kümesiyle eşleşecek şekilde değiştirilmesi gerekir.
* Değişkenlerin çözüm içinde tanımlanması ve uygun özelliklerde kullanılması gerekir.

### <a name="log-analytics-api-version"></a>Log Analytics API sürümü
Bir Kaynak Yöneticisi şablonunda tanımlanan tüm Log Analytics kaynakların, kaynağın kullanması gereken API sürümünü tanımlayan bir **Apiversion** özelliği vardır.  Bu sürüm, [Eski ve yükseltilen sorgu dilini](../../azure-monitor/log-query/log-query-overview.md)kullanan sorguları olan görünümlerde farklıdır.  

 Aşağıdaki tabloda, eski ve yükseltilen çalışma alanlarındaki görünümler için Log Analytics API sürümleri verilmiştir: 

| Çalışma alanı sürümü | API sürümü | Sorgu |
|:---|:---|:---|
| v1 (eski)   | 2015-11-01-Önizleme | Eski biçim.<br> Örnek: Type = olay EventLevelName = hata  |
| v2 (yükseltilmiş) | 2015-11-01-Önizleme | Eski biçim.  Yüklemede yükseltilen biçime dönüştürüldü.<br> Örnek: Type = olay EventLevelName = hata<br>Öğesine dönüştürüldü: EventLevelName = = "Error" olayına &#124;  |
| v2 (yükseltilmiş) | 2017-03-03-Önizleme | Yükseltme biçimi. <br>Örnek: EventLevelName = = "Error" adlı olay &#124;  |


## <a name="add-the-view-details"></a>Görünüm ayrıntılarını ekleme
İçe aktarılmış görünüm dosyasındaki görünüm kaynağı, görünümün ayrıntılı yapılandırmasını içeren **Dashboard** ve **overviewtile** adlı **Özellikler** öğesinde iki öğe içerir.  Bu iki öğeyi ve içeriklerini çözüm dosyanızdaki görünüm kaynağının **Properties** öğesine kopyalayın.

## <a name="example"></a>Örnek
Örneğin, aşağıdaki örnek, görünümü olan basit bir çözüm dosyasını göstermektedir.  Üç nokta (...), **Pano** için ve boşluk nedeniyle **fazla bakış kutucuğu** içerikleri için gösterilir.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Sonraki adımlar
* [Yönetim çözümleri](solutions-creating.md)oluşturma hakkında ayrıntılı bilgi edinin.
* [Otomasyon Runbook 'larını yönetim çözümünüze](solutions-resources-automation.md)dahil edin.
