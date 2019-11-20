---
title: Azure portal arama dizini oluşturma
titleSuffix: Azure Cognitive Search
description: Yerleşik bir portal Dizin Tasarımcısı kullanarak Azure Bilişsel Arama için dizin oluşturmayı öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112851"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Portalda Azure Bilişsel Arama dizini oluşturma

Azure Bilişsel Arama, Portal 'daki yerleşik bir dizin tasarımcısını, Prototiplerde faydalı olan veya Azure Bilişsel Arama hizmetinizde barındırılan bir [arama dizini](search-what-is-an-index.md) oluşturmak için içerir. Araç, şema oluşturma için kullanılır. Tanımı kaydettiğinizde, Azure Bilişsel Arama 'de boş bir dizin tam olarak ifade edilir. Aranabilir içerik ile nasıl yüklenir?

Dizin Tasarımcısı dizin oluşturmak için yalnızca bir yaklaşımdır. Alternatif olarak, [verileri Içeri aktarma sihirbazını](search-get-started-portal.md)kullanarak bir dizin oluşturup yükleyebilirsiniz. Sihirbaz yalnızca kendi oluşturduğu dizinlerde çalışmaktadır. Programlama yoluyla, [.net](search-create-index-dotnet.md) veya [rest](search-create-index-rest-api.md) API 'lerini kullanarak bir dizin oluşturabilirsiniz.

## <a name="start-index-designer"></a>Dizin tasarımcısını Başlat

