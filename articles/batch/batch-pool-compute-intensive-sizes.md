---
title: Batch ile yoğun işlem kullanan Azure VM 'Leri kullanma | Microsoft Docs
description: Azure Batch havuzlarındaki HPC ve GPU VM boyutlarının avantajlarından yararlanın
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: c8fa96e41b98cfa227fd25dc4b3bd66a171ff3c8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350122"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Batch havuzlarında RDMA veya GPU örnekleri kullanma

Belirli toplu işleri çalıştırmak için, büyük ölçekli hesaplama için tasarlanan Azure VM boyutlarının avantajlarından yararlanabilirsiniz. Örneğin:

* Çoklu örnek [MPI iş yüklerini](batch-mpi.md)çalıştırmak Için, H serisi veya uzak doğrudan bellek ERIŞIMI (RDMA) için bir ağ arabirimine sahip diğer boyutları seçin. Bu boyutlar, DEVI uygulamalarını hızlandırabilen, düğümler arası iletişim için bir InfiniBand ağına bağlanır. 

* CUDA uygulamaları için, NVıDıA Tesla grafik işleme birimi (GPU) kartlarını içeren N serisi boyutları seçin.

Bu makalede, Azure 'un Batch havuzlarında belirli boyutlardaki bir kısmını kullanmak için rehberlik ve örnekler sağlanmaktadır. Özellikler ve arka plan için bkz.:

