---
title: Portalda çalışma alanları oluşturma
titleSuffix: Azure Machine Learning
description: Azure portal veya Python için SDK ile Azure Machine Learning çalışma alanlarını oluşturmayı, görüntülemeyi ve silmeyi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518934"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning çalışma alanları oluşturma ve yönetme 

Bu makalede, [Python için](/python/api/overview/azure/ml/) Azure Portal veya SDK kullanarak [Azure Machine Learning](overview-what-is-azure-ml.md)için [**Azure Machine Learning çalışma alanları**](concept-workspace.md) oluşturacaksınız, görüntüleyebilir ve silebilirsiniz.

Gereksinimleriniz değiştikçe veya Otomasyon artışı için gereksinimler değiştikçe, [CLI kullanarak](reference-azure-machine-learning-cli.md)veya [vs Code uzantısı aracılığıyla](tutorial-setup-vscode-extension.md)çalışma alanları oluşturabilir ve silebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.
* Python SDK 'yı kullanıyorsanız SDK 'yı [yükler](/python/api/overview/azure/ml/install).

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

Varsayılan olarak, bir çalışma alanı oluşturmak Azure Container Registry (ACR) de oluşturur.  ACR Şu anda kaynak grubu adlarında Unicode karakterleri desteklemediğinden, bu karakterleri içermeyen bir kaynak grubu kullanın.

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

# <a name="python"></a>[Python](#tab/python)

