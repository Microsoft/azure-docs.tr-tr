---
title: Azure Linux VM boyutları-HPC
description: Azure 'da Linux yüksek performanslı bilgi işlem sanal makineleri için kullanılabilen farklı boyutları listeler. Bu serideki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034885"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Yüksek performanslı işlem sanal makine boyutları

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPı 

Azure 'daki SR-ıOV özellikli VM boyutları, neredeyse her türlü MPı kullanılmasına izin verir.
SR-ıOV etkin olmayan VM 'lerde yalnızca Intel MPı 5. x sürümleri desteklenir. Intel MPı çalışma zamanı kitaplığı 'nın sonraki sürümleri (2017, 2018), Azure Linux RDMA sürücüleriyle uyumlu olmayabilir veya olmayabilir.


### <a name="supported-os-images"></a>Desteklenen işletim sistemi görüntüleri
 
Azure Marketi 'nde RDMA bağlantısını destekleyen birçok Linux dağıtımı vardır:
  
* **CentOS tabanlı HPC** -SR-IOV etkin olmayan VM 'Ler, CentOS tabanlı sürüm 6,5 HPC veya sonraki bir sürüm olan 7,5 ' e kadar uygundur. H serisi VM 'Ler için 7,1 sürümlerinin 7,5 olması önerilir. RDMA sürücüleri ve Intel MPı 5,1 VM 'ye yüklenir.
  SR-ıOV VM 'lerinde, CentOS-HPC 7,6, RDMA sürücüleri ve çeşitli MPı paketleri yüklüyken en iyi duruma getirilmiş ve önceden yüklenmiş olarak sunulur.
  Diğer RHEL/CentOS VM görüntüleri için, InfiniBand 'yi etkinleştirmek üzere ınfinıbandlinux uzantısını ekleyin. Bu Linux VM uzantısı, RDMA bağlantısı için (SR-ıOV VM 'lerde), Mellanox temelli sürücüleri (SR-ıOV) Aşağıdaki PowerShell cmdlet 'i, mevcut bir RDMA özellikli sanal makineye ınfinibanddriverlinux uzantısının en son sürümünü (sürüm 1,0) yüklüyor. RDMA özellikli VM, *Myvm* olarak adlandırılır ve *Batı ABD* bölgesindeki *myresourcegroup* adlı kaynak grubunda aşağıdaki gibi dağıtılır:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternatif olarak, VM uzantıları aşağıdaki JSON öğesiyle kolay dağıtım için Azure Resource Manager şablonlarına dahil edilebilir:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Aşağıdaki komut, *Myresourcegroup*adlı kaynak grubunda dağıtılan *myvmss* ADLı var olan bir VM Ölçek kümesindeki tüm RDMA özellikli vm 'Lere en son 1,0 ınfinibanddriverlinux uzantısını yükleme:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > CentOS tabanlı HPC görüntülerinde, çekirdek güncelleştirmeleri, **yıum** yapılandırma dosyasında devre dışıdır. Bunun nedeni, Linux RDMA sürücülerinin bir RPM paketi olarak dağıtılmaktadır ve çekirdek güncelleştirilirse sürücü güncelleştirmeleri çalışmayabilir.
  >
  

* **SUSE Linux Enterprise Server** , HPC için SLES 12 SP3, HPC için SLES 12 SP3 (Premium), HPC için SLES 12 SP1, HPC için SLES 12 SP1, HPC (Premium), SLES 12 SP4 ve SLES 15. RDMA sürücüleri yüklenir ve Intel MPı paketleri sanal makineye dağıtılır. Aşağıdaki komutu çalıştırarak MPı 'yi çalıştırın:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16,04 LTS, 18,04 LTS. VM 'de RDMA sürücülerini yapılandırın ve Intel MPı 'yi indirmek için Intel ile kaydolun:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  InfiniBand 'yi etkinleştirme hakkında daha fazla ayrıntı için MPı 'yi [ayarlama bölümüne bakın](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Küme yapılandırma seçenekleri

Azure, RDMA ağını kullanarak iletişim kurabilen Linux HPC VM kümeleri oluşturmak için çeşitli seçenekler sunar; örneğin: 

* **Sanal makineler** -RDMA özellikli HPC VM 'lerini aynı Kullanılabilirlik kümesinde dağıtın (Azure Resource Manager dağıtım modelini kullandığınızda). Klasik dağıtım modelini kullanıyorsanız, VM 'Leri aynı bulut hizmetinde dağıtın. 

* **Sanal Makine Ölçek Kümeleri** -sanal makine ölçek kümesinde dağıtımı tek bir yerleştirme grubuyla sınırlandırtığınızdan emin olun. Örneğin, bir Kaynak Yöneticisi şablonunda, `singlePlacementGroup` özelliğini `true`olarak ayarlayın. 

* **Sanal makineler arasında MPI** -sanal makineler (VM 'ler) arasında gerekliyse MPI iletişimi varsa, VM 'lerin aynı Kullanılabilirlik kümesinde veya sanal makinede aynı ölçek kümesinde olduğundan emin olun.

* **Azure CycleCloud** -Linux düğümlerinde MPI işlerini çalıştırmak Için [Azure CYCLECLOUD](/azure/cyclecloud/) 'te bir HPC kümesi oluşturun.

* **Azure Batch** -Linux işlem düğümlerinde MPI iş yüklerini çalıştırmak için bir [Azure Batch](/azure/batch/) havuzu oluşturun. Daha fazla bilgi için bkz. [Batch havuzlarında RDMA özellikli veya GPU özellikli örnekler kullanma](../../batch/batch-pool-compute-intensive-sizes.md). Toplu iş üzerinde kapsayıcı tabanlı iş yüklerini çalıştırmak için [Batch Shipbahçe](https://github.com/Azure/batch-shipyard) projesine de bakın.

* **MICROSOFT HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) , RDMA özellikli Azure VM 'Lerinde dağıtılan ve bir Windows Server baş düğümü tarafından yönetilen Işlem düğümlerinde çalışacak birkaç Linux dağıtımını destekler. Örnek bir dağıtım için bkz. [Azure 'DA HPC Pack LINUX RDMA kümesi oluşturma](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Ağ konuları
* Azure 'da, SR-ıOV olmayan, RDMA özellikli Linux sanal makinelerinde eth1, RDMA ağ trafiği için ayrılmıştır. Herhangi bir eth1 ayarını veya bu ağa başvuran yapılandırma dosyasında herhangi bir bilgiyi değiştirmeyin.
* SR-ıOV etkinleştirilmiş VM 'Lerde (HB ve HC Serisi), İb0 RDMA ağ trafiği için ayrılmıştır.
* Azure 'daki RDMA ağı, 172.16.0.0/16 adres alanını ayırır. MPı uygulamalarını bir Azure sanal ağında dağıtılan örneklerde çalıştırmak için, sanal ağ adres alanının RDMA ağıyla çakışmadığından emin olun.
* Küme yönetim aracınız seçiminize bağlı olarak, MPı işlerini çalıştırmak için ek sistem yapılandırması gerekebilir. Örneğin, bir VM kümesinde, SSH anahtarları oluşturarak veya parolasız SSH oturumu açarak küme düğümleri arasında güven oluşturmanız gerekebilir.


## <a name="other-sizes"></a>Diğer boyutlar
- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'da [HPC iş yüklerini](../workloads/hpc/configure.md) ayarlama, iyileştirme ve ölçeklendirme hakkında daha fazla bilgi edinin.
- Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
