---
title: StorSimple 8000 serisi güncelleştirme 2,2 sürüm notları | Microsoft Docs
description: StorSimple 8000 serisi güncelleştirme 2,2 için yeni özellikleri, sorunları ve geçici çözümleri açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956611"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 serisi güncelleştirme 2,2 sürüm notları

## <a name="overview"></a>Genel Bakış
Aşağıdaki sürüm notları, yeni özellikleri anlatmaktadır ve StorSimple 8000 serisi güncelleştirme 2,2 için kritik açık sorunları belirler. Ayrıca, bu sürüme dahil olan StorSimple yazılım güncelleştirmelerinin bir listesini içerir.

Güncelleştirme 2,2, sürüm (GA) çalıştıran herhangi bir StorSimple cihaza veya güncelleştirme 2,1 ' ye güncelleştirme 0,1 ' e uygulanabilir. Güncelleştirme 2,2 ile ilişkili cihaz sürümü 6.3.9600.17708.

Güncelleştirmeyi StorSimple çözümünüzde dağıtmadan önce lütfen sürüm notlarında yer alan bilgileri gözden geçirin.

> [!IMPORTANT]
> * Güncelleştirme 2,2 ' de yalnızca yazılım güncelleştirmeleri vardır. Bu güncelleştirmeyi yüklemek yaklaşık 1,5-2 saat sürer. 
> * Güncelleştirme 2,1 çalıştırıyorsanız, güncelleştirme 2,2 ' yi mümkün olan en kısa sürede uygulamanızı öneririz.
> * Güncelleştirmelerin aşamalı olarak piyasaya sürültiğimiz için yeni sürümler için güncelleştirmeleri hemen göremeyebilirsiniz. Birkaç gün bekleyin ve bu süre yakında kullanılabilir hale gelecağından güncelleştirmeleri yeniden tarayın.
> 
> 

## <a name="whats-new-in-update-22"></a>Güncelleştirme 2,2 ' deki yenilikler
Güncelleştirme 2,2 ' de aşağıdaki temel geliştirmeler yapılmıştır.

* **Otomatik Space geri kazanma iyileştirmesi** – veriler ölçülü kaynak sağlanan birimlerde silindiğinde, kullanılmayan depolama bloklarının geri kazanımı gerekir. Bu sürüm, kullanılmayan alanın, önceki sürümlere kıyasla daha hızlı kullanılabilir hale gelmesine neden olan alanın geri kazanma işlemini buluttan geliştirmiştir.
* **Snapshot performans iyileştirmeleri** – güncelleştirme 2,2, büyük birimlerin kullanıldığı ve veri karmaşası için en az düzeyde olan belirli senaryolarda bir bulut anlık görüntüsünü işleme süresini iyileştirmiştir. Bu geliştirmelerden faydalanabilir bir senaryo arşiv birimleri olacaktır.
* **Destek paketi toplamayı sağlamlaştırma** – destek paketinin bu sürümde toplanması ve karşıya yüklenmesi gibi iyileştirmeler yapılmıştır. 
* **Güvenilirlik geliştirmelerini güncelleştirme** – bu sürümde, geliştirilmiş güncelleştirme güvenilirliğiyle sonuçlanan hata düzeltmeleri vardır.

## <a name="issues-fixed-in-update-22"></a>Güncelleştirme 2,2 ' de düzeltilen sorunlar
Aşağıdaki tablolarda, 2,2 ve 2,1 güncelleştirmelerinde düzeltilen sorunların özeti verilmiştir.    

