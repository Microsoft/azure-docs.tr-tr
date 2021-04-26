---
title: StorSimple Sanal dizisi güncelleştirme 0,6 sürüm notları | Microsoft Docs
description: Güncelleştirme 0,6 çalıştıran StorSimple Sanal dizisine yönelik kritik açık sorunlar ve çözümleri açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000921"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Sanal dizisi güncelleştirme 0,6 sürüm notları

## <a name="overview"></a>Genel Bakış

Aşağıdaki sürüm notları, Microsoft Azure StorSimple Sanal dizi güncelleştirmelerine yönelik kritik açık sorunları ve çözümlenen sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir ve geçici bir çözüm gerektiren kritik sorunlar keşfedildiğinde eklenirler. StorSimple Sanal dizinizi dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Güncelleştirme 0,6, yazılım sürümü **10.0.10293.0**'e karşılık gelir.

> [!IMPORTANT]
> - Güncelleştirmeler kesintiye uğratan ve cihazınızı yeniden başlatacak. G/ç devam ediyorsa, cihaz kapalı kalma süresini doğurur. Güncelleştirmeyi uygulama hakkında ayrıntılı yönergeler için [güncelleştirme 0,6](storsimple-virtual-array-install-update-06.md)' a gidin.
>
> - Güncelleştirme 0,6 ' i, kritik güvenlik düzeltmeleri içerdiğinden hemen yüklemenizi öneririz.


## <a name="whats-new-in-the-update-06"></a>Güncelleştirme 0,6 ' deki yenilikler
Güncelleştirme 0,6 kritik bir güncelleştirmedir ve hemen dağıtılmalıdır. Bu güncelleştirme aşağıdaki düzeltmeleri içerir: 

