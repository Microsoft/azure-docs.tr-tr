---
title: Azure Stack Edge Pro GPU cihazında Kubernetes 'te Azure yayı 'yi etkinleştirme | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazındaki mevcut bir Kubernetes kümesinde Azure Arc 'ın nasıl etkinleştirileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 1d42843805f4fce24368dd07de3a73fec2545957
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567534"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazındaki Kubernetes kümesinde Azure yayı 'yi etkinleştirme

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Azure Arc 'ın Azure Stack Edge Pro cihazınızdaki mevcut bir Kubernetes kümesinde nasıl etkinleştirileceği gösterilmektedir. 

Bu yordam, [Azure Stack Edge Pro cihazındaki Kubernetes iş yüklerini](azure-stack-edge-gpu-kubernetes-workload-management.md) Inceleyen ve [Azure Arc etkinleştirilmiş Kubernetes (Önizleme)](../azure-arc/kubernetes/overview.md)ile ilgili kavramlara yönelik kavramlar hakkında bilgi sahibi olmak için tasarlanmıştır.


## <a name="prerequisites"></a>Önkoşullar

Kubernetes kümesinde Azure yayı etkinleştirebilmeniz için, Azure Stack Edge Pro cihazınızda ve cihaza erişmek için kullanacağınız istemcide aşağıdaki önkoşulları tamamladığınızdan emin olun:

### <a name="for-device"></a>Cihaz için

1. 1 düğümlü Azure Stack Edge Pro cihazı için oturum açma kimlik bilgileriniz vardır.
    1. Cihaz etkinleştirilir. Bkz. [cihazı etkinleştirme](azure-stack-edge-gpu-deploy-activate.md).
    1. Cihazda Azure portal aracılığıyla yapılandırılmış işlem rolü vardır ve bir Kubernetes kümesi vardır. Bkz. [Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Aboneliğe erişim sahibi oldunuz. Hizmet sorumlunuz için rol atama adımı sırasında bu erişime ihtiyacınız olacaktır.
 

### <a name="for-client-accessing-the-device"></a>Cihaza erişen istemci için

1. Azure Stack Edge Pro cihazına erişmek için kullanılacak bir Windows istemci sisteminiz vardır.
  
    - İstemci Windows PowerShell 5,0 veya üstünü çalıştırıyor. Windows PowerShell 'in en son sürümünü indirmek için [Windows PowerShell 'ı yükleme](/powershell/scripting/install/installing-powershell-core-on-windows)bölümüne gidin.
    
    - [Desteklenen bir işletim sistemine](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) sahip başka bir istemciniz de olabilir. Bu makalede, bir Windows istemcisi kullanılırken yordam açıklanmaktadır. 
    
