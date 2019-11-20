---
title: Azure seri konsol hataları | Microsoft Docs
description: Azure seri konsolu 'nda sık karşılaşılan hatalar
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e4596ae2f92e5dfd99dc7c83857e0c9874358fd4
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949698"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure seri konsolu 'nda sık karşılaşılan hatalar
Azure seri konsolu 'nda bilinen hatalar kümesi vardır. Bu, bunlar için bu hataların ve risk azaltma adımlarının bir listesidir.

## <a name="common-errors"></a>Sık karşılaşılan hatalar

Hata                            |   Azaltma
:---------------------------------|:--------------------------------------------|
*@No__t-1VMNAME @ no__t-2*için önyükleme Tanılama ayarları alınamıyor. Seri konsolunu kullanmak için, bu VM için önyükleme tanılamaları etkinleştirildiğinden emin olun. ![Boot Diagnostics hatası @ no__t-1 | VM veya sanal makine ölçek kümesinin [önyükleme tanılaması](boot-diagnostics.md) 'nın etkinleştirildiğinden emin olun. Bir sanal makine ölçek kümesi örneğinde seri konsol kullanıyorsanız, örneğinizin en son modele sahip olduğundan emin olun.
VM durdurulmuş serbest bırakılmış durumda. VM 'yi başlatın ve seri konsol bağlantısını yeniden deneyin. ![Serbest bırakıldı hatası](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | Seri konsoluna erişmek için VM veya sanal makine ölçek kümesi örneğinin başlatılmış bir durumda olması gerekir. VM 'nizi veya sanal makine ölçek kümesi örneğinizi başlatın ve yeniden deneyin.
Bu VM 'nin önyükleme tanılama depolama hesabına erişirken bir "yasak" yanıtıyla karşılaşıldı. ![Depolama hesabı güvenlik duvarı hatası](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Önyükleme tanılamalarının bir hesap güvenlik duvarı olmadığından emin olun. Seri konsolunun çalışması için erişilebilir bir önyükleme tanılama depolama hesabı gereklidir. Seri konsol tasarıma göre, önyükleme tanılama depolama hesabında etkin olan depolama hesabı güvenlik duvarları ile birlikte çalışılamıyor.
Bu VM 'yi seri konsol ile kullanmak için gerekli izinlere sahip değilsiniz. En az sanal makine katılımcısı rol izinlerine sahip olduğunuzdan emin olun.| Seri konsol erişimi, VM 'niz veya sanal makine ölçek kümesi üzerinde katkıda bulunan düzeyinde erişime sahip olmanızı gerektirir. Daha fazla bilgi için [genel bakış sayfasına](serial-console-overview.md)bakın.
Bu VM 'de önyükleme tanılaması için kullanılan ' ' depolama hesabı bulunamadı. Bu VM için önyükleme tanılamaları 'nın etkinleştirildiğini, bu depolama hesabının silinmediğini ve bu depolama hesabına erişiminizin olduğunu doğrulayın. | VM 'niz veya sanal makine ölçek kümesi için önyükleme tanılama depolama hesabını silmediğinden emin olun
Bu VM için sağlama henüz başarılı değil. Lütfen VM 'nin tam olarak dağıtıldığından emin olun ve seri konsol bağlantısını yeniden deneyin. | VM 'niz veya sanal makine ölçek kümesi hala sağlanıyor olabilir. Bir süre bekleyip yeniden deneyin.
Bu VM için önyükleme tanılama depolama hesabına yazmak için gerekli izinlere sahip değilsiniz. Lütfen ' ' üzerinde en az sanal makine katılımcısı izinlerinizin olduğundan emin olun. | Seri konsol erişim, ön yükleme tanılama depolama hesabında katkıda bulunan düzeyinde erişim gerektirir. Daha fazla bilgi için [genel bakış sayfasına](serial-console-overview.md)bakın.
*@No__t-1STORAGEACCOUNTNAME @ no__t-2*adlı önyükleme tanılama depolama hesabı için kaynak grubu belirlenemiyor. Bu VM için önyükleme tanılamaları 'nın etkinleştirildiğini ve bu depolama hesabına erişiminizin olduğunu doğrulayın. | Seri konsol erişim, ön yükleme tanılama depolama hesabında katkıda bulunan düzeyinde erişim gerektirir. Daha fazla bilgi için [genel bakış sayfasına](serial-console-overview.md)bakın.
Web yuvası kapalı veya açılamadı. | @No__t-0 ' a güvenlik duvarı erişimi eklemeniz gerekebilir. Daha ayrıntılı ancak daha uzun bir yaklaşım, çok düzenli olarak değişen [Microsoft Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)güvenlik duvarı erişimine izin vermedir.
Seri konsol hiyerarşik ad alanları ile Azure Data Lake Storage 2. kullanılarak depolama hesabıyla birlikte çalışmaz. | Bu, hiyerarşik ad alanları ile ilgili bilinen bir sorundur. Azaltmak için, sanal makinenizin önyükleme tanılama depolama hesabının Azure Data Lake Storage 2. kullanılarak oluşturulmadığından emin olun. Bu seçenek, yalnızca depolama hesabı oluşturma sırasında ayarlanabilir. Bu sorunu azaltmak için Azure Data Lake Storage 2. etkin olmadan ayrı bir önyükleme tanılama depolama hesabı oluşturmanız gerekebilir.


## <a name="next-steps"></a>Sonraki adımlar
* [Linux VM 'leri Için Azure seri konsolu](./serial-console-linux.md) hakkında daha fazla bilgi edinin
* [Windows VM 'leri Için Azure seri konsolu](./serial-console-windows.md) hakkında daha fazla bilgi edinin