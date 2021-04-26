---
title: StorSimple Sanal dizisi güncelleştirme 0,2 & 0,1 sürüm notları
description: Güncelleştirme 0,2 ve 0,1 çalıştıran StorSimple Sanal dizisine yönelik kritik açık sorunlar ve çözümleri açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966165"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Sanal dizisi güncelleştirme 0,2 ve 0,1 sürüm notları
## <a name="overview"></a>Genel Bakış
Aşağıdaki sürüm notları, Microsoft Azure StorSimple Sanal dizi güncelleştirmelerine yönelik kritik açık sorunları ve çözümlenen sorunları belirler. (Microsoft Azure StorSimple Sanal dizi, StorSimple şirket içi sanal cihaz veya StorSimple Sanal cihazı olarak da bilinir.) 

Sürüm notları sürekli olarak güncelleştirilir ve geçici bir çözüm gerektiren kritik sorunlar keşfedildiğinde eklenirler. StorSimple Sanal cihazınızı dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Güncelleştirme 0,2, yazılım sürümü **10.0.10280.0**'a karşılık gelir; Güncelleştirme 0,1, sürüm **10.0.10279.0**. Aşağıdaki bölümlerde her güncelleştirme için değişiklikler listelenmiştir. 

> [!NOTE]
> Güncelleştirmeler karışıklığa neden olur ve cihazınızı yeniden başlatır. G/ç devam ediyorsa, cihaz kapalı kalma süresine neden olur.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Güncelleştirme 0,2 ' de düzeltilen sorunlar
Güncelleştirme 0,2, aşağıdaki tabloda açıklanan düzeltmeyle Ayrıca güncelleştirme 0,1 ' deki tüm değişiklikleri içerir:

| Özellik | Sorun |
| --- | --- |
| Güncelleştirmeler |Son sürümde, güncelleştirmeler klasik Azure portalında otomatik olarak algılanmadı, bu nedenle güncelleştirmeleri yüklemek için yerel Web Kullanıcı arabirimini kullanmanız gerekiyordu. Bu sorun bu sürümde düzeltilmiştir. Güncelleştirme 0,2 ' i yükledikten sonra, klasik Azure portalını kullanarak gelecekteki güncelleştirmeleri yükleyebilirsiniz. |

## <a name="whats-new-in-the-update-01"></a>Güncelleştirme 0,1 ' deki yenilikler
Güncelleştirme 0,1 aşağıdaki hata düzeltmelerini ve geliştirmeleri içerir. 

* **Bulut kesintileri Için iyileştirilmiş dayanıklılık**: Bu sürümde, bir bulut bağlantısı kesintisi durumunda olağanüstü durum kurtarma, yedekleme, geri yükleme ve katmanlama gibi çeşitli hata düzeltmeleri vardır. 
* **İyileştirilmiş geri yükleme performansı**: Bu sürümde, geri yükleme işlerinin tamamlanma süresini önemli ölçüde geçiren hata düzeltmeleri vardır.
* **Otomatik Space geri kazanma iyileştirmesi**: ölçülü kaynak sağlanan birimlerde veri silindiğinde kullanılmayan depolama bloklarının geri kazanılır olması gerekir. Bu sürüm, kullanılmayan alanın, önceki sürümlere kıyasla daha hızlı kullanılabilir hale gelmesine neden olan alanın geri kazanma işlemini buluttan geliştirmiştir.
* **Yeni sanal disk görüntüleri**: yeni VHD, VHDX ve VMDK, klasik Azure portalı aracılığıyla kullanıma sunulmuştur. Bu görüntüleri, yeni güncelleştirme 0,1 cihazları sağlamak için indirebilirsiniz.
* **Portalda işlerin doğruluğunu iyileştirmek**: yazılımın önceki sürümünde, portalda iş durumu raporlaması ayrıntılı değildi. Bu sorun, bu yayında çözümlenir.
* **Etki alanına katılma deneyimi**: cihazın etki alanına katılması ve yeniden adlandırılması ile ilgili hata düzeltmeleri.

