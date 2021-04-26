---
title: FPGA cihazında IoT Edge modüllerini Azure Stack Edge Pro GPU cihazında çalışacak şekilde değiştirin
description: Mevcut FPGA cihazlarındaki mevcut IoT Edge modüllerinin Azure Stack Edge Pro GPU cihazınızda çalışması için hangi değişikliklerin gerekli olduğunu açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1276a242efb1917a0c4a24aa73c3e0d11f81e158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559171"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazındaki Azure Stack Edge Pro FPGA cihazlarından mevcut IoT Edge modüllerini Çalıştır

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Bu makalede, Azure Stack Edge Pro GPU cihazındaki bir Kubernetes tabanlı IoT Edge platformunda çalıştırılabilmesi için Azure Stack Edge Pro FPGA üzerinde çalışan Docker tabanlı IoT Edge modülü için gereken değişiklikler ayrıntılı olarak açıklanır. 

## <a name="about-iot-edge-implementation"></a>IoT Edge uygulama hakkında 

IoT Edge uygulama Azure Stack Edge Pro FPGA cihazlarından ve Azure Stack Edge Pro GPU cihazlarından farklıdır. GPU cihazları için Kubernetes IoT Edge için bir barındırma platformu olarak kullanılır. FPGA cihazlarındaki IoT Edge Docker tabanlı bir platform kullanır. IoT Edge Docker tabanlı uygulama modeli, otomatik olarak Kubernetes yerel uygulama modeline çevrilir. Ancak, Kubernetes uygulama modelinin yalnızca küçük bir alt kümesi desteklenmeye devam etmek için bazı değişiklikler yapmanız gerekebilir.

İş yüklerinizi bir FPGA cihazından GPU cihazına geçiriyorsanız, Kubernetes platformunda başarıyla çalıştırılabilmesi için mevcut IoT Edge modüllerinde değişiklikler yapmanız gerekir. Depolama, ağ, kaynak kullanımı ve Web proxy gereksinimlerinizi farklı şekilde belirtmeniz gerekebilir. 

## <a name="storage"></a>Depolama

IoT Edge modülleri için depolama belirtirken aşağıdaki bilgileri göz önünde bulundurun.

- Kubernetes üzerindeki kapsayıcılar için depolama birimi, birim bağlama kullanılarak belirtildi.
- Kubernetes üzerinde dağıtım, kalıcı depolama veya konak yollarının ilişkilendirilmesi için bağlamalar içeremez.
    - Kalıcı depolama için, `Mounts` türüyle kullanın `volume` .
    - Konak yolları için, `Mounts` türü ile kullanın `bind` .
- Kubernetes üzerinde IoT Edge için, `Mounts` yalnızca dizin için geçerlidir, dosya için değildir.

#### <a name="example---storage-via-volume-mounts"></a>Birim bağlama aracılığıyla örnek depolama 

Docker üzerinde IoT Edge için konak yolu bağlamaları cihazdaki paylaşımları kapsayıcının içindeki yollarla eşlemek için kullanılır. Aşağıda, FPGA cihazlarında kullanılan kapsayıcı oluşturma seçenekleri verilmiştir:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Kubernetes üzerinde IoT Edge için ana bilgisayar yolları için, türü ile birlikte kullanmayla ilgili bir örnek `Mounts` `bind` burada gösterilmektedir:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Kubernetes üzerinde IoT Edge çalıştıran GPU cihazları için, depolama belirtmek için birim bağlama kullanılır. Paylaşımlar kullanarak depolama sağlamak için değeri, `Mounts.Source` GPU cihazınızda sağlanan SMB veya NFS paylaşımının adı olacaktır. , `/home/input` Birimin kapsayıcı içinde erişilebilir olduğu yoldur. GPU cihazlarında kullanılan kapsayıcı oluşturma seçenekleri şunlardır:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Ağ

IoT Edge modülleri için ağ belirtirken aşağıdaki bilgileri göz önünde bulundurun. 

- `HostPort` küme içinde ve dışında bir hizmeti kullanıma sunmak için belirtim gereklidir.
    - Hizmetin görünürlüğünü yalnızca kümeyle sınırlamak için K8sExperimental seçenekleri.
- Modüller arası iletişim için `HostPort` belirtim ve eşlenmiş bağlantı noktasını kullanarak bağlantı (kapsayıcı tarafından sunulan bağlantı noktası kullanmayan) gerekir.
- Konak ağı ile birlikte çalışarak `dnsPolicy = ClusterFirstWithHostNet` tüm kapsayıcılar (özellikle `edgeHub` ) de ana bilgisayar ağı üzerinde olması gerekmez. <!--Need further clarifications on this one-->
- TCP için bağlantı noktası eşlemelerini eklemek aynı istekteki UDP çalışmıyor.

#### <a name="example---external-access-to-modules"></a>Örnek-modüllere dış erişim 

Bağlantı noktası bağlamalarını belirten IoT Edge modüller için, cihazın yerel kullanıcı arabiriminde belirtilen Kubernetes dış hizmet IP aralığı kullanılarak bir IP adresi atanır. Aşağıdaki örnekte gösterildiği gibi, Kubernetes üzerinde Docker vs IoT Edge üzerinde IoT Edge kapsayıcı oluşturma seçeneklerinde hiçbir değişiklik yoktur.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Ancak modülünüzün atandığı IP adresini sorgulamak için, Kubernetes panosunu, [Hizmetler veya modüller IÇIN IP adresi al](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)bölümünde açıklandığı gibi kullanabilirsiniz. 

