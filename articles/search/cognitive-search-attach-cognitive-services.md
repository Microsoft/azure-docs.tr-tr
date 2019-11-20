---
title: Bilişsel Hizmetleri beceri 'e Ekle
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de bir AI zenginleştirme ardışık düzenine bir bilişsel hizmetler ekleme yönergeleri.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d65b9b60ce93656c9acdc76c77291114468d345a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113932"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure Bilişsel Arama bir beceri bilişsel hizmetler kaynağı iliştirme 

AI algoritmaları, Azure Bilişsel Arama 'da içerik dönüştürmesi için kullanılan [zenginleştirme işlem hatlarını](cognitive-search-concept-intro.md) kullanır. Bu algoritmalar, görüntü analizi için [görüntü işleme](https://azure.microsoft.com/services/cognitive-services/computer-vision/) , optik karakter tanıma (OCR) ve varlık tanıma, anahtar tümceciği ayıklama ve diğer zenginler için [metin analizi](https://azure.microsoft.com/services/cognitive-services/text-analytics/) dahil olmak üzere Azure bilişsel hizmetler kaynaklarını temel alır. Belge zenginleştirme amaçları için Azure Bilişsel Arama tarafından kullanıldığı gibi algoritmalar *, bir*yeteneğin içine yerleştirilmiş ve dizin oluşturma sırasında bir *Dizin Oluşturucu* tarafından başvurulan bir *yetenek*içine sarılır.

Sınırlı sayıda belgeyi ücretsiz olarak zenginleştirebilirsiniz. Ya da, daha büyük ve daha sık sık iş yükleri için *beceri* 'e faturalanabilir bir bilişsel hizmetler kaynağı iliştirebilirsiniz. Bu makalede, Azure Bilişsel Arama [Dizin oluşturma](search-what-is-an-index.md)sırasında belgeleri zenginleştirmek üzere bir faturalanabilir bilişsel hizmetler kaynağını nasıl ekleyeceğinizi öğreneceksiniz.

