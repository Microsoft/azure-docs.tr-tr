---
title: 'Azure ExpressRoute özel eşlemesi: IPSec aktarım modunu yapılandırma-Windows Konakları'
description: Azure Windows VM 'Leri ve şirket içi Windows konakları arasında, GPO ve OU 'Lar kullanılarak ExpressRoute özel eşlemesi arasında IPSec aktarım modunu etkinleştirme.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017281"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>ExpressRoute özel eşlemesi için IPSec aktarım modunu yapılandırma

Bu makale, ExpressRoute özel eşlemesi üzerinden Aktarım modunda IPSec tünelleri oluşturmanıza yardımcı olur. Tünel, Windows ve şirket içi Windows Konakları çalıştıran Azure VM 'Ler arasında oluşturulur. Bu yapılandırma için bu makaledeki adımlarda Grup İlkesi nesneleri kullanılır. Kuruluş birimleri (OU) ve Grup İlkesi nesneleri (GPO 'Lar) kullanılmadan bu yapılandırmayı oluşturmak mümkün olsa da. OU 'lar ve GPO 'ların birleşimi, Güvenlik ilkelerinizin denetimini basitleştirmeye yardımcı olur ve hızlı bir şekilde ölçeklendirme yapmanıza olanak sağlar. Bu makaledeki adımlarda, zaten bir Active Directory yapılandırmanız olduğu ve OU 'Lar ile GPO 'Ların kullanımı hakkında bilgi sahibisiniz.

## <a name="about-this-configuration"></a>Bu yapılandırma hakkında

Aşağıdaki adımlarda bulunan yapılandırma, ExpressRoute özel eşlemesi ile tek bir Azure sanal ağı (VNet) kullanır. Ancak, bu yapılandırma diğer Azure sanal ağları ve şirket içi ağların üzerine yayılabilir. Bu makale, bir Azure VM veya şirket içi ana bilgisayar grubuna uygulayabileceğiniz bir IPSec şifreleme ilkesi tanımlamanıza yardımcı olur. Bu Azure VM 'Leri veya şirket içi konaklar aynı OU 'nun bir parçasıdır. Azure VM 'Leri (VM1 ve VM2) ve şirket içi konak1 ile yalnızca hedef bağlantı noktası 8080 olan HTTP trafiği için şifreleme yapılandırırsınız. Gereksinimlerinize göre farklı türlerde IPSec ilkesi oluşturulabilir.

### <a name="working-with-ous"></a>OU 'Lar ile çalışma 

Bir OU ile ilişkili güvenlik ilkesi, GPO aracılığıyla bilgisayarlara gönderilir. Tek bir konağa ilke uygulamak yerine OU kullanmanın bazı avantajları şunlardır:

* Bir ilkeyi bir OU ile ilişkilendirmek, aynı OU 'ya ait bilgisayarların aynı ilkeleri almasını güvence altına alır.
* OU ile ilişkili güvenlik ilkesini değiştirmek, değişiklikleri OU 'daki tüm konaklara uygular.

### <a name="diagrams"></a>Diyagram

Aşağıdaki diyagramda, iç bağlantı ve atanan IP adresi alanı gösterilmektedir. Azure VM 'Leri ve şirket içi ana bilgisayar Windows 2016 çalıştırıyorsa. Azure VM 'Leri ve şirket içi konak1 aynı etki alanının bir parçasıdır. Azure VM 'Leri ve şirket içi konaklar, DNS kullanarak adları düzgün şekilde çözümleyebilir.

[![1]][1]

Bu diyagramda, ExpressRoute özel eşlemesindeki iletimde IPSec tünelleri gösterilmektedir.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>IPSec ilkesiyle çalışma

Windows 'ta şifreleme, IPSec ilkesiyle ilişkilendirilir. IPSec ilkesi, hangi IP trafiğinin güvenli olduğunu ve IP paketlerine uygulanan güvenlik mekanizmasını belirler.
**IPSec ilkeleri** şu öğelerden oluşur: **filtre listeleri**, **filtre eylemleri** ve **güvenlik kuralları**.

