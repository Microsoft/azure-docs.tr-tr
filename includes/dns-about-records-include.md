---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 261ae22348cd82b129727261c619727917e19c96
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832043"
---
### <a name="record-names"></a>Kayıt adları

Azure DNS’de, kayıtlar göreli adlar kullanılarak belirtilir. *Tam* etki alanı adında (FQDN) bölge adı varken *göreli* adda bu yoktur. Örneğin, `www` göreli kayıt adı bölge `contoso.com`, `www.contoso.com`tam kayıt adını verir.

*Tepe* kaydı, bir DNS bölgesinin kökündeki (veya *tepesindeki*) DNS kaydıdır. Örneğin, DNS bölgesinde `contoso.com`, bir tepesinde kaydı da `contoso.com` tam ada sahiptir (buna bazen *çıplak* etki alanı denir).  Kurala göre, '\@' göreli adı tepesinde kayıtlarını temsil etmek için kullanılır.

### <a name="record-types"></a>Kayıt türleri

Her DNS kaydında bir ad ve bir tür vardır. Kayıtlar, içerdikleri verilere göre çeşitli türlerde düzenlenmiştir. En yaygın tür olan "A" kaydı bir adı bir IPv4 adresiyle eşleştirir. Başka bir sık kullanılan tür olan "MX" kaydıysa bir adı bir posta sunucusuyla eşleştirir.

Azure DNS, tüm ortak DNS kayıt türlerini destekler: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV ve TXT. [SPF kayıtlarının TXT kaydı kullanılarak gösterildiğini](../articles/dns/dns-zones-records.md#spf-records) unutmayın.

### <a name="record-sets"></a>Kayıt kümeleri

Bazen, verilen ada ve türe sahip birden fazla DNS kaydı oluşturmanız gerekebilir. Örneğin, "www.contoso.com" web sitesinin iki farklı IP adresinde barındırıldığını var sayalım. Web sitesine, her IP adresi için bir tane olmak üzere iki farklı A kaydı gerekir. Bu bir kayıt kümesi örneğidir:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS tüm DNS kayıtlarını *kayıt kümeleri* kullanarak yönetir. Kayıt kümesi (kaynak *kayıt* kümesi olarak da bilinir), bir bölgede yer alan aynı ada sahip ve aynı türde olan DNS kayıtlarının koleksiyonudur. Çoğu kayıt kümesinde tek bir kayıt bulunur. Ancak yukarıdaki gibi birden fazla kayıt içeren kayıt kümeleri de kullanılabilir.

Örneğin, "contoso.com" bölgesinde "134.170.185.46" adresini işaret eden bir "www" A kaydı oluşturduğunuzu var sayalım (yukarıdaki ilk kayıt).  ikinci kaydı oluşturmak için yeni bir kayıt kümesi oluşturma yerine bu kaydı var olan kayıt kümesine ekleyebilirsiniz.

SOA ve CNAME kaydı kümeleri farklıdır. DNS standartları bu türler için aynı ada sahip birden fazla kayda izin vermediği için bu kayıt kümeleri yalnızca bir kayıt içerebilir.