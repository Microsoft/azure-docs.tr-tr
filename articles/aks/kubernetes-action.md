---
title: GitHub eylemlerini kullanarak kapsayıcıları Azure Kubernetes hizmetine oluşturun, test edin ve dağıtın
description: Kapsayıcısını Kubernetes 'e dağıtmak için GitHub eylemlerini nasıl kullanacağınızı öğrenin
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3d0b6030cc63d0d7f4eac2c72c3545cf315b1fd3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832396"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes hizmetine dağıtmaya yönelik GitHub eylemleri

[GitHub eylemleri](https://docs.github.com/en/actions) size otomatik yazılım geliştirme yaşam döngüsü iş akışı oluşturma esnekliği sağlar. GitHub eylemleri ile Azure Kubernetes hizmetine Azure Container Registry kapsayıcılara dağıtmak için birden fazla Kubernetes eylemini kullanabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar 

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub hesabı. Bir hesabınız yoksa [ücretsiz](https://github.com/join)kaydolun.  
- Çalışan bir Kubernetes kümesi
    - [Öğretici: Azure Kubernetes hizmeti için uygulama hazırlama](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>İş akışı dosyasına genel bakış

Bir iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

AKS 'i hedefleyen bir iş akışı için, dosyanın üç bölümü vardır:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | Özel bir kapsayıcı kayıt defterinde (ACR) oturum açma |
|**Derleme** | Kapsayıcı görüntüsünü oluşturun & gönderin  |
|**Dağıtma** | 1. hedef AKS kümesini ayarlama |
| |2. Kubernetes kümesinde genel/Docker-Registry gizli dizisi oluşturma  |
||3. Kubernetes kümesine dağıtın|

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutunu kullanarak bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) kullanarak veya **deneyin** düğmesini seçerek çalıştırabilirsiniz.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Yukarıdaki komutta yer tutucuları abonelik KIMLIĞINIZ ve kaynak grubuyla değiştirin. Çıktı, kaynağına erişim sağlayan rol atama kimlik bilgileridir. Komut şuna benzer bir JSON nesnesinin çıktısını almalıdır.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
GitHub 'dan kimlik doğrulamak için kullanabileceğiniz bu JSON nesnesini kopyalayın.

## <a name="configure-the-github-secrets"></a>GitHub gizli dizilerini yapılandırma

Gizli dizileri yapılandırmak için aşağıdaki adımları izleyin:

1. [GitHub](https://github.com/)'da deponuza gidin, **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

    ![Ekran görüntüsü, bir depo için yeni bir gizli dizi Ekle bağlantısı gösterir.](media/kubernetes-action/secrets.png)

2. Yukarıdaki `az cli` komutun içeriğini gizli değişkenin değeri olarak yapıştırın. Örneğin, `AZURE_CREDENTIALS`.

3. Benzer şekilde, kapsayıcı kayıt defteri kimlik bilgileri için aşağıdaki ek gizli dizileri tanımlayın ve bunları Docker oturum açma eyleminde ayarlayın. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Gizli dizileri, tanımlandıktan sonra aşağıda gösterildiği gibi göreceksiniz.

    ![Ekran görüntüsü, bir depo için mevcut gizli dizileri gösterir.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Bir kapsayıcı görüntüsü oluşturun ve Azure Kubernetes hizmet kümesine dağıtın

Kapsayıcı görüntülerinin oluşturulması ve gönderimi eylem kullanılarak yapılır `Azure/docker-login@v1` . 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Azure Kubernetes hizmet kümesine dağıtma

AKS 'e bir kapsayıcı görüntüsü dağıtmak için eylemini kullanmanız gerekir `Azure/k8s-deploy@v1` . Bu eylem beş parametreye sahiptir:

| **Parametre**  | **Açıklama**  |
|---------|---------|
| **uzayına** | Seçim Hedef Kubernetes ad alanını seçin. Ad alanı sağlanmazsa, komutlar varsayılan ad alanında çalıştırılır | 
| **listeleri** |  Istenir Dağıtım için kullanılacak olan bildirim dosyalarının yolu |
| **yansımasını** | Seçim Bildirim dosyalarındaki değişimler için kullanılacak görüntünün tam kaynak URL 'SI |
| **ımagepullgizlilikler** | Seçim Küme içinde zaten ayarlanmış olan Docker-Registry parolasının adı. Bu gizli adların her biri, giriş bildirimi dosyalarında bulunan iş yükleri için ımagepullsecret alanı altına eklenir |
| **kubectl-sürüm** | Seçim Belirli bir kubectl ikili sürümünü yüklüyor |


AKS 'e dağıtım yapabilmeniz için önce hedef Kubernetes ad alanını ayarlamanız ve bir görüntü çekme gizli anahtarı oluşturmanız gerekir. Görüntülerin nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Azure Container Registry 'den bir Kubernetes kümesine çekme görüntüleri](../container-registry/container-registry-auth-kubernetes.md). 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      arguments: --force true
```


Eylemi kullanarak dağıtımınızı doldurun `k8s-deploy` . Ortam değişkenlerini uygulamanızın değerleriyle değiştirin. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kubernetes kümeniz, kapsayıcı kayıt defteriniz ve deponuz artık gerekmiyorsa, kaynak grubunu ve GitHub deponuzu silerek dağıttığınız kaynakları temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Kubernetes hizmeti hakkında bilgi edinin](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Daha fazla Kubernetes GitHub eylemi

* [Kubectl aracı yükleyicisi](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): Çalıştırıcısı üzerinde belirli bir Kubectl sürümü yüklenir.
* [Kubernetes set Context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): diğer eylemler tarafından kullanılacak olan hedef Kubernetes küme bağlamını ayarlayın veya herhangi bir kubectl komutunu çalıştırın.
* [Aks set Context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): hedef Azure Kubernetes hizmet kümesi bağlamını ayarlayın.
* [Kubernetes gizli](https://github.com/Azure/k8s-create-secret) dizi ( `azure/k8s-create-secret` ) oluştur: Kubernetes kümesinde genel bir gizli dizi veya Docker-Registry gizli anahtarı oluşturun.
* [Kubernetes Deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): Kubernetes kümelerine bildirim dağıtın ve bildirimleri dağıtın.
* [Held kurulumu](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): Çalıştırıcısı üzerinde Hell ikilisinin belirli bir sürümünü yükleme.
* [Kubernetes hazırlama](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): helm2, kustomize veya kompose kullanan dağıtımlar için kullanılacak hazırlama bildirim dosyası.
* [Kubernetes Lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): bildirim dosyalarınızı doğrulayın/Lint.
