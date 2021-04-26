---
title: Azure NetApp Files | el ile QoS kapasite havuzu Microsoft Docs
description: El ile QoS kapasite havuzuna giriş ve ek bilgiler için başvurular sağlar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581134"
---
# <a name="manual-qos-capacity-pool"></a>El ile QoS kapasite havuzu

Bu makalede, el ile hizmet kalitesi (QoS) kapasite havuzu işlevlerine giriş sağlanır.

## <a name="how-manual-qos-differs-from-auto-qos"></a>El ile QoS otomatik QoS 'den farklı

[QoS türü](azure-netapp-files-understand-storage-hierarchy.md#qos_types) , bir kapasite havuzunun özniteliğidir. Azure NetApp Files iki QoS türü kapasite havuzu sağlar: otomatik (varsayılan) ve el ile.  

*El ile* QoS kapasite havuzunda, bir birimin kapasitesini ve iş üretimini bağımsız olarak atayabilirsiniz. En düşük ve en yüksek aktarım hızı düzeyleri için bkz. [Azure NetApp Files Için kaynak sınırları](azure-netapp-files-resource-limits.md#resource-limits). El ile QoS kapasite havuzu ile oluşturulan tüm birimlerin toplam verimlilik, havuzun toplam verimi ile sınırlıdır. Havuz boyutu ve hizmet düzeyi aktarım hızı birleşimine göre belirlenir. 

Bir *Otomatik* QoS kapasite havuzunda, işleme, havuzdaki birimlere göre otomatik olarak atanır ve birimlere atanan boyut kotasıyla orantılıdır.  

QoS türleri hakkında dikkat edilmesi gereken noktalar için bkz. [Azure NetApp Files depolama hiyerarşisi](azure-netapp-files-understand-storage-hierarchy.md) ve [performans Azure NetApp Files değerlendirmeleri](azure-netapp-files-performance-considerations.md) .

## <a name="example-of-using-manual-qos"></a>El ile QoS kullanma örneği

Örneğin, bir SAP HANA sistemi, Oracle veritabanı veya birden çok birim gerektiren diğer iş yükleri ile el ile QoS kapasite havuzu kullandığınızda, bu uygulama birimlerini oluşturmak için kapasite havuzu kullanılabilir.  Her birim, uygulama gereksinimlerini karşılamak için bireysel boyut ve üretilen iş miktarını sağlayabilir.  Avantajlar hakkında daha fazla bilgi için bkz. [El Ile QoS kapasite havuzundaki birimlerin aktarım hızı limiti örnekleri](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) .  

## <a name="how-to-specify-the-manual-qos-type"></a>El ile QoS türünü belirtme

[Bir kapasite havuzu oluşturduğunuzda](azure-netapp-files-set-up-capacity-pool.md), kapasite havuzunun el ile QoS türünü kullanmasını belirtebilirsiniz.  Ayrıca, el ile QoS türünü kullanmak için [var olan bir kapasite havuzunu değiştirebilirsiniz](manage-manual-qos-capacity-pool.md#change-to-qos) . 

Kapasite türünün el ile QoS olarak ayarlanması kalıcı bir değişiklikdir. El ile QoS tür kapasitesi aracını otomatik QoS kapasite havuzuna dönüştüremezsiniz. 

El ile QoS türünün kullanılması [, özelliği kaydetmenizi](manage-manual-qos-capacity-pool.md#register-the-feature)gerektirir.  

## <a name="next-steps"></a>Sonraki adımlar

* [El ile QoS kapasite havuzu yönetme](manage-manual-qos-capacity-pool.md)
* [Kapasite havuzunu ayarlama](azure-netapp-files-set-up-capacity-pool.md)
* [Depolama hiyerarşisi](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp Files için hizmet düzeyleri](azure-netapp-files-service-levels.md)
* [Azure NetApp Files için performansla ilgili önemli noktalar](azure-netapp-files-performance-considerations.md)
* [Azure NetApp Files için maliyet modeli](azure-netapp-files-cost-model.md)
* [Azure NetApp Files için kaynak sınırları](azure-netapp-files-resource-limits.md)
* [NFS birimi oluşturma](azure-netapp-files-create-volumes.md)
* [SMB birimi oluşturma](azure-netapp-files-create-volumes-smb.md)
* [Çift protokollü birim oluşturma](create-volumes-dual-protocol.md)
* [Azure NetApp Files için ölçümler](azure-netapp-files-metrics.md)
* [Kapasite havuzu sorunlarını giderme](troubleshoot-capacity-pools.md)