## <a name="issues-fixed-in-the-update-01"></a>Güncelleştirme 0,1 ' de düzeltilen sorunlar
Aşağıdaki tabloda, bu sürümde düzeltilen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun |
| --- | --- | --- |
| 1 |VMDK |Bazı VMware sürümlerinde, işletim sistemi diski, uyarılara neden olan seyrek, normal işlemleri kesintiye neden olarak görüldü. Bu sürümde düzeltildi. |
| 2 |Iscsı sunucusu |Son sürümde, kullanıcının StorSimple Sanal cihazınızın etkin olan her ağ arabirimi için bir ağ geçidi belirtmesi gerekiyordu. Bu davranış, kullanıcının etkinleştirilmiş tüm ağ arabirimleri için en az bir ağ geçidi yapılandırması gereken şekilde bu sürümde değiştirilmiştir. |
| 3 |Destek paketi |Yazılımın önceki sürümünde, paket boyutları 1 GB 'den büyük olduğunda destek paketi koleksiyonu başarısız oldu. Bu sorun bu sürümde düzeltilmiştir. |
| 4 |Bulut erişimi |Son sürümde, StorSimple Sanal dizisinin ağ bağlantısı yoksa ve yeniden başlatıldıysa, yerel kullanıcı arabiriminde bağlantı sorunları olur. Bu sorun bu sürümde düzeltilmiştir. |
| 5 |İzleme grafikleri |Önceki sürümde, cihaz yük devretmesi sonrasında, bulut kapasitesi kullanım grafikleri klasik Azure portalında yanlış değerler olarak görüntülendi. Bu, geçerli sürümde düzeltilmiştir. |

## <a name="known-issues-in-the-update-01"></a>Güncelleştirme 0,1 ' de bilinen sorunlar
Aşağıdaki tabloda, StorSimple Sanal dizisi için bilinen sorunların özeti verilmiştir ve önceki sürümlerden gelen sorunlar sürümü dahildir. **Bu sürümde not edilen sorunlar sürümü bir yıldız işaretiyle işaretlenir. Bu listedeki tüm sorunlar, StorSimple Sanal dizisinin GA sürümünden daha fazla taşınır.**

