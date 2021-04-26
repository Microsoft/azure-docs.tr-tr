---
title: Azure Resource Manager şablonu kullanarak Güncelleştirme Yönetimi’ni etkinleştirme
description: Bu makalede, Güncelleştirme Yönetimi etkinleştirmek için bir Azure Resource Manager şablonunun nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 687c3d49f98fe6832d23dc1529a9761d862e0666
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830889"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak Güncelleştirme Yönetimi’ni etkinleştirme

Kaynak grubunuzda Azure Otomasyonu Güncelleştirme Yönetimi özelliğini etkinleştirmek için bir [Azure Resource Manager şablonu](../../azure-resource-manager/templates/template-syntax.md) kullanabilirsiniz. Bu makale, aşağıdakileri otomatikleştiren örnek bir şablon sağlar:

* Bir Azure Izleyici Log Analytics çalışma alanı oluşturulmasını otomatikleştirir.
* Bir Azure Otomasyonu hesabının oluşturulmasını otomatikleştirir.
* Otomasyon hesabını Log Analytics çalışma alanına bağlar.
* Hesaba örnek Automation runbook 'ları ekler.
* Güncelleştirme Yönetimi özelliğini etkinleştirilir.

Şablon, bir veya daha fazla Azure veya Azure dışı VM üzerinde Güncelleştirme Yönetimi etkinleştirmeyi otomatik hale getirmiyor.

Aboneliğinizde desteklenen bir bölgede dağıtılmış bir Log Analytics çalışma alanı ve Otomasyon hesabı zaten varsa, bunlar bağlanmaz. Bu şablonu kullanmak bağlantıyı başarıyla oluşturur ve Güncelleştirme Yönetimi dağıtır.

>[!NOTE]
>Bir ARM şablonu kullanırken Otomasyon farklı çalıştır hesabının oluşturulması desteklenmez. Portaldan veya PowerShell ile el ile bir farklı çalıştır hesabı oluşturmak için bkz. [Farklı Çalıştır hesabı oluşturma](../create-run-as-account.md).

Bu adımları tamamladıktan sonra, bağlı Log Analytics çalışma alanına runbook iş durumu ve iş akışları göndermek üzere otomasyon hesabınız için [tanılama ayarlarını yapılandırmanız](../automation-manage-send-joblogs-log-analytics.md) gerekir.

## <a name="api-versions"></a>API sürümleri

Aşağıdaki tabloda, bu örnekte kullanılan kaynakların API sürümü listelenmektedir.

