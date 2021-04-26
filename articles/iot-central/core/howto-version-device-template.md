---
title: Azure IoT Central uygulamalarınız için cihaz şablonu sürüm oluşturmayı anlama | Microsoft Docs
description: Yeni sürümler oluşturarak ve canlı bağlı cihazlarınızı etkilemeden cihaz şablonlarınızda yineleme yapın
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 03d4b0e43c9f692b90f4ab5d4d136dac92b74de6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715055"
---
# <a name="create-a-new-device-template-version"></a>Yeni bir cihaz şablonu sürümü oluştur

*Bu makale, çözüm oluşturucular ve cihaz geliştiricileri için geçerlidir.*

Bir cihaz şablonu, bir cihazın IoT Central nasıl etkileşime gireceğini tanımlayan bir şema içerir. Bu etkileşimler telemetri, Özellikler ve komutları içerir. Hem cihaz hem de IoT Central uygulaması, bilgi alışverişi için bu şemanın paylaşılan olarak anlaşılmasını kullanır. Şemayı bozmadan yalnızca şemada sınırlı değişiklikler yapabilirsiniz. Bu, en çok şema değişikliklerinin cihaz şablonunun yeni bir sürümünü gerektirmesinin nedeni budur. Cihaz şablonunun sürümü, daha eski cihazların anladıkları şema sürümü ile devam etmesine izin verir, daha yeni veya güncelleştirilmiş cihazlar daha sonraki bir şema sürümünü kullanır.

Bir cihaz şablonundaki şema, cihaz modelinde ve arabirimlerinde tanımlanmıştır. Cihaz şablonları, bulut özellikleri, görüntüleme özelleştirmeleri ve görünümleri gibi diğer bilgileri içerir. Cihaz şablonunun bu bölümlerinde, cihazın IoT Central verileri nasıl değiş tokuşunu tanımlamayan bir değişiklik yaparsanız, şablonun sürümünü yüklemeniz gerekmez.

Bir işlecin kullanabilmesi için önce güncelleştirilmiş bir cihaz şablonunu her zaman yayımlamanız gerekir. IoT Central, önce şablon sürümü oluşturmadan bir cihaz şablonuna yönelik son değişiklikleri yayımlamanızı engeller.

> [!NOTE]
> Cihaz şablonu oluşturma hakkında daha fazla bilgi edinmek için bkz. [cihaz şablonu ayarlama ve yönetme](howto-set-up-template.md)

## <a name="versioning-rules"></a>Sürüm oluşturma kuralları

Bu bölüm, cihaz şablonları için uygulanan sürüm oluşturma kurallarını özetler. Hem cihaz modellerinin hem de arabirimlerin sürüm numaraları vardır. Aşağıdaki kod parçacığında, bir termostat cihazının cihaz modeli gösterilmektedir. Cihaz modelinde tek bir arabirim vardır. Alanın sonunda sürüm numarasını görebilirsiniz `@id` .

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Bu bilgileri IoT Central Kullanıcı arabiriminde görüntülemek için cihaz şablonu düzenleyicisinde **kimliği görüntüle** ' yi seçin:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Sürüm numarasını görmek için bir arabirimin kimliğini görüntüleme":::

Aşağıdaki listede, ne zaman yeni bir sürüm oluşturmanız gerektiğine ilişkin kurallar gösterilmektedir:

* Bir cihaz modeli yayımlandıktan sonra, cihaz şablonunun yeni bir sürümünde bile tüm arabirimleri kaldıramazsınız.
* Bir cihaz modeli yayımlandıktan sonra, cihaz şablonunun yeni bir sürümünü oluşturursanız bir arabirim ekleyebilirsiniz.
* Bir cihaz modeli yayımlandıktan sonra, cihaz şablonunun yeni bir sürümünü oluşturursanız bir arabirimi daha yeni bir sürümle değiştirebilirsiniz. Örneğin, algılayıcı v1 cihaz şablonu termostat v1 arabirimini kullanıyorsa, termostat v2 arabirimini kullanan bir algılayıcı v2 cihaz şablonu oluşturabilirsiniz.
* Bir arabirim yayımlandıktan sonra, yeni bir cihaz şablonu sürümünde bile arabirim içeriklerinin hiçbirini kaldıramazsınız.
* Arabirim yayımlandıktan sonra, arabirim ve cihaz şablonunun yeni bir sürümünü oluşturursanız bir arabirimin içeriğine öğeler ekleyebilirsiniz. Arabirime ekleyebileceğiniz öğeler telemetri, Özellikler ve komutları içerir.
* Bir arabirim yayımlandıktan sonra, arabirimin ve cihaz şablonunun yeni bir sürümünü oluşturursanız, arabirimdeki varolan öğelerde şema dışı değişiklikler yapabilirsiniz. Bir arabirim öğesinin şema dışı kısımları, görünen adı ve anlam türünü içerir. Değiştiremiyoruz bir arabirim öğesinin şema bölümleri ad, yetenek türü ve şemadır.

