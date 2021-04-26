---
title: Azure Stack Edge Pro GPU cihazında GPU paylaşımı
description: Azure Stack Edge Pro GPU cihazında GPU 'ları paylaşma yaklaşımlarını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: ff1c7b79a49b0b659056c89af3c61f28b72ebc50
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645236"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazınızda GPU paylaşımı

Grafik işleme birimi (GPU), grafik işlemesini hızlandırmak için tasarlanan özel bir işlemcisidir. GPU 'Lar çok sayıda veriyi eşzamanlı olarak işleyebilir, böylece makine öğrenimi, video düzenlemesi ve oyun uygulamaları için yararlı hale getirebilirsiniz. Genel amaçlı işlem için CPU 'ya ek olarak, Azure Stack Edge Pro GPU cihazlarınız, donanım hızlandırmalı sınırlama gibi yoğun işlem yoğunluklu iş yükleri için bir veya iki NVIDIA Tesla T4 GPU içerebilir. Daha fazla bilgi için bkz. [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>GPU paylaşımı hakkında

Birçok makine öğrenimi veya diğer bilgi işlem iş yükleri adanmış bir GPU gerektirmez. GPU 'lar paylaşılabilir ve Kapsayıcılı veya VM iş yükleri arasında GPU 'Lar, GPU 'nun performans avantajlarını önemli ölçüde etkilemeden GPU kullanımını artırmaya yardımcı olur.  

## <a name="using-gpu-with-vms"></a>Sanal makineler ile GPU kullanma

Azure Stack Edge Pro cihazınızda, sanal makine iş yükleri dağıtıldığında GPU paylaştırılamaz. GPU yalnızca bir VM ile eşleştirilebilir. Bu, iki GPU ile donatılmış bir cihazda tek bir GPU ve iki VM 'ye sahip bir cihazda yalnızca bir GPU VM 'si olabilir. Ayrıca, kapsayıcı iş yükleri için yapılandırılmış Kubernetes içeren bir cihazda GPU VM 'Leri kullanılırken göz önünde bulundurmanız gereken diğer faktörler vardır. Daha fazla bilgi için bkz. [GPU VM 'leri ve Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Kapsayıcılarla GPU kullanma

Kapsayıcılı iş yüklerini dağıtıyorsanız, bir GPU, donanım ve yazılım katmanında birden çok şekilde paylaşılabilir. Azure Stack Edge Pro cihazınızda Tesla T4 GPU ile yazılım paylaşımıyla sınırlandırıyoruz. Cihazınızda, GPU yazılım paylaşımı için aşağıdaki iki yaklaşım kullanılır: 

- İlk yaklaşım, paylaşılan zaman olabilecek GPU sayısını belirtmek için ortam değişkenlerinin kullanılmasını içerir. Bu yaklaşımı kullanırken aşağıdaki uyarıları göz önünde bulundurun:

    - Bu yöntemle bir veya daha fazla GPU belirtebilirsiniz. Kesirli kullanım belirtmek mümkün değildir.
    - Birden çok modül tek bir GPU ile eşleştirilebilir, ancak aynı modül birden fazla GPU ile eşlenemez.
    - NVIDIA SMı çıkışıyla, bellek kullanımı dahil olmak üzere genel GPU kullanımını görebilirsiniz.
    
    Daha fazla bilgi için bkz. cihazınızda [GPU 'yu kullanan IoT Edge modülünü dağıtma](azure-stack-edge-gpu-configure-gpu-modules.md) .

- İkinci yaklaşım, NVIDIA GPU 'larınız üzerinde Multi-Process hizmetini etkinleştirmenizi gerektirir. MPS, CUDA kullanan birden çok işlemin tek bir paylaşılan GPU üzerinde aynı anda çalışmasını sağlayan bir çalışma zamanı hizmetidir. MPS maksimum kullanım sağlamak için GPU 'daki farklı süreçlerden çekirdek ve Memcopy işlemlerinin örtüşmesini sağlar. Daha fazla bilgi için bkz. [çok Işlem hizmeti](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Bu yaklaşımı kullanırken aşağıdaki uyarıları göz önünde bulundurun:
    
    - MPS, GPU dağıtımında daha fazla bayrak belirtmenize olanak tanır.
    - MPS aracılığıyla kesirli kullanımı belirtebilir ve böylece cihazda dağıtılan her uygulamanın kullanımını sınırlaırsınız. `env`Aşağıdaki parametreyi ekleyerek, öğesinin bölümünde her bir uygulama için kullanılacak GPU yüzdesini belirtebilirsiniz `deployment.yaml` : 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU kullanımı
 
Cihazınıza dağıtılan Kapsayıcılı iş yüklerinde GPU 'YU paylaştığınızda, NVIDIA sistem yönetimi arabirimini (NVIDIA-SMI) kullanabilirsiniz. NVIDIA-SMI, NVIDIA GPU cihazlarını yönetmenize ve izlemenize yardımcı olan bir komut satırı yardımcı programıdır. Daha fazla bilgi için bkz. [NVIDIA sistem yönetimi arabirimi](https://developer.nvidia.com/nvidia-system-management-interface).

GPU kullanımını görüntülemek için önce cihazın PowerShell arabirimine bağlanın. Komutunu çalıştırın `Get-HcsNvidiaSmi` ve NVıDıA SMI çıkışını görüntüleyin. Ayrıca, GPU kullanımının nasıl değiştiği ve ardından cihazda birden çok iş yükü dağıtımı yapabilirsiniz. Daha fazla bilgi için bkz. [çok Işlem hizmeti 'Ni etkinleştirme](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'Da Kubernetes dağıtımları Için GPU paylaşımı](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Azure Stack Edge Pro 'Daki IoT dağıtımları Için GPU paylaşımı](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
