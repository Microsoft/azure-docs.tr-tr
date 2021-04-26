---
title: Kimlik modeli
titleSuffix: An Azure Communication Services concept
description: Kimlikler ve erişim belirteçleri hakkında bilgi edinin
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5e53f9e89dd9718111da4f8016bc3e5a7f791627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496584"
---
# <a name="identity-model"></a>Kimlik modeli

Azure Iletişim Hizmetleri, kimlik belirsiz bir hizmettir. Bu tasarım birden fazla avantaj sunar:

- Kimlik yönetimi sisteminizden mevcut kimlikleri yeniden kullanır
- Tümleştirme senaryoları için esneklik sağlar
- Azure Iletişim hizmetlerinde kimliklerinizi gizli tutar

Sisteminizdeki bilgileri çoğaltmak yerine, iş kasalarınızın gerektirdiği eşleme ilişkisini korursunuz. Örneğin, 1:1, 1: N, N:1, N:M. kimliklerini eşleyebilirsiniz. Telefon numaraları, kullanıcılar, cihazlar, uygulamalar ve GUID 'Ler gibi dış tanımlayıcılar, Azure Iletişim hizmetlerindeki kimlik için kullanılamaz. Azure Iletişim Hizmetleri kimliği için oluşturulan erişim belirteçleri, sohbet veya çağırma gibi temel elemanlarına erişmek için kullanılır.

## <a name="identity"></a>Kimlik

Azure Iletişim Hizmetleri kimlik kitaplığını kullanarak kimlik oluşturabilirsiniz. Bir kimlik, konuşmalardaki bir tanımlayıcı işlevi görür. Erişim belirteçleri oluşturmak için kullanılır. Aynı kimlik birden çok cihazda birden çok eş zamanlı oturuma katılabilir. Bir kimlik aynı anda birden fazla etkin erişim belirtece sahip olabilir.

Bir kimlik, kaynak veya aboneliğin silinmesi, tüm erişim belirteçlerini geçersiz kılar. Bu eylem, kimlik için depolanan tüm verileri de siler. Silinen bir kimlik yeni erişim belirteçleri oluşturamaz veya önceden depolanmış verilere erişemez (örneğin, sohbet iletileri).

Sahip olduğunuz kimlik sayısı için ücret ödemeniz gerekmez. Bunun yerine, temel elemanların kullanımı için ücretlendirilirsiniz. Kimliklerinizin sayısı, uygulamanızın kimliklerini Azure Iletişim Hizmetleri kimlikleriyle nasıl eşlekullanabileceğinizi kısıtlamak zorunda değildir.

Eşleme özgürlüğü, gizlilik sorumluluğunu sunar. Bir Kullanıcı sisteminizden silmek isterse, bu kullanıcıyla ilişkili tüm kimlikleri silmeniz gerekir.

Azure Iletişim Hizmetleri anonim kullanıcılar için özel kimlikler sağlamıyor. Kullanıcılar ve kimlikler arasındaki eşlemeyi yapmaz ve bir kimliğin anonim olup olmadığını belirleyemez. Kimlik kavramını gereksinimlerinize uyacak şekilde tasarlayabilirsiniz. Her uygulama üzerinde her anonim kullanıcı için yeni bir kimlik oluşturmak bizim önerimiz.

