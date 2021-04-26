---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358293"
---
1. Visual Studio Code, src/Edge öğesine gidin. Oluşturduğunuz. env dosyasını, birkaç dağıtım şablonu dosyası ile birlikte görürsünüz.

    Dağıtım şablonu, kenar cihazının bazı yer tutucu değerleriyle birlikte dağıtım bildirimini ifade eder. . Env dosyası bu değişkenlerin değerlerini içerir.
1. Ardından src/buluttan cihaza-Console-App klasörüne gidin. Burada, oluşturduğunuz dosyada, diğer birkaç dosyayla birlikte appsettings.jsgörürsünüz:

   * operations.json-bu dosya, programın çalıştırmasını istediğiniz farklı işlemleri listeler.
   * main.py-Bu örnek program kodudur ve şunları yapar:
    
        * Uygulama ayarlarını yükler.
        * Topoloji oluşturmak, grafiği örneklemek ve grafiği etkinleştirmek için IoT Edge modülünün doğrudan yöntemlerinde canlı video analizlerini çağırır.
        * , **TERMINAL** penceresinde grafik çıkışını ve **Çıkış** penceresinde IoT Hub 'ına gönderilen olayları incelemenizi sağlar.
        * Grafik örneğini devre dışı bırakır, grafik örneğini siler ve grafik topolojisini siler.
