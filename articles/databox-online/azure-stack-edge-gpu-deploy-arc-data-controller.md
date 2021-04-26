---
title: Azure Stack Edge Pro GPU cihazınıza Azure Arc veri denetleyicisi dağıtma | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazınızda Azure Arc veri denetleyicisi ve Azure veri hizmetlerinin nasıl dağıtılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: d56e03cd650032a775c30b02d939cf934f384fae
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568616"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazınızda Azure veri Hizmetleri 'ni dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Azure Arc veri denetleyicisi oluşturma ve ardından Azure veri Hizmetleri 'ni Azure Stack Edge Pro GPU cihazınızda dağıtma işlemi açıklanmaktadır. 

Azure Arc veri denetleyicisi, müşteri tarafından yönetilen ortamlarda Azure veri Hizmetleri 'ni sağlayan yerel denetim düzledir. Azure Stack Edge Pro GPU cihazınızda çalışan Kubernetes kümesinde Azure Arc veri denetleyicisi oluşturduktan sonra, bu veri denetleyicisinde SQL yönetilen örneği (Önizleme) gibi Azure veri hizmetlerini dağıtabilirsiniz.

Veri denetleyicisi oluşturma ve ardından bir SQL yönetilen örneği dağıtma yordamı, `kubectl` cihazdaki Kubernetes kümesine komut satırı erişimi sağlayan PowerShell ve yerel bir araç kullanımını içerir.


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Azure Stack Edge Pro GPU cihazına eriştiniz ve cihazınızı [etkinleştirme Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)' da anlatıldığı şekilde etkinleştirdiniz.

1. Cihazda işlem rolünü etkinleştirdiniz. Cihazda, [Azure Stack Edge Pro GPU cihazınızda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md)konusundaki yönergelere göre bir Kubernetes kümesi de oluşturulmuştur.

1. Yerel Web Kullanıcı arabiriminizdeki **cihaz** sayfasında Kubernetes API uç noktası vardır. Daha fazla bilgi için bkz. [Kubernetes API uç noktası](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)ile ilgili yönergeler.

