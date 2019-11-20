---
title: App Service Ortamı v1 'yi Yapılandırma-Azure
description: App Service Ortamı v1 yapılandırma, yönetim ve izleme
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b8a05b7e8466187202e6a4d11efce288238cc19b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069944"
---
# <a name="configuring-an-app-service-environment-v1"></a>App Service Ortamı v1 yapılandırma

> [!NOTE]
> Bu makale App Service Ortamı v1 ile ilgilidir.  Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.
> 

## <a name="overview"></a>Genel Bakış
Yüksek düzeyde, Azure App Service Ortamı birçok ana bileşenden oluşur:

* App Service Ortamı barındırılan hizmette çalışan işlem kaynakları
* Depolama
* Bir veritabanı
* Klasik (v1) veya Kaynak Yöneticisi (v2) Azure sanal ağı (VNet) 
* Üzerinde çalışan App Service Ortamı barındırılan hizmeti olan bir alt ağ

### <a name="compute-resources"></a>İşlem kaynakları
Dört kaynak havuzunuz için işlem kaynaklarını kullanırsınız.  Her App Service Ortamı (Ao), bir dizi ön ucu ve üç olası çalışan havuzu içerir. Üç çalışan havuzunun hepsini kullanmanız gerekmez. isterseniz, yalnızca bir veya iki kullanabilirsiniz.

Kaynak havuzlarındaki (ön uçlar ve çalışanlar) konaklar, kiracıların doğrudan erişimine açık değildir. Uzak Masaüstü Protokolü (RDP) kullanarak bunlara bağlanabilir, sağlamayı değiştirebilir veya üzerinde yönetici olarak davranabilir.

Kaynak havuzu miktarını ve boyutunu ayarlayabilirsiniz. Ao 'da, P4 ile P1 etiketli dört boyutlu seçenekleriniz vardır. Bu boyutlar ve fiyatlandırmasıyla ilgili ayrıntılar için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).
Miktar veya boyutun değiştirilmesi, ölçek işlemi olarak adlandırılır.  Tek seferde yalnızca bir ölçeklendirme işlemi devam edebilir.

**Ön uçlar**: Ön uçlar, ASE 'niz içinde tutulan uygulamalarınız için HTTP/HTTPS uç noktalardır. Ön uçlarında iş yüklerini çalıştırmazsınız.

* ATıCı, geliştirme/test iş yükleri ve düşük düzeyli üretim iş yükleri için yeterli olan iki P2s ile başlar. Orta ila ağır üretim iş yükleri için P3S önemle önerilir.
* Orta ila ağır üretim iş yükleri için, zamanlanmış bakım gerçekleştiğinde çalışan yeterli ön uç olduğundan emin olmak için en az dört P3S olmasını öneririz. Zamanlanan bakım etkinlikleri, bir anda bir ön uç getirecek. Bu, bakım etkinlikleri sırasında genel kullanıma açık ön uç kapasitesini azaltır.
* Ön uçların sağlanması bir saate kadar sürebilir. 
* Daha fazla ölçek ince ayarlama için, ön uç havuzu için CPU yüzdesini, bellek yüzdesini ve etkin Istek ölçümlerini izlemeniz gerekir. P3S çalıştırılırken CPU veya bellek yüzdeleri yüzde 70 ' luk üstündeyse, daha fazla ön uç ekleyin. Etkin Istekler değeri ön uç başına 15.000 ile 20.000 arasında bir değere ortaalıyorsa, daha ön uçlar da eklemeniz gerekir. Genel amaç, CPU ve bellek yüzdelerini% 70 ' ın altında tutmaktır ve etkin Istekler, P3S çalıştırırken ön uç başına 15.000 istekten daha düşük bir değer sağlar.  

**Çalışanlar**: Çalışanlar, uygulamalarınızın aslında çalıştırıldığı yerdir. App Service planlarınızı ölçeklendirirseniz, bu, ilişkili çalışan havuzundaki çalışanları kullanır.

