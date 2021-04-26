---
title: Windows 'da Linux için Azure IoT Edge nedir | Microsoft Docs
description: Genel bakış Windows 10 cihazlarında Linux IoT Edge modülleri çalıştırabilirsiniz
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538194"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Windows üzerinde Linux için Azure IoT Edge nedir (Önizleme)?

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows üzerinde Linux için Azure IoT Edge, Windows IoT dağıtımlarında Windows uygulamaları ile Kapsayıcılı Linux iş yüklerini çalıştırmanıza olanak sağlar. Windows IoT 'yi kullanan işletmeler, Edge cihazlarını güçlendirin ve Linux 'ta yerleşik olarak bulunan bulut Yerel analizler çözümlerinden faydalanabilir.

Windows üzerinde Linux için IoT Edge, bir Windows cihazında Linux sanal makinesi çalıştırarak çalışır. Linux sanal makinesi, IoT Edge çalışma zamanına göre önceden yüklenmiş olarak gelir. Cihaza dağıtılan tüm IoT Edge modülleri sanal makine içinde çalışır. Bu arada, Windows konak cihazında çalışan Windows uygulamaları Linux sanal makinesinde çalışan modüllerle iletişim kurabilir.

Önizlemeyi bugün [kullanmaya](how-to-install-iot-edge-on-windows.md) başlayın.

>[!NOTE]
>Lütfen IoT Edge arka planınızı ve hedeflerinize bağlı olarak Windows 'da Linux için Azure IoT Edge geliştirmemize yardımcı olmak üzere [ürün anketimizi](https://aka.ms/AzEFLOW-Registration) göz önünde bulundurun. Bu anketi Ayrıca, Windows bildirilerinde Linux için gelecekteki Azure IoT Edge kaydolmak üzere de kullanabilirsiniz.

## <a name="components"></a>Bileşenler

Windows üzerinde Linux için IoT Edge, Linux ve Windows iş yüklerinin birbirleriyle birlikte çalışmasını ve sorunsuz iletişim kurmasını sağlamak için aşağıdaki bileşenleri kullanır:

* **Azure IoT Edge çalıştıran bir Linux sanal makinesi**: Microsoft 'un birinci taraf [Mariner](https://github.com/microsoft/CBL-Mariner) Işletim sistemine dayanan bir linux sanal makinesi, IoT Edge çalışma zamanına göre oluşturulmuştur ve IoT Edge iş yükleri için Katman 1 desteklenen bir ortam olarak onaylanır.

* **Windows Yönetim Merkezi**: Windows Yönetim merkezi için IoT Edge uzantısı, Linux sanal makinesinde IoT Edge yükleme, yapılandırma ve tanılamayı kolaylaştırır. Windows Yönetim Merkezi, Linux için IoT Edge yerel cihazda dağıtabilir veya hedef cihazlara bağlanıp onları uzaktan yönetebilir.

* **Microsoft Update**: Microsoft Update tümleştirmesi, Windows çalışma zamanı bileşenlerini, Mariner Linux sanal makinesini ve IoT Edge güncel tutar.

![Windows ve Linux sanal makinesi paralel olarak çalışır, ancak Windows yönetici merkezi her iki bileşeni de denetler](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows süreci ile Linux sanal makinesi arasındaki çift yönlü iletişim, Windows işlemlerinin Linux kapsayıcılarında çalışan iş yükleri için Kullanıcı arabirimleri veya donanım proxy 'leri sağlayabileceği anlamına gelir.

## <a name="samples"></a>Örnekler

Windows üzerinde Linux için IoT Edge, Linux ve Windows bileşenleri arasında birlikte çalışabilirliği vurgular.

Windows Uygulamaları ve IoT Edge modülleri arasındaki iletişimi gösteren örnekler için bkz. [eFlow & Windows 10 IoT örnekleri](https://aka.ms/AzEFLOW-Samples).

## <a name="public-preview"></a>Genel Önizleme

Windows üzerinde Linux için IoT Edge Şu anda [genel önizlemededir](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Yükleme ve yönetim işlemi, genel kullanıma sunulan özelliklerden farklı olabilir.

## <a name="support"></a>Destek

Windows 'da Linux için IoT Edge hakkında yardım almak için destek ve geri bildirim kanallarını IoT Edge kullanın.

**Hataları raporlama** – hata IoT Edge açık kaynaklı projenin [sorunlar sayfasında](https://github.com/azure/iotedge/issues) hatalar bildirilebilir. Windows üzerinde Linux için Azure IoT Edge ilgili hatalar, [ıotedge-eFlow sorunları sayfasında](https://aka.ms/AzEFLOW-Issues)bildirilebilir.

**Microsoft müşteri destek ekibi** – [Destek planına](https://azure.microsoft.com/support/plans/) sahip olan kullanıcılar, doğrudan [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)bir destek bileti oluşturarak Microsoft müşteri destek ekibine geçebilir.

**Özellik istekleri** – Azure IoT Edge ürünü, ürünün [Kullanıcı sesi sayfası](https://feedback.azure.com/forums/907045-azure-iot-edge)aracılığıyla özellik isteklerini izler.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi ve bir örnek eylem için [Windows 10 IoT Enterprise 'Ta Linux için IoT Edge](https://aka.ms/EFLOWPPC9) izleyin.

Windows üzerinde Linux için IoT Edge bir cihaz kurmak üzere [Windows cihazında Linux için Azure IoT Edge yükleyip sağlama](how-to-install-iot-edge-on-windows.md) bölümündeki adımları izleyin.
