---
title: Azure Marketi 'nde sanal makine teklifi
description: Azure Marketi 'nde VM teklifi yayımlama sürecine genel bakış.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 0313c4f2581615e1d1a3a57fc191db7d7cc6e825
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808571"
---
# <a name="virtual-machine-offer"></a>Sanal makine teklifi

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Bu bölümde, [Azure Marketi](https://azuremarketplace.microsoft.com)'ne yeni bir sanal makine teklifinin nasıl yayımlanacağı açıklanmaktadır. Bir işletim sistemi sanal sabit diski (VHD) ve sıfır veya daha fazla veri VHD 'si içeren hem Windows tabanlı hem de Linux tabanlı sanal makineler için destek sağlanır. | ![sanal makine simgesi](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Yayımlamaya genel bakış

Aşağıdaki videoda, [Azure Market teklifinizi iyileştirin](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), bu Market 'te yayımlama (bir sanal makine çözümü kullanılarak), ürün sayfanız ile Kullanıcı deneyimini en iyileştirmek dahil olmak üzere Azure Marketi 'ne genel bakış sunulmaktadır. ve isteğe bağlı test sürücü deneyimi, Kullanıcı müşteri adayları nasıl oluşturulur ve bunları nasıl tüketirebileceğinizi ve müşteri katılımını iyileştirebilirsiniz.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>VM yayımlama işlemi akışı

Aşağıdaki diyagramda bir VM teklifi yayımlamanın üst düzey adımları gösterilmektedir. 

![VM yayımlama işlemi](./media/publishvm_001.png)

1. Teklifi oluşturun-teklif açıklaması, pazarlama malzemeleri, yasal, destek bilgileri ve varlık belirtimleri dahil olmak üzere teklifle ilgili tüm ayrıntıları ve bilgileri yapılandırılır.

2. İş ve teknik varlıkları oluşturun-ilgili çözüme yönelik iş varlıklarını (yasal belgeler ve pazarlama malzemeleri) ve teknik varlıkları oluşturun (burada, VM 'Ler ve bağlı diskler). 

3. SKU 'YU oluşturun-teklifle ilişkili ilgili SKU 'ları oluşturun ve bunları iletin.  Yayımlamayı planladığınız her görüntü için benzersiz bir SKU gereklidir. 
 
4. Teklifi onaylama ve yayımlama-teklif ve teknik varlıklar tamamlandığında teklifi gönderebilirsiniz. Bu gönderim, çözümün test edilmiş, doğrulanan, sertifikalı ve sonra Market 'teki "canlı" olduğu yayımlama işlemini başlatacak.  

## <a name="next-steps"></a>Sonraki adımlar

Bu adımları kabul etmeden önce, bir VM 'yi Microsoft Azure Market yayımlamak için [Teknik ve iş gereksinimlerini](./cpp-prerequisites.md) karşılamanız gerekir. 
