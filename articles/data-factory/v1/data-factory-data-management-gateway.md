---
title: Data Factory için Veri Yönetimi ağ geçidi
description: Şirket içi ve bulut arasında veri taşımak için bir veri ağ geçidi ayarlayın. Verilerinizi taşımak için Azure Data Factory Veri Yönetimi ağ geçidini kullanın.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 41d8a5d3569d0b38ff569f9ccfa28a4b2af1d959
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682722"
---
# <a name="data-management-gateway"></a>Veri Yönetimi Ağ Geçidi
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [içinde şirket içinde barındırılan tümleştirme çalışma zamanı](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Veri Yönetimi ağ geçidi artık şirket içinde barındırılan Integration Runtime olarak yeniden markalı.

Veri yönetimi ağ geçidi, bulut ve şirket içi veri depoları arasında veri kopyalamak için şirket içi ortamınıza kurmanız gereken bir istemci aracısıdır. Data Factory tarafından desteklenen şirket içi veri depoları, [desteklenen veri kaynakları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) bölümünde listelenmiştir.

Bu makale, Şirket [içi ve bulut veri depoları arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesindeki izlenecek yolu tamamlar. İzlenecek yolda, verileri şirket içi SQL Server veritabanından Azure blobuna taşımak için ağ geçidini kullanan bir işlem hattı oluşturacaksınız. Bu makalede, veri yönetimi ağ geçidi hakkında ayrıntılı bilgi verilmektedir.

Birden çok şirket içi makineyi ağ geçidiyle ilişkilendirerek bir veri yönetimi ağ geçidini ölçeklendirebilirsiniz. Bir düğümde aynı anda çalışabilen veri taşıma işlerinin sayısını artırarak ölçeği artırabilirsiniz. Bu özellik, tek düğümlü bir mantıksal ağ geçidi için de kullanılabilir. Ayrıntılar için [Azure Data Factory makalesindeki veri yönetimi ağ geçidini ölçeklendirme](data-factory-data-management-gateway-high-availability-scalability.md) bölümüne bakın.

> [!NOTE]
> Şu anda, ağ geçidi Data Factory içinde yalnızca kopyalama etkinliği ve saklı yordam etkinliğini destekler. Şirket içi veri kaynaklarına erişmek için özel bir etkinlikten ağ geçidini kullanmak mümkün değildir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış
### <a name="capabilities-of-data-management-gateway"></a>Veri yönetimi ağ geçidi özellikleri
Veri yönetimi ağ geçidi aşağıdaki özellikleri sağlar:

* Aynı veri fabrikası içindeki şirket içi veri kaynaklarını ve bulut veri kaynaklarını modelleyin ve verileri taşıyın.
* Data Factory sayfasından, ağ geçidi durumunu izlemek ve yönetmek için tek bir cam bölmesi vardır.
* Şirket içi veri kaynaklarına erişimi güvenli bir şekilde yönetin.
  * Kurumsal Güvenlik Duvarı için değişiklik gerekmiyor. Ağ geçidi yalnızca İnternet 'i açmak için giden HTTP tabanlı bağlantılar sağlar.
  * Şirket içi veri depolarınız için kimlik bilgilerini sertifikanıza göre şifreleyin.
* Verileri verimli bir şekilde taşıma-veriler, otomatik yeniden deneme mantığı ile aralıklı ağ sorunlarına paralel ve dayanıklı olarak aktarılır.

### <a name="command-flow-and-data-flow"></a>Komut akışı ve veri akışı
Şirket içi ve bulut arasında veri kopyalamak için bir kopyalama etkinliği kullandığınızda, etkinlik şirket içi veri kaynağından buluta veri aktarmak için bir ağ geçidi kullanır ve tam tersi de geçerlidir.

En üst düzey veri akışı ve veri ağ geçidi ile kopyalama adımlarının Özeti: ağ geçidini kullanarak veri akışı ![](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Veri geliştirici, [Azure Portal](https://portal.azure.com) veya [PowerShell cmdlet 'ini](https://docs.microsoft.com/powershell/module/az.datafactory/)kullanarak bir Azure Data Factory için ağ geçidi oluşturur.
2. Veri geliştiricisi, ağ geçidini belirterek şirket içi veri deposu için bağlı bir hizmet oluşturur. Bağlı hizmeti ayarlamanın bir parçası olarak, veri geliştiricisi kimlik doğrulama türlerini ve kimlik bilgilerini belirtmek için kimlik bilgilerini ayarla uygulamasını kullanır. Kimlik bilgileri uygulama iletişim kutusu, bağlantıyı test etmek için veri deposuyla ve kimlik bilgilerini kaydetmek için ağ geçidine iletişim kurar.
3. Ağ Geçidi, kimlik bilgilerini buluta kaydetmeden önce ağ geçidiyle ilişkili sertifikayla (veri geliştiricisi tarafından sağlanan) kimlik bilgilerini şifreler.
4. Data Factory hizmet, paylaşılan bir Azure Service Bus kuyruğu kullanan bir denetim kanalı aracılığıyla işlerin & yönetimini zamanlamaya yönelik ağ geçidiyle iletişim kurar. Bir kopyalama etkinliği işinin devre dışı olması gerektiğinde, Data Factory kimlik bilgileri ile birlikte isteği sıraya alır. Ağ Geçidi, kuyruğu yokladıktan sonra işi devre dışı bırakır.
5. Ağ Geçidi, aynı sertifikayla kimlik bilgilerinin şifresini çözer ve ardından uygun kimlik doğrulama türü ve kimlik bilgileriyle şirket içi veri deposuna bağlanır.
6. Ağ Geçidi, verileri şirket içi bir mağazadan bulut depolama alanına kopyalar veya kopyalama etkinliğinin veri ardışık düzeninde nasıl yapılandırıldığına bağlı olarak tam tersi olur. Bu adım için ağ geçidi, güvenli (HTTPS) kanal üzerinden Azure Blob depolama gibi bulut tabanlı depolama hizmetleriyle doğrudan iletişim kurar.

### <a name="considerations-for-using-gateway"></a>Ağ geçidini kullanma konuları
* Birden çok şirket içi veri kaynağı için tek bir veri yönetimi ağ geçidi örneği kullanılabilir. Ancak, **tek bir ağ geçidi örneği yalnızca bir Azure Data Factory 'ye bağlıdır** ve başka bir veri fabrikası ile paylaşılamaz.
* Tek bir makineye **yalnızca bir veri yönetimi ağ geçidi örneği** yüklenmiş olabilir. Şirket içi veri kaynaklarına erişmesi gereken iki veri fabrikası olduğunu varsayalım, ağ geçitlerini iki şirket içi bilgisayara yüklemeniz gerekir. Diğer bir deyişle, bir ağ geçidi belirli bir veri fabrikasına bağlanır
* **Ağ geçidinin veri kaynağıyla aynı makinede olması gerekmez**. Ancak, veri kaynağında ağ geçidinin daha yakın olması, ağ geçidinin veri kaynağına bağlanması için geçen süreyi azaltır. Ağ geçidini, şirket içi veri kaynağını barındıran bilgisayardan farklı bir makineye yüklemenizi öneririz. Ağ geçidi ve veri kaynağı farklı makinelerinizde, ağ geçidi veri kaynağı olan kaynaklar için rekabet etmez.
* **Aynı şirket içi veri kaynağına bağlanan farklı makinelerde birden fazla ağ**geçidine sahip olabilirsiniz. Örneğin, iki veri fabrikası sunan iki ağ geçidi olabilir, ancak aynı şirket içi veri kaynağı hem veri fabrikaları ile kaydedilir.
* Bilgisayarınızda **Power BI** senaryoya hizmet eden bir ağ geçidiniz zaten varsa, başka bir makineye **Azure Data Factory için ayrı bir ağ geçidi** yükleyebilirsiniz.
* **ExpressRoute**kullandığınızda bile ağ geçidinin kullanılması gerekir.
* **ExpressRoute**kullandığınızda bile veri kaynağınızı şirket içi veri kaynağı (bir güvenlik duvarının arkasında olan) olarak değerlendirin. Hizmet ve veri kaynağı arasında bağlantı kurmak için ağ geçidini kullanın.
* Veri deposu bir **Azure IaaS VM**'sinde bulutta olsa bile **ağ geçidini kullanmanız** gerekir.

## <a name="installation"></a>Yükleme
### <a name="prerequisites"></a>Ön koşullar
* Desteklenen **Işletim sistemi** sürümleri şunlardır; Windows 7, Windows 8/8.1, Windows 10, windows Server 2008 R2, windows Server 2012, windows Server 2012 R2. Bir etki alanı denetleyicisine veri yönetimi ağ geçidi yüklemesi şu anda desteklenmiyor.
* .NET Framework 4.5.1 veya üzeri gereklidir. Windows 7 makinesine ağ geçidi yüklüyorsanız, .NET Framework 4,5 veya sonraki bir sürümü yükleyebilirsiniz. Ayrıntılar için [.NET Framework sistem gereksinimleri](https://msdn.microsoft.com/library/8z6watww.aspx) ' ne bakın.
* Ağ Geçidi makinesi için önerilen **yapılandırma** en az 2 GHz, 4 çekirdek, 8 GB RAM ve 80 GB disk.
* Ana makine hazırda beklemesi durumunda, ağ geçidi veri isteklerine yanıt vermez. Bu nedenle, ağ geçidini yüklemeden önce bilgisayarda uygun bir **güç planı** yapılandırın. Makine hazırda bekleme moduna yapılandırıldıysa, ağ geçidi yüklemesi bir ileti ister.
* Veri yönetimi ağ geçidini başarılı bir şekilde yüklemek ve yapılandırmak için makinede yönetici olmanız gerekir. **Veri yönetimi ağ geçidi kullanıcıları** yerel Windows grubuna daha fazla kullanıcı ekleyebilirsiniz. Bu grubun üyeleri, ağ geçidini yapılandırmak için **veri yönetimi ağ geçidi Configuration Manager** aracını kullanabilir.

Kopyalama etkinliği belirli bir sıklıkta gerçekleşirken, makinedeki kaynak kullanımı (CPU, bellek) de yoğun ve boşta zamanlarla aynı düzene uyar. Kaynak kullanımı Ayrıca, taşınmakta olan veri miktarına göre büyük ölçüde farklılık gösterir. Birden çok kopyalama işi devam ederken, yoğun saatlerde kaynak kullanımını görürsünüz.

### <a name="installation-options"></a>Yükleme seçenekleri
Veri yönetimi ağ geçidi aşağıdaki yollarla yüklenebilir:

* Bir MSI kurulum paketini [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' nden indirerek. MSI Ayrıca, mevcut veri yönetimi ağ geçidini en son sürüme yükseltmek için kullanılabilir ve tüm ayarlar korunur.
* El Ile kurulum altında **veri ağ geçidini indir ve yükle** bağlantısını tıklayarak veya **doğrudan bu bilgisayara** hızlı kurulum altında yükleyin. Hızlı kurulum 'u kullanma hakkında adım adım yönergeler için bkz. Şirket [içi ve bulut makaleleri arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) . El ile adım adım sizi indirme merkezi 'ne götürür. İndirme merkezinden ağ geçidini indirme ve yükleme yönergeleri sonraki bölümde yer alınır.

### <a name="installation-best-practices"></a>Yükleme en iyi yöntemleri:
1. Makinenin hazırda bekletmeden olmaması için, ağ geçidinin konak makinesinde güç planını yapılandırın. Ana makine hazırda beklemesi durumunda, ağ geçidi veri isteklerine yanıt vermez.
2. Ağ geçidiyle ilişkili sertifikayı yedekleyin.

### <a name="install-the-gateway-from-download-center"></a>Ağ geçidini indirme merkezinden yükleme
1. [Microsoft veri yönetimi ağ geçidi indirme sayfasına](https://www.microsoft.com/download/details.aspx?id=39717)gidin.
2. **İndir**' e tıklayın, **64 bit** sürümünü seçin (32 bit daha desteklenmez) ve **İleri**' ye tıklayın.
3. **MSI** 'yi doğrudan çalıştırın veya sabit diskinize kaydedin ve çalıştırın.
4. **Hoş geldiniz** sayfasında bir **dil** seçin, **İleri**' ye tıklayın.
5. Son Kullanıcı Lisans sözleşmesini **kabul edin** ve **İleri**' ye tıklayın.
6. Ağ geçidini yüklemek için **klasör** ' i seçin ve **İleri**' ye tıklayın.
7. **Yüklemeye hazırlanma** sayfasında, **yükler**' e tıklayın.
8. Yüklemeyi tamamlamaya **son** ' a tıklayın.
9. Azure portal anahtarı alın. Adım adım yönergeler için sonraki bölüme bakın.
10. Makinenizde çalışan **veri yönetimi ağ geçidi Configuration Manager** **ağ geçidini kaydet** sayfasında, aşağıdaki adımları uygulayın:
    1. Metni metne yapıştırın.
    2. İsteğe bağlı olarak, anahtar metnini görmek için **ağ geçidi anahtarını göster** ' e tıklayın.
    3. **Kaydol**' a tıklayın.

### <a name="register-gateway-using-key"></a>Anahtar kullanarak ağ geçidini kaydetme
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Portalda zaten bir mantıksal ağ geçidi oluşturmadıysanız
Portalda bir ağ geçidi oluşturmak ve **yapılandırma** sayfasından anahtarı almak için, Şirket [içi ve bulut makalesinde verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesindeki İzlenecek adımları izleyin.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Mantıksal ağ geçidini portalda zaten oluşturduysanız
1. Azure portal ' de, **Data Factory** sayfasına gidin ve **bağlı hizmetler** Kutucuğu ' na tıklayın.

    ![Data Factory sayfası](media/data-factory-data-management-gateway/data-factory-blade.png)
2. **Bağlı hizmetler** sayfasında, portalda oluşturduğunuz mantıksal **ağ geçidini** seçin.

    ![mantıksal ağ geçidi](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. **Veri ağ geçidi** sayfasında, **indir ve veri ağ geçidini yükle**' ye tıklayın.

    ![Portalda bağlantıyı indirin](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. **Yapılandır** sayfasında **anahtarı yeniden oluştur**' a tıklayın. Dikkatle okuduktan sonra uyarı iletisinde Evet ' e tıklayın.

    ![Anahtarı yeniden oluştur](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Anahtarın yanındaki Kopyala düğmesine tıklayın. Anahtar panoya kopyalanır.

    ![Anahtarı kopyalama](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Sistem tepsisi simgeleri/bildirimleri
Aşağıdaki görüntüde, gördüğünüz bazı tepsi simgeleri gösterilmektedir.

![sistem tepsisi simgeleri](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

İmleci sistem tepsisi simgesinin/bildirim iletisinin üzerine taşırsanız, bir açılan pencerede ağ geçidi/güncelleştirme işleminin durumuyla ilgili ayrıntıları görürsünüz.

### <a name="ports-and-firewall"></a>Bağlantı noktaları ve güvenlik duvarı
Göz önünde bulundurmanız gereken iki güvenlik duvarı vardır: kuruluşun merkezi yönlendiricisinde çalışan **Kurumsal güvenlik duvarı** ve ağ geçidinin yüklü olduğu yerel makinede bir daemon olarak yapılandırılmış **Windows Güvenlik Duvarı** .

![duvar](./media/data-factory-data-management-gateway/firewalls2.png)

Şirket güvenlik duvarı düzeyinde, aşağıdaki etki alanlarını ve giden bağlantı noktalarını yapılandırmanız gerekir:

| Etki alanı adları | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Veri taşıma hizmeti arka ucu ile iletişim için kullanılır |
| *. core.windows.net |443 |Azure Blob kullanılarak hazırlanan kopya için kullanılır (yapılandırıldıysa)|
| *. frontend.clouddatahub.net |443 |Veri taşıma hizmeti arka ucu ile iletişim için kullanılır |
| *.servicebus.windows.net |9350-9354, 5671 |Kopyalama Sihirbazı tarafından kullanılan TCP üzerinden isteğe bağlı Service Bus geçişi |

Windows güvenlik duvarı düzeyinde, bu giden bağlantı noktaları normalde etkindir. Aksi takdirde, etki alanlarını ve bağlantı noktalarını ağ geçidi makinesinde uygun şekilde yapılandırabilirsiniz.

> [!NOTE]
> 1. Kaynak/havuz temelinde, şirket/Windows Güvenlik duvarınızdaki ek etki alanlarını ve giden bağlantı noktalarını beyaz listeye almanız gerekebilir.
> 2. Bazı bulut veritabanları (örneğin, [Azure SQL veritabanı](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), vb.) için, ağ geçidi makinesinin IP adresini güvenlik duvarı yapılandırmasıyla beyaz listeye almanız gerekebilir.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kaynak veri deposundan havuz veri deposuna veri kopyalama
Güvenlik Duvarı kurallarının kurumsal güvenlik duvarı, ağ geçidi makinesindeki Windows Güvenlik Duvarı ve veri deposunun kendisi üzerinde düzgün şekilde etkinleştirildiğinden emin olun. Bu kuralların etkinleştirilmesi, ağ geçidinin hem kaynağa hem de havuza başarıyla bağlanmasına izin verir. Kopyalama işleminde yer alan her bir veri deposu için kuralları etkinleştirin.

Örneğin, **Şirket içi bir veri deposundan bir Azure SQL veritabanı havuzuna veya bir Azure SQL veri ambarı havuzuna**kopyalamak için aşağıdaki adımları uygulayın:

* Windows Güvenlik Duvarı ve kurumsal güvenlik duvarı için **1433** numaralı bağlantı noktasında giden **TCP** iletişimine izin verin.
* Azure SQL Server 'ın güvenlik duvarı ayarlarını, ağ geçidi makinesinin IP adresini izin verilen IP adresleri listesine ekleyecek şekilde yapılandırın.

> [!NOTE]
> Güvenlik duvarınız giden bağlantı noktası 1433 ' ye izin vermediğinden, ağ geçidi Azure SQL 'e doğrudan erişemez. Bu durumda, veritabanı/SQL Azure DW SQL Azure için [hazırlanan kopyayı](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) kullanabilirsiniz. Bu senaryoda, veri taşıma için yalnızca HTTPS (bağlantı noktası 443) gereklidir.
>
>

### <a name="proxy-server-considerations"></a>Proxy sunucusu konuları
Şirket ağı ortamınız Internet 'e erişmek için bir proxy sunucusu kullanıyorsa, veri yönetimi ağ geçidini uygun proxy ayarlarını kullanacak şekilde yapılandırın. İlk kayıt aşamasında ara sunucu ayarlayabilirsiniz.

![Kayıt sırasında proxy ayarla](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Ağ Geçidi, bulut hizmetine bağlanmak için proxy sunucusunu kullanır. İlk kurulum sırasında bağlantıyı **Değiştir** ' e tıklayın. **Ara sunucu ayarı** iletişim kutusunu görürsünüz.

![Yapılandırma Yöneticisi 'ni kullanarak proxy ayarlama](media/data-factory-data-management-gateway/SetProxySettings.png)

Üç yapılandırma seçeneği vardır:

* **Proxy kullanma**: ağ geçidi, bulut hizmetlerine bağlanmak için hiçbir proxy 'yi açık olarak kullanmaz.
* **Sistem proxy 'Si kullan**: ağ geçidi, diahost. exe. config ve diawp. exe. config içinde yapılandırılan proxy ayarını kullanır. Diahost. exe. config ve diawp. exe. config dosyasında yapılandırılmış bir proxy yoksa, ağ geçidi doğrudan proxy 'ye geçmeden bulut hizmetine bağlanır.
* **Özel ara sunucu kullan**: diahost. exe. config ve diawp. exe. config içinde yapılandırma kullanmak yerine, ağ geçidi IÇIN kullanılacak http proxy ayarını yapılandırın. Adres ve bağlantı noktası gereklidir. Kullanıcı adı ve parola, proxy 'nizin kimlik doğrulama ayarına bağlı olarak isteğe bağlıdır. Tüm ayarlar, ağ geçidinin kimlik bilgisi sertifikasıyla şifrelenir ve ağ geçidi ana makinesinde yerel olarak depolanır.

Güncelleştirilmiş proxy ayarlarını kaydettikten sonra veri yönetimi ağ geçidi konak hizmeti otomatik olarak yeniden başlatılır.

Ağ Geçidi başarıyla kaydedildikten sonra, proxy ayarlarını görüntülemek veya güncelleştirmek istiyorsanız, Veri Yönetimi ağ geçidi Configuration Manager kullanın.

1. **Veri yönetimi ağ geçidi Configuration Manager**başlatın.
2. **Ayarlar** sekmesine geçin.
3. Http proxy 'yi **Ayarla** iletişim kutusunu başlatmak Için **http proxy 'Si** bölümünde bağlantıyı **Değiştir** ' e tıklayın.
4. **İleri** düğmesine tıkladıktan sonra, proxy ayarını kaydetme izninizin olduğunu soran bir uyarı iletişim kutusu görürsünüz ve ağ geçidi ana bilgisayar hizmetini yeniden başlatın.

Configuration Manager aracını kullanarak HTTP proxy 'sini görüntüleyebilir ve güncelleştirebilirsiniz.

![Yapılandırma Yöneticisi 'ni kullanarak proxy ayarlama](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> NTLM kimlik doğrulaması ile bir ara sunucu ayarlarsanız, ağ geçidi ana bilgisayar hizmeti, etki alanı hesabı altında çalışır. Etki alanı hesabının parolasını daha sonra değiştirirseniz, hizmetin yapılandırma ayarlarını güncelleştirmeyi ve uygun şekilde yeniden başlatmayı unutmayın. Bu gereksinim nedeniyle, parolayı sık güncelleştirmenizi gerektirmeyen ara sunucuya erişmek için adanmış bir etki alanı hesabı kullanmanızı öneririz.
>
>

### <a name="configure-proxy-server-settings"></a>Proxy sunucusu ayarlarını yapılandırma
HTTP proxy için **sistem proxy ayarını kullan** ' ı seçerseniz, ağ geçidi diahost. exe. config ve diawp. exe. config dosyasındaki proxy ayarını kullanır. Diahost. exe. config ve diawp. exe. config dosyasında bir proxy belirtilmemişse, ağ geçidi doğrudan proxy 'ye geçmeden bulut hizmetine bağlanır. Aşağıdaki yordam diahost. exe. config dosyasını güncelleştirmek için yönergeler sağlar.

1. Dosya Gezgini 'nde, *C:\\\\program dosyalarının güvenli bir kopyasını oluşturun\\Microsoft veri yönetimi Gateway\\2,0\\paylaşılan\\diahost. exe. config* dosyasını özgün dosyayı yedeklemek için.
2. Yönetici olarak çalışan Notepad. exe ' yi başlatın ve metin dosyası *C:\\\\program dosyalarını açın\\Microsoft veri yönetimi ağ geçidi\\2,0\\paylaşılan\\diahost. exe. config*. Aşağıdaki kodda gösterildiği gibi, system.net için varsayılan etiketi bulabilirsiniz:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Daha sonra aşağıdaki örnekte gösterildiği gibi proxy sunucu ayrıntılarını ekleyebilirsiniz:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    ScriptLocation gibi gerekli ayarları belirtmek için proxy etiketi içinde ek özelliklere izin verilir. Sözdiziminde [Ara sunucu öğesi (ağ ayarları)](https://msdn.microsoft.com/library/sa91de1e.aspx) bölümüne bakın.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Yapılandırma dosyasını özgün konumuna kaydedin, sonra değişiklikleri sağlayan Veri Yönetimi ağ geçidi ana bilgisayar hizmetini yeniden başlatın. Hizmeti yeniden başlatmak için: Denetim Masası 'ndaki hizmetler uygulamasını kullanın veya **veri yönetimi ağ geçidi Configuration Manager** > **hizmeti Durdur** düğmesine tıklayın ve ardından **Başlat hizmetine**tıklayın. Hizmet başlamazsa, düzenlenmiş uygulama yapılandırma dosyasına yanlış bir XML etiketi söz dizimi eklenmiş olabilir.

> [!IMPORTANT]
> Hem diahost. exe. config hem **de** diawp. exe. config ' i güncelleştirmeyi unutmayın.

Bu noktalara ek olarak, Microsoft Azure şirketinizin beyaz listesinde olduğundan emin olmanız gerekir. Geçerli Microsoft Azure IP adresleri listesi [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=41653)' nden indirilebilir.

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Güvenlik Duvarı ve ara sunucu ile ilgili sorunlar için olası belirtiler
Aşağıdakine benzer hatalarla karşılaşırsanız, büyük olasılıkla güvenlik duvarının veya ara sunucunun yanlış yapılandırılması nedeniyle, ağ geçidinin kendi kimliğini doğrulamak için Data Factory 'e bağlanmasını engeller. Güvenlik duvarınızın ve ara sunucunuzun düzgün yapılandırıldığından emin olmak için önceki bölüme bakın.

1. Ağ geçidini kaydetmeye çalıştığınızda, şu hatayı alırsınız: "ağ geçidi anahtarı kaydedilemedi. Ağ Geçidi anahtarını yeniden kaydetmeyi denemeden önce, veri yönetimi ağ geçidinin bağlı durumda olduğunu ve Veri Yönetimi ağ geçidi ana bilgisayar hizmetinin başlatıldığını doğrulayın. "
2. Configuration Manager açtığınızda, durumu "bağlantısı kesildi" veya "bağlanıyor" olarak görürsünüz. Windows olay günlüklerini görüntülerken, "Olay Görüntüleyicisi" > "uygulama ve hizmet günlükleri" > "Veri Yönetimi ağ geçidi" altında aşağıdaki hata iletileri görürsünüz: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Kimlik bilgisi şifreleme için 8050 numaralı bağlantı noktasını açın
**Kimlik bilgilerini ayarlama** uygulaması, Azure Portal şirket içi bağlı bir hizmeti ayarlarken kimlik bilgilerini ağ geçidine geçirmek için **8050** numaralı bağlantı noktasını kullanır. Ağ geçidi kurulumu sırasında varsayılan olarak ağ geçidi yüklemesi, ağ geçidi makinesinde açılır.

Üçüncü taraf bir güvenlik duvarı kullanıyorsanız, 8050 numaralı bağlantı noktasını el ile açabilirsiniz. Ağ geçidi kurulumu sırasında güvenlik duvarı sorunuyla karşılaşırsanız, güvenlik duvarını yapılandırmadan ağ geçidini yüklemek için aşağıdaki komutu kullanmayı deneyebilirsiniz.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Ağ Geçidi makinesinde 8050 bağlantı noktasını açmayı seçerseniz, veri deposu kimlik bilgilerini yapılandırmak için **kimlik bilgilerini ayarlama** uygulamasını kullanmaktan farklı mekanizmaları kullanın. Örneğin, [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell cmdlet 'ini kullanabilirsiniz. Veri deposunun kimlik bilgilerinin nasıl ayarlanacağı hakkında bilgi için bkz. kimlik bilgileri ve güvenlik bölümü.

## <a name="update"></a>Güncelleştirme
Varsayılan olarak, veri yönetimi ağ geçidi, ağ geçidinin daha yeni bir sürümü kullanılabilir olduğunda otomatik olarak güncelleştirilir. Tüm zamanlanmış görevler tamamlanana kadar ağ geçidi güncellenmez. Güncelleştirme işlemi tamamlanana kadar ağ geçidi tarafından başka görev işlenmeyecektir. Güncelleştirme başarısız olursa, ağ geçidi eski sürüme geri alınır.

Zamanlanan güncelleştirme saatini aşağıdaki konumlarda görürsünüz:

* Azure portal ağ geçidi özellikleri sayfası.
* Veri Yönetimi ağ geçidi Configuration Manager ana sayfası
* Sistem tepsisi bildirim iletisi.

Veri Yönetimi ağ geçidi Configuration Manager Giriş sekmesinde güncelleştirme zamanlaması ve ağ geçidinin en son yüklendiği/güncelleştirildiği zaman görüntülenir.

![Güncelleştirmeleri zamanlama](media/data-factory-data-management-gateway/UpdateSection.png)

Güncelleştirmeyi hemen yükleyebilir veya ağ geçidinin zamanlanan saatte otomatik olarak güncelleştirilmesini bekleyebilirsiniz. Örneğin, aşağıdaki görüntüde, ağ geçidi Configuration Manager gösterilen bildirim iletisi ve hemen yüklemek için tıklamanız gereken güncelleştirme düğmesiyle birlikte gösterilir.

![DMG Configuration Manager güncelleştirme](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Sistem tepsisindeki bildirim iletisi aşağıdaki görüntüde gösterildiği gibi görünür:

![Sistem tepsisi iletisi](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Sistem tepsisinde güncelleştirme işleminin durumunu (el ile veya otomatik) görürsünüz. Ağ Geçidi Configuration Manager bir sonraki sefer başlattığınızda, bildirim çubuğunda ağ geçidinin güncelleştirildiğini ve yenilikler [konusunun](data-factory-gateway-release-notes.md)bağlantısını içeren bir ileti görürsünüz.

### <a name="to-disableenable-auto-update-feature"></a>Otomatik güncelleştirme özelliğini devre dışı bırakmak/etkinleştirmek için
Aşağıdaki adımları uygulayarak otomatik güncelleştirme özelliğini devre dışı bırakabilir/etkinleştirebilirsiniz:

[Tek düğümlü ağ geçidi için]
1. Ağ Geçidi makinesinde Windows PowerShell 'i başlatın.
2. *C:\\\\Program Files\\Microsoft Integration Runtime\\3,0\\PowerShellScript\\* klasörü ' ne geçin.
3. Otomatik güncelleştirme özelliğini devre dışı bırakmak için aşağıdaki komutu çalıştırın (devre dışı bırakın).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Yeniden açmak için:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Çok düğümlü yüksek düzeyde kullanılabilir ve ölçeklenebilir ağ geçidi için](data-factory-data-management-gateway-high-availability-scalability.md)
1. Ağ Geçidi makinesinde Windows PowerShell 'i başlatın.
2. *C:\\\\Program Files\\Microsoft Integration Runtime\\3,0\\PowerShellScript\\* klasörü ' ne geçin.
3. Otomatik güncelleştirme özelliğini devre dışı bırakmak için aşağıdaki komutu çalıştırın (devre dışı bırakın).

    Yüksek kullanılabilirlik özelliğine sahip ağ geçidi için ek bir AuthKey parametresi gerekir.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Yeniden açmak için:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Ağ geçidini yükledikten sonra, aşağıdaki yollarla Veri Yönetimi ağ geçidi Configuration Manager başlatabilirsiniz:

1. **Ara** penceresinde, bu yardımcı programa erişmek Için **veri yönetimi ağ geçidi** yazın.
2. Şu klasörde çalıştırılabilir *Configmanager. exe* dosyasını çalıştırın: *C:\\\\Program Files\\Microsoft Veri Yönetimi Gateway\\2,0\\paylaşılan*.

### <a name="home-page"></a>Giriş sayfası
Giriş sayfası aşağıdaki eylemleri yapmanıza olanak sağlar:

* Ağ geçidinin durumunu görüntüleyin (bulut hizmetine bağlı vs.).
* Portaldan bir anahtar kullanarak **kaydolun** .
* Ağ Geçidi makinesinde **veri yönetimi ağ geçidi ana bilgisayar hizmetini** **durdurup** başlatın.
* **Güncelleştirmeleri** günün belirli bir saatinde zamanlayın.
* Ağ geçidinin **en son güncelleştirildiği**tarihi görüntüleyin.

### <a name="settings-page"></a>Ayarlar sayfası
Ayarlar sayfası aşağıdaki eylemleri yapmanıza olanak sağlar:

* Ağ Geçidi tarafından kullanılan **sertifikayı** görüntüleyin, değiştirin ve dışarı aktarın. Bu sertifika, veri kaynağı kimlik bilgilerini şifrelemek için kullanılır.
* Uç nokta için **HTTPS bağlantı noktasını** değiştirin. Ağ Geçidi, veri kaynağı kimlik bilgilerini ayarlamak için bir bağlantı noktası açar.
* Uç noktanın **durumu**
* **SSL sertifikasını** görüntüle, Portal ve ağ GEÇIDI arasındaki SSL iletişimi için veri kaynaklarının kimlik bilgilerini ayarlamak üzere kullanılır.

### <a name="remote-access-from-intranet"></a>İntranetten uzaktan erişim
Bu işlevsellik daha sonra etkinleştirilecek. Yaklaşan güncelleştirmelerde (v 3.4 veya üzeri), kimlik bilgilerini şifrelemek için PowerShell veya Credential Manager uygulaması kullanılırken, bugün bağlantı noktası 8050 (yukarıdaki bölüme bakın) kullanılarak gerçekleşen tüm uzak bağlantıları etkinleştirmenizi/devre dışı bırakmanızı mümkün kılacağız.

### <a name="diagnostics-page"></a>Tanılama sayfası
Tanılama sayfası aşağıdaki eylemleri yapmanıza olanak sağlar:

* Ayrıntılı **günlüğü**etkinleştirin, Olay Görüntüleyicisi 'nde günlükleri görüntüleyin ve bir hata oluşursa günlükleri Microsoft 'a gönderin.
* Bir veri kaynağına **bağlantıyı test** edin.

### <a name="help-page"></a>Yardım sayfası
Yardım sayfasında aşağıdaki bilgiler görüntülenir:

* Ağ geçidinin kısa açıklaması
* Sürüm numarası
* Çevrimiçi yardım, gizlilik bildirimi ve lisans sözleşmesi bağlantıları.

## <a name="monitor-gateway-in-the-portal"></a>Portalda ağ geçidini izleme
Azure portal, bir ağ geçidi makinesindeki kaynak kullanımının (CPU, bellek, ağ (/dışarı), vb.) neredeyse gerçek zamanlı anlık görüntüsünü görüntüleyebilirsiniz.

1. Azure portal ' de, Data Factory 'nizin giriş sayfasına gidin ve **bağlı hizmetler** Kutucuğu ' na tıklayın.

    ![Data factory giriş sayfası](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. **Bağlı hizmetler** sayfasında **ağ geçidini** seçin.

    ![Bağlı hizmetler sayfası](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. **Ağ geçidi** sayfasında, ağ geçidinin bellek ve CPU kullanımını görebilirsiniz.

    ![Ağ geçidinin CPU ve bellek kullanımı](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Ağ kullanımı gibi daha fazla ayrıntı görmek için **Gelişmiş ayarları** etkinleştirin.
    
    ![Ağ geçidinin gelişmiş izlemesi](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Aşağıdaki tabloda **ağ geçidi düğümleri** listesindeki sütunların açıklamaları verilmiştir:

İzleme özelliği | Açıklama
:------------------ | :----------
Ad | Ağ geçidiyle ilişkili mantıksal ağ geçidinin ve düğümlerin adı. Düğüm, ağ geçidinin yüklü olduğu şirket içi bir Windows makinedir. Tek bir mantıksal ağ geçidinde birden fazla düğüm (en fazla dört düğüme) olma hakkında bilgi için, bkz. [veri yönetimi ağ geçidi-yüksek kullanılabilirlik ve ölçeklenebilirlik](data-factory-data-management-gateway-high-availability-scalability.md).
Durum | Mantıksal ağ geçidinin ve ağ geçidi düğümlerinin durumu. Örnek: çevrimiçi/çevrimdışı/sınırlı/vb. Bu durumlar hakkında daha fazla bilgi için bkz. [ağ geçidi durumu](#gateway-status) bölümü.
Sürüm | Mantıksal ağ geçidi ve her ağ geçidi düğümünün sürümünü gösterir. Mantıksal ağ geçidinin sürümü, gruptaki düğümlerin çoğunluğu sürümüne göre belirlenir. Mantıksal ağ geçidi kurulumunda farklı sürümlere sahip düğümler varsa, yalnızca mantıksal ağ geçidi ile aynı sürüm numarasına sahip düğümler düzgün şekilde çalışır. Diğerleri sınırlı moddadır ve el ile güncelleştirilmesi gerekir (yalnızca büyük/küçük harfe otomatik güncelleştirme başarısız olur).
Available memory | Bir ağ geçidi düğümündeki kullanılabilir bellek. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür.
CPU utilization | Bir ağ geçidi düğümünün CPU kullanımı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür.
Networking (In/Out) | Ağ Geçidi düğümünün ağ kullanımı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür.
Concurrent Jobs (Running/ Limit) | Her düğümde çalışan iş veya görev sayısı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. Limit her düğüm için en fazla eş zamanlı işi belirtir. Bu değer makine boyutuna göre tanımlanır. CPU/bellek/ağın kullanıldığı, ancak etkinliklerin zaman aşımına uğramasının gerektiği Gelişmiş senaryolarda, eşzamanlı iş yürütmeyi ölçeklendirmeye yönelik sınırı artırabilirsiniz. Bu özellik, tek düğümlü bir ağ geçidiyle (ölçeklenebilirlik ve kullanılabilirlik özelliği etkinleştirilmediği halde) da kullanılabilir.
Rol | Çok düğümlü bir ağ geçidi-dağıtıcı ve çalışan üzerinde iki tür rol vardır. Tüm düğümler çalışanlardır, yani işleri yürütmek için hepsi kullanılabilirler. Bulut hizmetlerinden görevler/işler çekmek ve bunları farklı çalışan düğümlerine (kendisi dahil) göndermek için kullanılan yalnızca bir dağıtıcı düğümü vardır.

Bu sayfada, ağ geçidinde iki veya daha fazla düğüm (genişleme senaryosu) olduğunda daha anlamlı bir ayar görürsünüz. Çok düğümlü bir ağ geçidi kurma hakkında ayrıntılı bilgi için bkz. [veri yönetimi ağ geçidi-yüksek kullanılabilirlik ve ölçeklenebilirlik](data-factory-data-management-gateway-high-availability-scalability.md) .

### <a name="gateway-status"></a>Ağ Geçidi durumu
Aşağıdaki tabloda **ağ geçidi düğümünün**olası durumları verilmiştir:

Durum  | Açıklamalar/senaryolar
:------- | :------------------
Çevrimiçi | Data Factory hizmetine bağlı düğüm.
Offline | Düğüm çevrimdışı.
Yükseltmenin | Düğüm otomatik olarak güncelleştiriliyor.
Sınırlı | Bağlantı sorunu nedeniyle. HTTP bağlantı noktası 8050 sorunu, Service Bus bağlantı sorunu veya kimlik bilgisi eşitleme sorunu olabilir.
Olmadan | Düğüm, diğer çoğunluk düğümlerin yapılandırmasından farklı bir yapılandırmadır.<br/><br/> Düğüm, diğer düğümlere bağlanamıyorsa devre dışı olabilir.

Aşağıdaki tabloda, **mantıksal bir ağ geçidinin**olası durumları verilmiştir. Ağ Geçidi, ağ geçidi düğümlerinin durumlarına bağlıdır.

Durum | Yorumlar
:----- | :-------
Kayıt gerekiyor | Henüz bu mantıksal ağ geçidine kayıtlı düğüm yok
Çevrimiçi | Ağ Geçidi düğümleri çevrimiçi
Offline | Çevrimiçi durumda düğüm yok.
Sınırlı | Bu ağ geçidinde düğümlerin hepsi sağlıklı durumda değil. Bu durum, bir düğümün kapatılmış olabileceğini belirten bir uyarıdır! <br/><br/>Dağıtıcı/çalışan düğümündeki kimlik bilgisi eşitleme sorunundan kaynaklanıyor olabilir.

## <a name="scale-up-gateway"></a>Ağ geçidini büyütme
Şirket içi ve bulut veri depoları arasında veri taşıma yeteneğini ölçeklendirmek için bir düğümde çalışabilecek **eşzamanlı veri taşıma işlerinin** sayısını yapılandırabilirsiniz.

Kullanılabilir bellek ve CPU iyi kullanılmadığında, ancak boşta kapasitesi 0 ise, bir düğümde çalışabilecek eşzamanlı iş sayısını artırarak ölçeği arttırmalısınız. Ayrıca, ağ geçidi aşırı yüklendiği için etkinlikler zaman aşımına uğradığından ölçeğini ölçeklendirmek isteyebilirsiniz. Bir ağ geçidi düğümünün gelişmiş ayarlarında bir düğüm için maksimum kapasiteyi artırabilirsiniz.

## <a name="troubleshooting-gateway-issues"></a>Ağ Geçidi sorunlarını giderme
Veri yönetimi ağ geçidini kullanmayla ilgili sorun giderme hakkında bilgi ve ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-troubleshoot-gateway-issues.md) makalesi.

## <a name="move-gateway-from-one-machine-to-another"></a>Ağ geçidini bir makineden diğerine taşıma
Bu bölüm, ağ geçidi istemcisini bir makineden başka bir makineye taşımaya yönelik adımları sağlar.

1. Portalda **Data Factory giriş sayfasına**gidin ve **bağlı hizmetler** kutucuğuna tıklayın.

    ![Veri ağ geçitleri bağlantısı](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. **Bağlı hizmetler** SAYFASıNıN **veri ağ geçitleri** bölümünde ağ geçidinizi seçin.

    ![Ağ Geçidi seçiliyken bağlı hizmetler sayfası](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. **Veri ağ geçidi** sayfasında, **indir ve veri ağ geçidini yükle**' ye tıklayın.

    ![Ağ Geçidi bağlantısını indir](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. **Yapılandır** sayfasında, **veri ağ geçidini indir ve yükle**' ye tıklayın ve veri ağ geçidini makineye yüklemek için yönergeleri izleyin.

    ![Sayfayı Yapılandır](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. **Microsoft veri yönetimi Gateway Configuration Manager** açık tutun.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Portaldaki **Yapılandır** sayfasında, komut çubuğunda **anahtarı yeniden oluştur** ' a tıklayın ve uyarı iletisi için **Evet** ' e tıklayın. Anahtarı panoya kopyalayan anahtar metninin yanındaki **Kopyala düğmesine** tıklayın. Anahtarı yeniden oluşturduğunuz anda eski makinedeki ağ geçidi çalışmayı durduruyor.

    ![Anahtarı yeniden oluştur](./media/data-factory-data-management-gateway/RecreateKey.png)
7. **Anahtarı** , makinenizde **Veri Yönetimi ağ geçidi Configuration Manager** **Kaydet** sayfasında bulunan metin kutusuna yapıştırın. seçim Anahtar metnini görmek için **ağ geçidi anahtarını göster** onay kutusunu tıklatın.

    ![Anahtarı kopyala ve Kaydet](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Ağ geçidini bulut hizmetine kaydetmek için **Kaydet** ' e tıklayın.
9. **Ayarlar** sekmesinde, eski ağ geçidiyle kullanılan sertifikayı seçmek için **Değiştir** ' e tıklayın, **parolayı**girin ve **son**' a tıklayın.

   ![Sertifika belirtin](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Aşağıdaki adımları uygulayarak eski ağ geçidinden bir sertifikayı dışarı aktarabilirsiniz: Veri Yönetimi ağ geçidini eski makinede Configuration Manager başlatın, **sertifika** sekmesine geçin, **dışarı aktar** düğmesine tıklayın ve yönergeleri izleyin.
10. Ağ geçidinin başarıyla kaydettirildikten sonra, **kayıt** kümesinin **kayıtlı** olduğunu ve **durum** ' Configuration Manager un giriş sayfasında **başlatıldı** olarak ayarlandığını görmeniz gerekir.

## <a name="encrypting-credentials"></a>Kimlik bilgileri şifreleniyor
Data Factory düzenleyicisinde kimlik bilgilerini şifrelemek için aşağıdaki adımları uygulayın:

1. **Ağ geçidi makinesinde**Web tarayıcısını başlatın, [Azure Portal](https://portal.azure.com)' a gidin. Gerekirse veri fabrikasını arayın **, Data Factory sayfasında Data** Factory ' yi açın ve sonra Data Factory Düzenleyiciyi başlatmak için **yaz & dağıt** ' a tıklayın.
2. JSON tanımını görmek veya bir veri yönetimi ağ geçidi gerektiren bağlı bir hizmet oluşturmak için ağaç görünümünde mevcut bir **bağlı hizmete** tıklayın (örneğin: SQL Server veya Oracle).
3. JSON düzenleyicisinde, **GatewayName** özelliği için ağ geçidinin adını girin.
4. **ConnectionString**Içindeki **veri kaynağı** özelliği için sunucu adı girin.
5. **ConnectionString**Içindeki **ilk katalog** özelliğinin veritabanı adını girin.
6. Komut çubuğunda tıklama-bir kez **kimlik bilgileri Yöneticisi** uygulamasını başlatan **şifreleme** düğmesine tıklayın. **Kimlik bilgilerini ayarla** iletişim kutusunu görmeniz gerekir.

    ![Kimlik bilgilerini ayarlama iletişim kutusu](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. **Kimlik bilgilerini ayarla** iletişim kutusunda, aşağıdaki adımları uygulayın:
   1. Data Factory hizmetinin veritabanına bağlanmak için kullanmasını istediğiniz **kimlik doğrulamasını** seçin.
   2. **Kullanıcı adı ayarı için** veritabanına erişimi olan kullanıcının adını girin.
   3. **Parola** ayarı için Kullanıcı parolasını girin.
   4. Kimlik bilgilerini şifrelemek ve iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.
8. Şu anda **ConnectionString** Içinde bir **encryptedcredential** özelliği görmeniz gerekir.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Portala ağ geçidi makinesinden farklı bir makineden eriştiğinizde, kimlik bilgileri Yöneticisi uygulamasının ağ geçidi makinesine bağlanabildiğinizden emin olmanız gerekir. Uygulama ağ geçidi makinesine ulaşamadıysanız, veri kaynağı için kimlik bilgilerini ayarlamanıza ve veri kaynağıyla bağlantıyı test etmeniz izin vermez.

**Kimlik bilgilerini ayarla** uygulamasını kullandığınızda Portal, ağ geçidi makinesindeki **Configuration Manager ağ geçidinin** **sertifika** sekmesinde belirtilen sertifikayla kimlik bilgilerini şifreler.

Kimlik bilgilerini şifrelemek için API tabanlı bir yaklaşım arıyorsanız, kimlik bilgilerini şifrelemek için [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell cmdlet 'ini kullanabilirsiniz. Cmdlet 'i, kimlik bilgilerini şifrelemek için kullanılacak ağ geçidinin yapılandırıldığı sertifikayı kullanır. JSON 'daki **ConnectionString** öğesinin **encryptedcredential** öğesine şifreli kimlik bilgileri eklersiniz. JSON 'ı [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) cmdlet 'i veya Data Factory düzenleyicisinde kullanırsınız.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Data Factory düzenleyicisini kullanarak kimlik bilgilerini ayarlamak için bir yaklaşım daha vardır. Düzenleyiciyi kullanarak SQL Server bağlı bir hizmet oluşturursanız ve kimlik bilgilerini düz metin olarak girerseniz, kimlik bilgileri Data Factory hizmetinin sahip olduğu bir sertifika kullanılarak şifrelenir. Ağ geçidinin kullanılmak üzere yapılandırıldığı sertifikayı kullanmaz. Bu yaklaşım bazı durumlarda biraz daha hızlı olabilir, ancak daha az güvenlidir. Bu nedenle, bu yaklaşımı yalnızca geliştirme/test amacıyla izlemeniz önerilir.

## <a name="powershell-cmdlets"></a>PowerShell cmdlet'leri
Bu bölümde, Azure PowerShell cmdlet 'lerini kullanarak bir ağ geçidinin nasıl oluşturulacağı ve kaydedileceği açıklanmaktadır.

1. Yönetici modunda **Azure PowerShell** başlatın.
2. Aşağıdaki komutu çalıştırarak ve Azure kimlik bilgilerinizi girerek Azure hesabınızda oturum açın.

    ```powershell
    Connect-AzAccount
    ```
3. Aşağıdaki gibi bir mantıksal ağ geçidi oluşturmak için **New-AzDataFactoryGateway** cmdlet 'ini kullanın:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Örnek komut ve çıkış**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. Azure PowerShell ' de klasöre geçin: *C:\\\\Program Files\\Microsoft Integration Runtime\\3,0\\PowerShellScript\\* . Aşağıdaki komutta gösterildiği gibi **$Key** yerel değişkenle Ilişkili *registergateway. ps1* komutunu çalıştırın. Bu betik, makinenizde yüklü olan istemci aracısını daha önce oluşturduğunuz mantıksal ağ geçidine kaydeder.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Iregisteronremotemachine parametresini kullanarak ağ geçidini uzak bir makineye kaydedebilirsiniz. Örnek:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Veri fabrikanızdaki ağ geçitlerinin listesini almak için **Get-AzDataFactoryGateway** cmdlet 'ini kullanabilirsiniz. **Durum** **çevrimiçi**olduğunda, ağ geçidinizin kullanıma hazırlanmasıdır.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   **Remove-azdatafactorygateway** cmdlet 'ini kullanarak bir ağ geçidini kaldırabilir ve **set-azdatafactorygateway** cmdlet 'lerini kullanarak bir ağ geçidinin güncelleştirme açıklamasını kullanabilirsiniz. Söz dizimi ve bu cmdlet 'ler hakkında diğer ayrıntılar için bkz. Data Factory cmdlet başvurusu.  

### <a name="list-gateways-using-powershell"></a>PowerShell kullanarak ağ geçitlerini listeleme

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>PowerShell kullanarak ağ geçidini kaldırma

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Sonraki adımlar
* Bkz. Şirket [içi ve bulut veri depoları arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi. İzlenecek yolda, verileri şirket içi SQL Server veritabanından Azure blobuna taşımak için ağ geçidini kullanan bir işlem hattı oluşturacaksınız.