IPSec ilkesini yapılandırırken, aşağıdaki IPSec ilkesi terminolojisini anlamanız önemlidir:

* **IPSec ilkesi:** Kuralların koleksiyonu. Belirli bir zamanda yalnızca bir ilke etkin olabilir ("atanmış"). Her ilke, hepsi aynı anda etkin olabilen bir veya daha fazla kurala sahip olabilir. Bir bilgisayara, belirli bir zamanda yalnızca bir etkin IPSec ilkesi atanabilir. Bununla birlikte, IPSec ilkesinde, farklı durumlarda alınabilecek birden çok eylem tanımlayabilirsiniz. Her bir IPSec kuralları kümesi, kuralın geçerli olduğu ağ trafiği türünü etkileyen bir filtre listesiyle ilişkilendirilir.

* **Filtre listeleri:** Filtre listeleri bir veya daha fazla filtrenin paketleridir. Bir liste birden çok filtre içerebilir. Bir filtre, şu ölçütlere göre iletişimin engellenip engellenmediğini, izin verileceğini veya güvenli olup olmadığını tanımlar: IP adresi aralıkları, protokoller veya hatta belirli bağlantı noktaları. Her filtre belirli bir koşul kümesiyle eşleşir; Örneğin, belirli bir alt ağdan belirli bir hedef bağlantı noktasındaki belirli bir bilgisayara gönderilen paketler. Ağ koşulları bu filtrelerden bir veya daha fazlası ile eşleşirse, filtre listesi etkinleştirilir. Her filtre, belirli bir filtre listesi içinde tanımlanır. Filtreler filtre listeleri arasında paylaştırılamaz. Ancak, belirli bir filtre listesi çeşitli IPSec ilkelerine eklenebilir. 

* **Filtre eylemleri:** Bir güvenlik yöntemi, ıKE anlaşmaları sırasında bir bilgisayar tekliflerinin bir güvenlik algoritması, protokoller ve anahtar kümesi tanımlar. Filtre eylemleri, tercih sırasına göre derecelendirilen güvenlik yöntemi listeleridir.  Bir bilgisayar bir IPSec oturumu üzerinde anlaşır, filtre eylemleri listesinde depolanan güvenlik ayarına göre teklifleri kabul eder veya gönderir.

* **Güvenlik kuralları:** Kurallar, IPSec ilkesinin iletişimi nasıl ve ne zaman koruduğunu yönetir. IPSec bağlantısını oluşturmak için bir IPSec kuralı oluşturmak üzere **filtre listesi** ve **filtreleme eylemlerini** kullanır. Her ilke, hepsi aynı anda etkin olabilen bir veya daha fazla kurala sahip olabilir. Her kural, IP filtrelerinin bir listesini ve bu filtre listesiyle bir eşleşmesinden sonra gerçekleşen güvenlik eylemlerinin bir koleksiyonunu içerir:
  * IP filtresi eylemleri
  * Kimlik doğrulama yöntemleri
  * IP tüneli ayarları
  * Bağlantı türleri

[![5]][5]

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki önkoşulları karşıladığınızdan emin olun:

* Grup ilkesi ayarlarını uygulamak için kullanabileceğiniz, çalışan bir Active Directory yapılandırmasına sahip olmanız gerekir. GPO 'Lar hakkında daha fazla bilgi için bkz. [Grup İlkesi Objects](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir.
  * ExpressRoute bağlantı hattı oluşturma hakkında daha fazla bilgi için bkz. [ExpressRoute devresi oluşturma](expressroute-howto-circuit-arm.md). 
  * Bağlantı sağlayıcınız tarafından devre dışı olduğunu doğrulayın. 
  * Devreniz için Azure özel eşliğin yapılandırıldığını doğrulayın. Yönlendirme yönergeleri için [yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md) makalesine bakın. 
  * Oluşturulmuş ve tam olarak sağlanmış bir VNet ve sanal ağ geçidinizin olduğunu doğrulayın. [ExpressRoute için sanal ağ geçidi oluşturma](expressroute-howto-add-gateway-resource-manager.md)yönergelerini izleyin. ExpressRoute için bir sanal ağ geçidi, VPN değil GatewayType ' ExpressRoute ' kullanır.

