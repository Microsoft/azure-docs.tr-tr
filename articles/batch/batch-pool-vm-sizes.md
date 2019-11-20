---
title: Havuzlar için VM boyutlarını seçin-Azure Batch | Microsoft Docs
description: Azure Batch havuzlarda işlem düğümleri için kullanılabilir VM boyutları arasından seçim yapma
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: e3d96ad7783c43dba6b69f6b11acccf790d0d6c9
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983744"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Bir Azure Batch havuzundaki işlem düğümleri için VM boyutu seçme

Bir Azure Batch havuzu için bir düğüm boyutu seçtiğinizde, Azure 'da kullanılabilir olan neredeyse tüm VM boyutlarının arasından seçim yapabilirsiniz. Azure, farklı iş yükleri için Linux ve Windows VM 'Leri için bir dizi boyut sunar.

VM boyutu seçmenin bazı özel durumları ve sınırlamaları vardır:

* Batch 'de bazı VM serileri veya VM boyutları desteklenmez.
* Bazı VM boyutları kısıtlıdır ve ayrılabilmeleri için özel olarak etkinleştirilmesi gerekir.

## <a name="supported-vm-series-and-sizes"></a>Desteklenen VM Serisi ve boyutları

### <a name="pools-in-virtual-machine-configuration"></a>Sanal makine yapılandırmasındaki havuzlar

Sanal makine yapılandırmasındaki toplu iş havuzları neredeyse tüm VM boyutlarını destekler ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Desteklenen boyutlar ve kısıtlamalar hakkında daha fazla bilgi edinmek için aşağıdaki tabloya bakın.

Listelenen tüm promosyon veya önizleme VM boyutları destek için garanti edilmez.

| VM serisi  | Desteklenen boyutlar | Batch hesabı havuzu ayırma modu<sup>1</sup> |
|------------|---------|-----------------|
| Temel A serisi | Basic_A0 *hariç* tüm boyutlar (a0) | Any |
| A Serisi | Standard_A0 *hariç* tüm boyutlar | Any |
| Av2 Serisi | Tüm Boyutlar | Any |
| B serisi | Yok. | Yok |
| DC serisi | Yok. | Yok |
| Dv2, DSv2 serisi | Tüm Boyutlar | Any |
| Dv3, Dsv3 serisi | Tüm Boyutlar | Any |
| Ev3, Esv3 serisi | Tüm Boyutlar | Any |
| Fsv2-serisi | Tüm Boyutlar | Any |
| H Serisi | Tüm Boyutlar | Any |
| HB Serisi<sup>2</sup> | Tüm Boyutlar | Any |
| HC Serisi<sup>2</sup> | Tüm Boyutlar | Any |
| Ls serisi | Tüm Boyutlar | Any |
| Lsv2 serisi | Yok. | Yok |
| M serisi | Standard_M64ms (yalnızca düşük öncelikli), Standard_M128s (yalnızca düşük öncelikli) | Any |
| Mv2 serisi | Yok. | Yok |
| NC serisi | Tüm Boyutlar | Any |
| NCv2-serisi<sup>2</sup> | Tüm Boyutlar | Any |
| NCv3-serisi<sup>2</sup> | Tüm Boyutlar | Any |
| ND serisi<sup>2</sup> | Tüm Boyutlar | Any |
| NDv2 serisi | Tüm Boyutlar | Kullanıcı aboneliği modu |
| NV serisi | Tüm Boyutlar | Any |
| NVv3 serisi | Yok. | Yok |
| SAP HANA | Yok. | Yok |

<sup>1</sup> bazı yeni VM serileri başlangıçta kısmen desteklenir. Bu VM Serisi, **havuz ayırma modu** **Kullanıcı aboneliğine**ayarlanmış olan Batch hesapları tarafından tahsis edilebilir. Batch hesabı yapılandırması hakkında daha fazla bilgi için bkz. [Batch hesaplarını yönetme](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) . **Kullanıcı aboneliği** Batch hesapları için kısmen desteklenen bu VM serisine nasıl kota isteneceğini öğrenmek için [Kotalar ve sınırlar](batch-quota-limit.md) bölümüne bakın.  

