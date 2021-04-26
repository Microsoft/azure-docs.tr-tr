---
title: Azure portal bir demo uygulaması oluşturma
titleSuffix: Azure Cognitive Search
description: İşletimsel bir Web uygulaması için HTML sayfaları ve betiği oluşturmak üzere demo uygulaması oluşturma (Önizleme) sihirbazını çalıştırın. Sayfa bir arama çubuğu, sonuç alanı, kenar çubuğu ve typeahead desteği içerir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745759"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Hızlı başlangıç: portalda bir demo uygulaması oluşturma (Azure Bilişsel Arama)

Bir tarayıcıda çalışan indirilebilir, "localhost" stilinde bir Web uygulaması oluşturmak için Azure portal **tanıtım uygulaması oluşturma** Sihirbazı 'nı kullanın. Yapılandırmaya bağlı olarak, oluşturulan uygulama, uzak bir dizine yönelik canlı bir salt okuma bağlantısı ile ilk kullanımda çalışır. Varsayılan bir uygulama bir arama çubuğu, sonuç alanı, kenar çubuğu filtreleri ve typeahead desteği içerebilir.

Demo uygulaması, bir dizinin bir istemci uygulamasında nasıl çalıştığını görselleştirmenize yardımcı olabilir, ancak üretim senaryoları için tasarlanmamıştır. İstemci uygulamaları, oluşturulan HTML sayfasının sağlamadığı güvenlik, hata işleme ve barındırma mantığını içermelidir. Bir istemci uygulaması oluşturmaya hazırsanız, sonraki adımlar için [.NET SDK kullanarak ilk arama uygulamanızı oluşturma](tutorial-csharp-create-first-app.md) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

+ Bir Azure Bilişsel Arama hizmeti. Geçerli aboneliğiniz kapsamında [bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

+ [Microsoft Edge (en son sürüm)](https://www.microsoft.com/edge) veya Google Chrome.

+ Oluşturulan uygulamanızın temeli olarak kullanılacak bir [arama dizini](search-what-is-an-index.md) . 

  Bu hızlı başlangıç, küçük resimler içerdiğinden (sihirbaz sonuçlar sayfasına görüntü eklemeyi desteklediğinden) yerleşik gerçek örnek verileri ve dizini kullanır. Bu alıştırmada kullanılan dizini oluşturmak için, **veri alma** Sihirbazı ' nı çalıştırarak *reatastate-US-Sample* veri kaynağını seçin.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="örnek veriler için veri kaynağı sayfası" border="false":::

Dizin kullanıma hazırsa, sonraki adıma geçin.

## <a name="start-the-wizard"></a>Sihirbazı Başlat

1. Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Arama hizmetinizi bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) ve genel bakış sayfasında, sayfanın ortasındaki bağlantılardan **dizinler**' i seçin. 

1. Var olan dizinler listesinden *reatastate-US-Sample-Index* öğesini seçin.

1. Sihirbazı başlatmak için, üstteki Dizin sayfasında, **tanıtım uygulaması oluştur (Önizleme)** seçeneğini belirleyin.

1. İlk sihirbaz sayfasında, Dizin tanımınıza CORS desteği eklemek için **çapraz kaynak kaynak paylaşımını (CORS) etkinleştir** ' i seçin. Bu adım isteğe bağlıdır, ancak yerel Web uygulamanız bu olmadan uzak dizine bağlanmaz.

## <a name="configure-search-results"></a>Arama sonuçlarını yapılandırma

Sihirbaz, bir küçük resim görüntüsü, başlık ve açıklama için boşluk içeren işlenmiş arama sonuçları için temel bir düzen sağlar. Bu öğelerin her birini yedeklemek, dizininizdeki verileri sağlayan bir alandır. 

1. Küçük resim bölümünde *reatastate-US-Sample* dizinindeki *küçük resim* alanını seçin. Bu örnek, resim küçük resimlerini, *küçük resim* adlı bir alanda depolanan url ile adreslenen görüntüler biçiminde eklemek için oluşur. Dizininizdeki görüntü yoksa, bu alanı boş bırakın.

1. Başlık bölümünde her bir belgenin benzersizlik alanını ileten bir alan seçin. Bu örnekte, liste KIMLIĞI makul bir seçimdir.

1. Açıklama ' da, birisinin belirli bir belgeye tıklamaya karar vermesine yardımcı olabilecek ayrıntılar sağlayan bir alan seçin.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="örnek veriler için sonuçları yapılandırma" border="false":::

## <a name="add-a-sidebar"></a>Kenar çubuğu ekle

Arama hizmeti, genellikle bir kenar çubuğu olarak işlenen çok yönlü gezintiyi destekler. Modeller, dizin şemasında gösterildiği gibi filtrelenebilir ve çok yönlü tablo alanlarını temel alır.

Azure Bilişsel Arama 'de, çok yönlü gezinme bir toplu filtreleme deneyimidir. Bir kategori içinde birden çok filtre seçildiğinde sonuçlar genişletilir (örneğin, şehir içinde Seattle ve Bellevue ' i seçin). Kategoriler arasında birden çok filtre seçildiğinde sonuçlar daraltır.

