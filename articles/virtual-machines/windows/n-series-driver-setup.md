---
title: Windows için Azure N serisi NVıDıA GPU sürücü kurulumu
description: Azure 'da Windows Server veya Windows çalıştıran N serisi VM 'Ler için NVıDıA GPU sürücülerini ayarlama
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557446"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Windows çalıştıran N serisi VM 'Lere NVıDıA GPU sürücülerini yükler 

NVıDıA GPU 'Lar tarafından desteklenen Azure N serisi VM 'lerinin GPU Özellikleri avantajlarından yararlanmak için NVıDıA GPU sürücülerini yüklemelisiniz. [NVıDıA GPU sürücü uzantısı](../extensions/hpccompute-gpu-windows.md) , bir N serisi VM 'ye uygun NVIDIA CUDA veya kılavuz sürücülerini yükleme. Azure portal veya Azure PowerShell veya Azure Resource Manager şablonları gibi araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen işletim sistemleri ve dağıtım adımları için [NVıDıA GPU sürücü uzantısı belgelerine](../extensions/hpccompute-gpu-windows.md) bakın.

NVıDıA GPU sürücülerini el ile yüklemeyi seçerseniz, bu makale desteklenen işletim sistemleri, sürücüler ve yükleme ve doğrulama adımları sağlar. [Linux sanal makineleri](../linux/n-series-driver-setup.md)için el ile sürücü kurulum bilgileri de mevcuttur.

Temel özellikler, depolama kapasiteleri ve disk ayrıntıları için bkz. [GPU WINDOWS VM boyutları](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Sürücü yükleme

1. Her N serisi sanal makineye uzak masaüstü ile bağlanın.

2. Windows işletim sisteminiz için desteklenen sürücüyü indirin, ayıklayın ve yükleyin.

Bir VM 'de KıLAVUZ sürücüsü yüklemesinden sonra yeniden başlatma gerekir. CUDA sürücü yüklemesinden sonra yeniden başlatma gerekmez.

## <a name="verify-driver-installation"></a>Sürücü yüklemeyi doğrulama

Lütfen NVIDIA Denetim Masası 'nın yalnızca KıLAVUZ sürücüsü yüklemesiyle erişilebilir olduğunu unutmayın. CUDA sürücülerini yüklediyseniz, NVIDIA Denetim Masası görünür olmayacaktır.

Device Manager ' de sürücü yüklemeyi doğrulayabilirsiniz. Aşağıdaki örnek, bir Azure NC VM üzerinde Tesla K80 kartının başarıyla yapılandırılmasını gösterir.

![GPU sürücüsü özellikleri](./media/n-series-driver-setup/GPU_driver_properties.png)

GPU cihaz durumunu sorgulamak için, sürücüyle birlikte yüklenen [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) komut satırı yardımcı programını çalıştırın.

1. Bir komut istemi açın ve **C:\Program Files\nvıdıa Corporation\NVSMI** dizinine geçin.

2. `nvidia-smi` öğesini çalıştırın. Sürücü yüklüyse aşağıdakine benzer bir çıktı görürsünüz. VM 'de Şu anda bir GPU iş yükü çalıştırmadığınız takdirde **GPU-Util** , **%0** gösterir. Sürücü sürümünüz ve GPU ayrıntılarınız gösterilenlerden farklı olabilir.

![NVıDıA cihaz durumu](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA ağ bağlantısı

RDMA ağ bağlantısı, NC24r gibi, aynı Kullanılabilirlik kümesinde veya bir sanal makine ölçek kümesindeki tek bir yerleştirme grubunda dağıtılan gibi, RDMA özellikli N serisi VM 'lerde etkinleştirilebilir. RDMA bağlantısı sağlayan Windows ağ cihazı sürücülerini yüklemek için HpcVmDrivers uzantısının eklenmesi gerekir. VM uzantısını RDMA özellikli N serisi bir VM 'ye eklemek için Azure Resource Manager [Azure PowerShell](/powershell/azure/) cmdlet 'lerini kullanın.

Batı ABD bölgesinde myVM adlı mevcut bir RDMA özellikli VM 'ye en son sürüm 1,1 HpcVMDrivers uzantısını yüklemek için:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Daha fazla bilgi için bkz. [Windows Için sanal makine uzantıları ve özellikleri](../extensions/features-windows.md).

RDMA ağı, [MICROSOFT MPI](/message-passing-interface/microsoft-mpi) veya Intel MPI 5. x ile çalışan uygulamalar Için Ileti geçirme arabirimi (MPI) trafiğini destekler. 


## <a name="next-steps"></a>Sonraki adımlar

* NVıDıA Tesla GPU 'Lar için GPU hızlandırmalı uygulamalar oluşturmaya yönelik geliştiriciler ayrıca en son [CUDA araç setini](https://developer.nvidia.com/cuda-downloads)indirebilir ve yükleyebilir. Daha fazla bilgi için bkz. [CUDA Yükleme Kılavuzu](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).
