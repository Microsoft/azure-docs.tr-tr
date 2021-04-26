---
title: StorSimple 8000 serisi güncelleştirme 1,2 sürüm notları | Microsoft Docs
description: StorSimple 8000 serisi güncelleştirme 1,2 için yeni özellikleri, sorunları ve geçici çözümleri açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a5ba4b27ae19999237edb75c50db36ef57a10f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956696"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>StorSimple 8000 serisi cihazınız için güncelleştirme 1,2 sürüm notları

## <a name="overview"></a>Genel Bakış
Aşağıdaki sürüm notları, yeni özellikleri anlatmaktadır ve StorSimple 8000 serisi güncelleştirme 1,2 için kritik açık sorunları belirler. Ayrıca, bu sürüme dahil olan StorSimple yazılım, sürücü ve disk üretici yazılımı güncelleştirmelerinin bir listesini içerir. 

Güncelleştirme 1,2, sürüm (GA), güncelleştirme 0,1, güncelleştirme 0,2 veya 0,3 yazılımını çalıştıran herhangi bir StorSimple cihaza uygulanabilir. Cihazınız güncelleştirme 1 veya güncelleştirme 1,1 çalıştırıyorsa güncelleştirme 1,2 kullanılabilir değildir. Cihazınız sürüm (GA) çalıştırıyorsa, bu güncelleştirmeyi yüklerken size yardımcı olması için lütfen [Microsoft desteği başvurun](./storsimple-8000-contact-microsoft-support.md) .

Aşağıdaki tabloda, 1, 1,1 ve 1,2 güncelleştirmelerine karşılık gelen cihaz yazılımı sürümleri listelenmektedir.

| Güncelleştirme çalışıyorsa... | Bu, cihazınızın yazılım sürümüdür. |
| --- | --- |
| Güncelleştirme 1.2 |6.3.9600.17584 |
| Güncelleştirme 1.1 |6.3.9600.17521 |
| Güncelleştirme 1,0 |6.3.9600.17491 |

Güncelleştirmeyi StorSimple çözümünüzde dağıtmadan önce lütfen sürüm notlarında yer alan bilgileri gözden geçirin. Daha fazla bilgi için bkz. [StorSimple cihazınıza güncelleştirme 1,2](./storsimple-8000-install-update-5.md)' yi yüklemek. 

> [!IMPORTANT]
> * Bu güncelleştirmenin yüklenmesi yaklaşık 5-10 saat sürer (Windows güncelleştirmeleri dahil). 
> * Güncelleştirme 1,2 ' de yazılım, LSI sürücü ve disk üretici yazılımı güncelleştirmeleri vardır. Yüklemek için, [StorSimple cihazınızda güncelleştirme 1,2](./storsimple-8000-install-update-5.md)' yı yüklemek için yönergeleri izleyin.
> * Güncelleştirmelerin aşamalı olarak piyasaya sürültiğimiz için yeni sürümler için güncelleştirmeleri hemen göremeyebilirsiniz. En kısa zamanda kullanılabilir hale gelebilmesi için, birkaç gün içinde güncelleştirmeleri tarayın.
> 
> 

## <a name="whats-new-in-update-12"></a>Güncelleştirme 1,2 ' deki yenilikler
Bu özellikler ilk olarak, sınırlı bir Kullanıcı kümesiyle sunulan güncelleştirme 1 ' de kullanıma sunulmuştur. Güncelleştirme 1,2 sürümüyle birlikte, StorSimple kullanıcılarının çoğu aşağıdaki yeni özellikleri ve geliştirmeleri görür:

