---
title: Kubernetes hizmetinde Dil Algılama kapsayıcısı çalıştırma
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Dil algılama kapsayıcısını, çalışan bir örnekle, Azure Kubernetes hizmetine dağıtın ve bir Web tarayıcısında test edin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6918218d8434c06f59b0738e60cad53b94b0a0b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939841"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Metin Analizi dil algılama kapsayıcısını Azure Kubernetes hizmetine dağıtma

Dil algılama kapsayıcısını dağıtmayı öğrenin. Bu yordam, yerel Docker Kapsayıcıları oluşturma, kapsayıcıları kendi özel kapsayıcı Kayıt defterinize gönderme, kapsayıcıyı bir Kubernetes kümesinde çalıştırma ve bir Web tarayıcısında test etme işlemlerini gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu yordam, yüklenmesi ve yerel olarak çalıştırılması gereken çeşitli araçlar gerektirir. Azure Cloud Shell kullanmayın.

* Bir Azure aboneliği kullanın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services) oluşturun.
* Bu yordamda kullanılan [örneği](https://github.com/Azure-Samples/cognitive-services-containers-samples) kopyalayabilmeniz için işletim sisteminiz için [Git](https://git-scm.com/downloads) .
* [Azure CLI](/cli/azure/install-azure-cli).
* [Docker altyapısı](https://www.docker.com/products/docker-engine) ve DOCKER CLI 'nın konsol penceresinde çalışıp çalışmadığını doğrulama.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Doğru fiyatlandırma katmanına sahip bir Azure kaynağı. Fiyatlandırma katmanlarının tümü bu kapsayıcı ile çalışmaz:
  * Yalnızca F0 veya standart fiyatlandırma katmanlarına sahip **metin analizi** kaynak.
  * S0 fiyatlandırma katmanı ile bilişsel **Hizmetler** kaynağı.

## <a name="running-the-sample"></a>Örneği çalıştırma

Bu yordam, dil algılama için bilişsel hizmetler kapsayıcı örneğini yükler ve çalıştırır. Örnek, biri istemci uygulaması, diğeri de bilişsel hizmetler kapsayıcısı için olmak üzere iki kapsayıcı içerir. Bu resimlerin her ikisini de Azure Container Registry göndereceğiz. Kendi kayıt defterinizde olduktan sonra, bu görüntülere erişmek ve kapsayıcıları çalıştırmak için bir Azure Kubernetes hizmeti oluşturun. Kapsayıcılar çalışırken, kapsayıcıların performansını izlemek için **kubectl** CLI kullanın. İstemci uygulamasına bir HTTP isteğiyle erişin ve sonuçları görüntüleyin.

![Örnek kapsayıcıları çalıştırmanın kavramsal fikri](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Örnek kapsayıcılar

Örnek, biri ön uç Web sitesi için olmak üzere iki kapsayıcı görüntülerine sahiptir. İkinci görüntü, metnin algılanan dilini (kültür) döndüren dil algılama kapsayıcısıdır. İşiniz bittiğinde her iki kapsayıcıya dış IP 'den erişilebilir.

### <a name="the-language-frontend-container"></a>Dil ön uç kapsayıcısı

Bu web sitesi, dil algılama uç noktası isteklerini yapan kendi istemci tarafı uygulamanız ile eşdeğerdir. Yordam tamamlandığında, ile bir tarayıcıda Web sitesi kapsayıcısına erişerek bir karakter dizesinin algılanan dilini alırsınız `http://<external-IP>/<text-to-analyze>` . Bu URL 'ye bir örnek `http://132.12.23.255/helloworld!` . Tarayıcıdaki sonuç `English` .

### <a name="the-language-container"></a>Dil kapsayıcısı

Dil algılama kapsayıcısına, bu özel yordamda, herhangi bir dış istek tarafından erişilebilir. Kapsayıcı, standart bilişsel hizmetler kapsayıcısına özgü dil algılama API 'sinin kullanılabilir olması için hiçbir şekilde değiştirilmedi.

Bu kapsayıcı için bu API, dil algılama için bir POST isteğidir. Tüm bilişsel hizmetler kapsayıcılarında olduğu gibi, barındırılan Swagger bilgileriyle kapsayıcı hakkında daha fazla bilgi edinebilirsiniz `http://<external-IP>:5000/swagger/index.html` .

Bağlantı noktası 5000, bilişsel hizmetler kapsayıcılarıyla kullanılan varsayılan bağlantı noktasıdır.

## <a name="create-azure-container-registry-service"></a>Azure Container Registry hizmeti oluşturma

Kapsayıcıyı Azure Kubernetes hizmetine dağıtmak için kapsayıcı görüntülerinin erişilebilir olması gerekir. Görüntüleri barındırmak için kendi Azure Container Registry hizmetinizi oluşturun.

1. Azure CLı 'da oturum açın

    ```azurecli-interactive
    az login
    ```

1. `cogserv-container-rg`Bu yordamda oluşturulan her kaynağı tutan adlı bir kaynak grubu oluşturun.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Adınızın biçimiyle kendi Azure Container Registry oluşturun `registry` , örneğin `pattyregistry` . Adda tireler kullanmayın veya karakterlerin altını çizin.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    **Loginserver** özelliğini almak için sonuçları kaydedin. Bu, daha sonra dosyasında kullanılan barındırılan kapsayıcının adresinin bir parçası olacaktır `language.yml` .

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Kapsayıcı kayıt defterinizde oturum açın. Kayıt defterinize görüntü gönderebilmeniz için önce oturum açmanız gerekir.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Web sitesi Docker görüntüsünü al

1. Bu yordamda kullanılan örnek kod, bilişsel hizmetler kapsayıcıları örnekleri deposundadır. Depoyu, örneğin yerel kopyasına sahip olacak şekilde kopyalayın.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Depo yerel bilgisayarınızda olduktan sonra, [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) dizininde Web sitesini bulun. Bu web sitesi, dil algılama kapsayıcısında barındırılan dil algılama API 'sini çağıran istemci uygulaması gibi davranır.  

1. Bu web sitesi için Docker görüntüsünü oluşturun. Aşağıdaki komutu çalıştırdığınızda, konsolunun Dockerfile dosyasının bulunduğu [\Frontendservice](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) dizininde olduğundan emin olun:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Kapsayıcı kayıt defterinizde sürümü izlemek için etiketi, gibi bir sürüm biçimiyle ekleyin `v1` .

1. Görüntüyü kapsayıcı Kayıt defterinize gönderin. Bu birkaç dakika sürebilir.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Bir `unauthorized: authentication required` hata alırsanız komutuyla oturum açın `az acr login --name <your-container-registry-name>` . 

    İşlem tamamlandığında, sonuçların şuna benzer olması gerekir:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Dil algılama Docker görüntüsünü al

1. Docker görüntüsünün en son sürümünü yerel makineye çekin. Bu birkaç dakika sürebilir. Bu kapsayıcının daha yeni bir sürümü varsa, değerini `1.1.006770001-amd64-preview` daha yeni bir sürüm olarak değiştirin.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Resmi kapsayıcı kayıt defteriyle etiketleyin. En son sürümü bulun ve `1.1.006770001-amd64-preview` daha yeni bir sürüme sahipseniz sürümü değiştirin. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Görüntüyü kapsayıcı Kayıt defterinize gönderin. Bu birkaç dakika sürebilir.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Container Registry kimlik bilgilerini al

Bu yordamda daha sonra oluşturduğunuz Azure Kubernetes hizmeti ile kapsayıcı kayıt defterinizi bağlamak üzere gerekli bilgileri almak için aşağıdaki adımlar gereklidir.

1. Hizmet sorumlusu oluşturun.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    `appId`3. adımdaki atanan parametrenin sonuç değerini kaydedin `<appId>` . `password`Sonraki bölümün istemci gizli parametresi için öğesini kaydedin `<client-secret>` .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Kapsayıcı kayıt defteri KIMLIĞINIZI alın.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Kapsam parametre değeri için çıktıyı bir `<acrId>` sonraki adımda kaydedin. Şöyle görünür:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    3. adım için tam değeri bu bölümde saklayın.

1. AKS kümesine yönelik doğru erişimi, kapsayıcı kayıt defterinizde depolanan görüntüleri kullanmak üzere vermek için, bir rol ataması oluşturun. `<appId>`Ve `<acrId>` değerlerini önceki iki adımda toplanan değerlerle değiştirin.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes hizmeti oluşturma

1. Kubernetes kümesi oluşturun. Tüm parametre değerleri, ad parametresi dışında önceki bölümden oluşur. Kendisini kimin oluşturduğunu ve bunun amacını belirten bir ad seçin `patty-kube` .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Bu adım birkaç dakika sürebilir. Sonuç:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Hizmet oluşturuldu ancak Web sitesi kapsayıcısı veya dil algılama kapsayıcısı henüz yok.  

1. Kubernetes kümesinin kimlik bilgilerini alın.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Kubernetes hizmetinize Orchestration tanımını yükleyin

Bu bölümde, Azure Kubernetes hizmeti ile konuşmak için **kubectl** CLI kullanılmaktadır.

1. Orchestration tanımını yüklemeden önce, **kubectl** 'nin düğümlere erişimi olup olmadığını denetleyin.

    ```console
    kubectl get nodes
    ```

    Yanıt şöyle görünür:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Aşağıdaki dosyayı kopyalayın ve adlandırın `language.yml` . Dosyada bir `service` bölüm ve `deployment` her biri iki kapsayıcı türü, `language-frontend` Web sitesi kapsayıcısı ve algılama kapsayıcısı için bir bölüm bulunur `language` .

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. `language.yml`Kendi kapsayıcı kayıt defteri görüntü adlarınızı, istemci gizli anahtarı ve metin analizi ayarlarınızı eklemek için aşağıdaki tabloya göre dil ön uç dağıtım hatlarını değiştirin.

    Dil ön uç dağıtım ayarları|Amaç|
    |--|--|
    |Satır 32<br> `image` özelliði|Container Registry ön uç görüntüsünün görüntü konumu<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Satır 44<br> `name` özelliði|Resim için, önceki bölümde olduğu gibi bilinen gizli dizi Container Registry `<client-secret>` .|

1. `language.yml`Kendi kapsayıcı kayıt defteri görüntü adlarınızı, gizli anahtar ve metin analizi ayarlarınızı eklemek için aşağıdaki tabloya göre dil dağıtım çizgilerini değiştirin.

    |Dil dağıtım ayarları|Amaç|
    |--|--|
    |Satır 78<br> `image` özelliði|Container Registry dil görüntüsünün görüntü konumu<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Satır 95<br> `name` özelliði|Resim için, önceki bölümde olduğu gibi bilinen gizli dizi Container Registry `<client-secret>` .|
    |Satır 91<br> `apiKey` özelliði|Metin analizi kaynak anahtarınız|
    |Satır 92<br> `billing` özelliði|Metin analizi kaynağınız için faturalandırma uç noktası.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    **Apikey** ve **faturalandırma uç noktası** Kubernetes Orchestration tanımının bir parçası olarak ayarlandığı için, Web sitesi kapsayıcısının bu ilgili bilgileri bilmeleri veya isteğin bir parçası olarak iletilmesi gerekmez. Web sitesi kapsayıcısı, Orchestrator adına göre dil algılama kapsayıcısını ifade eder `language` .

1. Oluşturduğunuz ve kaydettiğiniz klasörden bu örnek için Orchestration tanım dosyasını yükleyin `language.yml` .

    ```console
    kubectl apply -f language.yml
    ```

    Yanıt:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Kapsayıcılardan dış IP 'Leri al

İki kapsayıcı için, `language-frontend` ve `language` hizmetlerinin çalıştığını doğrulayın ve dış IP adresini alın.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

`EXTERNAL-IP`Hizmeti için bekliyor olarak gösteriliyorsa, sonraki adıma geçmeden önce IP adresi gösterilene kadar komutu yeniden çalıştırın.

## <a name="test-the-language-detection-container"></a>Dil algılama kapsayıcısını test etme

Bir tarayıcı açın ve önceki bölümden kapsayıcının dış IP adresine gidin `language` : `http://<external-ip>:5000/swagger/index.html` . `Try it`Dil algılama uç noktasını test etmek IÇIN API 'nin özelliğini kullanabilirsiniz.

![Kapsayıcının Swagger belgelerini görüntüleme](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>İstemci uygulama kapsayıcısını test etme

Tarayıcıdaki URL 'YI `language-frontend` aşağıdaki biçimi kullanarak kapsayıcının dış IP 'sine değiştirin: `http://<external-ip>/helloworld` . ' Nin Ingilizce kültür metni `helloworld` olarak tahmin edilir `English` .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Küme ile işiniz bittiğinde Azure kaynak grubunu silin.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>İlgili bilgiler

* [Docker kullanıcıları için kubectl](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilişsel Hizmetler Kapsayıcıları](../cognitive-services-container-support.md)
