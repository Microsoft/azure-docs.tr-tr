---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c97c8231265cf87f52333a56d21d6fb13180c554
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808863"
---
## <a name="create-base-resources"></a>Temel kaynaklar oluşturma

İleti yönlendirmeyi yapılandırmadan önce bir IoT Hub, bir depolama hesabı ve Service Bus kuyruğu oluşturmanız gerekir. Bu kaynaklar, Bu öğreticinin 1. bölümünde kullanılabilen dört makale kullanılarak oluşturulabilir: Azure portal, bir Azure Resource Manager şablonu, Azure CLı veya Azure PowerShell.

Tüm kaynaklar için aynı kaynak grubunu ve konumunu kullanın. Sonunda, kaynak grubunu silerek tüm kaynakları tek bir adımda kaldırabilirsiniz.

Aşağıdaki bölümlerde gerçekleştirilecek adımların özeti aşağıda verilmiştir: 

1. Bir [kaynak grubu](../articles/azure-resource-manager/resource-group-overview.md) oluşturun.

2. S1 katmanında IoT hub'ı oluşturun. IOT hub'ınızı bir tüketici grubu ekleyin. Tüketici grubu Azure Stream Analytics tarafından veriler alınırken kullanılır.

   > [!NOTE]
   > Bu öğreticiyi tamamlayabilmeniz için ücretli bir katmanda IoT Hub 'ı kullanmanız gerekir. Ücretsiz katman yalnızca bir uç nokta ayarlamanıza izin verir ve bu öğretici birden fazla uç nokta gerektirir.
   > 

3. Standard_LRS çoğaltmasıyla standart bir V1 depolama hesabı oluşturun.

4. Service Bus ad alanı ve kuyruğu oluşturun.

5. Hub'ınıza iletiler gönderen simülasyon cihazı için cihaz kimliği oluşturun. Test aşaması için anahtarı kaydedin. (Kaynak Yöneticisi şablonu oluşturuyorsanız, bu, şablonu dağıttıktan sonra yapılır.)