* ExpressRoute sanal ağ geçidi ExpressRoute devresine bağlanmalıdır. Daha fazla bilgi için bkz. [sanal ağı bir ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md).

* Azure Windows VM 'lerinin VNet 'e dağıtıldığını doğrulayın.

* Şirket içi konaklar ve Azure VM 'Ler arasında bağlantı olduğunu doğrulayın.

* Azure Windows VM 'lerinin ve şirket içi konaklarının adları düzgün bir şekilde çözümlemek için DNS 'i kullanıp kullanbildiğini doğrulayın.

### <a name="workflow"></a>İş akışı

1. Bir GPO oluşturun ve onu OU ile ilişkilendirin.
2. IPSec **filtre eylemi** tanımlayın.
3. Bir IPSec **filtre listesi** tanımlayın.
4. **Güvenlik kuralları** Ile bir IPSec ilkesi oluşturun.
5. IPSec GPO 'sunu OU 'ya atayın.

### <a name="example-values"></a>Örnek değerler

* **Etki alanı adı:** ipsectest.com

* **OU:** IPSecOU

* Şirket **Içi Windows bilgisayarı:** konak1

* **Azure Windows VM 'leri:** VM1, VM2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. bir GPO oluşturun

1. Grup ilkesi Yönetimi ek bileşenini açarak bir OU 'ya bağlı yeni bir GPO oluşturun. Ardından, GPO 'nun bağlanacağı OU 'yu bulun. Örnekte, OU adı **Ipsecou** olarak adlandırılmıştır. 

   [![9]][9]
2. Grup ilkesi Yönetimi ek bileşeninde OU ' yı seçin ve sağ tıklayın. Açılan menüde, "**Bu etki alanında GPO oluştur ve buraya bağla...**" öğesini seçin.

   [![10]][10]
3. Daha sonra kolayca bulabilmeniz için GPO 'YU sezgisel bir ad olarak adlandırın. GPO oluşturmak ve bağlamak için **Tamam ' ı** seçin.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. GPO bağlantısını etkinleştirin

GPO 'yu OU 'ya uygulamak için GPO yalnızca OU 'ya bağlanmamalıdır, ancak bağlantının da etkinleştirilmesi gerekir.

1. Oluşturduğunuz GPO 'YU bulun, sağ tıklayın ve açılan listeden **Düzenle** ' yi seçin.
2. GPO 'YU OU 'ya uygulamak için **bağlantı etkin**' i seçin.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. IP filtre eylemini tanımlayın

1. Açılan listeden **Active Directory IP güvenlik ilkesi**' ne sağ tıklayın ve ardından **IP filtresi listelerini yönet ve eylemleri filtrele...**' yi seçin.

   [![15]][15]
2. "**Filtre eylemlerini yönet**" sekmesinde **Ekle**' yi seçin.

   [![16]][16]

3. **IP güvenlik filtresi eylem sihirbazında**, **İleri**' yi seçin.

   [![17]][17]
4. Daha sonra bulabilmeniz için filtre eylemini sezgisel bir ad olarak adlandırın. Bu örnekte, filtre eylemi **Myencryption** olarak adlandırılmıştır. Ayrıca, bir açıklama ekleyebilirsiniz. Ardından **İleri**' yi seçin.

   [![18]][18]
5. **Negotiate Güvenlik** , IPSec 'in başka bir bilgisayarla kurulamazsa davranışı tanımlamanızı sağlar. **Negotiate Güvenlik**' i seçin ve ardından **İleri**' yi seçin.

   [![19]][19]
6. IPSec 'i **desteklemeyen bilgisayarlarla Iletişim kurma** sayfasında **güvenli olmayan iletişime izin verme**' yi seçin ve ardından **İleri**' yi seçin.

   [![20]][20]