| Hayır. | Özellik | Sorun | Geçici çözüm/açıklamalar |
| --- | --- | --- | --- |
| **1.** |Güncelleştirmeler |Önizleme sürümünde oluşturulan sanal cihazlar desteklenen bir genel kullanılabilirlik sürümüne güncelleştirilemez. |Bu sanal cihazlar, olağanüstü durum kurtarma (DR) iş akışı kullanan genel kullanılabilirlik sürümü için yük devretmelidir. |
| **2.** |Sağlanan veri diski |Belirtilen boyutta bir veri diski sağladıktan ve karşılık gelen StorSimple Sanal cihazını oluşturduktan sonra, veri diskini genişletmemelidir veya küçültmemelidir. Bunun denenmesi, cihazın yerel katmanlarında tüm verilerin kaybedilmesine neden olur. | |
| **3.** |Grup İlkesi |Bir cihaz etki alanına katılmış olduğunda, bir grup ilkesi uygulandığında cihaz işlemi olumsuz etkilenebilir. |Sanal diziniz Active Directory için kendi kuruluş biriminde (OU) olduğundan ve buna hiçbir Grup İlkesi nesnesi (GPO) uygulanmadığından emin olun. |
| **4.** |Yerel Web Kullanıcı arabirimi |Internet Explorer 'da (IE ESC) Artırılmış güvenlik özellikleri etkinse, sorun giderme veya bakım gibi bazı yerel Web Kullanıcı arabirimi sayfaları düzgün çalışmayabilir. Bu sayfalardaki düğmeler de çalışmayabilir. |Internet Explorer 'da gelişmiş güvenlik özelliklerini devre dışı bırakın. |
| **e.** |Yerel Web Kullanıcı arabirimi |Hyper-V sanal makinesinde, Web Kullanıcı arabirimindeki ağ arabirimleri 10 Gbps arabirim olarak görüntülenir. |Bu davranış, Hyper-V ' y i bir yansıdır. Hyper-V, sanal ağ bağdaştırıcıları için her zaman 10 Gbps gösterir. |
| **inç.** |Katmanlı birimler veya paylaşımlar |StorSimple katmanlı birimlerle çalışan uygulamalar için bayt aralığı kilitleme desteklenmez. Bayt aralığı kilitleme etkinse, StorSimple katmanlama çalışmayacaktır. |Önerilen ölçüler şunlardır: <br></br>Uygulama mantığınızdaki bayt aralığı kilitlemeyi devre dışı bırakın.<br></br>Bu uygulamaya yönelik verileri katmanlı birimlerin aksine yerel olarak sabitlenmiş birimlerde koymak için seçin.<br></br>*Desteklenmediği uyarısıyla*: yerel olarak sabitlenmiş birimler kullanılıyorsa ve bayt aralığı kilitleme etkinse, geri yükleme tamamlanmadan önce, yerel olarak sabitlenmiş birimin çevrimiçi olduğunu unutmayın. Bu tür örneklerde, bir geri yükleme işlemi devam ediyorsa geri yüklemenin tamamlanmasını beklemeniz gerekir. |
| **7.** |Katmanlı paylaşımlar |Büyük dosyalarla çalışma, yavaş katmana neden olabilir. |Büyük dosyalarla çalışırken, en büyük dosyanın paylaşma boyutunun %3 ' inden küçük olmasını öneririz. |
| **240.** |Paylaşımlar için kullanılan kapasite |Paylaşımdaki tüm verilerin yokluğunda paylaşma kullanımını görebilirsiniz. Bunun nedeni, paylaşımlar için kullanılan kapasitenin meta verileri içerir. | |
| **tuşlarına.** |Olağanüstü durum kurtarma |Yalnızca bir dosya sunucusunun olağanüstü durum kurtarma işlemini kaynak cihazla aynı etki alanına gerçekleştirebilirsiniz. Bu sürümde, başka bir etki alanındaki hedef cihaza olağanüstü durum kurtarma desteklenmiyor. |Bu, sonraki bir sürümde uygulanır. |
| **(.** |Azure PowerShell |StorSimple Sanal cihazları bu sürümdeki Azure PowerShell tarafından yönetilemez. |Sanal cihazların tüm yönetimi, klasik Azure portalı ve yerel Web Kullanıcı arabirimi aracılığıyla yapılmalıdır. |
| **üst.** |Parola değiştirme |Sanal dizi cihaz konsolu yalnızca en-US klavye biçimindeki girişi kabul eder. | |
| **+.** |CHAP |Oluşturulduktan sonra CHAP kimlik bilgileri kaldırılamıyor. Ayrıca, CHAP kimlik bilgilerini değiştirirseniz, bu birimleri çevrimdışına almanız ve değişikliğin etkili olması için bunları çevrimiçi duruma getirmeniz gerekir. |Bunlar sonraki bir sürümde giderilecektir. |
| **hatası.** |Iscsı sunucusu |Bir Iscsı birimi için görünen ' kullanılan depolama alanı, StorSimple Yöneticisi hizmeti ve Iscsı ana bilgisayarında farklı olabilir. |Iscsı konağında dosya sistemi görünümü bulunur.<br></br>Cihaz, birim en büyük boyuttan ayrıldığı blokları görür. |
| **May.** |Dosya sunucusu * |Bir klasördeki bir dosya ile ilişkili alternatif veri akışı (ADS) varsa, reklamlar olağanüstü durum kurtarma, kopyalama ve öğe düzeyinde kurtarma aracılığıyla yedeklenmez veya geri yüklenmez. | |

## <a name="next-step"></a>Sonraki adım
StorSimple Sanal diziniz üzerinde [güncelleştirmeleri yükler](./storsimple-virtual-array-install-update-06.md) .