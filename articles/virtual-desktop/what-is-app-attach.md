---
title: Windows sanal masaüstü MALTı uygulama eklemeye genel bakış-Azure
description: MSIX uygulama iliştirme nedir? Bu makaleye ulaşın.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cdbb7574f9da5ea6cd0695b74c269d8e1b88b7f4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444080"
---
# <a name="what-is-msix-app-attach"></a>MSIX uygulama iliştirme nedir?

MSIX, tüm Windows uygulamaları için paketleme deneyimini geliştirmeyi hedefleyen çok sayıda özellik sunan yeni bir paketleme biçimidir. MSIX hakkında daha fazla bilgi edinmek için bkz. [msix genel bakış](/windows/msix/overview).

MSIX uygulama iliştirme, hem fiziksel hem de sanal makinelere MALTÝ uygulama sunmaya yönelik bir yoldur. Ancak, özellikle Windows sanal masaüstü için yapıldığından, MSIX uygulama iliştirme normal maltıdan farklıdır. Bu makalede, MALTıDA uygulama eklemenin ne olduğu ve sizin için neler yapabilecekleri açıklanır.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Windows sanal Masaüstündeki uygulama teslim seçenekleri

Windows sanal masaüstü 'nde aşağıdaki yöntemlerden birini kullanarak uygulama sunabilirsiniz:

- Uygulamaları ana görüntüye yerleştirme
- Merkezi Yönetim için SCCM veya Intune gibi araçları kullanma
- Dinamik uygulama sağlama (AppV, VMWare AppVolumes veya Citrix AppLayering)
- Microsoft ve üçüncü taraf bir araç kullanarak özel araçlar veya betikler oluşturma

## <a name="what-does-msix-app-attach-do"></a>MALTı uygulama iliştirme nedir?

Bir Windows sanal masaüstü dağıtımında, MSIX uygulama iliştirme şunları yapabilir:

- [Maltılardan](/windows/msix/msix-container)yararlanarak Kullanıcı verileri, işletim sistemi ve uygulamalar arasında ayrım oluşturun.
- Uygulamaları dinamik olarak sunarken yeniden paketleme gereksinimini ortadan kaldırın.
- Kullanıcının oturum açması için geçen süreyi azaltın.
- Altyapı gereksinimlerini ve maliyetini azaltın.

MSIX uygulama iliştirme, VDı veya SBC dışında uygulanabilir olmalıdır.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Geleneksel uygulama katmanlama ile MSIX uygulama iliştirme

Aşağıdaki tabloda, MSIX uygulama iliştirme ve uygulama katmanladığı anahtar özelliği karşılaştırılmaktadır.

| Özellik | Geleneksel uygulama katmanlama  | MSIX uygulama iliştirme  |
|-----|-----------------------------|--------------------|
| Biçimlendir               | Farklı uygulama katmanlama teknolojileri farklı mülkiyet biçimleri gerektirir. | Yerel MSIX paketleme biçimiyle birlikte kullanılır.        |
| Yeniden paketleme ek yükü | Özel biçimler, güncelleştirme başına sıralama ve yeniden paketleme gerektirir.         | MALTıYA olarak yayımlanan uygulamalar yeniden paketleme gerektirmez. Ancak, MSIX paketi kullanılamıyorsa yeniden paketleme ek yükü hala geçerlidir. |
| Ekosistem            | Yok (örneğin, satıcılar App-V ' y i teslim etmeyin)  | MSIX, Microsoft 'un temel ISV iş ortaklarının ve Office gibi şirket içi uygulamaların benimsediği temel teknolojisidir. MSIX 'yi hem sanal masaüstleri hem de fiziksel Windows bilgisayarlarda kullanabilirsiniz. |
| Altyapı       | Ek altyapı gerekli (sunucular, istemciler vb.) | Yalnızca depolama alanı   |
| Yönetim       | Bakım ve güncelleştirme gerektirir   | Uygulama güncelleştirmelerini basitleştirir |
| Kullanıcı deneyimi      | Kullanıcı oturum açma süresini etkiler. İşletim sistemi durumu, uygulama durumu ve Kullanıcı verileri arasında sınır var.  | Sunulan uygulamalar, yerel olarak yüklenen uygulamalardan ayırt edilemez. |

## <a name="next-steps"></a>Sonraki adımlar

MSIX uygulama iliştirme hakkında daha fazla bilgi edinmek istiyorsanız, [Sözlükte](app-attach-glossary.md) ve [SSS bölümüne](app-attach-faq.md)göz atın. Aksi takdirde, [uygulama eklemeyi ayarlama](app-attach.md)ile çalışmaya başlayın.
