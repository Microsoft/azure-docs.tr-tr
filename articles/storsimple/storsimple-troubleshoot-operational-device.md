---
title: Dağıtılan bir StorSimple cihazında sorun giderme | Microsoft Docs
description: Şu anda dağıtılmış ve çalışır durumda olan bir StorSimple cihazında oluşan hataların nasıl tanılandığını ve çözüleceğini açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956798"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>İşletimsel StorSimple cihazında sorun giderme
> [!NOTE]
> StorSimple için klasik portal kullanım dışıdır. StorSimple Cihaz Yöneticileriniz, yeni Azure portalına kullanımdan kaldırma zamanlamasına göre otomatik olarak taşınacaktır. Bu taşımayla ilgili bir e-posta ve portal bildirimi alacaksınız. Bu belge de yakında kullanımdan kaldırılacaktır. Taşıma hakkında tüm sorularınız için bkz. [SSS: Azure portalına taşıma](./index.yml).

## <a name="overview"></a>Genel Bakış
Bu makalede, StorSimple cihazınız dağıtıldıktan ve çalışır duruma geçtikten sonra karşılaşabileceğiniz yapılandırma sorunlarını çözmek için yararlı sorun giderme kılavuzu sağlanmıştır. Microsoft Azure StorSimple çalıştırdığınızda karşılaşabileceğiniz sorunları çözmenize yardımcı olmak için sık karşılaşılan sorunları, olası nedenleri ve önerilen adımları açıklar. Bu bilgiler hem StorSimple şirket içi fiziksel cihaz hem de StorSimple Sanal cihazı için geçerlidir.

Bu makalenin sonunda, Microsoft Azure StorSimple işlem sırasında karşılaşabileceğiniz hata kodlarının bir listesini ve hataları çözmek için uygulayabileceğiniz adımları bulabilirsiniz. 

## <a name="setup-wizard-process-for-operational-devices"></a>İşletimsel cihazlar için Kurulum Sihirbazı işlemi
Cihaz yapılandırmasını denetlemek ve gerekirse düzeltici eylemi gerçekleştirmek için Kurulum Sihirbazı 'nı ([Invoke-HcsSetupWizard][1]) kullanabilirsiniz.

Daha önce yapılandırılmış ve işlemsel bir cihazda Kurulum Sihirbazı 'nı çalıştırdığınızda, işlem akışı farklıdır. Yalnızca aşağıdaki girdileri değiştirebilirsiniz:

* IP adresi, alt ağ maskesi ve ağ geçidi
* Birincil DNS sunucusu
* Birincil NTP sunucusu
* İsteğe bağlı Web proxy yapılandırması

Kurulum Sihirbazı, parola toplama ve cihaz kaydı ile ilgili işlemleri gerçekleştirmez.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Kurulum sihirbazının sonraki çalıştırmaları sırasında oluşan hatalar
Aşağıdaki tabloda, işletimsel bir cihazda Kurulum Sihirbazı 'nı çalıştırdığınızda karşılaşabileceğiniz hatalar, hataların olası nedenleri ve bunları çözmek için önerilen işlemler açıklanmaktadır. 

| Hayır. | Hata iletisi veya koşulu | Olası nedenler | Önerilen eylem |
|:--- |:--- |:--- |:--- |
| 1 |Hata 350032: Bu cihaz zaten devre dışı bırakıldı. |Kurulum sihirbazını devre dışı bırakılmış bir cihazda çalıştırırsanız, bu hatayı görürsünüz. |Sonraki adımlar için [Microsoft desteği başvurun](./storsimple-8000-contact-microsoft-support.md) . Devre dışı bırakılmış bir cihaz hizmete geçirilemez. Cihazın yeniden etkinleştirilmesi için önce bir fabrika sıfırlaması gerekebilir. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (HRESULT 'ten özel durum: 0x80070001) |DNS sunucusu güncelleştirmesi başarısız oldu. DNS ayarları genel ayarlardır ve etkinleştirilmiş tüm ağ arabirimlerine uygulanır. |Arabirimi etkinleştirin ve DNS ayarlarını yeniden uygulayın. Bu ayarlar genel olduğundan, bu durum ağı etkin arabirimler için kesintiye uğratabilir. |
| 3 |Bu cihaz StorSimple Yöneticisi hizmet portalında çevrimiçi görünüyor, ancak en düşük kurulumu tamamlayıp yapılandırmayı kaydetmek istediğinizde işlem başarısız olur. |İlk kurulum sırasında, gerçek bir ara sunucu gerçekleşse bile Web proxy yapılandırılmadı. |Hatayı bulmak için [Test-HcsmConnection cmdlet 'ini][2] kullanın. Sorunu düzeltemezsiniz [Microsoft desteği başvurun](./storsimple-8000-contact-microsoft-support.md) . |
| 4 |Invoke-HcsSetupWizard: değer beklenen Aralık içinde değil. |Yanlış bir alt ağ maskesi bu hatayı üretir. Olası nedenler şunlardır: <ul><li> Alt ağ maskesi eksik veya boş.</li><li>IPv6 önek biçimi yanlış.</li><li>Arabirim bulutu etkinleştirdi, ancak ağ geçidi eksik ya da yanlış.</li></ul>VERI 0 ' ın, Kurulum Sihirbazı aracılığıyla yapılandırıldıysa otomatik olarak bulut etkin olduğunu unutmayın. |Sorunu anlamak için 0.0.0.0 veya 256.256.256.256 alt ağını kullanın ve ardından çıkışa bakın. Gerektiğinde alt ağ maskesi, ağ geçidi ve IPv6 öneki için doğru değerleri girin. |

## <a name="error-codes"></a>Hata kodları
Hatalar sayısal sırada listelenir.

| Hata numarası | Hata metni veya açıklaması | Önerilen kullanıcı eylemi |
|:--- |:--- |:--- |
| 10502 |Depolama hesabınıza erişilirken bir hatayla karşılaşıldı. |Birkaç dakika bekleyip yeniden deneyin. Hata devam ederse, sonraki adımlar için lütfen Microsoft Desteği başvurun. |
| 40017 |Yedekleme ilkesinde belirtilen bir birim cihazda bulunamadığı için yedekleme işlemi başarısız oldu. |Yedekleme işlemini yeniden deneyin, hata devam ederse Microsoft Desteği başvurun. Sonraki adımlar için. |
| 40018 |Yedekleme ilkesinde belirtilen birimlerden hiçbiri cihazda bulunamadığı için yedekleme işlemi başarısız oldu. |Yedekleme işlemini yeniden deneyin, hata devam ederse Microsoft Desteği başvurun. Sonraki adımlar için. |
| 390061 |Sistem meşgul veya kullanılamıyor. |Birkaç dakika bekleyip yeniden deneyin. Hata devam ederse, sonraki adımlar için lütfen Microsoft Desteği başvurun. |
| 390143 |390143 hata koduyla bir hata oluştu. (Bilinmeyen hata.) |Hata devam ederse, sonraki adımlar için lütfen Microsoft Desteği başvurun. |

## <a name="next-steps"></a>Sonraki adımlar
Sorunu çözemezse yardım için [Microsoft desteği başvurun](./storsimple-8000-contact-microsoft-support.md) . 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)