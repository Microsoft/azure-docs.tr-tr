---
title: StorSimple 8000 serisi güncelleştirme 2 sürüm notları | Microsoft Docs
description: StorSimple 8000 serisi güncelleştirme 2 için yeni özellikleri, sorunları ve geçici çözümleri açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b68e338c2675a3aeefda390d12ab2d82ea1c7a9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956662"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 serisi güncelleştirme 2 sürüm notları

## <a name="overview"></a>Genel Bakış
Aşağıdaki sürüm notları, yeni özellikleri anlatmaktadır ve StorSimple 8000 serisi güncelleştirme 2 için kritik açık sorunları belirler. Ayrıca, bu sürüme dahil olan StorSimple yazılım, sürücü ve disk üretici yazılımı güncelleştirmelerinin bir listesini içerir. 

Güncelleştirme 2, güncelleştirme 1,2 aracılığıyla sürüm (GA) veya güncelleştirme 0,1 ' i çalıştıran tüm StorSimple cihazlarına uygulanabilir. Güncelleştirme 2 ile ilişkili cihaz sürümü 6.3.9600.17673 ' dir.

Güncelleştirmeyi StorSimple çözümünüzde dağıtmadan önce lütfen sürüm notlarında yer alan bilgileri gözden geçirin.

> [!IMPORTANT]
> * Bu güncelleştirmenin yüklenmesi yaklaşık 4-7 saat sürer (Windows güncelleştirmeleri dahil). 
> * Güncelleştirme 2 ' de yazılım, LSI sürücü ve SSD üretici yazılımı güncelleştirmeleri vardır.
> * Güncelleştirmelerin aşamalı olarak piyasaya sürültiğimiz için yeni sürümler için güncelleştirmeleri hemen göremeyebilirsiniz. Birkaç gün bekleyin ve bu süre yakında kullanılabilir hale gelecağından güncelleştirmeleri yeniden tarayın.
> 
> 

## <a name="whats-new-in-update-2"></a>Güncelleştirme 2 ' deki yenilikler
Güncelleştirme 2, aşağıdaki yeni özellikleri tanıtır.

* **Yerel olarak sabitlenmiş birimler** – storsimple 8000 serisinin önceki sürümlerinde veri blokları, kullanıma göre buluta katmanlı. Blokların yerel üzerinde kalacağından emin olmanın bir yolu yoktu. Güncelleştirme 2 ' de, bir birim oluşturduğunuzda bir birimi yerel olarak sabitlenmiş olarak belirtebilir ve bu birimdeki birincil veriler buluta katmanlanmaz. Bulutun veri mobilite ve olağanüstü durum kurtarma amacıyla kullanılabilmesi için yerel olarak sabitlenmiş birimlerin anlık görüntüleri yedekleme için buluta kopyalanacaktır. Ayrıca, birim türünü değiştirebilir (yani, katmanlı birimleri yerel olarak sabitlenmiş birimlere dönüştürebilir ve yerel olarak sabitlenmiş birimleri katmanlı olarak dönüştürebilirsiniz). 
* **StorSimple sanal cihaz geliştirmeleri** – daha önce storsimple 8000 serisi sanal cihazı bir olağanüstü durum kurtarma veya geliştirme/test çözümü olarak konumlandırmıştır. Yalnızca bir sanal cihaz modeli (model 1100) vardı. Güncelleştirme 2 iki sanal cihaz modeli sunar: 
  
  * 8010 (eski adıyla 1100 olarak biliniyordu) – değişiklik yok; 30 TB kapasiteye sahiptir ve Azure Standart depolama kullanır.
  * 8020 – 64 TB kapasiteye sahiptir ve gelişmiş performans için Azure Premium depolama kullanır.
    
    Hem sanal cihaz modelleri (8010/8020) için tek bir VHD vardır. Sanal cihazı ilk başlattığınızda, platform parametrelerini algılar ve doğru model sürümünü uygular.
* **Ağ geliştirmeleri** – güncelleştirme 2, aşağıdaki ağ geliştirmelerini içerir:
  
  * Bir NIC başarısız olursa yük devretme gerçekleşebilmesi için, bulut için birden çok NIC etkinleştirilebilir.
  * Bulut özellikli bloklara yönelik sabit ölçümler ile yönlendirme geliştirmeleri.
  * Yük devretmeden önce başarısız kaynakları çevrimiçi yeniden deneme.
  * Hizmet hatalarıyla ilgili yeni uyarılar.
