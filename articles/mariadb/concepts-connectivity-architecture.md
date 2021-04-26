---
title: Bağlantı mimarisi-MariaDB için Azure veritabanı
description: MariaDB sunucusu için Azure veritabanı 'nın bağlantı mimarisini açıklar.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: f2ea671a6d44d12b3b37d5d06fa9405b7c589cdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559426"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda bağlantı mimarisi
Bu makalede, MariaDB bağlantı mimarisi için Azure veritabanı 'nın yanı sıra trafiğin Azure 'daki ve dışındaki istemcilerden MariaDB örneğine nasıl yönlendirildiği açıklanmaktadır.

## <a name="connectivity-architecture"></a>Bağlantı mimarisi

MariaDB için Azure veritabanınıza bağlantı, gelen bağlantıları kümelerimize ait fiziksel konuma yönlendirmekten sorumlu bir ağ geçidiyle oluşturulmuştur. Aşağıdaki diyagramda trafik akışı gösterilmektedir.

![Bağlantı mimarisine genel bakış](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)


İstemci veritabanına bağlıyken, sunucuya bağlantı dizesi ağ geçidi IP adresine çözümlenir. Ağ Geçidi 3306 bağlantı noktasındaki IP adresini dinler. Veritabanı kümesi içinde trafik, MariaDB için uygun Azure veritabanına iletilir. Bu nedenle, şirket ağları gibi sunucunuza bağlanmak için, **giden trafiğin ağ geçitlerimize erişebilmesini sağlamak üzere istemci tarafı güvenlik duvarını** açmanız gerekir. Aşağıda, bölge başına ağ geçitlerimiz tarafından kullanılan IP adreslerinin tamamen bir listesini bulabilirsiniz.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>MariaDB ağ geçidi IP adresleri için Azure veritabanı

Ağ Geçidi Hizmeti, bir IP adresinin arkasında bulunan durum bilgisiz işlem düğümleri grubunda barındırılır. Bu, istemcinizin öncelikle MariaDB sunucusu için bir Azure veritabanına bağlanmaya çalışırken ulaşacak. 

Devam eden hizmet bakımının bir parçası olarak, en güvenli ve yüksek performanslı deneyim sağlamamız için ağ geçitlerini barındıran işlem donanımını düzenli olarak yenileyeceğiz. Ağ Geçidi donanımı yenilendiğinde, önce işlem düğümlerinin yeni bir halkası oluşturulur. Bu yeni halka, tüm yeni oluşturulan MariaDB sunucuları için Azure veritabanı sunucularına yönelik trafiğe hizmet verir ve trafiğin ayırt edilebilmesi için aynı bölgedeki eski ağ geçidi halkalarından farklı bir IP adresine sahip olur. Yeni halka tam çalışır olduktan sonra, mevcut sunuculara hizmet veren eski ağ geçidi donanımı kullanımdan kaldırma için planlanmaktadır. Bir ağ geçidi donanımını kullanımdan kaldırmadan önce, sunucularını çalıştıran ve eski ağ geçidi halkalarına bağlanan müşteriler, kullanımdan kaldırmadan önce üç ay içinde ve Azure portal üzerinden gönderilir. Ağ geçitlerinin yetkisini alma, şu durumlarda sunucularınız için bağlantıyı etkileyebilir 

* Ağ geçidi IP adreslerini uygulamanızın bağlantı dizesinde sabit olarak kodlayın. **Önerilmez**. Uygulamanızın bağlantı dizesinde. mariadb.database.azure.com biçiminde sunucunuzun tam etki alanı adını (FQDN) kullanmanız gerekir <servername> . 
* Giden trafiğin yeni ağ geçidi halkalarımıza erişebilmesini sağlamak için istemci tarafı güvenlik duvarında daha yeni ağ geçidi IP adreslerini güncelleştirmeyin.

Aşağıdaki tabloda, tüm veri bölgeleri için MariaDB ağ geçidi için Azure veritabanı ağ geçidi IP adresleri listelenmektedir. Her bölge için ağ geçidi IP adreslerinin en güncel bilgileri aşağıdaki tabloda tutulur. Aşağıdaki tabloda sütunlar aşağıdakileri temsil eder:

* **Ağ GEÇIDI IP adresleri:** Bu sütunda, en son nesil donanımda barındırılan ağ geçitlerinin geçerli IP adresleri listelenir. Yeni bir sunucu sağlıyorsanız, bu sütunda listelenen IP adresleri için giden trafiğe izin vermek üzere istemci tarafı güvenlik duvarını açmanız önerilir.
* **Ağ GEÇIDI IP adresleri (yetki alma):** Bu sütun, şu anda kullanımdan kaldırılan daha eski nesil bir donanımda barındırılan ağ geçitlerinin IP adreslerini listeler. Yeni bir sunucu sağlıyorsanız, bu IP adreslerini yoksayabilirsiniz. Var olan bir sunucunuz varsa, henüz kullanımdan çıkarmadığından bu IP adreslerinin güvenlik duvarının giden kuralını tutmaya devam edin. Bu IP adreslerine yönelik güvenlik duvarı kurallarını düşürüriz, bağlantı hataları alabilirsiniz. Bunun yerine, kullanımdan kaldırma bildirimi aldıktan hemen sonra ağ geçidi IP adresleri sütununda listelenen yeni IP adreslerini giden güvenlik duvarı kuralına önceden eklemeniz beklenir. Bu, sunucunuzun en son ağ geçidi donanımına geçirilmesi durumunda, sunucunuza yönelik bir kesinti olmadığından emin olur.
* **Ağ GEÇIDI IP adresleri (kullanımdan çıkarıldı):** Bu sütunlar, kullanımdan kaldırılan ve artık işlemlerdeki ağ geçidi halkalarının IP adreslerini listeler. Bu IP adreslerini giden güvenlik duvarı kuralınızdan güvenli bir şekilde kaldırabilirsiniz. 