| Hayır | Özellik | Sorun | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- |
| 1 |Konak performansı |Önceki sürümde, ana bilgisayar tarafı performans sorunları yerel olarak sabitlenmiş bir birimin oluşturulması sırasında ve katmanlı bir birimin yerel olarak sabitlenmiş bir birime dönüştürülmesi sırasında gözlemlenmiştir. Bu sorunlar bu sürümde düzeltildiği için birim oluşturma ve dönüştürme yordamları sırasında ana bilgisayar performansının iyileştirmesiyle sonuçlanır. |Yes |Hayır |
| 2 |Yerel olarak sabitlenmiş birimler |Nadir örneklerde sistem, yerel olarak sabitlenmiş bir birim oluştururken çöker. Bu hata bu sürümde düzeltildi. |Yes |Hayır |
| 3 |Katmanlama |StorSimple bulut gereçlerinin (8010 ve 8020) meta verileri buluta katmanlı olduğunda, tek sayıda çökme vardı. Bu sorun bu sürümde düzeltilmiştir. |Hayır |Yes |
| 4 |Anlık görüntü oluşturma |Büyük hacimlerle senaryolarda Artımlı anlık görüntülerin oluşturulmasıyla ilgili ve veri karmaşası olmadan en düşük düzeyde bir sorun oluştu. Bu sorunlar bu sürümde düzeltilmiştir. |Yes |Yes |
| 5 |OpenStack kimlik doğrulaması |Bulut hizmeti sağlayıcısı olarak OpenStack kullanırken, Kullanıcı JSON ayrıştırıcısının kilitlenme ile sonuçlandığı kimlik doğrulamasıyla ilgili seyrek bir hata halinde çalışır. Bu hata bu sürümde düzeltilmiştir. |Yes |Hayır |
| 6 |Konak tarafı kopyası |Yazılımın önceki sürümlerinde, verileri bir birimden başka bir birime kopyalarken ODX zamanlamasıyla ilgili seyrek bir hata görüldü. Bu, denetleyicinin yük devretmesine neden olur ve sistem kurtarma moduna geçebilir. Bu hata bu sürümde düzeltilmiştir. |Yes |Hayır |
| 7 |Windows Yönetim Araçları (WMI) |Yazılımın önceki sürümlerinde, " \<ManagementException> sağlayıcı yükleme hatası" özel durumuyla birkaç Web proxy hatası örneği vardı. Bu hata bir WMI bellek sızıntısı ile sınırlandırıyordu ve şimdi düzeltildi. |Yes |Hayır |
| 8 |Güncelleştir |Daha nadir bazı örneklerde, yazılımın önceki sürümlerinde, güncelleştirmeleri taramaya veya yüklemeye çalışırken Kullanıcı bir "Cıpowershellhcsscripterror" almıştır. Bu sorun bu sürümde düzeltilmiştir. |Yes |Yes |
| 9 |Destek paketi |Bu sürümde, destek paketinin toplanması ve karşıya yüklenmesi gibi iyileştirmeler yapılmıştır. |Yes |Yes |

