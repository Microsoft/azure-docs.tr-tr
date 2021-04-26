---
title: Azure 'da ters DNS 'ye genel bakış-Azure DNS
description: Bu öğrenme yolunda, ters DNS 'in nasıl çalıştığını ve Azure 'da nasıl kullanılabileceğini öğrenmeye başlayın
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965621"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Azure 'da ters DNS ve desteğe genel bakış

Bu makalede, ters DNS 'in nasıl çalıştığı ve Azure 'da desteklenen ters DNS senaryolarıyla genel bir bakış sunulmaktadır.

## <a name="what-is-reverse-dns"></a>Ters DNS nedir?

Geleneksel DNS kayıtları bir DNS adından (' www.contoso.com ' gibi) bir IP adresine (64.4.6.100 gibi) bir eşlemeyi etkinleştirir.  Ters DNS, bir IP adresinin (64.4.6.100) bir ada (' www.contoso.com ') çevrilmesini sağlar.

Ters DNS kayıtları çeşitli durumlarda kullanılır. Örneğin, ters DNS kayıtları, e-posta iletisi göndereni doğrulanırken çok sayıda e-posta gönderici ortamında yaygın olarak kullanılır.  Alıcı posta sunucusu, gönderen sunucunun IP adresinin ters DNS kaydını alır ve bu konağın, kaynak etki alanından e-posta gönderme yetkisine sahip olup olmadığını doğrular. 

## <a name="how-reverse-dns-works"></a>Ters DNS 'in nasıl çalıştığı

Ters DNS kayıtları, ' ARPA ' bölgeleri olarak bilinen özel DNS bölgelerinde barındırılır.  Bu bölgeler, ' contoso.com ' gibi normal hiyerarşi barındırma etki alanları ile paralel olarak ayrı bir DNS hiyerarşisi oluşturur.

Örneğin, ' www.contoso.com ' DNS kaydı, ' contoso.com ' bölgesinde ' www ' adına sahip bir DNS ' A ' kaydı kullanılarak uygulanır.  Bu bir kayıt, ilgili IP adresine işaret eder, bu durumda 64.4.6.100.  Ters arama, ' 6.4.64.in-addr. arpa ' bölgesinde ' 100 ' adlı bir ' PTR ' kaydı kullanılarak ayrı olarak uygulanır (IP adreslerinin ARPA bölgelerinde tersine çevrildiğini unutmayın.)  Bu PTR kaydı, doğru şekilde yapılandırıldıysa, ' www.contoso.com ' adına işaret eder.

Bir kuruluşa bir IP adres bloğu atandığında, bunlara karşılık gelen ARPA bölgesini yönetme hakkı elde edilir. Azure tarafından kullanılan IP adresi bloklarına karşılık gelen ARPA bölgeleri Microsoft tarafından barındırılır ve yönetilir. ISS 'niz sizin için kendi IP adresleriniz için ARPA bölgesini barındırabilir veya Azure DNS gibi istediğiniz bir DNS hizmetinde ARPA bölgesini barındırmanıza izin verebilir.

> [!NOTE]
> İleri DNS aramaları ve ters DNS aramaları ayrı, paralel DNS hiyerarşilerinde uygulanır. ' Www.contoso.com ' için ters arama, karşılık gelen IP adresi bloğunun ARPA bölgesinde barındırıldığından, ' contoso.com ' bölgesinde **barındırılmaz** . Ayrı bölgeler IPv4 ve IPv6 adres blokları için kullanılır.

### <a name="ipv4"></a>IPv4

IPv4 geriye doğru arama bölgesinin adı şu biçimde olmalıdır: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

Örneğin, 192.0.2.0/24 ön ekine sahip IP 'Leri olan konaklar için kayıtları barındırmak üzere bir ters bölge oluştururken, bölge adı, adresin Ağ önekini yalıtarak (192.0.2) ve ardından sırayı ters çevirerek (2.0.192) ve son eki ekleyerek oluşturulur `.in-addr.arpa` .

|Alt ağ sınıfı|Ağ ön eki  |Ters ağ ön eki  |Standart sonek  |Ters bölge adı |
|-------|----------------|------------|-----------------|---------------------------|
|A sınıfı|203.0.0.0/8     | 203        | . in-addr. arpa   | `203.in-addr.arpa`        |
|B sınıfı|198.51.0.0/16   | 51,198     | . in-addr. arpa   | `51.198.in-addr.arpa`     |
|C sınıfı|192.0.2.0/24    | 2.0.192    | . in-addr. arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Sınıfsız IPv4 temsili

Bazı durumlarda, bir kuruluşa ayrılan IP aralığı, bir sınıf C (/24) aralığından daha küçüktür. Bu durumda, IP aralığı bölge hiyerarşisi içindeki bir bölge sınırına düşmez `.in-addr.arpa` ve bu nedenle alt bölge olarak temsil edilemez.