* **Geliştirmeler güncelleştiriliyor** – güncelleştirme 1,2 ve önceki sürümlerde, storsimple 8000 serisi iki kanal aracılığıyla güncelleştirildi: kümeleme, iSCSI, vb. için Windows Update ve ikili dosyalar ve bellenim için Microsoft Update.
    Güncelleştirme 2 tüm güncelleştirme paketleri için Microsoft Update kullanır. Bu, daha az zaman düzeltme eki uygulama veya yük devretme işlemleri yapmaya neden olmalıdır. 
* **Üretici yazılımı güncelleştirmeleri** – aşağıdaki bellenim güncelleştirmeleri dahil edilmiştir:
  
  * LSI: lsi_sas2.sys ürün sürümü 2.00.72.10
  * Yalnızca SSD (HDD güncelleştirmesi yok): XMGG, XGEG, KZ50, F6C2 ve VR08
* **Proaktif destek** – güncelleştirme 2 Microsoft 'un cihazdan ek tanılama bilgileri çekmesini sağlar. Operations ekibimiz sorun yaşayan cihazları tanımlarsa, cihazdan bilgi toplamak ve sorunları tanılamak için daha iyi bir şekilde donatılmıştır. **Güncelleştirme 2 ' yi kabul ederek, bu öngörülü desteği sağlamamıza izin verin**.    

## <a name="issues-fixed-in-update-2"></a>Güncelleştirme 2 ' de düzeltilen sorunlar
Aşağıdaki tablolarda güncelleştirme 2 ' de düzeltilen sorunların özeti verilmiştir.    

| Hayır. | Özellik | Sorun | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- |
| 1 |Ağ arabirimleri |StorSimple Manager hizmeti, güncelleştirme 1 ' e yükseltildikten sonra, bir denetleyicide Veri2 ve DATA3 bağlantı noktalarının başarısız olduğunu bildirdi. Bu sorun düzeltilmiştir. |Yes |Hayır |
| 2 |Güncelleştirmeler |Güncelleştirme 1 ' e yükselttikten sonra, klasik Azure portalında birden çok cihazda duyulabilir alarm uyarıları oluştu. Bu sorun düzeltilmiştir. |Yes |Hayır |
| 3 |OpenStack kimlik doğrulaması |Bulut hizmeti sağlayıcınız olarak OpenStack kullanırken, bulut kimlik doğrulama dizeniz çok uzun olduğu bir hata alabilirsiniz. Bu sorun düzeltildi. |Yes |Hayır |

## <a name="known-issues-in-update-2"></a>Güncelleştirme 2 ' de bilinen sorunlar
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
| 20 |Yerel olarak sabitlenmiş birimler |Katmanlı bir birimi (güncelleştirme 1,2 veya önceki bir sürümü) yerel olarak sabitlenmiş bir birime dönüştürmeye çalışırsanız ve cihazınızda alan tükenmişse veya bir bulut kesintisi yaşanırsa, kopya (ler) bozulmuş olabilir. |Bu sorun yalnızca güncelleştirme 2 öncesi yazılımlarla oluşturulan ve kopyalanan birimlerde meydana gelir. Bu, seyrek görülen bir senaryo olmalıdır. | | |
| 21 |Birim dönüştürme |Birim dönüştürme devam ederken bir birime bağlı olan ACRs 'yi güncelleştirmeyin (yerel olarak sabitlenmiş veya tam tersi). ACRs 'nin güncelleştirilmesi veri bozulmasına neden olabilir. |Gerekirse, birim dönüşümden önce ACRs 'yi güncelleştirin ve dönüştürme devam ederken daha fazla ACR güncelleştirmesi yapmayın. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Güncelleştirme 2 ' de denetleyici ve bellenim güncelleştirmeleri
Bu sürüm, cihazınızdaki sürücüyü ve disk bellenimini güncelleştirir.

* LSI üretici yazılımı güncelleştirmesi hakkında daha fazla bilgi için bkz. Microsoft Bilgi Bankası makalesi 3121900. 
* Disk üretici yazılımı güncelleştirmesi hakkında daha fazla bilgi için bkz. Microsoft Bilgi Bankası makalesi 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Güncelleştirme 2 ' de sanal cihaz güncelleştirmeleri
Bu güncelleştirme, sanal cihaza uygulanamıyor. Yeni sanal cihazların oluşturulması gerekir. 

## <a name="next-step"></a>Sonraki adım
StorSimple cihazınıza [güncelleştirme 2](./storsimple-8000-install-update-5.md) ' nin nasıl yükleneceğini öğrenin.