7. **IP trafiği ve güvenlik** sayfasında, **özel**' i seçin ve ardından **ayarlar...**' ı seçin.

   [![21]][21]
8. **Özel güvenlik yöntemi ayarları** sayfasında, **veri bütünlüğü ve şifreleme (ESP): SHA1, 3DES '** i seçin. Ardından **Tamam**'ı seçin.

   [![22]][22]
9. **Filtre eylemlerini yönet** sayfasında, **Myencryption** filtresinin başarıyla eklendiğini görebilirsiniz. **Kapat**’ı seçin.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. bir IP filtre listesi tanımlayın

Hedef bağlantı noktası 8080 ile şifrelenmiş HTTP trafiği belirten bir filtre listesi oluşturun.

1. Hangi trafik türlerinin şifrelendiğini nitelemek için bir **IP filtre listesi** kullanın. **IP filtresi listelerini yönet** sekmesinde **Ekle** ' yı seçerek yeni bir IP filtresi listesi ekleyin.

   [![24]][24]
2. **Ad:** ALANıNA, IP filtresi listeniz için bir ad yazın. Örneğin, **Azure-OnPremises-HTTP8080**. Ardından **Ekle**' yi seçin.

   [![25]][25]
3. **IP filtresi açıklaması ve yansıtmalı Özellik** sayfasında, **yansıtılmış**' ı seçin. Yansıtılmış ayar, iki yönlü iletişime olanak sağlayan paketlerle her iki yönde de eşleşir. Sonra **İleri**’yi seçin.

   [![26]][26]
4. **IP trafiği kaynağı** sayfasında, **kaynak adresi:** AÇıLAN menüsünde **belirli bir IP adresi veya alt ağ** seçin. 

   [![27]][27]
5. IP trafiği için kaynak adresi **IP adresini veya alt ağını** belirtin ve ardından **İleri**' yi seçin.

   [![28]][28]
6. **Hedef adresini belirtin:** IP adresi veya alt ağ. Ardından **İleri**' yi seçin.

   [![29]][29]
7. **IP protokol türü** sayfasında **TCP**' yi seçin. Ardından **İleri**' yi seçin.

   [![30]][30]
8. **IP protokolü bağlantı noktası** sayfasında, **herhangi bir bağlantı** noktasından ve **Bu bağlantı noktasına** seçim yapın:. Metin kutusuna **8080** yazın. Bu ayarlar yalnızca hedef bağlantı noktası 8080 ' deki HTTP trafiğinin şifrelenmesini belirtir. Ardından **İleri**' yi seçin.

   [![31]][31]
9. IP filtresi listesini görüntüleyin.  **Azure-OnPremises-HTTP8080** IP filtresi listesinin yapılandırması, aşağıdaki ölçütlere uyan tüm trafik için şifrelemeyi tetikler:

   * 10.0.1.0/24 ' deki herhangi bir kaynak adresi (Azure Subnet2)
   * 10.2.27.0/25 (Şirket içi alt ağ) içindeki herhangi bir hedef adres
   * TCP protokolü
   * Hedef bağlantı noktası 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. IP filtre listesini düzenleyin

Şirket içi konaktan Azure VM 'ye aynı türde trafiği şifrelemek için ikinci bir IP filtresine ihtiyacınız vardır. İlk IP filtresini ayarlamak için kullandığınız adımların aynısını izleyin ve yeni bir IP filtresi oluşturun. Tek farklar kaynak alt ağ ve hedef alt ağıdır.

1. IP filtresi listesine yeni bir IP filtresi eklemek için **Düzenle**' yi seçin.

   [![33]][33]
2. **IP filtresi listesi** sayfasında **Ekle**' yi seçin.

   [![34]][34]
3. Aşağıdaki örnekteki ayarları kullanarak ikinci bir IP filtresi oluşturun:

   [![35]][35]
