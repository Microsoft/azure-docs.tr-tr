---
title: Azure VM boyutları-GPU | Microsoft Docs
description: Azure 'daki sanal makineler için kullanılabilen farklı GPU iyileştirilmiş boyutları listeler. Bu serideki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 09d62ea5f2db77c14e8faff44de7fb3ce759c6fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709744"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU için iyileştirilmiş sanal makine boyutları

GPU en iyileştirilmiş VM boyutları, tek, birden çok veya kısmi GPU 'Lar ile sunulan özel sanal makinelerdir. Bu boyutlar, işlem yoğunluğu, grafik yoğun ve görselleştirme iş yükleri için tasarlanmıştır. Bu makalede GPU 'Lar, vCPU 'Lar, veri diskleri ve NIC 'lerin sayısı ve türleri hakkında bilgi sağlanır. Bu gruplandırmadaki her boyut için depolama verimlilik ve ağ bant genişliği de mevcuttur.

- [NCv3-Series](ncv3-series.md) ve [NC T4_v3 serisi](nct4-v3-series.md) BOYUTLARı, işlem yoğunluklu GPU hızlandırmalı uygulamalar için iyileştirilmiştir. Bazı örnekler CUDA ve OpenCL tabanlı uygulamalar ve benzetimler, AI ve derin öğrenime sahiptir. NC T4 v3 serisi, NVıDıA 'nin Tesla T4 GPU 'SU ve AMD EPYC2 Roma işlemcisi özelliklerine sahip çıkarım iş yüklerine odaklanılmıştır. NCv3 serisi, NVıDıA Tesla V100 GPU 'SU ile yüksek performanslı bilgi işlem ve AI iş yüklerine odaklanılmıştır.

- [NDv2 serisi](ndv2-series.md) boyutu, genişleme ve genişleme derinlemesine öğrenme eğitimi uygulamalarına odaklanır. NDv2-Series, NVIDIA Volta V100 ve Intel Xeon Platinum 8168 (ufuk Gölü) işlemcisini kullanır.

- [NV serisi](nv-series.md) ve [NVv3 serisi](nvv3-series.md) boyutları, OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun, kodlama ve VDI senaryoları için iyileştirilmiştir ve tasarlanmıştır. Bu VM 'Ler NVıDıA Tesla M60 GPU tarafından desteklenir.

- [NVv4 serisi](nvv4-series.md) VDı ve uzaktan görselleştirme için optimize edilmiş ve tasarlanan VM boyutları. Bölümlenmiş GPU 'Lar ile NVv4, daha küçük GPU kaynakları gerektiren iş yükleri için doğru boyut sunar. Bu VM 'Ler AMD Radeon Instinct MI25 GPU tarafından desteklenir. NVv4 VM 'Leri Şu anda yalnızca Windows Konuk işletim sistemini desteklemektedir.

## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

Azure N serisi VM 'lerin GPU yeteneklerini avantajlarından yararlanmak için NVıDıA veya AMD GPU sürücüleri yüklenmelidir.

- NVıDıA GPU 'Lar tarafından desteklenen VM 'Ler için, [NVıDıA GPU sürücü uzantısı](./extensions/hpccompute-gpu-windows.md) uygun NVIDIA CUDA veya kılavuz sürücülerini yüklemiştir. Azure portal veya Azure PowerShell veya Azure Resource Manager şablonları gibi araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen işletim sistemleri ve dağıtım adımları için [NVıDıA GPU sürücü uzantısı belgelerine](./extensions/hpccompute-gpu-windows.md) bakın. VM uzantıları hakkında genel bilgi için bkz. [Azure sanal makine uzantıları ve özellikleri](./extensions/overview.md).

   Alternatif olarak, NVıDıA GPU sürücülerini el ile de yükleyebilirsiniz. Bkz. [Windows çalıştıran n serisi VM 'LERE NVıDıA GPU sürücülerini yükleme](./windows/n-series-driver-setup.md) veya desteklenen işletim sistemleri, sürücüler, yükleme ve doğrulama adımları için [Linux çalıştıran n SERISI VM 'lere NVIDIA GPU sürücülerini yükleme](./linux/n-series-driver-setup.md) .

- AMD GPU 'Lar tarafından desteklenen VM 'Ler için, [AMD GPU sürücü uzantısı](./extensions/hpccompute-amd-gpu-windows.md) uygun AMD sürücülerini kurar. Azure portal veya Azure PowerShell veya Azure Resource Manager şablonları gibi araçları kullanarak uzantıyı yükler veya yönetir. VM uzantıları hakkında genel bilgi için bkz. [Azure sanal makine uzantıları ve özellikleri](./extensions/overview.md).

   Alternatif olarak, AMD GPU sürücülerini el ile de yükleyebilirsiniz. Desteklenen işletim sistemleri, sürücüler, yükleme ve doğrulama adımları için [Windows çalıştıran N serisi VM 'LERDE AMD GPU sürücülerini yükleme](./windows/n-series-amd-driver-setup.md) bölümüne bakın.

## <a name="deployment-considerations"></a>Dağıtma konuları

- N serisi VM 'lerin kullanılabilirliği için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services/).

- N serisi VM 'Ler yalnızca Kaynak Yöneticisi dağıtım modelinde dağıtılabilir.

- N serisi VM 'Ler, diskleri için destekledikleri Azure Storage türünde farklılık gösterir. NC ve NV VM 'Ler yalnızca standart Disk Depolama (HDD) tarafından desteklenen VM disklerini destekler. Diğer tüm GPU VM 'Leri, Standart Disk Depolama ve Premium Disk Depolama (SSD) tarafından desteklenen VM disklerini destekler.

- Birkaç tane N serisi VM dağıtmak istiyorsanız, Kullandıkça Öde aboneliğine veya diğer satın alma seçeneklerine göz önünde bulundurun. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/) kullanıyorsanız, yalnızca sınırlı sayıda Azure işlem çekirdeği kullanabilirsiniz.

- Azure aboneliğinizdeki çekirdek kotasını (bölge başına) artırmanız ve NC, NCv2, NCv3, ND, NDv2, NV veya NVv2 çekirdekleri için ayrı kotayı artırmanız gerekebilir. Kota artışı istemek için, ücretsiz [bir çevrimiçi müşteri destek isteği açın](../azure-portal/supportability/how-to-create-azure-support-request.md) . Varsayılan sınırlar, abonelik kategorime bağlı olarak değişebilir.

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
