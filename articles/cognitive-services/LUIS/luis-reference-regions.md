---
title: Yayımlama bölgeleri & uç noktaları-LUSıS
description: Azure portal belirtilen bölge, LUO uygulamasını yayımlayacağınız yer ve aynı bölge için bir uç nokta URL 'SI oluşturulur.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: fd02bf5c3291569b71416392b651967e0da701d9
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226676"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Yazma ve yayımlama bölgeleri ve ilişkili anahtarlar

LUSıS yazma bölgeleri, Lua portalı tarafından desteklenir. Bir LUIS uygulamasını birden fazla bölgeye yayımlamak için her bölgeye ait en az bir anahtara ihtiyacınız vardır.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUSıS yazma bölgeleri

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIN, [www.Luis.ai](https://www.luis.ai)bölgesinden bağımsız olarak kullanabileceğiniz bir portala sahiptir. Hala aynı bölgede yazar ve yayımlama yapmanız gerekir.

Yazma bölgeleri, [Yük devretme bölgelerini eşleştirilmiştir](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Yayımlama bölgeleri ve Azure kaynakları

Uygulama, LUIS portalına eklenmiş olan LUIS kaynaklarıyla ilişkilendirilen tüm bölgelerde yayımlanır. Örneğin, [www.Luis.ai][www.luis.ai]üzerinde oluşturulan bir uygulama için, **WESTUS** 'de bir lusıs veya bilişsel hizmet kaynağı oluşturup [bunu bir kaynak olarak uygulamaya eklerseniz](luis-how-to-azure-subscription.md), uygulama o bölgede yayımlanır.

## <a name="public-apps"></a>Ortak uygulamalar
Ortak bir uygulama tüm bölgelerde yayımlanır, böylece bölge tabanlı bir LUTO kaynak anahtarı olan bir Kullanıcı, kaynak anahtarı ile ilişkili olan herhangi bir bölgede uygulamaya erişebilir.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Yayımlama bölgeleri yazma bölgelerine bağlıdır

Yazma bölgesi uygulaması yalnızca ilgili yayımlama bölgesine yayımlanabilir. Uygulamanız yanlış yazma bölgesinde bulunuyorsa dışarı aktarıp yayımlama bölgenize uygun yazma bölgesinde içeri aktarın.

> [!NOTE]
> Üzerinde oluşturulan LUSıS uygulamaları https://www.luis.ai artık [Avrupa](#publishing-to-europe) ve [Avustralya](#publishing-to-australia) bölgeleri dahil tüm uç noktalara yayımlanabilir.

## <a name="publishing-to-europe"></a>Avrupa 'da yayımlama

 Genel bölge | Yazma API bölgesi | Bölge & yayımlanıyor<br>`API region name`   |  Uç nokta URL biçimi   |
|-----|------|------|------|
| Avrupa | `westeurope`| Orta Fransa<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Avrupa | `westeurope`| Kuzey Avrupa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Avrupa | `westeurope`| West Europe<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Avrupa | `westeurope`| Güney Birleşik Krallık<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Avrupa | `westeurope`| İsviçre Kuzey<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Avustralya 'da yayımlama

 Genel bölge | Yazma API bölgesi | Bölge & yayımlanıyor<br>`API region name`   |  Uç nokta URL biçimi   |
|-----|------|------|------|
| Avustralya | `australiaeast` | Doğu Avustralya<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Diğer yayımlama bölgeleri

 Genel bölge | Yazma API bölgesi | Bölge & yayımlanıyor<br>`API region name`   |  Uç nokta URL biçimi   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Güney Afrika - Kuzey<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Orta Hindistan<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Doğu Asya<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Doğu Japonya<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Batı Japonya<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Güney Kore - Orta<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Güneydoğu Asya<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asya | `westus`<br>[www.luis.ai][www.luis.ai]| Kuzey UAE<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Orta Kanada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Central US<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Doğu ABD<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Doğu ABD 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Orta Kuzey ABD<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Orta Güney ABD<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Orta Batı ABD<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Batı ABD<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Kuzey Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Batı ABD 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Güney Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Güney Brezilya<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Uç Noktalar

[Yazma ve tahmin uç noktaları](developer-reference-resource.md)hakkında daha fazla bilgi edinin.

## <a name="failover-regions"></a>Yük devretme bölgeleri

Her bölgenin yük devretmek için bir ikincil bölgesi vardır. Avrupa, Avrupa ve Avustralya 'Nın içinde devredilmekte ve Avustralya 'Nın içinde devredildi.

Yazma bölgeleri, [Yük devretme bölgelerini eşleştirilmiştir](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Önceden oluşturulmuş varlıklar başvurusu](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai