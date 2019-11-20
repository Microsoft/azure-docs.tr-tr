---
title: Blob veya kuyruk verilerine erişmek için Azure AD kimlik bilgileriyle Azure CLı veya PowerShell komutları çalıştırın | Microsoft Docs
description: Azure CLı ve PowerShell, Azure Depolama Blobu ve kuyruklar verilerinde komut çalıştırmak için Azure AD kimlik bilgileriyle oturum açmayı destekler. Oturum için bir erişim belirteci sağlanır ve çağrı işlemlerine yetki vermek için kullanılır. İzinler, Azure AD güvenlik sorumlusu 'na atanan RBAC rolüne bağımlıdır.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e1c7f4531dee8673cc5b6dfe675e4c793144931e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671097"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Blob veya kuyruk verilerine erişmek için Azure CLı veya PowerShell komutlarını Azure AD kimlik bilgileriyle çalıştırma

Azure depolama, Azure CLı ve PowerShell için, Azure Active Directory (Azure AD) kimlik bilgileriyle oturum açmanızı ve betik komutlarını çalıştırmanızı sağlayan uzantılar sağlar. Azure AD kimlik bilgileriyle Azure CLı veya PowerShell 'de oturum açtığınızda bir OAuth 2,0 erişim belirteci döndürülür. Bu belirteç, sonraki veri işlemlerini blob veya kuyruk depolamaya göre yetkilendirmek için CLı veya PowerShell tarafından otomatik olarak kullanılır. Desteklenen işlemler için artık komutuyla bir hesap anahtarını veya SAS belirtecini iletmeniz gerekmez.

Rol tabanlı erişim denetimi (RBAC) aracılığıyla bir Azure AD güvenlik sorumlusuna blob ve kuyruk verilerine izinler atayabilirsiniz. Azure depolama 'daki RBAC rolleri hakkında daha fazla bilgi için bkz. [RBAC Ile Azure depolama verilerine erişim haklarını yönetme](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Desteklenen işlemler

Uzantılar, kapsayıcılar ve sıralarda işlemler için desteklenir. Çağırabileceği işlemler, Azure CLı veya PowerShell 'de oturum açmak için Azure AD güvenlik sorumlusuna verilen izinlere bağlıdır. Azure depolama kapsayıcıları veya kuyrukları izinleri rol tabanlı erişim denetimi (RBAC) aracılığıyla atanır. Örneğin, **BLOB veri okuyucusu** rolünü atadıysanız, bir kapsayıcıdan veya kuyruktan veri okuyan betik komutlarını çalıştırabilirsiniz. **BLOB veri katılımcısı** rolü atandıysa, bir kapsayıcı veya kuyruğu veya içerdikleri verileri okuyan, yazan veya silen betik komutlarını çalıştırabilirsiniz. 

