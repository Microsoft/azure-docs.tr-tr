---
title: 'Azure ExpressRoute özel eşlemesi: IPSec aktarım modunu yapılandırma-Windows Konakları'
description: Azure Windows VM ve GPO'ları ve OU'ları kullanarak eşlemesi özel ExpressRoute aracılığıyla şirket içi Windows konaklar arasında IPSec aktarım modu etkinleştirme konusunda.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022001"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>IPSec aktarım modu için ExpressRoute özel eşlemesini yapılandırın

Bu makalede, IPSec tünelleri aktarım modunda ExpressRoute üzerinden özel Windows çalıştıran Azure Vm'leri arasında eşleme oluşturmanıza yardımcı olur ve şirket içi Windows konakları. Bu makaledeki adımlarda, Grup İlkesi nesneleri kullanılarak bu yapılandırmasını oluşturun. Grup İlkesi nesneleri (GPO'lar) ve kuruluş birimine (OU) kullanmadan bu yapılandırmayı oluşturmak mümkün olsa da, kuruluş birimleri ve GPO'lar birleşimi denetimi güvenlik ilkelerinizin kolaylaştırmaya yardımcı ve yukarı hızla ölçek sağlar. Bu makaledeki adımlarda, bir Active Directory yapılandırması zaten var ve kuruluş birimleri ve GPO'lar kullanımıyla ilgili bilgi sahibi olduğunuz varsayılır.

## <a name="about-this-configuration"></a>Bu yapılandırma hakkında

Aşağıdaki adımlarda, yapılandırma, ExpressRoute özel eşlemesi ile tek bir Azure sanal ağına (VNet) kullanın. Ancak, bu yapılandırma, daha fazla Azure sanal ağlar ve şirket içi ağlara yayılabilir. Bu makalede, IPSec şifrelemesi ilkesini tanımlayın ve Azure VM'ler ve konaklar aynı OU parçası olan yerinde bir gruba uygulanan yardımcı olur. Hedef bağlantı noktası 8080'ile ' de Azure sanal makinelerini (vm1 ve vm2) ve yalnızca HTTP trafiği için şirket içi konak1 arasında şifreleme yapılandırın. IPSec ilkesi oluşturulabilir farklı türde gereksinimlerinize göre.

### <a name="working-with-ous"></a>OU'ları ile çalışma 

Kuruluş birimi ile ilişkilendirilmiş güvenlik ilkesi, GPO bilgisayarlara gönderilir. Tek bir konağa sağlamaktan yerine OU'ları kullanarak bazı avantajları şunlardır:

* Bir ilke bir OU ile ilişkilendirme aynı OU'ya ait bilgisayarlar aynı ilkeler alma garanti eder.
* OU ile ilişkili güvenlik ilkesini değiştirmek, bir OU'daki tüm Konaklara değişiklikleri uygulanır.

### <a name="diagrams"></a>Diyagramlar

Aşağıdaki diyagramda, atanan IP adres alanı ve bağlantısı gösterir. Azure Vm'leri ve şirket içi ana bilgisayar Windows 2016 çalışır. Azure Vm'leri ve şirket içi konak1 aynı etki alanının parçasıdır. Azure VM ve şirket içi ana bilgisayarlarına adları doğru DNS kullanarak çözebilirsiniz.

[![1]][1]

Bu diyagramda IPSec tünelleri, ExpressRoute özel eşlemesi içinde Aktarımdaki gösterilmektedir.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>IPSec ilkesi ile çalışma

Windows şifreleme, IPSec ilkesi ile ilişkilidir. IPSec ilkesi hangi IP trafiği güvenli ve IP paketlerini uygulanan güvenlik mekanizmasını tanımlar.
**IPSec ilkeleri** şu öğelerden oluşan: **filtre listeleri**, **filtre eylemleri**, ve **güvenlik kuralları**.

IPSec ilkesi yapılandırırken aşağıdaki IPSec ilkesi terimleri anlamak önemlidir:

* **IPSec ilkesi:** kuralları koleksiyonu. Yalnızca bir ilke ("belirli bir zamanda atanan") etkin olabilir. Her ilke, bunların tümü aynı anda etkin olabilir, bir veya daha fazla kural bulunabilir. Bir bilgisayar yalnızca bir etkin IPSec ilkesinin atanabilir adresindeki saati verildiğinde. Ancak, IPSec ilkesi içinde farklı durumlarda büyütülmesine harcanabilir birden fazla eylem tanımlayabilirsiniz. Her bir IPSec kuralları kümesi, kuralın geçerli olduğu ağ trafiği türü etkileyen bir filtre listesi ile ilişkilendirilir.

* **Filtre listeleri:** filtre listeleri, bir veya daha fazla filtre paketidir. Bir liste, birden çok filtre içerebilir. Filtre iletişim izin, güvenliği veya IP adres aralıkları, protokolleri veya hatta belirli protokolü bağlantı noktasını göre engellenen tanımlar. Her filtre, belirli bir koşul kümesini eşleşir; Örneğin, belirli bir alt ağdan belirli bir bilgisayar üzerinde belirli hedef bağlantı noktasına gönderilen paketler. Ağ koşulları, bir veya daha fazla bu filtreler eşleştiğinde, filtrenin etkinleştirilir. Her filtre, belirli bir filtre listesi içinde tanımlanır. Filtreler, filtre listeleri arasında paylaşılamaz. Ancak, belirli filtresi listesi, birden fazla IPSec ilkelerine eklenebilir. 

* **Filtre eylemleri:** bir dizi güvenlik algoritmalar, protokolleri, bir güvenlik yöntemi tanımlar ve anahtar bir bilgisayar IKE görüşmelerinde sunar. Filtre, güvenlik yöntemleri, tercih sırasına göre sıralanmış listesini eylemlerdir.  Bir bilgisayarı bir IPSec oturumu belirleyici, kabul veya filtre Eylemler listesinde depolanan güvenlik ayarına göre teklifler gönderir.

* **Güvenlik kuralları:** belirleyen kuralları nasıl ve ne zaman bir IPSec ilkesi iletişimi korur. Kullandığı **filtresi listesi** ve **filtre eylemlerini** IPSec bağlantısı oluşturmak için bir IPSec kuralı oluşturmak için. Her ilke, bunların tümü aynı anda etkin olabilir, bir veya daha fazla kural bulunabilir. Her kural, IP filtreleri ve bu filtre listesi ile eşleşmeyi üzerinde gerçekleşmesi güvenlik eylemleri koleksiyonunu listesini içerir:
  * IP filtresi eylemleri
  * Kimlik doğrulama yöntemleri
  * IP tünel ayarları
  * Bağlantı türleri

[![5]][5]

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki önkoşulları karşıladığından emin olun:

* Grup İlkesi ayarlarını uygulamak için kullanabileceğiniz çalışan bir Active Directory yapılandırması olmalıdır. GPO'ları hakkında daha fazla bilgi için bkz: [Grup İlkesi nesneleri](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir.
  * ExpressRoute devresi oluşturma hakkında daha fazla bilgi için bkz: [ExpressRoute devresi oluşturma](expressroute-howto-circuit-arm.md). 
  * Bağlantı sağlayıcınız tarafından bağlantı hattının etkinleştirildiğinden emin olun. 
  * Bağlantı hattınız için yapılandırılmış Azure özel eşleme sahip olduğunuzu doğrulayın. Bkz: [yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md) makale için yönlendirme yönergeleri. 
  * Bir sanal ağ ile oluşturulan ve tam olarak sağlanan sanal ağ geçidi sahip olduğunuzu doğrulayın. Yönergelerini izleyin [ExpressRoute için sanal ağ geçidi oluşturma](expressroute-howto-add-gateway-resource-manager.md). ExpressRoute için sanal ağ geçidi, GatewayType 'ExpressRoute' VPN'değil kullanır.

