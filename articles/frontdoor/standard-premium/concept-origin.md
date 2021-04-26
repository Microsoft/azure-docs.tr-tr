---
title: Azure ön kapısı Standart/Premium 'da kaynak ve kaynak grubu
description: Bu makalede, hangi kaynak ve kaynak grubunun bir Azure ön Kapıyapılandırmasında olduğu açıklanır.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100531"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Azure ön kapısı Standart/Premium 'da kaynak ve kaynak grubu (Önizleme)

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Bu makalede, Web uygulaması dağıtımınızın Azure ön kapısı Standart/Premium ile nasıl çalıştığı hakkındaki kavramlar ele alınmaktadır. Ayrıca, Azure ön kapısı Standart/Premium yapılandırmasında bir *kaynak* ve *kaynak grubu* hakkında bilgi edineceksiniz.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Kaynak

Azure ön kapı Standart/Premium kaynağı, uygulamanızın istemci isteklerinize hizmet veren ana bilgisayar adını veya genel IP 'sini ifade eder. Azure ön kapısı Standart/Premium, kaynak grubunda hem Azure hem de Azure dışı kaynakları destekler. Uygulama, şirket içi veri merkezinizde veya başka bir bulut sağlayıcısıyla de barındırılabilir. Kaynak, veritabanı katmanınız veya depolama katmanınız ile karıştırılmamalıdır. Kaynak, uygulamanızın arka ucunuz için genel uç nokta olarak görüntülenmelidir. Azure ön kapısı Standart/Premium kaynak grubuna bir kaynak eklediğinizde, aşağıdaki bilgileri de eklemeniz gerekir:

* **Kaynak türü:** Eklemek istediğiniz kaynak türü. Ön kapı, App Service, bulut hizmeti veya depolama alanından uygulamanızın arka uçlarınızın otomatik olarak geri türlerini destekler. Azure 'da veya Azure dışı arka uçta farklı bir kaynak istiyorsanız **özel ana bilgisayar**' ı seçin.

    >[!IMPORTANT]
    >Yapılandırma sırasında, kaynak ön kapı ortamından erişilebilir değilse API 'Ler doğrulanmaz. Ön kapısının kaynağınıza ulaşabildiğinizden emin olun.

* **Abonelik ve kaynak konak adı:** Arka uç ana bilgisayar türü için **özel ana bilgisayar** seçmediyseniz, uygun aboneliği ve ilgili arka uç ana bilgisayar adını seçerek arka ucunuzu seçin.