4. İkinci IP filtresini oluşturduktan sonra, IP filtresi listesi şöyle görünür:

   [![36]][36]

Bir uygulamayı korumak için şirket içi bir konum ve bir Azure alt ağı arasında şifreleme gerekliyse. Mevcut IP filtresi listesini değiştirmek yerine, yeni bir IP filtre listesi ekleyebilirsiniz. İki veya daha fazla IP filtresi listesini aynı IPSec ilkesiyle ilişkilendirmek size daha fazla esneklik sağlar. IP filtre listesini, diğer IP filtresi listelerini etkilemeden değiştirebilir veya kaldırabilirsiniz.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. IPSec güvenlik ilkesi oluşturma 

Güvenlik kuralları ile bir IPSec ilkesi oluşturun.

1. OU ile ilişkili **Active Directory 'de IPI ilkelerini** seçin. Sağ tıklayın ve **IP Güvenlik Ilkesi oluştur**' u seçin.

   [![37]][37]
2. Güvenlik ilkesini adlandırın. Örneğin, **ilke-Azure-OnPremises**. Ardından **İleri**' yi seçin.

   [![38]][38]
3. Onay kutusunu seçmeden **İleri ' yi** seçin.

   [![39]][39]
4. **Özellikleri Düzenle** onay kutusunun seçili olduğundan emin olun ve ardından **son**' u seçin.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. IPSec güvenlik ilkesini düzenleyin

IPSec ilkesine, daha önce yapılandırdığınız **IP filtre listesi** ve **filtreleme eylemine** ekleyin.

1. HTTP ilkesi özellikleri **kuralları** sekmesinde **Ekle**' yi seçin.

   [![41]][41]
2. Hoş Geldiniz sayfasında, **İleri**’yi seçin.

   [![42]][42]
3. Bir kural, IPSec modunu tanımlama seçeneği sağlar: tünel modu veya aktarım modu.

   * Tünel modunda, özgün paket bir IP üstbilgileri kümesiyle kapsüllenir. Tünel modu, özgün paketin IP üstbilgisini şifreleyerek iç yönlendirme bilgilerini korur. Tünel modu, siteden siteye VPN senaryolarında ağ geçitleri arasında yaygın olarak uygulanır. Tünel modu, konaklar arasında uçtan uca şifreleme için kullanılan çoğu durumda bulunur.

   * Aktarım modu yalnızca yükü ve ESP artbilgisini şifreler; özgün paketin IP üstbilgisi şifrelenmedi. Aktarım modunda, paketlerin IP kaynağı ve IP hedefi değiştirilmez.

   **Bu kural bir tünel belirtmiyor** ve ardından **İleri**' yi seçin.

   [![43]][43]
4. **Ağ türü** , hangi ağ bağlantısının güvenlik ilkesiyle ilişkilendirildiğini tanımlar. **Tüm ağ bağlantıları**' nı seçin ve ardından **İleri**' yi seçin.

   [![44]][44]
5. Daha önce oluşturduğunuz IP filtresi listesini,  **Azure-OnPremises-HTTP8080**' ı seçin ve ardından **İleri**' yi seçin.

   [![45]][45]
6. Daha önce oluşturduğunuz mevcut filtre **eylemi '** ni seçin.

   [![46]][46]
7. Windows dört farklı kimlik doğrulaması türünü destekler: Kerberos, sertifikalar, NTLMv2 ve önceden paylaşılan anahtar. Etki alanına katılmış konaklar ile çalıştık, **Active Directory varsayılan (Kerberos V5 protokolü)** öğesini seçin ve ardından **İleri**' yi seçin.

   [![47]][47]
8. Yeni ilke güvenlik kuralını oluşturur: **Azure-OnPremises-HTTP8080**. **Tamam**’ı seçin.

   [![48]][48]

