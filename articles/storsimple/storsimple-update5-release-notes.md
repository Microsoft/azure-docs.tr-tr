---
title: StorSimple 8000 serisi güncelleştirme 5 sürüm notları
description: StorSimple 8000 serisi güncelleştirme 5 için yeni özellikleri, sorunları ve geçici çözümleri açıklar.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021053"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 serisi güncelleştirme 5 sürüm notları

## <a name="overview"></a>Genel Bakış

Aşağıdaki sürüm notları, yeni özellikleri anlatmaktadır ve StorSimple 8000 serisi güncelleştirme 5 için kritik açık sorunları belirler. Ayrıca, bu sürüme dahil olan StorSimple yazılım güncelleştirmelerinin bir listesini içerir.

Güncelleştirme 5, güncelleştirme 0,1 ile güncelleştirme çalıştıran herhangi bir StorSimple cihazına uygulanabilir. Güncelleştirme 5 ile ilişkili cihaz sürümü 6.3.9600.17845.

Güncelleştirmeyi StorSimple çözümünüze dağıtmadan önce sürüm notlarında yer alan bilgileri gözden geçirin.

> [!IMPORTANT]
> * Güncelleştirme 5, zorunlu bir güncelleştirmedir ve hemen yüklenmesi gerekir. Daha fazla bilgi için bkz. [güncelleştirme 5](storsimple-8000-install-update-5.md)' i uygulama.
> * Güncelleştirme 5 ' te cihaz yazılımı, disk üretici yazılımı, işletim sistemi güvenliği ve diğer işletim sistemi güncelleştirmeleri vardır. Bu güncelleştirmenin yüklenmesi yaklaşık 4 saat sürer. Disk üretici yazılımı güncelleştirmesi, kesintiye uğratan bir güncelleştirmedir ve cihazınız için kapalı kalma süresine neden olur. Cihazınızı güncel tutmak için güncelleştirme 5 ' i uygulamanızı öneririz.
> * Güncelleştirmelerin aşamalı olarak piyasaya sürültiğimiz için yeni sürümler için güncelleştirmeleri hemen göremeyebilirsiniz. Birkaç gün bekleyin ve sonra bu güncelleştirmeler yakında kullanılabilir hale gelecağından güncelleştirmeleri yeniden tarayın.

## <a name="whats-new-in-update-5"></a>Güncelleştirme 5 ' teki yenilikler

Güncelleştirme 5 ' te aşağıdaki önemli geliştirmeler ve hata düzeltmeleri yapılmıştır.

* **Storsimple Device Manager hizmeti ile kimlik doğrulamak için Azure Active Directory (AAD) kullanımı** -güncelleştirme 5 ' ten başlayarak, storsimple Device Manager hizmetinde kimlik doğrulaması yapmak için Azure Active Directory kullanılır. Eski kimlik doğrulama mekanizması, Aralık 2017 tarafından kullanım dışı bırakılacak. Tüm kullanıcıların güvenlik duvarı kurallarında yeni kimlik doğrulama URL 'Leri içermesi gerekir. Daha fazla bilgi için, [StorSimple cihazınız için ağ gereksinimleri ' nde listelenen kimlik doğrulama URL 'lerine](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal)gidin.

    Kimlik doğrulama URL 'SI güvenlik duvarı kurallarında yoksa, kullanıcılar StorSimple cihazının hizmetten kimlik doğrulayamadığından önemli bir uyarı görür. Kullanıcılar bu uyarıyı görtiklerinde, yeni kimlik doğrulama URL 'sini içermesi gerekir. Daha fazla bilgi için, [StorSimple ağ uyarıları](storsimple-8000-manage-alerts.md#networking-alerts)' na gidin.

* **Storsimple Snapshot Manager yeni sürümü** -storsimple Snapshot Manager 'ın yeni bir sürümü güncelleştirme 5 ile yayımlanmıştır ve güncelleştirme 4 veya sonraki sürümleri çalıştıran tüm StorSimple cihazlarıyla uyumludur. Bu sürüme güncelleştirmenizi öneririz. StorSimple Snapshot Manager önceki sürümü, güncelleştirme 3 veya öncesini çalıştıran StorSimple cihazları için kullanılır. [Storsimple Snapshot Manager uygun sürümünü indirin](https://www.microsoft.com/en-us/download/details.aspx?id=44220) ve [StorSimple Snapshot Manager dağıtma](storsimple-snapshot-manager-deployment.md)bölümüne bakın.


## <a name="issues-fixed-in-update-5"></a>Güncelleştirme 5 ' te düzeltilen sorunlar

Aşağıdaki tabloda güncelleştirme 5 ' te düzeltilen sorunların özeti verilmiştir.

| Hayır | Özellik | Sorun | Fiziksel cihaz için geçerlidir | Sanal cihaz için geçerlidir |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell uzaktan iletişim |Önceki sürümde, bir Kullanıcı Windows PowerShell aracılığıyla StorSimple Cloud Appliance uzak bağlantı kurmaya çalışırken bir hata alıyor. Bu sorun köklenmiştir ve bu sürümde düzeltildi. |Hayır |Yes |
| 2 |Bant genişliği şablonları |Daha önceki sürümlerde, bant genişliği şablonlarındaki bir sorun oluştu ve bu, cihazın için yapılandırılmış olandan daha düşük bant genişliğine neden oldu. Bu sorun, bu yayında çözümlenir. |Yes |Yes |
| 3 |Yük devretme |Önceki sürümde, çok sayıda birime sahip bir cihaz, güncelleştirme 4 çalıştıran başka bir cihaza devredildiği zaman, erişim denetim kayıtlarını uygulamaya çalışırken işlem başarısız olur. Bu sorun bu sürümde düzeltilmiştir. |Yes |Yes |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Önceki sürümlerden güncelleştirme 5 ' teki bilinen sorunlar

Güncelleştirme 5 ' te bilinen yeni bir sorun yoktur. Önceki sürümlerden güncelleştirme 5 ' e aktarılan sorunların listesi için, [güncelleştirme 3 sürüm notları](storsimple-update3-release-notes.md#known-issues-in-update-3)' na gidin.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Güncelleştirme 5 ' teki seri bağlantılı SCSI (SAS) denetleyicisi ve bellenim güncelleştirmeleri

Bu sürümde SAS denetleyicisi ve LSI sürücü ve bellenim güncelleştirmeleri vardır. Bu güncelleştirmeleri nasıl yükleyeceğiniz hakkında daha fazla bilgi için bkz. StorSimple cihazınıza [güncelleştirme 5](storsimple-8000-install-update-5.md) ' i yüklemek.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Güncelleştirme 5 ' teki StorSimple Cloud Appliance güncelleştirmeleri

Bu güncelleştirme StorSimple Cloud Appliance uygulanamıyor (sanal cihaz olarak da bilinir). Yeni bulut gereçlerinin güncelleştirme 5 görüntüsü kullanılarak oluşturulması gerekir. StorSimple Cloud Appliance oluşturma hakkında daha fazla bilgi için, [StorSimple Cloud Appliance Dağıt ve Yönet](storsimple-8000-cloud-appliance-u2.md)' e gidin.

## <a name="next-step"></a>Sonraki adım

StorSimple cihazınıza [güncelleştirme 5](storsimple-8000-install-update-5.md) ' i nasıl yükleyeceğinizi öğrenin.

