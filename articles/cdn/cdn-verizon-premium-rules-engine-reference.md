---
title: Azure CDN kuralları altyapısı başvurusu | Microsoft Docs
description: Azure CDN kuralları altyapısı için başvuru belgeleri koşullar ve özelliklerle eşleşir.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362589"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Verizon Premium kuralları altyapı başvurusundan Azure CDN

Bu makalede, Azure Content Delivery Network (CDN) [kuralları altyapısının](cdn-verizon-premium-rules-engine.md)kullanılabilir eşleşme koşullarının ve özelliklerinin ayrıntılı açıklamaları listelenmektedir.

Kural altyapısı, belirli istek türlerinin CDN tarafından nasıl işlendiği konusunda son yetkili olacak şekilde tasarlanmıştır.

**Ortak kullanımlar**:

- Özel bir önbellek ilkesini geçersiz kılın veya tanımlayın.
- Hassas içeriğe yönelik güvenli veya reddetme istekleri.
- İstekleri yeniden yönlendirin.
- Özel günlük verilerini depolayın.
## <a name="key-concepts"></a>Önemli kavramlar
Kural altyapısını ayarlamaya yönelik temel kavramlar aşağıda açıklanmıştır.
### <a name="draft"></a>Taslak
Bir ilkenin taslağı, istekleri ve bunlara uygulanacak eylem kümesini tanımlamak için bir veya daha fazla kuralla oluşur. Taslak, site trafiğini etkilemeden sık kullanılan yapılandırma güncelleştirmelerine izin veren bir çalışmadır. Bir taslak sonlandırılmaya hazırsa, salt okunurdur bir ilkeye dönüştürülmelidir.

### <a name="rule"></a>Kural
Bir kural bir veya daha fazla istek türünü ve bunlara uygulanacak eylem kümesini tanımlar.

Aşağıdakilerden oluşur: 

- İsteklerin tanımlandığı mantığı tanımlayan Koşullu ifadeler kümesi.
- İstekleri tanımlamak için kullanılan ölçütü tanımlayan bir eşleşme koşulları kümesi.
- CDN 'nin yukarıdaki istekleri nasıl işleyeceğini tanımlayan özellikler kümesi.
Bu öğeler aşağıdaki çizimde tanımlanmıştır.