IPSec ilkesi, 8080 hedef bağlantı noktasındaki tüm HTTP bağlantılarının IPSec aktarım modunu kullanmasını gerektirir. HTTP, açık bir metin Protokolü olduğundan ve güvenlik ilkesinin etkinleştirildiğinden, verilerin ExpressRoute özel eşlemesi aracılığıyla aktarılırken şifrelenmesini sağlar. Active Directory için IPSec ilkesi, Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'Nı yapılandırmak için daha karmaşıktır. Ancak, ıpsec bağlantısının daha fazla özelleştirmesini sağlar.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. IPSec GPO 'sunu OU 'ya atayın

1. İlkeyi görüntüleyin. Güvenlik grubu ilkesi tanımlanmış, ancak henüz atanmadı.

   [![49]][49]
2. Güvenlik grubu ilkesini OU **ıpsecou**'ya atamak için güvenlik ilkesine sağ tıklayıp **ata**' yı seçin.
   OU 'ya ait olan her bilgisayar için güvenlik grubu ilkesi atanır.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Trafik şifrelemesini denetleme

OU 'da uygulanan şifreleme GPO 'sunu kullanıma almak için, tüm Azure VM 'lerine ve konak1 ' ye IIS 'yi yüklersiniz. Her IIS, 8080 numaralı bağlantı noktasında HTTP isteklerine yanıt verecek şekilde özelleştirilir.
Şifrelemeyi doğrulamak için, OU 'daki tüm bilgisayarlara bir ağ algılayıcısı (Wireshark gibi) yükleyebilirsiniz.
Bir PowerShell betiği 8080 numaralı bağlantı noktasında HTTP istekleri oluşturmak için HTTP istemcisi olarak çalışmaktadır:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Aşağıdaki ağ yakalama, görüntüleme filtresi ile şirket içi konak1 ilgili sonuçları yalnızca şifrelenmiş trafikle eşleşecek şekilde gösterir:

[![51]][51]

Şirket içi PowerShell betiğini (HTTP istemcisi) çalıştırırsanız, Azure VM 'deki ağ yakalama, benzer bir izleme gösterir.

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).

<!--Image References-->

[]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "ExpressRoute aracılığıyla 1 ağ diyagramı IPSec aktarım modu"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPSec ilginç trafiği"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPSec ilkesi"
grup ilkesi [9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "kuruluş birimi"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "OU Ile ilişkili bir GPO oluşturma"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "OU ile ilişkili GPO 'ya bir ad atayın"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "GPO 'yu düzenleme"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP filtresi listelerini ve filtreleme eylemlerini yönetme"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "filtre ekleme eylemi"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Eylem Sihirbazı"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "filtre eylemi adı"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filtre eylemi"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "davranışın, güvenli olmayan bir bağlantı kurdu olduğunu belirtin"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "güvenlik mekanizması"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "özel güvenlik yöntemi"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "filtre eylemi listesi"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "yeni IP filtre listesi ekleme"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "IP filtresine HTTP trafiği ekleyin"
[]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "her iki yönde 26 eşleşme paketi"
[]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "kaynak alt ağın 27 seçimi"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "kaynak ağ"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "hedef ağ"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokol"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "kaynak bağlantı noktası ve hedef bağlantı noktası"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filtre listesi"
[]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "HTTP trafiğiyle 33 IP filtresi listesi"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Ikinci bir IP filtresi ekleme"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP filtre listesi-ikinci girdi"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP filtre listesi-ikinci girdi"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "IP güvenlik ilkesi oluşturma"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "IPSec ilkesinin adı"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPSec İlkesi Sihirbazı"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "IPSec ilkesini düzenleme"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "IPSec ilkesine yeni güvenlik kuralı ekle"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "Yeni bir güvenlik kuralı oluştur"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Aktarım modu"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "ağ türü"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "var olan IP filtresi listesinin seçimi"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "mevcut filtre eyleminin seçimi"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "kimlik doğrulama yöntemi seçimi"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "güvenlik ilkesi oluşturma işleminin sonu"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "ıPSEC ilkesi, GPO 'ya bağlı ancak atanmadı"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPSec ilkesi atandı"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "IPSec şifreli trafiği yakalama"