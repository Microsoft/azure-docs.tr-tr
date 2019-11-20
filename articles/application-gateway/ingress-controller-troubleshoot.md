---
title: Application Gateway giriş denetleyicisi sorunlarını giderme
description: Bu makale, Application Gateway giriş denetleyicisindeki yaygın soruların ve/veya sorunların nasıl giderileceği hakkında belgeler sağlar.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795504"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Giriş denetleyicisindeki yaygın soruların veya sorunların sorunlarını giderme

[Azure Cloud Shell](https://shell.azure.com/) , aks ve agic yüklemenizin sorunlarını gidermek için en kolay yoldur. Kabuğunu [Shell.Azure.com](https://shell.azure.com/) adresinden başlatın veya bağlantıya tıklayın:

[![Ekleme başlatma](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell'i başlatma")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Basit bir Kubernetes uygulamasıyla test etme

Aşağıdaki adımlarda varsayılmaktadır:
  - Gelişmiş ağ özellikli bir AKS kümeniz var
  - AGIC, AKS kümesine yüklendi
  - AKS kümeniz ile paylaşılan bir VNET üzerinde zaten bir Application Gateway hamalmış olursunuz

Application Gateway + AKS + AGIC yüklemesinin doğru şekilde ayarlandığından emin olmak için en basit olası uygulamayı dağıtın:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
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
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Yukarıdaki betikten bir kerede tüm satırları kopyalayıp bir [Azure Cloud Shell](https://shell.azure.com/)yapıştırın. Lütfen tüm komutun kopyalanıp `cat` ve son `EOF`dahil olmak üzere kopyalandığından emin olun.

![uygulayabilirsiniz](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Uygulamanın başarıyla dağıtımı, AKS kümenizin üzerinde başarılı bir şekilde uygulandıktan sonra yeni bir pod, hizmet ve giriş olur.

[Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide`ile pod 'nin listesini alın.
' Test-agic-App-Pod ' adlı bir pod 'un oluşturulmasını bekledik. Bir IP adresi olur. Bu adres, AKS ile kullanılan Application Gateway sanal ağı dahilinde olmalıdır.

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Hizmetlerin listesini alın: `kubectl get services -o wide`. ' Test-agic-App-Service ' adlı bir hizmet görmemiz beklenir.

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Giriş listesi listesini alın: `kubectl get ingress`. ' Test-agic-App-ınress ' adlı bir giriş kaynağı oluşturulması Bekleniyorduk. Kaynak, ' test.agic.contoso.com ' ana bilgisayar adına sahip olacak.

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Ayırımların biri AGIC olacaktır. `kubectl get pods`, biri ' ınress-Azure ' ile başlayacak olan bir pods listesini gösterir. Başarılı bir dağıtımımız olduğunu doğrulamak için, bu Pod 'ın tüm günlüklerini `kubectl logs <name-of-ingress-controller-pod>` ile alın. Başarılı bir dağıtım şu satırları günlüğe ekledi:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternatif olarak, [Cloud Shell](https://shell.azure.com/) yalnızca `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`ile başarılı Application Gateway yapılandırmayı belirten satırları alabiliriz; burada `<ingress-azure....>` agic Pod 'un tam adı olmalıdır.

Application Gateway aşağıdaki yapılandırma uygulanmış olacaktır:

- Dinleyici: dinleyici](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png) ![

- Yönlendirme kuralı: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Arka uç havuzu:
  - Arka uç adres havuzunda bir IP adresi olacak ve daha önce `kubectl get pods -o wide`
![backend_pool ile gözlemlediğimiz Pod 'un IP adresiyle eşleşmesi gerekir](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Son olarak, yeni dağıtılan uygulamaya HTTP bağlantısı kurmak için [Cloud Shell](https://shell.azure.com/) içinden `cURL` komutunu kullanabiliriz:

1. Application Gateway genel IP adresini almak için `kubectl get ingress` kullanın
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` kullan

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

`HTTP/1.1 200 OK` sonucu Application Gateway + AKS + AGIC sisteminin beklendiği gibi çalıştığını gösterir.


## <a name="inspect-kubernetes-installation"></a>Kubernetes yüklemesini İncele

### <a name="pods-services-ingress"></a>Pods, hizmetler, giriş
Application Gateway giriş denetleyicisi (AGIC) Şu Kubernetes kaynaklarını sürekli izler: [dağıtım](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) veya [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [hizmet](https://kubernetes.io/docs/concepts/services-networking/service/), giriş [](https://kubernetes.io/docs/concepts/services-networking/ingress/)


AGIC 'in beklenen şekilde çalışması için aşağıdakiler olması gerekir:
  1. Aks 'lerin bir veya daha fazla sağlıklı **Pod**olması gerekir.
     `apsnetapp`sahip bir pod 'niz varsa, bu [Cloud Shell](https://shell.azure.com/) `kubectl get pods -o wide --show-labels` ile, çıktınızdan şöyle görünebildiğini doğrulayın:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Bir veya daha fazla **hizmet**, eşleşen `selector` Etiketler aracılığıyla yukarıdaki Pod 'ye başvuruyor.
     Bunu `kubectl get services -o wide` [Cloud Shell](https://shell.azure.com/) doğrulayın
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. Yukarıdaki hizmete başvuran `kubernetes.io/ingress.class: azure/application-gateway`**giriş, `kubectl get ingress -o wide --show-labels`** Ile [Cloud Shell](https://shell.azure.com/) doğrulayın
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Yukarıdaki giriş açıklamalarını görüntüleyin: `kubectl get ingress aspnetapp -o yaml` (`aspnetapp` giriş adı ile değiştirin)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Giriş kaynağına `kubernetes.io/ingress.class: azure/application-gateway`ile açıklama eklenmelidir.
 

### <a name="verify-observed-namespace"></a>Gözlemlenen ad alanını doğrula

* Kubernetes kümesinde var olan ad alanlarını alın. Uygulamanız hangi ad alanı üzerinde çalışıyor? Bu ad alanını izliyor musunuz? Gözlemlenen ad alanlarını düzgün bir şekilde yapılandırma hakkında [birden çok ad alanı desteği](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) belgesine bakın.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC Pod, `default` ad alanında olmalıdır (bkz. sütun `NAMESPACE`). Sağlıklı pod, `STATUS` sütununda `Running` sahip olabilir. En az bir adet AGIC Pod olmalıdır.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* AGIC Pod sağlam değilse (yukarıdaki komuttan`STATUS` sütun `Running`):
  - şunları anlamak için günlükleri al: `kubectl logs <pod-name>`
  - Pod 'un önceki örneği için: `kubectl logs <pod-name> --previous`
  - daha fazla bağlam almak için pod 'ı açıkla: `kubectl describe pod <pod-name>`


* Bir Kubernetes [hizmeti](https://kubernetes.io/docs/concepts/services-networking/service/) [ve giriş](https://kubernetes.io/docs/concepts/services-networking/ingress/) kaynağınız var mı?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Giriş hesabınız [Şu şekilde açıklama](https://kubernetes.io/docs/concepts/services-networking/ingress/) eklendi: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC, yalnızca bu ek açıklamaya sahip Kubernetes giriş kaynaklarını izleyebilir.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC, belirli kritik hatalara karşı Kubernetes olaylarını yayar. Bunları görüntüleyebilirsiniz:
  - `kubectl get events --sort-by=.metadata.creationTimestamp` aracılığıyla terminalinizde
  - [Kubernetes Web Kullanıcı arabirimini (Pano)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) kullanarak tarayıcınızda


## <a name="logging-levels"></a>Günlüğe kaydetme düzeyleri

AGIC 'in 3 günlük düzeyi vardır. Düzey 1 varsayılan bir sayıdır ve en az sayıda günlük satırı gösterir.
Diğer yandan 5. düzey, ARM 'ye uygulanan yapılandırma için ayıklanmış içerik de dahil olmak üzere tüm günlükleri görüntüler.

Kubernetes topluluğu, [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) aracı için 9 günlük kayıt kurdu. Bu depoda, benzer anlamlarla birlikte bunlardan 3 ' ü kullanıyoruz:


| Ayrıntı | Açıklama |
|-----------|-------------|
|  1        | Varsayılan günlük düzeyi; başlangıç ayrıntılarını, uyarıları ve hataları gösterir |
|  3        | Olaylar ve değişiklikler hakkında genişletilmiş bilgiler; oluşturulan nesne listeleri |
|  5        | Sıralanan nesneleri günlüğe kaydeder; ARM 'ye uygulanan ayıklanmış JSON yapılandırmasını gösterir |


Ayrıntı düzeyleri, [helmconfig. YAML](#sample-helm-config-file) dosyasındaki `verbosityLevel` değişkeni aracılığıyla ayarlanabilir. [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)'ye gönderilen JSON yapılandırmasını almak için ayrıntı düzeyini `5` artırın:
  - [helmconfig. YAML](#sample-helm-config-file) içinde bir satıra `verbosityLevel: 5` ekleyin ve yeniden yükler
  - `kubectl logs <pod-name>` günlükleri al

### <a name="sample-helm-config-file"></a>Örnek HELI yapılandırma dosyası
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

