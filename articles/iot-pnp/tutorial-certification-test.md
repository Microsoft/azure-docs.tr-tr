---
title: IoT Tak ve Kullan önizleme cihazınızı onaylama | Microsoft Docs
description: Bu öğretici, Azure Sertifikalı IoT cihaz kataloğuna ürün bilgilerinizin nasıl ekleneceğini, cihazınızı Azure IoT sertifika hizmetine bağlamayı ve sonra IoT Tak ve Kullan sertifika testlerini çalıştırmayı açıklar.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 06/21/2019
ms.openlocfilehash: 1516a132372a81d06d82de2409c48220f27b8d87
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878085"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Öğretici: IoT Tak ve Kullan önizleme cihazınızı onaylama

IoT [Cihaz Kataloğu Için Azure Sertifikalı](https://aka.ms/iotdevcat)bir IoT Tak ve kullan önizleme cihazı yayımlamak için, bir sertifika testleri kümesi geçmelidir. Cihazınızı sertifika için göndermek üzere [IoT Için Azure sertifikası](https://aka.ms/ACFI) 'nı kullanın. [Azure IoT sertifika hizmeti](https://aka.ms/azure-iot-aics) , sertifika testlerini çalıştırır.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * IoT Tak ve Kullan sertifika gereksinimleri nelerdir?
> * Ürün adınızı ve bilgilerinizi portala ekleme.
> * IoT Tak ve Kullan arabirimlerini bağlama ve bulma.
> * IoT Tak ve Kullan arabirimlerini gözden geçirin ve sertifika testlerini çalıştırın.
> * Sertifikalı IoT Tak ve Kullan cihazını katalogda yayımlama.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Bir Microsoft Iş Ortağı Merkezi hesabı.
* Microsoft İş Ortağı Ağı KIMLIĞI.

Daha fazla bilgi için bkz. [IoT Için Azure Sertifikalı](howto-onboard-portal.md) ekleme portalı 'na ekleme.

## <a name="certification-requirements"></a>Sertifika gereksinimleri

IoT Tak ve Kullan cihazınızı onaylamak için cihazınızın aşağıdaki gereksinimleri karşılaması gerekir:

* IoT Tak ve Kullan cihaz kodunuzun cihazınızda yüklü olması gerekir.
* IoT Tak ve Kullan cihaz kodunuz Azure IoT SDK ile oluşturulmuştur.
* Cihaz kodunuzun [Azure IoT Hub cihaz sağlama hizmetini](../iot-dps/about-iot-dps.md)desteklemesi gerekir.
* Cihaz kodunuzun [cihaz bilgileri arabirimini](concepts-common-interfaces.md)uygulaması gerekir.
* Yetenek modeli ve cihaz kodu IoT Central birlikte çalışır.

Şu anda katalogdaki tüm cihazların ön sertifikalı IoT Tak ve Kullan cihazları olduğu kabul edilir. SDK ve Digital Ikizi tanım dili gibi IoT Tak ve Kullan yazılım bileşenlerinin son ürününün kalitesini ve uyumluluğunu garanti etmez.

Önceden sertifikalı IoT Tak ve Kullan cihazlarının tümü, Microsoft tarafından sunulan sertifika gereksinimlerinin ve yazılım bileşenlerinin son sürümüne bağlı olarak IoT Tak ve Kullan genel kullanıma sunulmasıyla emin olmalıdır.

## <a name="add-product-name"></a>Ürün adı Ekle

**Ürün** , bir müşterinin satın alabileceğiniz bir ürün modeli için kolay bir addır. Bir ürün eklemek için:

1. [IoT portalında Azure sertifikası](https://aka.ms/ACFI) ' nda oturum açın.
1. Sol menüden Portal ' daki **Ürünler** sayfasına gidin ve **+ Yeni**' yi seçin.
1. Kolay ürün adınızı girip **Oluştur**' u seçin. Buraya girilen ad, cihaz kataloğunda görüntülenden farklı.

## <a name="add-product-information"></a>Ürün bilgileri ekleme

Ürünü portalda başarıyla oluşturduktan sonra **Ürünler** sayfasında görüntülenir. Ürün bilgilerini eklemek için:

1. Ürün sütununda **ürün** sayfasında bulunan oluşturulan ürün bağlantısını seçin. Durum taslak durumunda olmalıdır.
1. **Ürün bilgileri** başlığının yanındaki **Düzenle** bağlantısını seçin. Ürün bilgileri sayfasında ürününüzle ilgili bilgileri girin ve gerekli tüm alanları tamamladığınızdan emin olun.
1. **Kaydet**’i seçin. **Kaydet** düğmesi yalnızca gerekli tüm alanları tamamladığınızda görüntülenir. Alanlar tamamlanmamışsa, düğme **Kaydet ve daha sonra sona kaydet**' i belirtir.
1. Girdiğiniz içeriği gözden geçirin. Cihazı cihaz kataloğunda yayımlamak için gerekli tüm alanları doldurun. Ürün **bilgileri** başlığının yanındaki **Düzenle** bağlantısına tıklayarak Ürün Ayrıntısı ' nda ürün bilgilerinde düzenleme yapma sayfasına her zaman geri gidebilirsiniz.

## <a name="connect-and-discover-interfaces"></a>Arabirimleri bağlama ve bulma

Sertifika testlerini çalıştırmak için, cihazınızı portalda bulunan [Azure IoT sertifika hizmeti](https://aka.ms/azure-iot-aics) 'ne (aics) bağlayın.

Bu adımlar, sertifika testlerini çalıştırmaya yönelik bir zaman adımıdır ve ürün cihazı kodunuzu değiştirmek gerekli değildir. Başlamak için, AıCS 'ye bağlanmak üzere aşağıdaki adımları izleyin:

1. Iş Ortağı Merkezi hesabınızı kullanarak portalda oturum açın.
1. Sertifika akışını başlatmak için **Bağlan + test** ' e tıklayın.
1. [Azure IoT Hub cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md)'ni kullanarak cihazınızı AICs 'ye sağlamak için [kimlik doğrulama yöntemini](../iot-dps/concepts-security.md#attestation-mechanism) seçin.
   * [X. 509.952 sertifikası](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)kullanıyorsanız, oluşturulan x. 509.440 sertifikanızı karşıya yükleyin. X. 509.440 sertifikalarının nasıl kullanılacağını gösteren örnek kodu gözden geçirmek isteyebilirsiniz: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * [Simetrik anahtar](../iot-dps/concepts-symmetric-key-attestation.md)kullanıyorsanız, simetrik anahtarı kopyalayıp cihaz kodunuza yapıştırın.
   * TPM kimlik doğrulama yöntemi şu anda desteklenmiyor.
1. Aşağıdaki oluşturulan kimlikleri kopyalayıp cihaz kodunuza yapıştırın.
   * [Kayıt KIMLIĞI](../iot-dps/use-hsm-with-sdk.md)
   * [DPS KIMLIĞI](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS uç noktası](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Cihaz kodunuz oluşturulup cihaza dağıtıldığında, IoT Tak ve Kullan arabirimlerini saptamak için **Bağlan** ' ı seçin.
1. AICS bağlantısı başarılı olduğunda, gözden geçirmek için **İleri** ' yi seçin IoT Tak ve kullan arabirimlerini tespit edin.

## <a name="run-tests-and-publish-the-device"></a>Testleri çalıştırma ve cihazı yayımlama

İnceleme sayfasında, bulunan IoT Tak ve Kullan arabirimlerini gözden geçirebilirsiniz. Bu sayfayı, arabirimde uygulanan temel elemanların doğru şekilde görüntülenmesini denetlemek için kullanın. Ayrıca, arabirimin konumunu da denetleyebilirsiniz.

1. Gerekli alanlar için yük girişlerinin girildiğinden emin olun. Bu alanlar, belirtilen arabirim için komut ilkel öğesi için yük bilgilerini içerir.
1. Gerekli tüm bilgileri girdikten **sonra ileri**' yi seçin.
1. Uygulanan IoT Tak ve Kullan arabirimleri için testleri çalıştırmak için **Testleri Çalıştır**' ı seçin.
1. Tüm testler otomatik olarak çalışır. Herhangi bir test başarısız olursa, AICS hata iletilerini ve Azure IoT Hub gönderilen ham telemetrisini görüntülemek için **günlükleri görüntüle** ' yi seçin.
1. Sertifika testlerini tamamlayabilmeniz için **son**' u seçin.
1. Sertifikalı IoT Tak ve Kullan cihazını katalogda yayımlayın. Sertifikalı cihazı kataloğa eklemek için araç çubuğunda **kataloğa Ekle** ' yi seçin. **Katalog ekleme** işlemi gri ise, ürün bilgileri eksik veya testlerin başarısız olduğu anlamına gelir. 
1. Yayınlanan cihazınızı cihaz kataloğunda görüntülemek için "SERTIFIKALı ve Katalog IÇINDE" bağlantısını seçin.

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan cihazının sertifika hakkında bilgi edindiğinize göre, önerilen sonraki adım yetenek modellerini yönetme hakkında daha fazla bilgi edinmektir:

> [!div class="nextstepaction"]
> [Modelleri yönetme](./howto-manage-models.md)