* ExpressRoute sanal ağ geçidi ExpressRoute işlem hattına bağlı olması gerekir. Daha fazla bilgi için [bir ExpressRoute bağlantı hattına bir VNet bağlama](expressroute-howto-linkvnet-arm.md).

* Azure Windows Vm'leri bir Vnet'e dağıtıldığını doğrulayın.

* Şirket içi ana bilgisayarlar ve Azure Vm'leri arasında bağlantı olduğundan emin olun.

* Azure Windows VM ve şirket içi ana bilgisayarlarına düzgün adlarını çözümlemek için DNS kullanabilir olduğundan emin olun.

### <a name="workflow"></a>İş akışı

1. Bir GPO oluşturun ve OU'ya ilişkilendirin.
2. Bir IPSec tanımlamak **filtre eylemi**.
3. Bir IPSec tanımlamak **filtre listesi**.
4. IPSec ilkesi ile oluşturmak **güvenlik kuralları**.
5. IPSec GPO OU'ya atayın.

### <a name="example-values"></a>Örnek değerler

* **Etki alanı adı:** ipsectest.com

* **OU:** IPSecOU

* **Şirket içi Windows bilgisayar:** konak1

* **Azure Windows sanal makineleri:** vm1, vm2

## <a name="creategpo"></a>1. bir GPO oluşturun

1. Bir kuruluş birimine bağlı yeni bir GPO oluşturmak için Grup İlkesi Yönetimi ek bileşenini açın ve GPO bağlanacak OU'yu bulun. Örnekte, OU adlı **IPSecOU**. 

   [![9]][9]
2. Grup İlkesi Yönetimi ek bileşeninde, OU seçin ve sağ tıklayın. Bulunan açılır menüye tıklayarak "**bu etki alanında GPO oluştur ve buraya bağla …** ".

   [![10]][10]
3. GPO, kolay, daha sonra bulabilmesi için sezgisel bir adı. Tıklayın **Tamam** oluşturmak ve GPO'yu bağlayın.

   [![11]][11]

## <a name="enablelink"></a>2. GPO bağlantısını etkinleştirin

GPO OU'ya uygulamak için GPO yalnızca kuruluş bağlanmalıdır değil, ancak bağlantı etkinleştirilmiş olması da gerekir.

1. Oluşturduğunuz GPO'ya sağ tıklayın ve seçin bulun **Düzenle** açılır listeden.
2. GPO OU'ya uygulamak için seçin **bağlantı etkin**.

   [![12]][12]

## <a name="filteraction"></a>3. IP filtre eylemini tanımlayın

1. Açılan listeden, sağ **IP güvenlik ilkesi Active Directory üzerinde**ve ardından **yönetme IP filtresi listelerini ve filtre eylemler...** .

   [![15]][15]
2. Üzerinde "**Yönet filtre eylemleri**" sekmesini tıklatın, **Ekle**.

   [![16]][16]

3. Üzerinde **IP Güvenlik filtreleme eylemini Sihirbazı**, tıklayın **sonraki**.

   [![17]][17]
4. Böylece daha sonra bulabilmeniz filtreleme eylemini kullanımı kolay adı. Bu örnekte filtreleme eylemini adlı **myEncryption**. Ayrıca, bir açıklama da ekleyebilirsiniz. Ardından **İleri**'ye tıklayın.

   [![18]][18]
5. **Güvenlik anlaşması** başka bir bilgisayarla IPSec kurulamıyorsa davranışı tanımlamanızı sağlar. Seçin **güvenlik anlaşması**, ardından **sonraki**.

   [![19]][19]
6. Üzerinde **IPSec desteği olmayan bilgisayarlarla Communicating** sayfasında **iletişime izin verme**, ardından **sonraki**.

   [![20]][20]
