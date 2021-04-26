---
title: Azure Red Hat OpenShift 4 küme destek ilkesi
description: Red Hat OpenShift 4 için destek ilkesi gereksinimlerini anlama
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 30579536b8051e9a045c217751871287636a3976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454287"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift destek ilkesi

Azure Red Hat OpenShift 4 kümelerine yönelik belirli yapılandırmalarda, kümenizin desteklenebilirliği etkilenebilir. Azure Red Hat OpenShift 4, küme yöneticilerinin iç küme bileşenlerinde değişiklik yapmasına izin verir, ancak tüm değişiklikler desteklenmez. Aşağıdaki destek ilkesi, hangi değişikliklerin ilkeyi ihlal edeceğini ve Microsoft ve Red Hat 'in void desteğini paylaşır.

> [!NOTE]
> OpenShift kapsayıcı platformunda teknoloji önizlemesi işaretli özellikler Azure Red Hat OpenShift 'te desteklenmez.

## <a name="cluster-configuration-requirements"></a>Küme yapılandırma gereksinimleri

* Tüm OpenShift kümesi işleçleri yönetilen bir durumda kalmalıdır. Küme işleçleri listesi, çalıştırılarak döndürülür `oc get clusteroperators` .
* Kümenin en az üç çalışan düğümü ve üç yönetici düğümü olmalıdır. OpenShift bileşenlerinin zamanlanmasını engelleyen talitre yok. Küme çalışanlarını sıfıra ölçeklendirmeyin veya düzgün bir küme kapatması yapmayı deneyin.
* Prometheus ve Alertmanager hizmetlerini kaldırmayın veya değiştirmeyin.
* Service Alertmanager kurallarını kaldırmayın.
* Ağ güvenlik gruplarını kaldırmayın veya değiştirmeyin.
* Azure Red Hat OpenShift hizmet günlüğü 'nü (MDSD pods) kaldırmayın veya değiştirmeyin.
* ' Arosvc.azurecr.io ' küme çekme parolasını kaldırmayın veya değiştirmeyin.
* Tüm küme sanal makinelerinin, en azından Azure Resource Manager (ARM) ve hizmet günlüğü (Genfiliz) uç noktalarına doğrudan giden internet erişimi olması gerekir.  HTTPS proxy için hiçbir form desteklenmez.
* Kümenin sanal ağının DNS yapılandırmasını değiştirmeyin. Varsayılan Azure DNS Çözümleyicisinin kullanılması gerekir.
* Kümenin MachineConfig nesnelerinden hiçbirini (örneğin, kubelet yapılandırması) herhangi bir şekilde geçersiz kılmayın.
* Hiçbir Unsupportedconfiggeçersiz kılma seçeneği ayarlama. Bu seçeneklerin ayarlanması, küçük sürüm yükseltmelerini engeller.
* Azure Red Hat OpenShift hizmeti, kümenize özel bağlantı hizmeti aracılığıyla erişir.  Hizmet erişimini kaldırmayın veya değiştirmeyin.
* RHCOS olmayan işlem düğümleri desteklenmez. Örneğin, bir RHEL işlem düğümü kullanamazsınız.

## <a name="supported-virtual-machine-sizes"></a>Desteklenen sanal makine boyutları

Azure Red Hat OpenShift 4, aşağıdaki sanal makine boyutlarında çalışan düğümü örneklerini destekler:

### <a name="general-purpose"></a>Genel amaçlı

|Seriler|Boyut|Sanal işlemci|Bellek: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Bellek için iyileştirilmiş

|Seriler|Boyut|Sanal işlemci|Bellek: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>İşlem için iyileştirilmiş

|Seriler|Boyut|Sanal işlemci|Bellek: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Ana düğümler

|Seriler|Boyut|Sanal işlemci|Bellek: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
