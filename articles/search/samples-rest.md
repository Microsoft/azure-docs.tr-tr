---
title: REST örnekleri
titleSuffix: Azure Cognitive Search
description: Arama veya yönetim REST API 'Lerini kullanan Azure Bilişsel Arama demo REST kod örneklerini bulun.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98956585"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Azure Bilişsel Arama için REST kodu örnekleri

Azure Bilişsel Arama çözümünün işlevselliğini ve iş akışını gösteren REST API örnekleri hakkında bilgi edinin. Bu örnekler [**arama REST API 'lerini**](/rest/api/searchservice)kullanır.

REST, Azure Bilişsel Arama için tanımlayıcı programlama arabirimidir ve program aracılığıyla çağrılabilen tüm işlemler, ilk olarak REST 'te ve ardından SDK 'larda kullanılabilir. Bu nedenle belgelerde çoğu örnek, önemli kavramları göstermek veya açıklamak için REST API 'Lerinden yararlanır.

REST örnekleri genellikle Postman 'da geliştirilir ve test edilmiştir, ancak HTTP çağrılarını destekleyen herhangi bir istemciyi kullanabilirsiniz:

+ Hızlı Başlangıç [: http çağrılarını formüllemek IÇIN REST API 'lerini kullanarak bir Azure bilişsel arama dizini oluşturun](search-get-started-rest.md) .
+ Visual Studio Code çalışıyorsanız, şu anda önizleme aşamasında olan [Azure bilişsel arama için Visual Studio Code uzantısını](search-get-started-vs-code.md)deneyin.

## <a name="doc-samples"></a>Belge örnekleri

Bilişsel Arama ekibinin kod örnekleri, özellikleri ve iş akışlarını gösterir. Bu örneklerin çoğuna öğreticiler, hızlı başlangıçler ve nasıl yapılır makalelerinde başvurulur. Bu örnekleri, GitHub 'daki [**Azure-Samples/Azure-Search-Postman-Samples**](https://github.com/Azure-Samples/azure-search-postman-samples) ' da bulabilirsiniz.

| Örnekler | Makale |
|---------|---------|
| [Hızlı Başlangıç](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Hızlı başlangıç için kaynak kodu [: REST API 'leri kullanarak bir arama dizini oluşturun](search-get-started-rest.md). Bu makale, örnek verileri kullanarak bir arama dizini oluşturma, yükleme ve sorgulama için temel iş akışını ele almaktadır. |
| [Öğretici](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Öğretici için kaynak kodu [: Azure Bloblarından aranabilir içerik oluşturmak IÇIN REST ve AI kullanın](cognitive-search-tutorial-blob.md). Bu makalede, bilgileri ayıklamak ve yapıyı çıkarsmak için Azure Blob 'ları üzerinden yineleyen bir beceri nasıl oluşturacağınız gösterilmektedir.|
| [Hata ayıklama-oturumlar](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Öğretici için kaynak kodu [: beceri uygulamanızda yapılan değişiklikleri tanılayın, onarın ve işleyin](cognitive-search-tutorial-debug-sessions.md). Bu makalede, Azure portal bir beceri hata ayıklama oturumunun nasıl kullanılacağı gösterilmektedir. REST, hata ayıklama sırasında kullanılan nesneleri oluşturmak için kullanılır.|
| [özel çözümleyiciler](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Öğretici için kaynak kodu [: telefon numaraları için özel bir çözümleyici oluşturun](tutorial-create-custom-analyzer.md). Bu makalede, Aranabilir içerikte desenleri ve özel karakterleri korumak için çözümleyicilerin nasıl kullanılacağı açıklanmaktadır.|
| [bilgi deposu](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | [Rest ve Postman kullanarak bilgi deposu oluşturmak](knowledge-store-create-rest.md)için kaynak kodu. Bu makalede, bilgi madenciliği iş akışları için kullanılan bir bilgi deposunu doldurmak için gereken adımlar açıklanmaktadır. |
| [malar](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | [Zenginleştirmelerin nasıl Şekillendirgörüntüleneceği ve dışarı aktarılacağı](knowledge-store-projections-examples.md)kaynak kodu. Bu makalede, bir bilgi deposundaki fiziksel veri yapılarının nasıl belirleneceği açıklanır.|
| [Dizin şifreli-blob 'lar](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | [BLOB Dizinleyicileri ve becerileri kullanarak şifrelenmiş Blobların nasıl dizinleneceğini gösteren](search-howto-index-encrypted-blobs.md)kaynak kodu. Bu makalede, Azure Blob depolamada daha önce Azure Key Vault kullanılarak şifrelenmiş belgelerin nasıl dizinleneceğini gösterilmektedir. |

> [!Tip]
> GitHub 'da ürün, hizmet ve dile göre filtrelenmiş Microsoft kod örneklerini aramak için [örnekler tarayıcısını](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) deneyin.

## <a name="other-samples"></a>Diğer örnekler

Aşağıdaki örnekler de Bilişsel Arama ekibi tarafından yayımlanır, Ancak belgelerde başvurulmaz. İlişkili Benioku dosyaları Kullanım yönergeleri sağlar.

| Örnekler | Description |
|---------|-------------|
| [Sorgu-örnekler](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Benzer arama, RegEx ve joker karakter arama, otomatik tamamlama gibi çeşitli sorgu tekniklerini gösteren Postman koleksiyonları. |