7. Üzerinde **IP trafiğini ve güvenlik** sayfasında **özel**, ardından **ayarları...** .

   [![21]][21]
8. Üzerinde **özel güvenlik yöntemi ayarları** sayfasında **veri bütünlüğü ve şifreleme (ESP): SHA1, 3DES**. ' A tıklayarak **Tamam**.

   [![22]][22]
9. Üzerinde **filtre eylemleri yönetme** sayfasında, gördüğünüz, **myEncryption** filtresi başarıyla eklendi. **Kapat**’a tıklayın.

   [![23]][23]

## <a name="filterlist1"></a>4. bir IP filtre listesi tanımlayın

Hedef bağlantı noktası 8080'ile şifrelenmiş HTTP trafik belirten bir filtre listesi oluşturun.

1. Hangi trafik türlerinin şifrelenmesi yapabilmek için kullanmak bir **IP filtresi listesi**. İçinde **IP Filtresi Listelerini Yönet** sekmesinde **Ekle** yeni IP filtre listesi eklemek için.

   [![24]][24]
2. İçinde **adı:** alanında, kendi IP filtresi listesi için bir ad yazın. Örneğin, **azure şirket içi HTTP8080**. ' A tıklayarak **Ekle**.

   [![25]][25]
3. Üzerinde **IP Filtresi açıklaması ve Yansımalı özelliği** sayfasında **Yansımalı**. Yansıtma ayarı için iki yönlü iletişim sağlayan her iki yönde de giden paketlerin eşleşir. Ardından **İleri**'ye tıklayın.

   [![26]][26]
4. Üzerinde **IP trafiğini kaynak** sayfasında gelen **kaynak adres:** açılır listesinde, seçin **belirli bir IP adresi veya alt ağ**. 

   [![27]][27]
5. Kaynak adresi belirtin **IP adresinin veya alt ağ:** IP trafiğinin, ardından **sonraki**.

   [![28]][28]
6. Belirtin **hedef adresi:** IP adresinin veya alt ağ. Ardından **İleri**'ye tıklayın.

   [![29]][29]
7. Üzerinde **IP protokol türü** sayfasında **TCP**. Ardından **İleri**'ye tıklayın.

   [![30]][30]
8. Üzerinde **IP protokolü bağlantı noktasını** sayfasında **herhangi bir bağlantı noktasından** ve **Bu bağlantı noktası:** . Tür **8080** metin kutusuna. Bu ayarlar, yalnızca HTTP trafiğini hedef bağlantı noktası 8080 üzerinde şifreleneceğini belirtir. Ardından **İleri**'ye tıklayın.

   [![31]][31]
9. IP filtre listesi görüntüleyin.  IP filtre listesi yapılandırmasını **azure şirket içi HTTP8080** aşağıdaki ölçütleri ile eşleşen tüm trafik için şifrelemeyi tetikleyen:

   * 10.0.1.0/24 (Azure Subnet2) herhangi bir kaynak adresi
   * 10.2.27.0/25 (şirket içi alt ağ) herhangi bir hedef adresi
   * TCP Protokolü
   * Hedef bağlantı noktası 8080

   [![32]][32]

## <a name="filterlist2"></a>5. IP filtre listesini düzenleyin

Aynı türde (Azure VM şirket içi konaktan) ters yönde trafiği şifrelemek için ikinci bir IP Filtresi gerekir. Yeni Filtre ayarlama işlemi ilk IP filtre ayarlamak için kullanılan aynı işlemidir. Tek fark, kaynak alt ağ ve hedef alt ağ vardır.

1. IP filtre listesine yeni bir IP filtre eklemek için seçin **Düzenle**.

   [![33]][33]
2. Üzerinde **IP filtresi listesi** sayfasında **Ekle**.

   [![34]][34]
3. Aşağıdaki örnekte ayarları kullanarak bir ikinci IP filtresini oluşturun:

   [![35]][35]
4. İkinci IP filtresini oluşturduktan sonra IP filtresi listesi şöyle görünür:

   [![36]][36]

