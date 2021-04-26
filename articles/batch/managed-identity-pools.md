---
title: Batch havuzlarında yönetilen kimlikleri yapılandırma
description: Toplu havuzlarda Kullanıcı tarafından atanan yönetilen kimlikleri ve düğümlerin içinde yönetilen kimliklerin nasıl kullanılacağını öğrenin.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: d69e983a4b17298150942c924a3c694e2cceaf72
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967260"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Batch havuzlarında yönetilen kimlikleri yapılandırma

[Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , Azure Active Directory (Azure AD) içinde Azure kaynağına bir kimlik sağlayarak ve Azure Active Directory (Azure AD) belirteçleri almak için kullanarak kimlik bilgilerini yönetmek zorunda olan geliştiricilerin gereksinimini ortadan kaldırır.

Bu konuda, toplu havuzlarda Kullanıcı tarafından atanan yönetilen kimliklerin nasıl etkinleştirileceği ve düğümlerin içinde yönetilen kimliklerin nasıl kullanılacağı açıklanmaktadır.

> [!IMPORTANT]
> Kullanıcı tarafından atanan yönetilen kimliklerle Azure Batch havuzlara yönelik destek şu bölgeler için şu anda genel önizlemededir: Batı ABD 2, Orta Güney ABD, Doğu ABD, US Gov Arizona ve US Gov Virginia.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik oluşturma

İlk olarak, [Kullanıcı tarafından atanan yönetilen kimliğinizi](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) Batch hesabınızla aynı kiracıda oluşturun. Bu yönetilen kimliğin aynı kaynak grubunda olması veya aynı abonelikte bile olması gerekmez.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Kullanıcı tarafından atanan yönetilen kimliklerle bir Batch havuzu oluşturma

Kullanıcı tarafından atanan bir veya daha fazla yönetilen kimlik oluşturduktan sonra [Batch .NET Yönetim Kitaplığı](/dotnet/api/overview/azure/batch#management-library)'nı kullanarak bu yönetilen kimlikle bir Batch havuzu oluşturabilirsiniz.

> [!IMPORTANT]
> Yönetilen kimlikleri kullanmak için havuzların [sanal makine yapılandırması](nodes-and-pools.md#virtual-machine-configuration) kullanılarak yapılandırılması gerekir.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        },
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> Yönetilen kimliklerle havuz oluşturma Işlemi Şu anda [Batch .NET istemci kitaplığı](/dotnet/api/overview/azure/batch#client-library)ile desteklenmiyor.

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Batch düğümlerinde Kullanıcı tarafından atanan Yönetilen kimlikler kullanma

Havuzlarınızı oluşturduktan sonra, Kullanıcı tarafından atanan yönetilen kimlikleriniz Secure Shell (SSH) veya uzak masaüstü (RDP) aracılığıyla havuz düğümlerine erişebilir. Ayrıca, yönetilen kimliklerin yönetilen kimlikleri [destekleyen Azure kaynaklarına](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)doğrudan erişebilmesi için görevlerinizi yapılandırabilirsiniz.

Toplu Iş düğümleri içinde, yönetilen kimlik belirteçlerini alabilir ve Azure [Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)aracılığıyla Azure AD kimlik doğrulaması aracılığıyla kimlik doğrulaması yapmak için bunları kullanabilirsiniz.

Windows için, kimlik doğrulaması için bir erişim belirteci almak üzere PowerShell betiği şunlardır:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Linux için bash betiği şu şekilde olur:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Daha fazla bilgi için bkz. bir [Azure VM 'de Azure kaynakları için Yönetilen kimlikler kullanarak erişim belirteci alma](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.
- [Müşteri tarafından yönetilen anahtarların Kullanıcı tarafından yönetilen kimliklerle](batch-customer-managed-key.md)nasıl kullanılacağını öğrenin.
- [Bir Batch havuzunda otomatik sertifika döndürmeyi etkinleştirmeyi](automatic-certificate-rotation.md)öğrenin.
