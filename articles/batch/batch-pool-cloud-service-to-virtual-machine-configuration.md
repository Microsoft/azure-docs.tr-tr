---
title: Batch havuzu yapılandırmasını Cloud Services sanal makinelere geçir
description: Havuz yapılandırmanızı en son ve önerilen yapılandırmaya güncelleştirme hakkında bilgi edinin
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200571"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Cloud Services Batch havuzu yapılandırmasını sanal makineye geçir

Şu anda, Batch havuzları [Virtualmachineconfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) ya da [cloudserviceconfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration)kullanılarak oluşturulabilir. Bu yapılandırma tüm Batch yeteneklerini desteklediğinden, yalnızca sanal makine yapılandırmasını kullanmanızı öneririz.

Cloud Services yapılandırma havuzları, geçerli toplu Iş özelliklerinden bazılarını desteklemez ve yeni eklenen özellikleri desteklemez. Yeni ' CloudServiceConfiguration ' havuzları oluşturamaz veya [29 şubat 2024 ' den sonra](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)var olan havuzlara yeni düğümler ekleyemeyeceksiniz.

Batch çözümleriniz Şu anda ' cloudServiceConfiguration ' havuzlarını kullanıyorsa, ' virtualMachineConfiguration ' olarak mümkün olan en kısa sürede değiştirilmesini öneririz. Bu, genişletilmiş [VM Serisi](batch-pool-vm-sizes.md), Linux VM 'ler, [kapsayıcılar](batch-docker-container-workloads.md), [Azure Resource Manager sanal ağlar](batch-virtual-network.md)ve [düğüm disk şifrelemesi](disk-encryption.md)gibi tüm Batch olanaklarından faydalanabilmeniz için size olanak sağlar.

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Sanal makine yapılandırmasını kullanarak havuz oluşturma

' CloudServiceConfiguration ' kullanan mevcut bir etkin havuzu ' virtualMachineConfiguration ' kullanmak için değiştiremezsiniz. Bunun yerine, yeni havuzlar oluşturmanız gerekir. Yeni ' virtualMachineConfiguration ' havuzlarınızı oluşturduktan ve tüm işlerinizi ve görevlerinizi çoğaltdıktan sonra artık kullanmadığınız eski ' Cloudserviceconfiguration' havuzlarını silebilirsiniz.

Tüm Batch API 'Leri, komut satırı araçları, Azure portal ve Batch Explorer kullanıcı arabirimi ' virtualMachineConfiguration ' kullanarak havuzlar oluşturmanıza olanak sağlar.

' VirtualMachineConfiguration ' kullanan havuzlar oluşturma işlemine ilişkin yönergeler için bkz. [.NET öğreticisi](tutorial-parallel-dotnet.md) veya [Python öğreticisi](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Havuz yapılandırma farkları

İki yapılandırma arasındaki bazı önemli farklılıklar şunlardır:

- ' cloudServiceConfiguration ' havuz düğümleri yalnızca Windows işletim sistemini kullanır. ' virtualMachineConfiguration ' havuzları, Linux veya Windows işletim sistemi kullanabilir.
- ' CloudServiceConfiguration ' havuzlarıyla karşılaştırıldığında, ' virtualMachineConfiguration ' havuzlarının kapsayıcı desteği, veri diskleri ve disk şifrelemesi gibi daha zengin bir özellik kümesi vardır.
- Havuz ve düğüm başlatma ve silme süreleri, ' cloudServiceConfiguration ' havuzları ve ' virtualMachineConfiguration ' havuzları arasında biraz farklı olabilir.
- ' virtualMachineConfiguration ' havuz düğümleri yönetilen işletim sistemi disklerini kullanır. Her düğüm için kullanılan [yönetilen disk türü](../virtual-machines/disks-types.md) , havuz IÇIN seçilen VM boyutuna bağlıdır. Havuz için bir ' VM boyutu belirtilmişse (örneğin, ' Standard_D2s_v3 '), Premium SSD kullanılır. '-S ' olmayan bir VM boyutu belirtilmemişse, örneğin ' Standard_D2_v3 ' için standart HDD kullanılır.

   > [!IMPORTANT]
   > Sanal makinelerde ve sanal makine ölçek kümelerinde olduğu gibi, her düğüm için kullanılan işletim sistemi tarafından yönetilen disk, VM maliyetine ek olarak bir maliyet doğurur. Yerel SSD düğümlerinde işletim sistemi diski oluşturulduğundan, ' cloudServiceConfiguration ' düğümleri için işletim sistemi disk maliyeti yoktur.

## <a name="azure-data-factory-custom-activity-pools"></a>Özel etkinlik havuzlarını Azure Data Factory

Azure Batch havuzları, Data Factory özel etkinlikleri çalıştırmak için kullanılabilir. Özel etkinlikleri çalıştırmak için kullanılan ' cloudServiceConfiguration ' havuzlarının silinmesi ve yeni ' virtualMachineConfiguration ' havuzlarının oluşturulması gerekir.

Data Factory özel etkinlikleri çalıştırmak için yeni havuzlarınızı oluştururken aşağıdaki yöntemleri izleyin:

- Yeni havuzları oluşturmadan önce tüm işlem hatlarını duraklatın ve yürütmelerin kesintiye uğratılmayacağını garantilemek için eskileri silin.
- Bağlı hizmet yapılandırma değişikliklerinden kaçınmak için aynı havuz kimliği kullanılabilir.
- Yeni havuzlar oluşturulduğunda işlem hatlarını sürdürür.

Data Factory özel etkinlikleri çalıştırmak için Azure Batch kullanma hakkında daha fazla bilgi için, bkz. [Azure Batch bağlantılı hizmet](../data-factory/compute-linked-services.md#azure-batch-linked-service) ve  [özel etkinlikleri Data Factory işlem hattı içinde](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Havuz yapılandırması](nodes-and-pools.md#configurations)hakkında daha fazla bilgi edinin.
- [Havuz en iyi uygulamaları](best-practices.md#pools)hakkında daha fazla bilgi edinin.
- [Havuz ekleme](/rest/api/batchservice/pool/add) ve [virtualmachineconfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)için REST API başvurusuna bakın.