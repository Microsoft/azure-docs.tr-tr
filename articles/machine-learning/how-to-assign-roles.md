---
title: Çalışma alanınızdaki rolleri yönetme
titleSuffix: Azure Machine Learning
description: Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak bir Azure Machine Learning çalışma alanına erişmeyi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 03/26/2021
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 4d037bdf266e70a2621b2627bc88abce30af652c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612628"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning çalışma alanına erişimi yönetme

Bu makalede, erişimi (yetkilendirme) bir Azure Machine Learning çalışma alanına yönetmeyi öğreneceksiniz. Azure [rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md) , Azure kaynaklarına erişimi yönetmek için kullanılır; Örneğin, yeni kaynaklar oluşturma veya var olanları kullanma özelliği. Azure Active Directory (Azure AD) kullanıcılara, kaynaklara erişim izni veren belirli roller atanır. Azure, yerleşik roller ve özel roller oluşturma yeteneği sağlar.

> [!TIP]
> Bu makale Azure Machine Learning odaklanırken, Azure ML 'nin kullandığı ayrı hizmetler kendi RBAC ayarlarını sağlar. Örneğin, bu makaledeki bilgileri kullanarak, Puanlama isteklerini Azure Kubernetes hizmetinde Web hizmeti olarak dağıtılan bir modele kimlerin gönderebilecekleri yapılandırabilirsiniz. Ancak Azure Kubernetes hizmeti kendi Azure rolleri kümesini sağlar. Azure Machine Learning yararlı olabilecek hizmete özgü RBAC bilgileri için aşağıdaki bağlantılara bakın:
>
> * [Azure Kubernetes küme kaynaklarına erişimi denetleme](../aks/azure-ad-rbac.md)
> * [Kubernetes yetkilendirmesi için Azure RBAC kullanma](../aks/manage-azure-rbac.md)
> * [Blob verilerine erişmek için Azure RBAC kullanma](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> Bazı rollerin uygulanması, diğer kullanıcılar için Azure Machine Learning Studio 'daki UI işlevselliğini sınırlayabilir. Örneğin, bir kullanıcının rolü bir işlem örneği oluşturma yeteneğine sahip değilse, bir işlem örneği oluşturma seçeneği Studio 'da kullanılamaz. Bu davranış beklenmektedir ve kullanıcının erişim reddedildi hatası döndürecek işlemler gerçekleştirmeye engel olur.

## <a name="default-roles"></a>Varsayılan roller

Azure Machine Learning çalışma alanı bir Azure kaynağıdır. Diğer Azure kaynaklarında olduğu gibi yeni bir Azure Machine Learning çalışma alanı da oluşturulduğunda üç varsayılan role sahip olur. Çalışma alanına kullanıcı ekleyebilir ve bunları bu yerleşik rollerden birine atayabilirsiniz.

| Rol | Erişim düzeyi |
| --- | --- |
| **Okuyucu** | Çalışma alanındaki salt okuma eylemleri. Okuyucular, bir çalışma alanında [veri deposu](how-to-access-data.md) kimlik bilgileri de dahil olmak üzere varlıkları listeleyebilir ve görüntüleyebilir. Okuyucular bu varlıkları oluşturamaz veya güncelleştiremez. |
| **Katkıda Bulunan** | Çalışma alanındaki varlıkları görüntüleyin, oluşturun, düzenleyin veya silin (uygulanabilir). Örneğin katkıda bulunanlar bir deneme oluşturabilir, işlem kümesi oluşturabilir veya ekleyebilir, çalıştırma gönderebilir ve bir web hizmeti dağıtabilir. |
| **Sahibi** | Çalışma alanına, çalışma alanındaki varlıkları görüntüleme, oluşturma, düzenleme veya silme özelliği de dahil olmak üzere tam erişim. İsterseniz rol atamalarını değiştirebilirsiniz. |
| **Özel rol** | Çalışma alanındaki belirli denetim veya veri düzlemi işlemlerine erişimi özelleştirmenizi sağlar. Örneğin, bir çalıştırma gönderme, işlem oluşturma, model dağıtma veya bir veri kümesini kaydetme. |

> [!IMPORTANT]
> Rol erişimi, Azure 'da birden çok düzey kapsamına eklenebilir. Örneğin, bir çalışma alanına sahip olan birisi, çalışma alanını içeren kaynak grubuna sahip erişimine sahip olmayabilir. Daha fazla bilgi için bkz. [Azure RBAC nasıl çalışmaktadır](../role-based-access-control/overview.md#how-azure-rbac-works).

Şu anda Azure Machine Learning özgü ek yerleşik roller yoktur. Yerleşik roller hakkında daha fazla bilgi için bkz. [Azure yerleşik rolleri](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Çalışma alanı erişimini yönetme

Bir çalışma alanının sahibiyseniz, çalışma alanı için roller ekleyebilir ve kaldırabilirsiniz. Ayrıca, kullanıcılara roller atayabilirsiniz. Erişimin nasıl yönetileceğini saptamak için aşağıdaki bağlantıları kullanın:
- [Azure portal Kullanıcı arabirimi](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure Resource Manager şablonları](../role-based-access-control/role-assignments-template.md)

[Azure MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md)'yı yüklediyseniz, kullanıcılara roller atamak için CLI komutlarını kullanabilirsiniz:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`Alan, çalışma alanı üst aboneliğinin yaşadığı Azure Active Directory örneğindeki mevcut bir kullanıcının e-posta adresidir. Bu komutun nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> "az ml çalışma alanı paylaşma" komutu, Azure Active Directory B2B tarafından Federal hesap için çalışmıyor. Lütfen komut yerine Azure UI portalını kullanın.

## <a name="create-custom-role"></a>Özel rol oluştur

Yerleşik rollerin yetersiz olması durumunda özel roller oluşturabilirsiniz. Özel rollerin bu çalışma alanında okuma, yazma, silme ve hesaplama kaynağı izinleri olabilir. Rolü belirli bir çalışma alanı düzeyinde, belirli bir kaynak grubu düzeyinde veya belirli bir abonelik düzeyinde kullanılabilir hale getirebilirsiniz.

> [!NOTE]
> Bu kaynak içinde özel Roller oluşturmak için bu düzeyde kaynağın sahibi olmanız gerekir.

Özel bir rol oluşturmak için, önce rolün iznini ve kapsamını belirten bir rol tanımı JSON dosyası oluşturun. Aşağıdaki örnek, belirli bir çalışma alanı düzeyinde "Data Bilimentist Custom" adlı özel bir rol tanımlar:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> `AssignableScopes`Bu özel rolün kapsamını abonelik düzeyinde, kaynak grubu düzeyinde veya belirli bir çalışma alanı düzeyinde ayarlamak için alanı değiştirebilirsiniz.
> Yukarıdaki özel rol yalnızca bir örnektir. [Azure Machine Learning hizmeti için önerilen bazı özel roller](#customroles)bölümüne bakın.

Bu özel rol, aşağıdaki eylemler hariç çalışma alanındaki her şeyi gerçekleştirebilir:

- İşlem kaynağı oluşturamaz veya güncelleştiremez.
- İşlem kaynağını silemiyor.
- Rol atamalarını ekleyemez, silemez veya değiştiremez.
- Çalışma alanını silemiyor.

Bu özel rolü dağıtmak için aşağıdaki Azure CLı komutunu kullanın:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Dağıtımdan sonra, bu rol belirtilen çalışma alanında kullanılabilir hale gelir. Artık bu rolü Azure portal ekleyip atayabilirsiniz. Ya da, CLI komutunu kullanarak bu rolü bir kullanıcıya atayabilirsiniz `az ml workspace share` :

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Özel roller hakkında daha fazla bilgi için bkz. [Azure özel rolleri](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning işlemler

Özel rollerle kullanılabilen işlemler (Eylemler ve eylemler) hakkında daha fazla bilgi için bkz. [kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). İşlemleri listelemek için aşağıdaki Azure CLı komutunu da kullanabilirsiniz:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Özel rolleri listeleme

Azure CLı 'de şu komutu çalıştırın:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Belirli bir özel rolün rol tanımını görüntülemek için aşağıdaki Azure CLı komutunu kullanın. `<role-name>`Yukarıdaki komutla döndürülen biçimde olmalıdır:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Özel rolü güncelleştirme

Azure CLı 'de şu komutu çalıştırın:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Yeni rol tanımınızın tüm kapsamındaki izinlere sahip olmanız gerekir. Örneğin, bu yeni rolün üç abonelik arasında bir kapsamı varsa, üç abonelik için de izinleriniz olması gerekir. 

> [!NOTE]
> Rol güncelleştirmelerinin, bu kapsamdaki tüm rol atamaları üzerinde uygulanması 15 dakika ila saat arasında sürebilir.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Yinelenebilirlik için Azure Resource Manager şablonları kullanma

Karmaşık rol atamalarını yeniden oluşturmanız gerektiğini düşünüyorsanız, bir Azure Resource Manager şablonu büyük bir yardım olabilir. [201-Machine-Learning-Dependencies-role-atama şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) , rol atamalarının yeniden kullanım için kaynak kodda nasıl belirtilenebilir olduğunu gösterir. 

## <a name="common-scenarios"></a>Genel senaryolar

Aşağıdaki tabloda Azure Machine Learning etkinliklerin Özeti ve bunları en az kapsamda gerçekleştirmek için gereken izinler verilmiştir. Örneğin, bir etkinlik bir çalışma alanı kapsamıyla (sütun 4) gerçekleştirilebileceği takdirde, bu izne sahip tüm daha yüksek kapsam de otomatik olarak çalışır:

> [!IMPORTANT]
> Bu tablodaki ile başlayan tüm yollar `/` **göreli yollardır** `Microsoft.MachineLearningServices/` :

| Etkinlik | Abonelik düzeyi kapsamı | Kaynak grubu düzeyi kapsamı | Çalışma alanı düzeyi kapsamı |
| ----- | ----- | ----- | ----- |
| Yeni çalışma alanı oluştur | Gerekli değil | Sahip veya katkıda bulunan | Yok (oluşturulduktan sonra sahip olur veya daha yüksek kapsam rolünü devralır) |
| Abonelik düzeyi Amlcompute kotası iste veya çalışma alanı düzeyi kotasını ayarla | Sahip veya katkıda bulunan veya özel rol </br>enlere `/locations/updateQuotas/action`</br> Abonelik kapsamında | Yetkilendirilmemiş | Yetkilendirilmemiş |
| Yeni işlem kümesi oluştur | Gerekli değil | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `/workspaces/computes/write` |
| Yeni işlem örneği oluştur | Gerekli değil | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `/workspaces/computes/write` |
| Herhangi bir türdeki çalışma gönderiliyor | Gerekli değil | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| İşlem hatlarını ve uç noktalarını yayımlama | Gerekli değil | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| AKS/ACI kaynağına kayıtlı model dağıtma | Gerekli değil | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Dağıtılan bir AKS uç noktasına göre Puanlama | Gerekli değil | Gerekli değil | Sahip, katkıda bulunan veya özel rol: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (Azure Active Directory kimlik doğrulaması kullanmadığınız zaman) veya `"/workspaces/read"` (belirteç kimlik doğrulamasını kullanırken) |
| Etkileşimli not defterleri kullanarak depolamaya erişme | Gerekli değil | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Yeni özel rol oluştur | Sahip, katkıda bulunan veya özel rol `Microsoft.Authorization/roleDefinitions/write` | Gerekli değil | Sahibi, katkıda bulunan veya özel rol şunları sağlar: `/workspaces/computes/write` |

> [!TIP]
> İlk kez çalışma alanı oluşturmaya çalışırken bir hata alırsanız, rolünüzün izin verdiğinden emin olun `Microsoft.MachineLearningServices/register/action` . Bu eylem, Azure aboneliğinize Azure Machine Learning kaynak sağlayıcısını kaydetmenizi sağlar.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Azure ML işlem kümesiyle Kullanıcı tarafından atanan yönetilen kimlik

Bir Azure Machine Learning işlem kümesine Kullanıcı tarafından atanan bir kimlik atamak için, işlem ve [yönetilen kimlik operatörü rolü](../role-based-access-control/built-in-roles.md#managed-identity-operator)oluşturmak için yazma izinlerine sahip olmanız gerekir. Yönetilen kimliklerle Azure RBAC hakkında daha fazla bilgi için, [Kullanıcı tarafından atanan kimliği yönetme](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) konusunu okuyun

### <a name="mlflow-operations"></a>MLflow işlemleri

Azure Machine Learning çalışma alanınızda MLflow işlemleri gerçekleştirmek için özel rolünüzün aşağıdaki kapsamlarını kullanın:

| MLflow işlemi | Kapsam |
| --- | --- |
| Çalışma alanı izleme deposundaki tüm denemeleri Listele, kimliğe göre bir deneme al, ada göre bir deneme al | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Ad ile bir deneme oluşturun, bir deneyde etiket ayarlayın, silme için işaretlenen bir denemeyi geri yükleyin| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Deneme silme | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Çalıştırma ve ilgili verileri ve meta verileri alın, belirli bir çalıştırma için belirtilen ölçüm için tüm değerlerin bir listesini alın, bir çalıştırma için yapıları listeleyin | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Deneme içinde yeni bir çalıştırma oluşturma, silme çalıştırmaları, silinen çalıştırmaları geri yükleme, geçerli çalışma altında Etiketleri ayarlama, bir çalıştırmada etiket ayarlama, bir çalıştırma için kullanılan etiketleri silme, günlük parametreleri (anahtar-değer çifti), bir çalıştırma için kullanılan bir toplu işlem, parametreleri ve etiketi günlüğe kaydetme, çalışma durumunu güncelleştirme | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Ada göre kayıtlı model al, kayıt defterindeki tüm kayıtlı modellerin bir listesini getirin, kayıtlı modeller için arama yapın, her bir istek için en son sürüm modellerini bulun, kayıtlı bir modelin sürümünü, arama modeli sürümlerini alın, bir model sürümünün bulunduğu URI 'yi alın, deneme kimliklerine göre çalıştırmaları arayın | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Yeni bir kayıtlı model oluşturma, kayıtlı bir modelin adını/açıklamasını güncelleştirme, var olan kayıtlı modeli yeniden adlandırma, modelin yeni sürümü oluşturma, model sürümünün açıklamasını güncelleştirme, kayıtlı bir modeli aşamaların birine geçirme | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Kayıtlı bir modeli tüm sürümüyle birlikte silme, kayıtlı bir modelin belirli sürümlerini silme | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Örnek özel roller

### <a name="data-scientist"></a>Veri bilimcisi

Bir veri bilimi 'nin bir çalışma alanı içinde tüm işlemleri gerçekleştirmesini sağlar, **ancak**:

* İşlem oluşturma
* Bir üretim AKS kümesine model dağıtma
* Üretimde bir işlem hattı uç noktası dağıtma

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>Veri bilimi kısıtlanmış

İzin verilen eylemlerde joker karakter olmadan daha kısıtlı bir rol tanımı. **Aşağıdakiler dışında**, çalışma alanındaki tüm işlemleri gerçekleştirebilir:

* İşlem oluşturma
* Bir üretim AKS kümesine model dağıtma
* Üretimde bir işlem hattı uç noktası dağıtma

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow veri bilimcisi

Veri bilimclarının **aşağıdakiler dışında** tüm Mlflow AzureML desteklenen işlemleri gerçekleştirmesini sağlar:

* İşlem oluşturma
* Bir üretim AKS kümesine model dağıtma
* Üretimde bir işlem hattı uç noktası dağıtma

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

Hizmet sorumlusuna rol atamanıza ve MLOps işlem hatlarınızı otomatikleştirmek için bunu kullanmanıza olanak sağlar. Örneğin, önceden yayımlanmış bir işlem hattına karşı çalıştırmaları göndermek için:

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>Çalışma alanı Yöneticisi

, **Aşağıdakiler dışında** tüm işlemleri çalışma alanının kapsamı içinde gerçekleştirmenize olanak tanır:

* Yeni bir çalışma alanı oluşturma
* Abonelik veya çalışma alanı düzeyinde kotalar atama

Çalışma alanı yöneticisi de yeni bir rol oluşturamaz. Yalnızca mevcut yerleşik veya özel rolleri, çalışma alanının kapsamı içinde atayabilir:

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>Veri etiketleyici

Yalnızca verileri etiketlemek için kapsamlı bir rol tanımlamanızı sağlar:

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>Sorun giderme

Azure rol tabanlı erişim denetimi (Azure RBAC) kullanırken dikkat etmeniz gereken birkaç nokta aşağıda verilmiştir:

- Azure 'da bir çalışma alanı gibi bir kaynak oluşturduğunuzda doğrudan kaynağın sahibi siz değilsiniz. Rolünüz, bu abonelikte yetkilendirdiğiniz en yüksek kapsam rolünden devralınır. Örnek olarak, bir ağ yöneticisiyseniz ve bir Machine Learning çalışma alanı oluşturma izinlerine sahipseniz, bu çalışma alanına karşı ağ yöneticisi rolüne sahip olur ve sahip rolü yoktur.

- Bir çalışma alanında kota işlemleri gerçekleştirmek için abonelik düzeyi izinlerine sahip olmanız gerekir. Bu, yönetilen işlem kaynaklarınız için abonelik düzeyi kotasının veya çalışma alanı düzeyi kotasının ayarlanması, yalnızca abonelik kapsamında yazma izinleriniz varsa meydana gelebileceği anlamına gelir.

- Aynı Azure Active Directory kullanıcıya eylem/NotActions bölümleri ile aynı olan iki rol ataması olduğunda, bir rolden NotActions bölümünde listelenen işlemler, başka bir roldeki eylemler olarak da listelendiklerinde etkili olmayabilir. Azure 'un rol atamalarını nasıl ayrıştırdığı hakkında daha fazla bilgi edinmek için, [kullanıcının bir kaynağa erişip erişemeyeceğini Azure RBAC 'Nin nasıl belirlediğini](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) okuyun

- İşlem kaynaklarınızı bir sanal ağ içinde dağıtmak için aşağıdaki eylemler için açıkça izinlerinizin olması gerekir:
    - `Microsoft.Network/virtualNetworks/*/read` , VNet kaynakları üzerinde.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` alt ağ kaynağında.
    
    Ağ ile Azure RBAC hakkında daha fazla bilgi için bkz. [ağ yerleşik rolleri](../role-based-access-control/built-in-roles.md#networking).

- Yeni rol atamalarınızın yığın genelinde önbelleğe alınmış izinlerle etkili olması için bazen 1 saate kadar zaman alabilir.
- [Koşullu erişim](../role-based-access-control/conditional-access-azure-management.md) şu anda Azure Machine Learning desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Kurumsal güvenliğe genel bakış](concept-enterprise-security.md)
- [Sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md)
- [Öğretici: modelleri eğitme](tutorial-train-models-with-aml.md)
- [Kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