## <a name="known-issues-in-update-22"></a>Güncelleştirme 2,2 ' de bilinen sorunlar
Aşağıdaki tabloda, bu sürümdeki bilinen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun | Açıklamalar/geçici çözüm | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk çekirdeği |Nadir örneklerde, bir 8600 cihazının EBOD muhafazasının çoğunluğunun bağlantısı kesildiyse, disk çekirdeği olmadığında depolama havuzu çevrimdışı olur. Diskler yeniden bağlansa bile çevrimdışı olarak kalır. |Cihazı yeniden başlatmanız gerekir. Sorun devam ederse, sonraki adımlar için lütfen Microsoft Desteği başvurun. |Yes |Hayır |
| 2 |Yanlış denetleyici KIMLIĞI |Bir denetleyici değişikliği gerçekleştirildiğinde, denetleyici 0, denetleyici 1 olarak görünebilir. Denetleyici değişikliği sırasında, görüntü eş düğümünden yüklendiğinde, denetleyici KIMLIĞI başlangıçta eş denetleyicinin KIMLIĞI olarak gösterilir. Nadir örneklerde, bu davranış sistem yeniden başlatıldıktan sonra da görünebilir. |Kullanıcı eylemi gerekli değildir. Bu durum, denetleyici değiştirme işlemi tamamlandıktan sonra kendisini çözmeyecektir. |Yes |Hayır |
| 3 |Depolama hesapları |Depolama hesabını silmek için depolama hizmetinin kullanılması desteklenmeyen bir senaryodur. Bu, Kullanıcı verilerinin alınamadığı bir duruma yol açacaktır. | |Yes |Yes |
| 4 |Cihaz yük devretmesi |Aynı kaynak cihazdan farklı hedef cihazlara bir birim kapsayıcısının çoklu yük devretme işlemleri desteklenmez. Tek bir ölü cihazdan birden çok cihaza yük devretme, ilk yük devredilen cihazdaki birim kapsayıcılarının veri sahipliğini kaybetmesine izin vermez. Bu tür bir yük devretmeden sonra, bu birim kapsayıcıları, klasik Azure portalında görüntülediğinizde görünür veya farklı davranır. | |Yes |Hayır |
| 5 |Yükleme |SharePoint için StorSimple Bağdaştırıcısı yüklemesi sırasında, yüklemenin başarıyla tamamlanabilmesi için bir cihaz IP 'si sağlamanız gerekir. | |Yes |Hayır |
| 6 |Web ara sunucusu |Web proxy yapılandırmanızda belirtilen protokol olarak HTTPS varsa, cihazdan hizmete iletişim etkilenecektir ve cihaz çevrimdışı olur. Destek paketleri, cihazınızda önemli kaynaklar tüketen işlem sırasında da oluşturulacaktır. |Web proxy URL 'sinin belirtilen protokol olarak HTTP içerdiğinden emin olun. Daha fazla bilgi için [Cihazınız için web ara sunucusunu yapılandırma](./storsimple-8000-configure-web-proxy.md)’ya gidin. |Yes |Hayır |
| 7 |Web ara sunucusu |Kayıtlı bir cihazda Web proxy 'yi yapılandırıp etkinleştirirseniz, cihazınızda etkin denetleyiciyi yeniden başlatmanız gerekir. | |Yes |Hayır |
| 8 |Yüksek bulut gecikme süresi ve yüksek g/ç iş yükü |StorSimple cihazınız çok yüksek bulut gecikme sürelerinin (saniye cinsinden) ve yüksek g/ç iş yükünün bir birleşimiyle karşılaştığında, cihaz birimleri düşürülmüş bir duruma geçer ve g/ç "cihaz için yok" hatasıyla başarısız olabilir. |Bu durumdan kurtarmak için cihaz denetleyicilerini el ile yeniden başlatmanız veya cihaz yük devretmesi gerçekleştirmeniz gerekir. |Yes |Hayır |
| 9 |Azure PowerShell |StorSimple cmdlet 'ini kullandığınızda **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object-ilk 1-** ilk nesneyi seçmek için bekleyin, böylece yeni bir **volumecontainer** nesnesi oluşturabilirsiniz, cmdlet tüm nesneleri döndürür. |Cmdlet 'i parantez içinde şu şekilde kaydırın: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object-ilk 1-bekleme** |Yes |Yes |
| 10 |Geçiş |Geçiş için birden çok birim kapsayıcısı geçirildiğinde, en son yedekleme için ETA yalnızca ilk birim kapsayıcısı için doğrudur. Ayrıca, ilk birim kapsayıcıdaki ilk 4 yedekleme geçirildikten sonra paralel geçiş başlar. |Tek seferde bir birim kapsayıcısını geçirmeniz önerilir. |Yes |Hayır |
| 11 |Geçiş |Geri yükleme işleminden sonra birimler yedekleme ilkesine veya sanal disk grubuna eklenmez. |Yedeklemeler oluşturmak için bu birimleri bir yedekleme ilkesine eklemeniz gerekir. |Yes |Yes |
| 12 |Geçiş |Geçiş işlemi tamamlandıktan sonra, 5000/7000 Serisi cihaz geçirilmiş veri kapsayıcılarına erişmemelidir. |Geçiş tamamlandıktan ve kaydedildikten sonra geçirilmiş veri kapsayıcılarını silmenizi öneririz. |Yes |Hayır |
| 13 |Kopyala ve DR |Güncelleştirme 1 çalıştıran bir StorSimple cihazı, güncelleştirme öncesi 1 yazılımını çalıştıran bir cihaza olağanüstü durum kurtarması kopyalayamıyor veya gerçekleştiremiyor. |Bu işlemlere izin vermek için 1 öğesini güncelleştirmek üzere hedef cihazı güncelleştirmeniz gerekir |Yes |Yes |
| 14 |Geçiş |İlişkili birimleri olmayan birim grupları varsa, geçiş için yapılandırma yedeklemesi 5000-7000 Serisi cihazda başarısız olabilir. |İlişkili birimleri olmayan tüm boş birim gruplarını silip yapılandırma yedeklemesini yeniden deneyin. |Yes |Hayır |
| 15 |Azure PowerShell cmdlet 'leri ve yerel olarak sabitlenmiş birimler |Azure PowerShell cmdlet 'leri aracılığıyla yerel olarak sabitlenmiş bir birim oluşturamazsınız. (Azure PowerShell aracılığıyla oluşturduğunuz tüm birimler katmanlı olacaktır.) |Yerel olarak sabitlenmiş birimleri yapılandırmak için her zaman StorSimple Yöneticisi hizmetini kullanın. |Yes |Hayır |
| 16 |Yerel olarak sabitlenmiş birimler için kullanılabilir alan |Yerel olarak sabitlenmiş bir birimi silerseniz, yeni birimler için kullanılabilir alan hemen güncelleştirilmemiş olabilir. StorSimple Yöneticisi hizmeti, her saat yaklaşık olarak kullanılabilir yerel alanı güncelleştirir. |Yeni birimi oluşturmayı denemeden önce bir saat bekleyin. |Yes |Hayır |
| 17 |Yerel olarak sabitlenmiş birimler |Geri yükleme işiniz, yedekleme kataloğunda geçici anlık görüntü yedeklemesini, ancak yalnızca geri yükleme işinin süresini gösterir. Ayrıca, **yedekleme ilkeleri** sayfasında, ancak geri yükleme işinin süresi boyunca **tmpcollection** ön ekine sahip bir sanal disk grubu gösterir. |Bu davranış, geri yükleme işiniz yalnızca yerel olarak sabitlenmiş birimler veya yerel olarak sabitlenmiş ve katmanlı birimler karışımı içeriyorsa meydana gelebilir. Geri yükleme işi yalnızca katmanlı birimler içeriyorsa, bu davranış gerçekleşmeyecektir. Kullanıcı müdahalesi gerekli değildir. |Yes |Hayır |
| 18 |Yerel olarak sabitlenmiş birimler |Bir geri yükleme işini iptal ederseniz ve daha sonra bir denetleyici yük devretmesi gerçekleşirse, geri yükleme işi **iptal** etme yerine **başarısız** olarak gösterilir. Geri yükleme işi başarısız olursa ve denetleyici yük devretmesi hemen sonra gerçekleşirse, geri yükleme işi **başarısız** yerine **iptal** edilir. |Bu davranış, geri yükleme işiniz yalnızca yerel olarak sabitlenmiş birimler veya yerel olarak sabitlenmiş ve katmanlı birimler karışımı içeriyorsa meydana gelebilir. Geri yükleme işi yalnızca katmanlı birimler içeriyorsa, bu davranış gerçekleşmeyecektir. Kullanıcı müdahalesi gerekli değildir. |Yes |Hayır |
| 19 |Yerel olarak sabitlenmiş birimler |Bir geri yükleme işini iptal ederseniz veya geri yükleme başarısız olursa ve sonra bir denetleyici yük devretmesi oluşursa, **işler** sayfasında ek bir geri yükleme işi görüntülenir. |Bu davranış, geri yükleme işiniz yalnızca yerel olarak sabitlenmiş birimler veya yerel olarak sabitlenmiş ve katmanlı birimler karışımı içeriyorsa meydana gelebilir. Geri yükleme işi yalnızca katmanlı birimler içeriyorsa, bu davranış gerçekleşmeyecektir. Kullanıcı müdahalesi gerekli değildir. |Yes |Hayır |
| 20 |Yerel olarak sabitlenmiş birimler |Katmanlı bir birimi (güncelleştirme 1,2 veya önceki bir sürümü) yerel olarak sabitlenmiş bir birime dönüştürmeye çalışırsanız ve cihazınızda alan tükenmişse veya bir bulut kesintisi yaşanırsa, kopya (ler) bozulmuş olabilir. |Bu sorun yalnızca güncelleştirme öncesi 2,1 yazılımıyla oluşturulan ve kopyalanan birimlerde meydana gelir. Bu, seyrek görülen bir senaryo olmalıdır. | | |
| 21 |Birim dönüştürme |Birim dönüştürme devam ederken bir birime bağlı olan ACRs 'yi güncelleştirmeyin (yerel olarak sabitlenmiş veya tam tersi). ACRs 'nin güncelleştirilmesi veri bozulmasına neden olabilir. |Gerekirse, birim dönüşümden önce ACRs 'yi güncelleştirin ve dönüştürme devam ederken daha fazla ACR güncelleştirmesi yapmayın. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Güncelleştirme 2,2 ' de denetleyici ve bellenim güncelleştirmeleri
Bu sürümde yalnızca yazılım güncelleştirmeleri vardır. Ancak, güncelleştirme 2 ' den önceki bir sürümden güncelleştiriyorsanız, cihazınızda sürücü, Storport, Spaceport ve (bazı durumlarda) disk üretici yazılımı güncelleştirmelerini yüklemeniz gerekir.

Sürücü, Storport, Spaceport ve disk üretici yazılımı güncelleştirmelerinin nasıl yükleneceği hakkında daha fazla bilgi için bkz. StorSimple cihazınıza [güncelleştirme 2,2](./storsimple-8000-install-update-5.md) ' yı yüklemek.

## <a name="virtual-device-updates-in-update-22"></a>Güncelleştirme 2,2 ' de sanal cihaz güncelleştirmeleri
Bu güncelleştirme, sanal cihaza uygulanamıyor. Yeni sanal cihazların oluşturulması gerekir. 

## <a name="next-step"></a>Sonraki adım
StorSimple cihazınıza [güncelleştirme 2,2](./storsimple-8000-install-update-5.md) ' i nasıl yükleyeceğinizi öğrenin.