| Kaynak | Kaynak türü | API sürümü |
|:---|:---|:---|
| [Çalışma alanı](/azure/templates/microsoft.operationalinsights/workspaces) | çalışma alanı | 2020-03-01-Önizleme |
| [Otomasyon hesabı](/azure/templates/microsoft.automation/automationaccounts) | otomasyon | 2020-01-13-Önizleme |
| [Çalışma alanı bağlı hizmetleri](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | çalışma alanı | 2020-03-01-Önizleme |
| [Çözümler](/azure/templates/microsoft.operationsmanagement/solutions) | çözümler | 2015-11-01-Önizleme |

## <a name="before-using-the-template"></a>Şablonu kullanmadan önce

JSON şablonu şunları isteyecek şekilde yapılandırılır:

* Çalışma alanının adı.
* Çalışma alanının oluşturulacağı bölge.
* Otomasyon hesabının adı.
* Otomasyon hesabının oluşturulacağı bölge.

Şablondaki aşağıdaki parametreler, Log Analytics çalışma alanı için varsayılan bir değerle ayarlanır:

* *SKU* , Nisan 2018 fiyatlandırma MODELINDE yayınlanan GB başına fiyatlandırma katmanını varsayılan olarak alır.
* *Dataretention* varsayılan değer 30 gündür.

>[!WARNING]
>Nisan 2018 fiyatlandırma modelini kabul eden bir abonelikte Log Analytics çalışma alanı oluşturmak veya yapılandırmak istiyorsanız, geçerli Log Analytics fiyatlandırma katmanı yalnızca *PerGB2018* olur.
>

JSON şablonu, ortamınızda standart bir yapılandırma olarak kullanılacak diğer parametreler için varsayılan bir değer belirtir. Şablonu kuruluşunuzda paylaşılan erişim için bir Azure depolama hesabında saklayabilirsiniz. Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [ARM şablonları ve Azure CLI ile kaynak dağıtma](../../azure-resource-manager/templates/deploy-cli.md).

Azure Otomasyonu ve Azure Izleyici 'de yeni başladıysanız aşağıdaki yapılandırma ayrıntılarını anlamanız önemlidir. Yeni otomasyon hesabınıza bağlı bir Log Analytics çalışma alanı oluşturmaya, yapılandırmaya ve kullanmaya çalıştığınızda hatalardan kaçınmanıza yardımcı olabilirler.

* Erişim denetimi modu, fiyatlandırma katmanı, bekletme ve kapasite ayırma düzeyi gibi çalışma alanı yapılandırma seçeneklerini tam olarak anlamak için [ek ayrıntıları](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) gözden geçirin.

* Desteklenen bölgeleri satır içi veya bir parametre dosyasında belirtmek için [çalışma alanı eşlemelerini](../how-to/region-mappings.md) gözden geçirin. Log Analytics çalışma alanını ve aboneliğinizdeki Otomasyon hesabını bağlamak için yalnızca belirli bölgeler desteklenir.

* Azure Izleyici günlüklerine yeni başladıysanız ve bir çalışma alanını henüz dağıtmadıysanız, [çalışma alanı tasarım kılavuzunu](../../azure-monitor/logs/design-logs-deployment.md)gözden geçirmeniz gerekir. Erişim denetimi hakkında bilgi edinmenize ve kuruluşunuz için önerdiğimiz tasarım uygulama stratejilerini anlamanıza yardımcı olur.

## <a name="deploy-template"></a>Şablon dağıtma

1. Aşağıdaki JSON söz dizimini kopyalayıp dosyanıza yapıştırın:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                    "description": "Workspace name"
                }
            },
            "sku": {
                "type": "string",
                "allowedValues": [
                    "pergb2018",
                    "Free",
                    "Standalone",
                    "PerNode",
                    "Standard",
                    "Premium"
                ],
                "defaultValue": "pergb2018",
                "metadata": {
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
                }
            },
            "automationAccountName": {
                "type": "string",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "automationAccountLocation": {
                "type": "string",
                "metadata": {
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Gereksinimlerinizi karşılayacak şekilde şablonu düzenleyin. Parametreleri satır içi değerler olarak geçirmek yerine bir [Kaynak Yöneticisi Parameters dosyası](../../azure-resource-manager/templates/parameter-files.md) oluşturmayı düşünün.

3. Bu dosyayı **deployUMSolutiontemplate.js** olarak yerel bir klasöre kaydedin.

4. Bu şablonu dağıtmaya hazırsınız. PowerShell veya Azure CLı kullanabilirsiniz. Bir çalışma alanı ve Otomasyon hesabı adı istendiğinde, tüm Azure abonelikleri genelinde genel olarak benzersiz bir ad sağlayın.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Dağıtımın tamamlanması birkaç dakika sürebilir. Tamamlandığında, sonucu içeren aşağıdakine benzer bir ileti görürsünüz:

    ![Dağıtım tamamlandığında örnek sonuç](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal oluşturduğunuz Otomasyon hesabını açın.

3. Sol bölmeden **runbook 'lar**' ı seçin. **Runbook 'lar** sayfasında, Otomasyon hesabıyla oluşturulan üç öğretici runbook 'u listelenir.

    ![Otomasyon hesabıyla oluşturulan öğretici runbook 'lar](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Sol bölmeden **bağlantılı çalışma alanı**' nı seçin. **Bağlantılı çalışma alanı** sayfasında, daha önce Otomasyon hesabınıza bağlı olarak belirttiğiniz Log Analytics çalışma alanını gösterir.

    ![Log Analytics çalışma alanına bağlı Otomasyon hesabı](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. Sol bölmeden **güncelleştirme yönetimi**' ni seçin. **Güncelleştirme yönetimi** sayfasında, yalnızca etkinleştirilme sonucu olarak hiçbir bilgi olmadan değerlendirme sayfasını gösterir ve makineler yönetim için yapılandırılmaz.

    ![Güncelleştirme Yönetimi Özellik değerlendirmesi görünümü](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında, Log Analytics çalışma alanındaki **güncelleştirmeler** çözümünü silin, Otomasyon hesabının çalışma alanından bağlantısını kaldırın ve ardından Otomasyon hesabını ve çalışma alanını silin.

## <a name="next-steps"></a>Sonraki adımlar

* VM 'Ler için Güncelleştirme Yönetimi kullanmak için bkz. [VM 'niz için güncelleştirmeleri ve düzeltme eklerini yönetme](manage-updates-for-vm.md).

* Artık Güncelleştirme Yönetimi kullanmak istemiyorsanız ve kaldırmak istiyorsanız, [güncelleştirme yönetimi kaldır özelliğindeki](remove-feature.md)yönergeler bölümüne bakın.

* VM 'Leri Güncelleştirme Yönetimi silmek için bkz. [güncelleştirme yönetimi VM 'Leri kaldırma](remove-vms.md).
