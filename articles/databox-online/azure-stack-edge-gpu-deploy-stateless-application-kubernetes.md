---
title: Kubernetes durum bilgisiz uygulamasını Azure Stack Edge Pro GPU cihazında kubectl kullanarak dağıtma | Microsoft Docs
description: Microsoft Azure Stack Edge Pro cihazında kubectl kullanarak bir Kubernetes durum bilgisiz uygulama dağıtımını oluşturmayı ve yönetmeyi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638664"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazınızdan kubectl aracılığıyla bir Kubernetes durum bilgisiz uygulaması dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, var olan bir Kubernetes kümesinde kubectl komutları kullanılarak durum bilgisiz bir uygulamanın nasıl dağıtılacağı açıklanır. Bu makale ayrıca durum bilgisiz uygulamanızda Pod oluşturma ve ayarlama sürecinde size yol gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bir Kubernetes kümesi oluşturabilmeniz ve `kubectl` komut satırı aracını kullanabilmeniz için önce aşağıdakileri yapmanız gerekir:

- 1 düğümlü Azure Stack Edge Pro cihazı için oturum açma kimlik bilgileriniz vardır.

- Windows PowerShell 5,0 veya üzeri bir Windows istemci sisteminde Azure Stack Edge Pro cihazına erişmek için yüklenir. Desteklenen bir işletim sistemine sahip başka bir istemciniz de olabilir. Bu makalede, bir Windows istemcisi kullanılırken yordam açıklanmaktadır. Windows PowerShell 'in en son sürümünü indirmek için [Windows PowerShell 'ı yükleme](/powershell/scripting/install/installing-windows-powershell)bölümüne gidin.

