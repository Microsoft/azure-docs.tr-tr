---
title: Azure Data Factory içinde, şirket içinde barındırılan, paylaşılan bir tümleştirme çalışma zamanı oluşturma
description: Azure Data Factory ' de paylaşılan bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmayı öğrenin. böylece, tümleştirme çalışma zamanına birden çok veri fabrikası erişebilir.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: fcda60863f78dd338bbfc64c1679561262c554a9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677047"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory içinde, şirket içinde barındırılan, paylaşılan bir tümleştirme çalışma zamanı oluşturma

Bu kılavuzda, Azure Data Factory paylaşılan bir şirket içinde barındırılan tümleştirme çalışma zamanının nasıl oluşturulacağı gösterilmektedir. Daha sonra, paylaşılan şirket içinde barındırılan tümleştirme çalışma zamanını başka bir veri fabrikasında kullanabilirsiniz.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Azure Data Factory Kullanıcı arabirimini kullanarak, şirket içinde barındırılan, paylaşılan bir IR oluşturma

Azure Data Factory Kullanıcı arabirimini kullanarak, şirket içinde barındırılan, paylaşılan bir IR oluşturmak için aşağıdaki adımları gerçekleştirebilirsiniz:

1. Paylaşılacak olan şirket içinde barındırılan IR 'de, bağlı IR oluşturmak istediğiniz veri fabrikasına izin verin.
      
    ![Paylaşım sekmesinde izin verme düğmesi](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![İzinleri atamaya yönelik seçimler](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Paylaşılacak, şirket içinde barındırılan IR 'nin kaynak KIMLIĞINI aklınızda olun.
      
   ![Kaynak KIMLIĞININ konumu](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. İzinlerin verildiği veri fabrikasında, yeni bir kendinden konak IR (bağlantılı) oluşturun ve kaynak KIMLIĞINI girin.
      
   ![Bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma düğmesi](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Ad ve kaynak KIMLIĞI için kutular](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Azure PowerShell kullanarak, şirket içinde barındırılan, paylaşılan bir IR oluşturma

Azure PowerShell kullanarak, şirket içinde barındırılan paylaşılan bir IR oluşturmak için aşağıdaki adımları gerçekleştirebilirsiniz: 
1. Veri fabrikası oluşturma. 
1. Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
1. Şirket içinde barındırılan tümleştirme çalışma zamanını diğer veri fabrikaları ile paylaşma.
1. Bağlı bir tümleştirme çalışma zamanı oluşturun.
1. Paylaşımı iptal edin.

### <a name="prerequisites"></a>Ön koşullar 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

- **Azure PowerShell**. [PowerShellGet Ile Windows üzerinde Azure PowerShell Install](https://docs.microsoft.com/powershell/azure/install-az-ps)içindeki yönergeleri izleyin. Diğer veri fabrikaları ile paylaşılabilen şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak için bir betiği çalıştırmak üzere PowerShell 'i kullanırsınız. 

> [!NOTE]  
> Data Factory Şu anda kullanılabildiği Azure bölgelerinin bir listesi için, [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)hakkında sizi ilgilendiren bölgeleri seçin.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Windows PowerShell Tümleşik Komut Dosyası Ortamı’nı (ISE) başlatın.

1. Değişken oluşturun. Aşağıdaki betiği kopyalayıp yapıştırın. **SubscriptionName** ve **resourcegroupname**gibi değişkenleri gerçek değerlerle değiştirin: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Oturum açın ve bir abonelik seçin. Oturum açmak ve Azure aboneliğinizi seçmek için betiğe aşağıdaki kodu ekleyin:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Bir kaynak grubu ve bir veri fabrikası oluşturun.

    > [!NOTE]  
    > Bu adım isteğe bağlıdır. Zaten bir veri fabrikanızı varsa, bu adımı atlayın. 

    [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek, WestEurope konumunda `myResourceGroup` adlı bir kaynak grubu oluşturur: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Veri fabrikası oluşturmak için aşağıdaki komutu çalıştırın: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

> [!NOTE]  
> Bu adım isteğe bağlıdır. Diğer veri fabrikaları ile paylaşmak istediğiniz şirket içinde barındırılan tümleştirme çalışma zamanı zaten varsa, bu adımı atlayın.

Şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Tümleştirme çalışma zamanı kimlik doğrulama anahtarını al ve bir düğümü Kaydet

Şirket içinde barındırılan tümleştirme çalışma zamanının kimlik doğrulama anahtarını almak için aşağıdaki komutu çalıştırın:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Yanıt, bu şirket içinde barındırılan tümleştirme çalışma zamanı için kimlik doğrulama anahtarını içerir. Tümleştirme çalışma zamanı düğümünü kaydettiğinizde bu anahtarı kullanırsınız.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanını yükleyip kaydetme

1. Şirket içinde barındırılan tümleştirme çalışma zamanı yükleyicisini [Azure Data Factory Integration Runtime](https://aka.ms/dmg)indirin.

2. Şirket içinde barındırılan tümleştirmeyi yerel bir bilgisayara yüklemek için yükleyiciyi çalıştırın.

3. Şirket içinde barındırılan yeni tümleştirmeyi, önceki adımda aldığınız kimlik doğrulama anahtarıyla kaydedin.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Şirket içinde barındırılan tümleştirme çalışma zamanını başka bir veri fabrikası ile paylaşma

#### <a name="create-another-data-factory"></a>Başka bir veri fabrikası oluşturma

> [!NOTE]  
> Bu adım isteğe bağlıdır. Paylaşmak istediğiniz Data Factory zaten varsa, bu adımı atlayın.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>İzin ver

Oluşturduğunuz ve kaydettiğiniz şirket içinde barındırılan tümleştirme çalışma zamanına erişmesi gereken veri fabrikasına izin verin.

> [!IMPORTANT]  
> Bu adımı atlayın!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

Bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Artık bağlı bir hizmette bu bağlı tümleştirme çalışma zamanını kullanabilirsiniz. Bağlantılı tümleştirme çalışma zamanı, etkinlikleri çalıştırmak için paylaşılan tümleştirme çalışma zamanını kullanır.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Veri fabrikasından tümleştirme çalışma zamanı paylaşımını iptal etme

Paylaşılan tümleştirme çalışma zamanından bir veri fabrikasının erişimini iptal etmek için aşağıdaki komutu çalıştırın:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Mevcut bağlı tümleştirme çalışma zamanını kaldırmak için, paylaşılan tümleştirme çalışma zamanına karşı aşağıdaki komutu çalıştırın:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory tümleştirme çalışma zamanı kavramlarını](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)gözden geçirin.

- [Azure Portal şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmayı](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)öğrenin.
