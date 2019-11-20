---
title: Azure ön kapıda Azure Web uygulaması güvenlik duvarı nedir?
description: Azure ön kapılarındaki Azure Web uygulaması güvenlik duvarı 'nın Web uygulamalarınızı kötü amaçlı saldırılardan nasıl koruduğunu öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 09/28/2019
ms.author: victorh
ms.openlocfilehash: ce70260c6033d22b20675d6f3872c2ffa6368252
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502355"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure ön kapıda Azure Web uygulaması güvenlik duvarı

Azure ön kapısının Azure Web uygulaması güvenlik duvarı (WAF), Azure ön kapısı kullanılarak küresel olarak sunulan Web uygulamalarınız için merkezi koruma sağlar. Bu çözüm, web hizmetlerinizi yaygın açıklardan yararlanma yazılımlarına ve güvenlik açıklarına karşı koruyup hizmetinizi kullanıcılarınız için yüksek oranda kullanılabilir tutmanın yanı sıra uyumluluk gereksinimlerini karşılamanıza yardımcı olmak üzere tasarlanmıştır ve bu amaçlara uygun şekilde işletilmektedir.

Ön kapıda WAF genel ve merkezi bir çözümdür. Bu, dünyanın her yerindeki Azure ağ Edge konumlarına ve ön kapıya göre dağıtılan bir WAF özellikli Web uygulamasına yönelik her gelen isteğe bağlı olarak ağ ucunda denetlenir. Bu, WAF 'nin, Sanal ağınızı girmeden önce, saldırı kaynaklarına yakın kötü amaçlı saldırıları engellemesine ve performanstan ödün vermeden genel koruma sağlamasına olanak tanır. Bir WAF ilkesi, aboneliğinizdeki herhangi bir ön kapı profiline kolayca bağlanabilir ve yeni kurallar dakikalar içinde dağıtılabilir ve bu da, tehdit desenlerine hızla yanıt vermenize olanak tanır.

