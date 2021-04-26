---
title: 'Hızlı başlangıç: ilk Python sorgunuz'
description: Bu hızlı başlangıçta, Python için kaynak grafiği kitaplığını etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 0bae0639e3f9913bc47b18fbc0b1d3d9ef1ad324
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533018"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Hızlı başlangıç: Python kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Azure Kaynak Grafiği 'ni kullanmanın ilk adımı, Python için gereken kitaplıkların yüklü olduğunu denetme. Bu hızlı başlangıç, Python yüklemenize kitaplıkları ekleme sürecinde size yol gösterir.

Bu işlemin sonunda, kitaplıkları Python yüklemenize eklemiş ve ilk kaynak Graf sorgunuzu çalıştıracaksınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Kaynak grafik kitaplığını ekleme

Python 'u Azure Kaynak Grafiği 'ni sorgulamak üzere etkinleştirmek için, kitaplığın eklenmesi gerekir. Bu kitaplık, [Windows 10 ' da Bash](/windows/wsl/install-win10) veya yerel olarak yüklü dahil olmak üzere Python 'un kullanılabileceği her yerde çalışmaktadır.

1. En son Python 'un yüklü olup olmadığını denetleyin (en az **3,8**). Henüz yüklenmemişse, [Python.org](https://www.python.org/downloads/)adresinden indirin.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.5.1**). Henüz yüklenmemişse bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLı, Python 'un aşağıdaki örneklerde **CLI tabanlı kimlik doğrulamasını** kullanmasını sağlamak için gereklidir. Diğer seçenekler hakkında daha fazla bilgi için bkz. [Python Için Azure Yönetim kitaplıklarını kullanarak kimlik doğrulama](/azure/developer/python/azure-sdk-authenticate).

1. Azure CLı aracılığıyla kimlik doğrulaması yapın.

   ```azurecli
   az login
   ```

1. Tercih ettiğiniz Python ortamınızda, Azure Kaynak Grafiği için gerekli kitaplıkları yükler:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Tüm kullanıcılar için Python yüklüyse, bu komutların yükseltilmiş bir konsoldan çalıştırılması gerekir.

1. Kitaplıkların yüklü olduğunu doğrulayın. `azure-mgmt-resourcegraph`**2.0.0** veya üzeri olmalıdır, `azure-mgmt-resource` **9.0.0** veya üzeri olmalıdır ve `azure-cli-core` **2.5.0** veya üzeri olmalıdır.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Tercih ettiğiniz ortamınıza eklenen Python kitaplıkları ile basit bir kaynak grafiği sorgusu denemenize zaman atalım. Sorgu, ilk beş Azure kaynağını her bir kaynağın **adı** ve **kaynak türü** ile döndürür.

1. Yüklenen kitaplıkları ve yöntemi kullanarak ilk Azure Kaynak Grafiği sorgunuzu çalıştırın `resources` :

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Bu sorgu örneği, `order by` gibi bir sıralama değiştirici sağlamadığı için, bu sorgunun birden çok kez çalıştırılması muhtemelen istek başına farklı bir kaynak kümesi sunacaktır.

1. Çağrısını güncelleştirin `getresources` ve sorguyu `order by` **ad** özelliği olarak değiştirin:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. Çağrısını güncelleştirin `getresources` ve sorguyu ilk olarak `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca değiştirin:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü kitaplıkları Python ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafik kitaplıklarını Python ortamınıza eklediniz ve ilk sorgunuzu çalıştırdık. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
