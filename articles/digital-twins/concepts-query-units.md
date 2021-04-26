---
title: Azure dijital TWINS 'de sorgu birimleri
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS 'te sorgu birimlerinin faturalandırma kavramını anlayın
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0e1c5f08c4292e4f3dfec448d8bf54d5d5601840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99050507"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure dijital TWINS 'de sorgu birimleri 

Azure dijital TWINS **sorgu birimi (QU)** , [sorgu API](/rest/api/digital-twins/dataplane/query)'Sini kullanarak [Azure dijital TWINS sorgularınızı](how-to-query-graph.md) yürütmek için kullanılan isteğe bağlı bir hesaplama birimidir. 

Azure dijital TWINS tarafından desteklenen sorgu işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlayıp bunun yerine sorgu birimlerindeki kullanımı izlemenize olanak sağlar.

Bir sorguyu yürütmek için tüketilen sorgu birimlerinin miktarı bundan etkilenir...

* sorgunun karmaşıklığı
* sonuç kümesinin boyutu (yani 10 sonuç döndüren bir sorgu, tek bir sonuç döndüren benzer karmaşıklık sorgusunun bir sorgusundan daha fazla QUs kullanır)

Bu makalede sorgu birimlerinin nasıl anlaşılması ve sorgu birimi tüketiminin izlenmesi açıklanmaktadır.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Azure dijital TWINS 'te sorgu birimi tüketimini bulma

Azure Digital TWINS [sorgu API](/rest/api/digital-twins/dataplane/query)'sini kullanarak bir sorgu çalıştırdığınızda, sorgunun tükettiği Qus sayısını izlemek için yanıt üst bilgisini inceleyebilirsiniz. Azure dijital TWINS 'den geri gönderilen yanıtta "sorgu ücreti" ni arayın.

Azure dijital TWINS [SDK 'ları](how-to-use-apis-sdks.md) , disk belleğine alınabilen yanıttan sorgu ücreti üst bilgisini ayıklamanızı sağlar. Bu bölümde, dijital TWINS sorgusunun nasıl sorgulanacağını ve sorgu ücreti üst bilgisini ayıklamak için disk belleğine alınabilen yanıtın nasıl yinelendirilecek gösterilmektedir. 

Aşağıdaki kod parçacığı, sorgu API 'SI çağrılırken oluşan sorgu ücretlerini nasıl ayıklayabileceğinizi gösterir. Sorgu ücreti başlığına erişmek için öncelikle yanıt sayfaları üzerinde dolaşır ve sonra her sayfa içindeki dijital ikizi sonuçlarının üzerinde dolaşır. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Sonraki adımlar

Azure dijital TWINS sorgulama hakkında daha fazla bilgi edinmek için şu adresi ziyaret edin:

* [*Kavramlar: sorgu dili*](concepts-query-language.md)
* [*Nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md)
* [Sorgu API 'SI başvuru belgeleri](/rest/api/digital-twins/dataplane/query/querytwins)

Azure Digital TWINS sorgu ile ilgili sınırları [*Azure dijital TWINS hizmet limitleriyle*](reference-service-limits.md)bulabilirsiniz.