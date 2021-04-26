---
title: Power BI çıkışı için Azure Stream Analytics işinizin kimliğini doğrulamak üzere yönetilen kimliği kullanın
description: Bu makalede, Power BI çıkışı için Azure Stream Analytics işinizin kimliğini doğrulamak üzere yönetilen kimliklerin nasıl kullanılacağı açıklanır.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589693"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Azure Stream Analytics işinizin kimliğini doğrulamak için yönetilen kimliği kullanın Power BI

Power BI çıkışı için [yönetilen kimlik kimlik doğrulaması](../active-directory/managed-identities-azure-resources/overview.md) , Stream Analytics işlerinin Power BI hesabınızdaki bir çalışma alanına doğrudan erişmesini sağlar. Bu özellik, bir kullanıcının Azure portal aracılığıyla Power BI etkileşimli olarak oturum açması için artık gerekli olmadığından, Stream Analytics işlerin dağıtımlarının tamamen otomatikleştirilmesi için izin verir. Ayrıca, işi düzenli olarak yeniden sağlamak zorunda kalabileceğinizden, Power BI yazan uzun süren işler artık daha iyi destekleniyor.

Bu makalede, Azure portal ve Azure Resource Manager dağıtımı aracılığıyla bir Stream Analytics işinin Power BI çıktıları için yönetilen kimliğin nasıl etkinleştirileceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmak için aşağıdakiler gereklidir:

- [Pro lisansına](/power-bi/service-admin-purchasing-power-bi-pro)sahip Power BI hesabı.

- Power BI hesabınızda yükseltilen bir çalışma alanı. Daha fazla bilgi için bu özelliğin [duyurusunu Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) bakın.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Azure portal kullanarak Stream Analytics işi oluşturma

1. Yeni bir Stream Analytics işi oluşturun veya Azure portal var olan bir işi açın. Ekranın sol tarafındaki menü çubuğundan **Yapılandır**' ın altında bulunan **yönetilen kimlik** ' i seçin. "Sistem tarafından atanan yönetilen kimliği kullan" öğesinin seçili olduğundan emin olun ve ardından ekranın alt kısmındaki **Kaydet** düğmesini seçin.

   ![Stream Analytics yönetilen kimliği yapılandırma](./media/common/stream-analytics-enable-managed-identity.png)

2. Çıktıyı yapılandırmadan önce, bu makalenin [Power BI çalışma alanınıza Stream Analytics iş erişimini verme](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) bölümündeki yönergeleri izleyerek Stream Analytics işi Power BI çalışma alanınıza erişim izni verin.

