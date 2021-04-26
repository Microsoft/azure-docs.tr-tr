---
title: Data Factory için yönetilen kimlik
description: Azure Data Factory için yönetilen kimlik hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: jingwang
ms.openlocfilehash: 65512f8e46b5545929a798392ac5f19ddeab39ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562469"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory için yönetilen kimlik

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makale, yönetilen bir kimliğin Data Factory (eski adıyla Yönetilen Hizmet Kimliği/MSI) ve nasıl çalıştığını anlamanıza yardımcı olur.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış

Bir veri fabrikası oluştururken, yönetilen bir kimlik, fabrika oluşturma ile birlikte oluşturulabilir. Yönetilen kimlik, Azure Active Directory kayıtlı bir yönetilen uygulamadır ve bu belirli veri fabrikasını temsil eder.

Data Factory yönetilen kimliği, aşağıdaki özellikleri avantajlarına yöneliktir:

- [Kimlik bilgilerini](store-credentials-in-key-vault.md), Azure Key Vault kimlik doğrulaması için veri fabrikası tarafından yönetilen kimliğin kullanıldığı Azure Key Vault depolayın.
- Azure Blob depolama, Azure Veri Gezgini, Azure Data Lake Storage 1., Azure Data Lake Storage 2., Azure SQL veritabanı, Azure SQL yönetilen örneği, Azure SYNAPSE Analytics, REST, Databricks etkinliği, Web etkinliği ve daha fazlası dahil olmak üzere yönetilen kimlik kimlik doğrulaması kullanarak veri depolarına veya hesaplayacağından erişin. Ayrıntılar için bağlayıcı ve etkinlik makalelerini inceleyin.

## <a name="generate-managed-identity"></a>Yönetilen kimlik oluştur

Data Factory için yönetilen kimlik şu şekilde oluşturulur:

