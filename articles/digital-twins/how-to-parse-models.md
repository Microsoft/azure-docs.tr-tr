---
title: Modelleri ayrıştırma ve doğrulama
titleSuffix: Azure Digital Twins
description: DTDL modellerini ayrıştırmak için ayrıştırıcı kitaplığını nasıl kullanacağınızı öğrenin.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 155566a125485fda326f9f5e02d4aead0ffe30e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560748"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>DTDL ayrıştırıcısı kitaplığıyla modelleri ayrıştırma ve doğrulama

Azure dijital TWINS 'deki [modeller](concepts-models.md) JSON-ld tabanlı dijital TWINS tanım DILI (dtdl) kullanılarak tanımlanır. **Azure dijital TWINS örneğinizi karşıya yüklemeden önce modellerinizi çevrimdışı doğrulamanız önerilir.**

Bunu yapmanıza yardımcı olmak için NuGet: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)üzerinde bir .NET istemci tarafı dtdl ayrıştırma kitaplığı sağlanır. 

Ayrıştırıcı kitaplığını doğrudan C# kodunuzda kullanabilir veya ayrıştırıcı kitaplığı: [**Dtdl Doğrulayıcı örneği**](/samples/azure-samples/dtdl-validator/dtdl-validator)üzerinde oluşturulmuş dilden bağımsız kod örnek projesini kullanabilirsiniz.

## <a name="use-the-dtdl-validator-sample"></a>DTDL doğrulayıcısı örneğini kullanma

[**Dtdl doğrulayıcısı**](/samples/azure-samples/dtdl-validator/dtdl-validator) , dtdl 'nin geçerli olduğundan emin olmak için model belgelerini doğrulayabilecek örnek bir projem. .NET Parser kitaplığı üzerine kurulmuştur ve dilden bağımsız olur. Örnek bağlantıda *ZIP 'ı indir* düğmesi ile edinebilirsiniz.

Kaynak kodda, ayrıştırıcı kitaplığının nasıl kullanılacağına ilişkin örnekler gösterilmektedir. Bir DTDL dosyaları dizin ağacını doğrulamak için, komut satırı yardımcı programı olarak Doğrulayıcı örneğini kullanabilirsiniz. Ayrıca etkileşimli bir mod sağlar.

DTDL doğrulayıcısı örneğinin klasöründe, örneği otomatik olarak içerilen bir yürütülebilire paketleme hakkında yönergeler için *README.MD* dosyasına bakın.

Kendi kendine içerilen bir paket oluşturup, bu dosyayı yolunuza ekledikten sonra, bu komutla olan doğrulayıcısı makinenizde bir konsolda çalıştırabilirsiniz:

```cmd/sh
DTDLValidator
```

Varsayılan seçeneklerle örnek, `*.json` geçerli dizinde ve tüm alt dizinlerde bulunan dosyaları arar. Ayrıca, belirtilen dizinde ve tüm alt dizinlerde *. dtdl* uzantılı örnek aramaya sahip olmak için aşağıdaki seçeneği de ekleyebilirsiniz:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

`-i`Etkileşimli moda girmek için örneğin seçeneğini ekleyebilirsiniz:

```cmd/sh
DTDLValidator -i
```

Bu örnek hakkında daha fazla bilgi için bkz. kaynak kodu veya çalıştırma `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>.NET Parser kitaplığını kullanma 

[**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) kitaplığı, dtdl tanımlarına, temel olarak Dtdl Için C# Reflection 'ın eşdeğeri olarak hareket eden model erişimi sağlar. Bu kitaplık, özellikle bir görsel veya metin düzenleyicisinde DTDL doğrulaması için herhangi bir [Azure dijital TWINS SDK 'sının](how-to-use-apis-sdks.md)bağımsız olarak kullanılabilir. Bunları hizmete yüklemeye çalışmadan önce model tanım dosyalarınızın geçerli olduğundan emin olmak için yararlıdır.

Ayrıştırıcı kitaplığını kullanmak için, bunu bir DTDL belgeleri kümesiyle birlikte sağlarsınız. Genellikle, bu model belgelerini hizmetten alırsınız, ancak istemciniz ilk yerde hizmete yüklenmeden sorumlu ise yerel olarak da kullanılabilir. 

Ayrıştırıcının kullanımı için genel iş akışı aşağıda verilmiştir:
1. Hizmetten bazı veya tüm DTDL belgelerini alın.
2. Döndürülen bellek içi DTDL belgelerini ayrıştırıcıya geçirin.
3. Ayrıştırıcı, kendisine geçirilen belge kümesini doğrular ve ayrıntılı hata bilgileri döndürür. Bu özellik, düzenleyici senaryolarında yararlı olur.
4. Belge kümesine dahil edilen modelleri çözümlemeye devam etmek için Ayrıştırıcı API 'Lerini kullanın. 

Ayrıştırıcısının özellikleri şunlardır:
* Uygulanan tüm model arabirimlerini al (arabirimin `extends` bölümünün içeriği).
* Modelde belirtilen tüm özellikleri, telemetri, komutları, bileşenleri ve ilişkileri alın. Bu komut ayrıca bu tanımlarda bulunan tüm meta verileri alır ve Devralınanlar ( `extends` bölümler) alır.
* Tüm karmaşık model tanımlarını alın.
* Bir modelin başka bir modelden atanabilir olup olmadığını belirleme.

> [!NOTE]
> [Iot Tak ve kullan (PnP)](../iot-pnp/overview-iot-plug-and-play.md) cihazları işlevlerini anlatmak için küçük bir sözdizimi değişkeni kullanır. Bu söz dizimi değişkeni, Azure dijital TWINS 'te kullanılan DTDL 'nin anlam ile uyumlu bir alt kümesidir. Ayrıştırıcı kitaplığı kullanılırken, dijital ikizi için DTDL oluşturmak üzere hangi sözdizimi çeşidinin kullanıldığını bilmeniz gerekmez. Ayrıştırıcı her zaman varsayılan olarak, hem PnP hem de Azure Digital TWINS sözdizimi için aynı modeli döndürür.

### <a name="code-with-the-parser-library"></a>Ayrıştırıcı kitaplığıyla kod

Kendi uygulamanızdaki modelleri doğrulama veya dinamik, model temelli UI, pano ve rapor oluşturma gibi şeyler için ayrıştırıcı kitaplığını doğrudan kullanabilirsiniz.

Aşağıdaki ayrıştırıcı kodu örneğini desteklemek için, bir Azure dijital TWINS örneğinde tanımlanan çeşitli modelleri göz önünde bulundurun:

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

Aşağıdaki kod, C# ' de bu tanımları yansıtmak için ayrıştırıcı kitaplığının nasıl kullanılacağına ilişkin bir örnek gösterir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Sonraki adımlar

Modellerinizi yazmayı tamamladıktan sonra, Digitaltwınsmodel API 'Leri ile bunları karşıya yükleme (ve diğer yönetim işlemlerini yapma) konusuna bakın:
* [*Nasıl yapılır: DTDL modellerini yönetme*](how-to-manage-model.md)