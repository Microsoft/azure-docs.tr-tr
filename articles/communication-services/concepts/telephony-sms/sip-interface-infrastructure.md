---
title: SIP arabirimi altyapı gereksinimleri-Azure Iletişim Hizmetleri
description: Azure Iletişim Hizmetleri SIP arabirimi yapılandırması için altyapı gereksinimleri hakkında bilgi edinin
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ede650ae072ef53ed40a9372a292ab69fe8cc1af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492741"
---
# <a name="sip-interface-infrastructure-requirements"></a>SIP arabirimi altyapı gereksinimleri 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Bu makalede, SIP arabirimi dağıtımınızı planlarken göz önünde bulundurmanız isteyebileceğiniz altyapı, lisanslama ve oturum sınır denetleyicisi (SBC) bağlantı ayrıntıları açıklanmaktadır.


## <a name="infrastructure-requirements"></a>Altyapı gereksinimleri
SIP arabirimini dağıtmak için desteklenen SBCs, etki alanları ve diğer ağ bağlantısı gereksinimlerinin altyapı gereksinimleri aşağıdaki tabloda listelenmiştir:  

|Altyapı gereksinimi|Şunlar gerekir|
|:--- |:--- |
|Oturum sınır denetleyicisi (SBC)|Desteklenen bir SBC. Daha fazla bilgi için bkz. [desteklenen SBCS](#supported-session-border-controllers-sbcs).|
|SBC 'ye bağlı telefon Trunks|Bir veya daha fazla telefon Trunks, SBC 'ye bağlandı. Bir uçta, SBC, SIP arabirimi aracılığıyla Azure Iletişim hizmetine bağlanır. SBC, PBXs, analog telefon bağdaştırıcıları vb. gibi üçüncü taraf telefon varlıklarına da bağlanabilir. SBC 'ye bağlı herhangi bir PSTN bağlantı seçeneği çalışacaktır. (PSTN Trunks 'in SBC 'ye yapılandırması için lütfen SBC satıcılarına veya santral sağlayıcısına bakın.)|
|Azure aboneliği|ACS kaynağı oluşturmak için kullandığınız bir Azure aboneliği ve SBC ile yapılandırma ve bağlantı.|
|İletişim Hizmetleri erişim belirteci|Çağrı yapmak için kapsama sahip geçerli bir erişim belirtecine ihtiyacınız vardır `voip` . Bkz. [erişim belirteçleri](../identity-model.md#access-tokens)|
|SBC için genel IP adresi|SBC 'ye bağlanmak için kullanılabilen genel bir IP adresi. SBC 'nin türüne bağlı olarak, SBC NAT kullanabilir.|
|SBC için tam etki alanı adı (FQDN)|, FQDN 'nin etki alanı bölümünün Microsoft 365 veya Office 365 kuruluşunuzdaki kayıtlı etki alanlarıyla eşleşmediği, SBC için bir FQDN. Daha fazla bilgi için bkz. [SBC etki alanı adları](#sbc-domain-names).|
|SBC için genel DNS girişi |SBC FQDN 'sini genel IP adresine eşleyen ortak bir DNS girişi. |
|SBC için genel güvenilen sertifika |SIP arabirimi ile tüm iletişimler için kullanılacak SBC için bir sertifika. Daha fazla bilgi için bkz. [SBC Için genel güvenilen sertifika](#public-trusted-certificate-for-the-sbc).|
|SIP sinyal ve medya için güvenlik duvarı IP adresleri ve bağlantı noktaları |SBC, bulutta aşağıdaki hizmetlerle iletişim kurar:<br/><br/>Sinyali işleyen SIP Proxy 'Si<br/>Medyayı işleyen medya Işlemcisi<br/><br/>Bu iki hizmetin, bu belgede daha sonra açıklanan Microsoft Bulut ayrı IP adresleri vardır.


## <a name="sbc-domain-names"></a>SBC etki alanı adları

Office 365 olmayan müşteriler, ortak bir sertifika alabileceği herhangi bir etki alanı adını kullanabilir.

Aşağıdaki tabloda, ad, kiracı için kayıtlı DNS adları örnekleri ve bu adın, SBC için tam etki alanı adı (FQDN) olarak kullanılıp kullanılamayacağını ve geçerli FQDN adları örneklerini göstermektedir:

|DNS adı|SBC FQDN için kullanılabilir|FQDN adı örnekleri|
|:--- |:--- |:--- |
contoso.com|Yes|**Geçerli adlar:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|No|SBC adlarında *. onmicrosoft.com etki alanlarının kullanılması desteklenmez

Office 365 müşterisiyseniz, SBC etki alanı adının Office 365 kiracının etki alanlarında kayıtlı olması gerekir. Office 365 ve Azure Iletişim hizmeti 'nin birlikte bulunma örneği aşağıda verilmiştir:

|Office 365 ' de kayıtlı etki alanı|Ekiplerde SBC FQDN örnekleri|ACS 'de SBC FQDN adı örnekleri|
|:--- |:--- |:--- |
**contoso.com** (ikinci düzey etki alanı)|**SBC.contoso.com** (ikinci düzey etki alanındaki ad)|**SBC.ACS.contoso.com** (üçüncü düzey etki alanındaki adı)<br/>**SBC.fabrikam.com** (farklı etki alanı içinde herhangi bir ad)|
|**O365.contoso.com** (üçüncü düzey etki alanı)|**SBC.O365.contoso.com** (üçüncü düzey etki alanındaki adı)|**SBC.contoso.com** (ikinci düzey etki alanındaki ad)<br/>**SBC.ACS.O365.contoso.com** (Dördüncü düzey etki alanındaki ad)<br/>**SBC.fabrikam.com** (farklı etki alanı içinde herhangi bir ad)

SBC eşleştirmesi Iletişim Hizmetleri kaynak düzeyinde çalışarak, çok sayıda SBCs 'yi tek bir Iletişim Hizmetleri kaynağına eşleştirmenizi, ancak tek bir SBC 'yi birden fazla Iletişim hizmeti kaynağına eşlenemezsiniz. Farklı kaynaklarla eşleştirme için benzersiz SBC FQDN 'leri gereklidir.

## <a name="public-trusted-certificate-for-the-sbc"></a>SBC için genel güvenilen sertifika

Microsoft, bir sertifika imzalama isteği (CSR) oluşturarak SBC için sertifika istemenizi önerir. Bir SBC için CSR oluşturmaya yönelik belirli yönergeler için, SBC satıcılarınız tarafından verilen iç bağlantı yönergelerine veya belgelerine bakın. 

  > [!NOTE]
  > Çoğu sertifika yetkilisi (CA), özel anahtar boyutunun en az 2048 olmasını gerektirir. CSR 'yi oluştururken bunu aklınızda bulundurun.

Sertifikanın, ortak ad (CN) veya konu diğer adı (SAN) alanı olarak SBC FQDN 'sine sahip olması gerekir. Sertifika, bir ara sağlayıcıdan değil, doğrudan bir sertifika yetkilisinden verilmelidir.

Alternatif olarak, Iletişim Hizmetleri SIP arabirimi CN ve/veya SAN 'da bir joker karakteri destekler ve joker karakterin [TLS üzerinden standart RFC http](https://tools.ietf.org/html/rfc2818#section-3.1)ile uyumlu olması gerekir. 

Bir örnek, `\*.contoso.com` SBC FQDN 'siyle eşleşecek `sbc.contoso.com` ancak ile eşleşmemelidir `sbc.test.contoso.com` .

Sertifikanın aşağıdaki kök sertifika yetkililerinden biri tarafından oluşturulması gerekir:

- Afırmtrust
- AddTrust dış CA kökü
- Baltimore CyberTrust kökü *
- Buypass
- CyberTrust
- Sınıf 3 genel birincil sertifika yetkilisi
- Comodo güvenli kök CA
- Deutsche Telekod 
- DigiCert genel kök CA 'sı
- DigiCert yüksek güvence EV kök CA
- Entrust
- GlobalSign
- Go Daddy
- Coğrafi güven
- Verisign, Inc. 
- SSL.com
- Starfield
- Microsoft için Symantec Enterprise Mobile Root 
- SwissSign
- Thawte damgalama CA 'sı
- TrustWave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekod)
- Quovada

Microsoft, müşteri isteklerine göre ek sertifika yetkilileri ekleme konusunda çalışmaktadır. 

## <a name="sip-signaling-fqdns"></a>SIP sinyali: FQDN 'Ler 

Iletişim Hizmetleri SIP arabirimine yönelik bağlantı noktaları aşağıdaki üç FQDN ' dir:

- **Sip.pstnhub.Microsoft.com** – Global FQDN – önce denenmelidir. SBC bu adı çözümlemek için bir istek gönderdiğinde, Microsoft Azure DNS sunucuları, SBC 'ye atanan birincil Azure veri merkezini işaret eden bir IP adresi döndürür. Atama, veri merkezlerinin performans ölçümlerini ve SBC 'ye coğrafi yakınlık düzeyini temel alır. Döndürülen IP adresi, birincil FQDN 'ye karşılık gelir.
- **sip2.pstnhub.Microsoft.com** – ikincil FQDN – coğrafi olarak ikinci öncelik bölgesine eşlenir.
- **sip3.pstnhub.Microsoft.com** – üçüncül FQDN – coğrafi olarak üçüncü öncelik bölgesine eşlenir.

Bu üç FQDN 'nin sırasıyla yerleştirilmesi için şunları yapmanız gerekir:

- En iyi deneyimi sağlayın (ilk FQDN 'yi sorgulayarak SBC veri merkezine daha az yüklenmiş ve en yakın).
- Bir SBC 'den bağlantı, geçici bir sorun yaşayan bir veri merkezine oluşturulduğunda yük devretme sağlayın. Daha fazla bilgi için aşağıdaki [Yük devretme mekanizmasına](#failover-mechanism-for-sip-signaling) bakın.  

FQDN 'Ler – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com ve sip3.pstnhub.microsoft.com – aşağıdaki IP adreslerinden birine çözülür:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Sinyallerden gelen ve giden trafiğe izin vermek için bu IP adreslerine yönelik güvenlik duvarı bağlantı noktalarını açın. Güvenlik duvarınız DNS adlarını destekliyorsa, FQDN `sip-all.pstnhub.microsoft.com` Tüm bu IP adreslerine çözümlenir. 

## <a name="sip-signaling-ports"></a>SIP sinyali: bağlantı noktaları

Iletişim Hizmetleri SIP arabirimi için aşağıdaki bağlantı noktalarını kullanın:

|Trafik|Kaynak|Amaç|Kaynak bağlantı noktası|Hedef bağlantı noktası|
|:--- |:--- |:--- |:--- |:--- |
|SıP/TLS|SIP Proxy 'Si|SBC|1024 – 65535|SBC üzerinde tanımlanmıştır (Office 365 GCC High/DoD yalnızca bağlantı noktası 5061 Için kullanılmalıdır)|
SıP/TLS|SBC|SIP Proxy 'Si|SBC üzerinde tanımlandı|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>SIP sinyali için yük devretme mekanizması

SBC, sip.pstnhub.microsoft.com çözümlemek için bir DNS sorgusu yapar. SBC konumuna ve veri merkezi performans ölçümlerine bağlı olarak, birincil veri merkezi seçilidir. Birincil veri merkezi bir sorun yaşıyorsa, SBC, ikinci atanan veri merkezine çözümlenen sip2.pstnhub.microsoft.com öğesini dener ve bu durumda iki bölgedeki veri merkezlerinin kullanılabilir olmadığı nadir olarak, SBC, üçüncül veri merkezi IP 'sini sağlayan son FQDN 'yi (sip3.pstnhub.microsoft.com) yeniden dener.

## <a name="media-traffic-ip-and-port-ranges"></a>Medya trafiği: IP ve bağlantı noktası aralıkları

Medya trafiği, ortam Işlemcisi olarak adlandırılan ayrı bir hizmetten ve bu hizmete akar. ACS için medya Işlemcisi yayımlama sırasında, herhangi bir Azure IP adresini kullanabilir. [Adreslerin tam listesini](https://www.microsoft.com/download/details.aspx?id=56519)indirin.

### <a name="port-range"></a>Bağlantı noktası aralığı
Medya Işlemcilerin bağlantı noktası aralığı aşağıdaki tabloda gösterilmiştir: 

|Trafik|Kaynak|Amaç|Kaynak bağlantı noktası|Hedef bağlantı noktası|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Medya Işlemcisi|SBC|3478-3481 ve 49152 – 53247|SBC üzerinde tanımlandı|
|UDP/SRTP|SBC|Medya Işlemcisi|SBC üzerinde tanımlandı|3478-3481 ve 49152 – 53247|

  > [!NOTE]
  > Microsoft, SBC üzerinde eş zamanlı çağrı başına en az iki bağlantı noktası önerir.


## <a name="media-traffic-media-processors-geography"></a>Medya trafiği: medya işlemcileri Coğrafya

Medya trafiği, medya işlemcileri adlı bileşenler aracılığıyla akar. Medya işlemcileri, SIP Proxy 'leriyle aynı veri merkezlerine yerleştirilir. Ayrıca, medya akışını iyileştirmek için ek medya işlemcileri de vardır. Örneğin, Avustralya 'da (Singapur veya Hong Kong çın ÖIB) artık bir SIP Proxy bileşeni yoktur, ancak Media Processor 'ı Avustralya 'da yerel olarak sunuyoruz. Medya işlemcilerin yerel olarak gereksinimi, trafik uzun mesafe göndererek (örneğin, Avustralya 'dan Singapur veya Hong Kong SAR), gecikme süresine göre belirlenir. Avustralya 'dan Hong Kong SAR veya Singapur 'a akan trafik örneğinde gecikme süresi, SIP trafiği için iyi çağrı kalitesini korumak için kabul edilebilir ancak gerçek zamanlı medya trafiği için bu değildir.

Hem SIP Proxy 'si hem de medya işlemcisi bileşenlerinin dağıtıldığı konumlar:
- ABD (ABD Batı ve ABD Doğu veri merkezlerinde ikisi)
- Avrupa (Amsterdam ve Dublin datacenters)
- Asya (Singapur ve Hong Kong çın ÖIB veri merkezleri)
- Avustralya (AU Doğu ve Güneydoğu veri merkezleri)

Yalnızca medya işlemcilerin dağıtıldığı konumlar (yukarıda listelenen en yakın veri merkezi üzerinden SIP akışları):
- Japonya (JP Doğu ve Batı veri merkezleri)


## <a name="media-traffic-codecs"></a>Medya trafiği: codec bileşenleri

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>SBC ile bulut medya Işlemcisi veya Microsoft ekipleri istemcisi arasında bacağı.
Hem medya atlama durumu hem de atlama olmayan durumlar için geçerlidir.

Oturum kenarlığı denetleyicisi ve bulut medyası Işlemcisi arasındaki Bada doğrudan yönlendirme arabirimi aşağıdaki codec bileşenlerini kullanabilir:

- SILK, G. 711, G. 722, G. 729

Öneriden istenmeyen codec bileşenleri dışlayarak, oturum kenarlığı denetleyicisinde belirli codec bileşeninin kullanımını zorlayabilirsiniz.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>ACS SDK uygulaması ile bulut medya Işlemcisi arasında bacak

Bulut medyası Işlemcisi ve ACS SDK uygulaması arasındaki Bata SILK veya G. 722 kullanılır. Bu Bada bulunan codec bileşeni, birden çok parametreye göz önünde bulundurarak Microsoft algoritmalarına dayalıdır. 

## <a name="supported-session-border-controllers-sbcs"></a>Desteklenen oturum kenarlığı denetleyicileri (SBCs)

Sertifika devam ediyor. Bu arada, müşteriler [ekip sertifikalı oturum kenarlığı denetleyicileri](/MicrosoftTeams/direct-routing-border-controllers)kullanabilir. 

## <a name="next-steps"></a>Sonraki adımlar

### <a name="conceptual-documentation"></a>Kavramsal belgeler

- [Telefon kavramı](./telephony-concept.md)
- [Azure Iletişim hizmetlerindeki telefon numarası türleri](./plan-solution.md)
- [Fiyatlandırma](../pricing.md)

### <a name="quickstarts"></a>Hızlı Başlangıçlar

- [Telefon çağrısı](../../quickstarts/voice-video-calling/pstn-call.md)