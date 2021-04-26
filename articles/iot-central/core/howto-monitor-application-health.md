---
title: Azure IoT Central uygulamasının sistem durumunu izleme | Microsoft Docs
description: Operatör veya yönetici olarak, IoT Central uygulamanıza bağlı cihazların genel sistem durumunu izleyin.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: df89d53e6b5043c1ef3caa1c92f2abaae542d6ec
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599018"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Bir IoT Central uygulamasının genel sistem durumunu izleme

> [!NOTE]
> Ölçümler yalnızca sürüm 3 IoT Central uygulamalarında kullanılabilir. Uygulamanızın sürümünü nasıl denetleyeceğinizi öğrenmek için, bkz. [uygulamanız hakkında](./howto-get-app-info.md).

*Bu makale işleçler ve yöneticiler için geçerlidir.*

Bu makalede, IoT Central uygulamanıza bağlı cihazların sistem durumunu değerlendirmek ve çalışan verilerin dışarı aktarımlarının sistem durumunu değerlendirmek için IoT Central tarafından sunulan ölçüm kümesini nasıl kullanacağınızı öğreneceksiniz.

Ölçümler, IoT Central uygulamanız için varsayılan olarak etkindir ve bunlara [Azure Portal](https://portal.azure.com/)erişebilirsiniz. [Azure izleyici veri platformu bu ölçümleri kullanıma sunar](../../azure-monitor/essentials/data-platform-metrics.md) ve bunlarla etkileşimde bulunmak için çeşitli yollar sağlar. Örneğin, Azure portal, bir REST API veya PowerShell veya Azure CLı içindeki sorguları kullanabilirsiniz.

### <a name="trial-applications"></a>Deneme uygulamaları

Ücretsiz deneme planını kullanan uygulamalar ilişkili bir Azure aboneliğine sahip değildir ve Azure Izleyici ölçümlerini desteklemez. [Bir uygulamayı standart fiyatlandırma planına dönüştürebilir](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) ve bu ölçümlere erişim sağlayabilirsiniz.

## <a name="view-metrics-in-the-azure-portal"></a>Azure portal ölçümleri görüntüleme

Aşağıdaki adımlarda, bazı [bağlı cihazlara](./tutorial-connect-device.md) veya çalışan bir [veri dışa aktarmaya](howto-export-data.md)sahip bir [IoT Central uygulamasına](./quick-deploy-iot-central.md) sahip olduğunuz varsayılır.

Portalda IoT Central ölçümleri görüntülemek için:

1. Portalda IoT Central uygulama kaynağına gidin. Varsayılan olarak, IoT Central kaynaklar **ıotc** adlı bir kaynak grubunda bulunur.
1. Uygulamanızın ölçümlerinden bir grafik oluşturmak için **izleme** bölümünde **ölçümler** ' i seçin.

![Azure ölçümleri](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure portal izinleri

Azure portal ölçümlerine erişim, [Azure rol tabanlı erişim denetimi](../../role-based-access-control/overview.md)tarafından yönetilir. Kullanıcılara erişim izni vermek için IoT Central uygulama/kaynak grubu/aboneliğine Kullanıcı eklemek için Azure portal kullanın. IoT Central uygulamasına zaten eklendikleri halde portala bir Kullanıcı eklemeniz gerekir. Daha ayrıntılı erişim denetimi için [Azure yerleşik rollerini](../../role-based-access-control/built-in-roles.md) kullanın.

## <a name="iot-central-metrics"></a>IoT Central ölçümleri

IoT Central için şu anda kullanılabilen ölçümlerin listesi için bkz. [Azure izleyici Ile desteklenen ölçümler](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Ölçümler ve faturalar

Ölçümler, Azure IoT Central faturanızda gösterilen numaralardan farklı olabilir. Bu durum, şöyle bir nedenden dolayı meydana gelir:

- IoT Central [Standart fiyatlandırma planları](https://azure.microsoft.com/pricing/details/iot-central/) , ücretsiz olarak iki cihaz ve farklı ileti kotaları içerir. Ücretsiz öğeler faturalandırma dışında tutulurken, bunlar ölçümlerde hala sayılır.

- IoT Central, uygulamadaki her cihaz şablonu için bir test cihaz KIMLIĞI oluşturur. Bu cihaz KIMLIĞI, cihaz şablonu için **Test cihazını Yönet** sayfasında görünür. Çözüm oluşturucular, bu test cihaz kimliklerini kullanan kod oluşturarak cihaz şablonlarını yayımlamadan önce doğrulamayı seçebilirler. Bu cihazlar faturalandırma dışında tutulurken, ölçümlerde hala sayılır.

- Ölçümler cihazdan buluta iletişimin bir alt kümesini gösterebilirken, cihaz ile bulut arasındaki tüm iletişimler [faturalandırma için bir ileti olarak sayılır](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Sonraki adımlar

Uygulama şablonlarını nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [Azure portal IoT Central yönetmeyi](howto-manage-iot-central-from-portal.md)öğrenmektedir.