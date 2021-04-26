---
title: Havuzlar için VM boyutlarını ve görüntülerini seçin
description: Azure Batch havuzlarda işlem düğümleri için kullanılabilir VM boyutları ve işletim sistemi sürümleri arasından seçim yapma
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 2c3b90d6188dc6660233ae659fb4280dc1d4f2a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027389"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Bir Azure Batch havuzundaki işlem düğümleri için VM boyutu ve görüntüsü seçme

Bir Azure Batch havuzu için bir düğüm boyutu seçtiğinizde, Azure 'da kullanılabilir olan neredeyse tüm VM boyutlarının arasından seçim yapabilirsiniz. Azure, farklı iş yükleri için Linux ve Windows VM 'Leri için bir dizi boyut sunar.

## <a name="supported-vm-series-and-sizes"></a>Desteklenen VM Serisi ve boyutları

### <a name="pools-in-virtual-machine-configuration"></a>Sanal makine yapılandırmasındaki havuzlar

Sanal makine yapılandırmasındaki toplu iş havuzları neredeyse tüm [VM boyutlarını](../virtual-machines/sizes.md)destekler. Desteklenen boyutlar ve kısıtlamalar hakkında daha fazla bilgi edinmek için aşağıdaki tabloya bakın.

| VM serisi  | Desteklenen boyutlar |
|------------|---------|
| Temel A | Basic_A0 *dışındaki* tüm boyutlar (a0) |
| A | Standard_A0, Standard_A8, Standard_A9, Standard_A10 ve Standard_A11 *hariç* tüm boyutlar |
| AV2 | Tüm Boyutlar |
| B | Desteklenmez |
| DCsv2 | Tüm Boyutlar |
| Dv2, DSv2 | Tüm Boyutlar |
| Dv3, Dsv3 | Tüm Boyutlar |
| Dav4, Dasv4 | Tüm Boyutlar |
| Ddv4, Ddsv4 |  Tüm Boyutlar |
| Dv4, Dsv4 | Desteklenmez |
| Ev3, Esv3 | E64is_v3 dışındaki tüm boyutlar |
| Eav4, Easv4 | Tüm Boyutlar |
| Edv4, Edsv4 |  Tüm Boyutlar |
| Ev4, Esv4 | Desteklenmez |
| F, FS | Tüm Boyutlar |
| Fsv2 | Tüm Boyutlar |
| G, GS | Tüm Boyutlar |
| H | Tüm Boyutlar |
| HB | Tüm Boyutlar |
| HBv2 | Tüm Boyutlar |
| HBv3 | Standard_HB120rs_v3 (henüz kullanılamayan diğer boyutlar) |
| HC | Tüm Boyutlar |
| Ls | Tüm Boyutlar |
| Lsv2 | Tüm Boyutlar |
| M | Tüm Boyutlar |
| Mv2<sup>1</sup> | Tüm Boyutlar |
| NC | Tüm Boyutlar |
| NCv2 | Tüm Boyutlar |
| NCv3 | Tüm Boyutlar |
| NCasT4_v3 | Tüm Boyutlar |
| ND | Tüm Boyutlar |
| NDv2 | Hiçbiri-henüz kullanılamıyor |
| NV | Tüm Boyutlar |
| NVv3 | Tüm Boyutlar |
| NVv4 | Tüm Boyutlar |
| SAP HANA | Desteklenmez |

<sup>1</sup> bu VM Serisi yalnızca 2. nesil VM görüntüleriyle kullanılabilir.

### <a name="using-generation-2-vm-images"></a>2. nesil VM görüntülerini kullanma

