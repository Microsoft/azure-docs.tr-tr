---
title: Azure IoT Hub Aracısı için cihaz güncelleştirmesini anlama | Microsoft Docs
description: Azure IoT Hub Aracısı için cihaz güncelleştirmesini anlayın.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d97287657b1e1fe7d540e8811c90794aaa5fece
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739575"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>IoT Hub Aracısı için Cihaz Güncelleştirmesi'ne Genel Bakış

Cihaz Güncelleştirme Aracısı iki kavramsal katmandan oluşur:

* Arabirim katmanı, cihaz Güncelleştirme Aracısı ile cihaz güncelleştirme hizmetleri arasında ileti akışına izin veren [Azure ıot Tak ve kullan (PnP)](../iot-pnp/overview-iot-plug-and-play.md) üzerinde oluşturulur.
* Platform katmanı, platform veya cihaza özgü olabilecek Indirme, yükleme ve uygulama işlemlerinin üst düzey güncelleştirme eylemleriyle sorumludur.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Aracı uygulamaları." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Arabirim katmanı

Arabirim katmanı, [ADU Core arabiriminden](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) ve [cihaz bilgileri arabiriminden](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)yapılır.

Bu arabirimler varsayılan değerler için bir yapılandırma dosyasını kullanır. Varsayılan değerler, AzureDeviceUpdateCore arabirimi ve model ve üreticinin Deviceınformation arabirimine yönelik aduc_manufacturer ve aduc_model içerir. Yapılandırma dosyası [hakkında daha fazla bilgi edinin](device-update-configuration-file.md) .

### <a name="adu-core-interface"></a>ADU çekirdek arabirimi

