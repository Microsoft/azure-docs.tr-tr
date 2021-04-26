---
title: Azure Stack Edge Pro GPU cihazında GPU paylaşımını kullanarak Kubernetes iş yükünü dağıtma
description: Azure Stack Edge Pro GPU cihazınızdan Kubernetes aracılığıyla GPU paylaşılan iş yükünü nasıl dağıtabileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 04299ba4028de313f640074ca98c0b611f734981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565398"
---
# <a name="deploy-a-kubernetes-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'unuzda GPU paylaşımını kullanarak Kubernetes iş yükünü dağıtma

Bu makalede Kapsayıcılı iş yüklerinin Azure Stack Edge Pro GPU cihazınızda GPU 'ları nasıl paylaşabileceği açıklanmaktadır. Bu makalede, GPU bağlamı paylaşımı olmadan diğeri cihazdaki Multi-Process Service (MPS) aracılığıyla etkinleştirilen bağlam paylaşımı ile birlikte iki işi çalıştıracaksınız. Daha fazla bilgi için bkz. [çok Işlem hizmeti](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Etkinleştirilen](azure-stack-edge-gpu-deploy-activate.md) ve [Işlem yapılandırılmış ](azure-stack-edge-gpu-deploy-configure-compute.md)Azure Stack Edge Pro GPU cihazına erişirsiniz. [Kubernetes API uç](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) noktanız var ve bu uç noktayı `hosts` istemcinizdeki cihaza erişebilecek dosyaya eklediniz.

1. [Desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)ile bir istemci sistemine erişirsiniz. Bir Windows istemcisi kullanıyorsanız, sistem cihaza erişmek için PowerShell 5,0 veya üstünü çalıştırmalıdır.

1. Bir ad alanı ve Kullanıcı oluşturdunuz. Kullanıcıya bu ad alanına erişimi de vermiş olursunuz. Bu ad alanının kubeconfig dosyası, cihazınıza erişmek için kullanacağınız istemci sisteminde yüklüdür. Ayrıntılı yönergeler için bkz. [Azure Stack Edge Pro GPU cihazınızdan kubectl aracılığıyla bir Kubernetes kümesine bağlanma ve bunları yönetme](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac). 

1. Aşağıdaki dağıtımı `yaml` Yerel sisteminize kaydedin. Bu dosyayı Kubernetes dağıtımını çalıştırmak için kullanacaksınız. Bu dağıtım, NVIDIA 'dan herkese açık olan [basit CUDA kapsayıcıları](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) temel alır. 

    ```yml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample1
    spec:
      template:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container1
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ---
    
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample2
    spec:
      template:
        metadata:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container2
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ```

## <a name="verify-gpu-driver-cuda-version"></a>GPU sürücüsünü doğrulama, CUDA sürümü

İlk adım, cihazınızın gerekli GPU sürücüsünü ve CUDA sürümlerini çalıştırdığından emin değildir.

