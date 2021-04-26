---
title: Azure CDN Azure Web uygulaması güvenlik duvarı nedir?
description: Azure CDN hizmetindeki Azure Web uygulaması güvenlik duvarının Web uygulamalarınızı kötü amaçlı saldırılara karşı nasıl koruduğunu öğrenin.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: cd1f21f150094094a6bf856b04a64a02963f35a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521722"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network-from-microsoft"></a>Microsoft 'tan Azure Content Delivery Network Azure Web uygulaması güvenlik duvarı

Microsoft 'un Azure Content Delivery Network (CDN) üzerinde Azure Web uygulaması güvenlik duvarı (WAF), Web içeriğiniz için merkezi koruma sağlar. WAF, Web hizmetlerinizi yaygın güvenlik açıklarından ve güvenlik açıklarına karşı Savunlar. Kullanıcılarınız için hizmetinizi yüksek oranda kullanılabilir tutar ve uyumluluk gereksinimlerini karşılamanıza yardımcı olur.

> [!IMPORTANT]
> Microsoft 'tan Azure CDN WAF Şu anda genel önizlemededir ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.  Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure CDN WAF, genel ve merkezi bir çözümdür. Dünyanın dört bir yanındaki Azure ağ Edge konumlarında dağıtılır. WAF, kötü amaçlı saldırıları, kaynak kaynağınıza ulaşmadan önce saldırı kaynaklarına yakın şekilde engeller. Performans performansınızı etkilemeden genel koruma kazanın. 

Bir WAF ilkesi, aboneliğinizdeki herhangi bir CDN uç noktasına kolayca bağlanır. Yeni kurallar dakikalar içinde dağıtılabilir ve bu sayede tehdit desenlerini değiştirmek için hızlıca yanıt verebilirsiniz.