* Çalışanları anında ekleyemezsiniz. Sağlanması bir saate kadar zaman alabilir.
* Herhangi bir havuz için bir işlem kaynağının boyutunun ölçeklendirilmesi, güncelleştirme etki alanı başına < 1 saat sürer. Ao 'da 20 güncelleştirme etki alanı vardır. Bir çalışan havuzunun işlem boyutunu 10 örnek ile ölçeklendirdiyseniz tamamlanması en fazla 10 saat sürebilir.
* Bir çalışan havuzunda kullanılan işlem kaynaklarının boyutunu değiştirirseniz, o çalışan havuzunda çalışan uygulamaların soğuk başlamasına neden olursunuz.

Herhangi bir uygulamayı çalıştırmayan bir çalışan havuzunun işlem kaynağı boyutunu değiştirmek için en hızlı yol şunlardır:

* Çalışan miktarını 2 ' ye kadar ölçeklendirin.  Portalda en küçük ölçek azaltma boyutu 2 ' dir. Örneklerinizin serbest olması birkaç dakika sürer. 
* Yeni işlem boyutunu ve örnek sayısını seçin. Buradan, tamamlanması 2 saate kadar sürer.

Uygulamalarınız daha büyük bir işlem kaynağı boyutu gerektiriyorsa, önceki kılavuzdan faydalanabilirsiniz. Bu uygulamaları barındıran çalışan havuzunun boyutunu değiştirmek yerine, başka bir çalışan havuzunu istenen boyutun çalışanları ile doldurabilir ve uygulamalarınızı bu havuza taşıyabilirsiniz.

* Gerekli işlem boyutunun ek örneklerini başka bir çalışan havuzunda oluşturun. Bu işlem tamamlanması bir saate kadar sürer.
* Yeni yapılandırılan çalışan havuzuna daha büyük bir boyut gerektiren uygulamaları barındıran App Service planlarınızı yeniden atayın. Bu işlemin tamamlanması bir dakikadan kısa sürer.  
* Kullanılmayan örneklere artık ihtiyacınız yoksa ilk çalışan havuzunu ölçeklendirin. Bu işlemin tamamlanabilmesi birkaç dakika sürer.

**Otomatik ölçeklendirme**: İşlem kaynağı tüketiminizi yönetmenize yardımcı olabilecek araçlardan biri otomatik ölçeklendiriliyor. Ön uç veya çalışan havuzları için otomatik ölçeklendirmeyi kullanabilirsiniz. Sabah herhangi bir havuz türünün örneklerinizi artırma ve akşam de azaltma gibi işlemleri yapabilirsiniz. Ya da bir çalışan havuzunda kullanılabilir çalışan sayısı belirli bir eşiğin altına düştüğünde örnek ekleyebilirsiniz.

İşlem kaynak havuzu ölçümlerinin etrafında otomatik ölçeklendirme kuralları ayarlamak istiyorsanız, sağlama zamanının gerektirdiği süreyi aklınızda bulundurun. App Service ortamları otomatik ölçeklendirme hakkında daha fazla ayrıntı için bkz. [App Service ortamı otomatik ölçeklendirmeyi yapılandırma][ASEAutoscale].

### <a name="storage"></a>Depolama
Her Ao, 500 GB depolama ile yapılandırılır. Bu alan, Ao 'daki tüm uygulamalarda kullanılır. Bu depolama alanı ATıCı 'in bir parçasıdır ve şu anda depolama alanınızı kullanmak için geçiş yapılamıyor. Sanal ağ yönlendirmesinde veya güvenlik ayarları yapıyorsanız, yine de Azure Storage 'a erişime izin vermeniz gerekir; Aksi takdirde ATıCı çalışamaz.

### <a name="database"></a>Database
Veritabanı, ortamı tanımlayan bilgileri ve içinde çalışan uygulamalar hakkındaki ayrıntıları içerir. Bu, Azure 'da tutulan aboneliğin bir parçasıdır. Doğrudan işleyebilme özelliği olan bir şey değildir. Sanal ağ yönlendirmesinde veya güvenlik ayarları yapıyorsanız, SQL Azure erişime hala izin vermeniz gerekir veya ATıCı çalışamaz.

### <a name="network"></a>Ağ
ASE 'niz ile kullanılan VNet, ASE 'yi oluştururken veya bir süre önce yaptığınız sırada yaptığınız bir tane olabilir. AIN oluşturma sırasında alt ağ oluşturduğunuzda, Ao 'nun sanal ağla aynı kaynak grubunda olmasını zorlar. ASE 'niz tarafından VNet 'iniz tarafından farklı olmak üzere kullanılan kaynak grubuna ihtiyacınız varsa, bir Resource Manager şablonu kullanarak ASE 'nizi oluşturmanız gerekir.

