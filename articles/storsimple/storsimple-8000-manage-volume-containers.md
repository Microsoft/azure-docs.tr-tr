---
title: StorSimple 8000 serisi cihazlar için birim kapsayıcılarını yönetme
description: Bir birim kapsayıcısı eklemek, değiştirmek veya silmek için StorSimple Device Manager hizmet birimi kapsayıcıları sayfasını nasıl kullanabileceğinizi açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545392"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>StorSimple birim kapsayıcılarını yönetmek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış
Bu öğreticide, StorSimple birim kapsayıcıları oluşturmak ve yönetmek için StorSimple Device Manager hizmetinin nasıl kullanılacağı açıklanmaktadır.

Microsoft Azure StorSimple cihazdaki bir birim kapsayıcısı, depolama hesabı, şifreleme ve bant genişliği tüketim ayarlarını paylaşan bir veya daha fazla birim içerir. Bir cihazın tüm birimleri için birden çok birim kapsayıcısı olabilir. 

Bir birim kapsayıcısı aşağıdaki özniteliklere sahiptir:

* **Birimler** : birim kapsayıcısı içinde yer alan katmanlı veya yerel olarak sabitlenmiş StorSimple birimleri. 
* **Şifreleme** : her birim kapsayıcısı için tanımlanabilir bir şifreleme anahtarı. Bu anahtar, StorSimple cihazınızdan buluta gönderilen verileri şifrelemek için kullanılır. Kullanıcı tarafından girilen anahtarla bir askeri sınıf AES-256 bit anahtarı kullanılır. Verilerinizin güvenliğini sağlamak için bulut depolama şifrelemesini her zaman etkinleştirmenizi öneririz.
* **Depolama hesabı** : verileri depolamak Için kullanılan Azure depolama hesabı. Bir birim kapsayıcısında bulunan tüm birimler bu depolama hesabını paylaşır. Mevcut bir listeden bir depolama hesabı seçebilir veya birim kapsayıcısını oluşturup bu hesabın erişim kimlik bilgilerini belirttiğinizde yeni bir hesap oluşturabilirsiniz.
* **Bulut bant genişliği** : cihazdaki veriler buluta gönderilirken cihaz tarafından tüketilen bant genişliği. Bu kapsayıcıyı oluştururken 1 Mbps ile 1.000 Mbps arasında bir değer belirterek bir bant genişliği denetimi uygulayabilirsiniz. Cihazın kullanılabilir tüm bant genişliğini kullanmasını istiyorsanız bu alanı **sınırsız** olarak ayarlayın. Ayrıca, zamanlamaya göre bant genişliği ayırmak için bir bant genişliği şablonu oluşturabilir ve uygulayabilirsiniz.

Aşağıdaki yordamlarda, StorSimple **birim kapsayıcıları** dikey penceresinin aşağıdaki genel işlemleri tamamlaması için nasıl kullanılacağı açıklanmaktadır:

* Birim kapsayıcısı ekleme
* Birim kapsayıcısını değiştirme
* Birim kapsayıcısını silme

## <a name="add-a-volume-container"></a>Birim kapsayıcısı ekleme
Birim kapsayıcısı eklemek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Birim kapsayıcısını değiştirme
Bir birim kapsayıcısını değiştirmek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Birim kapsayıcısını silme
Birim kapsayıcısının içindeki birimleri vardır. Yalnızca içinde yer alan tüm birimler ilk silinmişse silinebilir. Bir birim kapsayıcısını silmek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple birimlerini yönetme](storsimple-8000-manage-volumes-u2.md)hakkında daha fazla bilgi edinin. 
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.

