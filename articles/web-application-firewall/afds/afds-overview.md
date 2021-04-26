---
title: Azure ön kapıda Azure Web uygulaması güvenlik duvarı nedir?
description: Azure ön kapılarındaki Azure Web uygulaması güvenlik duvarı 'nın Web uygulamalarınızı kötü amaçlı saldırılardan nasıl koruduğunu öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 0e3073c491b251a1dbb505d4656de9c6a1e0ac7f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048502"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure ön kapıda Azure Web uygulaması güvenlik duvarı

Azure ön kapısının Azure Web uygulaması güvenlik duvarı (WAF), Web uygulamalarınız için merkezi koruma sağlar. WAF, Web hizmetlerinizi yaygın güvenlik açıklarından ve güvenlik açıklarına karşı Savunlar. Kullanıcılarınız için hizmetinizi yüksek oranda kullanılabilir tutar ve uyumluluk gereksinimlerini karşılamanıza yardımcı olur.

Ön kapıda WAF genel ve merkezi bir çözümdür. Dünyanın dört bir yanındaki Azure ağ Edge konumlarında dağıtılır. WAF etkin Web uygulamaları, ön kapıda ağ ucunda sunulan tüm gelen istekleri inceleyin. 

WAF, Sanal ağınızı girmeden önce saldırı kaynaklarına yakın kötü amaçlı saldırıları önler. Performans performansınızı etkilemeden genel koruma kazanın. Bir WAF ilkesi, aboneliğinizdeki herhangi bir ön kapılı profile kolayca bağlanır. Yeni kurallar dakikalar içinde dağıtılabilir ve bu sayede tehdit desenlerini değiştirmek için hızlıca yanıt verebilirsiniz.

![Azure web uygulaması güvenlik duvarı](../media/overview/wafoverview.png)

Azure ön kapı [Önizleme sürümünde iki yeni SKU](../../frontdoor/standard-premium/overview.md)tanıtır: ön kapı standardı ve ön kapı Premium SKU 'su. WAF, ön kapı Premium SKU 'SU ile tam yetenekler ile yerel olarak tümleşiktir. Ön kapı standart SKU 'SU için yalnızca [özel kurallar](#custom-authored-rules) desteklenir.

## <a name="waf-policy-and-rules"></a>WAF ilkesi ve kuralları

Bir [WAF ilkesi](waf-front-door-create-portal.md) yapılandırabilir ve bu ilkeyi koruma için bir veya daha fazla ön kapı ön uçları ile ilişkilendirebilirsiniz. Bir WAF ilkesi, iki tür güvenlik kuralından oluşur:

- Müşteri tarafından yazılan özel kurallar.

- Azure tarafından yönetilen önceden yapılandırılmış bir kural kümesi koleksiyonu olan yönetilen kural kümeleri.

Her ikisi de varsa, yönetilen bir kural kümesindeki kuralları işlemeden önce özel kurallar işlenir. Bir kural eşleştirme koşulu, öncelik ve bir eylemden oluşur. Desteklenen eylem türleri şunlardır: ızın ver, engelle, LOG ve REDIRECT. Yönetilen ve özel kuralları birleştirerek, belirli uygulama koruma gereksinimlerinizi karşılayan tam olarak özelleştirilmiş bir ilke oluşturabilirsiniz.

İlke içindeki kurallar öncelik sırasına göre işlenir. Öncelik, işlenecek kuralların sırasını tanımlayan benzersiz bir tamsayıdır. Daha küçük tamsayı değeri daha yüksek bir öncelik gösterir ve bu kurallar daha yüksek bir tamsayı değerine sahip kurallardan önce değerlendirilir. Bir kural eşleştiğinde, kuralda tanımlanan ilgili eylem isteğe uygulanır. Bu tür bir eşleşme işlendiğinde, daha düşük öncelikler olan kurallar daha fazla işlenmez.

