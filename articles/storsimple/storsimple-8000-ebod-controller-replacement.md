---
title: StorSimple 8600 EBOD denetleyicisini değiştirme | Microsoft Docs
description: StorSimple 8600 cihazındaki EBOD denetleyicilerinin bir veya her ikisinin de nasıl kaldırılacağını ve değiştirileceğini açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014848"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>StorSimple cihazınızda bir EBOD denetleyicisini değiştirme

## <a name="overview"></a>Genel Bakış
Bu öğreticide, Microsoft Azure StorSimple cihazınızda bir hatalı EBOD denetleyici modülünün nasıl değiştirileceği açıklanmaktadır. Bir EBOD denetleyici modülünü değiştirmek için şunları yapmanız gerekir:

* Hatalı EBOD denetleyicisini kaldır
* Yeni bir EBOD denetleyicisi yükler

Başlamadan önce aşağıdaki bilgileri göz önünde bulundurun:

* Boş EBOD modülleri kullanılmayan tüm yuvalara eklenmelidir. Yuva açık bırakılırsa, kutu düzgün şekilde seyrek olmaz.
* EBOD denetleyicisi etkin bir şekilde değiştirilebilir ve kaldırılabilir veya değiştirilebilir. Bir değişikliği yapana kadar başarısız olan modülü kaldırmayın. Değiştirme işlemini başlattığınızda, 10 dakika içinde bitmelidir.

> [!IMPORTANT]
> Herhangi bir StorSimple bileşenini kaldırmaya veya değiştirmeye çalışmadan önce, [Güvenlik simgesi kurallarını](storsimple-8000-safety.md#safety-icon-conventions) ve diğer [güvenlik önlemlerini](storsimple-8000-safety.md)gözden geçirdiğinizden emin olun.

## <a name="remove-an-ebod-controller"></a>EBOD denetleyicisini kaldırma
StorSimple cihazınızda başarısız olan EBOD denetleyici modülünü değiştirmeden önce, diğer EBOD denetleyici modülünün etkin ve çalışır durumda olduğundan emin olun. Aşağıdaki yordam ve tablo, EBOD denetleyici modülünün nasıl kaldırılacağını açıklamaktadır.

#### <a name="to-remove-an-ebod-module"></a>EBOD modülünü kaldırmak için
1. Azure portalını açın.
2. Cihazınıza gidin ve **Ayarlar**  >  **donanım sistem durumu**' na gidin ve etkin ebod denetleyicisi modülü için LED durumunun yeşil olduğunu ve başarısız ebod denetleyici modülünün ışığını kırmızı olduğunu doğrulayın.
3. Cihazın arkasında başarısız olan EBOD denetleyici modülünü bulun.
4. Ebod denetleyicisi modülünü sistem dışına çıkarmadan önce, denetleyiciye bağlayan kabloları kaldırın.
5. Denetleyiciye bağlı olan EBOD denetleyici modülünün tam SAS bağlantı noktasını not edin. EBOD modülünü değiştirdikten sonra sistemi bu yapılandırmaya geri yüklemeniz gerekecektir.
   
   > [!NOTE]
   > Genellikle, bu bağlantı noktası olur ve aşağıdaki diyagramda **ana bilgisayar** olarak etiketlenir.
   
    ![EBOD denetleyicisi 'nin geri düzlemi](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Şekil 1** EBOD modülünün geri dönmesi
   
   | Etiketle | Açıklama |
   |:--- |:--- |
   | 1 |Hata ışığı |
   | 2 |Güç ışığı |
   | 3 |SAS bağlayıcıları |
   | 4 |SAS LED 'Leri |
   | 5 |Yalnızca fabrika kullanımı için seri bağlantı noktaları |
   | 6 |Bağlantı noktası A (ana bilgisayar) |
   | 7 |Bağlantı noktası B (ana bilgisayar) |
   | 8 |Bağlantı noktası C (yalnızca fabrika kullanımı) |

## <a name="install-a-new-ebod-controller"></a>Yeni bir EBOD denetleyicisi yükler
Aşağıdaki yordam ve tabloda, StorSimple cihazınıza bir EBOD denetleyici modülünün nasıl yükleneceği açıklanmaktadır.

#### <a name="to-install-an-ebod-controller"></a>EBOD denetleyicisi yüklemek için
1. Özellikle arabirim bağlayıcısına, EBOD cihazını hasar için denetleyin. PIN 'ler Bükülü ise, yeni EBOD denetleyicisini yüklemeyin.
2. Açık konumdaki çim sayesinde, bu modül, bu, modülün katılımını yapana kadar, modülün yanına kaydırın.
   
    ![EBOD denetleyicisi yükleniyor](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Şekil 2**  EBOD denetleyici modülünü yükleme
3. Mandalı kapatın. Mandal olarak bir tıklama duymalısınız.
   
    ![EBOD mandalı bırakılıyor](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Şekil 3**  EBOD modül mandalı kapatılıyor
4. Kabloları yeniden bağlayın. Değiştirme işleminden önce mevcut olan doğru yapılandırmayı kullanın. Kabloların nasıl bağlanacağı hakkındaki ayrıntılar için aşağıdaki diyagrama ve tabloya bakın.
   
    ![4U cihazınızın güç bağlantısını yapın](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Şekil 4**. Kablolar yeniden bağlanıyor
   
   | Etiketle | Açıklama |
   |:--- |:--- |
   | 1 |Birincil kutu |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Denetleyici 0 |
   | 5 |Denetleyici 1 |
   | 6 |EBOD denetleyicisi 0 |
   | 7 |EBOD denetleyicisi 1 |
   | 8 |EBOD Kasası |
   | 9 |Güç dağıtımı birimleri |

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple donanım bileşeni değiştirme](storsimple-8000-hardware-component-replacement.md)hakkında daha fazla bilgi edinin.