![Azure web uygulaması güvenlik duvarı](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF ilkesi ve kuralları

Bir WAF ilkesi yapılandırabilir ve bu ilkeyi koruma için bir veya daha fazla CDN uç noktası ile ilişkilendirebilirsiniz. Bir WAF ilkesi, iki tür güvenlik kuralından oluşur:

- oluşturabileceğiniz özel kurallar.

- Azure tarafından yönetilen önceden yapılandırılmış kuralların bir koleksiyonu olan yönetilen kural kümeleri.

Her ikisi de varsa, yönetilen bir kural kümesindeki kuralları işlemeden önce özel kurallar işlenir. Bir kural eşleştirme koşulu, öncelik ve bir eylemden oluşur. Desteklenen eylem türleri şunlardır: *Izin ver*, *Engelle*, *log* ve *REDIRECT*. Yönetilen ve özel kuralları birleştirerek, belirli uygulama koruma gereksinimlerinizi karşılayan tam olarak özelleştirilmiş bir ilke oluşturabilirsiniz.

İlke içindeki kurallar öncelik sırasına göre işlenir. Öncelik, işlenecek kuralların sırasını tanımlayan benzersiz bir sayıdır. Daha küçük sayılar daha yüksek önceliktir ve bu kurallar daha büyük bir değere sahip kurallardan önce değerlendirilir. Bir kural eşleştiğinde, kuralda tanımlanan ilgili eylem isteğe uygulanır. Bu tür bir eşleşme işlendiğinde, daha düşük öncelikler olan kurallar daha fazla işlenmez.

Azure CDN barındırılan bir Web uygulamasının aynı anda kendisiyle ilişkili yalnızca bir WAF ilkesi olabilir. Ancak, kendisiyle ilişkilendirilmiş WAF ilkeleri olmadan bir CDN uç noktanız olabilir. Bir WAF ilkesi varsa, dünyanın tamamında tutarlı güvenlik ilkeleri sağlamak için tüm kenar konumlarımızla çoğaltılır.

## <a name="waf-modes"></a>WAF modları

WAF ilkesi aşağıdaki iki modda çalışacak şekilde yapılandırılabilir:

- *Algılama modu*: algılama modunda çalıştırıldığında WAF, izleyicileri dışında başka bir eylem almaz ve isteği ve eşleşen WAF KURALıNı WAF günlüklerine kaydeder. CDN için günlük tanılamayı açabilir. Portalı kullandığınızda **Tanılama** bölümüne gidin.

- *Önleme modu*: önleme modunda, bir istek bir kuralla eşleşiyorsa, WAF belirtilen eylemi alır. Bir eşleşme bulunursa, daha düşük önceliğe sahip başka hiçbir kural değerlendirilmez. Tüm eşleşen istekler de WAF günlüklerine kaydedilir.

## <a name="waf-actions"></a>WAF eylemleri

Bir istek bir kuralla eşleştiğinde, aşağıdaki eylemlerden birini seçebilirsiniz:

- *Izin ver*: Istek WAF üzerinden geçirilir ve arka uca iletilir. Daha düşük öncelikli kurallar, bu isteği engelleyebilir.
- *Engelle*: istek engellendi ve WAF, isteği arka uca iletmeksizin istemciye bir yanıt gönderiyor.
- *GNLK*: Istek WAF günlüklerinde günlüğe kaydedilir ve WAF, düşük öncelikli kuralları değerlendirmeye devam eder.
- *Redirect*: WAF, ISTEğI belirtilen URI 'ye yeniden yönlendirir. Belirtilen URI bir ilke düzeyi ayarıdır. Yapılandırıldıktan sonra, *yeniden yönlendirme* eylemiyle eşleşen tüm ISTEKLER bu URI 'ye gönderilir.

## <a name="waf-rules"></a>WAF kuralları

Bir WAF ilkesi iki tür güvenlik kuralı içerebilir:

- *özel kurallar*: kendi oluşturduğunuz kurallar 
- *yönetilen kural kümeleri*: Azure yönetilen önceden yapılandırılmış kurallar kümesi

### <a name="custom-rules"></a>Özel kurallar

Özel kuralların eşleşme kuralları ve oran denetim kuralları olabilir.

Aşağıdaki özel eşleşme kurallarını yapılandırabilirsiniz:

- *IP izin listesi ve Engellenenler listesi*: istemci IP ADRESLERI veya IP adresi aralıkları listesine göre Web uygulamalarınıza erişimi denetleyebilirsiniz. Hem IPv4 hem de IPv6 adres türleri desteklenir. Bu liste, kaynak IP 'nin listedeki bir IP ile eşleştiği istekleri engelleyecek ya da bu isteklere izin verecek şekilde yapılandırılabilir.

- *Coğrafi tabanlı erişim denetimi*: Web uygulamalarınıza erişimi, BIR istemcinin IP adresiyle ilişkili olan ülke koduna göre denetleyebilirsiniz.

- *Http parametreleri tabanlı erişim denetimi*: http/https istek parametrelerinde dize eşleştirmelerle ilgili kuralları temel alabilirsiniz.  Örneğin, sorgu dizeleri, POST bağımsız değişkenleri, Istek URI 'SI, istek üst bilgisi ve Istek gövdesi.

- *Yöntem tabanlı erişim denetimi iste*: KURALLARı isteğin HTTP istek yöntemine dayandırın. Örneğin, GET, PUT veya HEAD.

- *Boyut kısıtlaması*: bir isteğin, sorgu dizesi, URI veya istek gövdesi gibi belirli bölümlerinin uzunluklarıyla kuralları temel alabilirsiniz.

Bir hız denetim kuralı, herhangi bir istemci IP adresinden anormal yüksek trafiği sınırlar.

- *Hız sınırlandırma kuralları*: bir dakikalık süre boyunca istemci IP adresinden izin verilen Web isteği sayısında bir eşik yapılandırabilirsiniz. Bu kural, bir istemci IP adresinden tüm istekleri izin veren veya engelleyen bir IP listesi tabanlı izin verme/engelleme özel kuralından farklıdır. Hız sınırları, ayrıntılı hız denetimi için HTTP (S) parametre eşleşmeleri gibi ek eşleşme koşullarıyla birleştirilebilir.

### <a name="azure-managed-rule-sets"></a>Azure tarafından yönetilen kural kümeleri

Azure tarafından yönetilen kural kümeleri, yaygın bir güvenlik tehditleri kümesine karşı koruma dağıtmanın kolay bir yolunu sağlar. Bu RuleSets 'ler Azure tarafından yönetildiğinden, yeni saldırı imzalarından korunmak için kurallar gerektiği şekilde güncelleştirilir. Azure yönetilen varsayılan kural kümesi, aşağıdaki tehdit kategorilerine karşı kuralları içerir:

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
Varsayılan kural kümesi, WAF ilkelerinizin *algılama* modunda varsayılan olarak etkindir. Varsayılan kural kümesindeki kuralları uygulama gereksinimlerinizi karşılayacak şekilde etkinleştirebilir veya devre dışı bırakabilirsiniz. Kural başına belirli eylemleri (ızın verme/engelleme/yeniden yönlendirme/günlük) da ayarlayabilirsiniz. Yönetilen varsayılan kural kümesi için varsayılan eylem *blok*' dır.

Varsayılan kural kümesindeki kuralların değerlendirilmesinden önce özel kurallar her zaman uygulanır. Bir istek özel bir kuralla eşleşiyorsa, buna karşılık gelen kural eylemi uygulanır. İstek engellendi ya da arka uca geçirildi. Başka özel kurallar veya varsayılan kural kümesindeki kurallar işlenmez. Ayrıca, WAF ilkelerinizin varsayılan kural kümesini de kaldırabilirsiniz.

## <a name="configuration"></a>Yapılandırma

Azure portal, REST API 'Leri, Azure Resource Manager şablonları ve Azure PowerShell kullanarak tüm WAF kural türlerini yapılandırabilir ve dağıtabilirsiniz.

## <a name="monitoring"></a>İzleme

CDN ile WAF izleme, uyarıları izlemek ve trafik eğilimlerini kolayca izlemek için Azure Izleyici ile tümleşiktir.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure portal kullanarak Azure CDN bir WAF ilkesi oluşturma](waf-cdn-create-portal.md)
