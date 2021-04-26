---
title: Kubectl 'yi kullanarak Kubernetes durum bilgisi olan bir uygulamayı Azure Stack Edge Pro cihazında statik olarak sağlanan paylaşımda dağıtma | Microsoft Docs
description: Bir Azure Stack Edge Pro GPU cihazında kubectl kullanarak statik olarak sağlanan bir paylaşımdan Kubernetes durum bilgisi olan bir uygulama dağıtımının nasıl oluşturulduğunu ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610493"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Kubectl 'yi, Azure Stack Edge Pro cihazınızda bir PersistentVolume ile bir Kubernetes durum bilgisi olan uygulama çalıştırmak için kullanın

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, bir PersistentVolume (BD) ve dağıtım kullanarak Kubernetes 'te tek örnekli bir durum bilgisi olan uygulamanın nasıl dağıtılacağı gösterilir. Dağıtım, `kubectl` var olan bir Kubernetes kümesinde komutlar kullanır ve MySQL uygulamasını dağıtır. 

Bu yordam, [Azure Stack Edge Pro cihazındaki Kubernetes depolamayı](azure-stack-edge-gpu-kubernetes-storage.md) Inceleyen ve [Kubernetes depolama](https://kubernetes.io/docs/concepts/storage/)kavramlarını bildiğiniz kullanıcılara yöneliktir. 

Azure Stack Edge Pro, Azure SQL Edge kapsayıcılarını çalıştırmayı da destekler ve bu, MySQL için burada ayrıntılı şekilde dağıtılabilir. Daha fazla bilgi için bkz. [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Önkoşullar

Durum bilgisi olan uygulamayı dağıtabilmeniz için cihazınızda aşağıdaki önkoşulları ve cihaza erişmek için kullanacağınız istemciyi doldurun:

### <a name="for-device"></a>Cihaz için

- 1 düğümlü Azure Stack Edge Pro cihazı için oturum açma kimlik bilgileriniz vardır.
    - Cihaz etkinleştirilir. Bkz. [cihazı etkinleştirme](azure-stack-edge-gpu-deploy-activate.md).
    - Cihazda Azure portal aracılığıyla yapılandırılmış işlem rolü vardır ve bir Kubernetes kümesi vardır. Bkz. [Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Cihaza erişen istemci için

- Azure Stack Edge Pro cihazına erişmek için kullanılacak bir Windows istemci sisteminiz vardır.
    - İstemci Windows PowerShell 5,0 veya üstünü çalıştırıyor. Windows PowerShell 'in en son sürümünü indirmek için [Windows PowerShell 'ı yükleme](/powershell/scripting/install/installing-windows-powershell)bölümüne gidin.
    
    - [Desteklenen bir işletim sistemine](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) sahip başka bir istemciniz de olabilir. Bu makalede, bir Windows istemcisi kullanılırken yordam açıklanmaktadır. 
    
    - [Azure Stack Edge Pro cihazında Kubernetes kümesine erişme](azure-stack-edge-gpu-create-kubernetes-cluster.md)bölümünde açıklanan yordamı tamamladınız. Şunları yapabilirsiniz:
      - `userns1`Komutu aracılığıyla bir ad alanı oluşturuldu `New-HcsKubernetesNamespace` . 
      - `user1`Komutu aracılığıyla bir Kullanıcı oluşturuldu `New-HcsKubernetesUser` . 
      - `user1`Komutu aracılığıyla öğesine erişim verildi `userns1` `Grant-HcsKubernetesNamespaceAccess` .       
      - `kubectl`İstemcide yüklü ve `kubeconfig` dosyayı Kullanıcı yapılandırmasıyla C: \\ Users Kullanıcı \\ &lt; adı &gt; \\ . kuin olarak kaydetti. 
    
    - `kubectl`İstemci sürümünün, Azure Stack Edge Pro cihazınızda çalışan Kubernetes ana sürümünden birden fazla sürüm olmadığından emin olun. 
        - `kubectl version`İstemci üzerinde çalışan kubectl sürümünü denetlemek için kullanın. Tam sürümü bir yere unutmayın.
        - Azure Stack Edge Pro cihazınızın yerel kullanıcı arabiriminde **Genel Bakış ' a** gidin ve Kubernetes yazılım numarasına göz atın. 
        - Desteklenen Kubernetes sürümünde belirtilen eşlemenin uyumluluğuyla uyumluluk için bu iki sürümü doğrulayın.<!-- insert link--> 


Azure Stack Edge Pro cihazınızda durum bilgisi olan bir uygulamayı dağıtmaya hazırlanıyor. 

## <a name="provision-a-static-pv"></a>Statik BD sağlama

Statik olarak bir BD sağlamak için cihazınızda bir paylaşma oluşturmanız gerekir. SMB paylaşımınızda bir BD sağlamak için bu adımları izleyin. 

> [!NOTE]
> - Bu nasıl yapılır makalesinde kullanılan belirli örnek NFS paylaşımları ile çalışmaz. Genellikle, NFS paylaşımları, veritabanı olmayan uygulamalarla Azure Stack Edge cihazınızda sağlanabilir.
> - Kalıcı depolama sağlamak için depolama birimleri kullanan durum bilgisi olan uygulamaları dağıtmak için kullanmanızı öneririz `StatefulSet` . Bu örnek `Deployment` yalnızca bir çoğaltmayla birlikte kullanır ve geliştirme ve test için uygundur. 

1. Kenar paylaşma veya kenar yerel paylaşma oluşturmak isteyip istemediğinizi seçin. Paylaşma oluşturmak için [paylaşma ekleme](azure-stack-edge-manage-shares.md#add-a-share) bölümündeki yönergeleri izleyin. **Edge COMPUTE ile paylaşma kullan** onay kutusunu seçtiğinizden emin olun.

    ![BD için uç yerel paylaşma](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Yeni bir paylaşımın oluşturulması yerine, var olan bir paylaşımın kullanılmasına karar verirseniz, bu paylaşıma bağlamanız gerekir.
    
        Azure Stack Edge kaynağınız için Azure portal **Paylaşımlar**' a gidin. Mevcut paylaşımlar listesinden, kullanmak istediğiniz bir paylaşımı seçin ve tıklayın.

        ![BD için var olan yerel paylaşma seçin](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. **Bağla** ' yı seçin ve istendiğinde bağlamayı onaylayın.  

        ![BD için var olan yerel paylaşıma bağlama](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Paylaşma adını bir yere getirin. Bu paylaşma oluşturulduğunda, oluşturduğunuz SMB paylaşımıyla ilgili Kubernetes kümesinde otomatik olarak kalıcı bir birim nesnesi oluşturulur. 

## <a name="deploy-mysql"></a>MySQL dağıtma

Artık bir Kubernetes dağıtımı oluşturarak ve onu bir PersistentVolumeClaim (PVC) kullanarak önceki adımda oluşturduğunuz BD 'e bağlayarak durum bilgisi olan bir uygulamayı çalıştıracaksınız. 

`kubectl`Durum bilgisi olan uygulama dağıtımlarını oluşturmak ve yönetmek için kullandığınız tüm komutların yapılandırmayla ilişkili ad alanını belirtmesi gerekir. Bir kubectl komutunda ad alanını belirtmek için kullanın `kubectl <command> -n <your-namespace>` .

1. Ad uzayındaki Kubernetes kümenizde çalışan Pod 'lerin bir listesini alın. Pod, Kubernetes kümenizde çalışan bir uygulama kapsayıcısıdır veya işlemidir.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Komut kullanımı örneği aşağıda verilmiştir:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   Bu çıktı, kümenizde çalışan hiçbir uygulama bulunmadığından hiçbir kaynak (pods) bulunamadığını sağlamalıdır.

1. Aşağıdaki YAML dosyalarını kullanacaksınız. `mysql-deployment.yml`Dosya MySQL çalıştıran ve PVC 'ye başvuran bir dağıtımı açıklar. Dosya, için bir birim bağlaması tanımlar `/var/lib/mysql` ve ardından 20 GB 'lik bir birim gibi görünen BIR PVC oluşturur. 

    Bu talep, önceki adımda paylaşma oluşturduğunuzda statik olarak sağlanan herhangi bir BD tarafından karşılanır. Cihazınızda, her bir paylaşıma yönelik büyük bir BD 32 TB oluşturulur. BD, PVC tarafından ayarlanan gereksinimleri karşılar ve PVC 'nin bu BD 'e bağlanması gerekir.

    Aşağıdaki dosyayı kopyalayın ve `mysql-deployment.yml` Windows istemcisinde Azure Stack Edge Pro cihazına erişmek için kullandığınız bir klasöre kaydedin.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. ' İ kaydettiğiniz klasöre bir dosya olarak kopyalayın ve kaydedin `mysql-pv.yml` `mysql-deployment.yml` . Daha önce oluşturduğunuz SMB payını kullanmak için `kubectl` , `volumeName` PVC nesnesindeki alanı paylaşımın adına ayarlayın. 

    > [!NOTE] 
    > YAML dosyalarının doğru girintide bulunduğundan emin olun. Doğrulamak ve sonra kaydetmek için [YAML Lint](http://www.yamllint.com/) ile kontrol yapabilirsiniz.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Dosyayı dağıtın `mysql-pv.yaml` .

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Dağıtımın örnek bir çıktısı aşağıda verilmiştir.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Oluşturulan PVC 'nin adını aklınızda yapın. Daha sonraki bir adımda kullanacaksınız. 

4. Dosyanın içeriğini dağıtın `mysql-deployment.yml` .

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Dağıtımın örnek bir çıktısı aşağıda verilmiştir.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Dağıtım hakkındaki bilgileri görüntüleyin.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Dağıtım tarafından oluşturulan Pod 'leri listeleyin.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Örnek bir çıktı aşağıda verilmiştir.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. PersistentVolumeClaim ' i inceleyin.

    `kubectl describe pvc <your-pvc-name>`

    Örnek bir çıktı aşağıda verilmiştir.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>MySQL 'in çalıştığını doğrula


MySQL çalıştıran bir pod içindeki bir kapsayıcıya karşı bir komut çalıştırmak için şunu yazın:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Örnek bir çıktı aşağıda verilmiştir.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Bir dağıtımı silme

Dağıtımı silmek için dağıtılan nesneleri ada göre silin. Bu nesneler dağıtım, hizmet ve PVC 'yi içerir.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Dağıtım ve hizmeti sildiğinizde bunun örnek çıktısı aşağıda verilmiştir.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Burada, PVC 'yi sildiğinizde oluşan örnek çıktı verilmiştir.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

PVC silindiği için BD artık PVC 'ye bağlanmadı. Bu, paylaşımın oluşturulduğu sırada sağlandığı için, bu paylaşımın silinmesi gerekir. Şu adımları izleyin:

1. Paylaşımdan çıkarın. Azure portal, **Azure Stack Edge kaynak > paylaşımlarına** gidin ve çıkarmak istediğiniz paylaşımı seçin ve tıklayın. Çıkar **' ı** seçin ve işlemi onaylayın. Paylaşımın çıkarılana kadar bekleyin. Takma kaldırma, Kubernetes kümesinden paylaşımın (ve dolayısıyla ilişkili PersistentVolume) payını yayınlar. 

    ![BD için yerel paylaşımın bağlantısını kaldır](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Şimdi, hesabınızı silmek için **Sil** ' i ve Silmeyi Onayla ' yı seçebilirsiniz. Bu, Ayrıca, paylaşma ve karşılık gelen BD öğesini de silmelidir.

    ![BD için yerel paylaşma silme](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Sonraki adımlar

Depolama alanını dinamik olarak sağlamayı anlamak için, bkz. [Azure Stack Edge Pro cihazında dinamik sağlama aracılığıyla durum bilgisi olan bir uygulamayı dağıtma](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)
