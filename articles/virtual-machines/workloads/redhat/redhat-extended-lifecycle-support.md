---
title: Red Hat Enterprise Linux Genişletilmiş Yaşam Döngüsü Desteği
description: Red Hat kurumsal genişletilmiş yaşam döngüsü desteği ekleme hakkında bilgi edinin
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677194"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) genişletilmiş yaşam döngüsü desteği
Bu makale, Red Hat Enterprise görüntüleri için genişletilmiş yaşam döngüsü desteği hakkında bilgi sağlar:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 yaşam döngüsü
30 Kasım 2020 tarihinden itibaren 6 Red Hat Enterprise Linux, bakım aşamasına ulaşacaktır. Bakım aşamasına daha sonra genişletilmiş yaşam aşaması gelir. Tam/bakım aşamalarından Red Hat Enterprise Linux 6 ' nın dışına geçiş yapmak için, Red Hat Enterprise Linux 7 veya 8 ' e yükseltilmesi önemle önerilir. Müşterilerin Red Hat Enterprise Linux 6 ' da kalması gerekiyorsa, Red Hat Enterprise Linux genişletilmiş yaşam döngüsü desteği (virgülle) eklentisini eklemeniz önerilir.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Market Kullandıkça Öde VM 'lerinde genişletilmiş yaşam döngüsü desteği ekleme adımları
1. Bu form için, biçim desteği eklemek istediğiniz VM 'lerin iletişim ayrıntılarını ve abonelik bilgilerini [burada bulunan](https://aka.ms/els-form) alanları dolduracak şekilde doldurabilirsiniz. Fiyatlandırma ayrıntıları ekleme, form içinde de kullanılabilir.
1. Azure Red Hat Enterprise Linux ekibi, 1-2 iş günü içinde daha fazla destek eklemek için sanal makineler listesi ile size ulaşacak. Lütfen listeyi gözden geçirin ve kabul etmiş Ekle fiyatlandırmasına yanıt verin.
1. Azure Red Hat Enterprise Linux ekibi, sanal makinelere aynı istemci paketini ekleme adımlarını paylaşır. Yazılım Bakımı (hata ve güvenlik düzeltmeleri) ve Red Hat Enterprise Linux 6 desteği almaya devam etmek için e-postada sağlanacak adımları izleyin.

> [!Note]
> Kuruluşunuzun dışındaki kişilerle RHEL ile birlikte ekleme ile ilgili adımları paylaşmayın. AzureRedHatELS@microsoft.comDestek almak veya diğer sorular için uygulamasına ulaşın.

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6 kullanıyorum ve şu anda sonraki bir sürüme geçirilemez. Hangi seçeneklere sahip mıyım?
* Red Hat Enterprise Linux 6 ' yı çalıştırmaya devam edin ve sınırlı yazılım bakımı ve teknik destek almaya devam etmek için Uzatılmış yaşam döngüsü desteği (PIKSEL) Add-On depoları satın alın (bkz. yükseltmeye yönelik işlem ve fiyatlandırma ayrıntıları).
* Red Hat Enterprise Linux 7 veya 8 ' e geçiş yapabilirsiniz.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Red Hat Enterprise Linux genişletilmiş yaşam döngüsü desteği (PIKSEL) eklentisi kullanımı için ek ücret nedir?
Genişletilmiş yaşam döngüsü desteğiyle ilgili maliyetler, katılmış [biçimde](https://aka.ms/els-form) bulunabilir

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Özel görüntü kullanarak bir VM dağıttım. Bu VM 'ye nasıl genişletilmiş yaşam döngüsü desteği ekleyebilirim?
Doğrudan Red Hat ile iletişim kurmanız ve doğrudan bunlardan destek almanız gerekir.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Özel görüntü kullanarak bir VM dağıttım. Bu VM 'yi bir PAYG VM 'sine dönüştürebilir miyim?
Hayır, şu yapılamıyor. Dönüştürme Şu anda Azure 'da desteklenmiyor.


## <a name="next-steps"></a>Sonraki adımlar

* Azure 'daki RHEL görüntülerinin tam listesini görüntülemek için bkz. [Azure 'da kullanılabilen Red Hat Enterprise Linux (RHEL) görüntüleri](./redhat-imagelist.md).
* Azure Red Hat güncelleştirme altyapısı hakkında daha fazla bilgi edinmek için bkz. [Azure 'da isteğe bağlı RHEL VM 'ler Için Red Hat güncelleştirme altyapısı](./redhat-rhui.md).
* RHEL BYOS teklifi hakkında daha fazla bilgi edinmek için bkz. [Azure 'da kendi aboneliğinizi getir Gold görüntülerini Red Hat Enterprise Linux](./byos.md).
* Tüm RHEL sürümleri için Red Hat destek ilkeleri hakkında daha fazla bilgi için, [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata)' ne bakın.