Alternatif olarak, cihazın [PowerShell arabirimine bağlanabilir](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) ve `iotedge` cihazınızda çalışan tüm modülleri listelemek için List komutunu kullanabilirsiniz. [Komut çıktısı](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) Ayrıca modülle Ilişkili dış IP 'leri gösterir.


## <a name="resource-usage"></a>Kaynak kullanımı 

GPU cihazlarındaki Kubernetes tabanlı IoT Edge kurulumları ile donanım hızlandırma, bellek ve CPU gereksinimleri gibi kaynaklar, FPGA cihazlarından farklı şekilde belirtilmiştir. 

#### <a name="compute-acceleration-usage"></a>İşlem hızlandırma kullanımı

FPGA 'de modül dağıtmak için kapsayıcı oluşturma seçeneklerini kullanın <!--with Device Bindings--> Aşağıdaki yapılandırmada gösterildiği gibi: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
GPU için, aşağıdaki en düşük yapılandırmada gösterildiği gibi, cihaz bağlamaları yerine kaynak isteği belirtimlerini kullanın. NVIDIA kaynaklarını katapdan değil ve belirtmeniz gerekir `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Bellek ve CPU kullanımı
 
Bellek ve CPU kullanımını ayarlamak için, bölümündeki modüller için işlemci sınırları ' nı kullanın `k8s-experimental` . <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
Bellek ve CPU belirtimi gerekli değildir ancak genellikle iyi bir uygulamadır. `requests`Belirtilmemişse, limitlerde ayarlanan değerler gereken en düşük düzeyde kullanılır. 

Modüller için paylaşılan bellek kullanmak farklı bir yol da gerektirir. Örneğin, canlı video analizi ve çıkarım çözümleri arasında paylaşılan bellek erişimi için konak IPC modunu, [Azure Stack Edge üzerinde canlı video analizi dağıtma](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal)bölümünde açıklandığı gibi kullanabilirsiniz.


## <a name="web-proxy"></a>Web ara sunucusu 

Web proxy 'yi yapılandırırken aşağıdaki bilgileri göz önünde bulundurun:

Ağınızda Web ara sunucusu yapılandırdıysanız, `edgeHub` FPGA cihazlarında, Docker tabanlı IoT Edge kurulumunda dağıtım için aşağıdaki ortam değişkenlerini yapılandırın:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (Web proxy trafiğe izin verilmediği takdirde `Amqp` )

GPU cihazlarındaki Kubernetes tabanlı IoT Edge kurulumları için, dağıtım sırasında bu ek değişkeni yapılandırmanız gerekir:

- `no_proxy`: localhost

- Kubernetes platformunda IoT Edge proxy, 35000 ve 35001 numaralı bağlantı noktasını kullanır. Modülünüzün Bu bağlantı noktalarında çalıştırılmadığından emin olun veya bağlantı noktası çakışmalarına neden olabilir. 

## <a name="other-differences"></a>Diğer farklılıklar

- **Dağıtım stratejisi**: modüldeki güncelleştirmelerin dağıtım davranışını değiştirmeniz gerekebilir. IoT Edge modüller için varsayılan davranış sıralı güncelleştirmedir. Bu davranış, modül donanım ivmesi veya ağ bağlantı noktaları gibi kaynakları kullanıyorsa, güncelleştirilmiş modülün yeniden başlatılmasını önler. Bu davranışın, GPU cihazları için Kubernetes platformunda kalıcı birimlerle ilgilenirken özellikle beklenmeyen etkileri olabilir. Bu varsayılan davranışı geçersiz kılmak için `Recreate` `k8s-experimental` modülünüzün bölümünde bir belirtebilirsiniz.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- Modül **adları**: modül adları Kubernetes adlandırma kurallarını izlemelidir. Bu modülleri Kubernetes ile IoT Edge üzerine taşırken Docker ile IoT Edge çalışan modülleri yeniden adlandırmanız gerekebilir. Adlandırma hakkında daha fazla bilgi için bkz. [Kubernetes adlandırma kuralları](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Diğer seçenekler**: 
    - FPGA cihazlarında çalışan bazı Docker oluşturma seçenekleri, GPU cihazlarınızdaki Kubernetes ortamında çalışmaz. Örneğin:, – EntryPoint gibi.<!--can we confirm what exactly is required here-->
    - `:`İle değiştirilmeleri gereken ortam değişkenleri `__` .
    - Bir Kubernetes pod için **kapsayıcı oluşturma** durumu IoT Hub kaynaktaki bir modülün durumunu **geri** alma ' ya yönlendirir. Pod 'un bu durumda olmasının bazı nedenleri olsa da, büyük bir kapsayıcı görüntüsünün düşük bir ağ bant genişliği bağlantısı üzerinden çekilmesinin yaygın bir nedeni olur. Pod bu durumda olduğunda modülün durumu ıOT hub 'ında **geri** açılır, ancak modül hemen başlatılıyor.


## <a name="next-steps"></a>Sonraki adımlar

- [GPU 'yu bir modül kullanacak şekilde yapılandırma](./azure-stack-edge-gpu-configure-gpu-modules.md)hakkında daha fazla bilgi edinin.