Geçerli bir erişim belirtecine sahip olan herkes, geçerli kimlik içeriğine erişebilir. Örneğin, kullanıcılar gönderdikleri sohbet iletilerine erişebilir. Erişim yalnızca erişim belirtecinin parçası olan kapsamlarla kısıtlıdır. Daha fazla bilgi için bu makaledeki [erişim belirteçleri](#access-tokens) bölümüne bakın.

### <a name="identity-mapping"></a>Kimlik eşleme

Azure Iletişim Hizmetleri, Azure Identity Management sisteminin işlevlerini çoğaltmaz. Müşterilerin müşteriye özgü kimlikleri kullanması için bir yol sağlamaz. Örneğin, müşteriler telefon numarası veya e-posta adresi kullanamaz. Bunun yerine, Azure Iletişim Hizmetleri benzersiz tanımlayıcılar sağlar. Bu benzersiz tanımlayıcıları uygulamanızın kimliklerine atayabilirsiniz. Azure Iletişim Hizmetleri, kullanıcılarınızın gerçek kimliğini açığa çıkarması gerekebilecek herhangi bir türde bilgiyi depolamaz.

Sisteminizdeki bilgilerin yinelenmesinden kaçınmak için, kimlik etki alanındaki kullanıcıların Azure Iletişim Hizmetleri kimliklerine nasıl eşlendiğini planlayın. Herhangi bir tür kalıbı izleyebilirsiniz. Örneğin, 1:1, 1: N, N:1 veya m:nkullanabilirsiniz. Tek bir kullanıcının tek bir kimlikle mi, yoksa birden çok kimliğe mi eşlenmiş olduğuna karar verin.

Yeni bir kimlik oluşturulduğunda, eşlemesini uygulamanızın kullanıcısına veya kullanıcılarına saklayın. Kimlikler, temelleri kullanmak için erişim belirteçleri gerektirdiğinden, kimliğin uygulamanızın kullanıcısı veya kullanıcıları tarafından bilinmesi gerekir.

Kullanıcı bilgilerini depolamak için ilişkisel bir veritabanı kullanırsanız, tasarımınızı eşleme senaryonuz temelinde ayarlayabilirsiniz. 1:1 veya N:1 eşleyen senaryolar için, `CommunicationServicesId` Azure Iletişim Hizmetleri kimliğinizi depolamak üzere tabloya bir sütun eklemek isteyebilirsiniz. 1: N veya N:D ilişki kullanan senaryolarda, ilişkisel veritabanında ayrı bir tablo oluşturmayı düşünebilirsiniz.

## <a name="access-tokens"></a>Erişim belirteçleri

Erişim belirteci, Azure Iletişim hizmeti temel elemanlarına erişim sağlamak için kullanılabilen bir JSON Web Token (JWT). Verilen erişim belirtecinin bütünlük koruması vardır. Diğer bir deyişle, talepleri verildikten sonra değiştirilemez. Bu nedenle, kimlik, süre sonu veya kapsamlar gibi özelliklerin el ile değiştirilmesi erişim belirtecini geçersiz kılar. Temel elemanlar geçersiz kılınan belirteçlerle birlikte kullanılırsa, bu temel elemanların erişimi reddedilir.

Erişim belirtecinin özellikleri şunlardır:
* IDENTITY.
* Dolmadan.
* Kapsamları.

Erişim belirteci her zaman 24 saat için geçerlidir. Süresi dolduktan sonra, erişim belirteci geçersiz kılınır ve herhangi bir temel erişim için kullanılamaz.

Bir kimlik, sunucu tarafı hizmetinden yeni bir erişim belirteci istemek için bir yönteme ihtiyaç duyuyor. *Kapsam* parametresi, kullanılabilecek, boş olmayan bir temel öğeler kümesi tanımlar. Azure Iletişim Hizmetleri, erişim belirteçleri için aşağıdaki kapsamları destekler.

|Ad|Açıklama|
|---|---|
|Sohbet|  Bir sohbete katılma olanağı verir|
|Hizmetinin|  Kimlikleri ve telefon numaralarını çağırma olanağı verir|


Bir erişim belirtecini sona erme zamanından önce iptal etmek için Azure Iletişim Hizmetleri kimlik kitaplığını kullanın. Belirteç iptali anında değil. Yayılması 15 dakikaya kadar sürer. Bir kimlik, kaynak veya aboneliğin kaldırılması tüm erişim belirteçlerini iptal eder.

Bir kullanıcının belirli işlevlere erişme yeteneğini kaldırmak istiyorsanız, tüm erişim belirteçlerini iptal edin. Daha sonra daha sınırlı bir kapsam kümesine sahip yeni bir erişim belirteci yayınlayın.

Azure Iletişim hizmetlerinde, erişim anahtarlarının bir dönüşü eski erişim anahtarı kullanılarak oluşturulmuş tüm etkin erişim belirteçlerini iptal eder. Tüm kimlikler Azure Iletişim hizmetlerine erişimi kaybeder ve yeni erişim belirteçleri yayınmaları gerekir.

İstemci uygulamasında değil, sunucu tarafı hizmetinize erişim belirteçleri verilmesini öneririz. Bunun nedeni, verme işlemi için bir erişim anahtarı veya yönetilen kimlik gerektirir. Güvenlik nedenleriyle, erişim anahtarlarının istemci uygulamasıyla paylaşılması önerilmez.

İstemci uygulaması, istemcilerinizin kimliğini doğrulayabilecek bir güvenilir hizmet uç noktası kullanmalıdır. Uç noktanın, kendi adına erişim belirteçleri vermesi gerekir. Daha fazla bilgi için bkz. [istemci ve sunucu mimarisi](./client-and-server-architecture.md).

Erişim belirteçlerini bir yedekleme deposuna önbellekliyorsanız, şifrelemeyi kullanmanızı öneririz. Erişim belirteci hassas verilere sahiptir. Korumalı değilse, kötü amaçlı etkinlik için kullanılabilir. Erişim belirtecine sahip olan birisi SDK başlatabilir ve API 'ye erişebilir. Erişilebilir API yalnızca erişim belirtecinin sahip olduğu kapsamlara göre kısıtlanır. Yalnızca gerekli kapsamları olan erişim belirteçleri vermeyi öneririz.

## <a name="next-steps"></a>Sonraki adımlar

* Belirteç yönetimine erişim tanıtımı için bkz. [erişim belirteçleri oluşturma ve yönetme](../quickstarts/access-tokens.md).
* Kimlik doğrulamaya giriş için bkz. [Azure Iletişim hizmetlerinde kimlik doğrulama](./authentication.md).
* Veri yerleşimi ve gizliliği 'ne giriş için bkz. [bölge kullanılabilirliği ve veri](./privacy.md)yerleşimi.