Aşağıdaki bölümlerde, IoT Central cihaz şablonlarının değiştirilmesine ilişkin bazı örnekler gösterilmektedir.

## <a name="customize-the-device-template-without-versioning"></a>Cihaz şablonunu sürüm oluşturma olmadan özelleştirme

Cihaz olanaklarınızın belirli öğeleri, cihaz şablonu ve arabirimlerinizin sürümü gerekmeden düzenlenebilir. Örneğin, bu alanlardan bazıları görünen adı, anlam türünü, minimum değeri, en büyük değer, ondalık basamak, renk, birim, görüntü birimi, açıklama ve açıklama içerir. Şu özelleştirmelere birini eklemek için:

1. **Cihaz şablonları** sayfasına gidin.
1. Özelleştirmek istediğiniz cihaz şablonunu seçin.
1. **Özelleştir** sekmesini seçin.
1. Cihaz modelinizde tanımlanan tüm yetenekler burada listelenmiştir. Cihaz şablonunuzu sürüm gerekmeden bu alanları düzenleyebilir, kaydedebilir ve kullanabilirsiniz. Salt okunurdur düzenlemek istediğiniz alanlar varsa, bunları değiştirmek için cihaz şablonunuzu sürümüne uygulamanız gerekir. Düzenlemek istediğiniz bir alanı seçin ve yeni değerleri girin.
1. **Kaydet**’i seçin. Artık bu değerler, ilk olarak cihaz şablonunuzda kaydedilen ve uygulama genelinde kullanılan her şeyi geçersiz kılar.

## <a name="version-a-device-template"></a>Bir cihaz şablonu sürümü

Cihaz şablonunuzun yeni bir sürümünü oluşturmak, cihaz modelinin düzenlenebildiği şablonun taslak bir sürümünü oluşturur. Yayımlanan tüm arabirimler, tek tek sürümleyene kadar yayımlanmaya devam eder. Yayınlanmış bir arabirimi değiştirmek için önce yeni bir cihaz şablonu sürümü oluşturun.

Yalnızca, cihaz modelinin özelleştirmeler bölümünde düzenleyebileceğiniz bir parçasını düzenlemeye çalışırken cihaz şablonunu sürüm olarak değiştirin.

Bir cihaz şablonunu sürüm için:

1. **Cihaz şablonları** sayfasına gidin.
1. Sürüm oluşturmaya çalıştığınız cihaz şablonunu seçin.
1. Sayfanın üst kısmındaki **Sürüm** düğmesini seçin ve şablona yeni bir ad verin. IoT Central, düzenleyebilmeniz için yeni bir ad önerir.
1. **Oluştur**’u seçin.
1. Artık cihaz şablonunuz taslak modunda. Arabirimlerinizin hala kilitli olduğunu görebilirsiniz. Değiştirmek istediğiniz arabirimlerin herhangi bir sürümünü kullanabilirsiniz.

## <a name="version-an-interface"></a>Bir arabirim sürümü

Bir arabirimin sürümü oluşturma, daha önce oluşturmuş olduğunuz arabirim içinde özellik eklemenize ve güncelleştirmenize olanak tanır.

Bir arabirimi sürüm için:

1. **Cihaz şablonları** sayfasına gidin.
1. Taslak modunda olan cihaz şablonunu seçin.
1. Sürüm ve düzenleme yapmak istediğiniz yayımlanmış modda olan arabirimi seçin.
1. Arabirim sayfasının en üstündeki **Sürüm** düğmesini seçin.
1. **Oluştur**’u seçin.
1. Artık arabiriminiz taslak modunda. Mevcut özelleştirmeleri ve görünümleri bozmadan arabiriminize özellikler ekleyebilir veya düzenleyebilirsiniz.

## <a name="migrate-a-device-across-versions"></a>Bir cihazı sürümler arasında geçirme

Cihaz şablonunun birden çok sürümünü oluşturabilirsiniz. Zaman içinde, bu cihaz şablonlarını kullanan birden fazla bağlı cihazınız olacaktır. Cihazları, cihaz şablonunuzun bir sürümünden başka bir sürümüne geçirebilirsiniz. Aşağıdaki adımlarda bir cihazın nasıl geçirileceği açıklanır:

1. **Cihazlar** sayfasına gidin.
1. Başka bir sürüme geçirmeniz gereken cihazı seçin.
1. **Geçiş** seçin: :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="bir cihazı geçirmeye başlama seçeneğini belirleyin":::
1. Cihazı geçirmek istediğiniz sürüm numarasına sahip cihaz şablonunu seçin ve **geçir**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Operatör veya çözüm Oluşturucu kullanıyorsanız, [cihazlarınızın nasıl yönetileceğini](./howto-manage-devices.md)öğrenmek için bir sonraki adım önerilir.

Bir cihaz geliştiricisiyseniz, önerilen bir sonraki adım [Azure IoT Edge cihazları ve Azure IoT Central](./concepts-iot-edge.md)hakkında bilgi almanız gerekir.
