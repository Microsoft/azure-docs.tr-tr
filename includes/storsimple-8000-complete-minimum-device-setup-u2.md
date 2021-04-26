---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028530"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>En düşük StorSimple cihaz kurulumunu tamamlamak için

   > [!NOTE]
   > En düşük cihaz kurulumu tamamlandıktan sonra cihaz adını değiştiremezsiniz.
   
1. **Cihazlar** dikey penceresindeki tablosal cihaz listesinden cihazınızı seçmek için tıklayın. Cihaz **Kuruluma hazır** durumdadır. **Cihazı yapılandır** dikey penceresi açılır.

     ![StorSimple en düşük cihaz kurulumu ağ arabirimleri](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. **Cihazı yapılandır** dikey penceresinde:
   
   1. Cihazınızın **kolay adını** sağlayın. Varsayılan cihaz adı, cihaz modeli ve seri numarası gibi bilgileri yansıtır. Cihazı yönetmek için En fazla 64 karakterlik bir kolay ad atayabilirsiniz.
   2. Cihazın dağıtıldığı coğrafi konum temelinde **saat dilimini** ayarlayın. Cihazınız zamanlanan tüm işlemler için bu saat dilimini kullanır.
   3. **DATA 0 ayarları** altında:

       1. DATA 0 ağ arabiriminiz, kurulum sihirbazı aracılığıyla yapılandırılan ağ ayarlarıyla (IP, alt ağ, ağ geçidi) etkinleştirilmiş olarak görünür. iSCSI ile birlikte DATA 0 da bulut için otomatik olarak etkinleştirilir.

       2. Denetleyici 0 ve Denetleyici 1 için sabit IP adreslerini sağlayın. **Denetleyici sabit IP adreslerinin, cihaz IP adresinin erişebildiği alt ağda boş IP’ler olması gerekir.** DATA 0 arabirimi IPv4 için yapılandırılmışsa sabit IP adreslerinin IPv4 biçiminde verilmesi gerekir. IPv6 yapılandırması için bir önek sağladıysanız bu alanlar otomatik olarak sabit IP adresleriyle doldurulur.

            ![StorSimple en düşük cihaz kurulumu ağ arabirimleri 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Denetleyiciye yönelik sabit IP adresleri, cihaza güncelleştirmelerin uygulanması ve çöp toplama için kullanılır. Bu nedenle, sabit IP’lerin yönlendirilebilir ve İnternet’e bağlanabilir olması gerekir. [Test-HcsmConnection][Test] cmdlet'ini kullanarak sabit denetleyici IP'lerinizin yönlendirilebilir olup olmadığını denetleyebilirsiniz. Aşağıdaki örnekte sabit denetleyici IP'lerin İnternet'e yönlendirildiği ve Microsoft Update sunucularına erişebildiği gösterilmektedir.

            ![Yönlendirilebilir IP’leri gösteren Test HcsmConnection](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. **Tamam**'a tıklayın. Cihaz yapılandırması başlar. Cihaz yapılandırması tamamlandığında size bildirilir. **Cihazlar** dikey penceresinde cihazın durumu **Çevrimiçi** olarak değişir.

    ![StorSimple en düşük cihaz kurulumu ağ arabirimleri 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)