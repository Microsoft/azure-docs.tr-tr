---
title: App Service işletim sistemi işlevselliği-Azure
description: Azure App Service Web Apps, mobil uygulama arka uçları ve API Apps için kullanılabilen işletim sistemi işlevleri hakkında bilgi edinin
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b108814caaace83cd417dc8858e27ed01d54c39e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066777"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Azure App Service işletim sistemi işlevselliği
Bu makalede, [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)üzerinde çalışan tüm Windows uygulamaları için kullanılabilen ortak temel işletim sistemi işlevleri açıklanmaktadır. Bu işlevsellik dosya, ağ ve kayıt defteri erişimini ve tanılama günlüklerini ve olaylarını içerir. 

> [!NOTE] 
> App Service [Linux uygulamaları](containers/app-service-linux-intro.md) kendi kapsayıcılarında çalışır. Konak işletim sistemine erişime izin verilmiyor, kapsayıcıya kök erişiminiz var. Benzer şekilde, [Windows kapsayıcılarında çalışan uygulamalar](app-service-web-get-started-windows-container.md)için, kapsayıcıya yönetici erişimi vardır ancak ana bilgisayar işletim sistemine erişemez. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service plan katmanları
App Service, müşteri uygulamalarını çok kiracılı bir barındırma ortamında çalıştırır. **Ücretsiz** ve **paylaşılan** katmanlarda dağıtılan uygulamalar, paylaşılan sanal makinelerde çalışan Işlemlerde çalışır, ancak **Standart** ve **Premium** katmanlarda dağıtılan uygulamalar, özellikle ilişkilendirilen uygulamalar için ayrılmış sanal makinelerde çalışır. tek bir müşteriyle.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

App Service farklı katmanlar arasında sorunsuz ölçekleme deneyimini desteklediğinden, App Service uygulamalar için zorlanan güvenlik yapılandırması aynı kalır. Bu, uygulamaların aniden farklı şekilde davranmamasını sağlar ve App Service plan bir katmandan diğerine geçiş yapar.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Geliştirme çerçeveleri
App Service fiyatlandırma katmanları, uygulamalar için kullanılabilir işlem kaynaklarının (CPU, disk depolama, bellek ve ağ çıkışı) miktarını denetler. Ancak, uygulamalar için kullanılabilir olan çerçeve işlevselliğinin sınırları, ölçekleme katmanlarından bağımsız olarak aynı kalır.

App Service, IIS içindeki ASP.NET, klasik ASP, Node. js, PHP ve Python-All gibi çeşitli geliştirme çerçevelerini destekler. Güvenlik yapılandırmasını basitleştirecek ve normalleştirebilmek için App Service uygulamalar genellikle çeşitli geliştirme çerçevelerini varsayılan ayarlarıyla çalıştırır. Uygulamaları yapılandırmaya yönelik bir yaklaşım, her bir geliştirme çerçevesi için API Surface alanını ve işlevselliğini özelleştirmek zorunda olabilir. Bunun yerine App Service, uygulamanın geliştirme çerçevesinden bağımsız olarak ortak bir işletim sistemi işlevselliği temelini etkinleştirerek daha genel bir yaklaşım gerçekleştirir.

Aşağıdaki bölümlerde, App Service uygulamalar için kullanılabilen genel işletim sistemi işlevselliği türleri özetlenmektedir.

<a id="FileAccess"></a>

## <a name="file-access"></a>Dosya erişimi
App Service içinde, yerel sürücüler ve ağ sürücüleri dahil çeşitli sürücüler vardır.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Yerel sürücüler
App Service, Azure PaaS (hizmet olarak platform) altyapısının üstünde çalışan bir hizmettir. Sonuç olarak, bir sanal makineye "eklenmiş" olan yerel sürücüler, Azure 'da çalışan tüm çalışan rolleri için kullanılabilir olan sürücü türleridir. Buna aşağıdakiler dahildir:

- Bir işletim sistemi sürücüsü (D:\ sürücü
- Yalnızca App Service (ve müşterilerle erişilemeyen) tarafından kullanılan Azure paketi cspkg dosyalarını içeren bir uygulama sürücüsü
- "Kullanıcı" sürücüsü (C:\ sürücü), boyutu VM 'nin boyutuna bağlı olarak değişir. 

Uygulamanız büyüdükçe disk kullanımınızı izlemeniz önemlidir. Disk kotasına ulaşıldığında, uygulamanız olumsuz etkileri olabilir. Örneğin: 

- Uygulama, diskte yeterli alan olmadığını belirten bir hata oluşturabilir.
- Kudu konsoluna gözatarken disk hataları görebilirsiniz.
- Azure DevOps veya Visual Studio 'dan dağıtım ile `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`başarısız olabilir.
- Uygulamanız performansı düşürebilir.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Ağ sürücüleri (diğer adıyla UNC paylaşımları)
Uygulama dağıtımını ve bakımını basit hale getiren App Service benzersiz yönlerinden biri, tüm Kullanıcı İçeriklerinin bir dizi UNC paylaşımı üzerinde depolanmasıdır. Bu model, birden çok yük dengeli sunucu içeren şirket içi web barındırma ortamları tarafından kullanılan, içerik depolama alanının ortak düzeniyle birlikte eşleşir. 

App Service içinde, her bir veri merkezinde oluşturulmuş bir dizi UNC paylaşımı vardır. Her bir veri merkezindeki tüm müşteriler için Kullanıcı içeriğinin yüzdesi her bir UNC paylaşımında ayrılır. Ayrıca, tek bir müşterinin aboneliğine ait tüm dosya içeriği her zaman aynı UNC paylaşımında yer alır. 

Azure hizmetlerinin çalışma biçimi nedeniyle, bir UNC paylaşımının barındırmasından sorumlu olan belirli bir sanal makine zaman içinde değişmeyecektir. UNC paylaşımlarının, Azure işlemlerinin normal kursu sırasında yukarı ve aşağı getirilebileceği şekilde farklı sanal makineler tarafından bağlanacağı garanti edilir. Bu nedenle, uygulamalar asla bir UNC dosya yolundaki makine bilgilerinin zaman içinde kararlı kalacağından önce sabit kodlanmış varsayımlar yapmamalıdır. Bunun yerine, App Service tarafından sağlanan uygun *sahte* mutlak yol, **D:\home\site** kullanmalıdır. Bu sahte mutlak yol, tek bir uygulamaya başvurmak için taşınabilir, uygulama ve kullanıcıya belirsiz bir yöntem sağlar. **D:\home\site**kullanarak, bir tane, her aktarım için yeni bir mutlak yol yapılandırmak zorunda kalmadan, paylaşılan dosyaları uygulamadan uygulamaya aktarabilir.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Bir uygulamaya verilen dosya erişimi türleri
Her bir müşterinin aboneliği, bir veri merkezi içindeki belirli bir UNC paylaşımında ayrılmış bir dizin yapısına sahiptir. Müşterinin belirli bir veri merkezinde oluşturulmuş birden çok uygulaması olabilir, bu nedenle tek bir müşteri aboneliğine ait tüm dizinler aynı UNC paylaşımında oluşturulur. Paylaşımda içerik, hata ve tanılama günlükleri gibi dizinler ve kaynak denetimi tarafından oluşturulan uygulamanın önceki sürümleri yer alabilir. Beklenen şekilde, bir müşterinin uygulama dizinleri, çalışma zamanında uygulamanın uygulama kodu tarafından okuma ve yazma erişimi için kullanılabilir.

Bir uygulamayı çalıştıran sanal makineye bağlı yerel sürücülerde App Service, C:\ üzerinde bir alan yığını ayırır. uygulamaya özgü geçici yerel depolama için sürücü. Bir uygulamanın kendi geçici yerel depolama alanına tam okuma/yazma erişimi olsa da, bu depolama gerçekten doğrudan uygulama kodu tarafından kullanılmaya yönelik değildir. Bunun yerine, IIS ve Web uygulaması çerçeveleri için geçici dosya depolaması sağlamaktır. App Service, tek tek uygulamaların çok miktarda yerel dosya depolama alanı tüketmesini engellemek için her bir uygulama için kullanılabilen geçici yerel depolama miktarını da sınırlar.

App Service geçici yerel depolamayı nasıl kullandığını gösteren iki örnek, geçici ASP.NET dosyaları için dizin ve IIS sıkıştırılmış dosyalar için dizin. ASP.NET derleme sistemi, geçici bir derleme önbelleği konumu olarak "Temporary ASP.NET Files" dizinini kullanır. IIS, sıkıştırılmış yanıt çıkışını depolamak için "IIS Temporary Compressed Files" dizinini kullanır. Bu dosya kullanımı türlerinin her ikisi de (diğer bir deyişle diğerleri), uygulama başına geçici yerel depolama ile App Service eşleştirilir. Bu yeniden eşleme, işlevselliğin beklendiği gibi devam etmesini sağlar.

App Service içindeki her uygulama, aşağıda açıklandığı gibi, "uygulama havuzu kimliği" olarak adlandırılan rastgele benzersiz bir düşük ayrıcalıklı çalışan işlem kimliği olarak çalışır: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). Uygulama kodu, işletim sistemi sürücüsüne yönelik temel salt okuma erişimi için bu kimliği kullanır (D:\ sürücü). Bu, uygulama kodunun ortak dizin yapılarını listeme ve işletim sistemi sürücüsünde ortak dosyaları okuyabileceği anlamına gelir. Bu durum biraz geniş bir erişim düzeyi gibi görünse de, Azure 'da barındırılan bir hizmette çalışan rolü sağladığınızda ve sürücü içeriğini okurken aynı dizinlere ve dosyalara erişilebilir. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Birden çok örnek arasında dosya erişimi
Ana Dizin, bir uygulamanın içeriğini içerir ve uygulama kodu buna yazabilir. Bir uygulama birden çok örnek üzerinde çalışıyorsa, tüm örneklerin aynı dizini görebilmesi için giriş dizini tüm örnekler arasında paylaşılır. Bu nedenle, örneğin, bir uygulama karşıya yüklenen dosyaları giriş dizinine kaydederse, bu dosyalar tüm örnekler için hemen kullanılabilir. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Ağ erişimi
Uygulama kodu, dış hizmetleri kullanıma sunan Internet 'e erişilebilir uç noktalara giden ağ bağlantıları oluşturmak için TCP/IP ve UDP tabanlı protokolleri kullanabilir. Uygulamalar, Azure 'daki hizmetlere bağlanmak için aynı protokolleri kullanabilir, örneğin, SQL veritabanı 'na HTTPS bağlantıları kurarak.

