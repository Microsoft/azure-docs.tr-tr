---
title: Karşıya dosya yüklemeyi yapılandırmak için Azure portal kullanın | Microsoft Docs
description: Bağlı cihazlardan dosya yüklemelerini etkinleştirmek üzere IoT Hub 'ınızı yapılandırmak için Azure portal nasıl kullanılır. Hedef Azure Depolama hesabını yapılandırma hakkında bilgi içerir.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92535884"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Azure portalını kullanarak IoT Hub dosya karşıya yükleme işlemlerini yapılandırma

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Karşıya dosya yükleme

[IoT Hub içinde karşıya dosya yükleme işlevini](iot-hub-devguide-file-upload.md)kullanmak için, önce bir Azure Depolama hesabını merkeziniz ile ilişkilendirmeniz gerekir. Değiştirilmekte olan IoT Hub 'ı için karşıya dosya yükleme özelliklerinin listesini göstermek için **karşıya dosya yükleme** ' yi seçin.

![Portalda karşıya dosya yükleme ayarlarını görüntüleme IoT Hub](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Depolama kapsayıcısı**: IoT Hub ilişkilendirmek üzere geçerli Azure aboneliğinizde bir Azure depolama hesabındaki blob kapsayıcısını seçmek için Azure Portal kullanın. Gerekirse, **kapsayıcılar** dikey penceresinde **depolama hesapları** dikey penceresinde ve BLOB kapsayıcısında bir Azure depolama hesabı oluşturabilirsiniz. IoT Hub, dosyaları karşıya yüklerken kullanılacak cihazlar için bu blob kapsayıcısına yazma izinleri olan SAS URI 'Leri otomatik olarak oluşturur.

   ![Portalda karşıya dosya yükleme için depolama kapsayıcılarını görüntüleme](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Karşıya yüklenen dosyalar için bildirim al**: geçiş yoluyla karşıya dosya yükleme bildirimlerini etkinleştirin veya devre dışı bırakın.

* **SAS TTL**: Bu ayar, cihaza IoT Hub tarafından döndürülen sas URI 'lerinin yaşam süresi olarak belirlenir. Varsayılan olarak bir saate ayarlanır ancak kaydırıcı kullanılarak diğer değerlere özelleştirilebilir.

* **Dosya bildirim ayarları varsayılan TTL**: süresi dolmadan önce karşıya dosya yükleme bildiriminin yaşam süresi. Varsayılan olarak bir güne ayarlanır ancak kaydırıcı kullanılarak diğer değerlere özelleştirilebilir.

* **Dosya bildirimi en fazla teslimat sayısı**: IoT Hub dosya yükleme bildirimi sunmaya kaç kez girişimde bulunulmasını sağlar. Varsayılan olarak 10 olarak ayarlanır ancak kaydırıcı kullanılarak diğer değerlere özelleştirilebilir.

   ![Portalda IoT Hub dosya yüklemeyi yapılandırma](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub dosya yükleme özellikleri hakkında daha fazla bilgi için, bkz. IoT Hub geliştirici kılavuzundaki [bir cihazdan dosya yükleme](iot-hub-devguide-file-upload.md) .

Azure IoT Hub 'yi yönetme hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [IoT cihazlarını toplu yönetme](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 'ınızı izleme](monitor-iot-hub.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IOT Edge ile sınır cihazlarına Al dağıtma](../iot-edge/quickstart-linux.md)
* [IoT çözümünüzü baştan sona güvenli hale getirin](../iot-fundamentals/iot-security-ground-up.md)