Sanal ağ üzerinde as için kullanılan bazı kısıtlamalar vardır:

* Sanal ağ, bölgesel bir sanal ağ olmalıdır.
* Ao 'un dağıtıldığı 8 veya daha fazla adresi olan bir alt ağ olması gerekir.
* Bir AI barındırmak için bir alt ağ kullanıldıktan sonra, alt ağın adres aralığı değiştirilemez. Bu nedenle, alt ağın gelecekteki ATıCı büyümeye uyum sağlaması için en az 64 adres içermesi önerilir.
* Alt ağda başka hiçbir şey olmayabilir, ancak Ao.

ASA 'yı içeren barındırılan hizmetin aksine, [sanal ağ][virtualnetwork] ve alt ağ kullanıcı denetimi altındadır.  Sanal ağınızı sanal ağ kullanıcı arabirimi veya PowerShell üzerinden yönetebilirsiniz.  Bir ASE, klasik veya Kaynak Yöneticisi VNet 'e dağıtılabilir.  Portal ve API deneyimleri, klasik ve Kaynak Yöneticisi VNET 'ler arasında biraz farklıdır ancak Ao deneyimi aynıdır.

Bir ASE 'yi barındırmak için kullanılan VNet, özel RFC1918 IP adreslerini kullanabilir veya genel IP adreslerini kullanabilir.  RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) kapsamında olmayan bir IP aralığı kullanmak istiyorsanız, ASE oluşturma işlemi öncesinde ASE 'nin kullanacağı VNet ve alt ağınızı oluşturmanız gerekir.

Bu özellik Azure App Service sanal ağınıza yerleştirdiğinden, asa 'da barındırılan uygulamalarınızın artık doğrudan ExpressRoute veya siteden siteye sanal özel ağlar (VPN) üzerinden kullanılabilir hale getirilen kaynaklara erişebilmesini sağlar. App Service Ortamı içindeki uygulamalar, App Service Ortamı barındıran sanal ağın kullanabileceği kaynaklara erişmek için ek ağ özellikleri gerektirmez. Bu, içindeki veya sanal ağınıza bağlı kaynaklara ulaşmak için Sanal Ağ Tümleştirmesi ya da Karma Bağlantılar kullanmanız gerekmediği anlamına gelir. Sanal ağınıza bağlı olmayan ağlardaki kaynaklara erişmek için bu özelliklerden her ikisini de kullanabilirsiniz.

Örneğin, aboneliğinizdeki bir sanal ağla tümleştirilecek ancak ATıCı 'nizin bulunduğu sanal ağa bağlı olmayan Sanal Ağ Tümleştirmesi kullanabilirsiniz. Ayrıca, tıpkı normalde olduğu gibi diğer ağlardaki kaynaklara erişmek için Karma Bağlantılar de kullanabilirsiniz.  

Sanal ağınızın bir ExpressRoute VPN ile yapılandırılmış olması durumunda, bir ATıCı 'nin sahip olduğu bazı yönlendirme ihtiyaçlarını bilmelisiniz. ASA ile uyumlu olmayan bazı Kullanıcı tanımlı yol (UDR) yapılandırması vardır. ExpressRoute ile bir sanal ağda ATıCı çalıştırma hakkında daha fazla bilgi için bkz. [ExpressRoute ile sanal bir ağda App Service ortamı çalıştırma][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Gelen trafiğin güvenliğini sağlama
Ao size gelen trafiği denetlemek için iki birincil yöntem vardır.  Ağ güvenlik grupları 'nı (NSG 'ler) kullanarak, [bir App Service ortamı gelen trafiği denetleme hakkında](app-service-app-service-environment-control-inbound-traffic.md) burada açıklandığı gibi, Ao 'NIZE hangi IP adreslerinin erişebileceğini denetleyebilir ve atıcı 'Nizi bir iç Load Balancer (ILB) ile de yapılandırabilirsiniz.  Bu özellikler, NSG 'leri kullanarak erişimi ıLB Ace 'nize kısıtlamak istiyorsanız da birlikte kullanılabilir.

