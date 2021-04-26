---
title: Azure IoT Central bir Rigado basamakla 500 | Microsoft Docs
description: Bir Rigado basamaklı 500 ağ geçidi cihazını IoT Central uygulamanıza bağlamayı öğrenin.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014771"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Bir Rigado basamaklı 500 ağ geçidi cihazını Azure IoT Central uygulamanıza bağlama

*Bu makale, çözüm oluşturucuları için geçerlidir.*

Bu makalede, bir çözüm Oluşturucusu olarak bir Rigado basamaklı 500 ağ geçidi cihazını Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklanmaktadır. 

## <a name="what-is-cascade-500"></a>Cascade 500 nedir?

Basamaklı 500 IoT Ağ Geçidi, kendi basamaklı hizmet olarak Edge çözümünün bir parçası olarak bulunan Rigado 'ın sunduğu bir donanım tekliftir. Esnek sınır bilgi işlem gücü, güçlü kapsayıcılı bir uygulama ortamı ve Bluetooth 5, LTE, & Wi-Fi gibi çok sayıda kablosuz cihaz bağlantısı seçeneği sunan ticari IoT projesi ve ürün ekipleri sağlar.

Cascade 500, çözüm oluşturucularımızın cihazı uçtan uca çözümlere kolayca açmasına olanak tanımak için Azure IoT Tak ve Kullan (Önizleme) için önceden sertifikalandırilmiştir. Basamaklı ağ geçidi, ağ geçidi cihazına yakınlık halinde olan çeşitli durum izleme sensörlerinden kablosuz olarak bağlanmanıza olanak sağlar. Bu sensörler, ağ geçidi cihazı aracılığıyla IoT Central eklendi olabilir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için aşağıdaki kaynaklara ihtiyacınız vardır:

* Bir Rigado Cascade 500 cihazı. Daha fazla bilgi için lütfen [Rigado](https://www.rigado.com/)adresini ziyaret edin.
* Azure IoT Central uygulaması. Daha fazla bilgi için [Yeni uygulama oluşturma](./quick-deploy-iot-central.md)bölümüne bakın.

## <a name="add-a-device-template"></a>Cihaz şablonu ekleme

Basamaklı 500 ağ geçidi cihazını Azure IoT Central uygulama örneğinize eklemek için, uygulamanız içinde karşılık gelen bir cihaz şablonunu yapılandırmanız gerekir.

Basamaklı 500 cihaz şablonu eklemek için: 

1. Sol bölmedeki ***cihaz şablonları** _ sekmesine gidin, _ * + yeni * *: ![ yeni cihaz şablonu oluştur ' u seçin.](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Sayfa size ***özel şablon oluşturma** _ veya _ ' i *_önceden yapılandırılmış bir cihaz şablonu kullanma_* seçeneği sunar.*
1. Önceden yapılandırılmış cihaz şablonları listesinden aşağıda gösterildiği gibi C500 cihaz şablonunu seçin: ![ C500 cihaz şablonu seçin](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Sonraki adıma devam etmek için Ileri 'yi seçin ***: Özelleştir*** . 
1. Bir sonraki ekranda, C500 cihaz şablonunu IoT Central uygulamanıza eklemek için ***Oluştur*** ' u seçin.

## <a name="retrieve-application-connection-details"></a>Uygulama bağlantısı ayrıntılarını al

Cascade 500 cihazını bağlamak için artık Azure IoT Central uygulamanızın **kapsam kimliğini** ve **birincil anahtarını** almanız gerekir. 

1. Sol bölmedeki **Yönetim**  ' e gidin ve **cihaz bağlantısı**' na tıklayın. 
2. IoT Central uygulamanızın **kapsam kimliğini** bir yere getirin.
![Uygulama kapsamı KIMLIĞI](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Şimdi **anahtarları görüntüle** ' ye tıklayın ve **birincil anahtar** 
 ![ birincil anahtarını bir yere göz önünde yapın](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Ağ geçidine bağlanmak için Rigado ile iletişim kurun 

Cascade 500 cihazını IoT Central uygulamanıza bağlamak için, Rigado ile iletişim kurmanız ve yukarıdaki adımlardan uygulama bağlantı ayrıntılarını sağlamanız gerekir. 

Cihaz internet 'e bağlandıktan sonra, Rigado, güvenli bir kanal aracılığıyla bir yapılandırma güncelleştirmesini basamaklı 500 ağ geçidi cihazına doğru bir şekilde gönderebilecektir. 

Bu güncelleştirme, IoT Central bağlantı ayrıntılarını Cascade 500 cihazına uygular ve aygıtlar listenizde görünür. 

![Cihaz listesi](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Artık IoT Central uygulamanızda C500 cihazınızı kullanmaya hazırsınız!

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bazı sonraki adımlar şunlardır:

- [Azure IoT Central cihaz bağlantısı](./concepts-get-connected.md) hakkında bilgi edinin
- [Azure CLI kullanarak cihaz bağlantısını izlemeyi](./howto-monitor-devices-azure-cli.md) öğrenin
