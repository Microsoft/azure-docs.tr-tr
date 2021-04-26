---
title: Mimari IoT Smart Inventory Management | Microsoft Docs
description: IoT Central için IoT Smart Inventory Management Template mimarisi
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832649"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Akıllı envanter yönetimi uygulama şablonu IoT Central mimarisi

İş ortakları ve müşteriler, uçtan uca **akıllı envanter yönetimi** çözümlerini geliştirmek için uygulama şablonu ve Aşağıdaki kılavuzlardan yararlanabilirsiniz.

> [!div class="mx-imgBorder"]
> ![Akıllı envanter yönetimi](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Bir ağ geçidi cihazına telemetri verileri gönderen IoT sensörleri kümesi
2. IoT Central telemetri ve toplu Öngörüler gönderen ağ geçidi cihazları
3. Veriler, işleme için istenen Azure hizmetine yönlendirilir
4. ASA veya Azure Işlevleri gibi Azure Hizmetleri, veri akışlarını yeniden biçimlendirmek ve istenen depolama hesaplarına göndermek için kullanılabilir 
5. İşlenen veriler, ML veya Batch analizine dayalı ek Öngörüler için neredeyse gerçek zamanlı eylemler veya soğuk depolama için sık erişimli depolama alanında depolanır. 
6. Logic Apps, son kullanıcı iş uygulamalarında çeşitli iş iş akışlarını desteklemek için kullanılabilir

## <a name="details"></a>Ayrıntılar
Aşağıdaki bölümde, radyo frekansı tanımlama (RFıD), Bluetooth düşük enerji (BLE) etiketleriyle kavramsal mimari telemetri alma 'nın her bir bölümü özetlenmektedir

## <a name="rfid-tags"></a>RFıD etiketleri
RFıD etiketleri radyo dalgaları aracılığıyla bir öğeyle ilgili verileri iletir. Yalnızca belirtilmemişse, RFıD etiketlerinin pili yoktur. Etiketler, okuyucu tarafından oluşturulan radyo dalgalarından enerji alır ve RFıD okuyucusuna doğru bir sinyal gönderir.

## <a name="ble-tags"></a>BLO etiketleri
Enerji işaret, düzenli aralıklarla veri paketleri yayınlar. İşaret verileri, uyumlu olmayan okuyucular veya akıllı telefonlarda yüklü hizmetler tarafından algılanır ve ardından buluta iletiliyor.

## <a name="rfid--ble-readers"></a>RFıD & BLE okuyucuları
RFıD okuyucusu radyo dalgalarını daha kullanılabilir bir veri biçimine dönüştürür. Etiketlerden toplanan bilgiler, daha sonra MQTT üzerinden JSON-RPC 2,0 kullanılarak yerel uç sunucuda depolanır veya buluta gönderilir.
Erişim noktaları (AP) olarak da bilinen BLE okuyucusu, RFıD okuyucusuna benzer. Yakındaki Bluetooth sinyallerini algılamak ve iletisini MQTT üzerinden JSON-RPC 2,0 kullanarak yerel Azure IoT Edge veya buluta geçirmek için kullanılır.
Birçok okuyucu, RFıD ve işaret sinyallerini okuyabilir ve sıcaklık, nem, ivme ölçer ve jroscope ile ilgili ek algılayıcı yeteneği sağlar.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Ağ Geçidi
Azure IoT Edge sunucusu, verileri buluta göndermeden önce yerel olarak önceden işlemek için bir yer sağlar. Ayrıca, standart kapsayıcıları kullanarak bulut iş yükleri yapay zeka, Azure ve üçüncü taraf hizmetleri, iş mantığını de dağıtabiliriz.

## <a name="device-management-with-iot-central"></a>IoT Central ile cihaz yönetimi 
Azure IoT Central, IoT cihaz bağlantısı, yapılandırma ve yönetimini kolaylaştıran bir çözüm geliştirme platformudur. Platform IoT cihaz yönetimi, işlemler ve ilgili geliştirmelerin yükünü ve maliyetlerini önemli ölçüde azaltır. Müşteriler & iş ortakları, envanter yönetiminde dijital bir geri bildirim döngüsüne ulaşmak için uçtan uca bir kurumsal çözüm oluşturabilir.

## <a name="business-insights--actions-using-data-egress"></a>Veri çıkışı kullanan iş öngörüleri & eylemleri 
IoT Central platform sürekli veri dışa aktarma (CDE) ve API 'Ler aracılığıyla zengin genişletilebilirlik seçenekleri sağlar. Telemetri veri işleme veya ham telemetri temelinde iş öngörüleri genellikle tercih edilen iş kolu uygulamasına aktarılabilir. Daha zengin Öngörüler & makine öğrenimi modellerini derlemek, eğitme ve dağıtmak için Web kancası, hizmet veri yolu, Olay Hub 'ı veya blob depolama kullanılarak elde edilebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Akıllı envanter yönetimi şablonunu](./tutorial-iot-central-smart-inventory-management.md) dağıtmayı öğrenin
* [IoT Central perakende şablonları](./overview-iot-central-retail.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../core/overview-iot-central.md)