* **Varsayılan belirtim.** Varsayılan olarak, bağımlı kaynaklar ve kaynak grubu otomatik olarak oluşturulur. Bu kod, adlı bir çalışma alanı `myworkspace` ve içinde adlı bir kaynak grubu oluşturur `myresourcegroup` `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    `create_resource_group`Çalışma alanı için kullanmak istediğiniz mevcut bir Azure Kaynak grubunuz varsa, bu değeri false olarak ayarlayın.

* <a name="create-multi-tenant"></a>**Birden çok kiracı.**  Birden çok hesabınız varsa, kullanmak istediğiniz Azure Active Directory kiracı KIMLIĞINI ekleyin.  **Azure Active Directory, dış kimlikler** altındaki [Azure Portal](https://portal.azure.com) kiracı kimliğinizi bulun.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Sogeign bulutu](reference-machine-learning-cloud-parity.md)**. Bir bağımsız bulutu 'nda çalışıyorsanız Azure 'da kimlik doğrulaması yapmak için ek koda ihtiyacınız olacaktır.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Mevcut Azure kaynaklarını kullanın**.  Azure Kaynak KIMLIĞI biçimiyle mevcut Azure kaynaklarını kullanan bir çalışma alanı da oluşturabilirsiniz. Azure portal veya SDK ile ilgili Azure Kaynak kimliklerini bulun. Bu örnek, kaynak grubunun, depolama hesabının, anahtar kasasının, uygulama öngörülerinin ve kapsayıcı kayıt defterinin zaten var olduğunu varsayar.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Daha fazla bilgi için bkz. [çalışma alanı SDK başvurusu](/python/api/azureml-core/azureml.core.workspace.workspace).

Aboneliğinize erişim konusunda sorun yaşıyorsanız, bkz. [Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulamasını ayarlama ve](how-to-setup-authentication.md)Azure Machine Learning Not defteri ['nde kimlik doğrulama](https://aka.ms/aml-notebook-auth) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure aboneliğinizin kimlik bilgilerini kullanarak [Azure Portal](https://portal.azure.com/) oturum açın. 

1. Azure portal sol üst köşesinde **+ kaynak oluştur**' u seçin.

      ![Yeni kaynak oluşturma](./media/how-to-manage-workspace/create-workspace.gif)

1. **Machine Learning** bulmak için arama çubuğunu kullanın.

1. **Machine Learning** seçin.

1. Başlamak için **Machine Learning** bölmesinde **Oluştur** ' u seçin.

1. Yeni çalışma alanınızı yapılandırmak için aşağıdaki bilgileri sağlayın:

   Alan|Açıklama 
   ---|---
   Çalışma alanı adı |Çalışma alanınızı tanımlayan benzersiz bir ad girin. Bu örnekte **docs-WS** kullanıyoruz. Adlar, kaynak grubu genelinde benzersiz olmalıdır. Başkaları tarafından oluşturulan çalışma alanlarını birbirinden ayırmak ve geri çekmek için kolay bir ad kullanın. Çalışma alanı adı büyük/küçük harfe duyarlıdır.
   Abonelik |Kullanmak istediğiniz Azure aboneliğini seçin.
   Kaynak grubu | Aboneliğinizde mevcut kaynak gruplarından birini seçin veya bir ad girerek yeni bir kaynak grubu oluşturun. Kaynak grubu, bir Azure çözümü için ilgili kaynakları barındırır. Bu örnekte **docs-AML** kullanılır. Mevcut bir kaynak grubunu kullanmak için *katkıda bulunan* veya *sahip* rolünün olması gerekir.  Erişim hakkında daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanına erişimi yönetme](how-to-assign-roles.md).
   Region | Çalışma alanınızı oluşturmak için kullanıcılarınıza en yakın Azure bölgesini ve veri kaynaklarını seçin.
   | Depolama hesabı | Çalışma alanı için varsayılan depolama hesabı. Varsayılan olarak, yeni bir tane oluşturulur. |
   | Key Vault | Çalışma alanı tarafından kullanılan Azure Key Vault. Varsayılan olarak, yeni bir tane oluşturulur. |
   | Application Insights | Çalışma alanı için Application Insights örneği. Varsayılan olarak, yeni bir tane oluşturulur. |
   | Container Registry | Çalışma alanı için Azure Container Registry. Varsayılan olarak, çalışma alanı _için başlangıçta yeni bir tane oluşturulmaz._ Bunun yerine, eğitim veya dağıtım sırasında bir Docker görüntüsü oluştururken ihtiyacınız olduğunda oluşturulur. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Çalışma alanınızı yapılandırın.":::

1. Çalışma alanını yapılandırmayı bitirdiğinizde, **gözden geçir + oluştur**' u seçin. İsteğe bağlı olarak, çalışma alanı için daha fazla ayar yapılandırmak üzere [ağ](#networking) ve [Gelişmiş](#advanced) bölümleri kullanın.

1. Ayarları gözden geçirin ve ek değişiklik veya düzeltme yapın. Ayarları tatmin ediyorsanız **Oluştur**' u seçin.

   > [!Warning] 
   > Çalışma alanınızı bulutta oluşturmak birkaç dakika sürebilir.

   İşlem tamamlandığında, bir dağıtım başarı iletisi görüntülenir. 
 
 1. Yeni çalışma alanını görüntülemek için **Kaynağa Git**' i seçin.
 
---



### <a name="networking"></a>Ağ  

> [!IMPORTANT]  
> Çalışma alanınıza özel bir uç nokta ve sanal ağ kullanma hakkında daha fazla bilgi için bkz. [ağ yalıtımı ve gizliliği](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK 'Sı, özel bir uç nokta ile bir çalışma alanı oluşturmak için [çalışma alanı. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) Ile kullanılabilen [privateendpointconfig](/python/api/azureml-core/azureml.core.privateendpointconfig) sınıfını sağlar. Bu sınıf, var olan bir sanal ağ gerektirir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Varsayılan ağ yapılandırması, genel İnternet üzerinden erişilebilen __genel bir uç nokta__ kullanmaktır. Çalışma alanınıza erişimi oluşturduğunuz bir Azure sanal ağıyla sınırlamak için, __bağlantı yöntemi__ olarak __Özel uç nokta__ (Önizleme) seçeneğini belirleyip, ardından uç noktayı yapılandırmak için __+ Ekle__ ' yi kullanabilirsiniz.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Özel uç nokta seçimi":::  

1. __Özel uç nokta oluştur__ formunda, kullanılacak konumu, adı ve sanal ağı ayarlayın. Uç noktayı bir Özel DNS bölgesiyle kullanmak istiyorsanız, __özel DNS bölgesi Ile tümleştirin__ ' ı seçin ve __özel DNS bölgesi__ alanını kullanarak bölgeyi seçin. Uç noktayı oluşturmak için __Tamam ' ı__ seçin.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Özel uç nokta oluşturma":::   

1. Ağı yapılandırmayı bitirdiğinizde, __gözden geçir + oluştur__' u seçebilir veya Isteğe bağlı __Gelişmiş__ yapılandırmaya ilerleyebilirsiniz.

---

> [!IMPORTANT]  
> Azure Machine Learning çalışma alanı ile özel uç nokta kullanımı Şu anda genel önizlemededir. Bu önizleme, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.     
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Özel uç nokta içeren birden çok çalışma alanı

Özel bir uç nokta oluşturduğunuzda, __Privatelink.api.azureml.MS__ adlı yeni bir özel DNS bölgesi oluşturulur. Bu, sanal ağın bağlantısını içerir. Aynı kaynak grubunda özel uç noktaları olan birden çok çalışma alanı oluşturursanız, DNS bölgesine yalnızca ilk özel uç nokta için sanal ağ eklenebilir. Ek çalışma alanları/özel uç noktalar tarafından kullanılan sanal ağların girdilerini eklemek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com), çalışma alanını içeren kaynak grubunu seçin. Sonra __Privatelink.api.azureml.MS__ adlı özel DNS bölgesi kaynağını seçin.
2. __Ayarlar__' da, __sanal ağ bağlantıları__' nı seçin.
3. __Add (Ekle)__ seçeneğini belirleyin. __Sanal ağ bağlantısı ekle__ sayfasında, benzersiz bir __bağlantı adı__ girin ve eklenecek __sanal ağı__ seçin. Ağ bağlantısını eklemek için __Tamam ' ı__ seçin.

Daha fazla bilgi için bkz. [Azure Özel Uç Nokta DNS yapılandırması](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Güvenlik açığı taraması

Azure Güvenlik Merkezi, hibrit bulut iş yükleri arasında birleşik güvenlik yönetimi ve gelişmiş tehdit koruması sağlar. Azure Güvenlik Merkezi 'nin kaynaklarınızı tarayabilmesi ve önerilerini izlemesi gerekir. Daha fazla bilgi için bkz. Güvenlik Merkezi [Ile Azure Kubernetes hizmet tümleştirmesiyle](../security-center/defender-for-kubernetes-introduction.md) [Azure Container Registry görüntü tarama](../security-center/defender-for-container-registries-introduction.md) .

### <a name="advanced"></a>Gelişmiş

Varsayılan olarak, çalışma alanının meta verileri Microsoft 'un koruduğu bir Azure Cosmos DB örneğine depolanır. Bu veriler, Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir.

Çalışma alanınızda Microsoft 'un topladığı verileri sınırlandırmak için portalda __yüksek iş etkisi çalışma alanını__ seçin veya `hbi_workspace=true ` Python 'da ayarlayın. Bu ayar hakkında daha fazla bilgi için bkz. [bekleyen şifreleme](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> Yüksek iş etkisi seçilmesi, yalnızca bir çalışma alanı oluşturulurken yapılabilir. Çalışma alanı oluşturulduktan sonra bu ayarı değiştiremezsiniz.   

#### <a name="use-your-own-key"></a>Kendi anahtarınızı kullanın

Veri şifreleme için kendi anahtarınızı sağlayabilirsiniz. Bunun yapılması, meta verileri Azure aboneliğinizde depolayan Azure Cosmos DB örneğini oluşturur.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Kendi anahtarınızı sağlamak için aşağıdaki adımları kullanın:

> [!IMPORTANT]  
> Bu adımları uygulamadan önce, önce aşağıdaki eylemleri gerçekleştirmeniz gerekir:   
>
> 1. __Machine Learning uygulamayı__ (kimlik ve erişim yönetimi 'nde) aboneliğinizde katkıda bulunan izinlerle yetkilendirin.  
> 1. [Müşteri tarafından yönetilen anahtarları yapılandırma](../cosmos-db/how-to-setup-cmk.md) bölümündeki adımları izleyerek şunları yapın:
>     * Azure Cosmos DB sağlayıcıyı kaydetme
>     * Azure Key Vault oluşturma ve yapılandırma
>     * Anahtar oluştur
>   
>     Azure Cosmos DB örneğini el ile oluşturmanız gerekmez, bir tane, çalışma alanı oluşturma sırasında sizin için oluşturulur. Bu Azure Cosmos DB örneği, bu düzene göre bir ad kullanılarak ayrı bir kaynak grubunda oluşturulacak: `<your-workspace-resource-name>_<GUID>` .   
>   
> Çalışma alanı oluşturulduktan sonra bu ayarı değiştiremezsiniz. Çalışma alanınız tarafından kullanılan Azure Cosmos DB silerseniz, onu kullanan çalışma alanını da silmeniz gerekir.

# <a name="python"></a>[Python](#tab/python)

`cmk_keyvault` `resource_cmk_uri` Müşteri tarafından yönetilen anahtarı belirtmek için ve kullanın.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. __Müşteri tarafından yönetilen anahtarlar__' ı seçin ve ardından __anahtar seçmek Için tıklayın ' ı__ seçin.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Müşteri tarafından yönetilen anahtarlar":::

1. __Azure Key Vault anahtarı seç__ formunda, var olan bir Azure Key Vault, içerdiği bir anahtarı ve anahtarın sürümünü seçin. Bu anahtar, Azure Cosmos DB depolanan verileri şifrelemek için kullanılır. Son olarak, bu anahtarı kullanmak için __Seç__ düğmesini kullanın.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Anahtarı seçin":::

---

### <a name="download-a-configuration-file"></a>Yapılandırma dosyasını indir

Bir [işlem örneği](tutorial-1st-experiment-sdk-setup.md#azure)oluşturacağınız takdirde, bu adımı atlayın.  İşlem örneği, sizin için bu dosyanın bir kopyasını zaten oluşturdu.

# <a name="python"></a>[Python](#tab/python)

Yerel ortamınızda bu çalışma alanına () başvuran kodu kullanmayı planlıyorsanız `ws` , yapılandırma dosyasını yazın:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yerel ortamınızda bu çalışma alanına başvuran kodu kullanmayı planlıyorsanız, çalışma alanının **genel bakış** bölümünden **config.jsindir** ' i seçin.  

   ![config.jsindir](./media/how-to-manage-workspace/configure.png)

---

Dosyayı Python betikleriniz veya Jupyıter Not defterleriniz ile dizin yapısına yerleştirin. Aynı dizinde, *. azureml* adlı bir alt dizin veya bir üst dizin içinde olabilir. Bir işlem örneği oluşturduğunuzda, bu dosya VM 'deki doğru dizine eklenir.

## <a name="connect-to-a-workspace"></a>Çalışma alanına bağlan

Python kodunuzda, çalışma alanınıza bağlanmak için bir çalışma alanı nesnesi oluşturun.  Bu kod, çalışma alanınızı bulmak için yapılandırma dosyasının içeriğini okur.  Henüz kimlik doğrulamasından geçirilmediğinden oturum açmanız için bir istem alacaksınız.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Birden çok kiracı.**  Birden çok hesabınız varsa, kullanmak istediğiniz Azure Active Directory kiracı KIMLIĞINI ekleyin.  **Azure Active Directory, dış kimlikler** altındaki [Azure Portal](https://portal.azure.com) kiracı kimliğinizi bulun.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Sogeign bulutu](reference-machine-learning-cloud-parity.md)**. Bir bağımsız bulutu 'nda çalışıyorsanız Azure 'da kimlik doğrulaması yapmak için ek koda ihtiyacınız olacaktır.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Aboneliğinize erişim konusunda sorun yaşıyorsanız, bkz. [Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulamasını ayarlama ve](how-to-setup-authentication.md)Azure Machine Learning Not defteri ['nde kimlik doğrulama](https://aka.ms/aml-notebook-auth) .

## <a name="find-a-workspace"></a><a name="view"></a>Çalışma alanı bul

Kullanabileceğiniz tüm çalışma alanlarının listesini görüntüleyin.

# <a name="python"></a>[Python](#tab/python)

[Azure Portal abonelikler sayfasında](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)aboneliklerinizi bulun.  KIMLIĞI kopyalayın ve bu abonelik için kullanılabilir tüm çalışma alanlarını görmek için aşağıdaki kodda kullanın.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Üst arama alanına **Machine Learning** yazın.  

1. **Machine Learning** seçin.

   ![Azure Machine Learning çalışma alanı ara](./media/how-to-manage-workspace/find-workspaces.png)

1. Bulunan çalışma alanları listesine bakın. Abonelik, kaynak grupları ve konumlara göre filtre uygulayabilirsiniz.  

1. Özelliklerini göstermek için bir çalışma alanı seçin.

---


## <a name="delete-a-workspace"></a>Çalışma alanı silme

Artık bir çalışma alanına ihtiyacınız kalmadığında, silin.  

# <a name="python"></a>[Python](#tab/python)

Çalışma alanını Sil `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

