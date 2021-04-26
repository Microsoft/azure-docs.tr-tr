---
title: Horizon SDK’sı
description: Ufıt SDK 'Sı, IoT geliştiricilerin IoT Ağ Analizi programları için otomatik Defender tarafından işlenebilmesi için, IoT geliştiricilerine yönelik olarak ağ trafiğinin kodunu çözen bir sektör eklentileri tasarlamasına olanak tanır.
ms.date: 1/13/2021
ms.topic: article
ms.openlocfilehash: 500f1d78a6a2ab91284c993a5c56ef8e679db83f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782495"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Ufuk özel protokol dissektör

Ufuk, özel protokoller çalıştıran IoT ve ICS cihazlarının güvenliğini sağlamak için kullanılan bir açık geliştirme ortamıdır (ODE).

Bu ortam, müşteriler ve teknoloji iş ortakları için aşağıdaki çözümleri sağlar:

- Herhangi bir standarda göre farklılık gösteren ortak, özel, özel protokoller veya protokoller için sınırsız, tam destek. 

- DPı geliştirmesi için yeni bir esneklik ve kapsam düzeyi.

- IoT platform sürümleri için Defender 'ı yükseltmeye gerek olmadan, bir şekilde, bir, ve denetim gibi bir şekilde genişleyen bir araç.

- Divulging hassas bilgiler olmadan özel geliştirmeye izin verme güvenliği.

Ufıt SDK 'Sı, IoT geliştiricilerin IoT Ağ Analizi programları için otomatik Defender tarafından işlenebilmesi için, IoT geliştiricilerine yönelik olarak ağ trafiğinin kodunu çözen bir sektör eklentileri tasarlamasına olanak tanır.

Protokol kesimleri dış eklentiler olarak geliştirilmiştir ve IoT Hizmetleri için çok çeşitli bir Defender ile tümleşiktir. Örneğin, izleme, uyarı ve raporlama özellikleri sağlayan hizmetler.

## <a name="secure-development-environment"></a>Güvenli geliştirme ortamı 

Ufuk kodu, bir kuruluşun dışında paylaşılamayan özel veya özel protokollerin geliştirilmesini mümkün. Örneğin, yasal düzenlemeler veya şirket ilkeleri nedeniyle.

Olmadan dissektör eklentileri geliştirin: 

- protokollerin nasıl tanımlandıkları hakkında özel bilgiler gösterilmektedir.

- hassas PCAPs 'leri paylaşma.

- uyumluluk düzenlemeleri ihlal ediyor.

## <a name="customization-and-localization"></a>Özelleştirme ve yerelleştirme  

