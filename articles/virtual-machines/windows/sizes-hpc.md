---
title: Azure Windows VM boyutları-HPC
description: Azure 'da Windows yüksek performanslı bilgi işlem sanal makineleri için kullanılabilen farklı boyutları listeler. Bu serideki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: e96c74eb4b9b96459bb5de7db2faeb47ed99a82e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065448"
---
# <a name="high-performance-compute-vm-sizes"></a>Yüksek performanslı işlem VM boyutları

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **İşletim sistemi** -Windows Server 2016 yukarıdaki HPC serisi VM 'lerde. Windows Server 2012 R2, Windows Server 2012, SR-ıOV olmayan VM 'Lerde de desteklenir (Bu nedenle HB ve HC hariç).

* **MPI** -Azure 'daki SR-ıOV etkin VM boyutları (HB, HC), her türlü MPI 'ın Mellanox ile kullanılmasına izin verir.
SR-ıOV olmayan VM 'lerde desteklenen MPı uygulamaları, örnekler arasında iletişim kurmak için Microsoft ağ doğrudan (ND) arabirimini kullanır. Bu nedenle, yalnızca Microsoft MPı (MS-MPı) 2012 R2 veya üzeri ve Intel MPı 5. x sürümleri desteklenir. Intel MPı çalışma zamanı kitaplığı 'nın sonraki sürümleri (2017, 2018), Azure RDMA sürücüleriyle uyumlu olmayabilir veya olmayabilir.

* **INFINIBANDDRIVERWINDOWS VM Uzantısı** -RDMA özellikli VM 'Lerde, InfiniBand 'i etkinleştirmek Için ınfinibanddriverwindows uzantısını ekleyin. Bu Windows sanal makine uzantısı, RDMA bağlantısı için Windows ağ doğrudan sürücülerini (SR-ıOV olmayan VM 'Lere) veya (SR-ıOV VM 'lerinde) Mellanox sürücüleri (SR-ıOV sanal makinelerinde) kurar.
A8 ve A9 örneklerinin bazı dağıtımlarında, HpcVmDrivers uzantısı otomatik olarak eklenir. HpcVmDrivers VM uzantısının kullanım dışı olduğunu unutmayın; güncellenmeyecektir. VM uzantısını bir VM 'ye eklemek için [Azure PowerShell](/powershell/azure/overview) cmdlet 'lerini kullanabilirsiniz. 

  Aşağıdaki komut, *Batı ABD* bölgesinde *myresourcegroup* adlı kaynak grubunda dağıtılan *MYVM* adlı mevcut bir RDMA özellikli sanal makineye en son 1,0 ınfinibanddriverwindows uzantısını yükleme:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Alternatif olarak, VM uzantıları, aşağıdaki JSON öğesiyle kolay dağıtım için Azure Resource Manager şablonlarına dahil edilebilir:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Aşağıdaki komut, *Myresourcegroup*adlı kaynak grubunda dağıtılan *myvmss* adlı mevcut bir VM Ölçek kümesindeki tüm RDMA özellikli vm 'Lere en son 1,0 ınfinibanddriverwindows uzantısını yükleme:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Daha fazla bilgi için bkz. [sanal makine uzantıları ve özellikleri](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Klasik dağıtım modelinde](classic/manage-extensions.md)dağıtılan VM 'ler için uzantılara de çalışabilirsiniz.

* **RDMA ağ adresi alanı** -Azure 'daki RDMA ağı, 172.16.0.0/16 adres alanını ayırır. MPı uygulamalarını bir Azure sanal ağında dağıtılan örneklerde çalıştırmak için, sanal ağ adres alanının RDMA ağıyla çakışmadığından emin olun.


### <a name="cluster-configuration-options"></a>Küme yapılandırma seçenekleri

Azure, RDMA ağını kullanarak iletişim kurabilen Windows HPC VM kümeleri oluşturmak için çeşitli seçenekler sunar; örneğin: 

* **Sanal makineler** -RDMA özellikli HPC VM 'lerini aynı Kullanılabilirlik kümesinde dağıtın (Azure Resource Manager dağıtım modelini kullandığınızda). Klasik dağıtım modelini kullanıyorsanız, VM 'Leri aynı bulut hizmetinde dağıtın. 

* **Sanal Makine Ölçek Kümeleri** -sanal makine ölçek kümesinde dağıtımı tek bir yerleştirme grubuyla sınırlandırtığınızdan emin olun. Örneğin, bir Kaynak Yöneticisi şablonunda, `singlePlacementGroup` özelliğini `true`olarak ayarlayın. 

* **Sanal makineler arasında MPI** -sanal makineler (VM 'ler) arasında gerekliyse MPI iletişimi varsa, VM 'lerin aynı Kullanılabilirlik kümesinde veya sanal makinede aynı ölçek kümesinde olduğundan emin olun.

* **Azure CycleCloud** -Windows düğümlerinde MPI işlerini çalıştırmak Için [Azure CYCLECLOUD](/azure/cyclecloud/) 'te bir HPC kümesi oluşturun.

* **Azure Batch** -Windows Server işlem düğümlerinde MPI iş yüklerini çalıştırmak için bir [Azure Batch](/azure/batch/) havuzu oluşturun. Daha fazla bilgi için bkz. [Batch havuzlarında RDMA özellikli veya GPU özellikli örnekler kullanma](../../batch/batch-pool-compute-intensive-sizes.md). Toplu iş üzerinde kapsayıcı tabanlı iş yüklerini çalıştırmak için [Batch Shipbahçe](https://github.com/Azure/batch-shipyard) projesine de bakın.

* **MICROSOFT HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) , RDMA özellikli Windows VM 'lerinde dağıtıldığında Azure RDMA ağını kullanan MS-MPI için bir çalışma zamanı ortamı içerir. Örneğin, dağıtımlar için bkz. [MPI uygulamalarını çalıştırmak IÇIN HPC Pack Ile WINDOWS RDMA kümesi ayarlama](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Diğer boyutlar
- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Bellek için iyileştirilmiş](../virtual-machines-windows-sizes-memory.md)
- [Depolama için iyileştirilmiş](../virtual-machines-windows-sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

- Windows Server 'da HPC Pack ile işlem yoğunluğu kullanan örnekleri kullanmak için denetim listeleri için bkz. [MPI uygulamalarını çalıştırmak IÇIN HPC Pack Ile WINDOWS RDMA kümesi ayarlama](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- MPı uygulamalarını Azure Batch ile çalıştırırken yoğun işlem yoğunluklu örnekler kullanmak için bkz. [Azure Batch 'de Ileti geçirme arabirimi (MPı) uygulamalarını çalıştırmak için çok örnekli görevleri kullanma](../../batch/batch-mpi.md).

- Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