* **5000-7000 serisinden 8000 serisi cihazlara geçiş** : Bu sürüm, storsimple 5000-7000 Serisi gereci kullanıcılarının verilerini storsimple 8000 Series fiziksel gerecine veya sanal bir gereci geçirmeye izin veren yeni bir geçiş özelliği sunar. Geçiş özelliğinin iki anahtar değeri propositions vardır:                                                                  
  
  * 5000-7000 Serisi gereçlere mevcut verilerin 8000 serisi gereçlere geçişini etkinleştirerek **iş sürekliliği**.
  * StorSimple Yöneticisi hizmeti aracılığıyla birden çok gerecin verimli merkezi yönetimi, daha iyi donanım sınıfı ve güncelleştirilmiş bellenim, sanal gereçler, veri hareketliliği ve gelecekteki yol haritasında özellikler gibi **8000 serisi gereçlerinin geliştirilmiş özellik teklifleri**.
    
    StorSimple 5000-7000 serisini 8000 serisi cihaza geçirme hakkında ayrıntılı bilgi için [geçiş kılavuzuna](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) bakın. 
* **Azure Kamu portalındaki kullanılabilirlik** – StorSimple artık Azure Kamu portalında kullanılabilir. Bkz. [Azure Kamu portalında StorSimple cihazını dağıtma](./index.yml).
* **Diğer bulut hizmeti sağlayıcıları Için destek** – desteklenen diğer bulut hizmeti sağlayıcıları, Amazon S3, Amazon S3 ile RR, HP ve OpenStack (Beta).
* **En son depolama API 'Lerine güncelleştirme** – bu sürümle StorSimple, en son Azure depolama hizmeti API 'lerine güncelleştirilmiştir. Güncelleştirme öncesi 1 yazılım sürümleri (sürüm, 0,1, 0,2 ve 0,3) çalıştıran StorSimple 8000 serisi cihazlar, 17 Temmuz 2009 ' den daha eski Azure Storage hizmeti API 'Lerinin sürümlerini kullanıyor. [Depolama hizmeti sürümlerinin kaldırılması hakkında](/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), 1 Ağustos 2016 ' de belirtildiği gibi, bu API 'ler kullanım dışı bırakılır. 1 Ağustos 2016 ' den önce StorSimple 8000 serisi güncelleştirme 1 ' i uygulamanız zorunludur. Bunu yapmazsanız, StorSimple cihazları düzgün şekilde çalışmayı durdurur.
* Bölgesel olarak **yedekli depolama (ZRS) Için destek** – depolama API 'lerinin en son sürümüne yükseltme Ile, storsimple 8000 serisi yerel olarak yedekli depolama (LRS) ve coğrafi olarak yedekli depolama (GRS) özelliklerine ek olarak bölgesel olarak yedekli depolamayı (ZRS) destekleyecektir. ZRS ayrıntıları için [Azure Storage artıklık seçenekleri '](../storage/common/storage-redundancy.md) nde bu makaleye bakın.
* **Gelişmiş ilk dağıtım ve güncelleştirme deneyimi** – bu sürümde, yükleme ve güncelleştirme işlemi geliştirilmiştir. Ağ yapılandırması ve güvenlik duvarı ayarları yanlışsa, Kurulum Sihirbazı aracılığıyla yükleme, kullanıcıya geri bildirimde bulunmak için geliştirilmiştir. Cihazın ağ sorunlarını gidermeye yardımcı olmak için ek tanılama cmdlet 'leri verilmiştir. Sorun giderme için kullanılan yeni tanılama cmdlet 'leri hakkında daha fazla bilgi için [sorun giderme dağıtım makalesine](./storsimple-8000-troubleshoot-deployment.md) bakın.

## <a name="issues-fixed-in-update-12"></a>Güncelleştirme 1,2 ' de düzeltilen sorunlar
Aşağıdaki tabloda 1,2, 1,1 ve 1 güncelleştirmelerinde düzeltilen sorunların özeti verilmiştir.    