- Azure Stack Edge Pro cihazında işlem etkindir. İşlem ' ı etkinleştirmek için, cihazın yerel kullanıcı arabirimindeki **işlem** sayfasına gidin. Sonra, işlem için etkinleştirmek istediğiniz bir ağ arabirimi seçin. **Etkinleştir**’i seçin. İşlem, bu ağ arabirimindeki cihazınızda sanal anahtar oluşturulmasına neden olur. Daha fazla bilgi için bkz. [Azure Stack Edge Pro 'da işlem ağını etkinleştirme](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- Azure Stack Edge Pro cihazınız, sürüm v 1.9 veya üzeri olan bir Kubernetes kümesi sunucusuna sahiptir. Daha fazla bilgi için [Microsoft Azure Stack Edge Pro cihazında bir Kubernetes kümesi oluşturma ve yönetme](azure-stack-edge-gpu-create-kubernetes-cluster.md)konusuna bakın.

- Yüklediniz `kubectl` .

## <a name="deploy-a-stateless-application"></a>Durum bilgisi olmayan bir uygulama dağıtma

Başlamadan önce şunları yapmalısınız:

1. Bir Kubernetes kümesi oluşturuldu.
2. Bir ad alanı ayarlayın.
3. Bir kullanıcının ad alanıyla ilişkilendirilmesi.
4. Kullanıcı Yapılandırması ' ye kaydedildi `C:\Users\<username>\.kube` .
5. Yüklendi `kubectl` .

Artık Azure Stack Edge Pro cihazında durum bilgisiz uygulama dağıtımlarını çalıştırmaya ve yönetmeye başlayabilirsiniz. Kullanmaya başlamadan önce `kubectl` , doğru sürümüne sahip olduğunuzu doğrulamanız gerekir `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Doğru bir kubectl sürümüne sahip olduğunuzu ve yapılandırmayı ayarlabildiğinizi doğrulayın

Sürümünü denetlemek için `kubectl` :

1. Sürümünün `kubectl` 1,9 daha büyük veya ona eşit olduğunu doğrulayın:

   ```powershell
   kubectl version
   ```
    
   Çıktının bir örneği aşağıda gösterilmiştir:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   Bu durumda, kubectl 'nin istemci sürümü v 1.15.2 ve devam etmek için uyumludur.

2. Kubernetes kümenizde çalışan Pod 'lerin bir listesini alın. Pod, Kubernetes kümenizde çalışan bir uygulama kapsayıcısıdır veya işlemidir.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Komut kullanımı örneği aşağıda gösterilmiştir:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   Bu çıktı, kümenizde çalışan hiçbir uygulama bulunmadığından hiçbir kaynak (pods) bulunamadığını sağlamalıdır.

   Komut, "C:\Users \\ &lt; Kullanıcı adı &gt; \\ . KUIN dizin yapısını \" yapılandırma dosyalarıyla dolduracaktır. Kubectl komut satırı aracı, Kubernetes kümenizde durum bilgisiz uygulamalar oluşturmak ve yönetmek için bu dosyaları kullanır.  

3. "C:\Users \\ &lt; kullanıcıadı &gt; \\ . kuin" adlı dizin yapısını, \" *kubectl* 'nin aşağıdaki alt klasörlerle doldurmadığını doğrulamak için el ile denetleyin:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Tüm kubectl komutlarının bir listesini görüntülemek için, yazın `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Dağıtım kullanarak durum bilgisi olmayan bir uygulama oluşturma

Artık, kubectl komut satırı sürümünün doğru olduğunu ve gerekli yapılandırma dosyalarına sahip olduğunu doğruladığınıza göre, durum bilgisi olmayan bir uygulama dağıtımı oluşturabilirsiniz.

Pod, bir Kubernetes uygulamasının temel yürütme birimidir, oluşturduğunuz veya dağıttığınız Kubernetes nesne modelindeki en küçük ve en basit birimdir. Pod, depolama kaynaklarını, benzersiz bir ağ IP 'sini ve kapsayıcının nasıl çalışacağını belirleyen seçenekleri de kapsar.

Oluşturduğunuz durum bilgisiz uygulamanın türü bir NGINX web sunucusu dağıtımdır.

Durum bilgisiz uygulama dağıtımlarını oluşturmak ve yönetmek için kullandığınız tüm kubectl komutlarının yapılandırmayla ilişkili ad alanını belirtmesi gerekir. [Microsoft Azure Stack Edge Pro cihaz öğreticisindeki bir Kubernetes kümesi oluşturma ve yönetme konusunda](azure-stack-edge-gpu-create-kubernetes-cluster.md) Azure Stack Edge Pro cihazında kümeye bağlıyken ad alanını oluşturdunuz `New-HcsKubernetesNamespace` .

Bir kubectl komutunda ad alanını belirtmek için kullanın `kubectl <command> -n <namespace-string>` .

NGINX dağıtımı oluşturmak için aşağıdaki adımları izleyin:

1. Bir Kubernetes dağıtım nesnesi oluşturarak durum bilgisiz bir uygulama uygulayın:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   Bu örnekte, Application YAML dosyasının yolu bir dış kaynaktır.

   Komutun ve çıktısının örnek bir kullanımı aşağıda verilmiştir:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Alternatif olarak, aşağıdaki markı 'yi yerel makinenize kaydedebilir ve *-f* parametresindeki yolu ve dosya adını değiştirebilirsiniz. Örneğin, "C:\kubernetes\deploymentnyaml". Uygulama dağıtımı için yapılandırma şöyle olacaktır:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Bu komut, uygulamanızı çalıştırmak için iki dizin içeren bir varsayılan NGINX dağıtımı oluşturur.

2. Oluşturduğunuz Kubernetes Nginx-Deployment öğesinin açıklamasını alın:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Komutun, Output ile örnek bir kullanımı aşağıda gösterilmiştir:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   *Çoğaltmalar* ayarı için şunu görürsünüz:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   *Çoğaltmalar* ayarı, dağıtım belirtimin iki pod gerektirdiğini ve bu yığınların oluşturulup güncelleştirildiğini ve kullanıma hazırlandığını gösterir.

   > [!NOTE]
   > Bir çoğaltma kümesi, cihaz düğümü arızası veya kesintiye uğratan bir cihaz yükseltmesi gibi her nedenden dolayı silinen veya sonlandırılan Pod 'nin yerini alır. Bu nedenle, uygulamanız yalnızca tek bir pod gerektirdiğinden bile bir çoğaltma kümesi kullanmanızı öneririz.

3. Dağıtımınızdaki Pod 'leri listelemek için:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Komutun, Output ile örnek bir kullanımı aşağıda gösterilmiştir:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   Çıktı, kubectl kullanarak başvurduğumuz benzersiz adlara sahip olduğumuz iki katId olduğunu doğrular.

4. Dağıtımınızda bireysel bir pod hakkındaki bilgileri görüntülemek için:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Komutun, Output ile örnek bir kullanımı aşağıda gösterilmiştir:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Çoğaltma sayısını artırarak uygulama dağıtımını yeniden ölçekler

Her pod, belirli bir uygulamanın tek bir örneğini çalıştırmak için tasarlanmıştır. Uygulamanızı birden çok örnek çalıştırmak için yatay olarak ölçeklendirmek istiyorsanız, her bir örnek için, her bir örnek için bir tane olarak Kubernetes 'te bu, çoğaltma olarak adlandırılır.
Yeni bir YAML dosyası uygulayarak uygulama dağıtımınızdaki dizin sayısını artırabilirsiniz. YAML dosyası çoğaltmalar ayarını 4 olarak değiştirir, bu da dağıtımınızdaki yığınların sayısını dört adet sayıda artırır. 2 ile 4 arasında Pod sayısını artırmak için:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Alternatif olarak, aşağıdaki markı 'yi yerel makinenize kaydedebilir ve için *-f* parametresinin yolunu ve dosya adını değiştirebilirsiniz `kubectl apply` . Örneğin, "C:\kubernetes\deployment-scalenyaml". Uygulama dağıtımı ölçeğinin yapılandırması şöyle olacaktır:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Dağıtımın dört pods olduğunu doğrulamak için:

```powershell
kubectl get pods -l app=nginx
```

İki ile dört Pod arasında bir dağıtım dağıtımı için örnek çıktı aşağıda gösterilmiştir:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Çıktıdan görebileceğiniz gibi, dağıtımınızda uygulamanızı çalıştırabilmeniz için artık dört bir tane var.

### <a name="delete-a-deployment"></a>Bir dağıtımı silme

Tüm yığınların dahil olduğu dağıtımı silmek için, `kubectl delete deployment` *NGINX-Deployment* ' ın adını ve ad alanı adını belirterek öğesini çalıştırmanız gerekir. Dağıtımı silmek için:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Komut kullanımının bir örneği, çıkış ile aşağıda gösterilmiştir:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Sonraki adımlar

[Kubernetes genel bakış](azure-stack-edge-gpu-kubernetes-overview.md)