---
title: Azure blok zinciri belirteçleri nedir?
description: Azure blok zinciri belirteçleri, belirteç verme ve yönetimi için bir hizmet olarak platform (PaaS).
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: cd41d52e06a5c1833dca9669881cbe48f362d81d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579746"
---
# <a name="what-is-azure-blockchain-tokens"></a>Azure blok zinciri belirteçleri nedir?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure blok zinciri belirteçleri, Azure 'daki blok zinciri genelinde standartlaştırılmış belirteç verme ve yönetimi için hizmet olarak platform (PaaS).

Azure blok zinciri belirteçlerini kullanarak, önceden oluşturulmuş belirteç şablonu kullanarak blok zinciri çözümünüz için standartlaştırılmış belirteçler oluşturabilirsiniz. Ayrıca, hizmetini kullanarak kendi belirteç şablonunuzu da oluşturabilirsiniz. Oluşturulduktan sonra, bir blok zincirindeki belirteçleri bağlamak ve bu belirteçleri vermek için Azure blok zinciri belirteçlerini kullanın. Daha sonra, belirteçleri birden çok blok zinciri ağı genelinde yönetebilirsiniz.

## <a name="templates"></a>Şablonlar

Önceden oluşturulmuş bir belirteç şablonu seçmek veya kendi belirteç şablonunuzu oluşturmak için Azure blok zinciri belirteçlerini kullanın. Azure blok zinciri belirteçleri, desteklenen davranışları temel alarak kendi belirteç şablonunuzu oluşturmanıza olanak sağlayan belirteç şablonu bileşyelbilirliğini destekler. Belirteç şablonları, çoğu blok zinciri çözümü için en sık kullanılan belirteçlerle eşlendiğinden kullanılabilir. Bir şablonla başlayabilir, kişiselleştirebilir ve çözümünüzle ilgili belirteçleri dağıtabilirsiniz.

Azure blok zinciri belirteçleri şablonları hakkında daha fazla bilgi için bkz. [Azure blok zinciri belirteçleri şablonları](templates.md).

## <a name="management"></a>Yönetim

Azure blok zinciri belirteçleri, mevcut bir blok zinciri ağına bağlanmak için Azure portal yönetim ve API 'Ler sağlar. Şu anda [Azure blok zinciri hizmeti](../service/overview.md) 'ne veya başka bir Ethereum aile blok zincirine bağlanabilirsiniz.

Bir veya birden çok blok zinciri ağına bağlandıktan sonra, blok zinciri çözümünüzde kullanılacak belirteçleri vermek ve yönetmek için Azure blok zinciri belirteçleri API 'Lerini kullanabilirsiniz. API 'Leri kullanarak, iş uygulamalarınızda ve mantığınızdaki belirteç yönetimini tümleştirebilirsiniz. Örneğin, belirteçleri doğrudan blok zincirinde yönetmek yerine belirteçleri yönetmek için REST API kullanabilirsiniz.

## <a name="blockchains-and-accounts"></a>Blok zincirler ve hesaplar

Azure blok zinciri belirteçleri, bağlantılı blok zinciri ağlarında yeni gruplar ve yeni blok zinciri hesapları oluşturmak için Azure portal yönetim ve API 'Ler sağlar. Doğrudan bağlantılı ağlarınızda yeni hesaplar oluşturabilirsiniz ve Azure blok zinciri belirteçleri, hesap özel anahtarlarınızı sizin adınıza yönetir. Grupları kullanarak, birden fazla ağdan farklı blok zinciri hesapları gruplandırabilirsiniz ve gruplar aracılığıyla erişim denetimini yönetebilirsiniz.

Azure blok zinciri belirteçleri hesap yönetimi hakkında daha fazla bilgi için bkz. [Azure blok zinciri belirteçleri hesap yönetimi](account-management.md).

## <a name="token-taxonomy-framework"></a>Belirteç taksonomi çerçevesi

Azure blok zinciri belirteçleri, belirteç taksonomi çerçevesi (TTF) adlı standartlara dayalı bir temel üzerinde oluşturulmuştur. TTF, [belirteç taksonomi girişimi](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI) belirteci çalışma grubundan oluşturulan teslim edilebilirler kümesidir. TTı çalışma grubu, belirteçler için bir iş Taksonomi ve Ethereum, çekirdek, Corda ve hiper muhasebe dokusunu dahil olmak üzere tüm önemli defterlerde uygulanabilen davranışları tanımlar. Çalışma grubunun hedefi, bir iş perspektifinden, basitme ve herkese belirteci tabanlı geliştirmeyi sağlamak için belirteçlerin kullanımını standartlayan bir çerçeve oluşturmaktır. Sektörün bu belirteçleri ve davranışlarını iş düzeyinde tanımlamasına izin vererek, belirteçlerin ayrıntılı uygulanması belirteçleri belirleyen iş mantığından soyutlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Kullanılabilir [Azure blok zinciri belirteçleri şablonları](templates.md)hakkında daha fazla bilgi edinin.