Ayrıca, uygulamaların tek bir yerel geri döngü bağlantısı kurması için sınırlı bir yetenek vardır ve bu yerel geri döngü yuvasını dinlemek için bir uygulama de vardır. Bu özellik birincil olarak, işlevselliğinin bir parçası olarak yerel geri döngü yuvaları üzerinde dinleme yapan uygulamaları etkinleştirmek için vardır. Her uygulama bir "özel" geridöngü bağlantısı görür. "A" uygulaması, "B" uygulaması tarafından belirlenen bir yerel geri döngü yuvasını dinleyemiyor.

Adlandırılmış Kanallar, toplu olarak bir uygulamayı çalıştıran farklı işlemler arasında işlem arası iletişim (IPC) mekanizması olarak da desteklenir. Örneğin, IIS FastCGI modülü PHP sayfalarını çalıştıran bireysel işlemleri koordine etmek için adlandırılmış kanalları kullanır.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Kod yürütme, süreçler ve bellek
Daha önce belirtildiği gibi uygulamalar, rastgele bir uygulama havuzu kimliği kullanarak düşük ayrıcalıklı çalışan işlemleri içinde çalışır. Uygulama kodu, çalışan işlemle ilişkili bellek alanına, ayrıca CGI işlemleri veya diğer uygulamalar tarafından üretilen alt işlemlere erişebilir. Ancak, bir uygulama aynı sanal makine üzerinde olsa bile başka bir uygulamanın belleğine veya verilerine erişemez.

Uygulamalar, desteklenen Web geliştirme çerçeveleri ile yazılmış betikleri veya sayfaları çalıştırabilir. App Service herhangi bir Web Framework ayarını daha kısıtlı moda yapılandırmaz. Örneğin, App Service çalıştıran uygulamalar, daha kısıtlı güven modunun aksine "tam" güvende çalışır. Klasik ASP ve ASP.NET dahil Web çerçeveleri, Windows işletim sisteminde varsayılan olarak kaydedilen ADO (ActiveX Data Objects) gibi işlem içi COM bileşenlerini (ancak işlem dışı COM bileşenleri) çağırabilir.

