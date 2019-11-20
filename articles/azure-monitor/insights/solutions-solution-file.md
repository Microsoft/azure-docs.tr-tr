---
title: Azure 'da bir yönetim çözümü dosyası oluşturma | Microsoft Docs
description: Yönetim çözümleri, müşterilerin Azure ortamlarına ekleyebilmeleri için paketlenmiş yönetim senaryoları sağlar.  Bu makalede, kendi ortamınızda kullanılmak üzere yönetim çözümlerini nasıl oluşturabileceğiniz veya müşterileriniz için kullanılabilir hale getirdiğiniz hakkında ayrıntılar sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47ee691186da7f915ca8fcf87415784ab12ef1e0
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553851"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Azure 'da bir yönetim çözümü dosyası oluşturma (Önizleme)
> [!NOTE]
> Bu, şu anda önizleme aşamasında olan Azure 'da yönetim çözümleri oluşturmaya yönelik bir belgedir. Aşağıda açıklanan tüm şemalarla değişiklik yapılır.  

Azure 'daki yönetim çözümleri [Kaynak Yöneticisi şablonlar](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)olarak uygulanır.  Yönetim çözümlerinin nasıl yazıldığını öğrenirken ana görev, [bir şablonu](../../azure-resource-manager/resource-group-authoring-templates.md)nasıl yazacağınızı öğreniyor.  Bu makalede, çözümler için kullanılan şablonların ve tipik çözüm kaynaklarının nasıl yapılandırılacağı hakkında benzersiz ayrıntılar verilmektedir.


## <a name="tools"></a>Araçlar

Çözüm dosyaları ile çalışmak için herhangi bir metin düzenleyicisini kullanabilirsiniz, ancak Visual Studio 'da belirtilen özelliklerden veya Visual Studio Code aşağıdaki makalelerde açıklandığı gibi önerilir.

