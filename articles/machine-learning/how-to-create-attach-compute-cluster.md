---
title: İşlem kümeleri oluşturma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning çalışma alanınızda işlem kümeleri oluşturmayı öğrenin. Eğitim veya çıkarım için işlem hedefi olarak hesaplama kümesi kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: f61936e622a539b29c6788f631df5de42bb2f242
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861256"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning işlem kümesi oluşturma

Azure Machine Learning çalışma alanınızda bir [işlem kümesi](concept-compute-target.md#azure-machine-learning-compute-managed) oluşturmayı ve yönetmeyi öğrenin.

Bulutta bir CPU veya GPU işlem düğümleri kümesi üzerinde eğitim veya toplu çıkarım işlemi dağıtmak için Azure Machine Learning işlem kümesi kullanabilirsiniz. GPU 'ları içeren VM boyutları hakkında daha fazla bilgi için bkz. [GPU ile iyileştirilmiş sanal makine boyutları](../virtual-machines/sizes-gpu.md). 

Bu makalede şunları yapmayı öğreneceksiniz:

* İşlem kümesi oluşturma
* İşlem kümesi maliyetinizi düşürün
* Küme için [yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) ayarlama

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* [Machine Learning hizmeti Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)veya [Azure Machine Learning Visual Studio Code uzantısı](tutorial-setup-vscode-extension.md).

* Python SDK kullanıyorsanız, [geliştirme ortamınızı bir çalışma alanıyla ayarlayın](how-to-configure-environment.md).  Ortamınız kurulduktan sonra Python betiğinizdeki çalışma alanına ekleyin:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>İşlem kümesi nedir?

Azure Machine Learning işlem kümesi, kolayca tek veya çok düğümlü bir işlem oluşturmanıza olanak sağlayan bir yönetilen işlem altyapısıdır. İşlem, çalışma alanınızdaki diğer kullanıcılarla paylaşılabilecek bir kaynak olarak çalışma alanı bölgeniz içinde oluşturulur. İşlem, bir iş gönderildiğinde otomatik olarak ölçeklendirilir ve bir Azure sanal ağına yerleştirilebilir. İşlem kapsayıcılı bir ortamda yürütülür ve model bağımlılıklarınızı bir [Docker kapsayıcısında](https://www.docker.com/why-docker)paketleyebilir.

İşlem kümeleri, kuruluşların SSH bağlantı noktalarını açmasına gerek kalmadan, işleri bir [sanal ağ ortamında](how-to-secure-training-vnet.md)güvenli bir şekilde çalıştırabilir. İş kapsayıcılı bir ortamda yürütülür ve model bağımlılıklarınızı bir Docker kapsayıcısında paketleyebilir. 

## <a name="limitations"></a>Sınırlamalar

* Bu belgede listelenen senaryolardan bazıları __Önizleme__ olarak işaretlendi. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Şu anda ARM şablonları [] aracılığıyla kümelerin oluşturma (ve güncelleştirme değil) ' i destekliyoruz https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json . İşlem güncelleştirme için, şimdilik SDK, CLı veya UX kullanmanızı öneririz.

* Azure Machine Learning Işlem, ayrılabilen çekirdek sayısı gibi varsayılan sınırlara sahiptir. Daha fazla bilgi için bkz. [Azure kaynakları için kotaları yönetme ve isteme](how-to-manage-quotas.md).

* Azure, kaynakları, silinememesi veya salt okunurdur.  __Kaynak kilitlerini, çalışma alanınızı içeren kaynak grubuna uygulamayın__. Çalışma alanınızı içeren kaynak grubuna bir kilit uygulandığında, Azure ML işlem kümelerinin ölçeklendirme işlemleri engellenir. Kaynakları kilitleme hakkında daha fazla bilgi için, bkz. [beklenmeyen değişiklikleri engellemek için kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Gereken çekirdek sayısı için yeterli kotanın olması koşuluyla, kümeler genellikle 100 düğüme kadar ölçeklendirebilir. Varsayılan olarak kümeler, MPı işlerini desteklemek üzere küme düğümleri arasında etkinleştirilen düğümler arası iletişim ile ayarlanır. Ancak, [bir destek bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)oluşturarak ve abonelik veya çalışma alanınızı ya da düğümler arası iletişimi devre dışı bırakmaya yönelik belirli bir kümeyi listelemek istiyorsanız kümelerinizi 1000 ' e ölçeklendirebilirsiniz.


## <a name="create"></a>Oluştur

**Tahmini süre**: yaklaşık 5 dakika.

Azure Machine Learning Işlem, çalıştırmalar arasında yeniden kullanılabilir. İşlem, çalışma alanındaki diğer kullanıcılarla paylaşılabilir ve çalıştırmalar arasında korunur, gönderilen çalışma sayısına ve kümenizde ayarlanan max_nodes göre otomatik olarak düğümleri yukarı veya aşağı ölçeklendirin. Min_nodes ayarı kullanılabilir en düşük düğümleri denetler.

VM ailesi kotası başına bölge başına adanmış çekirdekler ve hesaplama kümesi oluşturma için geçerli olan toplam bölgesel kota, Azure Machine Learning eğitim işlem örneği kotasıyla birleştirilmiş ve paylaşılır. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

İşlem, kullanılmazsa otomatik olarak sıfır düğümlere ölçeklenir.   İşlerinizi gerektiğinde çalıştırmak için adanmış VM 'Ler oluşturulur.
    
# <a name="python"></a>[Python](#tab/python)


Python 'da kalıcı bir Azure Machine Learning Işlem kaynağı oluşturmak için **vm_size** ve **max_nodes** özelliklerini belirtin. Azure Machine Learning daha sonra diğer özellikler için akıllı Varsayılanları kullanır.
    
* **vm_size**: Azure Machine Learning işlem tarafından oluşturulan düğümlerin VM ailesi.
* **max_nodes**: Azure Machine Learning işlem sırasında bir işi çalıştırdığınızda otomatik olarak en fazla düğüm sayısı.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Ayrıca, Azure Machine Learning Işlem oluştururken birçok gelişmiş özelliği de yapılandırabilirsiniz. Özellikler, sabit boyutlu kalıcı bir küme oluşturmanıza veya mevcut bir Azure Sanal Ağa gelen aboneliğinizden bu.  Ayrıntılar için [Amlcompute sınıfına](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) bakın.


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Daha fazla bilgi için bkz. [az ml computetarget Create amlcompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Studio 'da bir işlem kümesi oluşturma hakkında bilgi için, bkz. [Azure Machine Learning Studio 'da işlem hedefleri oluşturma](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> İşlem kümesi maliyetinizi düşürün

İş yüklerinizin bazılarını veya tümünü çalıştırmak için [düşük öncelikli VM 'ler](concept-plan-manage-cost.md#low-pri-vm) kullanmayı da tercih edebilirsiniz. Bu VM 'Lerin garantili kullanılabilirliği yoktur ve kullanımda olabilir. Önden bir işi yeniden başlatmanız gerekir. 

Düşük öncelikli bir VM belirtmek için şu yolların herhangi birini kullanın:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Şunları ayarlayın `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Studio 'da, bir VM oluştururken **düşük öncelik** ' i seçin.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Yönetilen kimliği ayarlama

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Yönetilen kimliği, sağlama yapılandırmanızda yapılandırın:  

    * Sistem tarafından atanan yönetilen kimlik adlı bir çalışma alanında oluşturuldu `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Adlı bir çalışma alanında oluşturulan kullanıcı tarafından atanan yönetilen kimlik `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Şu adlı mevcut bir işlem kümesine yönetilen kimlik Ekle `cpu_cluster`
    
    * Sistem tarafından atanan yönetilen kimlik:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Kullanıcı tarafından atanan yönetilen kimlik:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Yönetilen kimliğe sahip yeni bir yönetilen işlem kümesi oluşturma

  * Kullanıcı tarafından atanan yönetilen kimlik

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Sistem tarafından atanan yönetilen kimlik

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Var olan bir kümeye yönetilen kimlik ekleme:

    * Kullanıcı tarafından atanan yönetilen kimlik
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Sistem tarafından atanan yönetilen kimlik

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Bkz. [Studio 'da yönetilen kimliği ayarlama](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Yönetilen kimlik kullanımı

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Sorun giderme

Azure Machine Learning çalışma alanını, GA sürümünden önce Azure portal oluşturan bazı kullanıcıların bu çalışma alanında AmlCompute oluşturmayabilir. Hizmette bir destek isteği oluşturabilir veya portal veya SDK aracılığıyla hemen engelini kaldırmak için yeni bir çalışma alanı oluşturabilirsiniz.

Azure Machine Learning işlem kümeniz düğüm durumu için yeniden boyutlandırılırken (0-> 0) takılmış görünüyorsa, bunun nedeni Azure kaynak kilitleri olabilir.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Sonraki adımlar

İşlem kümenizi kullanarak şunları yapın:

* [Eğitim çalışması gönder](how-to-set-up-training-targets.md) 
* [Toplu çıkarımı çalıştırın](./tutorial-pipeline-batch-scoring-classification.md).
