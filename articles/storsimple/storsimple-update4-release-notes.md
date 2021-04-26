---
title: StorSimple 8000 serisi güncelleştirme 4 sürüm notları | Microsoft Docs
description: StorSimple 8000 serisi güncelleştirme 4 için yeni özellikleri, sorunları ve geçici çözümleri açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954061"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 serisi güncelleştirme 4 sürüm notları

## <a name="overview"></a>Genel Bakış

Aşağıdaki sürüm notları, yeni özellikleri anlatmaktadır ve StorSimple 8000 serisi güncelleştirme 4 için kritik açık sorunları belirler. Ayrıca, bu sürüme dahil olan StorSimple yazılım güncelleştirmelerinin bir listesini içerir. 

Güncelleştirme 4, güncelleştirme 3,1 aracılığıyla Release (GA) veya güncelleştirme 0,1 ' i çalıştıran herhangi bir StorSimple cihaza uygulanabilir. Güncelleştirme 4 ile ilişkili cihaz sürümü 6.3.9600.17820 ' dir.

Güncelleştirmeyi StorSimple çözümünüzde dağıtmadan önce lütfen sürüm notlarında yer alan bilgileri gözden geçirin.

> [!IMPORTANT]
> * Güncelleştirme 4 ' te cihaz yazılımı, USı üretici yazılımı, LSI sürücü ve üretici yazılımı, disk üretici yazılımı, Storport ve Spaceport, güvenlik ve diğer işletim sistemi güncelleştirmeleri bulunur. Bu güncelleştirmenin yüklenmesi yaklaşık 4 saat sürer. Disk üretici yazılımı güncelleştirmesi, kesintiye uğratan bir güncelleştirmedir ve cihazınız için kapalı kalma süresine neden olur. Cihazınızı güncel tutmak için güncelleştirme 4 ' ü uygulamanızı öneririz. 
> * Güncelleştirmelerin aşamalı olarak piyasaya sürültiğimiz için yeni sürümler için güncelleştirmeleri hemen göremeyebilirsiniz. Birkaç gün bekleyin ve bu süre yakında kullanılabilir hale gelecağından güncelleştirmeleri yeniden tarayın.

## <a name="whats-new-in-update-4"></a>Güncelleştirme 4 ' teki yenilikler

Güncelleştirme 4 ' te aşağıdaki önemli geliştirmeler ve hata düzeltmeleri yapılmıştır.

* Daha **Akıllı otomatik Space geri kazanma algoritmaları** – güncelleştirme 4 ' te otomatik alan geri kazanma algoritmaları, bulutta bulunan beklenen geri kazanılan alana göre Space geri kazanma döngülerini ayarlamak için geliştirilmiştir. 

* **Yerel olarak sabitlenmiş birimler Için performans iyileştirmeleri** – güncelleştirme 4, yüksek veri alımı olan senaryolarda yerel olarak sabitlenmiş birimlerin performansını iyileştirmiştir (birim boyutuyla karşılaştırılabilir veriler).

* **Heatmap tabanlı geri yükleme** -önceki sürümlerde, olağanüstü durum kurtarma (Dr) sonrasında veriler, yavaş bir performansa neden olan erişim düzenlerine göre buluttan geri yüklendi. 

    Güncelleştirme 4 ' te, cihaz Dr 'den önce kullanılırken bir ısı haritasını oluşturmak için sık erişilen verileri izleyen yeni bir özellik uygulanır (en çok kullanılan veri öbeklerinin yüksek bir ısı olduğu, daha az kullanılan parçalar düşük bir ısı olur). Dr 'den sonra StorSimple, verileri buluttan otomatik olarak geri yüklemek ve yeniden oluşturmak için ısı haritasını 'i kullanır. 

    Tüm geri yüklemeler artık ısı haritasını tabanlı geri yüklemeler. Isı haritasını tabanlı geri yükleme ve yeniden doldurma işlerini sorgulama ve iptal etme hakkında daha fazla bilgi için [StorSimple için Windows PowerShell cmdlet başvurusuna](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps)gidin.

* **StorSimple Tanılama Aracı** – güncelleştirme 4 ' te, sistem, ağ, performans ve donanım bileşeni sistem durumu ile ilgili sorunların kolay bir şekilde tanılanması ve sorun gidermede izin vermek Için bir StorSimple Tanılama Aracı yayımlandı. Bu araç StorSimple için Windows PowerShell aracılığıyla çalıştırılır. Daha fazla bilgi için, [StorSimple Tanılama aracını kullanarak sorun giderme](storsimple-8000-diagnostics.md)bölümüne gidin.

* **UI tabanlı StorSimple geçiş aracı** -Bu sürümden önce, 5000-7000 serisinden verilerin geçirilmesi, kullanıcıların Azure PowerShell arabirimini kullanarak geçiş iş akışının bir parçasını yürütmelerine gerek duyduğu bir işlemdir. Bu sürümde, kullanımı kolay bir kullanıcı arabirimi tabanlı StorSimple geçiş aracı, aynı geçiş iş akışını kolaylaştırmak için kullanılabilir hale getirilir. Bu araç ayrıca kurtarma demetlerinin birleştirilmesi için de izin verir. 

