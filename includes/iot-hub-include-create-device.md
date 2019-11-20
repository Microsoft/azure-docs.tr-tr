---
title: include dosyası
description: include dosyası
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049060"
---
<!-- put the ## header in the file that includes this file -->

Bu bölümde, IoT Hub 'ınızdaki kimlik kayıt defterinde bir cihaz kimliği oluşturacaksınız. Kimlik kayıt defterinde bir girişi olmadığı takdirde bir cihaz bir hub 'a bağlanamaz. Daha fazla bilgi için [IoT Hub Geliştirici Kılavuzu](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)' na bakın.

1. IoT Hub 'ınız gezinti menüsünde **IoT cihazları**' nı açın ve ardından IoT Hub 'ınıza bir cihaz eklemek için **Yeni** ' yi seçin.

    ![Portalda cihaz kimliği oluşturma](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. **Cihaz oluştur**' da, yeni cihazınız Için **mydeviceıd**gibi bir ad girin ve **Kaydet**' i seçin. Bu eylem, IoT Hub 'ınız için bir cihaz kimliği oluşturur.

   ![Yeni bir cihaz ekleyin](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Cihaz oluşturulduktan sonra **IoT cihazları** bölmesindeki listeden cihazı açın. Daha sonra kullanmak üzere **birincil bağlantı dizesini** kopyalayın.

    ![Cihaz bağlantı dizesi](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT Hub kimlik kayıt defteri, yalnızca IoT hub'ına güvenli erişim sağlamak amacıyla cihaz kimliklerini depolar. Güvenlik kimlik bilgileri olarak kullanılmak üzere cihaz kimliklerini ve anahtarlarını ve tek bir cihaza erişimi devre dışı bırakmak için kullanabileceğiniz etkin/devre dışı bayrağını depolar. Uygulamanızın cihaza özgü diğer meta verileri depolaması gerekiyorsa uygulamaya özgü bir depo kullanması gerekir. Daha fazla bilgi için bkz. [IoT Hub Geliştirici Kılavuzu](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
