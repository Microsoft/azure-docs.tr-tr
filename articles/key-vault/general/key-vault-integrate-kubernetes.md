---
title: Azure Key Vault’u Kubernetes ile tümleştirme
description: Bu öğreticide, Kubernetes pods 'ye bağlamak için gizli dizi kapsayıcısı depolama arabirimi (CSı) sürücüsünü kullanarak Azure anahtar kasanızdan gizli dizi ve gizli dizileri alırsınız.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 6cf76e980fab4e5be3f8c2c6d72baff05ab03815
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108396"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Öğretici: Kubernetes 'te gizli dizi için Azure Key Vault sağlayıcıyı yapılandırma ve çalıştırma

> [!IMPORTANT]
> Gizli dizileri deposu CSı sürücüsü, Azure teknik desteği tarafından desteklenmeyen açık kaynaklı bir projem. Lütfen CSı sürücü [GitHub](https://github.com/kubernetes-sigs/secrets-store-csi-driver)'DA, csı sürücüsü Key Vault tümleştirmesiyle ilgili tüm geri bildirimleri ve sorunları bildirin. Bu araç, kullanıcıların kümelere kendi kendine yüklenmesi ve topluluğumuza geri bildirim toplaması için sağlanır.

Bu öğreticide, gizli dizileri bir birim olarak Kubernetes Pod 'ye bağlamak için gizli dizi kapsayıcısı depolama arabirimi (CSI) sürücüsünü kullanarak Azure anahtar kasanızdan gizli dizi ve parola alırsınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yönetilen kimlikleri kullanın.
> * Azure CLı kullanarak bir Azure Kubernetes hizmeti (AKS) kümesi dağıtın.
> * Held 'yi, anahtar deposu, CSI sürücüsü ve Azure Key Vault sağlayıcısı 'nı CSı sürücüsü için yükler.
> * Azure Anahtar Kasası oluşturun ve gizli dizilerinizi ayarlayın.
> * Kendi SecretProviderClass nesneniz oluşturun.
> * Ana kasanızdan bağlı gizli dizileri kullanarak Pod 'nizi dağıtın.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* Bu öğreticiye başlamadan önce [Azure CLI](/cli/azure/install-azure-cli-windows)'yı yükleyebilirsiniz.

Bu öğreticide, Linux düğümlerinde Azure Kubernetes hizmetini çalıştırdığınız varsayılmaktadır.

## <a name="use-managed-identities"></a>Yönetilen kimlikleri kullanma

Bu diyagramda, yönetilen kimlik için AKS – Key Vault tümleştirme akışı gösterilmektedir:

![Yönetilen kimlik için AKS – Key Vault tümleştirme akışını gösteren diyagram](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Azure CLı kullanarak bir Azure Kubernetes hizmeti (AKS) kümesi dağıtma

Azure Cloud Shell kullanmanız gerekmez. Azure CLı yüklü olan komut isteminize (Terminal) sahip olmanız yeterli olacaktır. 

[Azure CLI kullanarak bir Azure Kubernetes hizmet kümesi dağıtma](../../aks/kubernetes-walkthrough.md)bölümünde "kaynak grubu oluşturma", "aks kümesi oluşturma" ve "kümeye bağlanma" bölümlerine gidin. 

> [!NOTE] 
> Pod kimliği kullanmayı planlıyorsanız önerilen ağ eklentisi olur `azure` . Daha fazla ayrıntı için [belgeye](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) başvurun. Aşağıdaki komutta gösterildiği gibi Kubernetes kümesini oluşturun:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Path ortam değişkeninizi](https://www.java.com/en/download/help/path.xml) indirdiğiniz *kubectl.exe* dosyası olarak ayarlayın.
2. Aşağıdaki komutu kullanarak Kubernetes sürümünüzü denetleyin ve istemci ve sunucu sürümünü çıkartır. İstemci sürümü, yüklediğiniz *kubectl.exe* dosyasıdır ve sunucu sürümü, kümenizin üzerinde çalıştığı Azure Kubernetes hizmetidir (aks).
    ```azurecli
    kubectl version
    ```
3. Kubernetes sürümünüzün 1.16.0 veya üzeri olduğundan emin olun. Windows kümeleri için Kubernetes sürümünüzün 1.18.0 veya üzeri olduğundan emin olun. Aşağıdaki komut hem Kubernetes kümesini hem de düğüm havuzunu yükseltir. Komutun yürütülmesi birkaç dakika sürebilir. Bu örnekte, kaynak grubu *Contosoresourcegroup* ve Kubernetes kümesi *Contosoakscluster*' dir.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Oluşturduğunuz AKS kümesinin meta verilerini göstermek için aşağıdaki komutu kullanın. Daha sonra kullanmak üzere **PrincipalId**, **ClientID**, **SubscriptionID** ve **noderesourcegroup** 'u kopyalayın. AKS kümesi, Yönetilen kimlikler etkinleştirilmiş olarak oluşturulmadıysa, **PrincipalId** ve **ClientID** null olacaktır. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Çıktıda her iki parametre de vurgulanır:
    
    ![Azure CLı 'nin PrincipalId ve ClientID değerleri ile ekran görüntüsü, ](../media/kubernetes-key-vault-2.png) ![ SubscriptionID ve nodeResourceGroup değerleriyle vurgulanan Azure CLI ekran görüntüsü](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Held ve gizlilikler deposunun CSı sürücüsünü yükleyip
> [!NOTE]
> Aşağıdaki yükleme yalnızca Linux üzerinde AKS üzerinde çalışmaktadır. Gizli dizileri depolama CSı sürücü yüklemesi hakkında daha fazla bilgi için bkz. [Azure Key Vault Provider for gizlilikler Store CSI Driver](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) 

Gizli anahtar deposu CSı sürücüsü ve Azure Key Vault sağlayıcısı 'nı yüklemek için, önce [Held](https://helm.sh/docs/intro/install/)'yi yüklemeniz gerekir.

Gizli dizileri [depolama CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/) sürücü arabirimi ile, Azure Key Vault Örneğinizde depolanan gizli dizileri alabilir ve sonra gizli Içeriği Kubernetes pods 'ye bağlamak için sürücü arabirimini kullanabilirsiniz.

1. Held sürümünün v3 veya üzeri olduğundan emin olun:
    ```azurecli
    helm version
    ```
1. Sürücü için gizli anahtar deposu CSı sürücüsünü ve Azure Key Vault sağlayıcısını yükler:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Windows düğümlerinde gizli dizi ve Azure Key Vault sağlayıcıyı kullanmayı planlıyorsanız, [Helu yapılandırma değerlerini](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration) kullanarak Windows düğümlerinde sürücüyü ve sağlayıcıyı etkinleştirin

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Azure Anahtar Kasası oluşturma ve sırlarınızı ayarlama

Kendi anahtar kasanızı oluşturmak ve sırlarınızı ayarlamak için [Azure CLI kullanarak Azure Key Vault bir gizli anahtarı ayarlama ve alma](../secrets/quick-create-cli.md)bölümündeki yönergeleri izleyin.

> [!NOTE] 
> Azure Cloud Shell kullanmanız veya yeni bir kaynak grubu oluşturmanız gerekmez. Daha önce Kubernetes kümesi için oluşturduğunuz kaynak grubunu kullanabilirsiniz.

## <a name="create-your-own-secretproviderclass-object"></a>Kendi SecretProviderClass nesneniz oluşturma

Özel bir SecretProviderClass nesneniz olan gizli dizi parametrelerini sağlayıcıya özgü parametrelerle oluşturmak için [Bu şablonu kullanın](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/service-principal/v1alpha1_secretproviderclass_service_principal.yaml). Bu nesne, anahtar kasanıza kimlik erişimi sağlar.

Örnek SecretProviderClass YAML dosyasında eksik parametreleri girin. Aşağıdaki parametreler gereklidir:

* **Keyvaultname**: anahtar kasanızın adı
* **nesneler**: bağlamak istediğiniz tüm gizli içeriklerin listesi
    * **ObjectName**: gizli içeriğin adı
    * **ObjectType**: nesne türü (gizli, anahtar, sertifika)
* **Tenantıd**: anahtar kasaınızın Kiracı kimliği veya dizin kimliği

Tüm gerekli alanların ve Desteklenen yapılandırmaların belgeleri şurada bulunabilir: [bağlantı](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

Güncelleştirilmiş şablon aşağıdaki kodda gösterilmiştir. Bunu bir YAML dosyası olarak indirin ve gerekli alanları girin. Bu örnekte, Anahtar Kasası **contosoKeyVault5**' dir. İki gizli dizi vardır, **secret1** ve **secret2**.

> [!NOTE] 
> Pod kimlikleri kullanıyorsanız, **Usepodidentity** değerini *true* olarak ayarlayın ve **useratandidentityıd** değerini tırnak işareti (**""**) olarak ayarlayın. Yönetilen kimlikler kullanıyorsanız, **Usevmmanagedıdentity** değerini *true* olarak ayarlayın ve **useratandıdentityıd** değerini Kullanıcı tarafından atanan kimliğin ClientID olarak ayarlayın.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
Aşağıdaki görüntüde, **az keykasa Show--Name contosoKeyVault5** for the ilgili vurgulanan meta verilerle konsol çıktısı gösterilmektedir:

!["Az keykasa Show--Name contosoKeyVault5" için konsol çıktısını gösteren ekran görüntüsü](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Azure Active Directory (Azure AD) Pod kimliğini yükler

1. Oluşturduğunuz AKS kümesine belirli roller atayın. 

    Azure Active Directory (Azure AD) Pod kimliği olan tüm gerekli rol atamalarına yönelik belgeler şurada bulunabilir: [bağlantı](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    VAULT_RESOURCE_GROUP=contosoResourceGroup
    NODE_RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$VAULT_RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$NODE_RESOURCE_GROUP
    ```

2. Azure Active Directory (Azure AD) kimliğini AKS 'e yükler.

    > [!NOTE] 
    > Kubernetes kullanan Network eklentisi ile bir AKS kümesi kullanıyorsanız, kümedeki Pod kimliğini nasıl dağıtacağınızı öğrenmek için bu [belgeyi](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) gözden geçirin.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. User-Assigned yönetilen bir kimlik oluşturun. Çıkışta, daha sonra kullanmak üzere **ClientID** 'yi kopyalayın.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Anahtar kasaınızdan gizli dizileri almak için kimlik izinlerini verin. Kullanıcı tarafından atanan yönetilen kimliğin **ClientID** 'sini kullanın.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Ana kasanızdan bağlı gizli dizileri kullanarak Pod 'nizi dağıtın

SecretProviderClass nesneniz yapılandırmak için aşağıdaki komutu çalıştırın:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Yönetilen kimlikleri kullanma

Pod kimlikleri kullanıyorsanız, kümenizde daha önce oluşturduğunuz kimliğe başvuran bir *AzureIdentity* oluşturun. Daha sonra, oluşturduğunuz AzureIdentity 'e başvuran bir *AzureIdentityBinding* oluşturun. Aşağıdaki şablondaki parametreleri doldurun ve sonra da *Podidentityandbinding. YAML* olarak kaydedin.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Bağlamayı yapılandırmak için aşağıdaki komutu çalıştırın:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Sonra, Pod öğesini dağıtırsınız. Aşağıdaki kod, önceki adımdan Pod kimlik bağlamasını kullanan dağıtım YAML dosyasıdır. Bu dosyayı *Pod Bindingdeployment. YAML* olarak kaydedin.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

Pod 'nizi dağıtmak için aşağıdaki komutu çalıştırın:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Pod durum ve gizli içeriğini denetleme 

Dağıttığınız bir pod 'yi göstermek için aşağıdaki komutu çalıştırın:
```azurecli
kubectl get pods
```

Pod uygulamanızın durumunu denetlemek için şu komutu çalıştırın:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Pod 'un "çalışıyor" durumunu görüntüleyen ve tüm olayları "normal" olarak gösteren Azure CLı çıkışının ekran görüntüsü ](../media/kubernetes-key-vault-6.png)

Çıkış penceresinde, dağıtılan Pod, *çalışıyor* durumunda olmalıdır. Alttaki **Olaylar** bölümünde tüm olay türleri *normal* olarak görüntülenir.

Pod 'un çalıştığını doğruladıktan sonra Pod 'un anahtar kasasındaki gizli dizileri içerdiğini doğrulayabilirsiniz.

Pod 'da bulunan tüm gizli dizileri göstermek için aşağıdaki komutu çalıştırın:
```azurecli
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Belirli bir gizli dizi içeriğini göstermek için aşağıdaki komutu çalıştırın:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Gizli dizi içeriğinin görüntülendiğini doğrulayın.

## <a name="resources"></a>Kaynaklar
[Azure Key Vault hakkında](overview.md) 
 [Geliştirici kılavuzu Azure Key Vault](developers-guide.md) 
 [CSI Gizli sürücüsü](https://secrets-store-csi-driver.sigs.k8s.io/introduction.html)


Anahtar kasanızın kurtarılabilir olduğundan emin olmak için, bkz.:
> [!div class="nextstepaction"]
> [Geçici silme özelliğini aç](./key-vault-recovery.md)