1. [Azure portalında](https://portal.azure.com) oturum açın ve hizmet panosunu açın. Atlama çubuğundaki **Tüm hizmetler**’e tıklayarak geçerli abonelikteki mevcut "arama hizmetleri" için arama yapabilirsiniz. 

2. Sayfanın en üstündeki komut çubuğunda **Dizin Ekle** bağlantısına tıklayın.

   ![Komut çubuğunda Dizin bağlantısı ekle](media/search-create-index-portal/add-index.png "Komut çubuğunda Dizin bağlantısı ekle")

3. Azure Bilişsel Arama dizininizi adlandırın. Dizin adlarına dizin oluşturma ve sorgu işlemlerinde başvurulur. Dizin adı, dizine bağlantılarda ve Azure Bilişsel Arama REST API HTTP istekleri göndermek için kullanılan uç nokta URL 'sinin bir parçası haline gelir.

   * En başta bir harf kullanın.
   * Yalnızca küçük harfleri, rakamları veya kısa çizgileri ("-") kullanın.
   * Adı 60 karakterle sınırlayın.

## <a name="add-fields"></a>Alan Ekle

Dizin oluşturma, dizininizdeki aranabilir verileri tanımlayan bir *Alanlar koleksiyonunu* içerir. Tamamen, alanlar koleksiyonu ayrı olarak karşıya yüklediğiniz belgelerin yapısını belirler. Alanlar koleksiyonu, alanın nasıl kullanılabileceğini belirleyecek dizin öznitelikleri ile adlandırılmış ve yazılan isteğe bağlı alanları içerir.

1. Karşıya yükleyeceğiniz belgeleri tam olarak belirtmek için alanlar ekleyin, her biri için bir [veri türü](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) belirleyin. Örneğin, belgeler bir *otel kimliği*, *otel adı*, *Adres*, *şehir*ve *bölge*içeriyorsa, dizindeki her biri için karşılık gelen bir alan oluşturun. Öznitelikleri ayarlamayla ilgili yardım için [aşağıdaki bölümde yer alarak bulunan tasarım kılavuzunu](#design) gözden geçirin.

1. Gelen veriler doğası halinde hiyerarşik ise, şemanız iç içe yapıları temsil etmek için [karmaşık türler](search-howto-complex-data-types.md) içermelidir. Yerleşik örnek veri kümesi olan oteller, her bir otelle bire bir ilişkiye sahip olan ve her bir otel ile birden çok oda ilişkilendirildiği bir oda karmaşık koleksiyonu olan bir adres (birden çok alt alan içerir) kullanan karmaşık türleri gösterir. 

1. EDM. String türünde bir *anahtar* alanı belirtin. Her Azure Bilişsel Arama dizini için bir anahtar alanı zorunludur ve bir dize olmalıdır. Bu alanın değerleri her belgeyi benzersiz şekilde tanımlamalıdır. Varsayılan olarak alan, *id* olarak adlandırılır, ancak [adlandırma kurallarına](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) uygun şekilde alanı yeniden adlandırabilirsiniz. Örneğin, alanlar koleksiyonunuz *otel kimliği*içeriyorsa, anahtarınız için bunu seçersiniz. 

1. Her bir alanda öznitelikleri ayarlayın. Dizin Tasarımcısı, veri türü için geçersiz olan tüm öznitelikleri dışlar, ancak nelerin ekleneceğini tavsiye etmez. Özniteliklerin ne için olduğunu anlamak için sonraki bölümde yer alarak bulunan Kılavuzu gözden geçirin.

    Azure Bilişsel Arama API belgeleri, basit bir *oteller* dizini içeren kod örnekleri içerir. Aşağıdaki ekran görüntüsünde, Dizin tanımı sırasında belirtilen Fransızca dil Çözümleyicisi de dahil olmak üzere dizin tanımını görebilirsiniz. Bu, portalda alıştırma olarak yeniden oluşturabilirsiniz.

    ![Oteller tanıtım dizini](media/search-create-index-portal/field-definitions.png "Oteller tanıtım dizini")

1. İşiniz bittiğinde, dizini kaydetmek ve oluşturmak için **Oluştur** ' a tıklayın.

<a name="design"></a>

## <a name="set-attributes"></a>Öznitelikleri ayarla

İstediğiniz zaman yeni alanlar ekleyebilseniz de, dizinin ömrü boyunca mevcut alan tanımları kilitlenir. Bu nedenle geliştiriciler genellikle basit dizinler oluşturmak, fikirleri test etmek veya portal sayfalarını kullanarak bir ayarı bulmak için portalı kullanır. Dizini kolayca yeniden derleyebilmeniz için kod tabanlı bir yaklaşım izlerseniz, bir dizin tasarımı çerçevesinde sık sık yapılan yineleme daha verimli olur.

Dizin kaydedilmeden önce çözümleyiciler ve öneri araçları alanlarla ilişkilendirilir. Siz oluştururken Dizin tanımınıza dil Çözümleyicileri veya öneri araçları eklediğinizden emin olun.

Dize alanları genellikle **Aranabilir** ve **Alınabilir** olarak işaretlenir. Arama sonuçlarını daraltmak için kullanılan alanlar arasında **Sıralanabilir**, **Filtrelenebilir** ve **Modellenebilir** alanları yer alır.

Alan öznitelikleri, bir alanın nasıl kullanıldığını; örneğin, tam metin alanında mı, çok yönlü gezinmede mi, sıralama işlemlerinde vb.’de mi kullanılıp kullanılmayacağı belirler. Aşağıdaki tabloda her bir öznitelik açıklanmaktadır.

|Öznitelik|Açıklama|  
|---------------|-----------------|  
|**aranabilir**|Tam metin aranabilir, dizin oluşturma sırasında sözcüklere bölme gibi sözcük temelli analize tabidir. Aranabilir bir alanı, "güneşli gün" gibi bir değere ayarlarsanız, dahili olarak bu "güneşli" ve "gün" belirteçlerine bölünür. Ayrıntılar için bkz. [Tam metin araması nasıl çalışır?](search-lucene-query-architecture.md)|  
|**filtrelenebilir**|**$filter** sorgularında başvurulur. `Edm.String` veya `Collection(Edm.String)` türünde filtrelenebilir alanlara sözcüklere bölme işlemi uygulanmaz, bu nedenle karşılaştırmalar yalnızca tam eşleşmeler içindir. Örneğin, böyle bir alanı "güneşli gün" olarak ayarlarsanız `$filter=f eq 'sunny'` herhangi bir eşleşme bulmaz, ancak `$filter=f eq 'sunny day'` bulur. |  
|**sıralanabilir**|Varsayılan olarak sistem sonuçları puana göre sıralar, ancak belgelerdeki alanlara göre sıralamayı yapılandırabilirsiniz. `Collection(Edm.String)` türünde alanlar **sıralanabilir** olamaz. |  
|**modellenebilir**|Genellikle kategoriye göre (örneğin, belirli bir şehirdeki oteller) isabet sayısını içeren bir arama sonuçları sunumunda kullanılır. Bu seçenek, `Edm.GeographyPoint` türünde alanlarla kullanılamaz. `Edm.String`Filtrelenebilir **,** sıralanabilir**veya**modellenebilir**olan** türünde alanlar en fazla 32 kilobayt uzunluğunda olabilir. Ayrıntılar için bkz. [Dizin Oluşturma (REST API’si)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**anahtar**|Dizin içindeki belgeler için benzersiz tanımlayıcı. Anahtar alan olarak tam olarak bir alan seçilmeli ve `Edm.String` türünde olmalıdır.|  
|**alınabilir**|Alanın bir arama sonucunda döndürülüp döndürülemeyeceğini belirler. Filtre, sıralama veya puanlama mekanizması olarak bir alanı (örn. *kâr marjı*) kullanmak istediğinizde, ancak alanın son kullanıcıya görünür olmasını istemediğinizde bu faydalıdır. Bu öznitelik, `true` alanları için `key` olmalıdır.|  

## <a name="next-steps"></a>Sonraki adımlar

Bir Azure Bilişsel Arama dizini oluşturduktan sonra bir sonraki adıma geçebilirsiniz: [aranabilir verileri dizine yükleyin](search-what-is-data-import.md).

Alternatif olarak, [dizinlere daha ayrıntılı bir bakış](search-what-is-an-index.md)da alabilirsiniz. Alanlar koleksiyonuna ek olarak bir dizin, çözümleyicileri, öneri araçlarını, puanlama profillerini ve CORS ayarlarını da belirtir. Portal, en yaygın öğeleri tanımlamak için sekmeli sayfalar sağlar: Alanlar, çözümleyiciler ve öneri araçları. Başka öğeler oluşturmak veya değiştirmek için REST API’sini veya .NET SDK’sını kullanabilirsiniz.

## <a name="see-also"></a>Ayrıca bkz.

 [Tam metin araması nasıl çalışır?](search-lucene-query-architecture.md)  
 [Arama hizmeti REST API’si](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK’sı](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