![Etiketli ekran görüntüsü koşullu Ifadeyi, eşleşmeyi ve bir kuralın özelliklerini gösterir.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>İlke
Salt okuma kuralları kümesinden oluşan bir ilke, şunları sağlar:

- Kurallarınızın birden fazla çeşidini oluşturun, depolayın ve yönetin.
- Daha önce dağıtılan bir sürüme geri alma.
- Olaya özgü kuralları önceden hazırlayın (örneğin, trafiği müşteri kaynağı bakımının sonucu olarak yeniden yönlendiren bir kural).

> [!NOTE]
> Her ortam için yalnızca tek bir ilkeye izin verilse de, ilkeler gerektiği gibi dağıtılabilir.

### <a name="deploy-request"></a>Dağıtım isteği
Dağıtım isteği, bir ilkenin hazırlama veya üretim ortamına hızlı bir şekilde uygulanabileceğini sağlayan basit ve kolaylaştırılmış bir yordam sağlar. Dağıtım isteklerinin geçmişi, bu ortamlara uygulanan değişikliklerin izlenmesini kolaylaştırmak için sağlanır.

> [!NOTE]
> Yalnızca otomatik doğrulama ve hata algılama sistemimizi geçemeyecek isteklerin el ile incelenmesi ve onaylanması gerekir.

### <a name="rule-precedence"></a>Kural önceliği
Bir Ilkede yer alan kurallar genellikle listelendikleri sırada (yani, yukarıdan aşağıya) işlenir. İstek çakışan kurallarla eşleşiyorsa, işlenecek son kural öncelikli olur.

### <a name="policy-deployment-workflow"></a>İlke dağıtımı iş akışı
Bir ilkenin üretim veya hazırlama ortamına uygulanabileceğini içeren iş akışı aşağıda gösterilmiştir.

![İlke dağıtımı iş akışı](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Adım |Description |
|---------|---------|
|[Taslak Oluştur](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Bir taslak, içeriğinize yönelik isteklerin CDN tarafından nasıl işleneceğini tanımlayan bir kurallar kümesinden oluşur.     |
|Taslağı kilitle   |     Bir taslak sonlandırıldıktan sonra kilitleme ve salt okunurdur bir ilkeye dönüştürülmesi gerekir.    |
|[Dağıtım Isteği gönder](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Dağıtım isteği, bir ilkenin test ya da üretim trafiğine uygulanmasını sağlar.</br> <br>Hazırlama veya üretim ortamına bir dağıtım isteği gönderir.</br>     |
|Dağıtım Isteği Incelemesi   |    <br>Dağıtım isteği, otomatik doğrulama ve hata algılamayı daha düşük hale gelir.</br><br>Dağıtım isteklerinin çoğunluğu otomatik olarak onaylansa da, daha karmaşık ilkeler için el ile gözden geçirme gerekir.</br>   |
|İlke dağıtımı ([hazırlama](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Hazırlama ortamına bir dağıtım isteği onaylanınca, hazırlama ortamına bir ilke uygulanır. Bu ortam, bir ilkenin sahte site trafiğine karşı sınanmasını sağlar.</br><br>İlke canlı site trafiğine uygulanmaya hazırsa, üretim ortamı için yeni bir dağıtım isteği gönderilmesi gerekir.</br>      |
|İlke dağıtımı ([Üretim](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Üretim ortamına bir dağıtım isteği onaylandıktan sonra, üretim ortamına bir ilke uygulanır. Bu ortam, bir ilkenin CDN 'nin canlı trafiği nasıl işleyeceğini belirlemek için son yetkili görevi görmesini sağlar.     |
## <a name="syntax"></a>Syntax

Özel karakterlerin ele alındığı şekilde, eşleşme koşulunun veya özelliğin metin değerlerini nasıl işleydiğine göre farklılık gösterir. Bir eşleştirme koşulu veya özelliği, aşağıdaki yollarla metni yorumlayabilir:

- [**Değişmez değerler**](#literal-values)
- [**Joker karakter değerleri**](#wildcard-values)
- [**Normal ifadeler**](#regular-expressions)

### <a name="literal-values"></a>Değişmez değerler

Bir sabit değer olarak yorumlanan metin,% symbol özel durumuyla, eşleşmesi gereken değerin bir parçası olarak tüm özel karakterleri ele alır. Diğer bir deyişle, değişmez değer eşleştirme koşulu `\'*'\` yalnızca tam değer (yani,) bulunduğunda karşılanır `\'*'\` .

Bir yüzde sembolü, URL kodlamasını göstermek için kullanılır (örneğin, `%20` ).

### <a name="wildcard-values"></a>Joker karakter değerleri

Joker karakter değeri olarak yorumlanan metin özel karakterlere ek anlam atar. Aşağıdaki tabloda aşağıdaki karakter kümesinin nasıl yorumlanacağı açıklanmaktadır:

Karakter | Açıklama
----------|------------
\ | Bu tabloda belirtilen karakterlerden herhangi birini atlamak için ters eğik çizgi kullanılır. Bir ters eğik çizgi, önüne kaçılması gereken özel karakterden önce belirtilmelidir.<br/>Örneğin, aşağıdaki sözdizimi bir yıldız işaretine çıkar: `\*`
% | Bir yüzde sembolü, URL kodlamasını göstermek için kullanılır (örneğin, `%20` ).
\* | Yıldız işareti bir veya daha fazla karakteri temsil eden bir joker karakterdir.
Alan | Boşluk karakteri, bir eşleşme koşulunun belirtilen değerlerden veya desenlerden herhangi biri tarafından karşılanamayacağını gösterir.
deeri | Tek bir teklifin özel anlamı yoktur. Ancak, bir değerin değişmez değer olarak değerlendirilip değerlendirilmeyeceğini göstermek için bir dizi tek tırnak kullanılır. Aşağıdaki yollarla kullanılabilir:<br><br/>-Belirtilen değerin karşılaştırma değerinin herhangi bir bölümüyle eşleşmesi durumunda eşleşme koşulunun karşılanmasına izin verir.  Örneğin, `'ma'` aşağıdaki dizelerin herhangi biriyle eşleşir: <br/><br/>/Business/**ma** oython/asset.htm<br/>**ma** p.gif<br/>/Business/Template. **ma** p<br /><br />-Özel bir karakterin sabit karakter olarak belirtilmesini sağlar. Örneğin, bir boşluk karakterini tek tırnak (yani, veya) kümesi içinde çevreleyerek bir sabit boşluk karakteri belirtebilirsiniz `' '` `'sample value'` .<br/>-Boş değer belirtilmesini sağlar. Bir dizi tek tırnak (' ') belirterek boş bir değer belirtin.<br /><br/>**Önemli:**<br/>-Belirtilen değer bir joker karakter içermiyorsa, otomatik olarak değişmez değer olarak değerlendirilir, bu da tek tırnak kümesini belirtmek için gerekli değildir.<br/>-Ters eğik çizgi bu tablodaki başka bir karakterle eşleşmezse, bir dizi tek tırnak içinde belirtildiğinde yok sayılır.<br/>-Sabit karakter olarak özel bir karakter belirtmenin diğer bir yolu da bir ters eğik çizgi (yani,) kullanarak kaçış yöntemidir `\` .

### <a name="regular-expressions"></a>Normal ifadeler

Normal ifadeler, metin değeri içinde aranan bir model tanımlar. Normal ifade gösterimi çeşitli simgelere özgü anlamları tanımlar. Aşağıdaki tabloda, özel karakterlerin, normal ifadeleri destekleyen koşullar ve özellikler ile nasıl işlendiği gösterilmektedir.

Özel karakter | Description
------------------|------------
\ | Ters eğik çizgi, bu karakterin normal ifade anlamı yerine değişmez değer olarak işlenmesine neden olan karakteri izler. Örneğin, aşağıdaki sözdizimi bir yıldız işaretine çıkar: `\*`
% | Bir yüzde sembolünün anlamı, kullanımına bağlıdır.<br/><br/> `%{HTTPVariable}`: Bu sözdizimi bir HTTP değişkenini tanımlar.<br/>`%{HTTPVariable%Pattern}`: Bu sözdizimi bir HTTP değişkenini ve sınırlayıcı olarak tanımlamak için bir yüzde simgesi kullanır.<br />`\%`: Bir yüzde sembolünü kaçış, bir sabit değer olarak veya URL kodlamasının (örneğin,) kullanılmasına izin verir `\%20` .
\* | Bir yıldız işareti, önceki karakterin sıfır veya daha fazla kez eşleştirilmesini sağlar.
Alan | Boşluk karakteri genellikle sabit karakter olarak değerlendirilir.
deeri | Tek tırnak, sabit karakter olarak değerlendirilir. Tek tırnak kümesinin özel anlamı yoktur.

Normal ifadeleri destekleyen koşulların ve özelliklerin eşleşmesi, Perl uyumlu normal Ifadeler (PCRE) tarafından tanımlanan desenleri kabul eder.



## <a name="next-steps"></a>Sonraki adımlar

- [Kural altyapısı eşleştirme koşulları](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Kural altyapısı koşullu ifadeleri](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Kural altyapısı özellikleri](cdn-verizon-premium-rules-engine-reference-features.md)
- [Kural altyapısını kullanarak HTTP davranışını geçersiz kılma](cdn-verizon-premium-rules-engine.md)
- [Azure CDN genel bakış](cdn-overview.md)
