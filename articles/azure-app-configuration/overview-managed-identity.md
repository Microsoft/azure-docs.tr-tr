---
title: Azure Uygulama yapılandırması ile yönetilen kimlikleri yapılandırma
description: Yönetilen kimliklerin Azure Uygulama yapılandırması 'nda nasıl çalıştığını ve yönetilen bir kimliğin nasıl yapılandırılacağını öğrenin
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e4fdff2515dde941b2e9037a21ad931ac27b6fef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764234"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Azure Uygulama yapılandırması için Yönetilen kimlikler kullanma

Bu konu başlığı altında, Azure Uygulama yapılandırması için yönetilen bir kimlik nasıl oluşturacağınız gösterilmektedir. Azure Active Directory (AAD) tarafından yönetilen bir kimlik, Azure uygulama yapılandırmasının, Azure Key Vault gibi diğer AAD korumalı kaynaklara kolayca erişmesini sağlar. Kimlik, Azure platformu tarafından yönetiliyor. Herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. AAD 'deki Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

Uygulamanıza iki tür kimlik verilebilir:

- **Sistem tarafından atanan bir kimlik** yapılandırma deponuza bağlıdır. Yapılandırma depolu, silinirse silinir. Yapılandırma deposunda yalnızca bir sistem tarafından atanan kimlik olabilir.
- **Kullanıcı tarafından atanan bir kimlik** , yapılandırma deponuza atanabilecek tek başına bir Azure kaynağıdır. Bir yapılandırma deposunda birden çok kullanıcı tarafından atanan kimlik olabilir.

## <a name="adding-a-system-assigned-identity"></a>Sistem tarafından atanan kimlik ekleme

Sistem tarafından atanan kimlik ile uygulama yapılandırma deposu oluşturmak, depoda ek bir özelliğin ayarlanmasını gerektirir.

### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

Azure CLı kullanarak yönetilen bir kimlik ayarlamak için, var olan bir yapılandırma deposunda [az appconfig Identity Assign] komutunu kullanın. Bu bölümde örnekleri çalıştırmak için üç seçeneğiniz vardır:

- Azure portal [Azure Cloud Shell](../cloud-shell/overview.md) kullanın.
- Aşağıdaki her kod bloğunun sağ üst köşesinde bulunan "dene" düğmesini kullanarak katıştırılmış Azure Cloud Shell kullanın.
- Yerel bir CLı konsolu kullanmayı tercih ediyorsanız, [en son Azure CLI sürümünü](/cli/azure/install-azure-cli) (2,1 veya üzeri) yükleyebilirsiniz.

Aşağıdaki adımlar, bir uygulama yapılandırma deposu oluşturma ve CLı kullanarak bir kimlik atama işleminde size kılavuzluk eder:

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login] kullanarak Azure'da oturum açın. Azure aboneliğinizle ilişkili bir hesap kullanın:

    ```azurecli-interactive
    az login
    ```

1. CLı kullanarak bir uygulama yapılandırma deposu oluşturun. CLı 'yı Azure Uygulama yapılandırması ile birlikte kullanmaya ilişkin daha fazla örnek için bkz. [uygulama YAPıLANDıRMASı CLI örnekleri](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Bu yapılandırma deposu için sistem tarafından atanan kimliği oluşturmak için [az appconfig Identity Assign] komutunu çalıştırın:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik ekleme

Kullanıcı tarafından atanan bir kimlikle uygulama yapılandırma deposu oluşturmak için kimlik oluşturmanız ve ardından kaynak tanımlayıcısını deponuza atamanız gerekir.

### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

Azure CLı kullanarak yönetilen bir kimlik ayarlamak için, var olan bir yapılandırma deposunda [az appconfig Identity Assign] komutunu kullanın. Bu bölümde örnekleri çalıştırmak için üç seçeneğiniz vardır:

- Azure portal [Azure Cloud Shell](../cloud-shell/overview.md) kullanın.
- Aşağıdaki her kod bloğunun sağ üst köşesinde bulunan "dene" düğmesini kullanarak katıştırılmış Azure Cloud Shell kullanın.
- Yerel bir CLı konsolu kullanmayı tercih ediyorsanız, [en son Azure CLI sürümünü](/cli/azure/install-azure-cli) (2.0.31 veya üzeri) yükleyebilirsiniz.

Aşağıdaki adımlar, Kullanıcı tarafından atanan bir kimlik ve bir uygulama yapılandırma deposu oluşturma ve CLı kullanarak kimliği depoya atama konusunda size kılavuzluk eder:

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login] kullanarak Azure'da oturum açın. Azure aboneliğinizle ilişkili bir hesap kullanın:

    ```azurecli-interactive
    az login
    ```

1. CLı kullanarak bir uygulama yapılandırma deposu oluşturun. CLı 'yı Azure Uygulama yapılandırması ile birlikte kullanmaya ilişkin daha fazla örnek için bkz. [uygulama YAPıLANDıRMASı CLI örnekleri](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. CLı kullanılarak adlı Kullanıcı tarafından atanan bir kimlik oluşturun `myUserAssignedIdentity` .

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Bu komutun çıkışında, özelliğinin değerini aklınızda bir değer `id` .

1. Bu yapılandırma deposuna Kullanıcı tarafından atanan yeni kimliği atamak için [az appconfig Identity Assign] komutunu çalıştırın. `id`Önceki adımda not ettiğiniz özelliğin değerini kullanın.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Kimlik kaldırma

Azure CLı 'de [az appconfig Identity Remove](/cli/azure/appconfig/identity#az_appconfig_identity_remove) komutu kullanılarak özelliği devre dışı bırakarak, sistem tarafından atanan bir kimlik kaldırılabilir. Kullanıcı tarafından atanan kimlikler tek tek kaldırılabilir. Sistem tarafından atanan bir kimliğin bu şekilde kaldırılması, AAD 'den de silinecek. Uygulama kaynağı silindiğinde, sistem tarafından atanan kimlikler de AAD 'den otomatik olarak kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Uygulama yapılandırması ile ASP.NET Core uygulaması oluşturma](quickstart-aspnet-core-app.md)

[az appconfig Identity Assign]: /cli/azure/appconfig/identity#az_appconfig_identity_assign
[az login]: /cli/azure/reference-index#az_login
