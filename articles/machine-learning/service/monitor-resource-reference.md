---
title: Veri başvurusunu izleme | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Azure Machine Learning için toplanan ve Azure Izleyici 'de kullanılabilen veriler ve kaynaklar hakkında bilgi edinin. Azure Izleyici Azure Machine Learning çalışma alanınız hakkındaki verileri toplar ve yüzeyler ve ölçümleri görüntülemenize, uyarıları ayarlamanıza ve günlüğe kaydedilen verileri çözümlemenize olanak tanır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/06/2019
ms.openlocfilehash: 743007d3636516a39dd36caf3d44d3edb650c030
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929534"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure Machine Learning izleme verileri başvurusu

Azure Machine Learning çalışma alanınızdan Azure Izleyici tarafından toplanan veriler ve kaynaklar hakkında bilgi edinin. İzleme verilerini toplama ve çözümleme hakkında ayrıntılar için bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md) .

## <a name="resource-logs"></a>Kaynak günlükleri

Aşağıdaki tabloda, Azure Izleyici günlüklerinde veya Azure Storage 'da toplandıklarında Azure Machine Learning kaynak günlüklerinin özellikleri listelenmektedir.

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents tablosu

| Özellik | Açıklama |
|:--- |:---|
| TimeGenerated | Günlük girişinin oluşturulduğu saat |
| ThrottledRequests | Günlük olayı ile ilişkili işlemin adı |
| Kategori | Günlük olayının adı, AmlComputeClusterNodeEvent |
| JobId | Gönderilen Işin KIMLIĞI |
| ExperimentId | Deneme KIMLIĞI |
| experimentName | Deneme adı |
| Customersubscriptionıd | Deneme ve Işin gönderildiği abonelik |
| workspaceName | Machine Learning çalışma alanının adı |
| :/ | Kümenin adı |
| ProvisioningState | Iş gönderimi durumu |
| ResourceGroupName | Kaynak grubunun adı |
| JobName | Işin adı |
| Lclusterıd | Kümenin KIMLIĞI |
| Türü | Iş olayının türü, örneğin, Jobgönderildi, JobRunning, JobFailed, JobSucceeded vb. |
| ExecutionState | İşin durumu (çalıştırma), örneğin, sıraya alınmış, çalışıyor, başarılı, başarısız oldu |
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
| ThrottledRequests | Günlük olayı ile ilişkili işlemin adı |
| Kategori | Günlük olayının adı, AmlComputeClusterNodeEvent |
| ProvisioningState | Kümenin sağlama durumu |
| :/ | Kümenin adı |
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
| Yayımcı | Küme türü yayımcısı |
| Sunduğu | Kümenin oluşturulduğu teklif |
| Sku | Küme içinde oluşturulan düğüm/VM SKU 'su |
| Sürüm | Düğüm/VM oluşturulurken kullanılan görüntünün sürümü |
| SubnetID | Kümenin alt NetID |
| AllocationState | Küme ayırma durumu |
| CurrentNodeCount | Kümenin geçerli düğüm sayısı |
| TargetNodeCount | Ölçek artırma/azaltma sırasında kümenin hedef düğüm sayısı |
| Türü | Küme oluşturma sırasında olay türü. |
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
| ThrottledRequests | Günlük olayı ile ilişkili işlemin adı |
| Kategori | Günlük olayının adı, AmlComputeClusterNodeEvent |
| :/ | Kümenin adı |
| NodeId | Oluşturulan küme düğümünün KIMLIĞI |
| VmSize | Düğümün VM boyutu |
| VmFamilyName | Düğümün ait olduğu VM ailesi |
| VmPriority | Oluşturulan düğümün önceliği/LowPriority |
| Yayımcı | VM görüntüsünün yayımcısı, ör. Microsoft-dsvm |
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

### <a name="metrics"></a>Ölçümler

Aşağıdaki tablolarda Azure Machine Learning tüm ölçümler için toplanan platform ölçümleri listelenmektedir **Azure Machine Learning hizmet çalışma**alanında depolanır.

**Modelinizi**

