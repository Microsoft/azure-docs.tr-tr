---
title: "Azure ExpressRoute: devreleri için NPM 'yi yapılandırma"
description: Bulut tabanlı ağ (NPM) Azure ExpressRoute bağlantı hatları için izlemeyi yapılandırın. Bu, ExpressRoute özel eşlemesini ve Microsoft eşlemesi izleme kapsar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076601"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>ExpressRoute için Ağ Performansı İzleyicisi’ni Yapılandırma

Bu makalede bir ağ performansı İzleyicisi uzantısı, ExpressRoute izlemek için yapılandırmanıza yardımcı olur. Ağ Performansı İzleyicisi (NPM), Azure bulut dağıtımları ile şirket içi konumlar (Şube ofisleri vb.) arasındaki bağlantıyı izleyen, bulut tabanlı bir ağ izleme çözümüdür. NPM, Azure İzleyici günlüklerinin bir parçasıdır. NPM, ExpressRoute için özel eşleme veya Microsoft eşlemesi kullanmak üzere yapılandırılmış ExpressRoute bağlantı hatları üstünde ağ performansını izlemenizi sağlayan bir uzantı sunar. NPM’yi ExpressRoute için yapılandırdığınızda, ağ sorunlarını algılayabilir, tanımlayabilir ve giderebilirsiniz. Bu hizmet Azure Kamu Bulutu’nda da kullanılabilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Şunları yapabilirsiniz:

* Çeşitli sanal ağlarda kayıp ve gecikme süresi izleme ve uyarılar ayarlayın

* Ağdaki tüm yolları (yedekli yolları dahil) izleme

* Çoğaltması zor olan geçici ve zaman içinde nokta ağ sorunlarını giderme

* Yardımcı bir ağdaki performansın düşmesine neden olan segmenti belirleme

* (Her bir sanal ağda yüklü aracılar varsa) sanal ağ başına aktarım hızı alma

* Zaman içinde önceki bir noktaya ExpressRoute sistem durumundan bakın

## <a name="workflow"></a>İş akışı

İzleme aracılarının yüklü birden fazla sunucuda, hem şirket içinde ve azure'da. Aracıların birbirleriyle iletişim, ancak veri gönderme, bunlar TCP el sıkışması paketleri gönder. Azure ağ topolojisi ve trafik alabilir yolu eşlemek aracılar arasındaki iletişimi sağlar.

1. Bir NPM çalışma alanı oluşturun. Bir Log Analytics çalışma alanı ile aynı olmasıdır.
2. Yazılım aracılarını yükleyip yapılandırın. (Yalnızca Microsoft eşlemesini izlemek istiyorsanız, yazılım aracılarını yüklemeniz ve yapılandırmanız gerekmez.): 
    * Şirket içi sunucular ve Azure Vm'leri (özel eşdüzey hizmet sağlama için) izleme aracılarını yükleyin.
    * İzleme aracılarını iletişim kurmasına izin vermek için izleme Aracısı sunucularında ayarlarını yapılandırın. (Açık güvenlik duvarı bağlantı noktaları, vb.)
3. İzleme Aracısı ile şirket içi iletişim kurmak için Azure sanal makinelerinde yüklü izin vermek için ağ güvenlik grubu (NSG) kurallarını yapılandırın izleme aracılarını.
4. İzlemeyi ayarlama: otomatik olarak bulmak ve hangi ağları'nde NPM görülebilir yönetin.

Diğer nesneler veya hizmetlerini izlemek için Ağ Performansı İzleyicisi zaten kullanıyorsanız ve çalışma alanı desteklenen bölgelerden birinde zaten varsa, adım 1 ve 2. adımı atlayın ve yapılandırmanızı 3. adım ile başlar.

## <a name="configure"></a>1. adım: bir çalışma alanı oluşturma

Bir ExpressRoute devreden sanal ağ bağlantısı olan aboneliği bir çalışma alanı oluşturun.