Bir kapsayıcı veya kuyruktaki her bir Azure depolama işlemi için gereken izinler hakkında daha fazla bilgi için bkz. [OAuth belirteçleriyle depolama Işlemlerini çağırma](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Azure AD kimlik bilgilerini kullanarak CLı komutlarını çağırma

Azure CLI, `--auth-mode` blob ve kuyruk verisi işlemlerine yönelik parametreyi destekler:

- Bir Azure AD güvenlik `login` sorumlusu kullanarak oturum açmak için parametresiniolarakayarlayın.`--auth-mode`
- Hesap için kimlik doğrulama parametresi sağlanmazsa `key` , bir hesap anahtarı sorgulama girişiminde bulunan parametreyieskideğereayarlayın.`--auth-mode` 

Aşağıdaki örnek Azure AD kimlik bilgilerinizi kullanarak Azure CLı 'dan yeni bir depolama hesabında bir kapsayıcının nasıl oluşturulacağını göstermektedir. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın: 

1. Azure CLı sürüm 2.0.46 veya üstünü yüklediğinizden emin olun. Yüklü `az --version` sürümünüzü denetlemek için ' i çalıştırın.

1. Tarayıcı `az login` penceresinde çalıştırın ve kimlik doğrulaması yapın: 

    ```azurecli
    az login
    ```

1. İstediğiniz aboneliği belirtin. [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) ile bir kaynak grubu oluşturun. [Az Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)kullanılarak bu kaynak grubunda bir depolama hesabı oluşturun:

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Kapsayıcıyı oluşturmadan önce, [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rolünü kendinize atayın. Hesap sahibi olsanız bile, depolama hesabında veri işlemleri gerçekleştirmek için açık izinlere sahip olmanız gerekir. RBAC rolleri atama hakkında daha fazla bilgi için bkz. [Azure Blob 'a erişim verme ve Azure Portal RBAC ile kuyruk verileri](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC rol atamalarının yayılması birkaç dakika sürebilir.

1. Azure AD kimlik bilgilerinizi kullanarak kapsayıcıyı oluşturmak için `--auth-mode` parametresi olarak `login` ayarlanmış [az Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) komutunu çağırın:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

`--auth-mode`Parametresiyleilişkili ortam değişkeni. `AZURE_STORAGE_AUTH_MODE` Bir Azure depolama veri işlemine yapılan her çağrıya dahil kaçınmak için ortam değişkeninde uygun değeri belirtebilirsiniz.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD kimlik bilgilerini kullanarak PowerShell komutlarını çağırma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure AD kimlik bilgilerini kullanarak Azure depolama 'da oturum açmak ve sonraki işlemleri çalıştırmak için Azure PowerShell kullanmak için depolama hesabına başvurmak üzere bir depolama bağlamı oluşturun ve `-UseConnectedAccount` parametresini ekleyin.

Aşağıdaki örnek, Azure AD kimlik bilgilerinizi kullanarak Azure PowerShell yeni bir depolama hesabında kapsayıcının nasıl oluşturulacağını gösterir. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure hesabınızda oturum açın:

    ```powershell
    Connect-AzAccount
    ```

    Azure 'da PowerShell ile oturum açma hakkında daha fazla bilgi için bkz. [Azure PowerShell Ile oturum açma](/powershell/azure/authenticate-azureps).

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)çağırarak bir Azure Kaynak grubu oluşturun. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)öğesini çağırarak bir depolama hesabı oluşturun.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)' i çağırarak yeni depolama hesabını belirten depolama hesabı bağlamını alın. Bir depolama hesabında davrandığında, kimlik bilgilerini tekrar tekrar geçirmek yerine bağlama başvurabilirsiniz. Azure AD kimlik bilgilerinizi kullanarak sonraki veri işlemlerini çağırmak için parametresiniekleyin:`-UseConnectedAccount`

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Kapsayıcıyı oluşturmadan önce, [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rolünü kendinize atayın. Hesap sahibi olsanız bile, depolama hesabında veri işlemleri gerçekleştirmek için açık izinlere sahip olmanız gerekir. RBAC rolleri atama hakkında daha fazla bilgi için bkz. [Azure Blob 'a erişim verme ve Azure Portal RBAC ile kuyruk verileri](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC rol atamalarının yayılması birkaç dakika sürebilir.

1. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)çağırarak bir kapsayıcı oluşturun. Bu çağrı önceki adımlarda oluşturulan bağlamı kullandığından, kapsayıcı Azure AD kimlik bilgileriniz kullanılarak oluşturulur. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Sonraki adımlar

- Azure depolama için RBAC rolleri hakkında daha fazla bilgi edinmek için bkz. [RBAC ile depolama verileri için erişim haklarını yönetme](storage-auth-aad-rbac.md).
- Azure depolama ile Azure kaynakları için Yönetilen kimlikler kullanma hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile bloblara ve kuyruklara erişim kimlik doğrulaması](storage-auth-aad-msi.md).
- Depolama uygulamalarınızın içindeki kapsayıcılara ve kuyruklara erişimi yetkilendirmeyi öğrenmek için bkz. [Azure AD 'yi depolama uygulamalarıyla kullanma](storage-auth-aad-app.md).