1. Cihazınıza bağlanacak bir istemciye erişirsiniz. 
    1. Bu makalede, cihaza erişmek için PowerShell 5,0 veya üstünü çalıştıran bir Windows istemci sistemi kullanılır. [Desteklenen bir işletim sistemiyle](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)başka herhangi bir istemciyi kullanabilirsiniz. 
    1. `kubectl`İstemcinizi yapın. İstemci sürümü için:
        1. Cihazda yüklü Kubernetes sunucu sürümünü belirler. Cihazın yerel kullanıcı arabiriminde, **yazılım güncelleştirmeleri** sayfasına gidin. Bu sayfadaki **Kubernetes sunucu sürümüne** göz önünde edin.
        1. Ana sürümden en fazla bir alt sürüm uzakta olan bir istemci indirin. İstemci sürümü, ancak ana öğeyi bir alt sürüme kadar gösterebilir. Örneğin, bir v 1.3 yöneticisinin v 1.1, v 1.2 ve v 1.3 düğümleri ile çalışması ve v 1.2, v 1.3 ve v 1.4 istemcileri ile çalışması gerekir. Kubernetes istemci sürümü hakkında daha fazla bilgi için bkz. [Kubernetes sürümü ve sürüm eğriltme destek ilkesi](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. İsteğe bağlı olarak, [Azure Arc etkin veri hizmetlerini dağıtmak ve yönetmek için istemci araçları 'Nı yükler](../azure-arc/data/install-client-tools.md). Bu araçlar gerekli değildir ancak önerilir.  
1. Cihazınızda bir veri denetleyicisi ve bir SQL yönetilen örneği sağlamak için yeterli kaynak bulunduğundan emin olun. Veri denetleyicisi ve bir SQL yönetilen örneği için en az 16 GB RAM ve 4 CPU çekirdeği gerekir. Ayrıntılı kılavuz için, [Azure Arc etkin veri Hizmetleri dağıtımı Için en düşük gereksinimlere](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)gidin.


## <a name="configure-kubernetes-external-service-ips"></a>Kubernetes dış hizmet IP 'lerini yapılandırma

1. Cihazın yerel Web Kullanıcı arabirimine gidip **işlem**' a gidin.
1. İşlem için etkinleştirilen ağı seçin. 

    ![Yerel Kullanıcı arabirimi 2 ' de işlem sayfası](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Üç ek Kubernetes dış hizmet IP 'si sağladığınızdan emin olun (diğer dış hizmetler veya kapsayıcılar için zaten yapılandırdığınız IP 'lere ek olarak). Veri denetleyicisi iki hizmet IP 'si kullanır ve bir SQL yönetilen örneği oluşturduğunuzda üçüncü IP kullanılır. Dağıtacaksınız her ek veri hizmeti için bir IP gerekir. 

    ![Yerel Kullanıcı arabirimi 3 ' te işlem sayfası](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Ayarları Uygula ve bu yeni IP 'Ler, zaten var olan bir Kubernetes kümesi üzerinde devreye girer. 


## <a name="deploy-azure-arc-data-controller"></a>Azure Arc Data Controller 'ı dağıtma

Bir veri denetleyicisi dağıtmadan önce bir ad alanı oluşturmanız gerekir.

### <a name="create-namespace"></a>Ad alanı oluşturma 

Veri denetleyicisini dağıtacağınız yeni, ayrılmış bir ad alanı oluşturun. Ayrıca, bir kullanıcı oluşturup sonra da oluşturduğunuz ad alanına kullanıcı erişimi verirsiniz. 

> [!NOTE]
> Hem ad alanı hem de Kullanıcı adları için [DNS alt etki alanı adlandırma kuralları](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) geçerlidir.

1. [PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Ad alanı oluşturun. Şunu yazın:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Bir kullanıcı oluşturun. Şunu yazın: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Bir yapılandırma dosyası düz metin olarak görüntülenir. Bu dosyayı kopyalayın ve bir *yapılandırma* dosyası olarak kaydedin. 

    > [!IMPORTANT]
    > Yapılandırma dosyasını *. txt* dosyası olarak kaydetmeyin, dosyayı herhangi bir dosya uzantısı olmadan kaydedin.

1. Yapılandırma dosyası `.kube` yerel makinedeki Kullanıcı profilinizin klasöründe bulunmalıdır. Dosyayı Kullanıcı profilinizde bu klasöre kopyalayın.

    ![İstemcideki yapılandırma dosyası konumu](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Kullanıcı tarafından oluşturduğunuz ad alanına erişim izni verin. Şunu yazın: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Yukarıdaki komutların örnek çıktısı aşağıda verilmiştir. Bu örnekte, bir `myadstest` Kullanıcı ad alanı oluşturur `myadsuser` ve Kullanıcı adı alanına erişim izni verilir.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Sisteminizdeki Hosts dosyasına bir DNS girişi ekleyin. 

    1. Not defteri 'Ni yönetici olarak çalıştırın ve `hosts` konumunda bulunan dosyayı açın `C:\windows\system32\drivers\etc\hosts` . 
    2. Ana bilgisayar dosyasında girişi oluşturmak için yerel kullanıcı arabirimindeki (Önkoşul) **cihaz** sayfasından kaydettiğiniz bilgileri kullanın. 

        Örneğin, bu uç noktayı, `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` CIHAZ IP adresi ve DNS etki alanı ile aşağıdaki girdiyi oluşturmak için kopyalayın: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Kubernetes pods 'ye bağlanabildiğinizi doğrulamak için bir komut istemi veya PowerShell oturumu başlatın. Şunu yazın:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Artık veri denetleyicinizi ve veri Hizmetleri uygulamalarınızı ad alanına dağıtabilir, ardından uygulamaları ve bunların günlüklerini görüntüleyebilirsiniz.

### <a name="create-data-controller"></a>Veri denetleyicisi oluşturma

Veri denetleyicisi, bir API, denetleyici hizmeti, önyükleyici ve izleme veritabanları ile panolar sağlamak için Kubernetes kümenize dağıtılan bir pod koleksiyonudur. Daha önce oluşturduğunuz ad alanındaki Azure Stack Edge cihazınızda bulunan Kubernetes kümesinde bulunan bir veri denetleyicisi oluşturmak için bu adımları izleyin.   

1. Bir veri denetleyicisi oluşturmak için ihtiyacınız olacak aşağıdaki bilgileri toplayın:

    
    |Sütun1  |Sütun2  |
    |---------|---------|
    |Veri denetleyicisi adı     |Veri denetleyiciniz için açıklayıcı bir ad. Örneğin, `arctestdatacontroller`.         |
    |Veri denetleyicisi Kullanıcı adı     |Veri denetleyicisi Yönetici kullanıcısı için herhangi bir Kullanıcı adı. Veri denetleyicisi Kullanıcı adı ve parolası, yönetim işlevleri gerçekleştirmek için veri denetleyicisi API 'sine kimlik doğrulaması yapmak için kullanılır.          |
    |Veri denetleyicisi parolası     |Veri denetleyicisi Yönetici kullanıcısı için bir parola. Güvenli bir parola seçin ve yalnızca küme yöneticisi ayrıcalıklarına sahip olmaları gereken kişilerle paylaşabilirsiniz.         |
    |Kubernetes ad alanı adı     |İçinde veri denetleyicisi oluşturmak istediğiniz Kubernetes ad alanının adı.         |
    |Azure abonelik KIMLIĞI     |Azure 'da veri denetleyicisi kaynağının oluşturulmasını istediğiniz Azure abonelik GUID 'SI.         |
    |Azure kaynak grubu adı     |Azure 'da veri denetleyicisi kaynağının oluşturulmasını istediğiniz kaynak grubunun adı.         |
    |Azure konumu     |Veri denetleyicisi kaynak meta verilerinin Azure 'da depolanacağı Azure konumu. Kullanılabilir bölgelerin listesi için bkz. bölgeye göre Azure genel altyapısı/ürünleri.|


1. PowerShell arabirimine bağlanın. Veri denetleyicisi oluşturmak için şunu yazın: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Yukarıdaki komutların örnek çıktısı aşağıda verilmiştir.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Dağıtımın tamamlanması yaklaşık 5 dakika sürebilir.

    > [!NOTE]
    > Azure Stack Edge Pro GPU cihazınızdaki Kubernetes kümesinde oluşturulan veri denetleyicisi yalnızca geçerli yayındaki bağlantısı kesik modda kullanılabilir. Bağlantısı kesik modu, cihazınız için değil, veri denetleyicisi içindir.

### <a name="monitor-data-creation-status"></a>İzleme verileri oluşturma durumu

1. Başka bir PowerShell penceresi açın.
1. `kubectl`Veri denetleyicisinin oluşturma durumunu izlemek için aşağıdaki komutu kullanın. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Denetleyici oluşturulduğunda durum olmalıdır `Ready` .
    Yukarıdaki komutun örnek bir çıktısı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Veri denetleyicisinde çalışan dış hizmetlere atanan IP 'Leri tanımlamak için `kubectl get svc -n <namespace>` komutunu kullanın. Örnek çıktı aşağıdaki gibidir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>SQL yönetilen örneği dağıt

Veri denetleyicisini başarıyla oluşturduktan sonra, veri denetleyicisinde bir SQL yönetilen örneği dağıtmak için bir şablon kullanabilirsiniz.

### <a name="deployment-template"></a>Dağıtım şablonu

Cihazınızdaki veri denetleyicisinde bir SQL yönetilen örneği dağıtmak için aşağıdaki dağıtım şablonunu kullanın.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Meta veri adı
    
Meta veri adı, SQL yönetilen örneğinin adıdır. Yukarıdaki ilişkili Pod `deployment.yaml` adı, `sqlex-n` ( `n` uygulamayla ilişkili olan Pod sayısı) olarak ad olacaktır. 
    
#### <a name="password-and-username-data"></a>Parola ve Kullanıcı adı verileri

Veri denetleyicisi Kullanıcı adı ve parolası, yönetim işlevleri gerçekleştirmek için veri denetleyicisi API 'sine kimlik doğrulaması yapmak için kullanılır. Veri denetleyicisi Kullanıcı adı ve dağıtım şablonundaki parola için Kubernetes gizli dizisi, Base64 kodlamalı dizelerdir. 

İstediğiniz kullanıcı adınızı ve parolanızı Base64 olarak kodlamak için çevrimiçi bir araç kullanabilir veya platformunuza bağlı olarak yerleşik CLı araçlarını kullanabilirsiniz. Çevrimiçi bir Base64 kodlama aracı kullanırken, araçta Kullanıcı adı ve parola dizelerini (veri denetleyicisi oluştururken girdiğiniz) belirtin ve araç karşılık gelen Base64 kodlamalı dizeleri oluşturur.
    
#### <a name="service-type"></a>Hizmet türü

Hizmet türü olarak ayarlanmalıdır `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Depolama sınıfı adı

Azure Stack Edge cihazınızda, dağıtımın veri, yedeklemeler, veri günlükleri ve Günlükler için kullanacağı depolama sınıfını belirleyebilirsiniz. `kubectl get storageclass`Cihazınızda dağıtılan depolama sınıfını almak için komutunu kullanın.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Yukarıdaki örnek çıktıda, cihazınızdaki depolama sınıfı `ase-node-local` şablonda belirtilmelidir.
 
#### <a name="spec"></a>Değiştirilsin

Azure Stack Edge cihazınızda bir SQL yönetilen örneği oluşturmak için, ' ın spec bölümünde bellek ve CPU gereksinimlerinizi belirtebilirsiniz `deployment.yaml` . Her SQL yönetilen örneği, aşağıdaki örnekte gösterildiği gibi en az 2 GB bellek ve 1 CPU çekirdeği istemelidir. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Veri denetleyicisi ve 1 SQL yönetilen örneği için ayrıntılı boyutlandırma Kılavuzu için, [SQL yönetilen örnek boyutlandırma ayrıntılarını](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)gözden geçirin.

### <a name="run-deployment-template"></a>Dağıtım şablonunu Çalıştır

`deployment.yaml`Aşağıdaki komutu kullanarak çalıştırın:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Aşağıdaki komutun örnek bir çıktısı aşağıda verilmiştir:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

`sqlex-0`Örnek çıktıda Pod, SQL yönetilen örneğinin durumunu gösterir.

## <a name="remove-data-controller"></a>Veri denetleyicisini kaldır

Veri denetleyicisini kaldırmak için, dağıttığınız ayrılmış ad alanını silin.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'unuzda durum bilgisiz bir uygulama dağıtın](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