- **Azure Portal veya PowerShell** aracılığıyla veri fabrikası oluştururken yönetilen kimlik her zaman otomatik olarak oluşturulur.
- **SDK** aracılığıyla veri fabrikası oluştururken, yönetilen kimlik yalnızca, oluşturma için fabrika nesnesinde "Identity = New factoryıdentity ()" belirtirseniz oluşturulur. Bkz. [.net hızlı başlangıç-veri fabrikası oluşturma](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- **REST API** aracılığıyla veri fabrikası oluştururken, yönetilen kimlik yalnızca istek gövdesinde "kimlik" bölümünü belirtirseniz oluşturulur. Bkz. [rest hızlı başlangıç-veri fabrikası oluşturma](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Veri fabrikasının [yönetilen kimlik al](#retrieve-managed-identity) yönergesini izleyerek ilişkili bir yönetilen kimliği yoksa, kimlik başlatıcısı aracılığıyla veri fabrikasını programlı bir şekilde güncelleştirerek bir tane açıkça oluşturabilirsiniz:

- [PowerShell kullanarak yönetilen kimlik oluşturma](#generate-managed-identity-using-powershell)
- [REST API kullanarak yönetilen kimlik oluşturma](#generate-managed-identity-using-rest-api)
- [Azure Resource Manager şablonu kullanarak yönetilen kimlik oluşturma](#generate-managed-identity-using-an-azure-resource-manager-template)
- [SDK kullanarak yönetilen kimlik oluşturma](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Yönetilen kimlik değiştirilemez. Zaten yönetilen bir kimliğe sahip olan bir veri fabrikasının güncelleştirilmesi hiçbir etkiye sahip olmayacaktır, yönetilen kimlik değişmeden tutulur.
>- Fabrika nesnesinde "Identity" parametresi belirtmeden veya REST istek gövdesinde "kimlik" bölümünü belirtmeden yönetilen bir kimliğe sahip bir veri fabrikasını güncelleştirirseniz bir hata alırsınız.
>- Bir veri fabrikasını sildiğinizde, ilişkili yönetilen kimlik de silinir.

### <a name="generate-managed-identity-using-powershell"></a>PowerShell kullanarak yönetilen kimlik oluşturma

**Set-AzDataFactoryV2** komutunu çağırın, ardından yeni oluşturulan "kimlik" alanlarını görürsünüz:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>REST API kullanarak yönetilen kimlik oluşturma

İstek gövdesinde "Identity" bölümüyle API 'YI çağırın:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**İstek gövdesi**: "Identity": {"Type": "SystemAssigned"} ekleyin.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Yanıt**: yönetilen kimlik otomatik olarak oluşturulur ve "kimlik" bölümü buna göre doldurulur.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak yönetilen kimlik oluşturma

**Şablon**: "Identity": {"Type": "SystemAssigned"} ekleyin.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>SDK kullanarak yönetilen kimlik oluşturma

Data Factory create_or_update işlevini Identity = New Factoryıdentity () ile çağırın. .NET kullanan örnek kod:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Yönetilen kimliği al

Yönetilen kimliği Azure portal veya programlı bir şekilde alabilirsiniz. Aşağıdaki bölümlerde bazı örnekler gösterilmektedir.

>[!TIP]
> Yönetilen kimliği görmüyorsanız, fabrikanızı güncelleştirerek [yönetilen kimlik oluşturun](#generate-managed-identity) .

### <a name="retrieve-managed-identity-using-azure-portal"></a>Azure portal kullanarak yönetilen kimlik alma

Data Factory-> özelliklerinizi Azure portal > yönetilen kimlik bilgilerini bulabilirsiniz.

- Yönetilen kimlik nesnesi KIMLIĞI
- Yönetilen kimlik kiracısı

Yönetilen kimlik bilgileri, Azure blob, Azure Data Lake Storage, Azure Key Vault vb. gibi yönetilen kimlik doğrulamasını destekleyen bağlı hizmet oluştururken de görüntülenir.

İzin verirken, Azure kaynağının Access Control (ıAM) sekmesinde > rol ataması ekleyin-> > erişim ata-Data Factory sistem tarafından atanan yönetilen kimlik altında > seçin; fabrika adına göre seçin; veya genel olarak, bu kimliği bulmak için nesne KIMLIĞINI veya Veri Fabrikası adını (yönetilen kimlik adı olarak) kullanabilirsiniz. Yönetilen kimliğin uygulama KIMLIĞI ' ni almanız gerekiyorsa PowerShell kullanabilirsiniz.

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell kullanarak yönetilen kimlik alma

Yönetilen kimlik sorumlusu KIMLIĞI ve kiracı KIMLIĞI, belirli bir veri fabrikasını aşağıdaki şekilde aldığınızda döndürülür. Erişim vermek için **PrincipalId** kullanın:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Uygulama KIMLIĞI ' ni yukarıdaki asıl KIMLIĞI kopyalayarak, ardından aşağıdaki Azure Active Directory komutu bir parametre olarak asıl KIMLIK ile çalıştırabilirsiniz.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>REST API kullanarak yönetilen kimlik alma

Yönetilen kimlik sorumlusu KIMLIĞI ve kiracı KIMLIĞI, belirli bir veri fabrikasını aşağıdaki şekilde aldığınızda döndürülür.

İstekte aşağıdaki API 'YI çağırın:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Yanıt**: aşağıdaki örnekte gösterildiği gibi yanıt alacaksınız. "Kimlik" bölümü buna göre doldurulur.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Bir ARM şablonundan yönetilen kimliği almak için ARM JSON içinde bir **çıktılar** bölümü ekleyin:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından yönetilen kimliğin ne zaman ve nasıl kullanılacağını tanıtan aşağıdaki konulara bakın:

- [Kimlik bilgilerini Azure Key Vault içinde depola](store-credentials-in-key-vault.md)
- [Azure kaynakları kimlik doğrulaması için Yönetilen kimlikler kullanarak/veya Azure Data Lake Store veri kopyalama](connector-azure-data-lake-store.md)

Veri Fabrikası tarafından yönetilen kimliğin temel aldığı Azure kaynakları için Yönetilen kimlikler hakkında genel bakış için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) .