3. Stream analitik işinin **çıktılar** bölümüne gidin, **+ Ekle**' yi seçin ve ardından **Power BI**' yi seçin. Sonra **Yetkilendir** düğmesini seçin ve Power BI hesabınızla oturum açın.

   ![Power BI hesabıyla yetkilendir](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Yetkilendirildikten sonra, erişiminiz olan tüm çalışma alanları ile bir açılan liste doldurulur. Önceki adımda yetkilendirdiğiniz çalışma alanını seçin. Ardından "kimlik doğrulama modu" olarak **yönetilen kimlik** ' i seçin. Son olarak **Kaydet** düğmesini seçin.

   ![Yönetilen kimlikle Power BI çıkışını yapılandırma](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager dağıtımı

Azure Resource Manager, Stream Analytics işinizin dağıtımını tamamen otomatikleştirmenizi sağlar. Azure PowerShell ya da [Azure CLI](/cli/azure/)kullanarak kaynak yöneticisi şablonlarını dağıtabilirsiniz. Aşağıdaki örneklerde Azure CLı kullanılır.


1. Kaynak Yöneticisi şablonunuzun kaynak bölümüne aşağıdaki özelliği ekleyerek, yönetilen bir kimlikle bir **Microsoft. StreamAnalytics/streamingjobs** kaynağı oluşturabilirsiniz:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Bu özellik, Azure Resource Manager Stream Analytics işiniz için kimlik oluşturup yönetmeyeceğini söyler. Aşağıda yönetilen kimlik etkin bir Stream Analytics iş dağıtan bir Kaynak Yöneticisi şablonu ve yönetilen kimlik kullanan bir Power BI çıkış havuzu verilmiştir:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Aşağıdaki Azure CLı komutunu kullanarak yukarıdaki işi kaynak grubu **örnek grubuna** dağıtın:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. İş oluşturulduktan sonra, işin tam tanımını almak için Azure Resource Manager kullanın.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Yukarıdaki komut aşağıdaki gibi bir yanıt döndürür:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Stream Analytics işini Power BI çalışma alanınıza eklemek için Power BI REST API kullanmayı planlıyorsanız, döndürülen "PrincipalId" konumunu unutmayın.

3. İş oluşturulduktan sonra, bu makalenin [Power BI çalışma alanınıza Stream Analytics işi erişimi verme](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) bölümüne devam edin.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Stream Analytics işi Power BI çalışma alanınıza erişmesine izin verin

Stream Analytics işi oluşturuldığına göre, bir Power BI çalışma alanına erişim izni verilebilir. İş erişiminizi aldıktan sonra, kimliğin yayılması için birkaç dakika bekleyin.

### <a name="use-the-power-bi-ui"></a>Power BI Kullanıcı arabirimini kullanma

   > [!Note]
   > Kullanıcı arabirimini kullanarak Stream Analytics işini Power BI çalışma alanınıza eklemek için, Power BI yönetici portalındaki **Geliştirici ayarlarında** hizmet sorumlusu erişimini de etkinleştirmeniz gerekir. Daha fazla ayrıntı için bkz. [hizmet sorumlusu ile çalışmaya başlama](/power-bi/developer/embed-service-principal) .

1. Çalışma alanının erişim ayarlarına gidin. Daha ayrıntılı bilgi için bu makaleye bakın: [çalışma alanınıza erişim Izni verin](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Stream Analytics işinizin adını metin kutusuna yazın ve erişim düzeyi olarak **katkıda bulunan** ' i seçin.

3. **Ekle** ' yi seçin ve bölmeyi kapatın.

   ![Power BI çalışma alanına Stream Analytics işi ekleme](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Power BI PowerShell cmdlet 'lerini kullanma

1. Power BI `MicrosoftPowerBIMgmt` PowerShell cmdlet 'lerini yükler.

   > [!Important]
   > Lütfen cmdlet 'lerinin 1.0.821 veya sonraki bir sürümünü kullandığınızdan emin olun.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Power BI oturum açın.

```powershell
Login-PowerBI
```

3. Stream Analytics işinizi çalışma alanına katkıda bulunan olarak ekleyin.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Power BI kullanın REST API

Stream Analytics işi, doğrudan "Grup kullanıcısı ekle" REST API kullanılarak çalışma alanına katkıda bulunan olarak da eklenebilir. Bu API için tam belge buradan bulunabilir: [Gruplar-Grup kullanıcısı ekle](/rest/api/power-bi/groups/addgroupuser).

**Örnek İstek**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
İstek Gövdesi
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Yönetilen kimliği kaldır

Stream Analytics işi için oluşturulan yönetilen kimlik yalnızca iş silindiğinde silinir. İşi silmeden yönetilen kimliği silmenin bir yolu yoktur. Artık yönetilen kimliği kullanmak istemiyorsanız, çıkışın kimlik doğrulama yöntemini değiştirebilirsiniz. Yönetilen kimlik, iş silinene kadar mevcut olmaya devam eder ve yönetilen kimlik kimlik doğrulamasını yeniden kullanmaya karar verirseniz kullanılacaktır.

## <a name="limitations"></a>Sınırlamalar
Bu özelliğin sınırlamaları aşağıda verilmiştir:

- Klasik Power BI çalışma alanları desteklenmez.

- Azure Active Directory olmayan Azure hesapları.

- Çok kiracılı erişim desteklenmez. Belirli bir Stream Analytics iş için oluşturulan hizmet sorumlusu, işin oluşturulduğu Azure Active Directory kiracısında bulunmalı ve farklı bir Azure Active Directory kiracısında bulunan bir kaynakla birlikte kullanılamaz.

- [Kullanıcı tarafından atanan kimlik](../active-directory/managed-identities-azure-resources/overview.md) desteklenmiyor. Bu, Stream Analytics işleri tarafından kullanılmak üzere kendi hizmet sorumlunuzu giremeyeceğiniz anlamına gelir. Hizmet sorumlusu Azure Stream Analytics ile oluşturulmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics ile pano tümleştirmesi Power BI](./stream-analytics-power-bi-dashboard.md)
* [Azure Stream Analytics çıkışlarını anlayın](./stream-analytics-define-outputs.md)