| **Bölge adı** | **Ağ geçidi IP adresleri** |**Ağ geçidi IP adresleri (yetki alma)** | **Ağ geçidi IP adresleri (kullanımdan kaldırıldı)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Orta Avustralya| 20.36.105.0  | | |
| Avustralya Central2     | 20.36.113.0  | | |
| Doğu Avustralya | 13.75.149.87, 40.79.161.1     |  | |
| Avustralya Güneydoğu |191.239.192.109, 13.73.109.251   |  | |
| Güney Brezilya |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Orta Kanada |40.85.224.249  | | |
| Doğu Kanada | 40.86.226.166    | | |
| Central US | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Doğu Çin | 139.219.130.35    | | |
| Çin Doğu 2 | 40.73.82.1  | | |
| Kuzey Çin | 139.219.15.17    | | |
| Çin Kuzey 2 | 40.73.50.0     | | |
| Doğu Asya | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| Doğu ABD |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Doğu ABD 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| Orta Fransa | 40.79.137.0, 40.79.129.1  | | |
| Güney Fransa | 40.79.177.0     | | |
| Orta Almanya | 51.4.144.100     | | |
| Almanya Kuzey | 51.116.56.0 | |
| Almanya Kuzey Doğu | 51.5.144.179  | | |
| Almanya Orta Batı | 51.116.152.0 | |
| Hindistan Orta | 104.211.96.159     | | |
| Hindistan Güney | 104.211.224.146  | | |
| Hindistan Batı | 104.211.160.80    | | |
| Doğu Japonya | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Batı Japonya | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Güney Kore - Orta | 52.231.17.13   | 52.231.32.42 | |
| Güney Kore - Güney | 52.231.145.3     | 52.231.200.86 | |
| Orta Kuzey ABD | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Kuzey Avrupa | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Güney Afrika - Kuzey  | 102.133.152.0    | | |
| Güney Afrika - Batı | 102.133.24.0   | | |
| Orta Güney ABD |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Güneydoğu Asya | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| İsviçre Kuzey | 51.107.56.0 ||
| İsviçre Batı | 51.107.152.0| ||
| BAE Orta | 20.37.72.64  | | |
| BAE Kuzey | 65.52.248.0    | | |
| Güney Birleşik Krallık | 51.140.184.11   | | |
| Batı Birleşik Krallık | 51.141.8.11  | | |
| Orta Batı ABD | 13.78.145.25     | | |
| West Europe |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| Batı ABD |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Batı ABD 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Bağlantı yeniden yönlendirme

MariaDB için Azure veritabanı, istemci uygulamaları ve MariaDB sunucuları arasındaki ağ gecikmesini azaltmaya yardımcı olan ek bir bağlantı ilkesini, **yeniden yönlendirmeyi** destekler. Bu özellik ile, ilk TCP oturumu MariaDB sunucusu için Azure veritabanı 'na kurulduktan sonra sunucu, MariaDB sunucusunu istemciye barındıran düğümün arka uç adresini döndürür. Bundan sonra, sonraki tüm paketler doğrudan sunucuya akar ve ağ geçidini atlar. Paketler doğrudan sunucuya akar, gecikme süresi ve aktarım hızı performansı artırılmıştır.

Bu özellik, 10,2 ve 10,3 altyapı sürümleriyle MariaDB sunucuları için Azure veritabanı 'nda desteklenir.

Yeniden yönlendirme desteği, Microsoft tarafından geliştirilen PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) uzantısında mevcuttur ve, [](https://pecl.php.net/package/mysqlnd_azure) Uygulamalarınızda yeniden yönlendirmeyi kullanma hakkında daha fazla bilgi için [yeniden yönlendirmeyi yapılandırma](./howto-redirection.md) makalesine bakın.

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) uzantısında yeniden yönlendirme desteği şu anda önizleme aşamasındadır.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>Bu planlı bakımla ilgili bilmeniz gerekenler nelerdir?
Bu, yalnızca istemcilere saydam hale getiren bir DNS değişikidir. DNS sunucusunda FQDN için IP adresi değiştirildiğinde, yerel DNS önbelleği 5 dakika içinde yenilenir ve işletim sistemleri tarafından otomatik olarak yapılır. Yerel DNS yenilemesinde, tüm yeni bağlantılar yeni IP adresine bağlanır, eski IP adresleri tamamen kullanımdan kesilene kadar tüm mevcut bağlantılar eski IP adresine bağlı kalır. Kullanımdan kaldırmadan önce eski IP adresi üç-dört hafta boyunca kabaca ele alınacaktır; Bu nedenle, istemci uygulamaları üzerinde hiçbir etkisi olmaması gerekir.