Ön kapıya göre sunulan bir Web uygulamasının aynı anda kendisiyle ilişkili yalnızca bir WAF ilkesi olabilir. Ancak, kendisiyle ilişkilendirilmiş bir WAF ilkesi olmadan bir ön kapı yapılandırmasına sahip olabilirsiniz. Bir WAF ilkesi varsa, dünyanın tamamında tutarlı güvenlik ilkeleri sağlamak için tüm kenar konumlarımızla çoğaltılır.

## <a name="waf-modes"></a>WAF modları

WAF ilkesi aşağıdaki iki modda çalışacak şekilde yapılandırılabilir:

- **Algılama modu:** Algılama modunda çalıştırıldığında WAF, izleyicileri dışında başka bir eylem almaz ve isteği ve eşleşen WAF kuralını WAF günlüklerine kaydeder. Ön kapı için günlüğü tanılamayı açabilirsiniz. Portalı kullandığınızda **Tanılama** bölümüne gidin.

- **Önleme modu:** Önleme modunda, bir istek bir kuralla eşleşiyorsa WAF belirtilen eylemi alır. Bir eşleşme bulunursa, daha düşük önceliğe sahip başka kurallar değerlendirilmez. Tüm eşleşen istekler de WAF günlüklerine kaydedilir.

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

- **IP izin verilenler listesi ve Engellenenler listesi:** Web uygulamalarınıza erişimi, istemci IP adresleri veya IP adresi aralıkları listesine göre denetleyebilirsiniz. Hem IPv4 hem de IPv6 adres türleri desteklenir. Bu liste, kaynak IP 'nin listedeki bir IP ile eşleştiği istekleri engelleyecek ya da bu isteklere izin verecek şekilde yapılandırılabilir.

- **Coğrafi tabanlı erişim denetimi:** Web uygulamalarınıza erişimi, bir istemcinin IP adresiyle ilişkili olan ülke koduna göre denetleyebilirsiniz.

- **Http parametreleri tabanlı erişim denetimi:** HTTP/HTTPS istek parametrelerinde dize eşleştirmelerle ilgili kuralları temel alabilirsiniz.  Örneğin, sorgu dizeleri, POST bağımsız değişkenleri, Istek URI 'SI, istek üst bilgisi ve Istek gövdesi.

- **Yöntem tabanlı erişim denetimi iste:** İsteğe bağlı kurallar, isteğin HTTP isteği yöntemine göre yapılır. Örneğin, GET, PUT veya HEAD.

- **Boyut kısıtlaması:** Sorgu dizesi, URI veya istek gövdesi gibi bir isteğin belirli bölümlerinin uzunluklarına göre kurallar temel alabilirsiniz.

- **Hız sınırlandırma kuralları:** Bir hız denetim kuralı, anormal yüksek trafiği herhangi bir istemci IP 'sinden sınırlayabilmektir. Bir dakikalık süre boyunca istemci IP 'sinden izin verilen Web isteği sayısında bir eşik yapılandırabilirsiniz. Bu kural, bir istemci IP 'sinden tüm istekleri engellemeye izin veren veya engelleyen bir IP listesi tabanlı izin verme/engelleme özel kuralından farklıdır. Hız sınırları, ayrıntılı hız denetimi için HTTP (S) parametre eşleşmeleri gibi ek eşleşme koşullarıyla birleştirilebilir.

### <a name="azure-managed-rule-sets"></a>Azure tarafından yönetilen kural kümeleri

Azure tarafından yönetilen kural kümeleri, yaygın bir güvenlik tehditleri kümesine karşı koruma dağıtmanın kolay bir yolunu sağlar. Bu tür RuleSets 'ler Azure tarafından yönetildiğinden, yeni saldırı imzalarından korunmak için kurallar gerektiği şekilde güncelleştirilir. Azure tarafından yönetilen varsayılan kural kümesi, aşağıdaki tehdit kategorilerine karşı kuralları içerir:

- Siteler arası betik
- Java saldırıları
- Yerel dosya ekleme
- PHP ekleme saldırıları
- Uzak komut yürütme
- Uzak dosya ekleme
- Oturum armatürü
- SQL ekleme koruması
- Protokol saldırganlar

