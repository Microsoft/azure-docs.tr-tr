---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 91b6f1ed06fbf1f5575650f96f4622b3df9ca083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98187388"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Sonra bir cihaz kimliği oluşturun ve anahtarını daha sonra kullanmak üzere kaydedin. Bu cihaz kimliği simülasyon uygulaması tarafından IoT hub'ına iletileri göndermek için kullanılır. Bu özellik, PowerShell 'de veya Azure Resource Manager şablonu kullanılırken kullanılamaz. Aşağıdaki adımlarda, [Azure Portal](https://portal.azure.com)kullanarak sanal cihazı nasıl oluşturacağınız açıklanır.

1. [Azure portalını](https://portal.azure.com) açın ve Azure hesabınızla oturum açın.

2. **Kaynak grupları** ' nı seçin ve ardından kaynak grubunuzu seçin. Bu öğreticide **ContosoResources** kullanılır.

3. Kaynak listesinde IoT Hub 'ınızı seçin. Bu öğreticide **ContosoTestHub** kullanılır. Hub bölmesinden **IoT Cihazları**'nı seçin.

4. **+ Yeni** seçeneğini belirleyin. Cihaz Ekle bölmesinde cihaz kimliğini girin. Bu öğreticide **Contoso-Test-Device** kullanılır. Anahtarları boş bırakın ve **Anahtarları Otomatik Olarak Oluştur**'a tıklayın. **Cihazı IoT Hub'ına bağla** seçeneğinin etkinleştirildiğinden emin olun. **Kaydet**’i seçin.

   ![Cihaz Ekle ekranı](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Artık oluşturulduğuna göre, oluşturulan anahtarları görmek için cihazı seçin. Birincil anahtarda Kopyala simgesini seçin ve Bu öğreticinin test aşaması için Not Defteri gibi bir yere kaydedin.

   ![Anahtarlar dahil cihaz ayrıntıları](./media/iot-hub-include-create-simulated-device-portal/device-details.png)