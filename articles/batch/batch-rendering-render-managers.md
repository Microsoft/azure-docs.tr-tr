---
title: Oluşturma yöneticisi desteği-Azure Batch
description: Azure Batch render Manager tümleştirmesini kullanarak işleme için Azure kullanma
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: eb3ce47e5ffed697392065a1faacbbfaec19f4d1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983680"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>İşleme grubu yöneticileriyle Azure Batch kullanma

Var olan bir şirket içi işleme grubu kullanıyorsanız, işleme yöneticisinin işleme grubu kapasitesini ve işleme işlerini denetlemeleri oldukça olasıdır.

Azure, popüler oluşturma yöneticileri için yerleşik destek veya eklentiler sağlar. Daha sonra, kullanım için ödeme yapılacak uygulama lisanslama ve düşük öncelikli VM 'ler dahil olmak üzere Azure VM 'leri ekleyebilir ve kaldırabilirsiniz.

Aşağıdaki oluşturma yöneticileri desteklenir:

* [PipelineFX QUP!](https://www.pipelinefx.com/)
* [Royal oluşturma](https://www.royalrender.de/)
* [Thinkbox son tarih](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Işleme Merkezi

Azure Işleme Merkezi, Azure oluşturma gruplarının oluşturulmasını ve yönetimini basitleştirir.  İşleme merkezinde, PipelineFx qum ve son tarih 10 için yerel destek bulunur.  Daha fazla bilgi ve ayrıntılı yönergeler için [GitHub deposuna](https://github.com/Azure/azure-render-hub)bakın.

## <a name="using-azure-with-pipelinefx-qube"></a>Azure ile PipelineFX QUP kullanma

Azure Işleme Merkezi, son tarih dahil popüler oluşturma yöneticilerini destekler.  Işleme hub 'ı dağıtma ve kullanma hakkında yönergeler için bkz. [GitHub deposu](https://github.com/Azure/azure-render-hub).

Azure Batch havuz VM 'lerinin [GitHub deposunda](https://github.com/Azure/azure-qube)da kullanılabilir olması için kullanılacak betikler ve yönergeler.

## <a name="using-azure-with-royal-render"></a>Azure kullanarak Royal oluşturma

Royal oluşturma, Azure ve Azure Batch tümleştirme yerleşik olarak bulunur ve Azure tabanlı VM 'lerle bir işleme grubunu genişletmenize olanak tanır. Özet için [Yardım dosyalarına](https://www.royalrender.de/help8/index.html?Cloudrendering.html)bakın.

Azure tümleştirmesini kullanarak bir Royal oluşturma müşterisi örneği için bkz. [Jellybalık resimleri müşteri hikayesi](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Azure 'ı Thinkbox son tarihine kadar kullanma

Azure Işleme Merkezi, son tarih dahil popüler oluşturma yöneticilerini destekler.  Işleme hub 'ı dağıtma ve kullanma hakkında yönergeler için bkz. [GitHub deposu](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Sonraki adımlar

Uygun olan yerlerde GitHub ile ilgili eklenti ve yönergeleri kullanarak, işleme yöneticiniz için Azure Batch tümleştirmesini deneyin.
