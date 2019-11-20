---
title: Azure blok zinciri belirteçleri bileşim
description: Azure blok zinciri belirteçleri bileşim, gelişmiş senaryolar için belirteçler oluşturma esnekliği sağlar.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: a82d7ba606eac5dcafc26b1a8527810a5a21840d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577108"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure blok zinciri belirteçleri bileşim

[!INCLUDE [Preview note](./includes/preview.md)]

Belirteç bileşim, gelişmiş senaryolar için belirteçler oluşturma esnekliği sağlar. [Önceden oluşturulmuş dört belirteç şablonu](templates.md#base-token-types)kullanılarak uygulanamaz karmaşık bir senaryonuz olabilir. Belirteç bileşim, kendi belirteç şablonunuzu oluşturmak için tanımlı davranışları ekleyerek veya kaldırarak kendi belirteç şablonlarınızı tasarlamanıza olanak sağlar. Yeni bir belirteç şablonu oluştururken, Azure blok zinciri belirteçleri tüm belirteç dilbilgisi kurallarını doğrular. Oluşturulan şablonlar, bağlantılı blok zinciri ağlarında yayımlamak için Azure blok zinciri belirteçleri hizmetine kaydedilir.

Belirteç şablonunuzu tasarlamak için aşağıdaki bölümlerdeki [belirteç davranışlarını](templates.md#token-behaviors) kullanabilirsiniz.

## <a name="burnable-b"></a>Burleştir (b)

Belirteçleri tedariğin kaldırabilme özelliği.

Örneğin, bir hediye kartı için çevrimiçi kredi kartı noktaları kullandığınızda, kredi kartı noktaları yazılır.

## <a name="delegable-g"></a>Temsilci seçilebilen (g)

Sahip olduğunuz belirteç üzerinde gerçekleştirilen eylemleri temsil etme yeteneği.

Temsilci, belirteç sahibi olarak eylemleri gerçekleştirebilir. Örneğin, bir oyu uygulamak için bir temsilci seçilebilen belirteci kullanabilirsiniz. Bir temsilci seçilebilen belirteci, oy belirteci sahibinin adına başka birinin oymasına izin verir.

## <a name="logable-l"></a>Günlüklenebilir (l)

Günlüğe kaydetme özelliği.

Örneğin, belirli bir filmi gösteren her bir sineması için bir film dağıtımı için günlüğe kaydedebilir bir belirteç verebilirsiniz. Filmin yürütülmesi için, bir işlemin her biri için bir işlem çalıştırması gerekir, çünkü bu, telif ücreti ödemeler, filmin yayın çalıştırması sırasında gösterilmelidir. Aktör derlemesi, dağıtım sırasında her bir sineması gösteren film başına ödeme sayısını doğrulamak için film belirteçlerini kullanabilir.

## <a name="mint-able-m"></a>Mint-Able (d)

Belirteç sınıfı için ek belirteçleri mini gösterme özelliği. Minter rolü mintable davranışını içerir.

Örneğin, bir bağlılık programı programını uygulamak isteyen bir perakende şirketi, bağlılık programı programları için mintable belirteçlerini kullanabilir. Müşteri tabanı büyüdükçe müşterileri için ek bağlılık programı noktalarından daha fazla programlama yapabilir.  

## <a name="non-subdividable-or-whole-d"></a>Alt veya tam olmayan (~ d)

Belirtecin daha küçük parçalara bölünmesine engel olan kısıtlama.

Örneğin, tek bir resim boyama, birden çok küçük parçaya ayrılabilir. 

## <a name="non-transferable-t"></a>Aktarılamayan (~ t)

İlk belirteç sahibinden sahiplik değişikliğini önleme kısıtlaması.

Örneğin, bir üniversite DISA, aktarılamayan bir belirteçtir. Bir gönderdikten sonra, bir sevenma verildiğinde, mezun 'den başka bir kişiye aktarılamaz.

## <a name="roles-r"></a>Roller (r)

Belirli davranışlar için belirteç şablonu sınıfında Roller tanımlama yeteneği.

Belirtecin, belirteç oluşturma sırasında desteklediği rol adlarının listesini sağlayabilirsiniz. Roller belirtildiğinde, Kullanıcı bu davranışlara roller atayabilir. Şu anda yalnızca Minter rolü destekleniyor.

## <a name="singleton-s"></a>Tekli (lar)

Bir belirteç sağlamak için kısıtlama.

Örneğin, bir Museum yapıtı tek bir belirteçtir. Museum yapıtları benzersizdir. Yapıtı temsil eden belirtecin yalnızca TEDARİKTEKİ tek bir öğesi vardır.

## <a name="subdividable-d"></a>Alt (d)

Bir belirteci daha küçük parçalara bölebilme.

Örneğin, bir dolar, ilal 'ye ayrılabilir.

## <a name="transferable-t"></a>Transfer edilebilir (t)

Belirtecin sahipliğini aktarma özelliği.

Örneğin, bir özellik başlığı, özellik satıldığında bir kişiden diğerine aktarılabilen bir aktarılabilir belirteç olur.

## <a name="next-steps"></a>Sonraki adımlar

[Azure blok zinciri belirteçleri hesap yönetimi](account-management.md)hakkında bilgi edinin.