SDK aşağıdakiler dahil çeşitli özelleştirme seçeneklerini destekler:

  - İşlev kodları için metin. 

  - Uyarılar, olaylar ve protokol parametreleri için tam yerelleştirme metni. Daha fazla bilgi için bkz. [eşleme dosyaları oluşturma (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Tamamen yerelleştirilmiş uyarıları görüntüleyin.":::

## <a name="horizon-architecture"></a>Ufuk mimarisi

Mimari model üç ürün katmanı içerir.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>IoT platform katmanı için Defender

, IoT platform sürümü için Defender 'ı yükseltmeye gerek kalmadan, IoT Platformu için Defender 'daki özel dissektör eklentileri için anında tümleştirme ve gerçek zamanlı izleme sağlar.

## <a name="defender-for-iot-services-layer"></a>IoT Hizmetleri katmanı için Defender

Her hizmet, belirli bir protokolden ayrılmış, daha verimli, bağımsız geliştirme olanağı sağlayan bir işlem hattı olarak tasarlanmıştır.

Her hizmet, belirli bir protokolden ayrılmış bir işlem hattı olarak tasarlanmıştır. Hizmetler, işlem hattındaki trafiği dinler. Bu kişiler, dağıtılan protokollerin dizinini oluşturup trafik yükünü çözümleyerek ve daha verimli ve bağımsız bir geliştirme olanağı sağlayan, eklenti verileriyle ve algılayıcılar tarafından yakalanan trafikle etkileşime geçebilir.

## <a name="custom-dissector-layer"></a>Özel dissektör katmanı 

IoT özel SDK (C++ uygulama ve JSON yapılandırması dahil) için Defender 'ı kullanarak eklentilerin oluşturulmasını sağlar: 

- Protokolün nasıl tanımlanacağını tanımlayın

- Trafiğin içinden ayıklanmasını istediğiniz alanları nasıl eşleneceğini tanımlayın ve bunları ayıklayın 

- IoT Hizmetleri için Defender ile tümleştirmeyi tanımlama

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Yerleşik katmanlar.":::

IoT için Defender, ortak protokoller için temel kesimleri sağlar. Bu protokollerin en üstünde dissektörlerinizi oluşturabilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

## <a name="what-this-sdk-contains"></a>Bu SDK 'nın içerdiği 

Bu paket, geliştirme için gereken üst bilgi dosyalarını içerir. Geliştirme süreci, bu SDK 'da açıklanan temel adımları ve isteğe bağlı gelişmiş adımları gerektirir.

<support@cyberx-labs.com>Üstbilgi dosyalarını ve diğer kaynakları alma hakkında bilgi edinmek için iletişim kurun.

## <a name="about-the-environment-and-setup"></a>Ortam ve kurulum hakkında 

### <a name="requirements"></a>Gereksinimler 

- Tercih edilen geliştirme ortamı Linux. Bir Windows ortamında geliştiriyorsanız, Linux sistemiyle bir VM kullanmayı düşünün.

- Derleme işlemi için GCC 7.4.0 veya üstünü kullanın. STDLIB 'in c++ 17 altında desteklenen herhangi bir standart sınıfını kullanın.

- İçin Defender sürüm 3,0 ve üzeri.

### <a name="process"></a>İşleme

1. C/C++ geliştiricileri için tutulma IDE 'yi [indirin](https://www.eclipse.org/) . Tercih ettiğiniz herhangi bir IDE 'yi kullanabilirsiniz. Bu belge, tutulma IDE kullanarak yapılandırma sürecinde size rehberlik eder.

1. Çakışan Küreler IDE 'yi başlatıp çalışma alanını yapılandırdıktan sonra (projelerinizi depoladığınız), **CTRL + n** tuşlarına basın ve C++ projesi olarak oluşturun.

1. Sonraki ekranda, adı geliştirmek istediğiniz protokol olarak ayarlayın ve proje türünü ve olarak seçin `Shared Library` `AND Linux GCC` .

1. Proje özelliklerini düzenleyin, **C/C++ derleme**  >  **ayarları**  >  **araç ayarları**  >  **gcc C++ derleyicisi**  >  **çeşitli**  >  **değer konumu bağımsız kodu**.

1. SDK ile aldığınız örnek kodları yapıştırın ve derleyin.

1. Yapıtları (kitaplık, config.jsve meta verileri) bir tar. gz dosyasına ekleyin ve dosya uzantısını. HDP olarak değiştirin; \<XXX> burada, \<XXX> eklentinin adıdır.

### <a name="research"></a>Araştır 

Başlamadan önce şunları doğrulayın:

- Varsa protokol belirtimini okuyun.

- Hangi protokol alanlarını ayıklamanız planlandığınızı öğrenin.

- Eşleme hedeflerinizi planladınız.

## <a name="about-plugin-files"></a>Eklenti dosyaları hakkında 

Geliştirme işlemi sırasında üç dosya tanımlanmıştır.

### <a name="json-configuration-file-required"></a>JSON yapılandırma dosyası (gerekli) 

Bu dosya, değerleri adlara, numaralara metne dönüştürmek için dissektör KIMLIĞI ve bildirimleri, bağımlılıkları, Tümleştirme gereksinimlerini, doğrulama parametrelerini ve eşleme tanımlarını tanımlamalıdır. Daha fazla bilgi için aşağıdaki bağlantılara bakın:

- [Yapılandırma dosyasını hazırlama (JSON)](#prepare-the-configuration-file-json)

- [Uygulama kodu doğrulamaları hazırlama](#prepare-implementation-code-validations)

- [Cihaz meta verilerini Ayıkla](#extract-device-metadata)

- [Bir dizin oluşturma hizmetine (taban çizgisi) bağlanma](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Uygulama kodu: C++ (gerekli)

Uygulama kodu (CPP) ham trafiği ayrıştırır ve hizmet, sınıflar ve işlev kodları gibi değerlerle eşler. Katman alanlarını ayıklar ve bunları JSON yapılandırma dosyalarından dizin adlarıyla eşler. CPP Ayıklanacak alanlar yapılandırma dosyasında tanımlanır. daha fazla bilgi için bkz. [uygulama kodunu hazırlama (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Dosya eşleme (isteğe bağlı)

Eklenti çıkış metnini, kurumsal ortamınızın gereksinimlerini karşılayacak şekilde özelleştirebilirsiniz.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="geçiş":::

Kodu değiştirmeden metni güncelleştirmek için eşleme dosyalarını tanımlayabilir ve güncelleştirebilirsiniz. Her dosya bir veya daha fazla alanı eşleyebilir:

  - Alan değerlerinin adlara eşlenmesi, örneğin, 1: sıfırlama, 2: başlangıç, 3: durdurma.

  - Birden çok dili desteklemek için metin eşleme.

Daha fazla bilgi için bkz. [eşleme dosyaları oluşturma (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Dissektör eklentisi oluşturma (genel bakış)

1. [Ortamı ve kurulumu hakkında](#about-the-environment-and-setup) bölümünü gözden geçirin.

2.  [Uygulama kodunu hazırlayın (C++)](#prepare-the-implementation-code-c). template.jsdosya **üzerinde** kopyalayın ve gereksinimlerinizi karşılayacak şekilde düzenleyin. Anahtarları değiştirmeyin. 

3. [Yapılandırma dosyasını (JSON) hazırlayın](#prepare-the-configuration-file-json). **Template. cpp** dosyasını kopyalayın ve bir geçersiz kılma yöntemi uygulayın. Daha fazla bilgi için bkz. [ufuk::p rotocol:: BaseParser](#horizonprotocolbaseparser) ayrıntılar.

4. [Uygulama kodu doğrulamaları hazırlayın](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Uygulama kodunu hazırlama (C++)

CPP dosyası, şu bir Ayrıştırıcıdan sorumludur:

- Paket üst bilgisini ve yükünü doğrulama (örneğin, üst bilgi uzunluğu veya yük yapısı).

- Üstbilgi ve yükten verileri tanımlanmış alanlara ayıklama.

- JSON dosyası tarafından yapılandırılmış alan ayıklama uygulama.

### <a name="what-to-do"></a>Ne yapılmalı

Template **. cpp** dosyasını kopyalayın ve bir geçersiz kılma yöntemi uygulayın. Daha fazla bilgi için bkz. [ufuk::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Temel C++ şablonu örneği 

Bu bölüm, IoT ufku protokolüne yönelik örnek bir Defender için standart işlevlerle temel protokol şablonu sağlar.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Temel C++ şablonu açıklaması  

Bu bölüm, IoT ufku protokolüne yönelik örnek bir Defender için standart işlevlerin açıklamasıyla birlikte temel protokol şablonu sağlar. 

### <a name="include-pluginpluginh"></a>#include "Plugin/Plugin. h"

Eklentinin kullandığı tanım. Üst bilgi dosyası, geliştirmeyi tamamlaması için gereken her şeyi içerir.

### <a name="horizonprotocolbaseparser"></a>ufuk::p rotocol:: BaseParser

Ufuk altyapısı ve eklenti katmanı arasındaki iletişim arabirimi. Daha fazla bilgi için bkz. katmanlara genel bakış için [ufuk mimarisi](#horizon-architecture) .

ProcessLayer, verileri işlemek için kullanılan yöntemdir.

- İşlev kodundaki ilk parametre, daha önce işlenmiş olan verileri almak, yeni alanlar ve katmanlar oluşturmak için kullanılan işleme yardımcı programıdır.

- İşlev kodundaki ikinci parametre, önceki Ayrıştırıcıdan geçirilen geçerli veri.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Ayrıştırıcılarınızın örneğini oluşturmak için kullanın.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Protokol işlev kodu örneği 

Bu bölüm, kod numarasının (2 bayt) ve ileti uzunluğunun (4 bayt) nasıl ayıklandığını gösteren bir örnek sağlar.

Bu işlem, JSON yapılandırma dosyasında sağlanan bitiyine göre yapılır, bu da protokol *biraz* daha uygun olduğunda ve algılayıcı kısa bir süre sonra bir makine üzerinde çalışıyorsa, bu işlem dönüştürülür.

Ayrıca, verileri depolamak için bir katman oluşturulur. Yeni alanlar oluşturmak için işleme yardımcı programları ' ndan *alanlar yöneticisini* kullanın. Bir alan şu türlerden yalnızca birine sahip olabilir: *dize*, *sayı*, *ham veri*, *dizi* (belirli tür) veya *karmaşık*. Bu katman bir sayı, ham veya KIMLIĞI olan dize içerebilir.

Aşağıdaki örnekte, aşağıdaki iki alan ayıklanır:

- `function_code_number`

- `headerLength`

Yeni bir katman oluşturulur ve ayıklanan alan bu tabloya kopyalanır.

Aşağıdaki örnek, eklenti işleme için uygulanan ana mantık olan belirli bir işlevi açıklamaktadır.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>İlgili JSON alanı 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="İlgili JSON alanı.":::

## <a name="prepare-the-configuration-file-json"></a>Yapılandırma dosyasını hazırlama (JSON)

Ufuk SDK 'Sı standart JavaScript Nesne Gösterimi (JSON), verileri depolamak ve aktarmak için hafif bir biçim kullanır ve özel betik dilleri gerektirmez.

Bu bölümde, ilgili yapı ve bir protokolü tanımlayan örnek bir yapılandırma dosyası sağlayan en az JSON yapılandırma bildirimleri açıklanmaktadır. Bu protokol cihaz bulma hizmeti ile otomatik olarak tümleşiktir.

## <a name="file-structure"></a>Dosya yapısı

Aşağıdaki örnek dosya yapısını açıklamaktadır.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Dosya yapısının örneği.":::

### <a name="what-to-do"></a>Ne yapılmalı

Şablon dosyasını kopyalayın `config.json` ve gereksinimlerinizi karşılayacak şekilde düzenleyin. Anahtarı değiştirmeyin. Anahtarlar [örnek JSON yapılandırma dosyasında](#sample-json-configuration-file)kırmızı renkle işaretlenir. 

### <a name="file-naming-requirements"></a>Dosya adlandırma gereksinimleri 

JSON yapılandırma dosyasının olarak kaydedilmesi gerekir `config.json` .

### <a name="json-configuration-file-fields"></a>JSON yapılandırma dosyası alanları

Bu bölümde, tanımladığınız JSON yapılandırma alanları açıklanmaktadır. Alanlar *etiketlerini* değiştirmeyin.

### <a name="basic-parameters"></a>Temel parametreler

Bu bölümde temel parametreler açıklanmaktadır.

| Parametre etiketi | Açıklama | Tür |
|--|--|--|
| **ID** | Protokolün adı. Varsayılan ' i silin ve görüntülenen iletişim kuralının adını ekleyin. | Dize |
| **bitilaness** | Çoklu baytlık verilerin nasıl kodlandığını tanımlar. Yalnızca "az" veya "Big" terimini kullanın. Protokol belirtimi veya trafik kaydından alınmıştır | Dize |
| **sanity_failure_codes** | Bunlar, kodun kimliğiyle ilgili bir sanlık çakışması olduğunda Ayrıştırıcıdan döndürülen kodlardır. C++ bölümünde sihirli sayı doğrulaması ' na bakın. | Dize |
| **malformed_codes** | Bunlar düzgün tanımlanmış kodlardır, ancak bir hata algılanır. Örneğin, alan uzunluğu çok kısa veya uzunsa ya da bir değer geçersizdir. | Dize |
| **dissect_as** | Belirli protokol trafiğinin nereden gelmesi gerektiğini tanımlayan bir dizi. | TCP/UDP, bağlantı noktası vb. |
| **alanını** | Trafikten hangi alanların ayıklanabileceği bildirimi. Her alanın kendi KIMLIĞI (ad) ve türü (sayısal, dize, ham, dizi, karmaşık) vardır. Örneğin, uygulama ayrıştırıcı dosyasında ayıklanan alan [işlevi](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) . Yapılandırma dosyasında yazılan alanlar katmana eklenebilecek tek alanlardır. |  |

### <a name="other-advanced-fields"></a>Diğer Gelişmiş alanlar 

Bu bölümde diğer alanlar açıklanmaktadır.

| Parametre etiketi | Description |
|-----------------|--------|
| **listelere izin ver** | Protokol değerlerini dizinedebilir ve bunları veri araştırma raporlarında görüntüleyebilirsiniz. Bu raporlar, ağ temelinizi yansıtır. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Veri araştırma görünümü örneği."::: <br /> Daha fazla bilgi için bkz. Ayrıntılar için [Dizin oluşturma hizmetine (Baseline) bağlanma](#connect-to-an-indexing-service-baseline) . |
| **yazılımında** | Bellenim bilgilerini ayıklayabilir, dizin değerlerini tanımlayabilir ve eklenti protokolü için bellenim uyarılarını tetikleyebilirsiniz. Daha fazla bilgi için bkz. Ayrıntılar için [bellenim verilerini ayıklama](#extract-firmware-data) . |
| **value_mapping** | Eşleme dosyalarını tanımlayarak ve güncelleştirerek, eklenti çıkış metnini kurumsal ortamınızın ihtiyaçlarını karşılayacak şekilde özelleştirebilirsiniz. Örneğin, dil dosyalarıyla eşleyin. Değişiklikler kodu değiştirmeden veya etkilemeden kolayca metne uygulanabilir. Daha fazla bilgi için bkz. Ayrıntılar için [eşleme dosyaları oluşturma (JSON)](#create-mapping-files-json) . |

## <a name="sample-json-configuration-file"></a>Örnek JSON yapılandırma dosyası

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Uygulama kodu doğrulamaları hazırlama

Bu bölüm, uygulama C++ kod doğrulama işlevlerini açıklar ve örnek kod sağlar. İki doğrulama katmanı kullanılabilir:

- Sağlamlık.

- Hatalı biçimlendirilmiş kod.

Çalışan bir eklenti oluşturmak için doğrulama kodu oluşturmanız gerekmez. Doğrulama kodunu hazırlamazsanız, başarılı işleme göstergesi olarak algılayıcı veri araştırma raporlarını gözden geçirebilirsiniz.

Alan değerleri, eşleme dosyalarındaki metinle eşleştirilebilir ve işlemeyi etkilemeden sorunsuzca güncellenir.

## <a name="sanity-code-validations"></a>Sanity kod doğrulamaları 

Bu, iletilen paketin protokolün doğrulama parametreleriyle eşleştiğini doğrular ve bu da trafiği trafik içinde tanımlamanızı sağlar.

Örneğin, *sihirli sayı* olarak ilk 8 baytı kullanın. Sağlamlık başarısız olursa, bir sağlamlık hata yanıtı döndürülür.

Örnek:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

İlgili diğer eklentiler dağıtılmışsa, paket bunlara göre doğrulanacak.

## <a name="malformed-code-validations"></a>Hatalı biçimlendirilmiş kod doğrulamaları 

Hatalı biçimlendirilmiş doğrulamalar, protokol olumlu doğrulandıktan sonra kullanılır.

İletişim kuralına bağlı olarak paketleri işlemek için bir hata oluşursa, bir hata yanıtı döndürülür.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="Hatalı biçimlendirilmiş kod":::

## <a name="c-sample-with-validations"></a>Doğrulamaları olan C++ örneği

İşleve göre işlem, aşağıdaki örnekte gösterildiği gibi yürütülür.

### <a name="function-20"></a>İşlev 20 

- Üretici yazılımı olarak işlenir.

- Alanlar, işleve göre okunurdur.

- Alanlar katmana eklenir.

### <a name="function-10"></a>İşlev 10 

- İşlev, daha belirli bir işlem olan başka bir alt işlev içeriyor

- Alt işlev okundu ve katmana eklendi.

Bu yapıldıktan sonra işlem tamamlanır. Dönüş değeri, dissektör katmanının başarıyla işlenip işlenmeyeceğini gösterir. Bu durumda, katman kullanılabilir hale gelir.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Cihaz meta verilerini Ayıkla

Varlıklar üzerinde aşağıdaki meta verileri ayıklayabilirsiniz:

  - `Is_distributed_control_system` -Protokolün dağıtılmış denetim sisteminin bir parçası olup olmadığını gösterir. (örnek: SCADA Protokolü yanlış olmalıdır)

  - `Has_protocol_address` – Bir protokol adresi olup olmadığını belirtir; geçerli protokolün belirli bir adresi, örneğin MODBUS birim tanımlayıcısı

  - `Is_scada_protocol` -Protokolün OT ağlarına özgü olup olmadığını gösterir

  - `Is_router_potential` -Protokolün genellikle yönlendiriciler tarafından kullanıldığını gösterir. Örneğin, LLDP, CDP veya STP.

Bunu başarmak için, JSON yapılandırma dosyasının Metadata özelliği kullanılarak güncelleştirilmesi gerekir.

## <a name="json-sample-with-metadata"></a>Meta verilerle JSON örneği 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Programlama kodunu Ayıkla 

Programlama olayı gerçekleştiğinde, kod içeriğini ayıklayabilirsiniz. Ayıklanan içerik şunları yapmanızı sağlar:

- Farklı programlama olaylarında kod dosyası içeriğini karşılaştırın.

- Yetkisiz programlamada uyarı tetikleyin.  

- Programlama kodu dosyasını almak için bir olay tetikleyin.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Programlama değişiklik günlüğü.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Düğmeye tıklayarak programlamayı görüntüleyin.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Yetkisiz PLC programlama uyarısı.":::

Bunu başarmak için, JSON yapılandırma dosyasının özelliği kullanılarak güncelleştirilmesi gerekir `code_extraction` . 

### <a name="json-configuration-fields"></a>JSON yapılandırma alanları 

Bu bölümde JSON yapılandırma alanları açıklanmaktadır. 

- **yöntemidir**

  Programlama olayı dosyalarının alınma yöntemini gösterir. 

  Tümü (her programlama eylemi, değişiklik yapılmadan dosyalar olsa bile tüm kod dosyalarının alınmasına neden olur).

- **file_type**  

  Kod içerik türünü gösterir.  

  METIN (her kod dosyası metin bilgilerini içerir).

- **code_data_field**
  
  Kod içeriğini sağlamak için kullanılacak uygulama alanını gösterir.

  Alanla.

- **code_name_field**

  Kodlama dosyasının adını sağlamak için kullanılacak uygulama alanını gösterir.

  Alanla.

- **size_limit**

  Bir kod dosyası ayarlanan sınırı aşarsa bırakılacak her bir kodlama dosyası içeriğinin boyut sınırını bayt cinsinden gösterir. Bu alan belirtilmemişse, varsayılan değer 15 MB olan 15.000.000 olacaktır.

  Sayısından.

- **veriyi**

  Kod dosyası için ek bilgileri gösterir.

  İki özelliği olan nesneleri içeren dizi:

    - ad (dize)-meta veri anahtarı XSense 'nin şu anda yalnızca Kullanıcı adı anahtarını desteklediğini gösterir.
 
    - değer (alan)-meta veri verileri sağlamak için kullanılacak uygulama alanını gösterir.

## <a name="json-sample-with-programming-code"></a>Programlama kodlu JSON örneği

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Özel ufku uyarıları

Bazı protokoller işlev kodu bir hata gösterebilir. Örneğin, protokol her zaman belirli bir sıcaklığa göre depolanması gereken belirli bir kimyasal ile kapsayıcıyı denetliyorsa. Bu durumda, termometre 'de hata belirten işlev kodu olabilir. Örneğin, işlev kodu 25 ise, Web konsolunda, kapsayıcıda bir sorun olduğunu belirten bir uyarı tetikleyebilirsiniz. Böyle bir durumda, derin paket uyarıları tanımlayabilirsiniz.

, Eklentinin öğesine **Uyarılar** parametresini ekleyin `config.json` .

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON yapılandırma alanları

Bu bölümde JSON yapılandırma alanları açıklanmaktadır. 

| Alan adı | Description | Olası değerler |
|--|--|--|
| **ID** | Tek bir uyarı KIMLIĞINI temsil eder. Bu bağlamda benzersiz olmalıdır. | Sayısal değer 0-10000 |
| **message** | Kullanıcıya görüntülenecek bilgiler. Bu alan, farklı alanlar kullanmanıza izin verir. | Protokolinizdeki herhangi bir alanı veya herhangi bir alt katman protokolünü kullanın. |
| **Başlığın** | Uyarı başlığı |  |
| **expression** | Bu uyarının açılır olmasını istediğiniz zaman. | Alt katmanlarda veya geçerli katmanda bulunan herhangi bir sayısal alanı kullanın.</br></br> `{}`SDK 'nın onu bir alan olarak algılayabilmesi için her bir alanın ile sarmalayıcı olması gerekir; desteklenen mantıksal işleçler şunlardır</br> = =-Eşittir</br> <=-küçüktür veya eşittir</br> >=-daha fazla veya eşit</br> >-daha fazla</br> <-küçüktür</br> ~ =-Eşit değil |

## <a name="more-about-expressions"></a>İfadeler hakkında daha fazla bilgi

Ufuk SDK 'Sı ifadeyi her çağırdığında ve *doğru* hale geldiğinde, sensörde bir uyarı tetiklenir.

Aynı uyarıya birden çok ifade dahil edilebilir. Örneğin,

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Bu ifade, işlev kodunu yalnızca paket IPv4 src 10.10.10.10 olduğunda doğrular. Bu, IP adresinin sayısal gösterimde ham bir gösterimidir.

`and` `or` İfadelerini bağlamak için ya da kullanabilirsiniz.

## <a name="json-sample-custom-horizon-alerts"></a>JSON örnek özel ufku uyarıları

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Bir dizin oluşturma hizmetine (taban çizgisi) bağlanma

Protokol değerlerini dizinedebilir ve bunları veri araştırma raporlarında görüntüleyebilirsiniz.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Veri araştırma seçeneğinin görünümü.":::

Bu değerler daha sonra belirli metinlere eşleştirilebilir, örneğin sayıları metin olarak eşleme veya herhangi bir dilde bilgi ekleme olabilir.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="geçiş":::

Daha fazla bilgi için bkz. Ayrıntılar için [eşleme dosyaları oluşturma (JSON)](#create-mapping-files-json) .

Daha önce ek bilgi ayıklamak için ayrıştırılabilen protokollerin değerlerini de kullanabilirsiniz.

Örneğin, TCP 'yi temel alan değer için IPv4 katmanından değerleri kullanabilirsiniz. Bu katmandan paketin kaynağı ve hedef gibi değerleri ayıklayabilirsiniz.

Bunu başarmak için, JSON yapılandırma dosyasının özelliği kullanılarak güncelleştirilmesi gerekir `whitelists` .

## <a name="allow-list-data-mining-fields"></a>İzin verilenler listesi (veri madenciliği) alanları

Aşağıdaki izin verilenler listesi alanları kullanılabilir:

- ad – dizin oluşturmak için kullanılan ad.

- alert_title: olayı açıklayan, kısa, benzersiz bir başlık.

- alert_text – ek bilgi

Çoklu izin verilenler listesi eklenebilir ve dizin oluşturma sırasında tüm esnekliğe izin verebilir.

## <a name="json-sample-with-indexing"></a>Dizin oluşturma ile JSON örneği 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Bellenim verilerini Ayıkla

Bellenim bilgilerini ayıklayabilir, dizin değerlerini tanımlayabilir ve eklenti protokolü için bellenim uyarılarını tetikleyebilirsiniz. Örneğin,

- Bellenim modelini veya sürümünü ayıklayın. Bu bilgiler, Cilakleri belirlemek için daha fazla kullanılabilir.

- Yeni bir bellenim sürümü algılandığında uyarı tetikleyin.

Bunu başarmak için, JSON yapılandırma dosyasının bellenim özelliği kullanılarak güncelleştirilmesi gerekir.

## <a name="firmware-fields"></a>Bellenim alanları

Bu bölümde JSON üretici yazılımı yapılandırma alanları açıklanmaktadır.

- **ada**
  
  Alanın algılayıcı konsolunda nasıl sunulduğunu gösterir.

- **değer**

 Verileri sağlamak için kullanılacak uygulama alanını gösterir. 

- **firmware_index:**

  Birini seçin:  
  - **model**: cihaz modeli. Cdirme algılamasını sunar.
  - **seri**: cihaz seri numarası. Seri numarası tüm protokollerde her zaman kullanılabilir değildir. Bu değer, cihaz başına benzersizdir.
  - **raf**: cihaz bir rafa ait ise raf tanımlayıcısını gösterir.
  - **yuva**: cihaz bir rafın parçasıysa yuva tanımlayıcısı.  
  - **module_address**: modülün başka bir cihazın arkasında sunulabiliyorsanız bir hiyerarşi sunmak için kullanın. Daha basit bir sunum olan raf yuvası birleşimi yerine uygulanabilir.  
  - **firmware_version**: cihaz sürümünü gösterir. Cdirme algılamasını sunar.
  - **alert_text**: bellenim sapmalarını açıklayan metni gösterir, örneğin, sürüm değişiklikleri.  
  - **index_by**: cihazı tanımlamak ve dizinlemek için kullanılan alanları gösterir. Aşağıdaki örnekte, cihazın IP adresi ile tanımlanmıştır. Bazı protokollerde, daha karmaşık bir dizin kullanılabilir. Örneğin, başka bir cihaz bağlandıysa ve iç yolunun nasıl ayıklanacağını bilirsiniz. Örneğin, MODBUS birim KIMLIĞI, farklı bir IP adresi birleşimi ve birim tanımlayıcısı olarak dizinin bir parçası olarak kullanılabilir.
  - **firmware_fields**: hangi alanların cihaz meta verisi alanları olduğunu gösterir. Bu örnekte, aşağıdakiler kullanılır: model, düzeltme ve ad. Her protokol kendi üretici yazılımı verilerini tanımlayabilir.

## <a name="json-sample-with-firmware"></a>Üretici yazılımı ile JSON örneği 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Cihaz özniteliklerini Ayıkla 

Cihazda, envanter, veri madenciliği ve diğer raporlarda bulunan bilgileri geliştirebilirsiniz.

- Ad

- Tür

- Satıcı

- Operating System

Bunu başarmak için, JSON yapılandırma dosyasının **Özellikler** özelliği kullanılarak güncelleştirilmesi gerekir. 

Temel eklentiyi yazdıktan ve gerekli alanları ayıkladıktan sonra bunu yapabilirsiniz.

## <a name="properties-fields"></a>Özellikler alanları

Bu bölümde JSON özellikleri yapılandırma alanları açıklanmaktadır. 

**config_file** 

Alanlardaki her bir anahtarın nasıl işleyeceğini tanımlayan dosya adını içerir `key` . Yapılandırma dosyasının kendisi JSON biçiminde olmalıdır ve eklenti protokol klasörünün bir parçası olarak dahil edilmelidir.

JSON 'daki her anahtar, bu anahtarı bir paketten ayıkladığınızda gerçekleştirilmesi gereken eylem kümesini tanımlar.

Her anahtar şunları içerebilir:

- **Paket verileri** -paketten ayıklanan verilere (bu verileri sağlamak için kullanılan uygulama alanı) göre güncellenen özellikleri gösterir.

- **Statik veri** -güncellenmesi gereken önceden tanımlanmış `property-value` eylem kümesini gösterir.

Bu dosyada yapılandırılabilecek özellikler şunlardır: 

- **Ad** -cihaz adını gösterir.

- **Tür** -cihaz türünü gösterir.

- **Satıcı** -cihaz satıcısını gösterir.

- **Açıklama** -cihaz üretici yazılımı modelini gösterir ("model" değerinden düşük öncelik).

- **operatingSystem** -cihaz işletim sistemini belirtir.

### <a name="fields"></a>Alanlar

| Alan | Açıklama |
|--|--|
| anahtar | Anahtarı gösterir. |
| değer | Verileri sağlamak için kullanılacak uygulama alanını gösterir. |
| is_static_key | `key`Alanın paketten bir değer olarak türetildiğini veya önceden tanımlanmış bir değer olup olmadığını gösterir. |

### <a name="working-with-static-keys-only"></a>Yalnızca statik anahtarlarla çalışma

Statik anahtarlarla çalışıyorsanız, öğesini yapılandırmanız gerekmez `config.file` . Yalnızca JSON dosyasını yapılandırabilirsiniz.

## <a name="json-sample-with-properties"></a>Özelliklerle JSON örneği 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Eşleme dosyaları oluşturma (JSON)

Dosya tanımlayarak ve güncelleştirme eşleme ile kurumsal ortamınızın ihtiyaçlarını karşılamak için eklenti çıkış metnini özelleştirebilirsiniz. Değişiklikler kodu değiştirmeden veya etkilemeden kolayca metne uygulanabilir. Her dosya bir veya daha fazla alanı eşleyebilir.

- Alan değerlerinin adlarla eşlenmesi, örneğin 1: Reset, 2: başlangıç, 3: durdur.

- Birden çok dili desteklemek için metin eşleme.

İki tür eşleme dosyası tanımlanabilir.

  - [Basit eşleme dosyası](#simple-mapping-file).

  - [Bağımlılık eşleme dosyası](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="istatistiklerinden net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="İşlenmemiş uyarıların bir görünümü.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Bilinen ilke ihlallerinin listesi.":::

## <a name="file-naming-and-storage-requirements"></a>Dosya adlandırma ve depolama gereksinimleri 

Eşleme dosyaları meta veri klasörü altına kaydedilmelidir.

Dosyanın adı JSON yapılandırma dosyası KIMLIĞIYLE eşleşmelidir.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON yapılandırma dosyası örneği.":::

## <a name="simple-mapping-file"></a>Basit eşleme dosyası 

Aşağıdaki örnek, temel bir JSON dosyasını anahtar değer olarak gösterir.

Bir izin verilenler listesi oluşturduğunuzda ve bir veya daha fazla eşlenmiş alan içeriyorsa. Değer bir sayı, dize veya herhangi bir türden, eşleme dosyasında gösterilen biçimlendirilmiş metne dönüştürülecek.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Bağımlılık eşleme dosyası 

Dosyanın bir bağımlılık dosyası olduğunu göstermek için, eşleme yapılandırmasına anahtar sözcüğünü ekleyin `dependency` .

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Dosya, bağımlılık alanı ile işlev alanı arasında bir eşleme içerir. Örneğin, Function ve sub işlevi. Alt işlev, sağlanan işleve göre değişir.

Daha önce yapılandırılmış izin verilenler listesinde aşağıda gösterildiği gibi bağımlılık yapılandırması yoktur.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

Bağımlılık belirli bir değeri veya alanı temel alabilir. Aşağıdaki örnekte, bir alanı temel alır. Bu değeri bir değere dayandırırsanız, eşleme dosyasından okunacak ayıklama değerini tanımlayın.

Aşağıdaki örnekte, alanın aynı değeri için ' i takip eden bağımlılığı.

Örneğin, beş alt işlevinde, anlamı işlevine göre değişir.

  - Bir okuma işlevi ise, beş bellek okuma anlamına gelir.

  - Bir yazma işleviniz ise, bir dosyadan okumak için aynı değer kullanılır.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Örnek dosya

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Eşleme ile JSON örneği

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Eklentiyi paketleme, karşıya yükleme ve izleme 

Bu bölümde nasıl yapılacağı açıklanmaktadır

  - Eklentiyi paketleyin.

  - Eklentiyi karşıya yükleyin.

  - Ne kadar iyi bir şekilde çalıştığını değerlendirmek için eklentiyi izleyin ve hata ayıklayın.

Eklentiyi paketlemek için:

1. **Yapıtı** (olabilir, kitaplık, config.jsveya meta veriler) bir `tar.gz` dosyaya ekleyin.

1. Dosya uzantısını olarak değiştirin \<XXX.hdp> ; burada, \<XXX> eklentinin adıdır.

Ufku konsolunda oturum açmak için:

1.  Algılayıcı CLı 'yı yönetici, Six veya destek kullanıcısı olarak oturum açın.

2.  Dosyada: `/var/cyberx/properties/horizon.properties` **UI. Enabled** özelliğini **true** () olarak değiştirin `horizon.properties:ui.enabled=true` .

3.  Algılayıcı konsolunda oturum açın.

4.  Ana menüden **ufuk** seçeneğini belirleyin.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Sol taraftaki bölmeden ufuk seçeneğini belirleyin.":::

    Ufuk konsolu açılır.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Ufku konsolunun ve tüm eklentilerin bir görünümü.":::

## <a name="plugins-pane"></a>Eklentiler bölmesi

Eklenti bölmesi şunları listeler:

  - Altyapı eklentileri: IoT için Defender ile varsayılan olarak yüklenen altyapı eklentileri.

  - Uygulama eklentileri:, IoT için Defender ve IoT veya dış geliştiriciler tarafından geliştirilen diğer eklentiler için Defender ile varsayılan olarak yüklenen uygulama eklentileri.
    
Geçiş kullanılarak karşıya yüklenen eklentileri etkinleştirin ve devre dışı bırakın.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CıP geçişi.":::

### <a name="uploading-a-plugin"></a>Eklenti karşıya yükleniyor

Eklentiyi oluşturup paketledikten sonra IoT algılayıcısı için Defender 'a yükleyebilirsiniz. Ağınızın tam kapsamını elde etmek için, eklentiyi kuruluşunuzdaki her bir sensöre yüklemeniz gerekir.

Karşıya yüklemek için:

1.  Sensörde oturum açın.


2. **Karşıya Yükle**’yi seçin.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Eklentilerinizi karşıya yükleyin.":::

3. Eklentiye gidin ve eklentiyi eklenti iletişim kutusuna sürükleyin. Önekinin olduğunu doğrulayın `.hdp` . Eklenti yüklenir.

## <a name="plugin-status-overview"></a>Eklenti durumuna genel bakış 

Ufku konsoluna **genel bakış** penceresi, karşıya yüklediğiniz eklenti hakkında bilgi sağlar ve bunları devre dışı bırakıp etkinleştirmenizi sağlar.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Ufku konsoluna genel bakış.":::

| Alan | Açıklama |
|--|--|
| Uygulama | Karşıya yüklediğiniz eklentinin adı. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Açık ve kapalı anahtar."::: | Eklentiyi **açın** veya **kapatın** . Eklenti devre dışı bırakıldığında, IoT için Defender, eklenti içinde tanımlanan protokol trafiğini işlemeyecektir. |
| Saat | Verilerin en son çözümlenme zamanı. Her 5 saniyede bir güncelleştirilir. |
| PPS | Saniye başına paket sayısı. |
| Bant genişliği | Son 5 saniye içinde algılanan ortalama bant genişliği. |
| Malforms | Hatalı biçimlendirilmiş doğrulamalar, protokol olumlu doğrulandıktan sonra kullanılır. İletişim kuralına bağlı olarak paketleri işlemek için bir hata oluşursa, bir hata yanıtı döndürülür.   <br><br>Bu sütun, son 5 saniye içindeki malform hatalarının sayısını belirtir. Daha fazla bilgi için bkz. Ayrıntılar için [Hatalı biçimlendirilmiş kod doğrulamaları](#malformed-code-validations) . |
| Uyarılar | Paketler yapıyla ve belirtile eşleşir, ancak eklenti uyarı yapılandırmasına bağlı olarak beklenmeyen bir davranış vardır. |
| Hatalar | Temel protokol doğrulamaları başarısız olan paket sayısı. Paketin protokol tanımlarıyla eşleştiğini doğrular. Burada görüntülenen sayı, son 5 saniyede algılanan hataların sayısını gösterir. Daha fazla bilgi için bkz. Ayrıntılar için [Sanity kod doğrulamaları](#sanity-code-validations) . |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="İzleyici simgesi."::: | Plugin için algılanan malform ve uyarılarla ilgili ayrıntıları gözden geçirin. |

## <a name="plugin-details"></a>Eklenti ayrıntıları

Eklenti için algılanan *Malform* ve *Uyarı* sayısını çözümleyerek gerçek zamanlı eklenti davranışını izleyebilirsiniz. Daha fazla araştırma için ekranı dondurabilir ve dışarı aktarmaya yönelik bir seçenek mevcuttur

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP İzleyici ekranı.":::

Izlemek için:

Genel bakışta, eklenti için Izleyici düğmesini seçin.

Sonraki Adımlar

[Ufuk API](references-horizon-api.md) 'nizi ayarlama