Varsayılan eylem, çalışma alanı, yani kapsayıcı kayıt defteri, depolama hesabı, anahtar kasası ve Application Insights ile ilişkili kaynakları silmez.  `delete_dependent_resources`Bu kaynakları da silmek Için true olarak ayarlayın.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/), silmek istediğiniz çalışma alanının en üstünde bulunan **Sil** ' i seçin.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Çalışma alanını sil":::

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Sorun giderme

* **Azure Machine Learning Studio 'Da desteklenen tarayıcılar**: işletim sisteminizle uyumlu en güncel tarayıcıyı kullanmanızı öneririz. Aşağıdaki tarayıcılar desteklenir:
  * Microsoft Edge (yeni Microsoft Edge, en son sürüm. Microsoft Edge eski değil)
  * Safari (en so sürüm, yalnızca Mac)
  * Chrome (en son sürüm)
  * Firefox (en son sürüm)

* **Azure Portal**: 
  * SDK 'dan veya Azure portal bir Share bağlantısından çalışma alanınıza doğrudan giderseniz, içindeki abonelik bilgilerine sahip standart **genel bakış** sayfasını görüntüleyemezsiniz. Bu senaryoda, başka bir çalışma alanına geçiş yapamazsınız. Başka bir çalışma alanını görüntülemek için doğrudan [Azure Machine Learning Studio](https://ml.azure.com) 'ya gidin ve çalışma alanı adını arayın.
  * Tüm varlıklar (veri kümeleri, denemeleri, hesaplar vb.) yalnızca [Azure Machine Learning Studio](https://ml.azure.com)'da kullanılabilir. Azure portal mevcut *değildir* .

### <a name="resource-provider-errors"></a>Kaynak sağlayıcısı hataları

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Çalışma alanı taşınıyor

> [!WARNING]
> Azure Machine Learning çalışma alanınızı farklı bir aboneliğe taşımak veya sahip olunan aboneliğin yeni bir kiracıya taşınması desteklenmez. Bunun yapılması hatalara neden olabilir.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry silme

Azure Machine Learning çalışma alanı bazı işlemler için Azure Container Registry (ACR) kullanır. İlk kez ihtiyaç duyduğunda, otomatik olarak bir ACR örneği oluşturur.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Örnekler

Çalışma alanı oluşturma örnekleri:
* [Çalışma alanı ve işlem örneği oluşturmak](tutorial-1st-experiment-sdk-setup.md) için Azure Portal kullanma
* [Kendi ortamınızda bir çalışma alanı oluşturmak](tutorial-1st-experiment-sdk-setup-local.md) için Python SDK 'yı kullanma

## <a name="next-steps"></a>Sonraki adımlar

Çalışma alanınız olduktan sonra [bir modeli eğitme ve dağıtmayı](tutorial-train-models-with-aml.md)öğrenin.