---
title: 'Hızlı başlangıç: Python ile bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için Python 'u kullanırsınız.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9aec47e067ca62f4902df2dafb6a5d6d50a26d0e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533173"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Hızlı başlangıç: Python ile bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Kaynak grafik kitaplığını ekleme

Python 'un Yönetim gruplarını yönetmesine olanak tanımak için, kitaplığın eklenmesi gerekir. Bu kitaplık, [Windows 10 ' da Bash](/windows/wsl/install-win10) veya yerel olarak yüklü dahil olmak üzere Python 'un kullanılabileceği her yerde çalışmaktadır.

1. En son Python 'un yüklü olup olmadığını denetleyin (en az **3,8**). Henüz yüklenmemişse, [Python.org](https://www.python.org/downloads/)adresinden indirin.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.5.1**). Henüz yüklenmemişse bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLı, Python 'un aşağıdaki örneklerde **CLI tabanlı kimlik doğrulamasını** kullanmasını sağlamak için gereklidir. Diğer seçenekler hakkında daha fazla bilgi için bkz. [Python Için Azure Yönetim kitaplıklarını kullanarak kimlik doğrulama](/azure/developer/python/azure-sdk-authenticate).

1. Azure CLı aracılığıyla kimlik doğrulaması yapın.

   ```azurecli
   az login
   ```

1. Tercih ettiğiniz Python ortamınızda, yönetim grupları için gerekli kitaplıkları yükler:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Tüm kullanıcılar için Python yüklüyse, bu komutların yükseltilmiş bir konsoldan çalıştırılması gerekir.

1. Kitaplıkların yüklü olduğunu doğrulayın. `azure-mgmt-managementgroups`**0.2.0** veya üzeri olmalıdır, `azure-mgmt-resource` **9.0.0** veya üzeri olmalıdır ve `azure-cli-core` **2.5.0** veya üzeri olmalıdır.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Yönetim grubunu oluşturma

1. Python betiğini oluşturun ve aşağıdaki kaynağı şu şekilde kaydedin `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. İle Azure CLı ile kimlik doğrulaması yapın `az login` .

1. Terminalde aşağıdaki komutu girin:

   ```bash
   py mgCreate.py
   ```

Yönetim grubu oluşturmanın sonucu konsola bir nesne olarak çıktı olur `LROPoller` .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü kitaplıkları Python ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)