* **FIPS ile ilgili değişiklikler** -bu sürüm, hem Microsoft Azure Kamu hem de Azure genel bulut hesapları Için tüm storsimple 8000 serisi cihazlarda varsayılan olarak FIPS etkindir.

* **Değişiklikleri Güncelleştir** -bu sürümde, güncelleştirme hatalarıyla ilgili hatalar düzeltildi.

* **Disk hatalarıyla Ilgili uyarı** -bu sürümde, yaklaşan disk hatalarının kullanıcısını uyaran yeni bir uyarı eklenmiştir. Bu uyarıyla karşılaşırsanız, yeni bir disk göndermek için Microsoft Desteği başvurun. Daha fazla bilgi için, [StorSimple cihazınızda donanım uyarıları](storsimple-8000-manage-alerts.md#hardware-alerts)' na gidin.

* **Denetleyici değiştirme değişiklikleri** -kullanıcının denetleyici değiştirme işleminin durumunu sorgulamasını sağlayan bir cmdlet bu yayına eklenir. Daha fazla bilgi için, [Denetleyici değiştirme durumunu sorgulamak üzere cmdlet 'ine](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps)gidin.


## <a name="issues-fixed-in-update-4"></a>Güncelleştirme 4 ' te düzeltilen sorunlar

Aşağıdaki tabloda güncelleştirme 4 ' te düzeltilen sorunların özeti verilmiştir.    

| Hayır | Özellik | Sorun | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- |
| 1 |Yük devretme |Önceki sürümde, yük devretmeden sonra müşteri sitesinde gözlemlenen Temizleme ile ilgili bir sorun oluştu. Bu sorun bu sürümde düzeltilmiştir. |Yes |Yes |
| 2 |Yerel olarak sabitlenmiş birimler |Önceki sürümde, birim oluşturma hatalarıyla sonuçlanacağından yerel olarak sabitlenmiş birimler için ilgili birim oluşturma konusunda bir sorun oluştu. Bu sorun köklenmiştir ve bu sürümde düzeltildi. |Yes |Hayır |
| 3 |Destek paketi |Önceki sürümde, destek paketi ile ilgili, destek paketi oluşturma hatasına yol açacak bir sistem. OutOfMemory özel durumu veya diğer hatalara neden olacak sorunlar oluştu. Bu hatalar bu sürümde düzeltilmiştir. |Yes |Yes |
| 4 |İzleme |Önceki sürümde, yerel olarak sabitlenmiş birimlerde tüketimin, EB 'de gösterilmekte olan grafiklerle ilgili bir sorun var. Bu hata bu sürümde çözüldü. |Yes |Yes |
| 5 |Geçiş |Önceki sürümde, 5000-7000 serisinden 8000 serisi cihazlara geçişin güvenilirliğiyle ilgili birkaç sorun oluştu. Bu sorunlar bu sürümde ele alındı. |Yes |Yes |
| 6 |Güncelleştir |Önceki sürümlerde, bir güncelleştirme hatası oluşursa, denetleyiciler kurtarma moduna geçer ve bu nedenle Kullanıcı güncelleştirmeye devam edemeyebilir ve Microsoft Desteği iletişim kurabilmesi gerekir. <br> Bu davranış bu sürümde değiştirilmiştir. Her iki denetleyici de aynı sürümü (güncelleştirme 4) çalıştırdıktan sonra kullanıcının bir güncelleştirme hatası varsa, denetleyiciler kurtarma moduna geçmez. Kullanıcı bu hatayla karşılaşırsa, bir bit beklemeniz ve sonra güncelleştirmeyi yeniden denemeniz önerilir. Yeniden deneme başarılı olabilir. Yeniden deneme başarısız olursa, Microsoft Desteği ile bağlantı kurmanız gerekir. |Yes |Yes |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Önceki sürümlerden güncelleştirme 4 ' te bilinen sorunlar

Güncelleştirme 4 ' te bilinen yeni bir sorun yoktur. Önceki sürümlerden güncelleştirme 4 ' e aktarılan sorunların listesi için, [güncelleştirme 3 sürüm notları](storsimple-update3-release-notes.md#known-issues-in-update-3)' na gidin.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Güncelleştirme 4 ' te Seri Bağlantılı SCSI (SAS) denetleyicisi ve bellenim güncelleştirmeleri

Bu sürümde SAS denetleyicisi ve LSI sürücü ve bellenim güncelleştirmeleri vardır. Bu güncelleştirmeleri nasıl yükleyeceğiniz hakkında daha fazla bilgi için bkz. StorSimple cihazınıza [güncelleştirme 4](./storsimple-8000-install-update-4.md) ' ü yüklemek.

## <a name="virtual-device-updates-in-update-4"></a>Güncelleştirme 4 ' teki sanal cihaz güncelleştirmeleri

Bu güncelleştirme StorSimple Cloud Appliance uygulanamıyor (sanal cihaz olarak da bilinir). Yeni sanal cihazların oluşturulması gerekir. 

## <a name="next-step"></a>Sonraki adım

StorSimple cihazınıza [güncelleştirme 4 ' ü yüklemeyi](./storsimple-8000-install-update-4.md) öğrenin.