1. [Azure Stack Edge Pro cihazında Kubernetes kümesine erişme](azure-stack-edge-gpu-create-kubernetes-cluster.md)bölümünde açıklanan yordamı tamamladınız. Şunları yapabilirsiniz:
    
    - `kubectl`İstemciye yüklendi.    
    - `kubectl`İstemci sürümünün, Azure Stack Edge Pro cihazınızda çalışan Kubernetes ana sürümünden birden fazla sürüm olmadığından emin olun. 
      - `kubectl version`İstemci üzerinde çalışan kubectl sürümünü denetlemek için kullanın. Tam sürümü bir yere unutmayın.
      - Azure Stack Edge Pro cihazınızın yerel kullanıcı arabiriminde, **yazılım güncelleştirmesi** ' ne gidin ve Kubernetes sunucu sürüm numarasını aklınızda edin. 
    
        ![Kubernetes sunucu sürüm numarasını doğrula](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Bu iki sürümün uyumlu olduğunu doğrulayın. 


## <a name="register-kubernetes-resource-providers"></a>Kubernetes kaynak sağlayıcılarını Kaydet

Kubernetes kümesinde Azure Arc 'ı etkinleştirmeden önce, aboneliğinizi etkinleştirmeniz ve kaydetmeniz gerekir `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` . 

1. Bir kaynak sağlayıcısını etkinleştirmek için, Azure portal dağıtım için kullanmayı planladığınız aboneliğe gidin. **Kaynak sağlayıcıları**' na gidin. 
1. Sağ bölmede, eklemek istediğiniz sağlayıcıları arayın. Bu örnekte, `Microsoft.Kubernetes` ve `Microsoft.KubernetesConfiguration` .

    ![Kubernetes kaynak sağlayıcılarını Kaydet](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Bir kaynak sağlayıcı seçin ve komut çubuğunun üstünden **Kaydet**' i seçin. Kayıt birkaç dakika sürer. 

    ![Kubernetes kaynak sağlayıcılarını kaydet 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Kaynak sağlayıcının kayıtlı olduğunu görene kadar Kullanıcı arabirimini yenileyin. Her iki kaynak sağlayıcısı için işlemi yineleyin.
    
    ![Kubernetes kaynak sağlayıcılarını Kaydet 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Ayrıca, kaynak sağlayıcılarını ile de kaydedebilirsiniz `az cli` . Daha fazla bilgi için bkz. [Azure Arc etkin Kubernetes için iki sağlayıcıyı kaydetme](../azure-arc/kubernetes/quickstart-connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Hizmet sorumlusu oluşturma, rol atama

1. `Subscription ID`Azure Stack Edge hizmetiniz için kaynak dağıtımı için kullandığınız kaynak grubunun ve adınızın bulunduğundan emin olun. Abonelik KIMLIĞINI almak için Azure portal Azure Stack Edge kaynağına gidin. **Genel bakış > Essentials**'a gidin.

    ![Abonelik KIMLIĞINI al](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Kaynak grubu adını almak için, **Özellikler**' e gidin.

    ![Kaynak grubu adını al](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Hizmet sorumlusu oluşturmak için, aracılığıyla aşağıdaki komutu kullanın `az cli` .

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    ' De oturum açma hakkında bilgi için `az cli` [Azure Portal Cloud Shell başlatın](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    Aşağıda bir örnek verilmiştir. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Bunu,,, `appID` ve öğesini `name` bir `password` `tenantID` sonraki komutta giriş olarak kullanacaksınız gibi bir yere getirin.

1. Yeni hizmet sorumlusunu oluşturduktan sonra, `Kubernetes Cluster - Azure Arc Onboarding` rolü yeni oluşturulan sorumluya atayın. Bu, sınırlı izinlerle yerleşik bir Azure rolüdür (komutta rol KIMLIĞINI kullanın). Aşağıdaki komutu kullanın:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Aşağıda bir örnek verilmiştir.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Hizmet sorumlusu oluşturma ve rol atamasını gerçekleştirme hakkında daha fazla bilgi için, [Azure Arc özellikli ekleme hizmeti sorumlusu oluşturma](../azure-arc/kubernetes/create-onboarding-service-principal.md)içindeki adımlara bakın.


## <a name="enable-arc-on-kubernetes-cluster"></a>Kubernetes kümesinde Arc'ı etkinleştirme

Azure Arc yönetimi için Kubernetes kümesini yapılandırmak için aşağıdaki adımları izleyin:

1. Cihazınızın [PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) .

1. Şunu yazın:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Cihazınızda Azure yayı dağıtmak için [Azure Arc Için desteklenen bir bölge](../azure-arc/kubernetes/overview.md#supported-regions)kullandığınızdan emin olun. 
    > - `az account list-locations`Cmdlet 'te geçirilecek tam konum adını bulmak için komutunu kullanın `Set-HcsKubernetesAzureArcAgent` . Konum adları genellikle boşluk olmadan biçimlendirilir.
    
    Aşağıda bir örnek verilmiştir:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed&quot; -ResourceGroupName &quot;myaserg1&quot; -ResourceName &quot;myasetestresarc&quot; -Location &quot;westeurope&quot; -TenantId &quot;72f988bf-86f1-41af-91ab-2d7cd011db47&quot; -ClientId &quot;aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b&quot; -ClientSecret &quot;<password>"
        [10.128.44.240]: PS>
    ```
    
    Azure portal, önceki komutta verdiğiniz adla bir kaynak oluşturulmalıdır.

    ![Azure yay kaynağına git](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Azure Arc 'ın başarıyla etkinleştirildiğini doğrulamak için PowerShell arabiriminden aşağıdaki komutu çalıştırın:

    `kubectl get deployments -n azure-arc`

    Bu komut, `azure-arc` Azure yaya karşılık gelen ad alanına dağıtılan tüm uygulamaları bulur.

    Ad alanındaki Kubernetes kümenize dağıtılan Azure Arc aracılarını gösteren örnek bir çıktı aşağıda verilmiştir `azure-arc` . 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    Ad alanındaki Kubernetes kümenizde çalışan Pod 'lerin bir listesini de alabilirsiniz `azure-arc` . Pod, Kubernetes kümenizde çalışan bir uygulama kapsayıcısıdır veya işlemidir. 

    Aşağıdaki komutu kullanın:
    
    `kubectl get pods -n azure-arc`
    
    Örnek bir çıktı aşağıda verilmiştir.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Yukarıdaki Çıktıda gösterildiği gibi, Azure Arc etkin Kubernetes, kümenizde çalışan ve ad alanına dağıtılan birkaç aracıdan (işleçlerden) oluşur `azure-arc` .

- `config-agent`: kümede ve güncelleştirmelerin uyumluluk durumunda uygulanan kaynak denetimi yapılandırma kaynakları için bağlı kümeyi izler
- `controller-manager`: işleçler operatörü ve Azure Arc bileşenleri arasındaki etkileşimleri düzenler
- `metrics-agent`: Bu aracıların en iyi performansı sergilediğinden emin olmak için diğer yay aracılarının ölçümlerini toplar
- `cluster-metadata-operator`: küme meta verilerini toplar-küme sürümü, düğüm sayısı ve Azure Arc aracı sürümü
- `resource-sync-agent`: Yukarıdaki belirtilen küme meta verilerini Azure 'da eşitler
- `clusteridentityoperator`: Azure Arc etkin Kubernetes Şu anda sistem tarafından atanan kimliği desteklemektedir. clusterıdentityoperator, diğer aracıların Azure ile iletişim kurmak için kullandığı yönetilen hizmet kimliği (MSI) sertifikasını saklar.
- `flux-logs-agent`: kaynak denetimi yapılandırmasının bir parçası olarak dağıtılan Flox işleçlerinden günlükleri toplar.
- `connect-agent`: Azure Arc kaynağına konuşur.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Kubernetes kümesinden yayı kaldır

Azure Arc yönetimini kaldırmak için şu adımları izleyin:

1. 1. Cihazınızın [PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) .
2. Şunu yazın:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Varsayılan olarak, `yamls` Git deposundan kaynak silindiğinde, ilgili kaynaklar Kubernetes kümesinden silinmez. `--sync-garbage-collection`Git deposundan silindiği zaman kaynakların silinmesine izin vermek Için Arc OperatorParams ' de ayarlamanız gerekir. Daha fazla bilgi için bkz. [yapılandırmayı silme](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)

## <a name="next-steps"></a>Sonraki adımlar

Azure Arc dağıtımının nasıl çalıştırılacağını anlamak için, bkz. [ `Guestbook` Azure Stack Edge Pro cihazında Gile aracılığıyla durum bilgisiz olmayan bir php uygulaması dağıtma](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)