### <a name="what-are-we-decommissioning"></a>Ne açığa aldık?
Yalnızca ağ geçidi düğümlerinin yetkisi alınacaktır. Kullanıcılar sunucularına bağlandıklarında, bağlantı sunucuya iletilmeden önce bağlantının ilk durağı ağ geçidi düğümüne gönderilir. Eski ağ geçidi halkalarını (sunucunun çalıştırıldığı kiracı halkaları değil) kullanımdan oluşturacağız, daha fazla açıklama için [bağlantı mimarisine](#connectivity-architecture) bakın.

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Bağlantılarınızın eski ağ geçidi düğümlerine veya yeni ağ geçidi düğümlerine gittiğini nasıl doğrulayabileceğiniz?
Örneğin, sunucunuzun FQDN 'sine ping gönderin  ``ping xxx.mariadb.database.azure.com`` . Döndürülen IP adresi, yukarıdaki belgede ağ geçidi IP adresleri (yetki alma) altında listelenen IP 'lerden biri ise, bağlantınızın eski ağ geçidi üzerinden gittiğini gösterir. Yazdığında, döndürülen IP adresi ağ geçidi IP adresleri altında listelenen IP 'lerden biri ise, bağlantınızın yeni ağ geçidi üzerinden gittiğini gösterir.

Ayrıca, bağlantı noktası 3306 ile istemci uygulamanızdan veritabanı sunucusunu [Psping](/sysinternals/downloads/psping) veya bir şekilde görüntüleyerek test edebilir ve dönüş IP adresi, YETKI alma IP adreslerinden biri olmadığından emin olabilirsiniz

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Nasıl yaparım?, bakımın ne zaman üzerinde olduğunu ve eski IP adresleri kullanımdan çalıştırıldığında başka bir bildirim alabilirim mi?
Bakım işini başlatacağız sizi bilgilendirmek için bir e-posta alacaksınız. Bakım, al bölgelerinde geçirilmesi gereken sunucu sayısına bağlı olarak bir aya kadar sürebilir. Lütfen istemcinizi FQDN kullanarak veya yukarıdaki tablodaki yeni IP adresini kullanarak veritabanı sunucusuna bağlanmasını hazırlayın. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>İstemci uygulamalarım hala eski ağ geçidi sunucusuna bağlanılıyorsa ne yapmam gerekiyor?
Bu, uygulamalarınızın FQDN yerine statik IP adresi kullanarak sunucuya bağlandığını gösterir. Bağlantı dizelerini ve bağlantı havuzu ayarını, AKS ayarını veya kaynak kodunda bile gözden geçirin.

### <a name="is-there-any-impact-for-my-application-connections"></a>Uygulama bağlantılarım için herhangi bir etki var mı?
Bu bakım yalnızca bir DNS değişiki olduğundan, istemciye saydamdır. İstemcide DNS önbelleği yenilendiğinde (işletim sistemi tarafından otomatik olarak yapılır), tüm yeni bağlantı yeni IP adresine bağlanır ve tüm mevcut bağlantı, eski IP adresi tam olarak kullanımdan kaldırılana kadar düzgün çalışmaya devam eder ve bu da genellikle birkaç hafta daha sonra olur. Bu durum için yeniden deneme mantığı gerekli değildir, ancak uygulamanın yeniden deneme mantığının yapılandırıldığını görmek iyi bir durumdur. Lütfen veritabanı sunucusuna bağlanmak veya uygulama bağlantı dizinizdeki yeni ' ağ geçidi IP adresleri ' listesini etkinleştirmek için FQDN 'yi kullanın.
Bu bakım işlemi var olan bağlantıları düşürülemiyor. Yalnızca yeni bağlantı isteklerinin yeni ağ geçidi çalmasına gitmesini sağlar.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>Bakım için belirli bir zaman penceresi isteyebilir miyim? 
Geçişin saydam olması ve müşterinin bağlantısına herhangi bir etkisi olmaması nedeniyle, kullanıcıların büyük bölümü için bir sorun olmayacaktır. Uygulamanızı önceden gözden geçirin ve veritabanı sunucusuna bağlanmak veya uygulama bağlantı dizinizdeki yeni ' ağ geçidi IP adresleri ' listesini etkinleştirmek için FQDN 'yi kullandığınızdan emin olun.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Özel bağlantı kullanıyorum, bağlantılarım etkilenecek mi?
Hayır, bu bir ağ geçidi donanım yetki alma işlemi ve özel bağlantı ya da özel IP adresleriyle herhangi bir ilişki olmaması durumunda yalnızca kullanımdan kaldırma IP adresleri altında belirtilen genel IP adreslerini etkiler.


## <a name="next-steps"></a>Sonraki adımlar

* [Azure portal kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-portal.md)
* [Azure CLı kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-cli.md)