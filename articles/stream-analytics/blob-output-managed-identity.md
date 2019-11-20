---
title: Yönetilen kimlik Azure Stream Analytics blob çıkışının kimliğini doğrulama
description: Bu makalede, Azure Blob depolama çıktısında Azure Stream Analytics işinizin kimliğini doğrulamak için yönetilen kimliklerin nasıl kullanılacağı açıklanır.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1854a99cac3ead386f38c1c7fe27a56b672f365c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664316"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output-preview"></a>Azure Stream Analytics işinizin Azure Blob depolama çıkışına (Önizleme) kimliğini doğrulamak için yönetilen kimlik kullanma

Azure Blob depolama 'ya çıkış için [yönetilen kimlik kimlik doğrulaması](../active-directory/managed-identities-azure-resources/overview.md) , önizleme olarak Azure Stream Analytics kullanılabilir. Bu, Stream Analytics işlerin bir bağlantı dizesi kullanmak yerine bir depolama hesabına doğrudan erişmesini sağlar. Bu özellik, geliştirilmiş güvenliğe ek olarak Azure içindeki bir sanal ağdaki (VNET) bir depolama hesabına veri yazmanızı de sağlar.

Bu makalede, Azure portal ve Azure Resource Manager dağıtımı aracılığıyla bir Stream Analytics işinin blob çıkışları için yönetilen kimliğin nasıl etkinleştirileceği gösterilmektedir.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Azure portal kullanarak Stream Analytics işi oluşturun

1. Yeni bir Stream Analytics işi oluşturun veya Azure portal var olan bir işi açın. Ekranın sol tarafındaki menü çubuğundan **Yapılandır**' ın altında bulunan **yönetilen kimlik** ' i seçin. "Sistem tarafından atanan yönetilen kimliği kullan" öğesinin seçili olduğundan emin olun ve ardından ekranın alt kısmındaki **Kaydet** düğmesine tıklayın.

   ![Stream Analytics yönetilen kimliği yapılandırma](./media/common/stream-analytics-enable-managed-identity.png)

