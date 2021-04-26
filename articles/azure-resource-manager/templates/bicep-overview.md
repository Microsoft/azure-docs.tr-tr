---
title: Azure Resource Manager şablonları için bıcep dili
description: Azure Resource Manager şablonları aracılığıyla Azure 'a altyapı dağıtmak için Bıcep dilini açıklar.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773756"
---
# <a name="what-is-bicep-preview"></a>Bicep (Önizleme) nedir?

Bıcep, Azure kaynaklarını bildirimli olarak dağıtmaya yönelik bir dildir. Azure Resource Manager şablonlarınızı geliştirmek için JSON yerine Bıcep kullanabilirsiniz (ARM şablonları). Bıcep, daha kısa bir sözdizimi sağlayarak kod yeniden kullanımı için daha iyi destek ve geliştirilmiş tür güvenliği sunarak yazma deneyimini basitleştirir. Bıcep, etki alanına özgü bir dildir (DSL), bu, belirli bir senaryo veya etki alanı için tasarlanmasıdır. Uygulama yazmak için genel programlama dili olarak tasarlanmamıştır.

Şablon oluşturmak için JSON sözdizimi ayrıntılı olabilir ve karmaşık ifade gerektirebilir. Bıcep, bir JSON şablonunun özelliklerini kaybetmeden bu deneyimi geliştirir. ARM şablonları için JSON üzerinde saydam bir soyutlamadır. Her bicep dosyası standart bir ARM şablonuna derlenir. Bir ARM şablonunda geçerli olan kaynak türleri, API sürümleri ve özellikler bir Bıcep dosyasında geçerlidir. Geçerli sürümde bazı [bilinen sınırlamalar](#known-limitations) vardır.

Bıcep Şu anda önizleme aşamasındadır. Çalışmanın durumunu izlemek için, [Bıcep proje deposuna](https://github.com/Azure/bicep)bakın.

Bıcep hakkında bilgi edinmek için aşağıdaki videoya bakın.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>başlarken

Bıcep 'yi kullanmaya başlamak için [araçları yüklemelisiniz](bicep-install.md).

Araçları yükledikten sonra [Bıcep öğreticisini](./bicep-tutorial-create-first-bicep.md)deneyin. Öğretici serisi, Bıcep 'nin yapısı ve özellikleri boyunca size yol gösterir. Bıcep dosyalarını dağıtır ve bir ARM şablonunu eşdeğer Bıcep dosyasına dönüştürebilirsiniz.

Eşdeğer JSON ve Bıcep dosyalarını yan yana görüntülemek için, [Bıcep PLAYIN](https://aka.ms/bicepdemo)bölümüne bakın.

Bıcep 'ye dönüştürmek istediğiniz mevcut bir ARM şablonunuz varsa, bkz. [JSON ve bıcep arasında ARM şablonlarını dönüştürme](bicep-decompile.md).

## <a name="benefits-of-bicep-versus-other-tools"></a>Bicep ve diğer araçların avantajları

Bıcep diğer seçeneklere aşağıdaki avantajları sağlar:

* **Tüm kaynak türleri ve API sürümleri Için destek**: bıcep, Azure hizmetleri için tüm ÖNIZLEME ve GA sürümlerini hemen destekler. Kaynak sağlayıcısı yeni kaynak türleri ve API sürümleri sunarak, bunları Bıcep dosyanızda kullanabilirsiniz. Yeni hizmetleri kullanmadan önce araçların güncelleştirilmesini beklemeniz gerekmez.
* **Yazma deneyimi**: Bıcep dosyalarınızı oluşturmak için vs Code kullandığınızda, birinci sınıf bir yazma deneyimi alırsınız. Düzenleyici zengin tür güvenliği, IntelliSense ve sözdizimi doğrulaması sağlar.
* **Modülerlik**: [modülleri](bicep-modules.md)kullanarak bıcep kodunuzu yönetilebilir parçalara kesebilirsiniz. Modül bir dizi ilgili kaynağı dağıtır. Modüller kodu yeniden kullanmanıza ve geliştirmeyi basitleştirmenize imkan tanır. Bu kaynakları dağıtmanız gereken her zaman modülü bir Bıcep dosyasına ekleyin.
* **Azure hizmetleriyle tümleştirme**: bıcep, Azure ilkesi, şablon özellikleri ve planlar gibi Azure hizmetleriyle tümleşiktir.
* **Yönetilecek durum veya durum dosyası yok**: tüm durum Azure 'da depolanır. Kullanıcılar işbirliği yapabilir ve güncelleştirmelerin gerekli olduğu şekilde işlenmesini sağlayabilir. Şablonunuz dağıtılmadan önce değişiklikleri önizlemek için [ne yapılır işlemini](template-deploy-what-if.md) kullanın.
* **Maliyet ve açık kaynak yok**: bıcep tamamen ücretsizdir. Premium yetenekler için ödeme yapmak zorunda değilsiniz. Microsoft desteği tarafından da desteklenir.

## <a name="bicep-improvements"></a>Bıcep geliştirmeleri

Bıcep, denk JSON ile karşılaştırıldığında daha kolay ve daha kısa bir sözdizimi sunmaktadır. `[...]`İfadeler kullanmayın. Bunun yerine, doğrudan işlevleri çağırır ve parametrelerden ve değişkenlerden değerler alırsınız. Dağıtılan her kaynağa bir sembolik ad verirsiniz ve bu, şablonunuzda bu kaynağa başvurmayı kolaylaştırır.

Örneğin, aşağıdaki JSON bir kaynak özelliğinden çıkış değeri döndürüyor.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bıcep içindeki eşdeğer çıkış ifadesinin yazılması daha kolay. Aşağıdaki örnek, şablon içinde tanımlı bir kaynak için simgesel ad olan **Publicıp** adı kullanılarak aynı özelliği döndürür:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Sözdiziminin tam karşılaştırması için bkz. [Şablonlar IÇIN JSON ve Bıcep karşılaştırması](compare-template-syntax.md).

Bıcep, kaynaklar arasındaki bağımlılıkları otomatik olarak yönetir. Bir `dependsOn` kaynağın sembolik adı başka bir kaynak bildiriminde kullanıldığında ayarından kaçınabilirsiniz.

Bicep dosyasının yapısı, JSON şablonundan daha esnektir. Dosya içinde herhangi bir yerde parametreler, değişkenler ve çıktılar bildirebilirsiniz. JSON 'da, şablonun ilgili bölümlerinin içindeki tüm parametreleri, değişkenleri ve çıkışları bildirmeniz gerekir.

## <a name="known-limitations"></a>Bilinen sınırlamalar

Şu an şu sınırlar mevcuttur:

* Kopyalama döngüleri üzerinde mod veya toplu iş boyutu ayarlanamaz.
* Döngüleri ve koşulları birleştiremez.
* Gibi tek satırlı nesne ve diziler `['a', 'b', 'c']` desteklenmez.

## <a name="faq"></a>SSS

**Var olan bir dili kullanmak yerine neden yeni bir dil oluşturulsun?**

Bıcep 'yi yeni bir dil yerine var olan ARM şablon diline bir düzeltme olarak düşünebilirsiniz. Sözdizimi değişti, ancak çekirdek işlev ve çalışma zamanı aynı kalır.

Bıcep 'yi geliştirmeye başlamadan önce, var olan bir programlama dilini kullanmayı kabul ediyoruz. Hedef izleyicilerimize, başka bir dille çalışmaya başlamak yerine Bıcep 'nin öğrenmekte olduğunu karartık.

**Önerinizi Terkform veya diğer üçüncü taraf altyapısına kod teklifleri olarak odaklanmadık?**

Farklı kullanıcılar farklı yapılandırma dilleri ve araçları tercih eder. Bu araçların tümünün Azure 'da harika bir deneyim sunduğunuzdan emin olmak istiyoruz. Bıcep, bu çabanın bir parçasıdır.

Terrayform kullanıyorsanız, geçiş yapmak için bir neden yoktur. Microsoft, Azure 'da Tersform 'un en iyi şekilde suntığlarından emin olmanızı taahhüt etmektedir.

ARM şablonlarını seçen müşteriler için Bıcep 'nin yazma deneyimini geliştirdik. Bıcep, kod olarak altyapı benimseyen müşterilerin geçişine de yardımcı olur.

**Bıcep yalnızca Azure için mi?**

Bıcep, tam çözümlerin Azure 'a dağıtılmasıyla ilgili bir DSL. Hedef toplantı, Azure dışındaki bazı API 'lerle çalışmayı gerektirir. Bu senaryolar için genişletilebilirlik noktaları sağlamamız bekleniyor.

**Var olan ARM Şablonlarıma ne olur?**

Her zaman sahip oldukları için tamamen çalışmaya devam ederler. Herhangi bir değişiklik yapmanız gerekmez. Temel alınan ARM şablonu JSON dilini desteklemeye devam edeceğiz. Bıcep dosyaları JSON 'a derlenir ve bu JSON dağıtım için Azure 'a gönderilir.

Hazırsanız, [JSON dosyalarını Bıcep 'ye dönüştürebilirsiniz](bicep-decompile.md).

## <a name="next-steps"></a>Sonraki adımlar

[Bıcep öğreticisi](./bicep-tutorial-create-first-bicep.md)ile çalışmaya başlayın.
