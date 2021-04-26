---
title: Azure için Azure RBAC yay etkin Kubernetes kümeleri
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc etkin Kubernetes kümelerinde yetkilendirme denetimleri için Azure RBAC kullanma
ms.openlocfilehash: 0ee5f86ce12a39d86754d2e6e88263d8a03a012b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304214"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc etkin Kubernetes kümeleriyle Azure Active Directory tümleştirme

Kubernetes [Clusterrolebinding ve rolebinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) nesne türleri, Kubernetes 'te yerel olarak yetkilendirme tanımlamaya yardımcı olur. Bu özelliği kullanarak, kümedeki yetkilendirme denetimlerini denetlemek için Azure 'daki Azure Active Directory ve rol atamalarını kullanabilirsiniz. Bu, artık dağıtım, Pod ve hizmet gibi Kubernetes nesnelerinizi okuma, yazma ve silme işlemlerini bir daha fazla denetim için Azure rol atamalarını kullanabilirsiniz.

[Azure RBAC-Azure Arc etkin Kubernetes](conceptual-azure-rbac.md) makalesinde bu özelliğe kavramsal bir genel bakış sunulmaktadır.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Azure CLI 'yı sürüme yüklemek veya yükseltmek](https://docs.microsoft.com/cli/azure/install-azure-cli) >= 2.16.0

