---
title: GitHub eylemleri ile bir CI/CD işlem hattından kapsayıcınızı dağıtma-Azure App Service | Microsoft Docs
description: App Service kapsayıcınızı dağıtmak için GitHub eylemlerini nasıl kullanacağınızı öğrenin
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 7fbd7b571f5590ff35d52062cc621069a47b619c
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620222"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub eylemlerini kullanarak App Service özel kapsayıcı dağıtma

[GitHub eylemleri](https://help.github.com/en/articles/about-github-actions) size otomatik yazılım geliştirme yaşam döngüsü iş akışı oluşturma esnekliği sağlar. [Kapsayıcılar için Azure App Service eylemiyle](https://github.com/Azure/webapps-container-deploy), GitHub eylemlerini kullanarak [App Service uygulamaları özel kapsayıcılar](https://azure.microsoft.com/services/app-service/containers/) olarak dağıtmak üzere iş akışınızı otomatikleştirebilir.

> [!IMPORTANT]
> GitHub eylemleri Şu anda beta aşamasındadır. GitHub hesabınızı kullanarak [önizlemeye katmak için önce kaydolmanız](https://github.com/features/actions) gerekir.
> 

Bir iş akışı, deponuzdaki `/.github/workflows/` yolundaki bir YAML (. yıml) dosyası tarafından tanımlanır. Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

Azure App Service kapsayıcı iş akışı için, dosyanın üç bölümü vardır:

|Section  |Görevler  |
|---------|---------|
|**Kimlik doğrulaması** | 1. bir hizmet sorumlusu tanımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Derlemeyi** | 1. ortamı ayarlayın. <br /> 2. kapsayıcı görüntüsünü oluşturun. |
|**Dağıtma** | 1. kapsayıcı görüntüsünü dağıtın. |

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

[Azure CLI](https://docs.microsoft.com/cli/azure/)'de [az ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) komutunu kullanarak bir [hizmet sorumlusu](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) kullanarak veya **deneyin** düğmesini seçerek çalıştırabilirsiniz.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Çıktı, aşağıda gösterilene benzer App Service uygulamanıza erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. GitHub 'dan kimlik doğrulaması yapmak için bu JSON nesnesini kopyalayın.

 ```azurecli 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> En az erişim sağlamak her zaman iyi bir uygulamadır. Yukarıdaki az CLı komutundaki kapsamı, belirli App Service uygulamasıyla ve kapsayıcı görüntülerinin gönderildiği Azure Container Registry kısıtlayabilirsiniz.

## <a name="configure-the-github-secret"></a>GitHub gizliliğini yapılandırma

Aşağıdaki örnekte, Kullanıcı düzeyi kimlik bilgileri (örneğin, dağıtım için Azure hizmet sorumlusu) kullanılmaktadır. Gizli anahtarı yapılandırmak için aşağıdaki adımları izleyin:

1. [GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**

2. Aşağıdaki `az cli` komutunun içeriğini gizli değişkenin değeri olarak yapıştırın. Örneğin, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Şimdi dalınızdaki iş akışı dosyasında: `.github/workflows/workflow.yml` Azure oturum açma eyleminde gizli anahtarı gizli diziyle değiştirin.

4. Benzer şekilde, kapsayıcı kayıt defteri kimlik bilgileri için aşağıdaki ek gizli dizileri tanımlayın ve bunları Docker oturum açma eyleminde ayarlayın. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Gizli dizileri, tanımlandıktan sonra aşağıda gösterildiği gibi görürsünüz.

    ![kapsayıcı gizli dizileri](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Kapsayıcı görüntüsünü oluşturma

Aşağıdaki örnek, Docker görüntüsünü oluşturan iş akışının bir parçasını gösterir.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>App Service kapsayıcısına dağıtma

Görüntünüzü App Service özel bir kapsayıcıya dağıtmak için `azure/webapps-container-deploy@v1` eylemini kullanın. Bu eylem beş parametreye sahiptir:

| **Parametresinin**  | **Açıklama**  |
|---------|---------|
| **uygulama adı** | Istenir App Service uygulamasının adı | 
| **yuva adı** | Seçim Üretim yuvası dışında mevcut bir yuva girin |
| **yansımasını** | Istenir Tam kapsayıcı görüntü adını belirtin. Örneğin, ' myregistry.azurecr.io/nginx:latest ' veya ' Python: 3.7.2-alçam/'. Çok kapsayıcılı bir uygulama için birden çok kapsayıcı görüntüsü adı sağlanmış olabilir (çok satırlı ayrılmış) |
| **yapılandırma-dosya** | Seçim Docker-Compose dosyasının yolu. Tam olarak nitelenmiş bir yol olmalıdır veya varsayılan çalışma dizinine göre değişir. Çok Kapsayıcılı uygulamalar için gereklidir. |
| **kapsayıcı-komut** | Seçim Başlangıç komutunu girin. For ex. DotNet Run veya DotNet filename. dll |

Aşağıda, bir Node. js uygulamasını derlemek ve App Service bir özel kapsayıcıya dağıtmak için örnek iş akışı verilmiştir.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'daki farklı depolarda gruplanmış eylem listemizi, her biri, CI/CD için GitHub kullanmanıza ve uygulamalarınızı Azure 'a dağıtmanıza yardımcı olacak belgeler ve örnekler içeren bir şekilde bulabilirsiniz.

- [Azure oturum açma](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Kapsayıcılar için Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker oturum açma/kapatma](https://github.com/Azure/docker-login)

- [İş akışlarını tetikleyen olaylar](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s dağıtımı](https://github.com/Azure/k8s-deploy)

- [Başlangıç CI Iş akışları](https://github.com/actions/starter-workflows)

- [Azure 'a dağıtılacak Başlatıcı iş akışları](https://github.com/Azure/actions-workflow-samples)
