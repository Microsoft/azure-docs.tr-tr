---
title: Sorgu dili
titleSuffix: Azure Digital Twins
description: Azure Digital TWINS sorgu dilinin temellerini anlayın.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490985"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure dijital TWINS için sorgu dili hakkında

Azure dijital TWINS merkezinin, dijital TWINS ve ilişkilerden oluşturulan [ikizi Graf](concepts-twins-graph.md)olduğunu unutmayın. 

Bu grafik, içerdiği dijital TWINS ve ilişkiler hakkında bilgi almak için sorgulanabilir. Bu sorgular, **Azure Digital Twins sorgu dili** olarak adlandırılan özel ve SQL benzeri bir sorgu diliyle yazılır. Bu, çok sayıda karşılaştırılabilir özelliği olan [IoT Hub sorgu diline](../iot-hub/iot-hub-devguide-query-language.md) benzerdir.

Bu makalede, sorgu dilinin ve özelliklerine ilişkin temel bilgiler açıklanmaktadır. Sorgu söz dizimi ve sorgu isteklerinin nasıl çalıştırılacağı hakkında daha ayrıntılı örnekler için bkz. [*nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md).

## <a name="about-the-queries"></a>Sorgular hakkında

Azure dijital TWINS sorgu dilini kullanarak dijital TWINS 'i bunlara göre elde edebilirsiniz...
* Özellikler ( [etiket özellikleri](how-to-use-tags.md)dahil)
* modeller
* ilişkiler
  - ilişkilerin özellikleri

Bir istemci uygulamasından hizmete bir sorgu göndermek için Azure Digital TWINS [**sorgu API**](/rest/api/digital-twins/dataplane/query)'sini kullanırsınız. API 'yi kullanmanın bir yolu, Azure dijital TWINS için [SDK 'lardan](how-to-use-apis-sdks.md#overview-data-plane-apis) biridir.

### <a name="considerations-for-querying"></a>Sorgulama konuları

Azure dijital TWINS için sorgular yazarken aşağıdaki noktaları göz önünde bulundurun:
* **Büyük/küçük harf duyarlılığı 'Nı hatırla**: tüm Azure dijital TWINS sorgu işlemleri büyük/küçük harfe duyarlıdır, bu nedenle modellerinizde tanımlanan tam adları kullanın. Özellik adları yanlış yazılmıştır veya yanlış bir şekilde ayarlandıysa, sonuç kümesi hiçbir hata döndürülmeden boştur.
* **Tek tırnak işareti**: sorgu metniniz veride tek tırnak karakteri içeriyorsa, teklifin kaçış karakteriyle birlikte olması gerekir `\` . *D'Souza* öğesinin özellik değeri ile ilgilenen bir örnek aşağıda verilmiştir:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Başvuru: Ifadeler ve koşullar

Bu bölümde, Azure dijital TWINS sorguları yazmak için kullanılabilen işleçler ve işlevler açıklanmaktadır. Örneğin, bu özelliklerin kullanımını gösteren sorgular için bkz. [*nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md).

### <a name="operators"></a>İşleçler

Aşağıdaki işleçler desteklenir:

| Family (Aile) | İşleçler |
| --- | --- |
| Mantıksal |`AND`, `OR`, `NOT` |
| Karşılaştırma | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>İşlevler

Aşağıdaki tür denetimi ve atama işlevleri desteklenir:

| İşlev | Açıklama |
| -------- | ----------- |
| `IS_DEFINED` | Özelliğe bir değer atanıp atanmadığını gösteren bir Boole değeri döndürür. Bu yalnızca değer temel bir tür olduğunda desteklenir. İlkel türler String, Boolean, numeric veya içerir `null` . `DateTime`, nesne türleri ve diziler desteklenmez. |
| `IS_OF_MODEL` | Belirtilen ikizi belirtilen model türüyle eşleşip eşleşmediğini gösteren bir Boole değeri döndürür |
| `IS_BOOL` | Belirtilen ifadenin türünün bir Boolean olup olmadığını gösteren bir Boole değeri döndürür. |
| `IS_NUMBER` | Belirtilen ifadenin türünün bir sayı olup olmadığını gösteren bir Boole değeri döndürür. |
| `IS_STRING` | Belirtilen ifadenin türünün bir dize olup olmadığını gösteren bir Boole değeri döndürür. |
| `IS_NULL` | Belirtilen ifadenin türünün null olup olmadığını gösteren bir Boole değeri döndürür. |
| `IS_PRIMITIVE` | Belirtilen ifadenin türünün bir ilkel öğe (dize, Boolean, sayısal veya) olduğunu gösteren bir Boole değeri döndürür `null` . |
| `IS_OBJECT` | Belirtilen ifadenin türünün bir JSON nesnesi olup olmadığını gösteren bir Boole değeri döndürür. |

Aşağıdaki dize işlevleri desteklenir:

| İşlev | Açıklama |
| -------- | ----------- |
| `STARTSWITH(x, y)` | İlk dize ifadesinin ikinciyle başlatılıp başlatılmayacağını gösteren bir Boole değeri döndürür. |
| `ENDSWITH(x, y)` | İlk dize ifadesinin ikinciyle sonlanıp bitmediğini gösteren bir Boole değeri döndürür. |

## <a name="query-limitations"></a>Sorgu sınırlamaları

Bu bölümde sorgu dilinin kısıtlamaları açıklanmaktadır.

* Zamanlama: örnekteki değişiklikler sorgularda yansıtılmadan önce en fazla 10 saniye gecikme olabilir. Örneğin, Digitaltwıns API 'SI ile TWINS oluşturma veya silme gibi bir işlemi tamamladıysanız, sonuç sorgu API 'si isteklerinde hemen yansıtılmayabilir. Bir kısa dönemin beklenmesi, çözülmesi için yeterli olmalıdır.
* İfadesinde hiçbir alt sorgu desteklenmez `FROM` .
* `OUTER JOIN` semantikler desteklenmez, yani ilişkinin sıfır sıralaması varsa, tüm "satır" çıkış sonuç kümesinden kaldırılır.
* Grafik çapraz geçiş derinliği `JOIN` sorgu başına beş düzey ile sınırlıdır.
* Azure dijital TWINS 'deki ilişkiler bağımsız varlıklar olarak sorgulanamaz; Ayrıca, ilişkinin geldiği kaynak ikizi hakkında bilgi sağlamanız gerekir. Bu, sorgunun, sorgunun `JOIN` başladığı ikizi (ler) i bildirdiğinden emin olmak için, bu işlem üzerinde ilişkileri sorgulamak için kullanılan bazı kısıtlamalar olduğu anlamına gelir. Bunun örnekleri için bkz. *nasıl yapılır: sorgu ikizi grafiğini sorgulama* makalesinde [*ilişkiye göre sorgulama*](how-to-query-graph.md#query-by-relationship) .

## <a name="next-steps"></a>Sonraki adımlar

Sorguları yazmayı ve bkz. [*nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md)hakkında istemci kodu örnekleri.