| Hayır. | Özellik | Sorun | Güncelleştirmede düzeltildi | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- | --- |
| 1 |StorSimple için Windows PowerShell |Bir Kullanıcı StorSimple için Windows PowerShell kullanarak StorSimple cihazına uzaktan eriştiğinde ve ardından Kurulum Sihirbazı 'nı başlattığınızda, veri 0 IP 'si girişi gerçekleşerken bir kilitlenme meydana geldi. Bu hata güncelleştirme 1 ' de düzeltildi. |Güncelleştirme 1 |Yes |Yes |
| 2 |Fabrika sıfırlaması |Bazı örneklerde, bir fabrika sıfırlaması gerçekleştirdiğinizde, StorSimple cihazı takılmış olur ve bu ileti görüntülenir: **fabrika 'ye sıfırlama işlemi devam ediyor (5. aşama)**. Bu, cmdlet devam ederken CTRL + C tuşlarına basıldığında meydana gelen bir durum oluştu. Bu hata artık düzeltildi. |Güncelleştirme 1 |Yes |Hayır |
| 3 |Fabrika sıfırlaması |Başarısız bir çift Controller fabrika sıfırlaması sonrasında cihaz kaydıyla devam etme izniniz yok. Bu, desteklenmeyen bir sistem yapılandırması ile sonuçlandı. Güncelleştirme 1 ' de bir hata iletisi gösterilir ve kayıt, başarısız bir fabrika sıfırlaması olan bir cihazda engellenir. |Güncelleştirme 1 |Yes |Hayır |
| 4 |Fabrika sıfırlaması |Bazı örneklerde, yanlış pozitif uyumsuzluk uyarıları tetiklendi. Güncelleştirme 1 çalıştıran cihazlarda hatalı uyumsuzluk uyarıları daha fazla oluşturulmayacak. |Güncelleştirme 1 |Yes |Hayır |
| 5 |Fabrika sıfırlaması |Bir fabrika sıfırlaması tamamlanmadan önce kesintiye uğrarsa, cihaz kurtarma moduna girmiştir ve StorSimple için Windows PowerShell erişmenize izin vermez. Bu hata artık düzeltildi. |Güncelleştirme 1 |Yes |Hayır |
| 6 |Olağanüstü durum kurtarma |Bir olağanüstü durum kurtarma (DR) hatası, hedef cihazdaki yedeklemelerin bulunması sırasında DR 'nin başarısız olacağı bir hata düzeltildi. |Güncelleştirme 1 |Yes |Yes |
| 7 |İzleme LED 'Leri |Belirli örneklerde, gerecin arkasında bulunan izleme LED 'Leri doğru durumu göstermiyor. Mavi ışığı kapatıldı. Bu arabirimler yapılandırılmadığı zaman bile VERI 0 ve VERI 1 LED 'Leri yanıp sönmeye alındı. Sorun düzeltildi ve izleme LED 'Leri artık doğru durumu belirtiyor. |Güncelleştirme 1 |Yes |Hayır |
| 8 |İzleme LED 'Leri |Belirli örneklerde, güncelleştirme 1 ' i uyguladıktan sonra etkin denetleyicinin mavi ışığı devre dışı bırakılarak etkin denetleyiciyi tespit etmek zordur. Bu sorun, bu düzeltme eki sürümünde düzeltildi. |Güncelleştirme 1.2 |Yes |Hayır |
| 9 |Ağ arabirimleri |Önceki sürümlerde, yönlendirilemeyen bir ağ geçidi ile yapılandırılmış bir StorSimple cihazı çevrimdışı duruma gelebilir. Bu sürümde, veri 0 için yönlendirme ölçümü en düşük düzeyde yapılmıştır; Bu nedenle, diğer ağ arabirimleri bulut özellikli olsa bile, cihazdaki tüm bulut trafiği 0 verileri aracılığıyla yönlendirilir. |Güncelleştirme 1 |Yes |Yes |
| 10 |Yedeklemeler |Güncelleştirme 1 ' deki bir hata, düzeltme eki sürüm güncelleştirmesi 1,1 ' de 24 gün sonra yedeklemenin başarısız olmasına neden oldu. |Güncelleştirme 1.1 |Yes |Yes |
| 11 |Yedeklemeler |Önceki sürümlerde oluşan bir hata, düşük değişiklik oranlarına sahip bulut anlık görüntüleri için düşük performansa neden oldu. Bu hata, bu düzeltme eki sürümünde düzeltildi. |Güncelleştirme 1.2 |Yes |Yes |
| 12 |Güncelleştirmeler |Güncelleştirme 1 ' de, başarısız bir yükseltmeyi bildiren ve denetleyicilerin kurtarma moduna geçmesine neden olan bir hata, bu düzeltme eki sürümünde düzeltildi. |Güncelleştirme 1.2 |Yes |Yes |