1. [Cihazınızın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Şu komutu çalıştırın:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```

1. NVIDIA SMI çıktısında, cihazınızdaki GPU sürümünü ve CUDA sürümünü bir yere unutmayın. Azure Stack Edge 2102 yazılımı çalıştırıyorsanız, bu sürüm aşağıdaki sürücü sürümlerine karşılık gelir:

    - GPU sürücü sürümü: 460.32.03
    - CUDA sürümü: 11,2
    
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:24:27 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS> 
    ```

1. Bu oturumu, makale genelinde NVIDIA SMI çıkışını görüntülemek için kullandığınız sürece açık tutun.



## <a name="job-without-context-sharing"></a>Bağlam paylaşımı olmayan iş

Ad alanındaki cihazınızda bir uygulamayı dağıtmak için ilk işi çalıştıracaksınız `mynamesp1` . Bu uygulama dağıtımı aynı zamanda GPU bağlamı paylaşımının varsayılan olarak etkinleştirilmediğini de gösterecektir. 

1. Ad alanında çalışan tüm Pod 'leri listeleyin. Şu komutu çalıştırın: 

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    ```
1. Daha önce sağlanmış olan Deployment. YAML 'yi kullanarak cihazınızda bir dağıtım işi başlatın. Şu komutu çalıştırın: 

    ```powershell
    kubectl apply -f <Path to the deployment .yaml> -n <Name of the namespace> 
    ```  

    Bu iş iki kapsayıcı oluşturur ve her iki kapsayıcıda de n-body simülasyonu çalıştırır. Simülasyon yinelemelerinin sayısı içinde belirtilmiştir `.yaml` . Daha fazla bilgi için bkz. [N-Body simülasyonu](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).
    
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl apply -f -n mynamesp1 C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32>
    ```

1. Dağıtımda başlatılan Pod 'leri listelemek için aşağıdaki komutu çalıştırın:

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```   

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          28s
    cuda-sample2-db9vx   1/1     Running   0          27s
    PS C:\WINDOWS\system32>
    ```

    İki dizin bulunur `cuda-sample1-cf979886d-xcwsq` ve `cuda-sample2-68b4899948-vcv68` cihazınızda çalışır.

1. Pods 'nin ayrıntılarını getirir. Şu komutu çalıştırın:

    ```powershell
    kubectl -n <Name of the namespace> describe <Name of the job> 
    ```  

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
    Labels:         controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:34 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample1-27srm
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
    Labels:         controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:35 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample2-db9vx
    PS C:\WINDOWS\system32>
    ```
    Çıktı, her ikisinin de iş tarafından başarıyla oluşturulduğunu gösterir. 

1. Her iki kapsayıcı de n-body simülasyonu çalıştırırken, NVIDIA SMI çıktısından GPU kullanımını görüntüleyin. Cihazın PowerShell arabirimine gidin ve çalıştırın `Get-HcsGpuNvidiaSmi` .

    Her iki kapsayıcı de n-body simülasyonu çalıştırıldığında örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:26:41 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   64C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    197976      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    198051      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>    
    ```
    Gördüğünüz gibi, GPU 0 ' da n-body simülasyonu ile çalışan iki kapsayıcı (Type = C) vardır. 

1. N gövde benzetimini izleyin. Komutları çalıştırın `get pod` . Benzetim çalışırken örnek bir çıktı aşağıda verilmiştir. 

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          70s
    cuda-sample2-db9vx   1/1     Running   0          69s
    PS C:\WINDOWS\system32>
    ```

    Simülasyon tamamlandığında çıkış bunu gösterir. Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS      RESTARTS   AGE
    cuda-sample1-27srm   0/1     Completed   0          2m54s
    cuda-sample2-db9vx   0/1     Completed   0          2m53s
    PS C:\WINDOWS\system32>
    ```
 
1. Simülasyon tamamlandıktan sonra, benzetimi tamamlamak için günlükleri ve toplam süreyi görüntüleyebilirsiniz. Şu komutu çalıştırın:

    ```powershell
    kubectl logs -n <Name of the namespace> <pod name>
    ``` 

    Örnek bir çıktı aşağıda verilmiştir: 

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-27srm
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================  
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170398.766 ms
    = 98.459 billion interactions per second
    = 1969.171 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>
    ```

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-db9vx
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170368.859 ms
    = 98.476 billion interactions per second
    = 1969.517 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>    
    ```
1. Şu anda GPU üzerinde çalışan işlem olmaması gerekir. Bunu, NVIDIA SMI çıkışını kullanarak GPU kullanımını görüntüleyerek doğrulayabilirsiniz.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:32:52 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   38C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```

## <a name="job-with-context-sharing"></a>Bağlam paylaşımı olan iş

GPU bağlamı-paylaşımı, MPS 'de etkin olduğunda, n-body benzetimini iki CUDA kapsayıcıda dağıtmak için ikinci işi çalıştıracaksınız. İlk olarak, cihazda MPS 'yi etkinleştireceksiniz.

1. [Cihazınızın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Cihazınızda MP 'leri etkinleştirmek için `Start-HcsGpuMPS` komutunu çalıştırın.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    
    Set compute mode to EXCLUSIVE_PROCESS for GPU 00002C74:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Daha önce kullandığınız dağıtımı kullanarak işi çalıştırın `yaml` . Mevcut dağıtımı silmeniz gerekebilir. Bkz. [dağıtımı silme](#delete-deployment).

    Örnek bir çıktı aşağıda verilmiştir:

    ```yml
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 delete -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch "cuda-sample1" deleted
    job.batch "cuda-sample2" deleted
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 apply -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-vcznt   1/1     Running   0          21s
    cuda-sample2-zkx4w   1/1     Running   0          21s
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
    Labels:         controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  46s   job-controller  Created pod: cuda-sample1-vcznt
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
    Labels:         controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  47s   job-controller  Created pod: cuda-sample2-zkx4w
    PS C:\WINDOWS\system32>
    ```

1. Simülasyon çalışırken NVIDIA SMI çıkışını görüntüleyebilirsiniz. Çıktı, n-body simülasyonu ve MPS hizmeti (C türü) ile birlikte CUDA kapsayıcılarına (M + C türü) karşılık gelen işlemlerin çalıştığını gösterir. Tüm bu süreçler GPU 0 ' dır.

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:54:50 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   45C    P0    68W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144377    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144379    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

1. Simülasyon tamamlandıktan sonra, benzetimi tamamlamak için günlükleri ve toplam süreyi görüntüleyebilirsiniz. Şu komutu çalıştırın:

    ```powershell
        PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
        NAME                 READY   STATUS      RESTARTS   AGE
        cuda-sample1-vcznt   0/1     Completed   0          5m44s
        cuda-sample2-zkx4w   0/1     Completed   0          5m44s
        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-vcznt
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================    
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154979.453 ms
        = 108.254 billion interactions per second
        = 2165.089 single-precision GFLOP/s at 20 flops per interaction


        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-zkx4w
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154986.734 ms
        = 108.249 billion interactions per second
        = 2164.987 single-precision GFLOP/s at 20 flops per interaction
        PS C:\WINDOWS\system32>
    ```
1. Simülasyon tamamlandıktan sonra, NVIDIA SMI çıkışını yeniden görüntüleyebilirsiniz. Yalnızca MPS hizmeti için NVIDIA-CUDA-MPS-sunucu süreci çalışıyor olarak gösterilir. 

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:59:55 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   37C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

## <a name="delete-deployment"></a>Dağıtımı Sil

MP 'ler etkinken ve cihazınızda MPS Disable ile çalışırken dağıtımları silmeniz gerekebilir.

Cihazınızdaki dağıtımı silmek için şu komutu çalıştırın: 

```powershell
kubectl delete -f <Path to the deployment .yaml> -n <Name of the namespace> 
```

Örnek bir çıktı aşağıda verilmiştir:

```powershell
PS C:\WINDOWS\system32> kubectl delete -f 'C:\gpu-sharing\k8-gpusharing.yaml' -n mynamesp1
deployment.apps "cuda-sample1" deleted
deployment.apps "cuda-sample2" deleted
PS C:\WINDOWS\system32>
```
    
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'unuzda GPU paylaşımıyla IoT Edge bir iş yükü dağıtın](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
