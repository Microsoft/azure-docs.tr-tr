---
title: Azure Machine Learning veri başvurusunu izleme | Microsoft Docs
description: İzleme Azure Machine Learning için başvuru belgeleri. Azure Izleyici 'de toplanan ve sunulan veri & kaynakları hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031078"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Azure Machine Learning veri başvurusunu izleme

Azure Machine Learning çalışma alanınızdan Azure Izleyici tarafından toplanan veriler ve kaynaklar hakkında bilgi edinin. İzleme verilerini toplama ve çözümleme hakkında ayrıntılar için bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Ölçümler

Bu bölümde, Azure Machine Learning için toplanan otomatik olarak toplanan tüm platform ölçümleri listelenmektedir. Bu ölçümler için kaynak sağlayıcısı, [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modelleme**

| Metric | Birim | Description |
|--|--|--|
| Model kaydı başarılı oldu | Count | Bu çalışma alanında başarılı olan model kaydı sayısı |
| Model kaydı başarısız oldu | Count | Bu çalışma alanında başarısız olan model kayıtlarının sayısı |
| Model Dağıtımı başladı | Count | Bu çalışma alanında başlatılan model dağıtımı sayısı |
| Model Dağıtımı başarılı oldu | Count | Bu çalışma alanındaki başarılı olan model dağıtımı sayısı |
| Model Dağıtımı başarısız oldu | Count | Bu çalışma alanında başarısız olan model dağıtımı sayısı |

**Kota**

Kota bilgileri yalnızca Azure Machine Learning işlem amaçlıdır.

| Metric | Birim | Description |
|--|--|--|
| Toplam düğüm sayısı | Count | Toplam düğüm sayısı. Bu toplam etkin düğümlerin, boşta düğümlerin, kullanılamayan düğümlerin, Önkullanılmayan düğümlerin, düğüm dışı düğümlerin bazılarını içerir |
| Etkin düğümler | Count | Etkin düğümlerin sayısı. Etkin bir işi çalıştıran düğümler. |
| Boştaki düğümler | Count | Boştaki düğüm sayısı. Boştaki düğümler, herhangi bir işi çalıştırmayan ancak varsa yeni işi kabul edebilecek düğümlerdir. |
| Kullanılamayan düğümler | Count | Kullanılamayan düğüm sayısı. Çözümlenebilen bazı sorunlar nedeniyle kullanılamaz düğümler çalışmıyor. Azure bu düğümleri geri dönüştürecek. |
| Önden düğümler | Count | Yok edilecek düğüm sayısı. Bu düğümler, kullanılabilir düğüm havuzundan alınan düşük öncelikli düğümlerdir. |
| Düğümlerden çıkılıyor | Count | Düğüm sayısı. Düğüm ayrıldığınızda, bir işi işlemeyi tamamlamış olan düğümlerdir ve boşta durumuna gidecektir. |
| Toplam çekirdek sayısı | Count | Toplam çekirdek sayısı |
| Etkin çekirdekler | Count | Etkin çekirdek sayısı |
| Boştaki çekirdekler | Count | Boşta çekirdek sayısı |
| Kullanılamayan çekirdekler | Count | Kullanılamayan çekirdek sayısı |
| Önden çekirdekler | Count | Yok edilecek çekirdek sayısı |
| Çekirdekleri bırakma | Count | Bırakma çekirdekleri sayısı |
| Kota kullanım yüzdesi | Count | Kullanılan Kota yüzdesi |

**Kaynak**

| Metric| Birim | Description |
|--|--|--|
| Cpukullanımı | Count | Bir CPU düğümündeki kullanım yüzdesi. Kullanım, tek dakikalık aralıklarla raporlanır. |
| Gpukullanım | Count | Bir GPU düğümündeki kullanım yüzdesi. Kullanım, tek dakikalık aralıklarla raporlanır. |
| Gpumemoryutilileştirme | Count | Bir GPU düğümündeki bellek kullanımının yüzdesi. Kullanım, tek dakikalık aralıklarla raporlanır. |
| Gpuenerjcayjoules | Count | Bir GPU düğümündeki Joules 'de Aralık enerji. Enerji, tek dakikalık aralıklarla raporlanır. |

**Çalıştır**

Çalışma alanı için eğitim çalıştırmaları hakkında bilgiler.

| Metric | Birim | Description |
|--|--|--|
| İptal edilen çalıştırmalar | Count | Bu çalışma alanı için iptal edilen çalıştırma sayısı. Bir çalıştırma başarıyla iptal edildiğinde sayı güncelleştirilir. |
| Istenen çalıştırmaları iptal et | Count | Bu çalışma alanı için iptal edilen çalıştırma sayısı. Bir çalıştırma için iptal isteği alındığında sayı güncelleştirilir. |
| Tamamlanan çalıştırmalar | Count | Bu çalışma alanı için çalıştırma sayısı başarıyla tamamlandı. Bir çalıştırma tamamlandığında ve çıkış toplandığında sayı güncelleştirilir. |
| Başarısız çalıştırmalar | Count | Bu çalışma alanı için başarısız olan çalıştırma sayısı. Bir çalıştırma başarısız olduğunda sayı güncelleştirilir. |
| Çalıştırmalar sonlandırılıyor | Count | Bu çalışma alanı için sonlandırılıyor durum girilen çalıştırma sayısı. Bir çalıştırma tamamlandığında ancak çıkış koleksiyonu hala devam ederken sayı güncelleştirilir. | 
| Yanıt vermiyor çalıştırmaları | Count | Bu çalışma alanı için yanıt vermeyen çalıştırma sayısı. Bir çalıştırma yanıt vermiyorsa, sayı güncelleştirilir. |
| Başlatılmamış çalıştırmalar | Count | Bu çalışma alanı için başlatılmamış durumdaki çalıştırma sayısı. Sayı, çalıştırma oluşturmak için bir istek alındığında ancak çalıştırma bilgisi henüz doldurulmadığında güncelleştirilir. |
| Çalıştırmalar hazırlanıyor | Count | Bu çalışma alanı için hazırlanma çalıştırmaların sayısı. Çalışma ortamı hazırlanırken bir çalıştırma duruma hazırlık girdiğinde sayı güncellenir. |
| Sağlama çalıştırmaları | Count | Bu çalışma alanı için sağlama olan çalıştırma sayısı. Bir çalıştırma, işlem hedefi oluşturmayı veya sağlamayı beklerken, sayı güncelleştirilir. |
| Sıraya alınan çalıştırmalar | Count | Bu çalışma alanı için sıraya alınan çalıştırmaların sayısı. Bir çalıştırma işlem hedefinde sıraya alınmışsa, sayı güncelleştirilir. Gerekli işlem düğümlerinin hazırlanmasını beklerken gerçekleşebilir. |
| Başlatılan çalıştırmalar | Count | Bu çalışma alanı için çalıştırılan çalıştırma sayısı. Çalıştırma, gerekli kaynaklar üzerinde çalışmaya başladığında, sayı güncelleştirilir. |
| Çalıştırmalar başlatılıyor | Count | Bu çalışma alanı için başlatılan çalıştırma sayısı. Sayı, çalıştırma kimliği oluşturma ve çalıştırma kimliği gibi çalışma bilgisi doldurulduktan sonra güncelleştirilir. |
| Hatalar | Count | Bu çalışma alanındaki çalıştırma hatalarının sayısı. Çalışma bir hatayla karşılaştığında, sayı güncellenir. |
| Uyarılar | Count | Bu çalışma alanındaki çalışma uyarısı sayısı. Bir çalıştırma bir uyarıyla karşılaştığında, sayı güncellenir. |

## <a name="metric-dimensions"></a>Ölçüm boyutları

Ölçüm boyutları hakkında daha fazla bilgi için bkz. [çok boyutlu ölçümler](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning, ölçümleriyle ilişkili aşağıdaki boyutlara sahiptir.

| Boyut | Description |
| ---- | ---- |
| Küme Adı | İşlem kümesi kaynağının adı. Tüm Kota ölçümleri için kullanılabilir. |
| VM ailesi adı | Küme tarafından kullanılan VM ailesi adı. Kota kullanımı yüzdesi için kullanılabilir. |
| VM önceliği | VM 'nin önceliği. Kota kullanımı yüzdesi için kullanılabilir.
| CreatedTime | Yalnızca Cpukullanımı ve Gpukullanımı için kullanılabilir. |
| DeviceId | Cihazın KIMLIĞI (GPU). Yalnızca Gpukullanım için kullanılabilir. |
| NodeId | İşin çalıştığı yerde oluşturulan düğümün KIMLIĞI. Yalnızca Cpukullanımı ve Gpukullanımı için kullanılabilir. |
| RunId | Çalışma/iş KIMLIĞI. Yalnızca Cpukullanımı ve Gpukullanımı için kullanılabilir. |
| ComputeType | Çalıştırmanın kullandığı işlem türü. Yalnızca tamamlanan çalıştırmalar, başarısız çalıştırmalar ve başlatılan çalıştırmalar için kullanılabilir. |
| PipelineStepType | Çalıştırmada kullanılan [ardışık düzen Inestep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) türü. Yalnızca tamamlanan çalıştırmalar, başarısız çalıştırmalar ve başlatılan çalıştırmalar için kullanılabilir. |
| Publishedpipelineıd | Çalıştırmada kullanılan yayınlanan işlem hattının KIMLIĞI. Yalnızca tamamlanan çalıştırmalar, başarısız çalıştırmalar ve başlatılan çalıştırmalar için kullanılabilir. |
| RunType | Çalıştırma türü. Yalnızca tamamlanan çalıştırmalar, başarısız çalıştırmalar ve başlatılan çalıştırmalar için kullanılabilir. |

RunType boyutu için geçerli değerler şunlardır:

| Değer | Açıklama |
| ----- | ----- |
| Deneme | İşlem hattı olmayan çalıştırmalar. |
| Ardışık düzen eylemsizlik | Bir StepRun 'un üst öğesi olan bir işlem hattı çalıştırması. |
| StepRun | İşlem hattı adımı için bir çalıştırma. |
| ReusedStepRun | Önceki bir çalıştırmayı yeniden kullanan bir işlem hattı adımı için bir çalıştırma. |

## <a name="activity-log"></a>Etkinlik günlüğü

Aşağıdaki tabloda, etkinlik günlüğünde oluşturulabilecek Azure Machine Learning ilişkili işlemler listelenmiştir.

| İşlem | Description |
|:---|:---|
| Machine Learning çalışma alanı oluşturur veya güncelleştirir | Çalışma alanı oluşturuldu veya güncelleştirildi |
| Checkcomputenameavaılabılıty | İşlem adının zaten kullanımda olup olmadığını denetleyin |
| İşlem kaynaklarını oluşturur veya güncelleştirir | İşlem kaynağı oluşturuldu veya güncelleştirildi |
| İşlem kaynaklarını siler | Bir işlem kaynağı silindi |
| Gizli dizileri listeleme | Bir Machine Learning çalışma alanı için, işlem tarafından listelenen gizli dizileri |

## <a name="resource-logs"></a>Kaynak günlükleri

Bu bölümde, Azure Machine Learning çalışma alanı için toplayacağınız kaynak günlüklerinin türleri listelenmektedir.

Kaynak sağlayıcısı ve tür: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategori | Görünen Ad |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Şemalar

Aşağıdaki şemalar Azure Machine Learning tarafından kullanılıyor

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents tablosu

| Özellik | Açıklama |
|:--- |:---|
| TimeGenerated | Günlük girişinin oluşturulduğu saat |
| OperationName | Günlük olayı ile ilişkili işlemin adı |
| Kategori | Günlük olayının adı, AmlComputeClusterNodeEvent |
| JobId | Gönderilen Işin KIMLIĞI |
| ExperimentId | Deneme KIMLIĞI |
| ExperimentName | Deneme adı |
| CustomerSubscriptionId | Deneme ve Işin gönderildiği abonelik |
| WorkspaceName | Machine Learning çalışma alanının adı |
| ClusterName | Kümenin adı |
| ProvisioningState | Iş gönderimi durumu |
| ResourceGroupName | Kaynak grubunun adı |
| JobName | Işin adı |
| Lclusterıd | Kümenin KIMLIĞI |
| Olay türü | Iş olayının türü. Örneğin, Jobgönderildi, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | İşin durumu (çalıştırma). Örneğin, sıraya alınmış, çalışıyor, başarılı, başarısız |
| ErrorDetails | İş hatası ayrıntıları |
| CreationApiVersion | İşi oluşturmak için kullanılan API sürümü |
| ClusterResourceGroupName | Kümenin kaynak grubu adı |
| TFWorkerCount | TF Worker sayısı |
| TFParameterServerCount | TF Parameter Server sayısı |
| Araç türü | Kullanılan araç türü |
| Runıncontainer | İşin bir kapsayıcı içinde çalıştırılması gerekip gerekmediğini açıklayan bayrak |
| JobErrorMessage | Iş hatası ayrıntılı iletisi |
| NodeId | İşin çalıştığı, oluşturulan düğümün KIMLIĞI |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents tablosu

| Özellik | Açıklama |
|:--- |:--- |
| TimeGenerated | Günlük girişinin oluşturulduğu saat |
| OperationName | Günlük olayı ile ilişkili işlemin adı |
| Kategori | Günlük olayının adı, AmlComputeClusterNodeEvent |
| ProvisioningState | Kümenin sağlama durumu |
| ClusterName | Kümenin adı |
| ClusterType | Kümenin türü |
| CreatedBy | Kümeyi oluşturan Kullanıcı |
| CoreCount | Kümedeki çekirdekler sayısı |
| VmSize | Kümenin VM boyutu |
| VmPriority | Adanmış/LowPriority kümesi içinde oluşturulan düğümlerin önceliği |
| ScalingType | El ile veya otomatik olarak küme ölçeklendirme türü |
| Initialnodecount | Kümenin ilk düğüm sayısı |
| MinimumNodeCount | Kümenin en düşük düğüm sayısı |
| MaximumNodeCount | Kümenin en fazla düğüm sayısı |
| Nodeanlaşlocationoption | Düğümün nasıl serbest bırakılmasının gerektiği |
| Publisher | Küme türü yayımcısı |
| Sunduğu | Kümenin oluşturulduğu teklif |
| Sku | Küme içinde oluşturulan düğüm/VM SKU 'su |
| Sürüm | Düğüm/VM oluşturulurken kullanılan görüntünün sürümü |
| SubnetID | Kümenin alt NetID |
| AllocationState | Küme ayırma durumu |
| CurrentNodeCount | Kümenin geçerli düğüm sayısı |
| TargetNodeCount | Ölçek artırma/azaltma sırasında kümenin hedef düğüm sayısı |
| Olay türü | Küme oluşturma sırasında olay türü. |
| NodeIdleTimeSecondsBeforeScaleDown | Kümenin aşağı ölçeklendirilmesi için saniye cinsinden boş süre |
| PreemptedNodeCount | Kümenin küme dışı düğüm sayısı |
| Iresizegrow | Kümenin ölçeğini belirten bayrak |
| VmFamilyName | Küme içinde oluşturulabilecek düğümlerin VM ailesi adı |
| LeavingNodeCount | Kümenin düğüm sayısını bırakma |
| UnusableNodeCount | Kümenin kullanılamayan düğüm sayısı |
| Idıdnodecount | Kümenin boşta düğüm sayısı |
| RunningNodeCount | Kümenin çalışan düğüm sayısı |
| Hazırlık Ingnodecount | Kümenin düğüm sayısı hazırlanıyor |
| Kota ayrılmış | Kümeye ayrılan kota |
| Kota kullanıldı | Küme kotası kullanıldı |
| Allocationstategeçişli saati | Bir durumdan diğerine geçiş süresi |
| ClusterErrorCodes | Küme oluşturma veya ölçeklendirme sırasında hata kodu alındı |
| CreationApiVersion | Küme oluşturulurken kullanılan API sürümü |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents tablosu

| Özellik | Açıklama |
|:--- |:--- |
| TimeGenerated | Günlük girişinin oluşturulduğu saat |
| OperationName | Günlük olayı ile ilişkili işlemin adı |
| Kategori | Günlük olayının adı, AmlComputeClusterNodeEvent |
| ClusterName | Kümenin adı |
| NodeId | Oluşturulan küme düğümünün KIMLIĞI |
| VmSize | Düğümün VM boyutu |
| VmFamilyName | Düğümün ait olduğu VM ailesi |
| VmPriority | Oluşturulan düğümün önceliği/LowPriority |
| Publisher | VM görüntüsünün yayımcısı. Örneğin, Microsoft-dsvm |
| Sunduğu | VM oluşturma ile ilişkili teklif |
| Sku | Oluşturulan düğüm/VM SKU 'su |
| Sürüm | Düğüm/VM oluşturulurken kullanılan görüntünün sürümü |
| ClusterCreationTime | Kümenin oluşturulduğu zaman |
| ResizeStartTime | Küme ölçeği artırma/azaltma süresi |
| Resizebitişsaati | Küme ölçeği artırma/azaltma süresi sonu |
| NodeAllocationTime | Düğümün ayrıldığı saat |
| NodeBootTime | Düğümün önyüklendiği zaman |
| StartTaskStartTime | Görevin bir düğüme atandığı ve başlatıldığı zaman |
| StartTaskEndTime | Bir düğüme atanan görevin sona erdiği saat |
| TotalE2ETimeInSeconds | Toplam süre düğümü etkin |


## <a name="see-also"></a>Ayrıca bkz.

- İzleme Azure Machine Learning açıklaması için bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md) .
- Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md) .
