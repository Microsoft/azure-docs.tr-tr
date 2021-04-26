---
title: Azure Veri Kataloğu geliştirici örnekleri
description: Bu makalede, veri kataloğu REST API için kullanılabilir geliştirici örneklerine genel bir bakış sunulmaktadır.
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 15b48bc41e230ca5b9003675e2caab25741bcbfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674776"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure Veri Kataloğu geliştirici örnekleri

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Veri Kataloğu REST API kullanarak Azure Veri Kataloğu uygulamaları geliştirmeye başlayın. Veri Kataloğu REST API, veri varlıklarını programlı bir şekilde kaydetmek, bunlara açıklama eklemek ve aramak için veri kataloğu kaynaklarına programlı erişim sağlayan REST tabanlı bir API 'dir.

## <a name="samples-available-on-githubcom"></a>GitHub.com üzerinde kullanılabilir örnekler

* [Azure Veri Kataloğu ile çalışmaya başlama](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   Kullanmaya başlama örneği, veri kataloğu REST API kullanarak bir veri varlığını kaydetmek, aramak ve silmek için Azure AD ile nasıl kimlik doğrulaması yapılacağını gösterir.
   
* [Hizmet sorumlusu kullanarak Azure Veri Kataloğu ile çalışmaya başlama](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   Bu örnekte, veri kataloğu REST API kullanarak bir veri varlığını kaydetme, arama ve silme işlemlerinin nasıl yapılacağı gösterilmektedir. Bu örnek, hizmet sorumlusu kimlik doğrulamasını kullanır.

* [Azure Veri Kataloğu için içeri/dışarı aktarma aracı](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   Bu örnek, Azure Veri Kataloğu 'ndan varlıkları getirmek ve bunları bir dosyaya seri hale getirmek için veri kataloğu REST API nasıl kullanacağınızı gösterir. Ayrıca, JSON olarak serileştirilmiş bir varlık kümesinin nasıl alınacağını ve bunları kataloğa nasıl göndereceğinizi gösterir. Bir arama sorgusu kullanarak kataloğun bir alt kümesini dışarı aktarmayı destekler.

* [Azure Veri Kataloğu 'nda toplu kayıt ve not ekleme](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   Veri Kataloğu REST API ve XML 'yi kullanarak bir Excel çalışma kitabından veri varlıklarını toplu olarak nasıl kaydedekullanacağınızı gösteren bu örnek.
  
* [Sözlük koşullarını Azure Veri Kataloğu 'na toplu olarak içeri aktarma](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   Bu örnekte, CSV dosyalarından ADC sözlüğü 'ne sözlük koşullarının nasıl içeri aktarılacağı gösterilmektedir.

* [Azure Veri Kataloğu 'na toplu içeri aktarma ilişkileri](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   Bu örnek, bir CSV dosyasındaki ilişki bilgilerini bir veri kataloğuna programlı bir şekilde nasıl içeri aktarakullanacağınızı gösterir.

* [Azure Veri Kataloğu 'nda ilişkileri yayımlama](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   Bu örnek, bir veri kataloğuna programlı olarak ilişki bilgilerini nasıl yayımlayacağınızı gösterir.
   
## <a name="next-steps"></a>Sonraki adımlar
[Azure Veri Kataloğu REST API başvurusu](/rest/api/datacatalog/)