[Mv2](../virtual-machines/mv2-series.md)gıbı bazı VM serileri yalnızca [2. nesil VM görüntüleriyle](../virtual-machines/generation-2.md)kullanılabilir. 2. nesil VM görüntüleri, [' ImageReference '](/rest/api/batchservice/pool/add#imagereference) yapılandırmasının ' SKU ' özelliği kullanılarak HERHANGI bir VM görüntüsü gibi belirtilir; ' SKU ' dizelerinin "-G2" veya "-Gen2" gibi bir soneki vardır. 2. nesil görüntüler dahil toplu Işlem tarafından desteklenen VM görüntülerinin listesini almak için [' desteklenen görüntüleri Listele '](/rest/api/batchservice/account/listsupportedimages) API, [POWERSHELL](/powershell/module/az.batch/get-azbatchsupportedimage)veya [Azure CLI](/cli/azure/batch/pool/supported-images)kullanın.

### <a name="pools-in-cloud-service-configuration"></a>Bulut hizmeti yapılandırmasındaki havuzlar

Bulut hizmeti yapılandırmasındaki toplu iş havuzları aşağıdakiler **dışında** [Cloud Services için tüm VM boyutlarını](../cloud-services/cloud-services-sizes-specs.md) destekler:

| VM serisi  | Desteklenmeyen Boyutlar |
|------------|-------------------|
| A Serisi   | Çok küçük       |
| Av2 Serisi | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Boyutla ilgili dikkat edilmesi gerekenler

- **Uygulama gereksinimleri** -düğümlerde çalıştıracağınız uygulamanın özelliklerini ve gereksinimlerini göz önünde bulundurun. Uygulamanın çok iş parçacıklı olup olmadığı ve ne kadar bellek kullandığı gibi konular en uygun ve ekonomik düğüm boyutunu belirlemeye yardımcı olabilir. Çok örnekli [MPI iş yükleri](batch-mpi.md) veya CUDA uygulamaları için sırasıyla özel [HPC](../virtual-machines/sizes-hpc.md) veya [GPU özellikli](../virtual-machines/sizes-gpu.md) VM boyutlarını göz önünde bulundurun. Daha fazla bilgi için bkz. [Batch havuzlarında RDMA özellikli veya GPU özellikli örnekler kullanma](batch-pool-compute-intensive-sizes.md).

- **Düğüm başına görevler** -tek seferde bir düğümde bir görevin çalıştığını varsayarak düğüm boyutu seçmek tipik bir davranıştır. Ancak, iş yürütme sırasında birden fazla görevin (ve dolayısıyla birden çok uygulama örneğinin) işlem düğümlerinde [paralel olarak çalıştırılması](batch-parallel-node-tasks.md) avantajlı olabilir. Bu durumda, paralel görev yürütmesinin arttığı talebe uyum sağlamak için çok düğümlü bir düğüm boyutu seçmek yaygındır.

- **Farklı görevler Için yük düzeyleri** -bir havuzdaki tüm düğümler aynı boyutta. Farklı sistem gereksinimlerine ve/veya yük düzeylerine sahip uygulamalar çalıştırmayı planlıyorsanız ayrı havuzlar oluşturmanız önerilir.

- **Bölge kullanılabilirliği** -Batch hesaplarınızı oluşturduğunuz bölgelerde bir VM serisi veya boyutu kullanılamayabilir. Bir boyutun kullanılabilir olduğunu denetlemek için bkz. [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services/).

- **Kotalar** -Batch hesabınızdaki [çekirdekler kotaları](batch-quota-limit.md#resource-quotas) , bir Batch havuzuna ekleyebileceğiniz belirli boyuttaki düğümlerin sayısını sınırlayabilir. Gerektiğinde, [bir kota artışı isteyebilirsiniz](batch-quota-limit.md#increase-a-quota).

- **Havuz yapılandırması** -genel olarak, sanal makine yapılandırmasında, bulut hizmeti yapılandırmasıyla karşılaştırıldığında bir havuz oluşturduğunuzda daha fazla VM boyutu seçeneğiniz vardır.

## <a name="supported-vm-images"></a>Desteklenen VM görüntüleri

Her bir görüntü için düğüm Aracısı SKU kimlikleri dahil olmak üzere Batch tarafından şu anda desteklenen Windows ve Linux VM görüntülerinin listesini döndürmek için aşağıdaki API 'lerden birini kullanın:

- Batch hizmeti REST API: [desteklenen görüntüleri listeleme](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLı: [az Batch Pool destekleniyor-Images](/cli/azure/batch/pool/supported-images)

Toplu Iş desteği ömrü sonu (EOL) tarihleri olan görüntülerden kaçınmamak kesinlikle önerilir. Bu tarihler [ `ListSupportedImages` API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), [PowerShell](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)veya [Azure CLI](https://docs.microsoft.com/cli/azure/batch/pool/supported-images)aracılığıyla bulunabilir. Batch havuzu VM görüntüsü seçimi hakkında daha fazla bilgi için lütfen [toplu işlem en iyi yöntemler kılavuzuna](best-practices.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- İşlem yoğunluklu VM boyutlarını kullanma hakkında daha fazla bilgi için bkz. [Batch havuzlarında RDMA özellikli veya GPU özellikli örnekler kullanma](batch-pool-compute-intensive-sizes.md).