1. İçinde [Azure portalında](https://portal.azure.com), sanal ağlar'ı içeren aboneliği seçin ExpressRoute devreniz eşlenmiş. Ardından, hizmet listesinde arama **Market** 'Ağ Performans İzleyicisi'. İade içinde açmak için tıklayın **Ağ Performansı İzleyicisi** sayfası.

   >[!NOTE]
   >Yeni bir çalışma alanı oluşturun veya mevcut bir çalışma alanını kullanın. Mevcut bir çalışma alanı kullanmak istiyorsanız, yeni sorgu diline çalışma geçirildiğinden emin emin olmanız gerekir. [Daha fazla bilgi...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Ana sayfanın alt kısmında **Ağ Performansı İzleyicisi** sayfasında **Oluştur** açmak için **Ağ Performansı İzleyicisi - yeni çözüm Oluştur** sayfası. Tıklayın **Log Analytics çalışma alanı - çalışma alanı seçin** çalışma sayfasını açın. Tıklayın **+ oluştur yeni çalışma alanı** çalışma sayfasını açın.
3. **Log Analytics çalışma alanı** sayfasında, **Yeni oluştur**' u seçin ve sonra aşağıdaki ayarları yapılandırın:

   * Log Analytics çalışma alanı - çalışma alanınız için bir ad yazın.
   * Abonelik - yeni çalışma alanı ile ilişkilendirmek istediğiniz seçin, birden fazla aboneliğiniz varsa.
   * Kaynak grubu - kaynak grubu oluşturun veya var olanı kullanın.
   * Konumu - bu konum, aracı bağlantı günlükleri için kullanılan depolama hesabı konumu belirtmek için kullanılır.
   * Fiyatlandırma katmanı - fiyatlandırma katmanını seçin.
  
     >[!NOTE]
     >ExpressRoute bağlantı hattı, dünyanın herhangi bir yerde olabilir. Çalışma alanı ile aynı bölgede olması gerekmez.
     >
  
     ![çalışma alanı](./media/how-to-npm/4.png)<br><br>
4. Tıklayın **Tamam** kaydedip ayarları şablonu dağıtmak için. Şablon doğrulama bitince **Oluştur** çalışma dağıtılacak.
5. Çalışma alanı dağıtıldıktan sonra gidin **NetworkMonitoring(name)** oluşturduğunuz kaynak. Ayarları doğrulayın ve ardından tıklayın **çözüm ek yapılandırma gerektirir**.

   ![ek yapılandırma](./media/how-to-npm/5.png)

## <a name="agents"></a>2. adım: Yükleme ve aracılarını yapılandırma

### <a name="download"></a>2.1: aracı kurulum dosyasını indirin

1. Git **ortak ayarları** sekmesinde **Ağ Performansı İzleyicisi Yapılandırması** kaynağınızın sayfası. Sunucunuzun işlemci karşılık gelen Aracısı **Log Analytics aracılarını yükleme** bölümünde ve kurulum dosyasını indirirsiniz.
2. Ardından, kopyalama **çalışma alanı kimliği** ve **birincil anahtar** not defteri için.
3. Gelen **yapılandırma Log Analytics aracılarını TCP protokolünü kullanılarak izleme için** bölümünde, Powershell betiği indirin. PowerShell betiğini TCP işlemler için ilgili güvenlik duvarı bağlantı noktası açmanıza yardımcı olur.

   ![PowerShell betiği](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: İzleme Aracısı her izleme sunucusuna (izlemek istediğiniz her VNET) yükleyin

ExpressRoute bağlantı yedeklilik (örneğin, şirket içi, Azure sanal ağları) için her iki tarafında en az iki aracı yüklemenizi öneririz. Aracı bir Windows sunucusuna yüklenmesi gerekir (2008 SP1 veya üzeri). Windows masaüstü işletim sistemi ve Linux işletim sistemi kullanarak ExpressRoute bağlantı hatları izleme desteklenmiyor. Aracıları yüklemek için aşağıdaki adımları kullanın:
   
  >[!NOTE]
  >Aracıları SCOM tarafından gönderilen (içerir [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) Azure'da barındırılıyorsa konumlarına tutarlı bir şekilde algılamak mümkün olmayabilir. Bu aracıları Azure sanal ağları ExpressRoute izlemek için kullanmamanızı öneririz.
  >

1. Çalıştırma **Kurulum** ExpressRoute izleme için kullanmak istediğiniz her sunucuda aracıyı yüklemek için. İzleme için kullandığınız sunucunun bir VM veya şirket içinde ya da olabilir ve Internet erişimi olmalıdır. Azure'da izlemek istediğiniz her bir ağ kesimindeki en az bir aracı şirket içi ve bir aracı yüklemeniz gerekir.
2. **Hoş Geldiniz** sayfasında **İleri**'ye tıklayın.
3. Üzerinde **lisans koşulları** sayfasında, lisansı okuyun ve ardından **ediyorum**.
4. Üzerinde **hedef klasör** sayfasında, değiştirin veya varsayılan yükleme klasörünü tutun ve ardından **sonraki**.
5. **Aracı kurulum seçenekleri** sayfasında, aracıyı Azure izleyici günlüklerine veya Operations Manager bağlamayı seçebilirsiniz. Veya aracıyı daha sonra yapılandırmak istiyorsanız seçimleri boş bırakabilirsiniz. Seçiminizi yaptıktan sonra tıklayın **sonraki**.

   * Bağlanmak seçerseniz, **Azure Log Analytics**, Yapıştır **çalışma alanı kimliği** ve **çalışma alanı anahtarı** (birincil anahtar, önceki bölümde Defteri'ne kopyaladığınız). Ardından **İleri**'ye tıklayın.

     ![Kimliği ve anahtarı](./media/how-to-npm/8.png)
   * Bağlanmak seçerseniz, **Operations Manager**, **yönetim grubu Yapılandırması** sayfasında **yönetim grubu adı**, **yönetim sunucusu** ve **yönetim sunucusu bağlantı noktası**. Ardından **İleri**'ye tıklayın.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Üzerinde **aracı eylem hesabı** sayfasında **yerel sistem** hesabı veya **etki alanı veya yerel bilgisayar hesabı**. Ardından **İleri**'ye tıklayın.

     ![Hesap](./media/how-to-npm/10.png)
6. Üzerinde **yüklemeye hazır** sayfasında, seçimlerinizi gözden geçirin ve ardından **yükleme**.
7. **Yapılandırma başarıyla tamamlandı** sayfasında **Son**'a tıklayın.
8. Tamamlandığında, Microsoft Monitoring Agent Denetim Masası'nda görünür. Yapılandırmanızı burada gözden geçirebilir ve aracının Azure Izleyici günlüklerine bağlı olduğunu doğrulayabilirsiniz. Her ne zaman bağlandığını belirten bir ileti Aracısı gösterir: **Microsoft Monitoring Agent Microsoft Operations Management Suite hizmetine başarıyla bağlandı**.

9. İzlenmesi gereken her sanal ağ için bu yordamı yineleyin.

### <a name="proxy"></a>2.3: (isteğe bağlı) proxy ayarlarını yapılandırma

İnternet'e erişmek için bir web proxy kullanıyorsanız, Microsoft İzleme Aracısı için Ara sunucu ayarlarını yapılandırmak için aşağıdaki adımları kullanın. Her sunucu için aşağıdaki adımları gerçekleştirin. Yapılandırmanız gereken birden çok sunucu olması durumunda, bu işlemi otomatikleştirmek için bir betik kullanmak sizin için daha kolay olabilir. Öyleyse bkz [bir betik kullanarak Microsoft İzleme Aracısı için Ara sunucu ayarlarını yapılandırmak için](../log-analytics/log-analytics-windows-agent.md).

Denetim Masası'nı kullanarak Microsoft İzleme Aracısı için Ara sunucu ayarlarını yapılandırmak için:

1. Açık **Denetim Masası**.
2. **Microsoft İzleme Aracısı**'nı açın.
3. **Ara Sunucu Ayarları** sekmesine tıklayın.
4. Seçin **proxy sunucusu kullan** URL'sini yazın ve bağlantı noktası numarası, gerektiğinde. Ara sunucunuz kimlik doğrulaması gerektiriyorsa ara sunucuya erişmek için kullanıcı adını ve parolayı yazın.

   ![Proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: aracı bağlantısını doğrulama

Kolayca aracılarınızı iletişim kurduğunu doğrulayabilirsiniz.

1. İzleme Aracısı ile bir sunucu üzerinde açın **Denetim Masası**.
2. Açık **Microsoft İzleme Aracısı**.
3. Tıklayın **Azure Log Analytics** sekmesi.
4. **Durum** sütununda, aracının Azure izleyici günlüklerine başarıyla bağlandığını görmeniz gerekir.

   ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: İzleme Aracısı sunucu güvenlik duvarı bağlantı noktalarını açmanız

TCP protokolünü kullanmak için izleme aracılarını iletişim kurabildiğinden emin olmak için güvenlik duvarı bağlantı noktalarını açmanız gerekir.

Ağ Performansı İzleyicisi tarafından gerekli kayıt defteri anahtarları oluşturmak için bir PowerShell betiğini çalıştırabilirsiniz. Bu betik, aynı zamanda izleme aracılarını TCP bağlantıları birbirleri ile oluşturmak için izin vermek için Windows Güvenlik duvarı kuralları oluşturur. Betiği tarafından oluşturulan kayıt defteri anahtarlarını oturum hata ayıklama günlükleri ve günlük dosyasının yolunu belirtin. Ayrıca, iletişim için kullanılan Aracısı TCP bağlantı noktasını tanımlar. Bu anahtarları için değerleri otomatik olarak komut dosyası tarafından ayarlanır. Bu anahtarları el ile değiştirmemelisiniz.

Bağlantı noktası 8084'ü varsayılan olarak açılır. Komut dosyasının ' portNumber' parametresi sağlayarak, özel bir bağlantı noktası kullanabilirsiniz. Ancak bunu yaparsanız, komut dosyasını çalıştırmak tüm sunucuların aynı bağlantı noktasını belirtmeniz gerekir.

>[!NOTE]
>'EnableRules' PowerShell Betiği, betiğin çalıştırıldığı sunucuda Windows Güvenlik duvarı kuralları yapılandırır. Bir ağ güvenlik duvarınız varsa, Ağ Performansı İzleyicisi tarafından kullanılan TCP bağlantı noktasının giden trafiğe izin verdiğinden emin olun.
>
>

Aracı sunucularda yönetici ayrıcalıklarıyla bir PowerShell penceresi açın. Çalıştırma [EnableRules](https://aka.ms/npmpowershellscript) (Bu, daha önce indirdiğiniz) PowerShell Betiği. Herhangi bir parametre kullanmayın.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>3. adım: ağ güvenlik grubu kurallarını yapılandırma

Azure'da Aracısı sunucuları izlemek için ağ güvenlik grubu (NSG) kuralları yapay işlemler için NPM tarafından kullanılan bağlantı noktası TCP trafiğine izin verecek şekilde yapılandırmanız gerekir. Varsayılan bağlantı noktası 8084 ' dir. Böylece, bir şirket içi ile iletişim kurmak için bir Azure sanal makinesinde yüklü İzleme Aracısı İzleme Aracısı.

NSG hakkında daha fazla bilgi için bkz: [ağ güvenlik grupları](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>(Hem şirket içi sunucu Aracısı hem de Azure server Aracısı) aracılarını yüklediğinizden ve bu adım ile devam etmeden önce PowerShell betiğini çalıştırdığınız emin olun.
>

## <a name="setupmonitor"></a>4. adım: eşleme bağlantıları bulma

1. Ağ Performansı İzleyicisi'ne genel bakış kutucuğu gidebilirsiniz **tüm kaynakları** sayfasında ve beyaz listeye NPM çalışma'ı tıklatın.

   ![npm çalışma](./media/how-to-npm/npm.png)
2. Tıklayın **Ağ Performansı İzleyicisi** Pano görüntülemek için genel bakış kutucuğu. ExpressRoute 'yapılandırılmamış bir durumda' olduğunu gösterir ve ExpressRoute sayfasında, Pano içerir. Tıklayın **özellik kurulumu** Ağ Performansı İzleyicisi'ni yapılandırma sayfasını açın.

   ![özellik kurulumu](./media/how-to-npm/npm2.png)
3. Yapılandırma sayfasında, sol tarafta panelde bulunan 'ExpressRoute eşlemeleri' sekmesine gidin. Ardından, **Şimdi Bul**.

   ![bul](./media/how-to-npm/13.png)
4. Bulma tamamlandıktan sonra aşağıdaki öğeleri içeren bir liste görürsünüz:
   * Tüm bu abonelikle ilişkili bir ExpressRoute devreden Microsoft eşleme bağlantıları.
   * Tüm sanal ağlara bağlanan özel eşleme bağlantıları, bu abonelikle ilişkili.
            
## <a name="configmonitor"></a>5. adım: İzleyici yapılandırma

Bu bölümde, izleyicilerin yapılandırın. Eşleme türü için izlemek istediğiniz adımları izleyin: **özel eşdüzey hizmet sağlama**, veya **Microsoft eşlemesi**.

### <a name="private-peering"></a>Özel eşleme

Özel eşleme için bulma işlemi tamamlandığında, gördüğünüz kurallarını olacak benzersiz **devre adına** ve **VNet adı**. Başlangıçta, bu kuralları devre dışı bırakıldı.

![rules](./media/how-to-npm/14.png)

1. Denetleme **bu eşlemeyi İzle** onay kutusu.
2. Onay kutusunu işaretleyin **etkinleştirme Bu eşleme için sistem durumu izleme**.
3. İzleme koşulları seçin. Eşik değerleri yazarak sistem durumu olayları oluşturmak üzere özel eşikler ayarlayabilirsiniz. Seçili ağ/alt ağ çifti için seçilen eşiğin üzerinde koşul değeri gider her sistem durumu olayı oluşturulur.
4. ON-PREM aracıları tıklayın **aracı Ekle** düğmesini özel eşleme bağlantısını izlemek istediğiniz şirket içi sunucular ekleyin. Yalnızca bağlantı bölümünde 2. adımda belirttiğiniz Microsoft hizmet uç noktası olan aracılarının seçtiğinizden emin olun. Şirket içi aracıları ExpressRoute bağlantısı kullanarak uç noktaya ulaşabilmelidir olması gerekir.
5. Ayarları kaydedin.
6. Kuralları etkinleştirme ve değerleri ve izlemek istediğiniz aracılar seçmenin sonra yaklaşık 30-60 dakika doldurma başlamak değerler için bir bekleme yoktur ve **ExpressRoute izleme** kullanılabilir hale gelmesi için kutucuklar.

### <a name="microsoft-peering"></a>Microsoft eşlemesi

Microsoft eşlemesi için izlemek istediğiniz Microsoft eşleme bağlantıları tıklayın ve ayarlarını yapılandırın.

1. Denetleme **bu eşlemeyi İzle** onay kutusu. 
2. (İsteğe bağlı) Hedef Microsoft hizmet uç noktası değiştirebilirsiniz. Varsayılan olarak, NPM, hedef olarak bir Microsoft Hizmeti uç noktası seçer. NPM, bu hedef uç noktasına ExpressRoute aracılığıyla şirket içi sunucularınıza bağlantısını izler. 
    * Bu hedef uç nokta değiştirmek için tıklayın **(Düzenle)** altında bağlantı **hedef:** , başka bir Microsoft hizmeti hedef uç nokta URL'leri listesinden seçin.
      ![Hedef Düzenle](./media/how-to-npm/edit_target.png)<br>

    * Özel URL veya IP adresi kullanabilirsiniz. Bu seçenek, Microsoft Azure PaaS hizmetlerine, Azure depolama, SQL veritabanları ve genel IP adreslerinde sunulan Web siteleri gibi bir bağlantı kurmak için eşleme kullanıyorsanız özellikle geçerlidir. Bunu yapmak için bağlantıya tıklayın **(Bunun yerine özel URL veya IP adresi kullanın)** URL listesi sonunda, ExpressRoute Microsoft eşlemesi üzerinden bağlı, Azure PaaS hizmeti genel uç noktasını girin.
    ![Özel URL](./media/how-to-npm/custom_url.png)<br>

    * Bu isteğe bağlı ayarlar kullanıyorsanız, yalnızca Microsoft hizmet uç noktası burada seçili olduğundan emin olun. Uç nokta, şirket içi aracıları tarafından expressroute'a bağlı ve erişilebilir olmalıdır.
3. Onay kutusunu işaretleyin **etkinleştirme Bu eşleme için sistem durumu izleme**.
4. İzleme koşulları seçin. Eşik değerleri yazarak sistem durumu olayları oluşturmak üzere özel eşikler ayarlayabilirsiniz. Seçili ağ/alt ağ çifti için seçilen eşiğin üzerinde koşul değeri gider her sistem durumu olayı oluşturulur.
5. ON-PREM aracıları tıklayın **aracı Ekle** düğmesini Microsoft eşleme bağlantısını izlemek istediğiniz şirket içi sunucular ekleyin. Yalnızca bağlantı bölümünde 2. adımda belirttiğiniz Microsoft hizmet uç noktalarına sahip aracılara seçtiğinizden emin olun. Şirket içi aracıları ExpressRoute bağlantısı kullanarak uç noktaya ulaşabilmelidir olması gerekir.
6. Ayarları kaydedin.
7. Kuralları etkinleştirme ve değerleri ve izlemek istediğiniz aracılar seçmenin sonra yaklaşık 30-60 dakika doldurma başlamak değerler için bir bekleme yoktur ve **ExpressRoute izleme** kullanılabilir hale gelmesi için kutucuklar.

## <a name="explore"></a>6. adım: kutucuk izleme görünümü

ExpressRoute bağlantı hatları ve bağlantı kaynaklarını izleme kutucukları gördükten sonra NPM tarafından izlenmekte olan. Microsoft Peering bağlantılarının durumunu detaya gitmek için Microsoft Peering kutucuğuna tıklayabilirsiniz.

![kutucukları izleme](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Ağ Performansı İzleyicisi sayfası

NPM sayfa ExpressRoute için ExpressRoute bağlantı hatları ve eşleme durumunu genel bakışını gösteren bir sayfa içerir.

![Pano](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Bağlantı hatları listesi

ExpressRoute bağlantı hatları izlenen tüm listesini görüntülemek için tıklayın **ExpressRoute bağlantı hatları** kutucuk. Bağlantı hattı seçin ve görüntüleme, sistem durumu, paket kaybı, bant genişliği kullanımı ve gecikme süresi eğilim grafikleri. Grafik etkileşimlidir. Bir özel zaman penceresi, grafik çizim için seçebilirsiniz. Fare yakınlaştırmak ve ayrıntılı veri noktaları görmek için grafikteki bir alanı üzerine sürükleyebilirsiniz.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Eğilim kaybı, gecikme süresi ve aktarım hızı

Bant genişliği ve gecikme süresi kaybı grafik etkileşimlidir. Fare denetimlerini kullanarak bu grafiklerin herhangi bir bölüme yakınlaştırma yapabilirsiniz. Tıklayarak diğer aralıkları için bant genişliği, gecikme süresi ve kayıp veri görebilirsiniz **tarih/saat**sol üst eylem çubuğunda aşağıda bulunan.

![Eğilim](./media/how-to-npm/16.png)

### <a name="peerings"></a>Eşlemeleri listesi

Tüm özel eşdüzey hizmet sağlama üzerinden sanal ağlara bağlantılar görünümü listesine tıklayın **özel eşlemeler** Panoda kutucuk. Burada, bir sanal seçebilirsiniz, sistem durumu, paket kaybı, bant genişliği kullanımı ve gecikme süresi eğilim grafikleri görüntülemek ve ağ bağlantısı.

![bağlantı hattı listesi](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>düğümleri görüntüleyin

VM'ler/Microsoft Azure hizmet uç noktaları seçilen ExpressRoute eşleme bağlantısını için şirket içi düğümleri arasındaki tüm bağlantıların görünümünü listesine tıklayın **düğüm bağlantılarını görüntüle**. Kayıp ve gecikme süresi ilişkili eğilimini yanı sıra her bağlantı sistem durumunu görüntüleyebilirsiniz.

![düğümleri görüntüleyin](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Bağlantı hattı topolojisi

Bağlantı hattı topolojiyi görüntülemek için tıklayın **topolojisi** Döşe. Bu seçili topoloji görünümü için gereken bağlantı hattı veya eşleme. Topoloji diyagramı ağdaki her bir kesim gecikme sürelerini sağlar. Her katman 3 atlama diyagramın bir düğümle temsil edilir. Tıklayarak bir atlama atlama hakkında daha fazla ayrıntı ortaya çıkarır.

Şirket içi atlama aşağıdaki kaydırıcı çubuğunu hareket ettirerek içerecek şekilde görünürlük düzeyini artırabilir **filtreleri**. Sola veya sağa kaydırıcı çubuğunu taşımak, artar/topolojisi graftaki durak sayısını azaltır. Her bir kesim arasında gecikme süresi yüksek gecikme parçaların ağınızdaki daha hızlı yalıtım sağlayan görünür durumda.

![filtreler](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Bir bağlantı hattının ayrıntılı topoloji görünümü

Bu görünüm, sanal ağ bağlantılarını gösterir.
![ayrıntılı topolojisi](./media/how-to-npm/17.png)