Şifreleme bir şirket içi konumunuz ve mevcut IP filtresi listesi, değiştirme yerine bir uygulamayı korumak için Azure bir alt ağ arasında gerekiyorsa, bunun yerine yeni bir IP filtre listesi ekleyebilirsiniz. 2 IP ilişkilendirme bırakıldığına aynı IPSec ilkesi sağlar daha iyi esneklik belirli bir IP filtre listesi değiştirildiğinde veya diğer IP filtresi listeleri etkilemeden herhangi bir zamanda kaldırıldı.

## <a name="ipsecpolicy"></a>6. IPSec güvenlik ilkesi oluşturma 

IPSec ilkesi ile güvenlik kuralları oluşturun.

1. Seçin **Active Directory'de IPSecurity ilkeleri** OU ile ilişkili. Sağ tıklatın ve seçin **IP Güvenlik İlkesi Oluştur**.

   [![37]][37]
2. Güvenlik İlkesi adı. Örneğin, **İlkesi-azure-şirket içi**. Ardından **İleri**'ye tıklayın.

   [![38]][38]
3. Tıklayın **sonraki** onay kutusunu seçmeden.

   [![39]][39]
4. Doğrulayın **özelliklerini düzenleme** onay kutusu seçilidir ve ardından **son**.

   [![40]][40]

## <a name="editipsec"></a>7. IPSec güvenlik ilkesini düzenleyin

IPSec ilkesi için ekleme **IP filtresi listesi** ve **filtreleme eylemini** önceden yapılandırılmış.

1. HTTP ilkeye özellikleri **kuralları** sekmesinde **Ekle**.

   [![41]][41]
2. Hoş Geldiniz sayfasında tıklayın **sonraki**.

   [![42]][42]
3. Bir kural IPSec modunu tanımlayan seçeneği sunar: tünel modu veya aktarım modu.

   * Tünel modundaysa, özgün paketin IP üst kümesi tarafından kapsüllenir. Tünel modu, özgün paketin IP üst şifreleyerek iç yönlendirme bilgilerini korur. Tünel modu, ağ geçitleri siteden siteye VPN senaryoları arasında yaygın olarak uygulanır. Konaklar arasında uçtan uca şifreleme için kullanılan durum çoğunu tünel modu bileşenidir.

   * Yalnızca yükü ve tanıtım ESP taşıma modu şifreler; özgün paketin IP üst şifreli değil. Aktarım modunda, IP kaynak ve paketlerin IP hedef değiştirilmez.

   Seçin **bu kural bir tünel belirtmiyor**ve ardından **sonraki**.

   [![43]][43]
4. **Ağ türü** tanımlayan ağ güvenlik ilkesi ile bağlantı ilişkilendirir. Seçin **tüm ağ bağlantıları**ve ardından **sonraki**.

   [![44]][44]
5. Daha önce oluşturduğunuz IP filtresi listesi seçin **azure şirket içi HTTP8080**ve ardından **sonraki**.

   [![45]][45]
6. Varolan bir filtre eylemini seçin **myEncryption** daha önce oluşturduğunuz.

   [![46]][46]
7. Windows kimlik doğrulama dört farklı türlerini destekler: Kerberos, sertifikaları, NTLMv2 ve anahtarı'önceden paylaşılan. Etki alanına katılmış konaklarla çalışıyoruz çünkü seçin **varsayılan Active Directory (Kerberos V5 protokolü)** ve ardından **sonraki**.

   [![47]][47]
8. Yeni ilke güvenlik kuralı oluşturur: **azure şirket içi HTTP8080**. **OK (Tamam)** düğmesine tıklayın.

   [![48]][48]