Bir ASE oluşturduğunuzda, VNet 'iniz içinde bir VIP oluşturur.  Harici ve dahili olmak üzere iki VIP türü vardır.  Bir dış VIP ile Ao oluşturduğunuzda, Ao 'daki uygulamalarınız internet yönlendirilebilir IP adresi aracılığıyla erişilebilir olacaktır. Dahili ' ı seçtiğinizde, Ao 'niz bir ıLB ile yapılandırılır ve doğrudan internet 'e erişilemeyecektir.  ILB Ao, hala bir dış VIP gerektirir, ancak yalnızca Azure yönetim ve bakım erişimi için kullanılır.  

ILB AI oluşturma sırasında ıLB Ao tarafından kullanılan alt etki alanını sağlarsınız ve belirttiğiniz alt etki alanı için kendi DNS 'nizi yönetmeniz gerekecektir.  Alt etki alanı adını ayarladığınız için, HTTPS erişimi için kullanılan sertifikayı da yönetmeniz gerekir.  AS oluşturulduktan sonra sertifikayı sağlamanız istenir.  Bir [App Service ortamı Ile iç Load Balancer kullanarak][ILBASE]bir ıLB atıcı oluşturma ve kullanma hakkında daha fazla bilgi edinmek için. 

## <a name="portal"></a>Portal
Azure portal Kullanıcı arabirimini kullanarak App Service Ortamı yönetebilir ve izleyebilirsiniz. Ao 'niz varsa, büyük olasılıkla Kenar çubuğunuzu App Service sembolünü görürsünüz. Bu simge, Azure portal App Service ortamlarını temsil etmek için kullanılır:

![App Service Ortamı simgesi][1]

App Service ortamlarınızın tümünü listeleyen Kullanıcı arabirimini açmak için simgeyi kullanabilir veya App Service ortamları seçmek için kenar çubuğunun altındaki köşeli çift ayracı (">" simgesini) seçebilirsiniz. Listelenen Aprlerden birini seçerek, izlemek ve yönetmek için kullanılan Kullanıcı arabirimini açarsınız.

![App Service Ortamı izlemek ve yönetmek için Kullanıcı arabirimi][2]

Bu ilk dikey pencere, kaynak havuzu başına bir ölçüm grafiği ile birlikte Ao 'nizin bazı özelliklerini gösterir. **Essentials** bloğunda gösterilen özelliklerden bazıları Ayrıca onunla ilişkili dikey pencerenin açılacağı köprülerdir. Örneğin, ATıCı 'nizin üzerinde çalıştığı sanal ağla ilişkili kullanıcı arabirimini açmak için **sanal ağ** adını seçebilirsiniz. **Planlar** ve **uygulamalar** App Service, Ao 'da bulunan bu öğeleri listelemek için her bir açılır dikey pencere.  

### <a name="monitoring"></a>İzleme
Grafikler, her kaynak havuzunda çeşitli performans ölçümlerini görmenizi sağlar. Ön uç havuzu için Ortalama CPU ve belleği izleyebilirsiniz. Çalışan havuzlarında, kullanılan miktarı ve kullanılabilir miktarı izleyebilirsiniz.

Birden çok App Service planı, çalışan havuzundaki çalışanları kullanabilir. İş yükü, ön uç sunucularıyla aynı şekilde dağıtılmaz, bu nedenle CPU ve bellek kullanımı yararlı bilgiler açısından pek çok sağlamaz. Ne kadar çalışan ve bu sistemi başkalarının kullanması için yönetiyorsanız, kaç çalışanın kullanıldığını ve kullanılabildiğini izlemek daha önemlidir.  

Uyarıları ayarlamak için grafiklerde izlenebilecek tüm ölçümleri de kullanabilirsiniz. Uyarıları ayarlama, App Service ile aynı şekilde çalışır. **Uyarılar** kullanıcı arabiriminden bir uyarı ayarlayabilir veya herhangi bir ölçüm Kullanıcı arabirimine detaya giderek **Uyarı Ekle**' ye seçim yapabilirsiniz.

![Ölçümler Kullanıcı arabirimi][3]

Yeni tartışılan ölçümler App Service Ortamı ölçümleridir. App Service plan düzeyinde kullanılabilen ölçümler de vardır. Bu, CPU ve bellek izlemenin çok daha anlamlı olmasını sağlar.