| Ölçüm | Birim | Açıklama |
| ----- | ----- | ----- |
| Model dağıtılamadı | Sayı | Başarısız olan model dağıtımı sayısı. |
| Model dağıtımı başlatıldı | Sayı | Başlatılan model dağıtımı sayısı. |
| Model dağıtımı başarılı oldu | Sayı | Başarılı olan model dağıtımı sayısı. |
| Model kaydı başarısız oldu | Sayı | Başarısız olan model kaydı sayısı. |
| Model kaydı başarılı oldu | Sayı | Başarılı olan model kaydı sayısı. |

**Kota**

Kota bilgileri yalnızca Azure Machine Learning işlem amaçlıdır.

| Ölçüm | Birim | Açıklama |
| ----- | ----- | ----- |
| Etkin çekirdekler | Sayı | Etkin işlem çekirdekleri sayısı. |
| Etkin düğümler | Sayı | Etkin düğümlerin sayısı. |
| Boştaki çekirdekler | Sayı | Boşta işlem çekirdekleri sayısı. |
| Boştaki düğümler | Sayı | Boşta işlem düğümlerinin sayısı. |
| Çekirdekleri bırakma | Sayı | Çekirdekleri bırakma sayısı. |
| Düğümlerden çıkılıyor | Sayı | Düğüm sayısı. |
| Önden çekirdekler | Sayı | Yok edilecek çekirdek sayısı. |
| Önden düğümler | Sayı | Yok edilecek düğüm sayısı. |
| Kota kullanım yüzdesi | Yüzde | Kullanılan Kota yüzdesi. |
| Toplam çekirdek sayısı | Sayı | Toplam çekirdek sayısı. |
| Toplam düğüm sayısı | Sayı | Toplam düğüm sayısı. |
| Kullanılamayan çekirdekler | Sayı | Kullanılamayan çekirdek sayısı. |
| Kullanılamayan düğümler | Sayı | Kullanılamayan düğüm sayısı. |

Kota ölçümlerini filtrelemek için kullanılabilen boyutlar aşağıda verilmiştir:

| Boyut | Şu ile kullanılabilen metrik | Açıklama |
| ---- | ---- | ---- |
| Küme Adı | Tüm Kota ölçümleri | İşlem örneğinin adı. |
| VM ailesi adı | Kota kullanım yüzdesi | Küme tarafından kullanılan VM ailesi adı. |
| VM önceliği | Kota kullanım yüzdesi | VM 'nin önceliği.

**Çalışmaz**

Eğitim çalıştırmaları hakkında bilgiler.

| Ölçüm | Birim | Açıklama |
| ----- | ----- | ----- |
| Tamamlanan çalıştırmalar | Sayı | Tamamlanan çalıştırma sayısı. |
| Başarısız çalıştırmalar | Sayı | Başarısız çalıştırmaların sayısı. |
| Başlatılan çalıştırmalar | Sayı | Başlatılan çalıştırma sayısı. |

Aşağıda, çalıştırılan ölçümleri filtrelemek için kullanılabilen boyutlar verilmiştir:

| Boyut | Açıklama |
| ---- | ---- |
| ComputeType | Çalıştırmanın kullandığı işlem türü. |
| PipelineStepType | Çalıştırmada kullanılan [ardışık düzen Inestep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) türü. |
| Publishedpipelineıd | Çalıştırmada kullanılan yayınlanan işlem hattının KIMLIĞI. |
| RunType | Çalıştırma türü. |

RunType boyutu için geçerli değerler şunlardır:

| Değer | Açıklama |
| ----- | ----- |
| Deneyin | İşlem hattı olmayan çalıştırmalar. |
| Ardışık düzen eylemsizlik | Bir StepRun 'un üst öğesi olan bir işlem hattı çalıştırması. |
| StepRun | İşlem hattı adımı için bir çalıştırma. |
| ReusedStepRun | Önceki bir çalıştırmayı yeniden kullanan bir işlem hattı adımı için bir çalıştırma. |

## <a name="see-also"></a>Ayrıca Bkz.

- İzleme Azure Machine Learning açıklaması için bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md) .
- Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](/azure/azure-monitor/insights/monitor-azure-resource) .