- [Visual Studio aracılığıyla Azure Kaynak grupları oluşturma ve dağıtma](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Visual Studio Code Azure Resource Manager şablonlarıyla çalışma](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>yapısı
Bir yönetim çözümü dosyasının temel yapısı, aşağıdaki gibi [Kaynak Yöneticisi şablonuyla](../../azure-resource-manager/resource-group-authoring-templates.md#template-format)aynıdır.  Aşağıdaki bölümlerin her biri, en üst düzey öğeleri ve bunların içeriğini bir çözümde açıklamaktadır.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametreler
[Parametreler](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) , yönetim çözümünü yüklediklerinde kullanıcıdan ihtiyacınız olan değerlerdir.  Tüm çözümlerin sahip olacağı standart parametreler vardır ve belirli çözümünüz için gereken ek parametreleri de ekleyebilirsiniz.  Kullanıcılarınızın çözümünüzü yüklediklerinde parametre değerlerini nasıl sağlayabilecekleri, belirli parametreye ve çözümün yüklenme biçimine bağlıdır.

Bir Kullanıcı Azure Marketi veya Azure hızlı başlangıç şablonları aracılığıyla [Yönetim çözümünüzü yüklediğinde](solutions.md#install-a-monitoring-solution) , bir [Log Analytics çalışma alanı ve Otomasyon hesabı](solutions.md#log-analytics-workspace-and-automation-account)seçmeleri istenir.  Bunlar her standart parametrenin değerlerini doldurmak için kullanılır.  Kullanıcıdan standart parametrelerin değerlerini doğrudan sağlaması istenmez, ancak ek parametreler için değer sağlamaları istenir.


Örnek bir parametre aşağıda gösterilmiştir.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Aşağıdaki tabloda bir parametresinin öznitelikleri açıklanmaktadır.

| Öznitelik | Açıklama |
|:--- |:--- |
| type |Parametre için veri türü. Kullanıcı için görünen giriş denetimi, veri türüne bağlıdır.<br><br>bool-açılan kutusu<br>dize-metin kutusu<br>int-metin kutusu<br>SecureString-Password alanı<br> |
| category |Parametre için isteğe bağlı kategori.  Aynı kategorideki parametreler birlikte gruplandırılır. |
| Denetimle |Dize parametreleri için ek işlevsellik.<br><br>DateTime-DateTime denetimi görüntülenir.<br>GUID-GUID değeri otomatik olarak oluşturulur ve parametresi gösterilmez. |
| açıklama |Parametre için isteğe bağlı açıklama.  Parametrenin yanındaki bir bilgi balonunda görüntülenir. |

### <a name="standard-parameters"></a>Standart parametreler
Aşağıdaki tabloda tüm yönetim çözümleri için standart parametreler listelenmektedir.  Bu değerler, çözümünüz Azure Marketi 'nden veya hızlı başlangıç şablonlarından yüklendiğinde istemek yerine Kullanıcı için doldurulur.  Çözüm başka bir yöntemle yüklendiyse, Kullanıcı bu değerler için değer sağlamalıdır.

> [!NOTE]
> Azure Marketi ve hızlı başlangıç şablonlarındaki Kullanıcı arabirimi, tablodaki parametre adlarını bekliyor.  Farklı parametre adları kullanırsanız, kullanıcıya bu kullanıcı sorulur ve otomatik olarak doldurulmayacaktır.
>
>

| Parametre | Tür | Açıklama |
|:--- |:--- |:--- |
| Adı |string |Azure Otomasyonu hesap adı. |
| pricingTier |string |Log Analytics çalışma alanının ve Azure Otomasyonu hesabının fiyatlandırma katmanı. |
| RegionID |string |Azure Otomasyonu hesabının bölgesi. |
| solutionName |string |Çözümün adı.  Çözümünüzü hızlı başlangıç şablonları aracılığıyla dağıtıyorsanız, kullanıcının bir tane belirtmesini gerektirmek yerine bir dize tanımlayabilmeniz için solutionName parametresini parametre olarak tanımlamanız gerekir. |
| workspaceName |string |Log Analytics çalışma alanı adı. |
| workspaceRegionId |string |Log Analytics çalışma alanının bölgesi. |


Aşağıda, kopyalayıp çözüm dosyanıza yapıştırabileceğiniz standart parametrelerin yapısı verilmiştir.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Sözdizimi **parametreleri (' parametre adı ')** ile çözümün diğer öğelerinde parametre değerlerine başvurursunuz.  Örneğin, çalışma alanı adına erişmek için **parametreleri (' Çalışmaalanıadı ')** kullanacaksınız

## <a name="variables"></a>Değişkenler
[Değişkenler](../../azure-resource-manager/resource-group-authoring-templates.md#variables) , yönetim çözümünün geri kalanında kullanacağınız değerlerdir.  Bu değerler, çözümü yükleyen kullanıcıya gösterilmez.  Bu kişiler, çözüm boyunca birden çok kez kullanılabilecek değerleri yönetebilecekleri tek bir konum ile yazar sağlamaya yöneliktir. Çözümünüzde çözümünüze özgü tüm değerleri, **kaynaklar** öğesinde sabit kodlamadan farklı şekilde kodlamak gerekir.  Bu, kodu daha okunaklı hale getirir ve sonraki sürümlerde bu değerleri kolayca değiştirmenize olanak sağlar.

Aşağıda, çözümlerinde tipik parametrelerle kullanılan bir **değişkenler** öğesi örneği verilmiştir.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Sözdizimi **değişkenleri (' değişken adı ')** olan çözüm aracılığıyla değişken değerlerine başvurursunuz.  Örneğin, SolutionName değişkenine erişmek için **değişkenleri (' SolutionName ')** kullanacaksınız.

Ayrıca, birden çok değer kümesine yönelik karmaşık değişkenler de tanımlayabilirsiniz.  Bunlar özellikle farklı kaynak türleri için birden çok özellik tanımladığınız yönetim çözümlerinde yararlıdır.  Örneğin, yukarıda gösterilen çözüm değişkenlerini aşağıdaki şekilde yeniden yapılandırabilirsiniz.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Bu durumda, sözdizimi **değişkenleri (' değişken adı '). özelliği**ile çözüm aracılığıyla değişken değerlerine başvurursunuz.  Örneğin, çözüm adı değişkenine erişmek için **değişkenleri (' çözüm ') kullanacaksınız. Ad**.

## <a name="resources"></a>Kaynaklar
[Kaynaklar](../../azure-resource-manager/resource-group-authoring-templates.md#resources) , yönetim çözümünüzün yükleneceği ve yapılandıracağına farklı kaynakları tanımlar.  Bu, şablonun en büyük ve en karmaşık bölümü olacaktır.  [Azure Resource Manager şablonlarda](../../azure-resource-manager/resource-group-authoring-templates.md#resources), kaynak öğelerinin yapısını ve tüm açıklamalarını alabilirsiniz.  Genellikle tanımlayabileceğiniz farklı kaynaklar, bu belgelerindeki diğer makalelerde ayrıntılıdır. 


### <a name="dependencies"></a>Bağımlılıklar
**Bağımlıdson** öğesi, başka bir kaynağa [bağımlılığı](../../azure-resource-manager/resource-group-define-dependencies.md) belirtir.  Çözüm yüklendiğinde, tüm bağımlılıkları oluşturuluncaya kadar bir kaynak oluşturulmaz.  Örneğin, çözümünüz bir [iş kaynağı](solutions-resources-automation.md#automation-jobs)kullanılarak yüklendiğinde [bir runbook başlatabilir](solutions-resources-automation.md#runbooks) .  İş kaynağı, runbook 'un iş oluşturulmadan önce oluşturulduğundan emin olmak için Runbook kaynağına bağımlıdır.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics çalışma alanı ve Otomasyon hesabı
Yönetim çözümleri, runbook 'ları ve ilgili kaynakları içeren görünümler ve bir [Otomasyon hesabı](../../automation/automation-security-overview.md#automation-account-overview) içermesi için bir [Log Analytics çalışma alanı](../../azure-monitor/platform/manage-access.md) gerektirir.  Bunlar, çözümdeki kaynaklar oluşturulmadan önce kullanılabilir olmalıdır ve çözümün kendisinde tanımlanmamalıdır.  Kullanıcı çözümünüzü dağıtırken [bir çalışma alanı ve hesap belirtir](solutions.md#log-analytics-workspace-and-automation-account) , ancak yazar olarak aşağıdaki noktaları dikkate almalısınız.


## <a name="solution-resource"></a>Çözüm kaynağı
Her çözüm, çözümün kendisini tanımlayan **Resources** öğesinde bir kaynak girişi gerektirir.  Bu, **Microsoft. OperationsManagement/Solutions** türüne sahip olur ve aşağıdaki yapıya sahiptir. Bu, genellikle çözümün özelliklerini tanımlamak için kullanılan [Standart parametreleri](#parameters) ve [değişkenleri](#variables) içerir.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Bağımlılıklar
Çözüm oluşturulmadan önce mevcut olmaları gerektiğinden çözüm kaynağının çözümdeki her bir kaynağa [bağımlılığı](../../azure-resource-manager/resource-group-define-dependencies.md) olmalıdır.  Bunu, **Bağımlıdson** öğesindeki her kaynak için bir giriş ekleyerek yapabilirsiniz.

### <a name="properties"></a>Özellikler
Çözüm kaynağı aşağıdaki tablodaki özelliklere sahiptir.  Bu, başvurulan kaynakları içerir ve çözüm yüklendikten sonra kaynağın nasıl yönetildiğini tanımlayan çözüm tarafından içerilir.  Çözümdeki her bir kaynağın **Referencedresources** veya **containedresources** özelliğinde listelenmesi gerekir.

| Özellik | Açıklama |
|:--- |:--- |
| workspaceResourceId |Form *\<Resource grup kimliği >/providers/Microsoft.OperationalInsights/workspaces/\<Workspace adı \>* Log Analytics çalışma alanının kimliği. |
| referencedResources |Çözümde, çözüm kaldırıldığında kaldırılmamalıdır. |
| containedResources |Çözümde, çözüm kaldırıldığında kaldırılması gereken kaynakların listesi. |

Yukarıdaki örnek, runbook 'un, zamanlamanın ve görünümün bulunduğu bir çözüm içindir.  Zamanlama ve Runbook 'un, çözüm kaldırıldığında kaldırılmaması için **Özellikler** öğesinde *Başvurulmuş* .  Görünüm, çözüm kaldırıldığında kaldırılacak şekilde *bulunur* .

### <a name="plan"></a>Plan
Çözüm kaynağının **plan** varlığı aşağıdaki tablodaki özelliklere sahiptir.

| Özellik | Açıklama |
|:--- |:--- |
| ad |Çözümün adı. |
| version |Yazar tarafından belirlendiği şekilde çözümün sürümü. |
| ürün |Çözümü tanımlayacak benzersiz dize. |
| 'ın |Çözümün yayımcısı. |



## <a name="next-steps"></a>Sonraki adımlar
* [Kayıtlı aramaları ve uyarıları](solutions-resources-searches-alerts.md) yönetim çözümünüze ekleyin.
* Yönetim çözümünüze [Görünümler ekleyin](solutions-resources-views.md) .
* Yönetim çözümünüze [runbook 'ları ve diğer otomasyon kaynaklarını ekleyin](solutions-resources-automation.md) .
* [Yazma Azure Resource Manager şablonlarının](../../azure-resource-manager/resource-group-authoring-templates.md)ayrıntılarını öğrenin.
* Farklı Kaynak Yöneticisi şablonlarının örnekleri için [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/documentation/templates) arama yapın.