Bunun yerine, ayrı bir ters arama (PTR) kayıtlarının denetimini ayrılmış bir DNS bölgesine aktarmak için farklı bir mekanizma kullanılır. Bu mekanizma her IP aralığı için bir alt bölge devreder ve sonra aralıktaki her IP adresini CNAME kayıtlarını kullanarak bu alt bölgeye ayrı ayrı eşler.

Örneğin, bir kuruluşa ISS tarafından 192.0.2.128/26 IP aralığı verildiğini varsayalım. Bu, 192.0.2.128 ile 192.0.2.191 arasında IP adresini temsil 64 eder. Bu aralığın ters DNS 'i aşağıdaki gibi uygulanır:
- Kuruluş, 128-26.2.0.192.in-addr. arpa adlı bir geriye doğru arama bölgesi oluşturur. ' 128-26 ' öneki, C Sınıfı (/24) aralığında kuruluşa atanan ağ kesimini temsil eder.
- ISS, yukarıdaki bölge için DNS temsilcisini C sınıfı üst bölgesinden ayarlamak üzere NS kayıtları oluşturur. Ayrıca, üst (sınıf C) geriye doğru arama bölgesinde CNAME kayıtları oluşturur, IP aralığındaki her IP adresini kuruluş tarafından oluşturulan yeni bölgeye eşleyerek.

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Ardından kuruluş, alt bölgelerinde tek tek PTR kayıtlarını yönetir.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
' 129.2.0.192.in-addr. arpa ' adlı bir PTR kaydı için ' 192.0.2.129 ' IP adresi için ters arama. Bu sorgu, üst bölgedeki CNAME aracılığıyla alt bölgedeki PTR kaydına çözümlenir.

### <a name="ipv6"></a>IPv6

IPv6 geriye doğru arama bölgesinin adı aşağıdaki biçimde olmalıdır: `<IPv6 network prefix in reverse order>.ip6.arpa`

Örneğin,. 2001: db8:1000: abdc::/64 ön ekine sahip konaklar için kayıtları barındırmak üzere bir ters bölge oluştururken, bölgenin ağ ön ekini yalıtarak bölge adı oluşturulur (2001: db8: abdc::). Daha sonra, IPv6 adresi önekini kısaltmak için kullanılmışsa, [sıfır sıkıştırmayı](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10))kaldırmak için IPv6 ağ önekini genişletin (2001:0db8: abdc: 0000::). Ters çevrilen ağ önekini oluşturmak `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ve son eki eklemek için, ön ek içindeki her bir onaltılık sayı arasındaki sınırlayıcı olarak bir dönem kullanarak sırayı tersine çevirin `.ip6.arpa` .


|Ağ ön eki  |Genişletilmiş ve ters çevrilmiş ağ ön eki |Standart sonek |Ters bölge adı  |
|---------|---------|---------|---------|
|2001: db8: abdc::/64    | 0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2        | . ip6. arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001: db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8. b. d. 0.1.0.0.2        | . ip6. arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Ters DNS için Azure desteği

Azure, ters DNS ile ilgili iki ayrı senaryoyu destekler:

**IP adresi blobuna karşılık gelen geriye doğru arama bölgesini barındırma.**
Azure DNS, [geriye doğru arama Bölgelerinizi barındırmak ve hem IPv4 hem de IPv6 için her ters DNS araması IÇIN PTR kayıtlarını yönetmek](dns-reverse-dns-hosting.md)üzere kullanılabilir.  Ters arama (ARPA) bölgesi oluşturma, temsilciyi ayarlama ve PTR kayıtlarını yapılandırma işlemi, normal DNS bölgeleriyle aynıdır.  Tek fark, temsilcinin DNS kaydediciniz yerine ISS 'niz aracılığıyla yapılandırılması ve yalnızca PTR kayıt türünün kullanılması gerekir.

**Azure hizmetinize atanan IP adresi için ters DNS kaydını yapılandırın.** Azure [, Azure hizmetinize AYRıLAN IP adresleri için ters aramayı yapılandırmanızı](dns-reverse-dns-for-azure-services.md)sağlar.  Bu ters arama, Azure tarafından ilgili ARPA bölgesinde bir PTR kaydı olarak yapılandırılır.  Azure tarafından kullanılan tüm IP aralıklarına karşılık gelen bu ARPA bölgeleri Microsoft tarafından barındırılır

## <a name="next-steps"></a>Sonraki adımlar

Ters DNS hakkında daha fazla bilgi için bkz. [Vikipde ters DNS araması](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
[Azure DNS 'de ISS tarafından atanan IP aralığınızı geriye doğru arama bölgesini nasıl barındıracağınızı](dns-reverse-dns-for-azure-services.md)öğrenin.
<br>
[Azure hizmetlerinize yönelik ters DNS kayıtlarını yönetmeyi](dns-reverse-dns-for-azure-services.md)öğrenin.