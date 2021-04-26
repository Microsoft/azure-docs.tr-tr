---
title: Azure 'da desteklenen FHıR özellikleri-FHıR için Azure API
description: Bu makalede, fhır için Azure API 'de uygulanan FHıR belirtiminin hangi özellikleri açıklanmaktadır
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530634"
---
# <a name="features"></a>Özellikler

FHıR için Azure API 'SI, Azure için Microsoft FHıR sunucusunun tam olarak yönetilen bir dağıtımını sağlar. Sunucu, [Fhır](https://hl7.org/fhir) standardının bir uygulamasıdır. Bu belge, FHıR sunucusunun ana özelliklerini listeler.

## <a name="fhir-version"></a>FHıR sürümü

Desteklenen en son sürüm: `4.0.1`

Şu anda desteklenen önceki sürümler şunlardır: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | Desteklenen-PaaS | Desteklenen-OSS (SQL) | Desteklenen-OSS (Cosmos DB) | Yorum                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| okuma                           | Yes       | Yes       | Yes       |                                                     |
| vread                          | Yes       | Yes       | Yes       |                                                     |
| update                         | Yes       | Yes       | Yes       |                                                     |
| iyimser kilitleme ile güncelleştirme | Yes       | Yes       | Yes       |                                                     |
| Güncelleştirme (koşullu)           | Yes       | Yes       | Yes       |                                                     |
| düzeltmesi                          | Hayır        | Hayır        | Hayır        |                                                     |
| delete                         | Yes       | Yes       | Yes       |  Aşağıdaki nota bakın.                                   |
| Sil (koşullu)           | Hayır        | Hayır        | Hayır        |                                                     |
| geçmiş                        | Yes       | Yes       | Yes       |                                                     |
| oluşturmaya                         | Yes       | Yes       | Yes       | Her iki GÖNDERI/PUT desteği                               |
| oluştur (koşullu)           | Yes       | Yes       | Yes       | Sorun [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Kısmi   | Kısmi   | Kısmi   | Aşağıdaki arama bölümüne bakın.                           |
| zincirleme arama                 | Kısmi       | Yes       | Kısmi   | Aşağıdaki 2. nota bakın.                                   |
| ters zincirleme arama         | Kısmi       | Yes       | Kısmi   | Aşağıdaki 2. nota bakın.                                   |
| özellikler                   | Yes       | Yes       | Yes       |                                                     |
| toplu iş                          | Yes       | Yes       | Yes       |                                                     |
| işlem                    | Hayır        | Yes       | Hayır        |                                                     |
| Sayfalamayı                         | Kısmi   | Kısmi   | Kısmi   | `self` ve `next` desteklenir                     |
| aracıların                 | Hayır        | Hayır        | Hayır        |                                                     |

> [!Note]
> FHıR belirtimi tarafından tanımlanan silme, silindikten sonra, bir kaynağın sonraki sürüme özgü olmayan okumalarının bir 410 HTTP durum kodu döndürdüğünden ve kaynak artık arama yoluyla bulunamamasına gerek duyar. FHıR için Azure API 'SI Ayrıca, kaynağı tamamen silmenizi (tüm geçmiş dahil) sağlar. Kaynağı tamamen silmek için bir parametre ayarlarını `hardDelete` true () değerine geçirebilirsiniz `DELETE {server}/{resource}/{id}?hardDelete=true` . Bu parametreyi geçirmezseniz veya `hardDelete` false olarak ayarlarsanız, kaynağın geçmiş sürümleri kullanılabilir olmaya devam edecektir.


 **2. nota**
* CosmosDB 'de zincirleme ve ters zincirleme FHıR araması için MVP desteği ekler. 

  FHIR için Azure API ve Cosmos tarafından desteklenen açık kaynaklı FHıR sunucusu, zincirleme arama ve ters zincirleme arama bir MVP uygulamasıdır. Cosmos DB üzerinde zincirleme arama gerçekleştirmek için, uygulama arama ifadesinin üzerine gider ve eşleşen kaynakları çözümlemek için alt sorgular verir. Bu, ifadenin her düzeyi için yapılır. Herhangi bir sorgu 100 'den fazla sonuç döndürürse bir hata oluşur. Varsayılan olarak, zincirleme arama özelliği bir özellik bayrağının arkasında bulunur. Cosmos DB üzerinde zincirleme aramayı kullanmak için üst bilgisini kullanın `x-ms-enable-chained-search: true` . Daha ayrıntılı bilgi için bkz. [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Arayın

Tüm arama parametresi türleri desteklenir. 

| Arama parametresi türü | Desteklenen-PaaS | Desteklenen-OSS (SQL) | Desteklenen-OSS (Cosmos DB) | Yorum |
|-----------------------|-----------|-----------|-----------|---------|
| Sayı                | Yes       | Yes       | Yes       |         |
| Tarih/Tarih saat         | Yes       | Yes       | Evet       |         |
| Dize                | Yes       | Yes       | Yes       |         |
| Belirteç                 | Yes       | Yes       | Yes       |         |
| Başvuru             | Yes       | Yes       | Yes       |         |
| Bileşik             | Yes       | Yes       | Yes       |         |
| Miktar              | Yes       | Yes       | Yes       |         |
| URI                   | Yes       | Yes       | Yes       |         |
| Spec               | Hayır        | Hayır        | Hayır        |         |


| Değiştiriciler             | Desteklenen-PaaS | Desteklenen-OSS (SQL) | Desteklenen-OSS (Cosmos DB) | Yorum |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Yes       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | Yes       |         |
|`:[type]` başvurunun  | Yes       | Yes       | Yes       |         |
|`:not`                 | Yes       | Yes       | Yes       |         |
|`:below` kullanılmamışsa         | Yes       | Yes       | Yes       |         |
|`:above` kullanılmamışsa         | Hayır        | Hayır        | Hayır        | Sorun [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` simgesinde          | Hayır        | Hayır        | Hayır        |         |
|`:below` simgesinde       | Hayır        | Hayır        | Hayır        |         |
|`:above` simgesinde       | Hayır        | Hayır        | Hayır        |         |
|`:not-in` simgesinde      | Hayır        | Hayır        | Hayır        |         |

| Ortak arama parametresi | Desteklenen-PaaS | Desteklenen-OSS (SQL) | Desteklenen-OSS (Cosmos DB) | Yorum |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_type`                 | Yes       | Yes       | Yes       | Sorun [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_profile`              | Kısmi   | Kısmi   | Kısmi   | STU3 içinde desteklenir. Veritabanınızı 20 Mart 2021 ' **den sonra** oluşturduysanız, R4 'de de destek sahibi olursunuz. 20 Şubat 2021 ' den önce oluşturulan veritabanlarında _profile etkinleştirmek için çalışıyoruz. |
| `_text`                 | Hayır        | Hayır        | Hayır        |         |
| `_content`              | Hayır        | Hayır        | Hayır        |         |
| `_has`                  | Hayır        | Hayır        | Hayır        |         |
| `_query`                | Hayır        | Hayır        | Hayır        |         |
| `_filter`               | Hayır        | Hayır        | Hayır        |         |

| Arama sonucu parametreleri | Desteklenen-PaaS | Desteklenen-OSS (SQL) | Desteklenen-OSS (Cosmos DB) | Yorum |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Yes       | Yes       | Yes       | Sorun [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Yes       | Yes       | Yes       | `_count` 1000 karakterle sınırlıdır. 1000 'den yüksek olarak ayarlandıysa, yalnızca 1000 döndürülür ve pakete bir uyarı döndürülür. |
| `_include`              | Yes       | Yes       | Yes       |Dahil edilen öğeler 100 ile sınırlıdır. PaaS ve OSS 'e dahil et Cosmos DB şunları içermez: yineleme desteği.|
| `_revinclude`           | Yes       | Yes       | Yes       | Dahil edilen öğeler 100 ile sınırlıdır. PaaS ve OSS 'e dahil et Cosmos DB şunları [içermez: yineleme desteği](https://github.com/microsoft/fhir-server/issues/1313). Sorun [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Kısmi   | Kısmi   | Kısmi   | `_summary=count` desteklenir |
| `_total`                | Kısmi   | Kısmi   | Kısmi   | `_total=none` ve `_total=accurate`      |
| `_sort`                 | Kısmi   | Kısmi   | Kısmi   |   `_sort=_lastUpdated` desteklenir       |
| `_contained`            | Hayır        | Hayır        | Hayır        |         |
| `containedType`         | Hayır        | Hayır        | Hayır        |         |
| `_score`                | Hayır        | Hayır        | Hayır        |         |

## <a name="extended-operations"></a>Genişletilmiş Işlemler

Yeniden gerçekleştirilen API 'YI genişleten desteklenen tüm işlemler.

| Arama parametresi türü | Desteklenen-PaaS | Desteklenen-OSS (SQL) | Desteklenen-OSS (Cosmos DB) | Yorum |
|------------------------|-----------|-----------|-----------|---------|
| $export (tüm sistem) | Yes       | Yes       | Yes       |         |
| Hasta/$export        | Yes       | Yes       | Yes       |         |
| Grup/$export          | Yes       | Yes       | Yes       |         |
| $convert-veri          | Yes       | Yes       | Yes       |         |


## <a name="persistence"></a>Kalıcılığı

Microsoft FHıR sunucusunda takılabilir bir kalıcılık modülü vardır (bkz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) .).

Şu anda FHıR sunucusu açık kaynaklı kodu, [Azure Cosmos DB](../../cosmos-db/index-overview.md) ve [SQL veritabanı](https://azure.microsoft.com/services/sql-database/)için bir uygulama içerir.

Cosmos DB, genel olarak dağıtılmış çok modelli (SQL API, MongoDB API 'SI vb.) veritabanıdır. Farklı [tutarlılık düzeylerini](../../cosmos-db/consistency-levels.md)destekler. Varsayılan dağıtım şablonu, FHıR sunucusunu tutarlılık ile yapılandırır `Strong` , ancak istek üst bilgisi kullanılarak istek temeline göre bir istek için tutarlılık İlkesi değiştirilebilir (genellikle gevşek) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

FHıR sunucusu, Access Control için [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) kullanır. Özellikle, yapılandırma parametresi olarak ayarlanmışsa rol tabanlı erişim denetimi (RBAC) zorlanır `FhirServer:Security:Enabled` `true` ve `/metadata` Fhır sunucusuna yönelik tüm isteklerin (hariç) `Authorization` istek üst bilgisi olarak ayarlanmış olması gerekir `Bearer <TOKEN>` . Belirtecin, talepte tanımlanan bir veya daha fazla rol içermesi gerekir `roles` . Belirteç belirtilen kaynakta belirtilen eyleme izin veren bir rol içeriyorsa, bir isteğe izin verilir.

Şu anda, belirli bir rolün izin verilen eylemleri API üzerinde *Global olarak* uygulanır.

## <a name="service-limits"></a>Hizmet sınırlamaları

* [**Istek birimleri (ru)**](../../cosmos-db/concepts-limits.md) -Azure API için Portal 'da fhır için en fazla 10.000 ru yapılandırabilirsiniz. Hangisi daha büyükse, en az 400 ru veya 40 ru/GB gerekecektir. 10.000 ' den fazla ru 'ya ihtiyacınız varsa, bu artış için bir destek bileti koyabilirsiniz. Kullanılabilir en yüksek değer 1.000.000 ' dir.

* **Eşzamanlı bağlantılar** ve **örnekler** -varsayılan olarak, kümedeki iki örnek üzerinde beş eş zamanlı bağlantınız vardır (Toplam 10 eşzamanlı istek için). Daha fazla eşzamanlı istek gerektiğini düşünüyorsanız, gereksinimlerinize ilişkin ayrıntılarla bir destek bileti açın.

* **Paket boyutu** -her paket 500 öğe ile sınırlıdır.

* **Veri boyutu** -veri/belge her bırı 2 MB 'tan biraz daha az olmalıdır.

## <a name="performance-expectations"></a>Performans beklentileri

Sistemin performansı, RUs sayısına, eşzamanlı bağlantılara ve gerçekleştirdiğiniz işlem türüne (put, Post, vb.) bağlıdır. Aşağıda, yapılandırılmış RUs 'e göre beklediklerinize ilişkin bazı genel aralıklar verilmiştir. Genel olarak, performans, ru 'daki bir artış ile doğrusal şekilde ölçeklendirilir:

| RUs | Kaynak/sn |    En fazla depolama alanı (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1.000    | 100-150       |      25  |
| 10,000   | 225-400       |      250  |
| 100.000  | 2500-4000   |      2,500  |

Note: Cosmos DB gereksinimi başına, GB depolama alanı başına en az 40 RU/sn 'lik verimlilik gereksinimi vardır. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, FHıR için Azure API 'sindeki desteklenen FHıR özellikleri hakkında bilgi aldınız. Ardından, FHıR için Azure API 'YI dağıtın.
 
>[!div class="nextstepaction"]
>[FHIR için Azure API'sini dağıtma](fhir-paas-portal-quickstart.md)