* Yüksek performanslı işlem VM boyutları ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU etkin VM boyutları ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Belirli VM boyutları, Batch hesaplarınızı oluşturduğunuz bölgelerde kullanılamayabilir. Bir boyutun kullanılabilir olup olmadığını denetlemek için [bölgeye göre kullanılabilir ürünlere](https://azure.microsoft.com/regions/services/) bakın ve [bir Batch havuzu için bir VM boyutu seçin](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Bağımlılıkları

Batch 'de yoğun işlem yoğunluğu bulunan boyutlardaki RDMA veya GPU Özellikleri yalnızca belirli işletim sistemlerinde desteklenir. (Desteklenen işletim sistemlerinin listesi, bu boyutlarda oluşturulan sanal makineler için desteklenen bir alt kümesidir.) Batch havuzunu oluşturma yönteminize bağlı olarak, düğümlere ek sürücü veya başka bir yazılım yüklemeniz veya yapılandırmanız gerekebilir. Aşağıdaki tablolarda bu bağımlılıklar özetlenmektedir. Ayrıntılar için bkz. bağlantılı makaleler. Toplu Iş havuzlarını yapılandırma seçenekleri için, bu makalede daha sonra bölümüne bakın.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux havuzları-sanal makine yapılandırması

| Size | Özellik | İşletim sistemleri | Gerekli yazılım | Havuz ayarları |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | ALINMA | Ubuntu 16,04 LTS veya<br/>CentOS tabanlı HPC<br/>(Azure Marketi) | Intel MPı 5<br/><br/>Linux RDMA sürücüleri | Düğüm içi iletişimi etkinleştir, eşzamanlı görev yürütmeyi devre dışı bırak |
| [NC, NCv2, NCv3, NDv2 serisi](../virtual-machines/linux/n-series-driver-setup.md) | NVıDıA Tesla GPU (seriye göre değişir) | Ubuntu 16,04 LTS veya<br/>CentOS 7,3 veya 7,4<br/>(Azure Marketi) | NVıDıA CUDA veya CUDA araç seti sürücüleri | Yok | 
| [NV, NVv2 serisi](../virtual-machines/linux/n-series-driver-setup.md) | NVıDıA Tesla M60 GPU | Ubuntu 16,04 LTS veya<br/>CentOS 7,3<br/>(Azure Marketi) | NVıDıA KıLAVUZ sürücüleri | Yok |

<sup>*</sup>RDMA özellikli N serisi boyutlar da NVıDıA Tesla GPU 'ları içerir

### <a name="windows-pools---virtual-machine-configuration"></a>Windows havuzları-sanal makine yapılandırması

| Size | Özellik | İşletim sistemleri | Gerekli yazılım | Havuz ayarları |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | ALINMA | Windows Server 2016, 2012 R2 veya<br/>2012 (Azure Marketi) | Microsoft MPı 2012 R2 veya üzeri ya da<br/> Intel MPı 5<br/><br/>Windows RDMA sürücüleri | Düğüm içi iletişimi etkinleştir, eşzamanlı görev yürütmeyi devre dışı bırak |
| [NC, NCv2, NCv3, ND, NDv2 serisi](../virtual-machines/windows/n-series-driver-setup.md) | NVıDıA Tesla GPU (seriye göre değişir) | Windows Server 2016 veya <br/>2012 R2 (Azure Marketi) | NVıDıA CUDA veya CUDA araç seti sürücüleri| Yok | 
| [NV, NVv2 serisi](../virtual-machines/windows/n-series-driver-setup.md) | NVıDıA Tesla M60 GPU | Windows Server 2016 veya<br/>2012 R2 (Azure Marketi) | NVıDıA KıLAVUZ sürücüleri | Yok |

<sup>*</sup>RDMA özellikli N serisi boyutlar da NVıDıA Tesla GPU 'ları içerir

### <a name="windows-pools---cloud-services-configuration"></a>Windows havuzları-Bulut Hizmetleri Yapılandırması

> [!NOTE]
> Bulut hizmeti yapılandırması olan Batch havuzlarında N serisi boyutlar desteklenmez.
>

| Size | Özellik | İşletim sistemleri | Gerekli yazılım | Havuz ayarları |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | ALINMA | Windows Server 2016, 2012 R2, 2012 veya<br/>2008 R2 (konuk işletim sistemi ailesi) | Microsoft MPı 2012 R2 veya üzeri ya da<br/>Intel MPı 5<br/><br/>Windows RDMA sürücüleri | Düğüm içi iletişimi etkinleştir,<br/> eşzamanlı görev yürütmeyi devre dışı bırak |

## <a name="pool-configuration-options"></a>Havuz yapılandırma seçenekleri

Batch havuzunuzun özelleştirilmiş bir VM boyutunu yapılandırmak için gerekli yazılım veya sürücüleri yüklemek için çeşitli seçenekleriniz vardır:

* Sanal makine yapılandırmasındaki havuzlar için, sürücüler ve yazılım önceden yüklenmiş olarak önceden yapılandırılmış bir [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/) VM görüntüsü seçin. Örnekler: 

  * [CentOS tabanlı 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) -RDMA sürücülerini ve Intel mpı 5,1 'yi içerir

  * Linux veya Windows için [veri bilimi sanal makinesi](../machine-learning/data-science-virtual-machine/overview.md) -NVıDıA CUDA sürücülerini içerir

  * GPU ve RDMA sürücülerini de içeren toplu Iş kapsayıcısı iş yükleri için Linux görüntüleri:

    * [Azure Batch kapsayıcı havuzları için CentOS (GPU ve RDMA sürücülerle)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Azure Batch kapsayıcı havuzları için Ubuntu Server (GPU ve RDMA sürücüleriyle birlikte)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Üzerinde sürücü, yazılım veya VM boyutu için gereken diğer ayarları yüklediğiniz [özel bir Windows veya LINUX VM görüntüsü](batch-sig-images.md) oluşturun. 

* Daraltılmış bir sürücüden veya uygulama yükleyicisinden bir toplu Işlem [uygulama paketi](batch-application-packages.md) oluşturun ve toplu işi, paketi havuz düğümlerine dağıtmak ve her düğüm oluşturulduğunda yüklemek üzere yapılandırın. Örneğin, uygulama paketi bir yükleyicidir, uygulamayı tüm havuz düğümlerine sessizce yüklemek için bir [Başlangıç görevi](batch-api-basics.md#start-task) komut satırı oluşturun. İş yükünüz belirli bir sürücü sürümüne bağımlıysa bir uygulama paketi ve havuz başlangıç görevi kullanmayı düşünün.

  > [!NOTE] 
  > Başlangıç görevinin yükseltilmiş (yönetici) izinlerle çalışması gerekir ve bunun başarıyı beklemesi gerekir. Uzun süre çalışan görevler, bir Batch havuzu sağlama süresini arttırır.
  >

* [Batch Shipyarda](https://github.com/Azure/batch-shipyard) , GPU ve RDMA sürücülerini otomatik olarak, Azure Batch Kapsayıcılı iş yükleri ile saydam şekilde çalışacak şekilde yapılandırır. Batch Shipbahçe, yapılandırma dosyalarıyla tamamen çalıştırılır. N serisi VM 'lerde GPU sürücülerini önceden yapılandıran ve Microsoft Cognitive Toolkit yazılımlarını bir Docker görüntüsü olarak yükleyen [Cntk GPU yemek tarifi](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) gibi GPU ve RDMA iş yüklerini etkinleştiren birçok örnek tarif yapılandırması vardır.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Örnek: Windows NC VM havuzundaki NVıDıA GPU sürücüleri

CUDA uygulamalarını bir Windows NC düğümü havuzunda çalıştırmak için NVDIA GPU sürücüleri yüklemeniz gerekir. Aşağıdaki örnek adımlarda, NVıDıA GPU sürücülerini yüklemek için bir uygulama paketi kullanılır. İş yükünüz belirli bir GPU sürücü sürümüne bağımlıysa, bu seçeneği belirleyebilirsiniz.

1. [NVIDIA Web sitesinden](https://www.nvidia.com/Download/index.aspx) (örneğin, [Sürüm 411,82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe)) Windows Server 2016 ' de GPU sürücüleri için bir kurulum paketi indirin. *Gpudriversetup. exe*gibi kısa bir ad kullanarak dosyayı yerel olarak kaydedin.
2. Paketin bir ZIP dosyasını oluşturun.
3. Paketi Batch hesabınıza yükleyin. Adımlar için bkz. [uygulama paketleri](batch-application-packages.md) Kılavuzu. *Gpudriver*gibi bir uygulama kimliği ve *411,82*gibi bir sürüm belirtin.
1. Batch API 'Lerini veya Azure portal kullanarak, sanal makine yapılandırmasında istenen sayıda düğüm ve ölçeğe sahip bir havuz oluşturun. Aşağıdaki tabloda, bir başlangıç görevi kullanarak NVıDıA GPU sürücülerini sessizce yüklemeye yönelik örnek ayarlar gösterilmektedir:

| Ayar | Value |
| ---- | ----- | 
| **Görüntü Türü** | Market (Linux/Windows) |
| **Yayımcı** | MicrosoftWindowsServer |
| **Teklif** | WindowsServer |
| **Sku** | 2016-veri merkezi |
| **Düğüm boyutu** | NC6 standart |
| **Uygulama paketi başvuruları** | GPUDriver, sürüm 411,82 |
| **Başlangıç görevi etkin** | Doğru<br>**Komut satırı** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Kullanıcı kimliği** -havuz oto Kullanıcı, yönetici<br/>**Başarılı bekleme** -doğru

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Örnek: Linux NC VM havuzunda NVıDıA GPU sürücüleri

CUDA uygulamalarını bir Linux NC düğümü havuzunda çalıştırmak için, CUDA araç setinde gerekli NVıDıA Tesla GPU sürücülerini yüklemeniz gerekir. Aşağıdaki örnek adımlarda, GPU sürücüleriyle özel bir Ubuntu 16,04 LTS görüntüsü oluşturulur ve dağıtılır:

1. Ubuntu 16,04 LTS çalıştıran bir Azure NC serisi VM dağıtın. Örneğin, ABD Orta Güney bölgesinde sanal makineyi oluşturun. 
2. [Azure Portal, Azure aboneliğine bağlanan bir](../virtual-machines/extensions/hpccompute-gpu-linux.md
) istemci bilgisayarı veya Azure Cloud Shell kullanarak NVIDIAGPUsürücüleriuzantısınısanalmakineyeekleyin. Alternatif olarak, sanal makineye bağlanma ve [CUDA sürücülerini](../virtual-machines/linux/n-series-driver-setup.md) el ile yüklemek için adımları izleyin.
3. Toplu Iş için [paylaşılan bir görüntü Galerisi görüntüsü](batch-sig-images.md) oluşturma adımlarını izleyin.
4. NC VM 'Leri destekleyen bir bölgede Batch hesabı oluşturun.
5. Batch API 'Lerini veya Azure portal kullanarak, [özel görüntüyü kullanarak](batch-sig-images.md) ve istenen sayıda düğüm ve ölçeğe sahip bir havuz oluşturun. Aşağıdaki tabloda görüntünün örnek havuz ayarları gösterilmektedir:

| Ayar | Value |
| ---- | ---- |
| **Görüntü Türü** | Özel Görüntü |
| **Özel görüntü** | *Görüntünün adı* |
| **Düğüm Aracısı SKU 'SU** | Batch. Node. Ubuntu 16,04 |
| **Düğüm boyutu** | NC6 standart |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Örnek: Windows H16r VM havuzunda Microsoft MPı

Windows MPı uygulamalarını bir Azure H16r VM düğümü havuzunda çalıştırmak için, HpcVmDrivers uzantısını yapılandırmanız ve [MICROSOFT MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi)yüklemeniz gerekir. Gerekli sürücüler ve yazılımlarla özel bir Windows Server 2016 görüntüsünü dağıtmak için örnek adımlar aşağıda verilmiştir:

1. Windows Server 2016 çalıştıran bir Azure H16r VM dağıtın. Örneğin, ABD Batı bölgesinde sanal makineyi oluşturun. 
2. hpcvmdrivers uzantısını sanal makineye ekleyin [Azure aboneliğinize bağlanan bir istemci bilgisayarından bir Azure PowerShell komutu ](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) çalıştırarak veya Azure Cloud Shell kullanarak, 
1. SANAL makineye Uzak Masaüstü bağlantısı oluşturun.
1. Microsoft MPı 'nin en son sürümü için [Kurulum paketini](https://www.microsoft.com/download/details.aspx?id=57467) (msmpisetup. exe) Indirin ve Microsoft MPI 'yi yükleyin.
1. Toplu Iş için [paylaşılan bir görüntü Galerisi görüntüsü](batch-sig-images.md) oluşturma adımlarını izleyin.
1. Batch API 'Lerini veya Azure portal kullanarak, [paylaşılan görüntü galerisini kullanarak](batch-sig-images.md) ve istenen sayıda düğüm ve ölçeğe sahip bir havuz oluşturun. Aşağıdaki tabloda görüntünün örnek havuz ayarları gösterilmektedir:

| Ayar | Value |
| ---- | ---- |
| **Görüntü Türü** | Özel Görüntü |
| **Özel görüntü** | *Görüntünün adı* |
| **Düğüm Aracısı SKU 'SU** | Batch. Node. Windows AMD64 |
| **Düğüm boyutu** | H16r standart |
| **Düğümler arası iletişim etkin** | Doğru |
| **Düğüm başına en fazla görev** | 1\. |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Örnek: Linux H16r VM havuzunda Intel MPı

MPı uygulamalarını bir Linux H serisi düğümleri havuzunda çalıştırmak için, tek bir seçenek Azure Marketi 'ndeki [CentOS tabanlı 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) görüntüsünü kullanmaktır. Linux RDMA sürücüleri ve Intel MPı önceden yüklenir. Bu görüntü, Docker kapsayıcı iş yüklerini de destekler.

Batch API 'Lerini veya Azure portal kullanarak, bu görüntüyü kullanarak ve istenen sayıda düğüm ve ölçeğe sahip bir havuz oluşturun. Aşağıdaki tabloda örnek havuz ayarları gösterilmektedir:

| Ayar | Value |
| ---- | ---- |
| **Görüntü Türü** | Market (Linux/Windows) |
| **Yayımcı** | OpenLogic |
| **Teklif** | CentOS-HPC |
| **Sku** | 7.4 |
| **Düğüm boyutu** | H16r standart |
| **Düğümler arası iletişim etkin** | Doğru |
| **Düğüm başına en fazla görev** | 1\. |

## <a name="next-steps"></a>Sonraki adımlar

* MPı işlerini bir Azure Batch havuzunda çalıştırmak için [Windows](batch-mpi.md) veya [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) örneklerine bakın.

* Toplu Iş üzerinde GPU iş yükleri örnekleri için bkz. [Batch Shipbahçe](https://github.com/Azure/batch-shipyard/) tariflerine bakın.