Hedef bağlantı noktası 8080 IPSec aktarım modu kullanmak için tüm HTTP bağlantılarda IPSec ilkesi gerektirir. Düz metin Protokolü HTTP olduğu için etkin güvenlik ilkesine sahip veriler, ExpressRoute özel eşlemesi üzerinden aktarıldığında şifrelenir sağlar. IP güvenlik ilkesi Active Directory için daha Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı'nı yapılandırmak için daha karmaşık olmakla birlikte daha fazla özelleştirme IPSec bağlantısı ile ilgili izin vermiyor.

## <a name="assigngpo"></a>8. IPSec GPO 'sunu OU 'ya atayın

1. İlke görüntüleyin. Güvenlik grubu ilkesi tanımlı, ancak henüz atanmadı.

   [![49]][49]
2. Kuruluş güvenlik Grup İlkesi atamak için **IPSecOU**, Güvenlik İlkesi'ne sağ tıklayın ve seçtiğiniz **atama**.
   Her bilgisayar tht ait olduğu kuruluş atanmış güvenlik Grup İlkesi gerekir.

   [![50]][50]

## <a name="checktraffic"></a>Onay trafiği şifreleme

GPO uygulandı OU'SUNDA şifreleme görmek için tüm Azure sanal makinelerinde ve konak1 IIS yükleyin. Her IIS, 8080 bağlantı noktası HTTP isteklerine yanıt özelleştirilir.
Şifreleme doğrulamak için bir OU'daki tüm bilgisayarlarda ağ algılayıcı (gibi Wireshark) yükleyebilirsiniz.
Bir powershell betiği, 8080 numaralı bağlantı noktasındaki HTTP istekleri oluşturmak için bir HTTP istemcisi olarak çalışır:

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
Aşağıdaki ağ yakalama yalnızca şifrelenmiş trafik eşleşecek şekilde filtre ESP ile şirket içi konak1 için sonuçları görüntülemek gösterir:

[![51]][51]

Powershell komut dosyası üzerinde-premisies (HTTP istemci) çalıştırıyorsanız, Azure sanal ağ yakalama benzer bir izleme gösterir.

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "ExpressRoute aracılığıyla Ağ Diyagramı IPSec aktarım modu"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPSec trafiği ilginç"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPSec ilkesi"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Grup İlkesi'nde kuruluş birimi"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "OU ile ilişkili bir GPO oluştur"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "OU ile ilişkili GPO için bir ad atayın."
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "GPO'yu düzenleyin"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP filtresi listelerini yönetebilir ve filtre eylemleri"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "filtreleme eylemini ekleme"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "eylem Sihirbazı"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "filtre eylem adı"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filtre eylemi"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "davranıştır güvenli olmayan bir bağlantı kurulur belirtin"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "güvenlik mekanizması"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "özel güvenlik yöntemi"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "filtresi eylem listesi"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "yeni IP filtre listesi Ekle"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "IP Filtresi Ekle HTTP trafiği"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "her iki yönde de eşleşme paket"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "kaynak alt ağ seçimi"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "kaynak ağ"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "hedef ağ"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protokolü"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "kaynak bağlantı noktası ve hedef bağlantı noktası"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filtresi listesi"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP filtre listesi ile HTTP trafiği"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "ikinci IP filtre ekleme"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP filtresi listesi ikinci girdi"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP filtresi listesi ikinci girdi"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "IP güvenlik ilkesi oluşturma"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "IPSec ilkesinin adı"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPSec İlkesi Sihirbazı"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "IPSec ilkesi Düzenle"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "IPSec ilkesi için yeni güvenlik Kuralı Ekle"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "yeni bir güvenlik kuralı oluşturun"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "aktarım modu"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "ağ türü"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "seçimi mevcut IP filtre listesi"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "mevcut filtreleme eylemini seçimi"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "kimlik doğrulama yönteminin seçimi"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "güvenlik ilkesinin oluşturulması işlemi sonu"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "GPO'ya bağlı ancak atanmamış IPSec ilkesi"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "GPO'ya atanmış IPSec ilkesi"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "yakalama, IPSec şifrelenmiş trafik"
