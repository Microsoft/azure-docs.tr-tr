---
title: Depolama Gezgini ile bilgi deposunu (Önizleme) görüntüleme
titleSuffix: Azure Cognitive Search
description: Azure portal Depolama Gezgini bir Azure Bilişsel Arama bilgi deposu görüntüleyin ve çözümleyin. bilgi deposu Şu anda genel önizleme aşamasındadır.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3ea879a419aa14d3a6693e23f4f120aca8d9d51
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720057"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Depolama Gezgini bir bilgi deposu görüntüleme

> [!IMPORTANT] 
> bilgi deposu Şu anda genel önizleme aşamasındadır. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) , Önizleme özellikleri sağlar. Şu anda sınırlı sayıda portal desteği var ve .NET SDK desteği yok.

Bu makalede, Azure portal Depolama Gezgini kullanarak bir bilgi deposuna nasıl bağlanacağınızı ve keşfedeceğinizi öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar

+ Bu kılavuzda kullanılan örnek bilgi deposunu oluşturmak için, [Azure Portal 'de bilgi deposu oluşturma](knowledge-store-create-portal.md) veya [rest kullanarak bir Azure bilişsel arama bilgi deposu oluşturma](knowledge-store-create-rest.md) bölümündeki adımları izleyin.

+ Ayrıca, bilgi deposunu oluşturmak için kullandığınız Azure depolama hesabının adına, Azure portal erişim anahtarıyla birlikte ihtiyacınız olacaktır.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Depolama Gezgini bir bilgi deposunu görüntüleyin, düzenleyin ve sorgulayın

1. Azure portal, bilgi deposunu oluşturmak için kullandığınız [Depolama hesabını açın](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) .

1. Depolama hesabının sol gezinti bölmesinde **Depolama Gezgini**' ye tıklayın.

1. Otelinizde **veri alma** Sihirbazı 'nı çalıştırdığınızda oluşturulan Azure Tablo projeksiyonlarını bir liste olarak görmek için **Tablolar** listesini genişletin, örnek verileri gözden geçirir.

Anahtar tümceleri yaklaşım puanları, enlem ve Boylam konum verileri ve daha fazlası dahil olmak üzere zenginleştirilmiş verileri görüntülemek için herhangi bir tablo seçin.

   ![Depolama Gezgini tabloları görüntüleme](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Depolama Gezgini tabloları görüntüleme")

Herhangi bir tablo değeri için veri türünü değiştirmek veya tablonuzdaki tek tek değerleri değiştirmek için, **Düzenle**' ye tıklayın. Bir tablo satırındaki herhangi bir sütunun veri türünü değiştirdiğinizde, tüm satırlara uygulanır.

   ![Depolama Gezgini tabloyu Düzenle](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Depolama Gezgini tabloyu Düzenle")

Sorguları çalıştırmak için Komut çubuğundaki **sorgu** ' ya tıklayın ve koşullarınızı girin.  

   ![Depolama Gezgini sorgu tablosu](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Depolama Gezgini sorgu tablosu")

## <a name="clean-up"></a>Temizleme

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha derin analizler için bu bilgi deposunu Power BI bağlayın veya farklı bir bilgi deposu oluşturmak için REST API ve Postman kullanarak kodla ilerle ilerleyin.

> [!div class="nextstepaction"]
> [Power BI Ile bağlanma](knowledge-store-connect-power-bi.md)
> [rest 'Te bilgi deposu oluşturma](knowledge-store-howto.md)
