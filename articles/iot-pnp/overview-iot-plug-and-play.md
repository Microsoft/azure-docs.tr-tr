---
title: IoT Tak ve Kullan önizlemesine giriş | Microsoft Docs
description: IoT Tak ve Kullan önizlemesi hakkında bilgi edinin. IoT Tak ve Kullan, IoT cihazlarının yeteneklerini bildirmesine olanak sağlayan bir açık modelleme dilini temel alır. IoT cihazları, cihaz yetenek modeli olarak adlandırılan ve Azure IoT Central veya iş ortağı uygulamaları gibi bulut çözümlerine bağlandıklarında bu bildirimi sunar. Bulut çözümü daha sonra herhangi bir kod yazmadan cihazı otomatik olarak anlayabilir ve bununla etkileşime başlayabilir.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: fd1479a224255f74f4d47cfd90576a5afd25010f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935388"
---
# <a name="what-is-iot-plug-and-play-preview"></a>IoT Tak ve Kullan önizlemesi nedir?

IoT Tak ve Kullan önizlemesi, çözüm geliştiricilerinin, ekli kod yazmadan cihazları çözümlerle tümleştirmelerini sağlar. IoT Tak ve Kullan 'nin çekirdeği, cihaz yeteneklerini açıklayan bir _cihaz yetenek modeli_ şemadır. Bu şema, tanımları içeren bir arabirim kümesi olarak yapılandırılmış bir JSON belgesidir:

- Bir cihazın veya diğer varlıkların salt okunurdur ve okuma/yazma durumunu temsil eden _Özellikler_ . Örneğin, bir cihaz seri numarası salt okunurdur ve bir termostat üzerinde hedef sıcaklık bir okuma/yazma özelliği olabilir.
- Bir cihaz _tarafından yayılan,_ verilerin düzenli algılayıcı okumalar, zaman zaman bir hata veya bilgi iletisi olup olmadığı.
- Bir cihazda yapılabilen bir işlevi veya işlemi tanımlayan _Komutlar_ . Örneğin, bir komut bir ağ geçidini yeniden başlatır veya uzak bir kamerayı kullanarak bir resim alabilir.

İşbirliğini kolaylaştırmak ve geliştirmeyi hızlandırmak için cihaz yetenek modelleri genelinde arabirimleri yeniden kullanabilirsiniz.

IoT Tak ve Kullan [Azure dijital TWINS](../digital-twins/about-digital-twins.md)ile sorunsuz bir şekilde çalışmasını sağlamak Için, IoT Tak ve kullan şeması [dijital ikizi tanım dili (dtdl)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)kullanılarak tanımlanmıştır. IoT Tak ve Kullan ve DTDL, topluluk için açıktır ve Microsoft, müşteriler, iş ortakları ve sektörle işbirliği ile gelir. Her ikisi de JSON-LD ve RDF gibi açık W3C standartlarına dayalıdır ve bu da hizmetler ve araçlar arasında daha kolay benimseme olanağı sağlar. Ayrıca, IoT Tak ve Kullan ve DTDL kullanımı için ek bir maliyet yoktur. Azure [IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/core/overview-iot-central.md)ve diğer Azure hizmetleri için standart ücretler aynı kalır.

IoT Hub veya IoT Central oluşturulan çözümler, IoT Tak ve Kullan yararlanabilir.

Bu makalede şunları özetlenmektedir:

- IoT Tak ve Kullan kullanan bir projeyle ilişkili tipik roller.
- Uygulamanızda IoT Tak ve Kullan cihazları kullanma.
- IoT Tak ve Kullan destekleyen bir IoT cihaz uygulaması geliştirme.
- IoT Tak ve Kullan cihazını onaylama ve [IoT cihaz kataloğu Için sertifikalı](https://catalog.azureiotsolutions.com/)yayımlama.

## <a name="user-roles"></a>Kullanıcı rolleri

IoT Tak ve Kullan iki tür geliştirici için yararlıdır:

- _Çözüm geliştiricisi_ , Azure IoT ve diğer Azure kaynaklarını kullanarak bir IoT çözümü geliştirmekten ve IoT cihazlarının tümleştirileceği şekilde tanımlanmasından sorumludur.
- Bir _cihaz geliştiricisi_ , çözümünüze bağlı bir cihazda çalışan kodu oluşturur.

## <a name="use-iot-plug-and-play-devices"></a>IoT Tak ve Kullan cihazları kullanma

Çözüm geliştiricisi olarak IoT Tak ve Kullan cihazları kullanan, bulutta barındırılan bir IoT çözümü geliştirebilirsiniz. Aşağıdaki Azure hizmetlerinden birini kullanabilirsiniz:

- [IoT Central](../iot-central/core/overview-iot-central.md) -fiziksel ve dijital çalışma LDS 'yi bağlayan ürünler oluşturmayı kolaylaştıran, tam olarak yönetilen bir hizmet olarak yazılım çözümü.
- [IoT Hub](../iot-hub/about-iot-hub.md) , IoT uygulamanız ve cihazlarınız arasında güvenli, çift yönlü iletişim için ileti hub 'ı görevi gören yönetilen bir bulut hizmetidir.

IoT Tak ve Kullan cihazlarını IoT için Azure Sertifikalı cihaz Kataloğu aracılığıyla bulabilirsiniz. Katalogdaki her bir IoT Tak ve Kullan aygıtı doğrulandıktan sonra bir cihaz yetenek modeli vardır. Cihazın işlevselliğini anlamak veya cihazın Azure IoT Central benzetimini yapmak için onu kullanmak üzere cihaz yetenek modelini görüntüleyin.

IoT Tak ve Kullan cihazını bağladığınızda, cihaz yetenek modelini, modele dahil olan arabirimleri ve bu arabirimlerde tanımlanan telemetri, Özellikler ve komutları görüntüleyebilirsiniz.

## <a name="develop-an-iot-device-application"></a>IoT cihaz uygulaması geliştirme

Bir cihaz geliştiricisi olarak IoT Tak ve Kullan destekleyen bir IoT donanım ürünü geliştirebilirsiniz. İşlem iki temel adımdan oluşur:

1. Cihaz yetenek modeli ve arabirimlerini tanımlayın. [Dtdl](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)kullanarak cihazınızın yeteneklerini BILDIREN bir JSON dosyaları kümesi yazar. Bir cihaz yetenek modeli, fiziksel ürün gibi bir bütün varlığı tanımlar ve bu varlık tarafından uygulanan arabirimlerin kümesini tanımlar. Arabirimler, bir cihaz tarafından desteklenen telemetri, özellik ve komutları benzersiz bir şekilde tanımlayan paylaşılan sözleşmelerdir. Arabirimler farklı cihaz yeteneği modelleri arasında yeniden kullanılabilir.

1. Cihaz yeteneği modelinde ve arabirimlerinde belirtilen özellikleri uygulayan cihaz yazılımını veya üretici yazılımını yazar. Azure IoT SDK 'Sı, cihaz yetenek modellerini uygulayan API 'Leri içerir.

VS Code Uzantı paketi [Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , size yardımcı olacak birçok özellik sağlar. Örneğin, bir cihaz geliştiricisi olarak bir yetenek modelinden iskelet C projesi oluşturmak için bir uzantı kullanabilirsiniz. Ancak, cihaz yetenek modellerini yazmak ve uygulamak için herhangi bir IDE kullanabilirsiniz.

## <a name="certify-an-iot-plug-and-play-device"></a>IoT Tak ve Kullan cihazını onaylama

Bir cihaz geliştiricisi olarak, sertifika için IoT donanım ürünleri gönderebilirsiniz. Sertifikalı bir cihazı, IoT için sertifikalı cihaz kataloğunda yayımlayabilirsiniz. Sertifika işlemi adımları şunları içerir:

- [Microsoft iş ortağı ağı](https://partner.microsoft.com)birleştirin.
- Azure IoT portalına sertifikalıdır.
- Yeni bir cihaz kaydı oluşturmak için IoT Tak ve Kullan cihaz yetenek modeli ve pazarlama bilgilerini gönderebilirsiniz.
- Cihaz için otomatik doğrulama testleri kümesi geçirin.
- IoT cihaz kataloğunda sertifikalı yayımlayın.

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Genel Önizleme sırasında IoT Tak ve Kullan Kuzey Avrupa, Orta ABD ve Japonya Doğu bölgelerinde kullanılabilir. Lütfen hub 'ınızı bu bölgelerden birinde oluşturduğunuzdan emin olun.

## <a name="message-quotas-in-iot-hub"></a>IoT Hub 'deki ileti kotaları
Genel Önizleme sırasında, IoT Tak ve Kullan cihazları Arabirim başına ayrı mesajlar gönderir ve bu da [ileti kotasından](../iot-hub/iot-hub-devguide-quotas-throttling.md)doğru olarak sayılan ileti sayısını artırabilir.

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan 'ye genel bir bakış edinolduğunuza göre, önerilen sonraki adım hızlı başlangıçlardan birini denemektedir:

- [IoT Tak ve Kullan cihazı oluşturmak için cihaz yetenek modeli kullanma](./quickstart-create-pnp-device.md)
- [Cihazı IoT Hub bağlama](./quickstart-connect-pnp-device.md)
- [Çözümünüzde bir cihaza bağlanma](./quickstart-connect-pnp-device-solution.md)
