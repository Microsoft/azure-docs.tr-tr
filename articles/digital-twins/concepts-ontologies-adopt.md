---
title: Sektörün endüstri standardı ontoloi 'yi benimseme
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS için benimseyen mevcut sektör ontoloesi hakkında bilgi edinin
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102124236"
---
# <a name="adopting-an-industry-ontology"></a>Endüstri ontology benimseme

Boş bir sayfadan itibaren açık kaynaklı bir DTDL ontology ile başlamak daha kolay olabileceğinden, Microsoft, yaygın olarak kabul edilen sektör kurallarını temsil eden ve çeşitli müşteri kullanım örneklerini destekleyen, ontoloetyi yayımlamak için etki alanı uzmanlarıyla ortaklık yapıyor. 

Sonuç, endüstri standartlarını öğren, derleme, öğrenme, öğrenme veya doğrudan kullanma hakkında bilgi veren açık kaynaklı bir DTDL tabanlı ontolode kümesidir. Ontoloda, sektör tarafından yaygın olarak benimsenme ve/veya genişletilmiş olma potansiyeli, aşağı akış geliştiricilerin ihtiyaçlarını karşılamak üzere tasarlanmıştır.

Microsoft, [akıllı binalar](#realestatecore-smart-building-ontology) için bir ontology ve [Akıllı Şehirler](#smart-cities-ontology)için bir ontology geliştirmek üzere iş ortaklarıyla birlikte çalışarak, bu sektörlerdeki standartlara dayalı olarak modellemeye yönelik ortak bir temel alan, yeniden sayım yapmaktan kaçınır. 

## <a name="realestatecore-smart-building-ontology"></a>Reatastatecore akıllı derleme ontology

*Ontology burada bulabilirsiniz: [**akıllı binalar Için dijital TWINS tanımı dil tabanlı RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)*.

Microsoft, bu açık kaynaklı DTDL ontology ve gerçek BT sektörü için bu açık kaynaklı DTDL 'yi teslim etmek üzere [Reatastatecore](https://www.realestatecore.io/)ile iş ortağı, gerçek emlak sahipleri, yazılım satıcıları ve araştırma kurumları ile işbirliği yaptı.

Bu akıllı binalar ontology, yeniden oluşturmayı önlemek için sektör standartlarını ( [blok şeması](https://brickschema.org/ontology/) veya [W3C yapı topolojisi ontology](https://w3c-lbd-cg.github.io/bot/index.html)gibi) kullanarak modelleme akıllı binaları sunar. Ontology Ayrıca, bunu nasıl kullanacağınızı ve doğru şekilde genişletmenin en iyi uygulamaları ile birlikte sunulur. 

Ontology 'in yapısı ve modelleme kuralları hakkında daha fazla bilgi edinmek için, nasıl kullanılacağı, nasıl genişletileceği ve katkıda bulunma hakkında daha fazla bilgi edinmek için GitHub 'daki ontology deposunu ziyaret edin: [Azure/opendigitaltwins-Building](https://github.com/Azure/opendigitaltwins-building). 

Ayrıca, bu Web günlüğü gönderisine ve buna eşlik eden bu girişim için Reatastatecore ile iş ortaklığı hakkında daha fazla bilgi edinebilirsiniz ve ilgili video: [Reatastatecore, dijital TWINS için akıllı bina ontology artık kullanılabilir](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Akıllı Şehirler ontology

*Ontology burada bulabilirsiniz: [**akıllı şehirlerde dijital TWINS tanım DILI (dtdl) ontology**](https://github.com/Azure/opendigitaltwins-smartcities)*.

Microsoft, akıllı şehirlere yönelik DTDL tabanlı ontology, [ETSI CıM NGSı-ld](https://www.etsi.org/committee/cim)Ile başlayan [Açık çevik Akıllı şehirler (oasc)](https://oascities.org/) ve [Sirus](https://sirus.be/) ile işbirliği içerir. ETSı NGSı-LD ' ye ek olarak, Saref4City, CityGML, ISO ve diğerleri de değerlendirdik.

Ontology 'in geçerli sürümü, bir başlangıç modeli kümesine odaklanır. Ontology yazarları, ilk kullanım örneği kümesini genişletmek ve var olan modelleri geliştirmek için size katkıda bulunmak için hoş geldiniz. 

Ontology ve nasıl kullanılacağı hakkında daha fazla bilgi edinmek için GitHub 'daki ontology deposunu ziyaret edin: [Azure/opendigitaltwins-smartşehirler](https://github.com/Azure/opendigitaltwins-smartcities). 

Ayrıca, bu blog gönderisine ve buna eşlik eden akıllı şehirlere yönelik ortaklıklar ve yaklaşım hakkında daha fazla bilgi edinebilirsiniz: [dijital TWINS Için Akıllı Şehirler ontology](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Sonraki adımlar

* Belirtimlerinizi karşılamak için sektör standardı ontoloi 'yi genişletme hakkında daha fazla bilgi edinin: [*Kavramlar: sektör ontoloi 'Yi genişletme*](concepts-ontologies-extend.md).

* Ya da, [*ontolobir model geliştirme yolunda ontology stratejilerini kullanarak*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)modeller geliştirmeye yönelik yol üzerinde devam edin.