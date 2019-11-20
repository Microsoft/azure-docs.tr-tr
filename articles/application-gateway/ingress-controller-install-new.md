---
title: Yeni bir Application Gateway giriş denetleyicisi oluşturma
description: Bu makalede yeni bir Application Gateway Application Gateway giriş denetleyicisi dağıtma hakkında bilgi sağlanır.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 30b5f6593d2d2ca17ad600a55f9dc7e2a379f0f0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795941"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Yeni bir Application Gateway kullanarak Application Gateway giriş denetleyicisi (AGIC) nasıl yüklenir

Aşağıdaki yönergelerde Application Gateway giriş denetleyicisinin (AGIC), önceden var olan bileşenlere sahip olmayan bir ortama yükleneceği varsayılır.

## <a name="required-command-line-tools"></a>Gerekli komut satırı araçları

Aşağıdaki tüm komut satırı işlemleri için [Azure Cloud Shell](https://shell.azure.com/) kullanılmasını öneririz. Kabuğunu shell.azure.com adresinden başlatın veya bağlantıya tıklayın:

[![Ekleme başlatma](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell'i başlatma")](https://shell.azure.com)

Alternatif olarak, aşağıdaki simgeyi kullanarak Azure portal Cloud Shell başlatın:

![Portal başlatma](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure Cloud Shell](https://shell.azure.com/) tüm gerekli araçlara zaten sahip. Başka bir ortam kullanmayı seçmeniz gerekir, lütfen aşağıdaki komut satırı araçlarının yüklü olduğundan emin olun:

* `az`-Azure CLı: [yükleme yönergeleri](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`-Kubernetes komut satırı aracı: [yükleme yönergeleri](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`-Kubernetes Paket Yöneticisi: [yükleme yönergeleri](https://github.com/helm/helm/releases/latest)
* `jq`-komut satırı JSON işlemcisi: [yükleme yönergeleri](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Kimlik oluşturma

Azure Active Directory (AAD) [hizmet sorumlusu nesnesi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)oluşturmak için aşağıdaki adımları izleyin. Lütfen `appId`, `password`ve `objectId` değerlerini kaydedin-bu, aşağıdaki adımlarda kullanılacaktır.

1. AD hizmet sorumlusu oluşturma ([RBAC hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```bash
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    JSON çıkışındaki `appId` ve `password` değerleri aşağıdaki adımlarda kullanılacaktır


1. Yeni hizmet sorumlusunun `objectId` almak için önceki komutun çıktısından `appId` kullanın:
    ```bash
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Bu komutun çıktısı, aşağıdaki Azure Resource Manager şablonunda kullanılacak `objectId`.

1. Azure Resource Manager şablonu dağıtımında kullanılacak parametre dosyasını daha sonra oluşturun.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    **RBAC** etkin bir küme dağıtmak için `aksEnabledRBAC` alanını `true` olarak ayarlayın

## <a name="deploy-components"></a>Bileşenleri dağıtma
Bu adım, aboneliğinize aşağıdaki bileşenleri ekleyecek:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview) v2
- 2 [alt ağı](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) olan [sanal ağ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Genel IP Adresi](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [AAD Pod kimliği](https://github.com/Azure/aad-pod-identity/blob/master/README.md) tarafından kullanılacak [yönetilen kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. Azure Resource Manager şablonunu indirin ve şablonu gerektiği gibi değiştirin.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Azure Resource Manager şablonunu `az cli`kullanarak dağıtın. Bu işlem 5 dakikaya kadar sürebilir.
    ```bash
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Dağıtım tamamlandıktan sonra, dağıtım çıkışını `deployment-outputs.json`adlı bir dosyaya indirin.
    ```bash
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Application Gateway giriş denetleyicisi 'ni ayarlama

Önceki bölümdeki yönergelerden yeni bir AKS kümesi ve bir Application Gateway oluşturup yapılandırdık. Şimdi yeni Kubernetes altyapısına örnek bir uygulama ve giriş denetleyicisi dağıtmaya hazırsınız.

### <a name="setup-kubernetes-credentials"></a>Kubernetes kimlik bilgilerini ayarlama
Aşağıdaki adımlarda, yeni Kubernetes kümenize bağlanmak için kullanabilmemiz için [kubectl](https://kubectl.docs.kubernetes.io/) komutunu kurun. [Cloud Shell](https://shell.azure.com/) `kubectl` zaten yüklü. Kubernetes kimlik bilgilerini almak için `az` CLı kullanacağız.

Yeni dağıtılan AKS ([daha fazla bilgi](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)) için kimlik bilgilerini alın:
```bash
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>AAD Pod kimliğini yükler
  Azure Active Directory Pod kimliği, [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)belirteç tabanlı erişim sağlar.

  [AAD Pod kimliği](https://github.com/Azure/aad-pod-identity) , Kubernetes kümenize aşağıdaki bileşenleri ekleyecek:
   * Kubernetes [CRD 'ler](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
   * [Yönetilen kimlik denetleyicisi (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) bileşeni
   * [Düğüm yönetilen kimliği (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) bileşeni


AAD Pod kimliğini kümenize yüklemek için:

   - *RBAC etkin* AKS kümesi

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
    ```

   - *RBAC devre dışı* AKS kümesi

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
    ```

### <a name="install-helm"></a>Held 'yi yükler
[Held](https://docs.microsoft.com/azure/aks/kubernetes-helm) , Kubernetes için bir paket yöneticisidir. `application-gateway-kubernetes-ingress` paketini yüklemek için bunu kullanacağız:

1. [Held](https://docs.microsoft.com/azure/aks/kubernetes-helm) 'yi yükleyip `application-gateway-kubernetes-ingress` Held paketi eklemek için aşağıdakileri çalıştırın:

    - *RBAC etkin* AKS kümesi

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC devre dışı* AKS kümesi

        ```bash
        helm init
        ```

1. AGIC Held deposunu ekleyin:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Giriş denetleyicisi Held grafiğini yükler

1. Yukarıda oluşturulan `deployment-outputs.json` dosyasını kullanın ve aşağıdaki değişkenleri oluşturun.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. AGC-config. YAML 'yi indirin ve bu da AGIC 'yi yapılandırır:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Veya aşağıdaki YAML dosyasını kopyalayın: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Yeni indirilen Held-config. YAML dosyasını düzenleyin ve `appgw` ve `armAuth`bölümleri doldurun.
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Değerler:
     - `verbosityLevel`: AGIC günlük altyapısının ayrıntı düzeyini ayarlar. Olası değerler için [günlük düzeylerini](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) görüntüleyin.
     - `appgw.subscriptionId`: Application Gateway bulunduğu Azure abonelik KIMLIĞI. Örnek: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Application Gateway oluşturulduğu Azure Kaynak grubunun adı. Örnek: `app-gw-resource-group`
     - `appgw.name`: Application Gateway adı. Örnek: `applicationgatewayd0f0`
     - `appgw.shared`: Bu Boole bayrağı `false`için varsayılan olarak ayarlanmalıdır. `true` olarak ayarlayın, [paylaşılan bir Application Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)gerekir.
     - `kubernetes.watchNamespace`: AGIC 'in izlemeniz gereken ad alanını belirtin. Bu tek bir dize değeri ya da virgülle ayrılmış ad alanları listesi olabilir.
    - `armAuth.type`: `aadPodIdentity` veya `servicePrincipal`
    - `armAuth.identityResourceID`: Azure tarafından yönetilen kimliğin kaynak KIMLIĞI
    - `armAuth.identityClientId`: kimliğin Istemci KIMLIĞI. Kimlik hakkında daha fazla bilgi için aşağıya bakın
    - `armAuth.secretJSON`: yalnızca hizmet sorumlusu gizli türü seçildiğinde gereklidir (`armAuth.type` `servicePrincipal`olarak ayarlandığında) 


   > [!NOTE]
   > `identityResourceID` ve `identityClientID`, [kimlik oluşturma](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) adımları sırasında oluşturulan değerlerdir ve aşağıdaki komut kullanılarak yeniden edinilebilir:
   > ```bash
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > Yukarıdaki komutta `<resource-group>`, Application Gateway kaynak grubudur. `<identity-name>` oluşturulan kimliğin adıdır. Belirli bir abonelik için tüm kimlikler şu kullanılarak listelenebilir: `az identity list`


1. Application Gateway giriş denetleyicisi paketini yükler:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Örnek uygulama yükler
Artık Application Gateway, AKS ve AGIC yüklediğimiz için, [Azure Cloud Shell](https://shell.azure.com/)aracılığıyla örnek bir uygulama yükleyebiliriz:

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternatif olarak şunları yapabilirsiniz:

* Yukarıdaki YAML dosyasını indirin:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* YAML dosyasını uygulayın:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Diğer örnekler
Bu [nasıl yapılır Kılavuzu](ingress-controller-expose-service-over-http-https.md) , http veya HTTPS aracılığıyla bir aks hizmetini Application Gateway ile Internet 'e sunmaya yönelik daha fazla örnek içerir.