> [!TIP]
> Tam dizin şemasını portalda görüntüleyebilirsiniz. Her dizinin Genel Bakış sayfasındaki **Dizin tanımı (JSON)** bağlantısını bulun. Çok yönlü gezintiye uygun olan alanlarda "filtrelenebilir: true" ve "çok yönlü tablo: true" öznitelikleri vardır.

1. Sihirbazda, sayfanın üst kısmındaki **kenar çubuğu** sekmesini seçin. Dizinde filtrelenebilir ve çok yönlü tablo olarak öznitelikli tüm alanların listesini görürsünüz.

1. Çok yönlü olan alanların geçerli seçimini kabul edin ve sonraki sayfaya devam edin.

## <a name="add-typeahead"></a>Typeahead Ekle

Typeahead işlevselliği otomatik tamamlama ve sorgu önerileri biçiminde kullanılabilir. Sihirbaz sorgu önerilerini destekler. Kullanıcı tarafından sunulan tuş vuruşu girişlerini temel alarak arama hizmeti, giriş olarak seçilebilirler "tamamlanan" sorgu dizelerinin listesini döndürür.

Öneriler, belirli alan tanımlarında etkinleştirilir. Sihirbaz, bir önerisine Ne kadar bilgi ekleneceğini yapılandırmaya yönelik seçenekler sağlar. 

Aşağıdaki ekran görüntüsünde, sihirbazda bulunan işlenmiş bir sayfayla birlikte, sihirbazdaki seçenekler gösterilmektedir. Alan seçimlerinin nasıl kullanıldığını ve öneri içindeki etiketlemeyi dahil etmek veya hariç tutmak için "alan adını göster" seçeneğini görebilirsiniz.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Sorgu önerisi yapılandırması":::

## <a name="add-suggestions"></a>Öneri ekleme

Öneriler, arama kutusuna eklenen otomatikleştirilmiş sorgu istemlerine başvurur. Bilişsel Arama, kısmen girilen bir arama teriminin *Otomatik tamamlamayı* destekler ve olası eşleşen belgelerin bir açılan listesi için *öneriler* .

Sihirbaz önerileri destekler ve önerilen sonuçlar sağlayabilen alanlar, dizindeki bir yapıdan türetilir [`Suggesters`](index-add-suggesters.md) :

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. Sihirbazda, sayfanın üst kısmındaki **öneriler** sekmesini seçin. Öneri sağlayıcıları olarak dizin şemasında belirtilen tüm alanların listesini görürsünüz.

1. Geçerli seçimi kabul edin ve sonraki sayfaya devam edin.

## <a name="create-download-and-execute"></a>Oluşturma, indirme ve yürütme

1. HTML dosyasını oluşturmak için sayfanın alt kısmındaki **demo uygulaması oluştur** ' u seçin.

1. İstendiğinde, dosyayı indirmek için **uygulamanızı indir** ' i seçin.

1. Dosyayı açın ve Ara düğmesine tıklayın. Bu eylem, `*` rastgele bir sonuç kümesi döndüren boş bir sorgu () olabilen bir sorguyu yürütür. Sayfa aşağıdaki ekran görüntüsüne benzer görünmelidir. Bir terim girin ve sonuçları daraltmak için filtreleri kullanın. 

Temel alınan dizin, belgeler arasında çoğaltılan kurgusal, oluşturulan verilerden oluşur ve açıklamalar bazen görüntüyle eşleşmez. Kendi dizinlerinizi temel alan bir uygulama oluştururken daha açık bir deneyim de bekleyebilir.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="Uygulamayı çalıştırma":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Demo uygulaması, prototip veya ön uç kodu yazmak zorunda kalmadan bir son kullanıcı deneyiminin benzetimini yapmanıza olanak tanıdığından, prototip yazma için faydalıdır. Ön uç özellikleri hakkında daha fazla bilgi için, çok yönlü gezinme ile başlayın:

> [!div class="nextstepaction"]
> [Model filtresi oluşturma](search-filters-facets.md)