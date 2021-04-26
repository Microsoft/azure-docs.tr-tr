---
title: Azure IoT Hub sorunlarını giderme hatası 403006 Devicemaximumactivefileuploadlimitexceıbaşında
description: 403006 Devicemaximumactivefileuploadlimitexceıda hatasını nasıl düzelteceğinizi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3a70c11fd4f2a0549370933c300f431a03ffcf1d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061307"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Bu makalede, **403006 Devicemaximumactivefileuploadlimitexceıo** hatalarına yönelik nedenler ve çözümler açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

Dosya yükleme isteğiniz **403006** hata koduyla başarısız oluyor ve "etkin dosya yükleme isteklerinin sayısı 10 ' dan fazla olamaz.

## <a name="cause"></a>Nedeni

Her cihaz istemcisi, [10 eşzamanlı dosya yükleme](./iot-hub-devguide-quotas-throttling.md#other-limits)ile sınırlıdır. 

Dosya karşıya yükleme tamamlandığında cihazınız IoT Hub bildirimde yoksa, sınırı kolayca aşabilirsiniz. Bu sorun genellikle güvenilmez bir cihaz tarafı ağından kaynaklanır.

## <a name="solution"></a>Çözüm

Cihazın [karşıya dosya yükleme tamamlamayı IoT Hub derhal bilgilendirdiğinden](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)emin olun. Daha sonra, [dosya yükleme yapılandırması IÇIN SAS BELIRTECI TTL 'sini azaltmayı](iot-hub-configure-file-upload.md)deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Karşıya dosya yükleme hakkında daha fazla bilgi edinmek için bkz. [IoT Hub dosyaları karşıya yükleme](./iot-hub-devguide-file-upload.md) ve [Azure Portal kullanarak IoT Hub dosya yüklemelerini yapılandırma](./iot-hub-configure-file-upload.md).