---
title: .NET ve REST için API sürüm yönetimi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama REST API 'Leri ve .NET SDK içindeki istemci kitaplığı için sürüm ilkesi.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03dbb679c25ea692d2c52f80b9493889e367823d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112157"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Bilişsel Arama API sürümleri

Azure Bilişsel Arama, özellik güncelleştirmelerini düzenli olarak kaydeder. Bazen, her zaman olmasa da, geriye dönük uyumluluğu korumak için bu güncelleştirmeler API 'nin yeni bir sürümünü gerektirir. Yeni bir sürümü yayımlamak, kodunuzda arama hizmeti güncelleştirmelerini ne zaman ve nasıl tümleştirmenizi denetlemenize olanak tanır.

Bir kural olarak, Azure Bilişsel Arama ekibi, kodunuzu yeni bir API sürümü kullanacak şekilde yükseltmek için biraz çaba içerebileceği için, yalnızca gerektiğinde yeni sürümler yayımlar. Yeni bir sürüm yalnızca API 'nin bazı yönlerinin geriye dönük uyumluluğu kesen bir şekilde değişmesi durumunda gereklidir. Bu tür değişiklikler, mevcut özelliklerle ilgili düzeltmeler veya var olan API yüzey alanını değiştiren yeni özelliklerden kaynaklanıyor olabilir.

Aynı kural SDK güncelleştirmeleri için de geçerlidir. Azure Bilişsel Arama SDK, anlam sürümü [oluşturma](https://semver.org/) kurallarını izler, yani sürümünün üç bölümü vardır: Ana, ikincil ve derleme numarası (örneğin, 1.1.0). SDK 'nın yeni bir ana sürümü yalnızca geriye dönük uyumluluğu kesen değişiklikler için serbest bırakılır. Önemli olmayan özellik güncelleştirmeleri ikincil sürümü artırır ve hata düzeltmeleri yalnızca derleme sürümünü artırır.

> [!NOTE]
> Azure Bilişsel Arama hizmet örneğiniz, en son sürüm dahil olmak üzere birkaç REST API sürümü destekler. Artık en son bir sürüm olmadığında bir sürümü kullanmaya devam edebilirsiniz, ancak kodunuzu en yeni sürümü kullanmak için geçirmeniz önerilir. REST API kullanırken, API sürümü parametresi aracılığıyla her istekte API sürümünü belirtmeniz gerekir. .NET SDK kullanıldığında, kullanmakta olduğunuz SDK sürümü REST API ilgili sürümünü belirler. Daha eski bir SDK kullanıyorsanız, hizmet daha yeni bir API sürümünü destekleyecek şekilde yükseltilse bile, bu kodu hiçbir değişiklik yapmadan çalıştırmaya devam edebilirsiniz.

## <a name="snapshot-of-current-versions"></a>Geçerli sürümlerin anlık görüntüsü
Azure Bilişsel Arama tüm programlama arabirimlerinin geçerli sürümlerinin bir anlık görüntüsü aşağıda verilmiştir.


| Arabirimlerdeki | En son ana sürüm | Durum |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Genel kullanıma sunulan, 2019 Mayıs |
| [.NET SDK önizlemesi](https://aka.ms/search-sdk-preview) |8,0-Önizleme |Önizleme, yayın 2019 Nisan |
| [Hizmet REST API'si](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Genel Kullanıma Sunuldu |
| [Service REST API 2019-05-06-Önizleme](search-api-preview.md) |2019-05-06-Önizleme |Önizleme |
| [.NET Yönetim SDK'sı](https://aka.ms/search-mgmt-sdk) |3.0 |Genel Kullanıma Sunuldu |
| [Yönetim REST API'si](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Genel Kullanıma Sunuldu |

REST API 'Leri için, her çağrıda `api-version` de dahil olmak üzere gereklidir. `api-version` kullanmak, önizleme API 'SI gibi belirli bir sürümü hedeflemek kolaylaştırır. Aşağıdaki örnekte `api-version` parametresinin nasıl belirtildiği gösterilmektedir:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Her isteğin bir `api-version`olsa da, tüm API istekleri için aynı sürümü kullanmanızı öneririz. Bu, yeni API sürümleri önceki sürümler tarafından tanınmayan öznitelikler veya işlemler tanıtdığında özellikle doğrudur. API sürümlerinin karışması istenmeyen sonuçlara sahip olabilir ve kaçınılmalıdır.
>
> Hizmet REST API ve yönetim REST API birbirinden bağımsız olarak sürümlüdür. Sürüm numaralarında benzerlik varsa coarızdır.

Genel olarak kullanılabilir (veya GA) API 'Leri üretimde kullanılabilir ve Azure hizmet düzeyi sözleşmelerine tabidir. Önizleme sürümleri, her zaman bir GA sürümüne geçirilmeyen deneysel özelliklere sahiptir. **Üretim uygulamalarında önizleme API 'Lerini kullanmaktan kaçınmak kesinlikle önerilir.**

## <a name="about-preview-and-generally-available-versions"></a>Önizleme ve genel kullanıma açık sürümler hakkında
Azure Bilişsel Arama, önce REST API önce, sonra .NET SDK 'nin ön sürüm sürümlerini kullanarak deneysel özellikleri önceden yayınlar.

Önizleme özellikleri, test ve deneme için, özellik tasarımı ve uygulama hakkında geri bildirim toplama hedefi ile kullanılabilir. Bu nedenle, Önizleme özellikleri zaman içinde değişebilir, belki de geriye doğru uyumluluğu kesen şekillerde değişiklik yapabilir. Bu, bir GA sürümündeki özelliklerin aksine, bu durum, küçük ve geriye dönük olarak uyumlu düzeltmeler ve geliştirmeler dışında değişmez ve çok düşüktür. Ayrıca, Önizleme özellikleri her zaman bir GA sürümüne değildir.

Bu nedenlerden dolayı, önizleme sürümlerine bağımlılığı alan üretim kodu yazmayı öneririz. Daha eski bir önizleme sürümü kullanıyorsanız, genel kullanıma açık (GA) sürüme geçiş yapmanızı öneririz.

.NET SDK için: [.NET SDK yükseltme](search-dotnet-sdk-migration-version-9.md)sırasında kod geçişi için rehberlik bulunabilir.

Genel kullanılabilirlik, Azure Bilişsel Arama artık hizmet düzeyi sözleşmesi (SLA) altında olduğu anlamına gelir. SLA, [Azure bilişsel arama hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/search/v1_0/)' nde bulunabilir.
