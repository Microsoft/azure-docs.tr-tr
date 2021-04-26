---
title: Azure Stack Edge Pro GPU cihazında GPU paylaşımını kullanarak IoT Edge iş yükünü dağıtma
description: Azure Stack Edge Pro GPU cihazınızdan IoT Edge bir GPU paylaşılan iş yükünü nasıl dağıtabileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565406"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'unuzda GPU paylaşımını kullanarak IoT Edge iş yükü dağıtma

Bu makalede Kapsayıcılı iş yüklerinin Azure Stack Edge Pro GPU cihazınızda GPU 'ları nasıl paylaşabileceği açıklanmaktadır. Yaklaşım, çok Işlem hizmeti 'nin (MPS) etkinleştirilmesini ve ardından IoT Edge bir dağıtım aracılığıyla GPU iş yüklerini belirtmeyi içerir. 

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Etkinleştirilen](azure-stack-edge-gpu-deploy-activate.md) ve [Işlem yapılandırılmış](azure-stack-edge-gpu-deploy-configure-compute.md)Azure Stack Edge Pro GPU cihazına erişirsiniz. [Kubernetes API uç](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) noktanız var ve bu uç noktayı `hosts` istemcinizdeki cihaza erişebilecek dosyaya eklediniz.

1. [Desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)ile bir istemci sistemine erişirsiniz. Bir Windows istemcisi kullanıyorsanız, sistem cihaza erişmek için PowerShell 5,0 veya üstünü çalıştırmalıdır.

1. Aşağıdaki dağıtımı `json` Yerel sisteminize kaydedin. IoT Edge dağıtımını çalıştırmak için bu dosyadaki bilgileri kullanacaksınız. Bu dağıtım, NVIDIA 'dan herkese açık olan [basit CUDA kapsayıcıları](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) temel alır. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>GPU sürücüsünü doğrulama, CUDA sürümü

İlk adım, cihazınızın gerekli GPU sürücüsünü ve CUDA sürümlerini çalıştırdığından emin değildir.

