---
title: include dosyası
description: include dosyası
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: f88f6aa6daec342b24d165c4c4cc0a89a0805f7a
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558486"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Bir Azure Depolama hesabını IoT Hub ilişkilendir

Sanal cihaz uygulaması bir blob 'a dosya yüklediğinde, IoT Hub 'ınız ile ilişkili bir [Azure depolama](../articles/storage/common/storage-quickstart-create-account.md) hesabınızın olması gerekir. Bir Azure Depolama hesabını IoT Hub ile ilişkilendirdiğinizde, IoT Hub 'ı bir SAS URI 'SI oluşturur. Bir cihaz, bir dosyayı bir blob kapsayıcısına güvenli bir şekilde yüklemek için bu SAS URI 'sini kullanabilir. IoT Hub hizmeti ve cihaz SDK 'ları, SAS URI 'sini oluşturan işlemi koordine edin ve bir dosyayı karşıya yüklemek için kullanılacak bir cihaz için kullanılabilir hale getirir.

[Azure Portal kullanarak dosya karşıya yüklemelerini yapılandırma](../articles/iot-hub/iot-hub-configure-file-upload.md)bölümündeki yönergeleri izleyin. Bir blob kapsayıcısının IoT Hub 'ınız ile ilişkilendirildiğinden ve dosya bildirimlerinin etkinleştirildiğinden emin olun.

![Portalda dosya bildirimlerini etkinleştir](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