2. Azure Blob depolama çıkış havuzunun çıkış özellikleri penceresinde kimlik doğrulama modu açılan listesini seçin ve **yönetilen kimlik**' i seçin. Diğer çıkış özellikleriyle ilgili daha fazla bilgi için bkz. [Azure Stream Analytics çıkışları anlama](./stream-analytics-define-outputs.md). İşiniz bittiğinde **Kaydet**' e tıklayın.

   ![Azure Blob depolama çıkışını yapılandırma](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Artık iş oluşturulduktan sonra, bu makalenin [depolama hesabınıza Stream Analytics işi erişimi verme](#give-the-stream-analytics-job-access-to-your-storage-account) bölümüne bakın.

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager dağıtımı

Azure Resource Manager kullanmak Stream Analytics işinizin dağıtımını tamamen otomatikleştirmenizi sağlar. Azure PowerShell ya da [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)kullanarak kaynak yöneticisi şablonlarını dağıtabilirsiniz. Aşağıdaki örneklerde Azure CLı kullanılır.


1. Kaynak Yöneticisi şablonunuzun kaynak bölümüne aşağıdaki özelliği ekleyerek, yönetilen bir kimlikle bir **Microsoft. StreamAnalytics/streamingjobs** kaynağı oluşturabilirsiniz:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Bu özellik, Azure Resource Manager Stream Analytics işiniz için kimlik oluşturup yönetmeyeceğini söyler. Aşağıda yönetilen kimlik etkin bir Stream Analytics iş dağıtan ve yönetilen kimlik kullanan bir blob çıkış havuzu olan bir örnek Kaynak Yöneticisi şablonu verilmiştir:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    Yukarıdaki iş, aşağıdaki Azure CLı komutu kullanılarak kaynak grubu **examplegroup** 'a dağıtılabilir:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. İş oluşturulduktan sonra, işin tam tanımını almak için Azure Resource Manager kullanabilirsiniz.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Yukarıdaki komut aşağıdaki gibi bir yanıt döndürür:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   İş tanımından, işinizin yönetilen kimliğini tanımlayan Azure Active Directory ve depolama hesabına Stream Analytics iş erişimi vermek için bir sonraki adımda kullanılacak olan **PrincipalId** ' yi bir yere göz atın.

3. Artık iş oluşturulduktan sonra, bu makalenin [depolama hesabınıza Stream Analytics işi erişimi verme](#give-the-stream-analytics-job-access-to-your-storage-account) bölümüne bakın.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Stream Analytics işi depolama hesabınıza erişmesine izin verin

Stream Analytics işinizi sağlamak için seçebileceğiniz iki erişim düzeyi vardır:

1. **Kapsayıcı düzeyinde erişim:** Bu seçenek, iş için var olan belirli bir kapsayıcıya erişim sağlar.
2. **Hesap düzeyinde erişim:** Bu seçenek, yeni kapsayıcılar oluşturma özelliği de dahil olmak üzere, depolama hesabına genel erişim sağlar.

İş yerinize kapsayıcı oluşturmaya gerek duymadığınız takdirde, bu seçenek iş için gereken en düşük erişim düzeyine izin verdiklerinden, **kapsayıcı düzeyi erişim** ' i seçmeniz gerekir. Her iki seçenek de Azure portal ve komut satırı için aşağıda açıklanmıştır.

### <a name="grant-access-via-the-azure-portal"></a>Azure portal ile erişim izni verme

#### <a name="container-level-access"></a>Kapsayıcı düzeyinde erişim

1. Depolama hesabınızın içindeki kapsayıcının yapılandırma bölmesine gidin.

2. Sol taraftaki **Access Control (IAM)** seçeneğini belirleyin.

3. "Rol ataması Ekle" bölümünde **Ekle**' ye tıklayın.

4. Rol atama bölmesinde:

    1. **Rolü** "Depolama Blobu verileri katılımcısı" olarak ayarlayın
    2. Açılan listeye **erişim ata erişimi** 'Nin "Azure AD Kullanıcı, Grup veya hizmet sorumlusu" olarak ayarlandığından emin olun.
    3. Arama alanına Stream Analytics işinizin adını yazın.
    4. Stream Analytics işinizi seçin ve **Kaydet**' e tıklayın.

   ![Kapsayıcı erişimi verme](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Hesap düzeyinde erişim

1. Depolama hesabınıza gidin.

2. Sol taraftaki **Access Control (IAM)** seçeneğini belirleyin.

3. "Rol ataması Ekle" bölümünde **Ekle**' ye tıklayın.

4. Rol atama bölmesinde:

    1. **Rolü** "Depolama Blobu verileri katılımcısı" olarak ayarlayın
    2. Açılan listeye **erişim ata erişimi** 'Nin "Azure AD Kullanıcı, Grup veya hizmet sorumlusu" olarak ayarlandığından emin olun.
    3. Arama alanına Stream Analytics işinizin adını yazın.
    4. Stream Analytics işinizi seçin ve **Kaydet**' e tıklayın.

   ![Hesap erişimi verme](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Komut satırı aracılığıyla erişim izni verme

#### <a name="container-level-access"></a>Kapsayıcı düzeyinde erişim

Belirli bir kapsayıcıya erişim vermek için, Azure CLı kullanarak aşağıdaki komutu çalıştırın:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Hesap düzeyinde erişim

Tüm hesaba erişim vermek için, Azure CLı kullanarak aşağıdaki komutu çalıştırın:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET erişimini etkinleştir

Depolama hesabınızın **güvenlik duvarlarını ve sanal ağlarını**yapılandırırken, isteğe bağlı olarak diğer güvenilir Microsoft hizmetlerinden gelen ağ trafiğine izin verebilirsiniz. Yönetilen kimliği kullanarak kimlik doğrulaması Stream Analytics, isteğin güvenilir bir hizmetten kaynaklanan kanıt sağlar. Aşağıda, bu VNET erişim özel durumunun etkinleştirilmesi için yönergeler verilmiştir.

1.  Depolama hesabının yapılandırma bölmesi içindeki "güvenlik duvarları ve sanal ağlar" bölmesine gidin.
2.  "Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver" seçeneğinin etkin olduğundan emin olun.
3.  Etkinleştirilirse **Kaydet**' e tıklayın.

   ![VNET erişimini etkinleştir](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Sınırlamalar
Bu özelliğin geçerli sınırlamaları aşağıda verilmiştir:

1. Klasik Azure depolama hesapları.

2. Azure Active Directory olmayan Azure hesapları.

3. Çok kiracılı erişim desteklenmez. Belirli bir Stream Analytics iş için oluşturulan hizmet sorumlusu, işin oluşturulduğu Azure Active Directory kiracısında bulunmalı ve farklı bir Azure Active Directory kiracısında bulunan bir kaynakla birlikte kullanılamaz.

4. [Kullanıcı tarafından atanan kimlik](../active-directory/managed-identities-azure-resources/overview.md) desteklenmiyor. Bu, kullanıcının Stream Analytics işleri tarafından kullanılmak üzere kendi hizmet sorumlusunu giremediği anlamına gelir. Hizmet sorumlusu Azure Stream Analytics ile oluşturulmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics çıkışlarını anlayın](./stream-analytics-define-outputs.md)
* [Özel blob çıkış bölümlendirme Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