- **Windows Güvenlik düzeltmeleri** -bu sürümde **Windows kritik güvenlik düzeltmeleri** vardır. Güvenlik sorunları ve ilgili düzeltmeler hakkında daha fazla bilgi için aşağıdaki güvenlik güncelleştirmelerini gözden geçirin:
    - [Windows 8.1 ve Windows Server 2012 R2 için Aralık 2016 güvenlik yalnızca kalite güncelleştirmesi](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Windows 8.1 ve Windows Server 2012 R2 için Mart 2017 güvenlik yalnızca kalite güncelleştirmesi](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 Mayıs 2017 — KB4019213 (yalnızca güvenlik güncelleştirmesi)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Geri yükleme onarma** -önceki sürümlerde geri yüklemenin tamamlanmasını engelleyecek bir hata oluştu. Bu hata bu sürümde düzeltildi.


## <a name="issues-fixed-in-the-update-06"></a>Güncelleştirme 0,6 ' de düzeltilen sorunlar

Aşağıdaki tabloda, bu sürümde düzeltilen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun |
| --- | --- | --- |
| 1 |Güvenlik| Bu sürüm, kritik Windows güvenlik güncelleştirmeleri içerir. Bu güncelleştirmeyi hemen yüklemenizi öneririz.|
| 2 |Geri Yükleme| Geri yükleme sırasında geri yükleme işinin tamamlanmasını engelleyecek bir yarış durumu vardı. Hata düzelttiğinde bu yarış durumu ele alınmaktadır.|


## <a name="known-issues-in-the-update-06"></a>Güncelleştirme 0,6 ' de bilinen sorunlar

Aşağıdaki tabloda, StorSimple Sanal dizisi için bilinen sorunların özeti verilmiştir ve önceki sürümlerden gelen sorunlar sürümü dahildir.

| Hayır. | Özellik | Sorun | Geçici çözüm/açıklamalar |
| --- | --- | --- | --- |
| **1.** |Güncelleştirmeler |Önizleme sürümünde oluşturulan sanal cihazlar desteklenen bir genel kullanılabilirlik sürümüne güncelleştirilemez. |Bu sanal cihazlar, olağanüstü durum kurtarma (DR) iş akışı kullanan genel kullanılabilirlik sürümü için yük devretmelidir. |
| **2.** |Sağlanan veri diski |Belirtilen boyutta bir veri diski sağladıktan ve karşılık gelen StorSimple Sanal cihazını oluşturduktan sonra, veri diskini genişletmemelidir veya küçültmemelidir. Bu durum, cihazın yerel katmanlarında tüm verilerin kaybedilmesine neden olur. | |
| **3.** |Grup İlkesi |Bir cihaz etki alanına katılmış olduğunda, bir grup ilkesi uygulandığında cihaz işlemi olumsuz etkilenebilir. |Sanal diziniz Active Directory için kendi kuruluş biriminde (OU) olduğundan ve buna hiçbir Grup İlkesi nesnesi (GPO) uygulanmadığından emin olun. |
| **4.** |Yerel Web Kullanıcı arabirimi |Internet Explorer 'da (IE ESC) Artırılmış güvenlik özellikleri etkinse, sorun giderme veya bakım gibi bazı yerel Web Kullanıcı arabirimi sayfaları düzgün çalışmayabilir. Bu sayfalardaki düğmeler de çalışmayabilir. |Internet Explorer 'da gelişmiş güvenlik özelliklerini devre dışı bırakın. |
| **e.** |Yerel Web Kullanıcı arabirimi |Hyper-V sanal makinesinde, Web Kullanıcı arabirimindeki ağ arabirimleri 10 Gbps arabirim olarak görüntülenir. |Bu davranış, Hyper-V ' y i bir yansıdır. Hyper-V, sanal ağ bağdaştırıcıları için her zaman 10 Gbps gösterir. |
| **inç.** |Katmanlı birimler veya paylaşımlar |StorSimple katmanlı birimlerle çalışan uygulamalar için bayt aralığı kilitleme desteklenmez. Bayt aralığı kilitleme etkinse, StorSimple katmanlama çalışmaz. |Önerilen ölçüler şunlardır: <br></br>Uygulama mantığınızdaki bayt aralığı kilitlemeyi devre dışı bırakın.<br></br>Bu uygulamaya yönelik verileri katmanlı birimlerin aksine yerel olarak sabitlenmiş birimlerde koymak için seçin.<br></br>*Desteklenmediği uyarısıyla*: yerel olarak sabitlenmiş birimler kullanılırken ve bayt aralığı kilitleme etkinleştirildiğinde, geri yükleme tamamlanmadan önce yerel olarak sabitlenmiş birim çevrimiçi olabilir. Bu tür örneklerde, bir geri yükleme işlemi devam ediyorsa geri yüklemenin tamamlanmasını beklemeniz gerekir. |
| **7.** |Katmanlı paylaşımlar |Büyük dosyalarla çalışma, yavaş katmana neden olabilir. |Büyük dosyalarla çalışırken, en büyük dosyanın paylaşma boyutunun %3 ' inden küçük olmasını öneririz. |
| **240.** |Paylaşımlar için kullanılan kapasite |Paylaşımda veri olmadığında paylaşma tüketimi görebilirsiniz. Bu tüketim, paylaşımlar için kullanılan kapasitenin meta verileri içermesine yöneliktir. | |
| **tuşlarına.** |Olağanüstü durum kurtarma |Yalnızca bir dosya sunucusunun olağanüstü durum kurtarma işlemini kaynak cihazla aynı etki alanına gerçekleştirebilirsiniz. Bu sürümde, başka bir etki alanındaki hedef cihaza olağanüstü durum kurtarma desteklenmiyor. |Bu, sonraki bir sürümde uygulanır. Daha fazla bilgi için, [StorSimple Sanal diziniz Için yük devretme ve olağanüstü durum kurtarma](storsimple-virtual-array-failover-dr.md) bölümüne gidin |
| **(.** |Azure PowerShell |StorSimple Sanal cihazları bu sürümdeki Azure PowerShell tarafından yönetilemez. |Sanal cihazların tüm yönetimi Azure portal ve yerel Web Kullanıcı arabirimi aracılığıyla yapılmalıdır. |
| **üst.** |Parola değiştirme |Sanal dizi cihaz konsolu yalnızca en-US klavye biçimindeki girişi kabul eder. | |
| **+.** |CHAP |Oluşturulduktan sonra CHAP kimlik bilgileri kaldırılamıyor. Ayrıca, CHAP kimlik bilgilerini değiştirirseniz, bu birimleri çevrimdışına almanız ve değişikliğin etkili olması için bunları çevrimiçi duruma getirmeniz gerekir. |Bu sorun, sonraki bir sürümde giderilmiştir. |
| **hatası.** |Iscsı sunucusu |Bir Iscsı birimi için görüntülenmiş ' kullanılan depolama alanı, StorSimple Device Manager hizmetinde ve Iscsı konağında farklı olabilir. |Iscsı konağında dosya sistemi görünümü bulunur.<br></br>Cihaz, birim en büyük boyuttan ayrıldığı blokları görür. |
| **May.** |Dosya sunucusu |Bir klasördeki bir dosya ile ilişkili alternatif veri akışı (ADS) varsa, reklamlar olağanüstü durum kurtarma, kopyalama ve öğe düzeyinde kurtarma aracılığıyla yedeklenmez veya geri yüklenmez. | |
| **aşamaz.** |Dosya sunucusu |Sembolik bağlantılar desteklenmiyor. | |
| **k.** |Dosya sunucusu |StorSimple Sanal dizisi dosya sunucusu üzerinde kopyalanırken veya depolanırken Windows şifreleme dosya sistemi (EFS) tarafından korunan dosyalar desteklenmeyen bir yapılandırmayla sonuçlanır.  | |
| **17.** |Güncelleştirmeler |Yerel kullanıcı arabiriminden bir düzeltme yüklemeye çalışırken hata kodu: 2359302 (onaltılı 0x240006) görürseniz, bu düzeltmenin cihazınızda zaten yüklü olduğunu gösterir.   | |

## <a name="next-step"></a>Sonraki adım
StorSimple Sanal diziniz üzerinde [0,6 güncelleştirmesini yükler](storsimple-virtual-array-install-update-06.md) .

## <a name="references"></a>Başvurular
Daha eski bir sürüm notuna mi bakıyorsunuz? Şuraya gidin:

* [StorSimple Sanal dizisi güncelleştirme 0,5 sürüm notları](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,4 sürüm notları](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,3 sürüm notları](storsimple-ova-update-03-release-notes.md)
* [StorSimple Sanal dizisi güncelleştirme 0,1 ve 0,2 sürüm notları](storsimple-ova-update-01-release-notes.md)
* [StorSimple Sanal dizisi genel kullanılabilirlik sürüm notları]()