Bir Ao 'da, tüm App Service planları adanmış App Service planlardır. Diğer bir deyişle, bu App Service planına ayrılan konaklarda çalışan uygulamalar bu App Service planındaki uygulamalardır. App Service planınızdaki ayrıntıları görmek için, Ao Kullanıcı arabirimindeki listelerden veya **göz at App Service planlarındaki** listelerden herhangi birinden App Service planınızı alın (bunların tümünü listeler).   

### <a name="settings"></a>Ayarlar
ATıCı dikey penceresinde, birkaç önemli özelliği içeren bir **Ayarlar** bölümü vardır:

Ayar > **özellikleri**: Ao dikey penceresini getirdiğinizde **Ayarlar** dikey penceresi otomatik olarak açılır. Üst kısımdaki **Özellikler**. Burada, **temel**Özellikler 'de gördüklerinizle yedekli olan, ancak **sanal IP adresinin**yanı sıra **giden IP adreslerinden**çok yararlı olan birçok öğe vardır.

![Ayarlar dikey penceresi ve özellikleri][4]

Ayarlar > **IP adresleri**: Ao 'da bir IP Güvenli Yuva Katmanı (SSL) uygulaması oluşturduğunuzda, bir IP SSL adresiniz olması gerekir. Bir tane elde etmek için, Ao 'un ayrılabileceği, sahip olduğu adreslere IP SSL gerekir. Bir Ao oluşturulduğunda, bu amaç için bir IP SSL adresi vardır, ancak daha fazla bilgi ekleyebilirsiniz. [App Service fiyatlandırması][AppServicePricing] (SSL bağlantıları üzerindeki bölümde) gösterildiği gibi ek IP SSL adresler için ücretlendirilir. Ek fiyat IP SSL fiyatıdır.

**Ayarlar** > **ön uç havuzu** / **çalışan havuzları**: Bu kaynak havuzu dikey pencerelerinin her biri, kaynak havuzunu tamamen ölçeklendirmeye yönelik denetimler sağlamaya ek olarak, yalnızca o kaynak havuzundaki bilgileri görmenizi sağlar.  

Her kaynak havuzu için temel dikey pencere, bu kaynak havuzu için ölçümleri olan bir grafik sağlar. Ao dikey penceresindeki grafiklerle tıpkı olduğu gibi, grafiğe gidebilir ve uyarıları istediğiniz gibi ayarlayabilirsiniz. Belirli bir kaynak havuzu için AKEN dikey penceresinden bir uyarı ayarlanması, kaynak havuzundan yaptığı gibi aynı şeyi yapar. Çalışan havuzu **ayarları** dikey penceresinde bu çalışan havuzunda çalışan tüm uygulamalara veya App Service planlara erişebilirsiniz.

![Çalışan havuzu ayarları kullanıcı arabirimi][5]

### <a name="portal-scale-capabilities"></a>Portal ölçek özellikleri
Üç ölçeklendirme işlemi vardır:

* Ao 'da IP SSL kullanımı için kullanılabilen IP adreslerinin sayısını değiştirme.
* Kaynak havuzunda kullanılan işlem kaynağının boyutunu değiştirme.
* Kaynak havuzunda kullanılan işlem kaynaklarının sayısını el ile veya otomatik ölçeklendirmeyle değiştirme.

Portalda, kaynak Havuzlarınızda kaç tane sunucu olduğunu denetlemek için üç yol vardır:

* Üstteki ana Ao dikey penceresinden bir ölçeklendirme işlemi. Ön uç ve çalışan havuzlarında birden çok ölçek yapılandırma değişikliği yapabilirsiniz. Hepsi tek bir işlem olarak uygulanır.
* **Ayarlar**altında olan ayrı kaynak havuzu **Ölçek** dikey penceresinden el ile ölçeklendirme işlemi.
* Ayrı kaynak havuzu **Ölçek** dikey penceresinden ayarladığınız otomatik ölçeklendirme.

AI dikey penceresinde ölçek işlemini kullanmak için kaydırıcıyı istediğiniz miktara sürükleyin ve kaydedin. Bu Kullanıcı arabirimi, boyutun değiştirilmesini de destekler.  