Kural kümesine yeni saldırı imzaları eklendiğinde varsayılan kural kümesinin sürüm numarası artar.
Varsayılan kural kümesi, WAF ilkelerinizin algılama modunda varsayılan olarak etkindir. Varsayılan kural kümesindeki kuralları uygulama gereksinimlerinizi karşılayacak şekilde etkinleştirebilir veya devre dışı bırakabilirsiniz. Kural başına belirli eylemleri (ızın verme/engelleme/yeniden yönlendirme/günlük) da ayarlayabilirsiniz.

Bazen bir WAF değerlendirmesinden belirli istek özniteliklerini atlamanızı gerekebilir. Ortak bir örnek, kimlik doğrulaması için kullanılan Active Directory eklenen belirteçlerdir. Yönetilen bir kural, kural grubu veya tüm kural kümesi için bir dışlama listesi yapılandırabilirsiniz.  

Varsayılan eylem ENGELLENECEK. Ayrıca, varsayılan kural kümesindeki önceden yapılandırılmış kuralların herhangi birini atlamak istiyorsanız, özel kurallar aynı WAF ilkesinde yapılandırılabilir.

Varsayılan kural kümesindeki kuralların değerlendirilmesinden önce özel kurallar her zaman uygulanır. Bir istek özel bir kuralla eşleşiyorsa, buna karşılık gelen kural eylemi uygulanır. İstek engellendi ya da arka uca geçirildi. Başka özel kurallar veya varsayılan kural kümesindeki kurallar işlenmez. Ayrıca, WAF ilkelerinizin varsayılan kural kümesini de kaldırabilirsiniz.

### <a name="bot-protection-rule-set-preview"></a>Bot koruma kuralı kümesi (Önizleme)

Yönetilen bir bot koruma kuralı kümesini, bilinen bot kategorilerinin istekleri üzerinde özel eylemler alacak şekilde etkinleştirebilirsiniz. 

Desteklenen üç bot kategorisi vardır: Hatalı, Iyi ve bilinmiyor. Bot imzaları, WAF platformu tarafından yönetilir ve dinamik olarak güncelleştirilir.

Kötü amaçlı olan botlar, kimliklerini hatalı yapan kötü amaçlı IP adreslerinden ve botları içerir. Kötü amaçlı IP adresleri, Microsoft Threat Intelligence akışından kaynaklıdır ve her saat güncelleştirilir. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) , Microsoft Threat Intelligence 'ı güçlendirir ve Azure Güvenlik Merkezi dahil birden çok hizmet tarafından kullanılır.

İyi botlar, doğrulanan arama altyapılarını içerir. Bilinmeyen Kategoriler, kendisini botları olarak tanımlamış ek bot gruplarını içerir. Örneğin, Pazar çözümleyici, akış fetchers ve veri toplama aracıları. 

Bilinmeyen botlar, ek doğrulama yapılmadan yayımlanmış Kullanıcı aracıları aracılığıyla sınıflandırılır. Farklı botların türlerini engellemek, izin vermek, günlüğe kaydetmek veya yeniden yönlendirmek için özel eylemler ayarlayabilirsiniz.

![Bot koruma kuralı kümesi](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Bot koruma kuralı kümesi şu anda genel önizleme aşamasındadır ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.  Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bot koruması etkinleştirilirse, bot kurallarıyla eşleşen gelen istekler FrontdoorWebApplicationFirewallLog günlüğüne kaydedilir. WAF günlüklerine bir depolama hesabından, Olay Hub 'ından veya Log Analytics 'e erişebilirsiniz.

## <a name="configuration"></a>Yapılandırma

Azure portal, REST API 'Leri, Azure Resource Manager şablonları ve Azure PowerShell kullanarak tüm WAF kural türlerini yapılandırabilir ve dağıtabilirsiniz.

## <a name="monitoring"></a>İzleme

Ön kapıda WAF için izleme, uyarıları izlemek ve trafik eğilimlerini kolayca izlemek için Azure Izleyici ile tümleşiktir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Application Gateway Web uygulaması güvenlik duvarı](../ag/ag-overview.md) hakkında bilgi edinin