![Azure Web uygulaması güvenlik duvarı](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>WAF ilkesi ve kuralları

Bir WAF ilkesi yapılandırabilir ve bu ilkeyi koruma için bir veya daha fazla ön kapı ön uçları ile ilişkilendirebilirsiniz. Bir WAF ilkesi, iki tür güvenlik kuralından oluşur:

- Müşteri tarafından yazılan özel kurallar.

- Azure tarafından yönetilen önceden yapılandırılmış bir kural kümesi koleksiyonu olan yönetilen kural kümeleri.

Her ikisi de varsa, yönetilen bir kural kümesindeki kuralları işlemeden önce özel kurallar işlenir. Bir kural eşleştirme koşulu, öncelik ve bir eylemden oluşur. Desteklenen eylem türleri şunlardır: ızın ver, engelle, LOG ve REDIRECT. Yönetilen ve özel kuralları birleştirerek, belirli uygulama koruma gereksinimlerinizi karşılayan tam olarak özelleştirilmiş bir ilke oluşturabilirsiniz.

Bir ilke içindeki kurallar, önceliğinin, işlenen kuralların sırasını tanımlayan benzersiz bir tamsayı olduğu öncelikli bir sırada işlenir. Daha küçük tamsayı değeri daha yüksek bir öncelik gösterir ve bunlar daha yüksek bir tamsayı değerine sahip kurallardan önce değerlendirilir. Bir kural eşleştiğinde, kuralda tanımlanan ilgili eylem isteğe uygulanır. Bu tür bir eşleşme işlendiğinde, daha düşük öncelikler olan kurallar daha fazla işlenmez.

Ön kapıya göre sunulan bir Web uygulamasının aynı anda kendisiyle ilişkili yalnızca bir WAF ilkesi olabilir. Ancak, kendisiyle ilişkilendirilmiş bir WAF ilkesi olmadan bir ön kapı yapılandırmasına sahip olabilirsiniz. Bir WAF ilkesi varsa, dünyanın dört bir yanındaki güvenlik ilkelerinde tutarlılık sağlamak için tüm kenar konumlarımızla çoğaltılır.

## <a name="waf-modes"></a>WAF modları

WAF ilkesi aşağıdaki iki modda çalışacak şekilde yapılandırılabilir:

- **Algılama modu:** Algılama modunda çalıştırıldığında WAF, izleyicileri dışında başka bir eylem almaz ve isteği ve eşleşen WAF kuralını WAF günlüklerine kaydeder. Ön kapı için günlük tanılamayı açabilirsiniz (portal kullanırken, bu, Azure portal) **Tanılama** bölümüne giderek elde edilebilir.

- **Önleme modu:** Önleme modunda çalışacak şekilde yapılandırıldığında, bir istek bir kuralla eşleşiyorsa ve bir eşleşme bulunursa, daha düşük önceliğe sahip başka hiçbir kural hesaplanmazsa, WAF belirtilen eylemi alır. Tüm eşleşen istekler de WAF günlüklerine kaydedilir.

## <a name="waf-actions"></a>WAF eylemleri

WAF müşterileri, bir isteğin bir kuralla eşleştiğinde bir eylemden birini çalıştırmayı seçebilir:

- **Izin ver:**  İstek WAF üzerinden geçer ve arka uca iletilir. Daha düşük öncelikli kurallar, bu isteği engelleyebilir.
- **Engelle:** İstek engellendi ve WAF, isteği arka uca iletmeksizin istemciye bir yanıt gönderiyor.
- **Günlüğe kaydet:**  İstek WAF günlüklerinde günlüğe kaydedilir ve WAF, düşük öncelikli kuralları değerlendirmeye devam eder.
- **Yeniden yönlendir:** WAF, isteği belirtilen URI 'ye yeniden yönlendirir. Belirtilen URI bir ilke düzeyi ayarıdır. Yapılandırıldıktan sonra, **yeniden yönlendirme** eylemiyle eşleşen tüm ISTEKLER bu URI 'ye gönderilir.

## <a name="waf-rules"></a>WAF kuralları

Bir WAF ilkesi, müşteri tarafından yazılan ve yönetilen RuleSets, Azure tarafından yönetilen önceden yapılandırılmış kural kümesi olan özel kuralların bulunduğu iki tür güvenlik kuralı içerebilir.

### <a name="custom-authored-rules"></a>Özel yazılan kurallar

WAF özel kurallarını aşağıdaki gibi yapılandırabilirsiniz:

- **IP izin verilenler listesi ve Engellenenler listesi:** İstemci IP adresleri veya IP adresi aralıkları listesine göre Web uygulamalarınıza erişimi denetlemek için özel kurallar yapılandırabilirsiniz. Hem IPv4 hem de IPv6 adres türleri desteklenir. Bu liste, kaynak IP 'nin listedeki bir IP ile eşleştiği istekleri engelleyecek ya da bu isteklere izin verecek şekilde yapılandırılabilir.

- **Coğrafi tabanlı erişim denetimi:** Web uygulamalarınıza erişimi, bir istemcinin IP adresiyle ilişkili ülke koduna göre denetlemek için özel kurallar yapılandırabilirsiniz.

- **Http parametreleri tabanlı erişim denetimi:** Sorgu dizeleri, POST args, Istek URI 'SI, istek üstbilgisi ve Istek gövdesi gibi HTTP/HTTPS istek parametreleri ile eşleşen dize tabanlı özel kurallar yapılandırabilirsiniz.

- **Yöntem tabanlı erişim denetimi iste:** İstek al, koy veya HEAD gibi HTTP isteği yöntemine göre özel kurallar yapılandırabilirsiniz.

- **Boyut kısıtlaması:** Sorgu dizesi, URI veya istek gövdesi gibi bir isteğin belirli bölümlerinin uzunluklarına göre özel kurallar yapılandırabilirsiniz.

- **Hız sınırlandırma kuralları:** Bir hız denetim kuralı, anormal yüksek trafiği herhangi bir istemci IP 'sinden sınırlayabilmektir. Bir dakikalık süre boyunca istemci IP 'sinden izin verilen Web isteği sayısında bir eşik yapılandırabilirsiniz. Bu, tümüne izin veren veya bir istemci IP 'sinden tüm istekleri engelleyen bir IP listesi tabanlı izin verme/engelleme özel kuralından farklıdır. Hız sınırlaması, ayrıntılı hız denetimi için HTTP (S) parametreleri ile eşleşen ek eşleşme koşullarıyla birleştirilebilir.

### <a name="azure-managed-rule-sets"></a>Azure tarafından yönetilen kural kümeleri

Azure tarafından yönetilen kural kümeleri, yaygın bir güvenlik tehditleri kümesine karşı koruma dağıtmanın kolay bir yolunu sağlar. Bu tür RuleSets 'ler Azure tarafından yönetildiğinden, yeni saldırı imzalarından korunmak için kurallar gerektiği şekilde güncelleştirilir. Genel önizlemede, Azure tarafından yönetilen varsayılan kural kümesi, aşağıdaki tehdit kategorilerine karşı kuralları içerir:

- Siteler arası betik oluşturma
- Java saldırıları
- Yerel dosya ekleme
- PHP ekleme saldırıları
- Uzak komut yürütme
- Uzaktan dosya ekleme
- Oturum armatürü
- SQL ekleme koruması
- Protokol saldırganlar

Kural kümesine yeni saldırı imzaları eklendiğinde varsayılan kural kümesinin sürüm numarası artacaktır.
Varsayılan kural kümesi, WAF ilkelerinizin algılama modunda varsayılan olarak etkindir. Varsayılan kural kümesindeki kuralları uygulama gereksinimlerinizi karşılayacak şekilde etkinleştirebilir veya devre dışı bırakabilirsiniz. Kural başına belirli eylemleri (ızın verme/engelleme/yeniden yönlendirme/günlük) da ayarlayabilirsiniz. Varsayılan eylem ENGELLENECEK. Ayrıca, varsayılan kural kümesindeki önceden yapılandırılmış kuralların herhangi birini atlamak istiyorsanız, özel kurallar aynı WAF ilkesinde yapılandırılabilir.
Varsayılan kural kümesindeki kuralların değerlendirilmesinden önce özel kurallar her zaman uygulanır. Bir istek özel bir kuralla eşleşiyorsa, buna karşılık gelen kural eylemi uygulanır ve daha fazla özel kural veya varsayılan kural kümesindeki kuralların çağrılması gerekmeden istek engellenir veya arka uca geçirilir. Ayrıca, WAF ilkelerinizin varsayılan kural kümesini kaldırma seçeneğiniz de vardır.


### <a name="bot-protection-rule-preview"></a>Bot koruma kuralı (Önizleme)

WAF 'nizin bilinen kötü amaçlı IP adreslerinden gelen isteklere özel eylemler kazanması için yönetilen bir bot koruma kuralı kümesi etkinleştirilebilir. IP adresleri Microsoft Threat Intelligence akışından kaynaklıdır. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) , Microsoft Threat Intelligence 'ı güçlendirir ve Azure Güvenlik Merkezi dahil birden çok hizmet tarafından kullanılır.

![Bot koruma kuralı kümesi](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Bot koruma kuralı kümesi şu anda genel önizleme aşamasındadır ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.  Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bot koruması etkinleştirilmişse, kötü amaçlı botlar istemci IP 'Leri ile eşleşen gelen istekler FrontdoorWebApplicationFirewallLog günlüğüne kaydedilir. WAF günlüklerine depolama hesabından, Olay Hub 'ından veya Log Analytics 'ten erişebilirsiniz. 

## <a name="configuration"></a>Yapılandırma

Tüm WAF kural türlerini yapılandırmak ve dağıtmak Azure portal, REST API 'Leri, Azure Resource Manager şablonları ve Azure PowerShell kullanarak tam olarak desteklenmektedir.

## <a name="monitoring"></a>İzleme

Ön kapıda WAF için izleme, uyarıları izlemek ve trafik eğilimlerini kolayca izlemek için Azure Izleyici ile tümleşiktir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Application Gateway Web uygulaması güvenlik duvarı](../ag/ag-overview.md) hakkında bilgi edinin