![Ölçek Kullanıcı arabirimi][6]

Belirli bir kaynak havuzundaki el ile veya otomatik ölçeklendirme yeteneklerini kullanmak için, **Ayarlar** > **ön uç havuzu** / **çalışan havuzlarıyla** uygun şekilde gidin. Sonra değiştirmek istediğiniz havuzu açın. **Ayarlar**ölçeği genişletme veyaayarların > ölçeğini genişletme bölümüne gidin. >  **Ölçek Genişletme** dikey penceresi, örnek miktarını denetlemenize olanak sağlar. **Ölçeği artırma** , kaynak boyutunu denetlemenizi sağlar.  

![Ölçek ayarları kullanıcı arabirimi][7]

## <a name="fault-tolerance-considerations"></a>Hataya dayanıklılık konuları
Bir App Service Ortamı toplam 55 işlem kaynağını kullanacak şekilde yapılandırabilirsiniz. Bu 55 işlem kaynaklarından, iş yüklerini barındırmak için yalnızca 50 kullanılabilir. Bunun nedeni, Twofold 'dir. En az 2 ön uç işlem kaynağı vardır.  Bu, çalışan havuzu ayırmayı desteklemek için 53 'e kadar kalır. Hata toleransı sağlamak için, aşağıdaki kurallara göre ayrılan ek bir işlem kaynağınız olması gerekir:

* Her çalışan havuzunun, bir iş yüküne atanmak için kullanılamayan en az 1 ek işlem kaynağı olması gerekir.
* Bir çalışan havuzundaki işlem kaynakları miktarı belirli bir değere geçtiğinde, hata toleransı için başka bir hesaplama kaynağı gerekir. Bu, ön uç havuzundaki durum değildir.

Tek bir çalışan havuzunda, hata toleransı gereksinimleri, bir çalışan havuzuna atanan belirli bir X kaynakları değeri için kullanılır:

* X 2 ila 20 arasındaysa, iş yükleri için kullanabileceğiniz kullanılabilir işlem kaynakları miktarı X-1 ' dir.
* X 21 ile 40 arasındaysa, iş yükleri için kullanabileceğiniz kullanılabilir işlem kaynakları miktarı X-2 ' dir.
* X 41 ve 53 arasındaysa, iş yükleri için kullanabileceğiniz kullanılabilir işlem kaynakları miktarı X-3 ' dir.

En küçük parmak izi 2 ön uç sunucu ve 2 çalışan içerir.  Yukarıdaki deyimleri daha sonra açıklığa kavuşturacak birkaç örnek aşağıda verilmiştir:  

* Tek bir havuzda 30 çalışanunuz varsa, iş yüklerini barındırmak için 28 ' i kullanabilirsiniz.
* Tek bir havuzda 2 çalışanunuz varsa, iş yüklerini barındırmak için 1 kullanılabilir.
* Tek bir havuzda 20 çalışan varsa, iş yüklerini barındırmak için 19 kullanılabilir.  
* Tek bir havuzda 21 çalışanunuz varsa, iş yüklerini barındırmak için yine de yalnızca 19 kullanılabilir.  

Hataya dayanıklı en önemli değer önemlidir, ancak belirli eşiklerin üzerinde ölçeklendirerek bunu göz önünde bulundurmanız gerekir. 20 örneklerden daha fazla kapasite eklemek istiyorsanız 22 veya daha yüksek bir değere giderek 21 daha fazla kapasite eklemez. Aynı değer, kapasiteyi ekleyen bir sonraki sayının 42 ' den sonra da 40 geçerlidir.  

## <a name="deleting-an-app-service-environment"></a>App Service Ortamı silme
Bir App Service Ortamı silmek istiyorsanız, App Service Ortamı dikey pencerenin en üstündeki **silme** eylemini kullanmanız yeterlidir. Bunu yaptığınızda, gerçekten bunu yapmak istediğinizi onaylamak için App Service Ortamı adını girmeniz istenir. Bir App Service Ortamı sildiğinizde, içindeki içeriğin tümünü de sildiğini unutmayın.  

![App Service Ortamı Kullanıcı arabirimini silme][9]  

## <a name="getting-started"></a>Başlarken
App Service ortamları ile çalışmaya başlamak için bkz. [nasıl App Service ortamı oluşturma](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