* **Kaynak ana bilgisayar üst bilgisi:** Her istek için arka uca gönderilen ana bilgisayar üst bilgisi değeri. Daha fazla bilgi için bkz. [kaynak ana bilgisayar üstbilgisi](#hostheader).

* **Öncelik:** Tüm trafik için birincil kaynak kullanmak istediğinizde, farklı kaynağınıza öncelikler atayın. Ayrıca birincil veya yedek kaynakları kullanılamıyorsa yedeklemeler sağlayın. Daha fazla bilgi için bkz. [Öncelik](#priority).

* **Ağırlık:** Trafiği, eşit veya ağırlık katlarına göre bir çıkış kümesi üzerinden dağıtmak için farklı kaynağınıza ağırlık atayın. Daha fazla bilgi için bkz. [ağırlıklar](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Kaynak barındırma üst bilgisi

Azure ön kapısı Standart/Premium tarafından bir kaynağa gönderilen istekler, kaynağın hedeflenen kaynağı almak için kullandığı bir ana bilgisayar üst bilgisi alanı içerecektir. Bu alanın değeri genellikle ana bilgisayar üst bilgisi ve bağlantı noktasına sahip olan kaynak URI 'sinden gelir.

Örneğin, için yapılan bir istek `www.contoso.com` ana bilgisayar üstbilgisine sahip olur `www.contoso.com` . Kaynağı yapılandırmak için Azure portal kullanırsanız, bu alan için varsayılan değer, arka ucun ana bilgisayar adıdır. Başlangıç kaynağınız Azure portal, `contoso-westus.azurewebsites.net` kaynak ana bilgisayar üst bilgisi için, oto doldurulmuş değer olacaktır `contoso-westus.azurewebsites.net` . Ancak, bu alanı açıkça ayarlamadan Azure Resource Manager şablonları veya başka bir yöntemi kullanırsanız, ön kapı, ana bilgisayar adını konak üstbilgisinin değeri olarak gönderir. İstek için yapıldıysa `www.contoso.com` ve Origin 'in `contoso-westus.azurewebsites.net` boş bir başlık alanı varsa, ön kapı ana bilgisayar üst bilgisini olarak ayarlar `www.contoso.com` .

Çoğu uygulama arka ucu (Azure Web Apps, BLOB depolama ve Cloud Services), ana bilgisayar üstbilgisinin arka ucun etki alanıyla eşleşmesini gerektirir. Ancak, arka ucunuza yönlendiren ön uç ana bilgisayar, gibi farklı bir ana bilgisayar adı kullanır `www.contoso.net` .

Kaynağınız ana bilgisayar üstbilgisinin arka uç ana bilgisayar adıyla eşleşmesini gerektiriyorsa, arka uç konak üstbilgisinin arka ucun ana bilgisayar adını içerdiğinden emin olun.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Kaynak ana bilgisayar üst bilgisini yapılandırma

Kaynak grubu bölümünde bir kaynak için **kaynak ana bilgisayar üst bilgisi** alanını yapılandırmak için:

1. Ön kapı kaynağınızı açın ve yapılandırılacak kaynağın bulunduğu kaynak grubunu seçin.

2. Yapmadıysanız bir kaynak ekleyin veya var olan bir kaynağı düzenleyin.

3. Kaynak ana bilgisayar üst bilgisi alanını özel bir değere ayarlayın veya boş bırakın. Gelen istek için konak adı, ana bilgisayar üst bilgisi değeri olarak kullanılacaktır.

## <a name="origin-group"></a>Kaynak grubu

Azure ön kapısı Standart/Premium 'daki bir kaynak grubu, uygulamaları için benzer trafiği alan bir çıkış kümesini ifade eder. Diğer bir deyişle, aynı trafiği alan ve beklenen bir davranışla yanıt veren dünya genelindeki uygulama örneklerinizin mantıksal bir gruplandırmasıdır. Bu kaynaklar, farklı bölgelerde veya aynı bölge içinde dağıtılabilir. Tüm kaynaklar etkin/etkin dağıtım modunda ya da etkin/Pasif yapılandırma olarak tanımlanmış olabilir.

Kaynak grubu, kaynakları sistem durumu araştırmaları aracılığıyla nasıl değerlendirileceğini tanımlar. Ayrıca, bunlar arasında yük dengelemenin nasıl gerçekleşeceğini tanımlar.

### <a name="health-probes"></a>Durum araştırmaları

Azure ön kapısının Standart/Premium, yapılandırılmış tüm kaynaklardan her birine düzenli HTTP/HTTPS araştırma istekleri gönderir. Araştırma istekleri, Son Kullanıcı isteklerinizin yük dengelenmesi için her bir başlangıcının yakınlığını ve sistem durumunu tespit edin. Kaynak grubunun sistem durumu araştırma ayarları, uygulama arka uçları 'nın sistem durumunu nasıl yokladığımızda tanımlar. Yük dengeleme yapılandırması için aşağıdaki ayarlar kullanılabilir:

* **Yol**: kaynak grubundaki tüm kaynaklar için araştırma istekleri IÇIN kullanılan URL. Örneğin, kaynaklardan biri ise `contoso-westus.azurewebsites.net` ve yol/Probe/test.aspx olarak ayarlandıysa, ön kapı ortamları, bu durumda protokol http olduğunda, durum araştırma istekleri öğesine gönderilir `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protokol**: durum araştırma Isteklerinin ön kapıdan, http veya https protokolüyle kaynaklarına mı gönderileceğini tanımlar.

* **Yöntem**: sistem durumu araştırmaları göndermek IÇIN kullanılacak http yöntemi. Seçenekler GET veya HEAD (varsayılan) içerir.
    > [!NOTE]
    > Ön kapı, arka uçlarınızın daha düşük yük ve maliyet için sistem durumu araştırmalarının baş isteklerini kullanmanızı önerir.

* **Aralık (saniye)**: kaynaklarına yönelik sistem durumu araştırmalarının sıklığını veya ön kapı ortamlarının her birinin bir araştırma gönderdiği aralıkları tanımlar.

    >[!NOTE]
    >Daha hızlı yük devretme için, aralığı daha düşük bir değere ayarlayın. Değer ne kadar düşükse, arka uçlarınızın aldığı durum araştırma hacmi daha yüksektir. Örneğin, sıklık, 100 ön kapı pop 'Ları ile 30 saniyeye ayarlandıysa, her arka uç dakikada yaklaşık 200 araştırma isteği alır.

Daha fazla bilgi için bkz. [sistem durumu araştırmaları](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Yük Dengeleme ayarları

Kaynak grubu için Yük Dengeleme ayarları, sistem durumu araştırmalarının nasıl değerlendirileceğini tanımlar. Bu ayarlar, kaynağın sağlıklı mı yoksa sağlıksız mi olduğunu belirlenir. Ayrıca, kaynak grubundaki farklı kaynaklar arasında trafiğin yük dengelemesini de denetler. Yük dengeleme yapılandırması için aşağıdaki ayarlar kullanılabilir:

* **Örnek boyutu:** Kaynak sistem durumu değerlendirmesi için göz önünde bulundurmanız gereken sistem durumu araştırmalarının kaç örnek olduğunu tanımlar.

* **Başarılı örnek boyutu:** Daha önce belirtilen örnek boyutunu, kaynak sağlıklı durumunu çağırmak için gereken başarılı örnek sayısını tanımlar. Örneğin, ön kapılı bir sistem durumu araştırma aralığının 30 saniye, örnek boyutu 5 olduğunu ve başarılı örnek boyutunun 3 olduğunu varsayalım. Kaynağınıza yönelik sistem durumu araştırmalarını her değerlendirdiğiniz her seferinde, 150 saniyelik son beş örnek (5 x 30) üzerinde bakıyoruz. Kaynağı sağlıklı olarak bildirmek için en az üç başarılı araştırmanın olması gerekir.

* **Gecikme süresi duyarlılığı (ekstra gecikme):** Azure ön kapısı Standart/Premium 'un isteği gecikme süresi ölçüm duyarlılığı aralığı içinde kaynağa göndermesini veya isteği en yakın arka uca iletmesinin gerekip gerekmediğini tanımlar.

Daha fazla bilgi için bkz. [en az gecikmeli tabanlı yönlendirme yöntemi](#latency).

## <a name="routing-methods"></a>Yönlendirme yöntemleri

Azure ön kapısının Standart/Premium, HTTP/HTTPS trafiğinizi farklı hizmet uç noktalarına nasıl yönlendirildiğini belirlemede farklı türlerde trafik yönlendirme yöntemlerini destekler. İstemciniz ön kapıya ulaşıldığında, isteklerin en iyi arka uç örneğine iletilmesini sağlamak için yapılandırılmış yönlendirme yöntemi uygulanır. 

Azure ön kapı Standart/Premium 'da dört trafik yönlendirme yöntemi mevcuttur:

* **[Gecikme süresi](#latency):** Gecikme tabanlı yönlendirme, isteklerin duyarlık aralığı içinde kabul edilebilir en düşük gecikme süreli arka uçlara gönderilmesini sağlar. Temel olarak, Kullanıcı istekleriniz, ağ gecikmesi açısından arka uçların "en yakın" kümesine gönderilir.
* **[Öncelik](#priority):** Tüm trafiğe hizmet vermek üzere birincil arka uç yapılandırmak istediğinizde arka uçlarınız için öncelikler atayabilirsiniz. İkincil arka uç, birincil arka ucun kullanılamaz duruma gelmesi durumunda bir yedekleme olabilir.
* **[Ağırlıklı](#weighted):** Trafiği bir arka uç kümesi genelinde dağıtmak istediğinizde, arka uçlarınızın ağırlıklarını atayabilirsiniz. Ağırlık katsayılarını eşit bir şekilde dağıtmak veya bu döneme göre yapmak isteyip istemediğiniz.

Tüm Azure ön kapı Standart/Premium yapılandırmalarında, arka uç durumu ve otomatik anlık genel yük devretme izleme bulunur. Daha fazla bilgi için bkz. [arka uç izleme](concept-health-probes.md). Ön kapılarınız, tek bir yönlendirme yönteminden birini temel alabilir. Ancak, uygulamanızın gereksinimlerine bağlı olarak, en iyi yönlendirme topolojisini oluşturmak için birden çok yönlendirme yöntemini de birleştirebilirsiniz.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>En düşük gecikme süreleri tabanlı trafik-yönlendirme

Arka uçlarda iki veya daha fazla konumda arka uçlar dağıtmak, son kullanıcılarınıza ' en yakın ' olan hedefe trafiği yönlendirerek uygulamalarınızın yanıt hızını iyileştirebilir. Ön kapı yapılandırmanıza yönelik varsayılan trafik yönlendirme yöntemi, istekleri son kullanıcılarınızın isteği alan ön kapı ortamının en yakın arka ucuna iletir. Azure ön kapısının tek noktaya yayın mimarisiyle birlikte bu yaklaşım, son kullanıcılarınızın her birinin konumlarına göre kişiselleştirilmiş en yüksek performansı almasını sağlar.

' En yakın ' arka ucu coğrafi mesafede ölçülen şekilde en yakın değildir. Bunun yerine, ön kapı ağ gecikmesini ölçerek en yakın arka uçları belirler.

Genel karar akışı aşağıda verilmiştir:

| Kullanılabilir arka uçlar | Öncelik | Gecikme sinyali (sistem durumu araştırmasına göre) | Çizgi |
|-------------| ----------- | ----------- | ----------- |
| İlk olarak, etkin olan ve sistem durumu araştırması için sağlıklı (200 Tamam) olarak döndürülen tüm arka uçları seçin. Altı arka ucu A, B, C, D, E ve F varsa ve C 'ler arasında iyi durumda değilse ve E devre dışı bırakılır. Kullanılabilir arka uçların listesi A, B, D ve F 'dir.  | Bundan sonra, kullanılabilir olanlar arasındaki en üst öncelik arka uçları seçilir. A, B ve D arka ucu öncelik 1 ve arka uç F 'nin önceliği 2 ' dir. Ardından, seçilen arka uçlar A, B ve D olur.| Gecikme aralığına sahip arka uçları seçin (en az gecikme süresi & gecikme süresi duyarlılığı). Arka uç A 15 MS ise, B 30 MS ve D, isteğin bulunduğu ön kapıdan, gecikme süresi 30 MS 'den 60 ms ise en düşük gecikme süresi, bir olan en yakın arka uçta 30 MS 'nin ötesinde. | Son olarak, ön kapı, belirtilen ağırlıkların oranıyla, son arka uçların seçili havuzu arasındaki trafiği bir kez daha yuvarlar. Yani, arka uç A 'nın ağırlığı 5, arka uç B ise 8 ağırlığa sahipse trafik, arka uçların A ve B arasındaki 5:8 oranına göre dağıtılır. |

>[!NOTE]
> Varsayılan olarak, gecikme süresi duyarlılığı özelliği 0 MS olarak ayarlanır, diğer bir deyişle, her zaman isteği kullanılabilir en hızlı arka uca iletir.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Öncelik tabanlı trafik-yönlendirme

Genellikle bir kuruluş, birincil birinin kapanması durumunda birden fazla yedekleme hizmeti dağıtarak Hizmetleri için yüksek kullanılabilirlik sağlamak istemektedir. Sektör genelinde, bu topoloji etkin/beklemede veya etkin/Pasif dağıtım topolojisi olarak da adlandırılır. ' Priority ' trafik-yönlendirme yöntemi, Azure müşterilerinin bu yük devretme modelini kolayca uygulamasına olanak tanır.

Varsayılan ön kapılarınız, arka uçların eşit öncelik listesini içerir. Varsayılan olarak, ön kapı trafiği yalnızca üst öncelikli arka uçlara (öncelik için en düşük değer), arka uçların birincil kümesi olarak gönderir. Birincil arka uçlar kullanılamıyorsa, ön kapı trafiği, trafiği ikincil arka uç kümesine yönlendirir (öncelik için ikinci en düşük değer). Birincil ve ikincil arka uçların her ikisi de yoksa, trafik üçüncü olarak gider ve bu şekilde devam eder. Arka ucun kullanılabilirliği, yapılandırılan durumu (etkin veya devre dışı) ve sistem durumu araştırmaları tarafından belirlendiği şekilde devam eden arka uç sistem durumunu temel alır.

#### <a name="configuring-priority-for-backends"></a>Backenler için önceliği yapılandırma

Ön kapı yapılandırmasının arka uç havuzundaki her arka uç, 1 ile 5 arasında bir sayı olabilen ' Priority ' adlı bir özelliğe sahiptir. Azure ön kapısının her arka uç için bu özelliği kullanarak arka uç önceliğini açıkça yapılandırırsınız. Bu özellik 1 ile 5 arasında bir değerdir. Daha düşük değerler daha yüksek bir öncelik temsil eder. Backenler, öncelik değerlerini paylaşabilir.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ağırlıklı trafik-yönlendirme yöntemi
' Ağırlıklı ' trafik-yönlendirme yöntemi, trafiği eşit bir şekilde dağıtmanıza veya önceden tanımlanmış bir ağırlığa kullanmanıza olanak sağlar.

Ağırlıklı trafik-yönlendirme yönteminde, arka uç havuzunuzun ön kapı yapılandırmasındaki her bir arka uca bir ağırlık atarsınız. Ağırlık 1-1000 arasında bir tamsayıdır. Bu parametre varsayılan ' 50 ' ağırlığını kullanır.

Kabul edilebilir gecikme süresine sahip kullanılabilir arka uçların listesi ile trafik, belirtilen ağırlıklar oranını kullanarak hepsini bir kez deneme mekanizmasıyla dağıtılır. Gecikme duyarlılığı 0 milisaniyeye ayarlandıysa, aynı ağ gecikmesi ile iki arka uç olmadıkça bu özellik geçerli olmaz. 

Ağırlıklı yöntem bazı faydalı senaryolara izin verebilir:

* **Aşamalı uygulama yükseltme**: yeni bir arka uca yönlendiren trafik yüzdesini verir ve diğer arka uçlarla eşit olarak geçen trafiği giderek zaman içinde arttırır.
* **Azure 'A uygulama geçişi**: hem Azure hem de dış arka uçlarla bir arka uç havuzu oluşturun. Arka uçların kalınlığını, yeni arka uçları tercih etmek için ayarlayın. Bu ayarı, yeni arka uçların devre dışı bırakılması, daha sonra en düşük ağırlıklarla birlikte atamak ve daha fazla trafik aldıkları düzeylere göre daha yavaş bir şekilde artırmak için kademeli olarak ayarlayabilirsiniz. Daha sonra son olarak tercih edilen arka uçları devre dışı bırakıp havuzdan kaldırma.  
* **Bulut-ek kapasite için**: şirket içi bir dağıtımı, ön kapıya geri yerleştirerek buluta hızlıca genişletin. Bulutta ek kapasiteye ihtiyaç duyduğunuzda, daha fazla arka uç ekleyebilir veya etkinleştirebilir ve trafiğin her bir arka uca ne kadar zaman gidebileceklerini belirtebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Ön kapı Standart/Premium oluşturmayı](create-front-door-portal.md) öğrenin