- `connectedk8s`Sürüm >= 1.1.0 Azure CLI uzantısını yükler:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    `connectedk8s`Uzantı zaten yüklüyse, aşağıdaki komutu kullanarak en son sürüme güncelleştirebilirsiniz: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Mevcut bir Azure Arc, Kubernetes bağlı kümesini etkinleştirdi.
    - Henüz bir kümeye bağlanmadıysanız [hızlı](quickstart-connect-cluster.md)başlangıçlarımızı kullanın.
    - [Aracılarınızı sürüme yükseltin](agent-upgrade.md#manually-upgrade-agents) >= 1.1.0.

> [!NOTE]
> Bu özellik, kullanıcının kümeye erişiminin olmadığı elastik Kubernetes hizmeti veya Google Kubernetes altyapısı gibi bulut sağlayıcılarının yönetilen Kubernetes teklifleri için ayarlanamaz `apiserver` . Azure Kubernetes Service (AKS) kümeleri için bu [özellik yerel olarak kullanılabilir](../../aks/manage-azure-rbac.md) ve aks kümesinin Azure yaya bağlanmasını gerektirmez.

## <a name="set-up-azure-ad-applications"></a>Azure AD uygulamalarını ayarlama

### <a name="create-server-application"></a>Sunucu uygulaması oluştur

1. Yeni bir Azure AD uygulaması oluşturun ve `appId` daha sonraki adımlarda kullanılan değerini alın `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Uygulama grubu üyeliği taleplerini güncelleştirin:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Bir hizmet sorumlusu oluşturun ve `password` Bu özelliği daha sonra kümede etkinleştirmek üzere gerekli olan alan değerini alın `serverApplicationSecret` :

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Uygulama API 'SI izinleri verme:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Bu adım bir Azure kiracı yöneticisi tarafından yürütülmelidir.
    > * Üretimde bu özelliğin kullanımı için, her küme için farklı bir sunucu uygulaması oluşturmanız önerilir.

### <a name="create-client-application"></a>İstemci uygulaması oluştur

1. Yeni bir Azure AD uygulaması oluşturun ve sonraki adımlarda şu şekilde kullanılan ' AppID ' değerini alın `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Bu istemci uygulaması için bir hizmet sorumlusu oluşturun:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. `oAuthPermissionId`Sunucu uygulaması için ' i alın:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. İstemci uygulaması için gerekli izinleri verin:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Sunucu uygulaması için bir rol ataması oluşturma

Sunucu uygulamasının, isteği `Microsoft.Authorization/*/read` yapan kullanıcının isteğin bir parçası olan Kubernetes nesnelerinde yetkilendirilip yetkilendirilmediğini denetlemek için izinleri olması gerekir.

1. Aşağıdaki içeriklerle accessCheck.jsadlı bir dosya oluşturun:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Değerini `<subscription-id>` gerçek ABONELIK kimliğiyle değiştirin.

2. Yeni özel rolü oluşturmak için aşağıdaki komutu yürütün:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. Yukarıdaki komutun çıktısından, `id` daha sonraki adımlarda kullanılan alanın değerini, olarak depolayın `roleId` .

4. Sunucu uygulamasında, yukarıda oluşturulan rolü kullanarak atane olarak bir rol ataması oluşturun:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Kümede Azure RBAC 'yi etkinleştirme

1. Aşağıdaki komutu çalıştırarak, Arc etkin Kubernetes kümesinde Azure RBAC 'yi etkinleştirin:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Yukarıdaki komutu çalıştırmadan önce, `kubeconfig` makinedeki dosyanın Azure RBAC özelliğinin etkinleştirileceği kümeye işaret ettiğinden emin olun.
    > 2. `--skip-azure-rbac-list`Azure RBAC yerine Kubernetes yerel ClusterRoleBinding ve rolebinding nesnelerini kullanarak yetkilendirme denetimleri sırasında, Kullanıcı adları/e-posta/OID 'nin virgülle ayrılmış bir listesi için yukarıdaki komutla birlikte kullanın.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Apıver belirtiminde çalışan bağdaştırıcı olmayan genel bir küme için:

1. Kümenin her ana düğümüne SSH ekleyin ve aşağıdaki adımları yürütün:

    1. `apiserver`Bildirimi düzenleme modunda aç:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Aşağıdaki belirtimi altına ekleyin `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Aşağıdaki belirtimi altına ekleyin `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Aşağıdaki `apiserver` bağımsız değişkenleri ekleyin:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Kubernetes kümesi sürüm >= 1.19.0 ise, aşağıdakilerin `apiserver argument` de ayarlanması gerekir:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Pod 'u güncelleştirmek için düzenleyiciyi kaydedin ve kapatın `apiserver` .


### <a name="for-a-cluster-created-using-cluster-api"></a>Küme API 'SI kullanılarak oluşturulan bir küme için

1. Kimlik doğrulaması ve yetkilendirme Web kancası yapılandırma dosyalarını içeren koruma gizli anahtarını `from the workload cluster` makinenize kopyalayın:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. `namespace`Dosyadaki alanı, `azure-arc-guard-manifests.yaml` iş yükü kümelerini oluşturmak için özel kaynakları uyguladığınız yönetim kümesi içindeki ad alanı olarak değiştirin.

1. Bu bildirimi Uygula:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Şunu `KubeadmControlPlane` yürüterek nesneyi düzenleyin `kubectl edit kcp <clustername>-control-plane` :
    
    1. Aşağıdaki kod parçacığını ekleyin `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Aşağıdaki kod parçacığını ekleyin `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Aşağıdaki kod parçacığını ekleyin `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Nesneyi güncelleştirmek için Kaydet ve çıkar `KubeadmControlPlane` . Bu değişikliklerin iş yükü kümesinde tamamlanmasını bekleyin.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Kullanıcıların kümeye erişmesi için rol atamaları oluşturma

Azure Arc etkin Kubernetes kaynağı sahipleri, diğer kullanıcılara Kubernetes kümesine erişim vermek için yerleşik roller veya özel roller kullanabilir.

### <a name="built-in-roles"></a>Yerleşik roller

| Rol | Açıklama |
|---|---|
| [Azure Arc Kubernetes Görüntüleyici](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Bir ad alanındaki birçok nesneyi görmek için salt okuma erişimine izin verir. Bu rol, parolaların görüntülenmesine izin vermez. Bunun nedeni, `read` Gizli `ServiceAccount` dizi izinlerinin ad alanındaki kimlik bilgilerine erişimini olanaklı hale, bu da `ServiceAccount` (bir ayrıcalık yükseltme BIÇIMI) kullanarak API erişimine izin vermeyi sağlayacaktır. |
| [Azure Arc Kubernetes yazıcı](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Bir ad alanındaki nesnelerin çoğuna okuma/yazma erişimi sağlar. Bu rol, rolleri veya rol bağlamalarını görüntülemeye veya değiştirmeye izin vermez. Bununla birlikte, bu rol, ad alanı içinde her türlü `ServiceAccount` API erişim düzeylerini elde etmek için kullanılabilir `ServiceAccount` . |
| [Azure Arc Kubernetes Yöneticisi](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Yönetici erişimine izin verir. RoleBinding kullanılarak bir ad alanı içinde verilmek üzere tasarlanmıştır. Bir RoleBinding içinde kullanılırsa, ad alanı içinde rol ve rol bağlamaları oluşturma özelliği de dahil olmak üzere bir ad alanındaki kaynakların çoğuna okuma/yazma erişimi sağlar. Bu rol, kaynak kotasına veya ad alanının kendine yazma erişimine izin vermez. |
| [Azure Arc Kubernetes Küme Yöneticisi](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Süper Kullanıcı erişiminin herhangi bir kaynakta herhangi bir eylemi yürütmesine izin verir. Bir ClusterRoleBinding içinde kullanıldığında, kümedeki her kaynak ve tüm ad alanlarında tam denetim sağlar. Bir RoleBinding 'te kullanıldığında, rol bağlamanın ad alanındaki her kaynak için ad alanının kendisi de dahil olmak üzere tam denetim sağlar.|

Azure portal küme kaynağının dikey penceresinde yay etkinleştirilmiş Kubernetes kümesi kapsamındaki rol atamaları oluşturabilirsiniz `Access Control (IAM)` . Azure CLı komutlarını aşağıda gösterildiği gibi da kullanabilirsiniz:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

Burada `AZURE-AD-ENTITY-ID` bir Kullanıcı adı (örneğin, testuser@mytenant.onmicrosoft.com ) veya hizmet sorumlusu da olabilir `appId` .

Küme içindeki belirli bir ad alanı kapsamına alınmış bir rol ataması oluşturmaya yönelik başka bir örnek aşağıda verilmiştir.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Küme kapsamındaki rol atamaları Azure portal veya CLı kullanılarak oluşturulabilir, ancak ad alanları kapsamındaki rol atamaları yalnızca CLı kullanılarak oluşturulabilir.

### <a name="custom-roles"></a>Özel roller

Rol atamalarında kullanım için kendi rol tanımınızı oluşturmayı tercih edebilirsiniz.

Bir kullanıcının yalnızca dağıtımları okumasına izin veren bir rol tanımı örneğini adım adım inceleyin. Daha fazla bilgi için, [bir rol tanımı oluşturmak için kullanabileceğiniz veri eylemlerinin tam listesine](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)bakın.

Aşağıdaki JSON nesnesini custom-role.jsadlı bir dosyaya kopyalayın. `<subscription-id>`Yer tutucusunu gerçek ABONELIK kimliğiyle değiştirin. Aşağıdaki özel rol, veri eylemlerinden birini kullanır ve rol atamasının oluşturulduğu kapsamdaki tüm dağıtımları (küme/ad alanı) görüntülemenizi sağlar.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Aşağıdaki komutu kaydettiğiniz klasörden çalıştırarak rol tanımını oluşturun `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Bu özel rol tanımını kullanarak bir rol ataması oluşturun:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Kubectl 'yi Kullanıcı kimlik bilgileriyle yapılandırma

`kubeconfig`Kümeye erişmek için gereken dosyayı almanın iki yolu vardır:
1. [](cluster-connect.md) `az connectedk8s proxy` Azure Arc etkin Kubernetes kümesinin küme bağlama özelliğini () kullanın.
1. `kubeconfig`Diğer tüm kullanıcılar ile Küme Yöneticisi paylaşımları dosyası.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Küme Connect özelliğini kullanarak kümeye erişiyorsanız

Proxy işlemini başlatmak için aşağıdaki komutu yürütün:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Proxy işlemi çalıştıktan sonra, [isteklerinizi kümeye göndermeye başlamak](#sending-requests-to-cluster)için konsolunuza başka bir sekme açabilirsiniz.

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Küme Yöneticisi `kubeconfig` dosyayı sizinle paylaşmışsa 

1. Kullanıcının kimlik bilgilerini ayarlamak için aşağıdaki komutu yürütün:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. `kubeconfig`Daha önce oluşturduğunuz dosyayı açın. Altında `contexts` , küme noktalarıyla ilişkili bağlamı, önceki adımda oluşturulan kullanıcı kimlik bilgileri ile doğrulayın.

1. Kullanıcı Yapılandırması altına **yapılandırma modu** ayarı ekle:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>İstekler kümeye gönderiliyor

1. Herhangi bir `kubectl` komutu çalıştırın. Örnek:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Tarayıcı tabanlı bir kimlik doğrulaması istendiğinde, cihaz oturum açma URL 'sini kopyalayın `https://microsoft.com/devicelogin` ve Web tarayıcınızda açın.

1. Konsolunuza yazdırılmış kodu girin, terminalinizdeki kodu kopyalayıp cihaz kimlik doğrulama giriş istemine yapıştırın.

1. Kullanıcı adı ( testuser@mytenant.onmicrosoft.com ) ve ilişkili parolayı girin.

1. Bunun gibi bir hata iletisi görürseniz, istenen kaynağa erişim yetkiniz yok demektir:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Yöneticinin, bu kullanıcıya kaynak üzerinde erişim yetkisi veren yeni bir rol ataması oluşturması gerekir.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Küme bağlantısı](cluster-connect.md) kullanarak kümeye güvenli bir şekilde bağlanma