> [!NOTE]
> Faturalanabilir olaylar, Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak Bilişsel Hizmetler API'si ve görüntü ayıklama için çağrılar içerir. Belgelerden veya bilişsel hizmetler 'i çağırmayan yetenekler için metin ayıklama ücreti alınmaz.
>
> Faturalanabilir yeteneklerin yürütülmesi, bilişsel [Hizmetler Kullandıkça Öde fiyatındaki](https://azure.microsoft.com/pricing/details/cognitive-services/)ücretlendirilir. Görüntü ayıklama fiyatlandırması için bkz. [Azure bilişsel arama fiyatlandırma sayfası](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Aynı bölge gereksinimi

Azure Bilişsel Arama ve Azure bilişsel hizmetler 'in aynı bölgede mevcut olması gerekir. Aksi takdirde, bu iletiyi çalışma zamanında alırsınız: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Bir hizmeti bölgeler arasında taşımanın bir yolu yoktur. Bu hatayı alırsanız, Azure Bilişsel Arama ile aynı bölgede yeni bir bilişsel hizmetler kaynağı oluşturmanız gerekir.

> [!NOTE]
> Bazı yerleşik yetenekler, bölgesel olmayan bilişsel hizmetler 'e (örneğin, [metin çevirisi becerisi](cognitive-search-skill-text-translation.md)) dayanır. Bu becerilerden herhangi birini beceri ' e eklerseniz, verilerinizin Azure Bilişsel Arama veya bilişsel hizmetler kaynağı ile aynı bölgede kalmasını sağlamak için garanti olmadığını unutmayın. Daha fazla bilgi için bkz. [hizmet durumu sayfası](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Ücretsiz kaynakları kullanma

AI zenginleştirme öğreticisini ve hızlı başlangıç alıştırmaları ' nı tamamlayabilmeniz için sınırlı, ücretsiz bir işleme seçeneğini kullanabilirsiniz.

Ücretsiz (sınırlı enzenginler) kaynakları, her abonelik için günde 20 belge ile kısıtlıdır.

1. Veri Içeri aktarma Sihirbazı 'nı açın:

   ![Veri Içeri aktarma Sihirbazı 'nı açın](media/search-get-started-portal/import-data-cmd.png "Veri Içeri aktarma Sihirbazı 'nı açın")

1. Bir veri kaynağı seçin ve **AI zenginleştirme (Isteğe bağlı) eklemeye**devam edin. Bu sihirbaza yönelik adım adım yönergeler için bkz. [Azure Portal dizin oluşturma](search-get-started-portal.md).

1. Bilişsel **Hizmetler Ekle** ' yi genişletin ve ücretsiz ' i **(sınırlı enzenginler)** seçin:

   ![Genişletilmiş ek bilişsel Hizmetler bölümü](./media/cognitive-search-attach-cognitive-services/attach1.png "Genişletilmiş ek bilişsel Hizmetler bölümü")

1. Artık bilişsel **yetenekler ekleme**dahil olmak üzere sonraki adımlara devam edebilirsiniz.

## <a name="use-billable-resources"></a>Faturalanabilir kaynakları kullanma

Günde 20 ' den fazla zenginleştirme oluşturan iş yükleri için faturalandırılabilir bilişsel hizmetler kaynağı iliştirdiğinizden emin olun. Bilişsel Hizmetler API'si çağrısı yapmasanız bile, her zaman faturalanabilir bir bilişsel hizmetler kaynağı iliştirmenizi öneririz. Kaynak eklemek günlük sınırı geçersiz kılar.

Yalnızca Bilişsel Hizmetler API'si çağıran yetenekler için ücret ödersiniz. [Özel yetenekler](cognitive-search-create-custom-skill-example.md)veya [metin merkli](cognitive-search-skill-textmerger.md), [metin bölücü](cognitive-search-skill-textsplit.md)ve [mil](cognitive-search-skill-shaper.md)gibi yetenekler için, API tabanlı olmayan beceriler için faturalandırılırsınız.

1. Veri alma Sihirbazı ' nı açın, bir veri kaynağı seçin ve **AI zenginleştirme (Isteğe bağlı) eklemeye**devam edin.

1. Bilişsel **Hizmetler Ekle** ' yi genişletin ve yeni bilişsel **Hizmetler kaynağı oluştur**' u seçin Kaynağı oluşturabilmeniz için yeni bir sekme açılır:

   ![Bilişsel hizmetler kaynağı oluşturma](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Bilişsel Hizmetler kaynağı oluşturma")

1. **Konum** listesinde Azure bilişsel arama hizmetinizin bulunduğu bölgeyi seçin. Performans nedenleriyle bu bölgeyi kullandığınızdan emin olun. Bu bölge kullanıldığında, bölgeler arasında giden bant genişliği ücretlerini de indirebilirsiniz.

1. **Fiyatlandırma katmanı** listesinde, Azure bilişsel arama tarafından sunulan yerleşik becerileri kapsayan adım adım Hizmetleri özellikleri dahil olmak üzere tüm bilişsel hizmetler özelliklerini almak için **S0** ' ı seçin.

   S0 katmanı için, bilişsel [Hizmetler fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/cognitive-services/)belirli iş yükleri için ücretleri bulabilirsiniz.
  
   + **Teklif Seç** listesinde bilişsel **Hizmetler** ' in seçildiğinden emin olun.
   + **Dil** özellikleri ' nin altında, **metın ANALIZI Standard** ücretleri AI dizin oluşturma için geçerlidir.
   + **Görme** özellikleri altında, **görüntü işleme S1** için ücretler geçerlidir.

1. Yeni bilişsel hizmetler kaynağını sağlamak için **Oluştur** ' u seçin.

1. Veri Içeri aktarma Sihirbazı 'nı içeren önceki sekmeye geri dönün. Bilişsel hizmetler kaynağını göstermek için **Yenile** ' yi seçin ve ardından kaynağı seçin:

   ![Bilişsel hizmetler kaynağını seçin](./media/cognitive-search-attach-cognitive-services/attach2.png "Bilişsel hizmetler kaynağını seçin")

1. Verilerinizde çalıştırmak istediğiniz belirli bilişsel becerileri seçmek için bilişsel **yetenekler Ekle** bölümünü genişletin. Sihirbazın geri kalanını tamamlayın.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Bilişsel hizmetler kaynağına mevcut bir beceri iliştirme

Mevcut bir beceri varsa, bunu yeni veya farklı bir bilişsel hizmetler kaynağına ekleyebilirsiniz.

1. **Hizmete genel bakış** sayfasında **becerileri**' yi seçin:

   ![Becerileri sekmesi](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Becerileri sekmesi")

1. Beceri adını seçin ve ardından var olan bir kaynağı seçin veya yeni bir kaynak oluşturun. Değişikliklerinizi onaylamak için **Tamam ' ı** seçin.

   ![Beceri kaynak listesi](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Beceri kaynak listesi")

   **Ücretsiz (sınırlı enzenginler)** seçeneğinin her gün 20 belge olduğunu ve yeni bir faturalanabilir kaynak sağlamak için yeni bilişsel **Hizmetler kaynağı oluşturma** ' yı kullanacağınızı unutmayın. Yeni bir kaynak oluşturursanız, bilişsel hizmetler kaynakları listesini yenilemek için **Yenile** ' yi seçin ve ardından kaynağı seçin.

## <a name="attach-cognitive-services-programmatically"></a>Bilişsel hizmetler 'i programlı olarak ekle

Program aracılığıyla beceri tanımlarken, Beceri bölümüne bir `cognitiveServices` bölümü ekleyin. Bu bölümde, Beceri ilişkilendirmek istediğiniz bilişsel hizmetler kaynağının anahtarını ekleyin. Kaynağın Azure Bilişsel Arama kaynağınız ile aynı bölgede olması gerektiğini unutmayın. Ayrıca `@odata.type`ekleyin ve `#Microsoft.Azure.Search.CognitiveServicesByKey`olarak ayarlayın.

Aşağıdaki örnekte bu desenler gösterilmektedir. Tanımın sonundaki `cognitiveServices` bölümüne dikkat edin.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Örnek: tahmini maliyetler

Bilişsel arama dizinlemesi ile ilişkili maliyetleri tahmin etmek için, ortalama bir belgenin nasıl göründüğünü bir fikir ile başlayın, böylece bazı sayılar çalıştırabilirsiniz. Örneğin, şunları yapabilirsiniz:

+ 1\.000 PDF.
+ Altı sayfa her.
+ Sayfa başına bir görüntü (6.000 resim).
+ sayfa başına 3.000 karakter.

Her PDF 'nin belge çözme, görüntü ve metin ayıklama, optik karakter tanıma (OCR) ve kuruluşların varlık tanıma özelliğini içeren bir işlem hattı varsayın.

Bu makalede gösterilen fiyatlar kuramsal olarak gösterilmiştir. Tahmin işlemini göstermek için kullanılırlar. Maliyetleriniz daha düşük olabilir. İşlemlerin gerçek fiyatları için bkz. bilişsel [Hizmetler fiyatlandırması](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Metin ve görüntü içeriğiyle belge çözme için metin ayıklama Şu anda ücretsizdir. 6\.000 görüntü için, ayıklanan her 1.000 görüntüsü için $1 ' i varsayın. Bu adım için $6,00 maliyeti vardır.

2. OCR Bilişsel Beceri, Ingilizce olan 6.000 görüntünün OCR için en iyi algoritmayı (DescribeText) kullanır. 1\.000 görüntü başına $2,50 maliyeti varsayarak, bu adım için $15,00 ödersiniz.

3. Varlık ayıklama için, sayfa başına toplam üç metin kaydı vardır. Her kayıt 1.000 karakterdir. Sayfa başına 6.000 sayfa ile çarpılan üç metin kaydı 18.000 metin kayıtlarına eşittir. Her 1.000 metin kaydı için $2,00 varsayılıyor, bu adımda maliyet $36,00.

Tümünü bir araya getirmek için, açıklanan beceri bu türdeki 1.000 PDF belgelerini almak üzere $57,00 ile ödeme yaparsınız.

## <a name="next-steps"></a>Sonraki adımlar
+ [Azure Bilişsel Arama fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/search/)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Beceri oluşturma (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Zenginleştirilmiş alanları eşleme](cognitive-search-output-field-mapping.md)