Uygulamalar rastgele kod oluşturabilir ve çalıştırabilir. Bir uygulamanın bir komut kabuğu oluşturma veya bir PowerShell betiği çalıştırma gibi şeyler yapması için izin verilebilir. Ancak, bir uygulamadan rastgele kod ve süreçler üretilmese de, çalıştırılabilir programlar ve betikler hala üst uygulama havuzuna verilen ayrıcalıklarla sınırlandırılır. Örneğin, bir uygulama giden HTTP çağrısını yapan bir yürütülebilir dosya oluşturabilir, ancak aynı yürütülebilir dosya bir sanal makinenin IP adresinin NIC 'sinden bağlantısını kaldırma girişiminde bulunabilir. Düşük ayrıcalıklı koda giden ağ çağrısı yapılmasına izin verilir, ancak sanal bir makinedeki ağ ayarlarını yeniden yapılandırma girişimi için yönetici ayrıcalıkları gerekir.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Tanılama günlükleri ve olayları
Günlük bilgileri, bazı uygulamaların erişmeyi deneyeceği başka bir veri kümesidir. App Service çalışan kod için kullanılabilir günlük bilgisi türleri, uygulama tarafından oluşturulan ve ayrıca uygulamaya kolayca erişilebilen bir uygulama tarafından oluşturulan tanılama ve günlük bilgilerini içerir. 

Örneğin, etkin bir uygulama tarafından oluşturulan W3C HTTP günlükleri, uygulama için oluşturulan ağ paylaşma konumundaki bir günlük dizininde ya da bir müşteri W3C günlük kaydı için ayarlandıysa BLOB depolama alanında kullanılabilir. İkinci seçenek, bir ağ paylaşımıyla ilişkili dosya depolama sınırlarını aşmadan büyük miktarlarda günlüklerin toplanmasını sağlar.

Benzer bir Vein, .NET uygulamalarından gelen gerçek zamanlı tanılama bilgileri, izleme bilgilerini uygulamanın ağ paylaşımıyla veya alternatif olarak bir BLOB depolama konumuna yazma seçenekleriyle birlikte .NET izleme ve tanılama altyapısı kullanılarak da kaydedilebilir.

Uygulamalar için kullanılamayan tanılama günlüğü ve izleme alanlarının Windows ETW olayları ve genel Windows olay günlükleri (örneğin, sistem, uygulama ve güvenlik olay günlükleri). ETW izleme bilgileri potansiyel olarak makine genelinde (doğru ACL 'lerle) görüntülenebildiğinden ETW olaylarına yönelik okuma ve yazma erişimi engellenir. Geliştiriciler, ETW olaylarını okumak ve yazmak için API çağrılarının ve ortak Windows olay günlüklerinin çalıştığını, ancak bu durum, App Service başarılı olarak görünmeleri için çağrıların "fapalama" olduğu fark edebilir. Gerçekte, uygulama kodunun bu olay verilerine erişimi yok.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Kayıt defteri erişimi
Uygulamalar üzerinde çalıştıkları sanal makinenin kayıt defterine çok (hiç olmasa da) salt okuma erişimi vardır. Bu uygulamada, bu, yerel kullanıcılar grubuna salt okuma erişimine izin veren kayıt defteri anahtarlarının uygulamalar tarafından erişilebilmesini sağlar. Kayıt defterinin, şu anda okuma veya yazma erişimi için desteklenmeyen bir alanı HKEY\_geçerli\_Kullanıcı kovanına ait.

Kullanıcı başına kayıt defteri anahtarlarına erişim de dahil olmak üzere kayıt defterine yazma erişimi engellenir. Uygulama açısından, uygulamaların farklı sanal makinelere geçirilmesi (ve yapması) için, uygulamanın perspektifinden, kayıt defterine yazma erişimi hiçbir şekilde Azure ortamında hiçbir şekilde güvenilmemelidir. Bir uygulama tarafından bağımlılaşabilecek tek kalıcı yazılabilir depolama, App Service UNC paylaşımlarında depolanan uygulama başına içerik dizini yapısıdır. 

## <a name="remote-desktop-access"></a>Uzak Masaüstü erişimi

App Service, sanal makine örneklerine uzak masaüstü erişimi sağlamaz.

## <a name="more-information"></a>Daha fazla bilgi

[Azure App Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -App Service yürütme ortamı hakkında en güncel bilgiler. Bu sayfa doğrudan App Service geliştirme ekibi tarafından korunur.

