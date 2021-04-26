---
title: Microsoft Azure haritalardaki Mobility Hizmetleri (Önizleme) veri yapıları
description: Azure haritalar Mobility hizmetlerindeki (Önizleme) verilerin Metro alanlarında nasıl düzenlendiğini anlayın. Bkz. genel transit hakkında bilgileri depolayan alanları ve çizgileri.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904729"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Azure haritalar Mobility hizmetlerindeki veri yapıları (Önizleme) 

> [!IMPORTANT]
> Azure haritalar Mobility Hizmetleri şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Bu makalede, [Azure Maps Mobility hizmetlerinde](/rest/api/maps/mobility)Metro alanı kavramı tanıtılmaktadır. Bu hizmet, genel aktarım duraklarına ve çizgilere yönelik olarak sorgulandığında döndürülen bazı yaygın alanları tartıştık. Mobility Hizmetleri API 'Leri ile geliştirmeye başlamadan önce bu makaleyi okumanızı öneririz.

## <a name="metro-area"></a>Metro alanı

Mobility Services (Önizleme) verileri, desteklenen Metro alanlarıyla gruplandırılır. Metro alanlarında şehir sınırları izdeğildir. Bir metro alanı birden çok şehir, daha seyrek doldurulmuş şehir ve çevreleyen şehirler içerebilir. Aslında bir ülke/bölge bir metro alanı olabilir. 

, `metroID` [Metro alanı BILGILERINI al API](/rest/api/maps/mobility/getmetroareainfopreview)'sini çağırmak için kullanılabilen BIR Metro alanı kimliğidir. Aktarım türlerini, transit kuruluşlarını, etkin uyarıları ve seçilen Metro için ek ayrıntıları istemek üzere Azure Maps ' "Metro 'yı al" API 'sini kullanın. Ayrıca, desteklenen Metro alanını ve metroIDs isteyebilirsiniz. Metro alanı kimlikleri değişikliğe tabidir.

**metroID:** 522   **ad:** Seattle-Tacoma-Bellevue

![Seattle-Metro alanı](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Kimliği durdur

Aktarım duraklarına iki tür kimlik, [genel transit akış belirtimi (GFTS)](http://gtfs.org/) kimliği ve Azure HARITALAR durdurma kimliği tarafından başvurulabilir. GFTS ID, stopKey olarak adlandırılır ve Azure Maps durdurma KIMLIĞI, stopID olarak adlandırılır. Aktarım için sık sık yanıt durdurulduğunda, Azure haritalar durdurma KIMLIĞINI kullanmanız önerilir. stopID daha kararlı ve büyük ihtimalle fiziksel durun var olduğu sürece aynı kalabiliyor. GTFS durdurma KIMLIĞI daha sık güncelleştirilir. Örneğin, gtfs durdurma KIMLIĞI, GTFS sağlayıcı isteği başına ya da yeni bir GTFS sürümü yayınlandığında güncelleştirilebilen olabilir. Fiziksel durda değişikliğe sahip olmasa da, GTFS durdurma KIMLIĞI değişebilir.

Başlamak için yakında ulaşım [API 'Sini al](/rest/api/maps/mobility/getnearbytransitpreview)' ı kullanarak yakındaki geçiş işlemini izleyebilirsiniz.

## <a name="line-groups-and-lines"></a>Satır grupları ve satırlar

Mobility Hizmetleri (Önizleme) çizgiler ve satır grupları için bir paralel veri modeli kullanır. Bu model, [Gtfs](http://gtfs.org/) rotalarından ve gezme verilerinden devralınan değişikliklerle daha iyi uğraşmak için kullanılır.


### <a name="line-groups"></a>Satır grupları

Bir satır grubu, aynı grubun mantıksal bir parçası olan tüm satırları gruplandıran bir varlıktır. Genellikle, bir satır grubu iki satır içerir, biri A 'dan B 'ye ve diğeri B 'den A 'ya döndürülüyor. Her iki satır da aynı ortak taşıma ajanına ait ve aynı satır numarasına sahip olur. Ancak, bir satır grubunun iki satırı veya yalnızca tek bir satırı içerdiği durumlar olabilir.


### <a name="lines"></a>Satırlar

Yukarıda açıklandığı gibi, her satır grubu bir dizi satırdan oluşur. Her satır grubu iki satırdan oluşur ve her satır bir yönü açıklar.  Ancak, daha fazla satır bir satır grubu oluşturan durumlar vardır. Örneğin, bazen belirli bir komşuları içinde Gezinmeyen ve bazen olmayan bir çizgi vardır. Her iki durumda da, aynı satır numarası altında çalışır. Ayrıca, bir satır grubu tek bir satırdan oluşabilir. Tek yönlü bir dairesel çizgi, tek satırlık bir daire grubudur.

Başlamak için, [geçiş satırı al API](/rest/api/maps/mobility/gettransitlineinfopreview)'sini kullanarak satır grupları isteyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Mobility hizmetlerini (Önizleme) kullanarak geçiş verileri isteme hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Geçiş verileri isteme](how-to-request-transit-data.md)

Mobility hizmetlerini (Önizleme) kullanarak gerçek zamanlı veriler isteme hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Gerçek zamanlı veriler isteme](how-to-request-real-time-data.md)

Azure haritalar Mobility Hizmetleri (Önizleme) API belgelerini inceleyin

> [!div class="nextstepaction"]
> [Mobility Hizmetleri API 'SI belgeleri](/rest/api/maps/mobility)