---
title: CI/CD için GitHub Actions
titleSuffix: Azure Machine Learning
description: Azure Machine Learning bir modeli eğitme için bir GitHub eylemleri iş akışı oluşturma hakkında bilgi edinin
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 6505523aa367eaf202ece81a4253429e864e169a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780380"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Azure Machine Learning GitHub eylemlerini kullanma

Azure Machine Learning bir modeli eğitmek için [GitHub eylemleri](https://docs.github.com/en/actions) ile çalışmaya başlayın. 

> [!NOTE]
> Azure Machine Learning için GitHub eylemleri, olduğu gibi sağlanır ve Microsoft tarafından tam olarak desteklenmez. Belirli bir eylemle ilgili sorunlarla karşılaşırsanız, eylem için depoda bir sorun açın. Örneğin, AML-Deploy eylemiyle ilgili bir sorunla karşılaşırsanız, sorunu [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) depoya bildirin.

## <a name="prerequisites"></a>Önkoşullar 

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub hesabı. Bir hesabınız yoksa [ücretsiz](https://github.com/join)kaydolun.  

## <a name="workflow-file-overview"></a>İş akışı dosyasına genel bakış

Bir iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

Dosya dört bölümden oluşur:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | 1. bir hizmet sorumlusu tanımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Bağlan** | 1. Machine Learning çalışma alanına bağlanın. <br /> 2. bir işlem hedefine bağlanın. |
|**Çalıştır** | 1. bir eğitim çalıştırması iletin. |
|**Dağıtma** | 1. Azure Machine Learning kayıt defterine modeli kaydedin. 1. Modeli dağıtma. |

## <a name="create-repository"></a>Depo oluştur

[GitHub eylemleri ve Azure Machine Learning şablonuyla ml Ops](https://github.com/machine-learning-apps/ml-template-azure)'dan yeni bir depo oluşturun. 

1. [Şablonu](https://github.com/machine-learning-apps/ml-template-azure) GitHub üzerinde açın. 
2. **Bu şablonu kullan**' ı seçin. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Bu şablonu kullan ' ı seçin":::
3. Şablondan yeni bir depo oluşturun. Depo adını `ml-learning` ya da seçtiğiniz bir adı ayarlayın. 


## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

Yukarıdaki örnekte yer tutucuları abonelik KIMLIĞINIZ, kaynak grubu adı ve uygulama adınızla değiştirin. Çıktı, aşağıda gösterilene benzer App Service uygulamanıza erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. Bu JSON nesnesini daha sonra kopyalayın.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>GitHub gizliliğini yapılandırma

1. [GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

2. Azure CLı komutundan tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi adını verin `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Çalışma alanına bağlan

Azure Machine Learning çalışma alanınıza bağlanmak için [Azure Machine Learning çalışma alanı eylemini](https://github.com/marketplace/actions/azure-machine-learning-workspace) kullanın. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Varsayılan olarak, eylem bir dosya bekler `workspace.json` . JSON dosyanız farklı bir ada sahipse, bunu `parameters_file` giriş parametresiyle belirtebilirsiniz. Bir dosya yoksa, depo adıyla yeni bir tane oluşturulur.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Eylem, çalışma alanı Azure Resource Manager (ARM) özelliklerini bir yapılandırma dosyasına yazar ve bu, gelecekteki tüm Azure Machine Learning GitHub eylemleri tarafından alınacaktır. Dosya öğesine kaydedilir `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Azure Machine Learning bir Işlem hedefine bağlanma

Azure Machine Learning bir işlem hedefine bağlanmak için [Azure Machine Learning işlem eylemini](https://github.com/Azure/aml-compute) kullanın.  İşlem hedefi varsa, eylem buna bağlanır. Aksi takdirde eylem yeni bir işlem hedefi oluşturur. [AML işlem eylemi](https://github.com/Azure/aml-compute) yalnızca Azure ML işlem kümesini ve Azure Kubernetes hizmetini (aks) destekler. 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Eğitim çalıştırmasını gönderme

Azure Machine Learning için bir ScriptRun, bir Estimator veya bir işlem hattı göndermek için [Azure Machine Learning eğitim eylemini](https://github.com/Azure/aml-run) kullanın. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Modeli kayıt defterine kaydet

Azure Machine Learning bir modeli kaydetmek için [Azure Machine Learning modeli Kaydet eylemini](https://github.com/Azure/aml-registermodel) kullanın.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Modeli ACI 'ya Azure Machine Learning dağıtma

Model dağıtmak ve model için bir uç nokta oluşturmak için [Azure Machine Learning dağıt eylemini](https://github.com/Azure/aml-deploy) kullanın. Azure Kubernetes hizmetine dağıtmak için Azure Machine Learning dağıtımını de kullanabilirsiniz. Azure Kubernetes hizmetine dağıtan bir model için [Bu örnek iş akışına](https://github.com/Azure-Samples/mlops-enterprise-template) bakın.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Örnek Tamam

Modelinizi eğitme ve Azure Machine Learning dağıtım. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunuz ve deponuz artık gerekmiyorsa, kaynak grubunu ve GitHub deponuzu silerek dağıttığınız kaynakları temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Machine Learning SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma](./how-to-create-machine-learning-pipelines.md)