' ADU Core ' arabirimi, cihaz Güncelleştirme Aracısı ve hizmetleri arasındaki birincil iletişim kanalsıdır. Bu arabirim hakkında [daha fazla bilgi edinin](device-update-plug-and-play.md#adu-core-interface) .

### <a name="device-information-interface"></a>Cihaz bilgileri arabirimi

Cihaz bilgileri arabirimi arabirimini uygulamak için kullanılır `Azure IoT PnP DeviceInformation` . Bu arabirim hakkında [daha fazla bilgi edinin](device-update-plug-and-play.md#device-information-interface) .

## <a name="the-platform-layer"></a>Platform katmanı

Platform katmanının iki uygulaması vardır. Simülatör platformu katmanının, güncelleştirme eylemlerinin önemsiz bir uygulamasına sahip olması ve IoT Hub hizmetleri ve kurulumu için cihaz güncelleştirmesini hızlıca test etmek ve değerlendirmek için kullanılır. Cihaz Güncelleştirme Aracısı simülatör platform katmanıyla oluşturulduğunda, cihaz güncelleştirme simülatör Aracısı veya yalnızca simülatör olarak buna değineceğiz. Simülatör aracısının nasıl kullanılacağı hakkında [daha fazla bilgi edinin](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) . Linux platform katmanı, indirmeler için [teslim iyileştirmesi](https://github.com/microsoft/do-client) ile tümleşir ve Raspberry PI başvuru Görüntümiz ve Linux sistemlerinde çalışan tüm istemciler için kullanılır.

### <a name="simulator-platform-layer"></a>Simülatör platform katmanı

Simülatör platformu katman uygulamasına ' de bulunabilir `src/platform_layers/simulator_platform_layer` ve IoT Hub Için cihaz güncelleştirmesini test etmek ve değerlendirmek üzere kullanılabilir.  "Simülatör" uygulamasındaki eylemlerin birçoğu, IoT Hub için cihaz güncelleştirmesiyle denemeler yapmak için fiziksel değişiklikleri azaltmaya yönelik olarak tasarlanmıştır.  Uçtan uca "benzetimli" güncelleştirme, bu platform katmanı kullanılarak gerçekleştirilebilir. Simülatör aracısını çalıştırma hakkında [daha fazla bilgi edinin](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) .

### <a name="linux-platform-layer"></a>Linux platform katmanı

Linux platform katmanı uygulamasının üzerinde bulunabilir `src/platform_layers/linux_platform_layer` ve bu, indirme Için [teslim iyileştirme istemcisiyle](https://github.com/microsoft/do-client/releases) tümleştirilir ve Raspberry PI başvuru görüntüsünde ve Linux sistemlerinde çalışan tüm istemcilerde kullanılır.

Bu katman, yükleyiciyi uygulamak için farklı güncelleştirme Işleyicileriyle tümleştirilebilir. Örneğin, `SWUpdate` güncelleştirme işleyicisi, `SWUpdate` bir güncelleştirmeyi gerçekleştirmek için çalıştırılabilire çağırmak üzere bir kabuk betiği çağırır.

## <a name="update-handlers"></a>Işleyicileri Güncelleştir

Güncelleştirme Işleyicileri, güncelleştirmenin içeriğini veya yükleyiciye özgü bölümlerini işleyen bileşenlerdir. Güncelleştirme Işleyicisi uygulamaları içinde bulunur `src/content_handlers` .

### <a name="simulator-update-handler"></a>Simülatör güncelleştirme Işleyicisi

Simülatör güncelleştirme Işleyicisi simülatör platform katmanı tarafından kullanılır ve bir Içerik Işleyicisiyle sahte etkileşimler için Linux platform katmanıyla birlikte kullanılabilir. Simülatör güncelleştirme Işleyicisi, güncelleştirme Işleyici API 'Lerini genellikle Hayır-Ops ile uygular. Simülatör güncelleştirme Işleyicisinin uygulanması aşağıda bulunabilir:
* [Görüntü güncelleştirme simülatörü](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Paket güncelleştirmesi apt simülatörü](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Note: AzureDeviceUpdateCore PnP arabirimindeki ınstalınstalte Criteria alanının içeriğin SHA256 karması olması gerekir. Bu, [Içeri aktarma bildirimi nesnesinde](import-update.md#create-a-device-update-import-manifest)mevcut olan karmadır. Ve arabirimi hakkında [daha fazla bilgi edinin](device-update-plug-and-play.md) `installedCriteria` `AzureDeviceUpdateCore` .

### <a name="swupdate-update-handler"></a>`SWUpdate` Güncelleştirme Işleyicisi

`SWUpdate`Güncelleştirme işleyicisi, `SWUpdate` özel olarak Raspberry PI başvuru görüntüsüne yönelik bir/B güncelleştirmeleri uygulamak için komut satırı yürütülebilir dosyası ve diğer kabuk komutlarıyla tümleştirilir. En son Raspberry PI başvuru görüntüsünü [buradan](https://github.com/Azure/iot-hub-device-update/releases)bulun. `SWUpdate`Güncelleştirme işleyicisi [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler)içinde uygulanır.

### <a name="apt-update-handler"></a>APT güncelleştirme Işleyicisi

APT güncelleştirme Işleyicisi, bir APT 'e özgü güncelleştirme bildirimini işler ve belirtilen detem paketlerini yüklemek veya güncelleştirmek için APT 'yi çağırır.

## <a name="self-update-device-update-agent"></a>Self-Update cihaz Güncelleştirme Aracısı

Cihaz Güncelleştirme Aracısı ve bağımlılıkları IoT Hub işlem hattı için cihaz güncelleştirmesi aracılığıyla güncelleştirilebilen olabilir. Görüntü tabanlı bir güncelleştirme kullanıyorsanız, yeni yansımanıza en son cihaz güncelleştirme aracısını ekleyin. Paket tabanlı güncelleştirme kullanıyorsanız, cihaz güncelleştirme aracısını ve istenen sürümünü, diğer tüm paketler gibi apt bildirimine ekleyin. Apt bildirimi hakkında [daha fazla bilgi edinin](device-update-apt-manifest.md) . Cihaz güncelleştirme aracısının ve teslim Iyileştirme aracısının yüklü sürümünü [IoT cihazınızın](../iot-hub/iot-hub-devguide-device-twins.md)cihaz özellikleri bölümünde kontrol edebilirsiniz. [ADU Core Interface altında cihaz özellikleri hakkında daha fazla bilgi edinin](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Sonraki Adımlar
[Cihaz Güncelleştirme Aracısı yapılandırma dosyasını anlama](device-update-configuration-file.md)