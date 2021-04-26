---
title: Küme bağlantısı 'nı kullanarak Azure Arc etkin Kubernetes kümelerine bağlanma
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc etkin Kubernetes kümelerine güvenli bir şekilde bağlanmak için küme bağlantısı 'nı kullanma
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451200"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Küme bağlantısı 'nı kullanarak Azure Arc etkin Kubernetes kümelerine bağlanma

Küme bağlantısı ile, Azure Arc etkin Kubernetes kümelerine, güvenlik duvarında herhangi bir gelen bağlantı noktasının etkinleştirilmesini gerektirmeden güvenli bir şekilde bağlanabilirsiniz. `apiserver`Arc etkin Kubernetes kümesine erişim aşağıdaki senaryoları sağlar:
* Etkileşimli hata ayıklamayı ve sorun gidermeyi etkinleştirin.
* [Özel konumlar](custom-locations.md) ve üzerine oluşturulan diğer kaynaklar için Azure hizmetlerine küme erişimi sağlayın.

Bu özelliğe kavramsal genel bakış, [cluster Connect-Azure Arc etkin Kubernetes](conceptual-cluster-connect.md) makalesinde bulunabilir.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Önkoşullar   

- [Azure CLI 'yı sürüme yüklemek veya yükseltmek](https://docs.microsoft.com/cli/azure/install-azure-cli) >= 2.16.0

- `connectedk8s`Sürüm >= 1.1.0 Azure CLI uzantısını yükler:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Uzantıyı zaten yüklediyseniz `connectedk8s` , uzantıyı en son sürüme güncelleştirin:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Mevcut bir Azure Arc, Kubernetes bağlı kümesini etkinleştirdi.
    - Henüz bir kümeye bağlanmadıysanız [hızlı](quickstart-connect-cluster.md)başlangıçlarımızı kullanın.
    - [Aracılarınızı sürüme yükseltin](agent-upgrade.md#manually-upgrade-agents) >= 1.1.0.

- Dosyanın sorun kümesine işaret ettiği bir makinede aşağıdaki komutu çalıştırarak herhangi bir Azure Arc etkin Kubernetes kümesinde Küme bağlantısını etkinleştirin `kubeconfig` :

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- [Bir Kubernetes kümesini Azure yaya bağlama](quickstart-connect-cluster.md#meet-network-requirements)altında belirtilenlerden bahsetenlere ek olarak, giden erişim için aşağıdaki uç noktaları etkinleştirin:

    | Uç Nokta | Bağlantı noktası |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Kullanım

Küme bağlantısı özelliği ile iki kimlik doğrulama seçeneği desteklenir: 
* Azure Active Directory (Azure AD) 
* Hizmet hesabı belirteci

### <a name="option-1-azure-active-directory"></a>Seçenek 1: Azure Active Directory

1. `kubeconfig` `apiserver` Kubernetes kümenizi işaret eden dosya Ile, Azure AD varlığı (hizmet sorumlusu veya Kullanıcı) için erişim gerektiren bir clusterrolebinding veya rolebinding oluşturun:

    **Kullanıcı için:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Azure AD uygulaması için:**

    1. `objectId`Azure AD uygulamanızla ilişkili ' i alın:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Bu kümeye erişmesi gereken Azure AD varlığı (hizmet sorumlusu veya Kullanıcı) için bir ClusterRoleBinding veya RoleBinding oluşturun:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Azure AD 'nin ilgi varlığını kullanarak Azure CLı 'de oturum açtıktan sonra küme `kubeconfig` ile her yerden (kümeyi çevreleyen güvenlik duvarının dışında) iletişim kurmak için gereken küme bağlantısını alın:

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. `kubectl`İstekleri kümeye göndermek için kullanın:

    ```console
    kubectl get pods
    ```
    
    Şimdi, ad alanı altındaki tüm yığınların listesini içeren kümeden bir yanıt görmeniz gerekir `default` .

### <a name="option-2-service-account-bearer-token"></a>Seçenek 2: hizmet hesabı taşıyıcı belirteci

1. `kubeconfig` `apiserver` Kubernetes kümenizi işaret eden dosya ile herhangi bir ad alanında bir hizmet hesabı oluşturun (aşağıdaki komut onu varsayılan ad alanında oluşturur):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Bu [hizmet hesabına kümede uygun izinleri](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding)vermek Için clusterrolebinding veya rolebinding oluşturun:

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Aşağıdaki komutları kullanarak hizmet hesabının belirtecini alın

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Küme `kubeconfig` ile her yerden (kümeyi çevreleyen güvenlik duvarının dışında) iletişim kurmak için gereken küme bağlantısını alın:

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. `kubectl`İstekleri kümeye göndermek için kullanın:

    ```console
    kubectl get pods
    ```

    Şimdi, ad alanı altındaki tüm yığınların listesini içeren kümeden bir yanıt görmeniz gerekir `default` .

## <a name="known-limitations"></a>Bilinen sınırlamalar

Kubernetes kümesine istek yaparken, kullanılan Azure AD varlığı 200 taneden fazla grubun bir parçası ise, bu bilinen bir sınırlama olduğu için aşağıdaki hata izlenir:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Bu hatayı almak için:
1. 200 ' den fazla grubun üyesi olma olasılığı daha düşük olan bir [hizmet sorumlusu](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)oluşturun.
1. Komutunu çalıştırmadan önce hizmet sorumlusu ile Azure CLı ['Da oturum açın](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) `az connectedk8s proxy` .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> Kümelerinizde [Azure AD RBAC](azure-rbac.md) ayarlama