1. [Cihazınızın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Şu komutu çalıştırın:

    `Get-HcsGpuNvidiaSmi`

1. NVIDIA SMI çıktısında, cihazınızdaki GPU sürümünü ve CUDA sürümünü bir yere unutmayın. Azure Stack Edge 2102 yazılımı çalıştırıyorsanız, bu sürüm aşağıdaki sürücü sürümlerine karşılık gelir:

    - GPU sürücü sürümü: 460.32.03
    - CUDA sürümü: 11,2
    
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
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


## <a name="deploy-without-context-sharing"></a>Bağlam paylaşımı olmadan dağıtma

Artık multi-process hizmeti çalışmadığı ve bağlam paylaşımı olmadığı durumlarda cihazınızda bir uygulamayı dağıtabilirsiniz. Dağıtım, cihazınızda bulunan ad alanındaki Azure portal aracılığıyla yapılır `iotedge` .

### <a name="create-user-in-iot-edge-namespace"></a>IoT Edge ad alanında Kullanıcı oluşturma

Önce ad alanına bağlanacak bir Kullanıcı oluşturacaksınız `iotedge` . IoT Edge modülleri iotedge ad alanında dağıtılır. Daha fazla bilgi için bkz. [cihazınızdaki Kubernetes ad alanları](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Kullanıcı oluşturmak ve kullanıcıya ad alanına erişim vermek için bu adımları izleyin `iotedge` . 

1. [Cihazınızın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Ad alanında yeni bir kullanıcı oluşturun `iotedge` . Şu komutu çalıştırın:

    `New-HcsKubernetesUser -UserName <user name>`

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Düz metin olarak görünen çıktıyı kopyalayın. Çıktıyı yerel makinenizde Kullanıcı profilinizin klasörüne (uzantısı olmadan) bir *yapılandırma* dosyası olarak kaydedin ( `.kube` Örneğin,) `C:\Users\<username>\.kube` . 

1. Oluşturduğunuz kullanıcıya `iotedge` ad alanı erişimi verin. Şu komutu çalıştırın:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Örnek bir çıktı aşağıda verilmiştir:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Ayrıntılı yönergeler için bkz. [Azure Stack Edge Pro GPU cihazınızdan kubectl aracılığıyla bir Kubernetes kümesine bağlanma ve bunları yönetme](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Modülleri Portal aracılığıyla dağıtma

IoT Edge modüllerini Azure portal aracılığıyla dağıtın. N-body simülasyonu çalıştıran, genel kullanıma açık NVIDIA CUDA örnek modülleri dağıtırsınız. Daha fazla bilgi için bkz. [N-Body simülasyonu](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Cihazınızda IoT Edge hizmetinin çalıştığından emin olun.

    ![IoT Edge hizmet çalışıyor.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Sağ bölmedeki IoT Edge kutucuğunu seçin. **IoT Edge > özellikleri**' ne gidin. Sağ bölmede, aygıtınızla ilişkili IoT Hub kaynağını seçin.

    ![Özellikleri görüntüleyin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. IoT Hub kaynağında, **otomatik cihaz yönetimi > IoT Edge**' ne gidin. Sağ bölmede, aygıtınızla ilişkili IoT Edge cihazını seçin.

    ![IoT Edge gidin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. **Modül ayarla**' yı seçin.

    ![Modülleri ayarla ' ya gidin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. **+ Add > + IoT Edge modülünü** seçin.

    ![IoT Edge modülünü ekleyin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. **Modül ayarları** sekmesinde, **IoT Edge modül adı** ve **görüntü URI 'si** sağlayın. **Görüntü çekme ilkesini** **oluşturma sırasında** olarak ayarlayın.

    ![Modül ayarları.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. **Ortam değişkenleri** sekmesinde, **NVIDIA_VISIBLE_DEVICES** **0** olarak belirtin.

    ![Ortam değişkenleri.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. **Kapsayıcı oluşturma seçenekleri** sekmesinde aşağıdaki seçenekleri belirtin:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    Seçenekler aşağıdaki gibi görüntülenir:

    ![Kapsayıcı oluşturma seçenekleri.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    **Add (Ekle)** seçeneğini belirleyin.

1. Eklediğiniz modülün **çalışıyor** olarak gösterilmesi gerekir. 

    ![İnceleme ve dağıtım oluşturma.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. İlk modülü eklerken izlediğiniz bir modül eklemek için tüm adımları yineleyin. Bu örnekte, modülün adını olarak sağlayın `cuda-sample2` . 

    ![2. modülün modül ayarları.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Aynı ortam değişkenini, her iki modülde aynı GPU 'YU paylaşacağı şekilde kullanın.

    ![2. modülün ortam değişkeni.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    İlk modül için sağladınız aynı kapsayıcı oluşturma seçeneklerini kullanın ve **Ekle**' yi seçin.

    ![Kapsayıcı oluşturma seçeneklerini ikinci modüller için oluşturun.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. **Modülleri ayarla** sayfasında, **gözden geçir + oluştur** ' u seçin ve ardından **Oluştur**' u seçin. 

    ![İkinci dağıtımı inceleyin ve oluşturun.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. Her iki modülün **çalışma zamanı durumu** artık **çalışıyor** olarak gösterilmelidir.  

    ![2. dağıtım durumu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>İş yükü dağıtımını izleme

1. Yeni bir PowerShell oturumu açın.

1. Ad alanında çalışan Pod 'leri listeleyin `iotedge` . Şu komutu çalıştırın:

    `kubectl get pods -n iotedge`   

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    İki dizin bulunur `cuda-sample1-97c494d7f-lnmns` ve `cuda-sample2-d9f6c4688-2rld9` cihazınızda çalışır.

1. Her iki kapsayıcı de n-body simülasyonu çalıştırırken, NVIDIA SMI çıktısından GPU kullanımını görüntüleyin. Cihazın PowerShell arabirimine gidin ve çalıştırın `Get-HcsGpuNvidiaSmi` .

    Her iki kapsayıcı de n-body simülasyonu çalıştırıldığında örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Gördüğünüz gibi, GPU 0 ' da n-body simülasyonu ile çalışan iki kapsayıcı vardır. Ayrıca, bunlara karşılık gelen bellek kullanımını da görüntüleyebilirsiniz.

1. Simülasyon tamamlandığında, NVIDIA SMI çıktısı cihazda çalışan işlem olmadığını gösterir.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
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
 
1. N-body simülasyonu tamamlandıktan sonra, dağıtımın ayrıntılarını ve benzetimin tamamlanması için gereken süreyi anlamak için günlükleri görüntüleyin. 

    İlk kapsayıcıdan örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    İkinci kapsayıcıdan örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Modül dağıtımını durdurun. Cihazınız için IoT Hub kaynak:
    1. **Otomatik cihaz dağıtımına > IoT Edge** gidin. Cihazınıza karşılık gelen IoT Edge cihazını seçin.

    1. Modül **Ayarla** ' ya gidin ve bir modül seçin. 

        ![Modülü ayarla ' yı seçin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. **Modüller** sekmesinde bir modül seçin.
    
        ![Bir modül seçin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  **Modül ayarları** sekmesinde, **istenen durumu** durduruldu olarak ayarlayın. **Güncelleştir**’i seçin.

        ![Modül ayarlarını değiştirin.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Cihaza dağıtılan ikinci modülü durdurmak için adımları tekrarlayın. **Gözden Geçir + oluştur**’u ve sonra da **Oluştur**’u seçin. Bu, dağıtımı güncelleştirmelidir.

        ![Gözden geçirin ve güncelleştirilmiş dağıtımı oluşturun.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. **Modülleri ayarlama** sayfasını birden çok kez yenileyin. modül **çalışma zamanı durumu** **durduruldu** olarak gösterilene kadar.

        ![Dağıtım durumunu doğrulayın.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Bağlam paylaşımı ile dağıtma

Şimdi de MPS 'yi cihazınızda çalışırken iki CUDA kapsayıcı üzerinde n-body simülasyonu dağıtabilirsiniz. İlk olarak, cihazda MPS 'yi etkinleştireceksiniz.


1. [Cihazınızın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Cihazınızda MP 'leri etkinleştirmek için `Start-HcsGpuMPS` komutunu çalıştırın.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Cihazın PowerShell arabiriminden NVIDIA SMI çıkışını alın. `nvidia-cuda-mps-server`İşlemi veya CIHAZDA MPS hizmeti 'nin çalıştığını görebilirsiniz.

    Örnek bir çıktı aşağıda verilmiştir:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Daha önce durdurduğu modülleri dağıtın. **İstenen durumu** **set modules** aracılığıyla çalışıyor olarak ayarlayın.

    Örnek çıktı aşağıda verilmiştir:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Modüllerin cihazınızda dağıtıldığını ve çalıştığını görebilirsiniz.

1. Modüller dağıtıldığında, n-body benzetimi her iki kapsayıcıda de çalışmaya başlar. Simülasyon ilk kapsayıcıda tamamlandığında örnek çıktı şöyledir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Simülasyon ikinci kapsayıcıda tamamlandığında örnek çıktı şöyledir:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Her iki kapsayıcı da n gövde simülasyonu çalıştırıyorsa, cihazın PowerShell arabiriminden NVIDIA SMI çıkışını alın. Örnek bir çıktı aşağıda verilmiştir. Üç işlem vardır, `nvidia-cuda-mps-server` işlem (tür C) MPS hizmetine karşılık gelir ve `/tmp/nbody` işlemler (tür M + C), modüller tarafından dağıtılan n gövde iş yüklerine karşılık gelir. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'unuzda paylaşılan BIR GPU Kubernetes iş yükünü dağıtın](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