<sup>2</sup> bu VM boyutları, sanal makine yapılandırmasındaki Batch havuzlarında tahsis edilebilir, ancak belirli bir [Kota artışı](batch-quota-limit.md#increase-a-quota)istemeniz gerekir.

### <a name="pools-in-cloud-service-configuration"></a>Bulut hizmeti yapılandırmasındaki havuzlar

Bulut hizmeti yapılandırmasındaki toplu iş havuzları aşağıdakiler **dışında** [Cloud Services için tüm VM boyutlarını](../cloud-services/cloud-services-sizes-specs.md) destekler:

| VM serisi  | Desteklenmeyen Boyutlar |
|------------|-------------------|
| A Serisi   | Çok küçük       |
| Av2 Serisi | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Boyut konuları

* **Uygulama gereksinimleri** -düğümlerde çalıştıracağınız uygulamanın özelliklerini ve gereksinimlerini göz önünde bulundurun. Uygulamanın çok iş parçacıklı olup olmadığı ve ne kadar bellek kullandığı gibi konular en uygun ve ekonomik düğüm boyutunu belirlemeye yardımcı olabilir. Çok örnekli [MPI iş yükleri](batch-mpi.md) veya CUDA uygulamaları için sırasıyla özel [HPC](../virtual-machines/linux/sizes-hpc.md) veya [GPU özellikli](../virtual-machines/linux/sizes-gpu.md) VM boyutlarını göz önünde bulundurun. (Bkz. [Batch havuzlarında RDMA özellikli veya GPU özellikli örnekler kullanma](batch-pool-compute-intensive-sizes.md).)

* **Düğüm başına görevler** -tek seferde bir düğümde bir görevin çalıştığını varsayarak düğüm boyutu seçmek tipik bir davranıştır. Ancak, iş yürütme sırasında birden fazla görevin (ve dolayısıyla birden çok uygulama örneğinin) işlem düğümlerinde [paralel olarak çalıştırılması](batch-parallel-node-tasks.md) avantajlı olabilir. Bu durumda, paralel görev yürütmesinin arttığı talebe uyum sağlamak için çok düğümlü bir düğüm boyutu seçmek yaygındır.

* **Farklı görevler Için yük düzeyleri** -bir havuzdaki tüm düğümler aynı boyutta. Farklı sistem gereksinimlerine ve/veya yük düzeylerine sahip uygulamalar çalıştırmayı planlıyorsanız ayrı havuzlar oluşturmanız önerilir.

* **Bölge kullanılabilirliği** -Batch hesaplarınızı oluşturduğunuz bölgelerde bir VM serisi veya boyutu kullanılamayabilir. Bir boyutun kullanılabilir olduğunu denetlemek için bkz. [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services/).

* **Kotalar** -Batch hesabınızdaki [çekirdekler kotaları](batch-quota-limit.md#resource-quotas) , bir Batch havuzuna ekleyebileceğiniz belirli boyuttaki düğümlerin sayısını sınırlayabilir. Kota artışı istemek için [Bu makaleye](batch-quota-limit.md#increase-a-quota)bakın. 

* **Havuz yapılandırması** -genel olarak, sanal makine yapılandırmasında, bulut hizmeti yapılandırmasıyla karşılaştırıldığında bir havuz oluşturduğunuzda daha fazla VM boyutu seçeneğiniz vardır.

## <a name="next-steps"></a>Sonraki adımlar

* Toplu Işe yönelik ayrıntılı genel bakış için bkz. [Batch ile büyük ölçekli paralel işlem çözümleri geliştirme](batch-api-basics.md).
* İşlem yoğunluklu VM boyutlarını kullanma hakkında daha fazla bilgi için bkz. [Batch havuzlarında RDMA özellikli veya GPU özellikli örnekler kullanma](batch-pool-compute-intensive-sizes.md).