## <a name="known-issues-in-update-12"></a>Güncelleştirme 1,2 ' de bilinen sorunlar
Aşağıdaki tabloda, bu sürümdeki bilinen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun | Açıklamalar/geçici çözüm | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk çekirdeği |Nadir örneklerde, bir 8600 cihazının EBOD muhafazasının çoğunluğunun bağlantısı kesildiğinde, disk çekirdeği olmadığında depolama havuzu çevrimdışı olur. Diskler yeniden bağlansa bile çevrimdışı olarak kalır. |Cihazı yeniden başlatmanız gerekir. Sorun devam ederse, sonraki adımlar için lütfen Microsoft Desteği başvurun. |Yes |Hayır |
| 2 |Yanlış denetleyici KIMLIĞI |Bir denetleyici değişikliği gerçekleştirildiğinde, denetleyici 0, denetleyici 1 olarak görünebilir. Denetleyici değişikliği sırasında, görüntü eş düğümünden yüklendiğinde, denetleyici KIMLIĞI başlangıçta eş denetleyicinin KIMLIĞI olarak gösterilir. Nadir örneklerde, bu davranış sistem yeniden başlatıldıktan sonra da görünebilir. |Kullanıcı eylemi gerekli değildir. Bu durum, denetleyici değiştirme işlemi tamamlandıktan sonra kendisini çözmeyecektir. |Yes |Hayır |
| 3 |Depolama hesapları |Depolama hesabını silmek için depolama hizmetinin kullanılması desteklenmeyen bir senaryodur. Bu, Kullanıcı verilerinin alınamadığı bir duruma yol açacaktır. |Yes |Yes | |
| 4 |Cihaz yük devretmesi |Aynı kaynak cihazdan farklı hedef cihazlara bir birim kapsayıcısının çoklu yük devretme işlemleri desteklenmez. Tek bir ölü cihazdan birden çok cihaza cihaz yük devretmesi, ilk yük devredilen cihazdaki birim kapsayıcılarının veri sahipliğini kaybetmesine izin vermez. Bu tür bir yük devretmeden sonra, bu birim kapsayıcıları, klasik Azure portalında görüntülediğinizde görünür veya farklı davranır. | |Yes |Hayır |
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

## <a name="physical-device-updates-in-update-12"></a>Güncelleştirme 1,2 ' deki fiziksel cihaz güncelleştirmeleri
Düzeltme Eki güncelleştirme 1,2, fiziksel bir cihaza (güncelleştirme 1 ' den önceki sürümlerde çalışan sürümler) uygulanmışsa, yazılım sürümü 6.3.9600.17584 olarak değişir.

## <a name="controller-and-firmware-updates-in-update-12"></a>Güncelleştirme 1,2 ' de denetleyici ve bellenim güncelleştirmeleri
Bu sürüm, cihazınızdaki sürücüyü ve disk bellenimini güncelleştirir.

* SAS denetleyicisi güncelleştirmesi hakkında daha fazla bilgi için, [Microsoft Azure StorSimple gereci IÇINDEKI LSI SAS denetleyicileri Için güncelleştirme 1](https://support.microsoft.com/kb/3043005)' i inceleyin. 
* Disk üretici yazılımı güncelleştirmesi hakkında daha fazla bilgi için bkz. [Microsoft Azure StorSimple gereç Için disk üretici yazılımı güncelleştirme 1](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Güncelleştirme 1,2 ' de sanal cihaz güncelleştirmeleri
Bu güncelleştirme, sanal cihaza uygulanamıyor. Yeni sanal cihazların oluşturulması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar
* [Cihazınıza güncelleştirme 1,2 ' ü yükler](./storsimple-8000-install-update-5.md).