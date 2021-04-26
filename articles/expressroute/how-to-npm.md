---
title: "Azure ExpressRoute: devreleri için NPM 'yi yapılandırma"
description: Azure ExpressRoute devreleri için bulut tabanlı ağ izlemeyi (NPM) yapılandırın. Bu, ExpressRoute özel eşlemesi ve Microsoft eşlemesi üzerinde izlemeyi ele alır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 907c03bd15463368def316e72f55ce214cb3e617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571045"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>ExpressRoute için Ağ Performansı İzleyicisi’ni Yapılandırma

Bu makale, ExpressRoute 'ı izlemek için bir Ağ Performansı İzleyicisi uzantısı yapılandırmanıza yardımcı olur. Ağ Performansı İzleyicisi (NPM), Azure bulut dağıtımları ile şirket içi konumlar (Şube ofisleri vb.) arasındaki bağlantıyı izleyen, bulut tabanlı bir ağ izleme çözümüdür. NPM, Azure İzleyici günlüklerinin bir parçasıdır. NPM, ExpressRoute için özel eşleme veya Microsoft eşlemesi kullanmak üzere yapılandırılmış ExpressRoute bağlantı hatları üstünde ağ performansını izlemenizi sağlayan bir uzantı sunar. NPM’yi ExpressRoute için yapılandırdığınızda, ağ sorunlarını algılayabilir, tanımlayabilir ve giderebilirsiniz. Bu hizmet Azure Kamu Bulutu’nda da kullanılabilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Seçenekleriniz şunlardır:

* Çeşitli VNET 'lerde kayıp ve gecikme süresini izleyin ve Uyarılar ayarlayın

* Ağdaki tüm yolları (gereksiz yollar dahil) izleme

* Çoğaltma zor olan geçici ve zaman içinde ağ sorunlarını giderme

* Ağ üzerinde, performansın düşmesine neden olan belirli bir segmenti belirlemede yardımcı olun

* Sanal ağ başına aktarım hızını al (her VNet 'te aracılar yüklüyse)

* Önceki bir zaman noktasından ExpressRoute sistem durumuna bakın

## <a name="workflow"></a><a name="workflow"></a>İş akışı

İzleme aracıları, hem şirket içinde hem de Azure 'da birden çok sunucuya yüklenir. Aracılar birbirleriyle iletişim kurar ancak veri göndermez, TCP el sıkışma paketleri gönderir. Aracılar arasındaki iletişim, Azure 'un trafiğin gerçekleşmesi için ağ topolojisini ve yolunu eşlemesine olanak tanır.

1. NPM çalışma alanı oluşturun. Bu, Log Analytics çalışma alanıyla aynıdır.
2. Yazılım aracılarını yükleyip yapılandırın. (Yalnızca Microsoft eşlemesini izlemek istiyorsanız, yazılım aracılarını yüklemeniz ve yapılandırmanız gerekmez.): 
    * İzleme aracılarını şirket içi sunuculara ve Azure VM 'lerine (özel eşleme için) yükler.
    * İzleme aracılarının iletişim kurmasına izin vermek için izleme Aracısı sunucularındaki ayarları yapılandırın. (Güvenlik duvarı bağlantı noktalarını aç vb.)
3. Azure VM 'lerinde yüklü izleme aracısının şirket içi izleme aracılarıyla iletişim kurmasına izin vermek için ağ güvenlik grubu (NSG) kurallarını yapılandırın.
4. İzlemeyi ayarlama: NPM 'de hangi ağların görülebileceğini otomatik keşfet ve yönetin.

Zaten diğer nesneleri veya hizmetleri izlemek için Ağ Performansı İzleyicisi kullanıyorsanız ve desteklenen bölgelerden birinde çalışma alanınız zaten varsa, 1. adımı ve 2. adımı atlayabilir ve 3. adım ile yapılandırmanıza başlayabilirsiniz.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>1. Adım: çalışma alanı oluşturma

Abonelikte ExpressRoute bağlantı hattına olan sanal ağlar içeren bir çalışma alanı oluşturun.

1. [Azure Portal](https://portal.azure.com)ExpressRoute bağlantı hattınızı hedefleyen VNET 'Leri içeren aboneliği seçin. Ardından, ' Ağ Performansı İzleyicisi ' için **Market** 'teki hizmet listesinde arama yapın. Dönüş içinde **ağ performansı İzleyicisi** sayfasını açmak için tıklayın.

   >[!NOTE]
   >Yeni bir çalışma alanı oluşturabilir veya var olan bir çalışma alanını kullanabilirsiniz. Mevcut bir çalışma alanını kullanmak istiyorsanız, çalışma alanının yeni sorgu diline geçirilmiş olduğundan emin olmanız gerekir. [Daha fazla bilgi...](../azure-monitor/logs/log-query-overview.md)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Ana **ağ performansı İzleyicisi** sayfasının en altında **Oluştur** ' a tıklayarak **ağ performansı İzleyicisi yeni çözüm oluştur** sayfasını açın. Log Analytics çalışma alanı ' na tıklayın. çalışma alanları sayfasını açmak için **bir çalışma alanı seçin** . **+ Yeni çalışma alanı oluştur** ' a tıklayarak çalışma alanı sayfasını açın.
3. **Log Analytics çalışma alanı** sayfasında, **Yeni oluştur**' u seçin ve sonra aşağıdaki ayarları yapılandırın:

   * Log Analytics çalışma alanı-çalışma alanınız için bir ad yazın.
   * Abonelik-birden çok aboneliğiniz varsa, yeni çalışma alanıyla ilişkilendirmek istediğiniz birini seçin.
   * Kaynak grubu-bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu kullanın.
   * Konum-bu konum, aracı bağlantı günlükleri için kullanılan depolama hesabının konumunu belirtmek için kullanılır.
   * Fiyatlandırma Katmanı-fiyatlandırma katmanını seçin.
  
     >[!NOTE]
     >ExpressRoute bağlantı hattı dünyanın herhangi bir yerinden olabilir. Çalışma alanıyla aynı bölgede olması gerekmez.
     >
  
     ![çalışma alanı](./media/how-to-npm/4.png)<br><br>
4. Ayarlar şablonunu kaydedip dağıtmak için **Tamam** ' ı tıklatın. Şablon doğrulandıktan sonra, çalışma alanını dağıtmak için **Oluştur** ' a tıklayın.
5. Çalışma alanı dağıtıldıktan sonra, oluşturduğunuz **Networkmonitoring (ad)** kaynağına gidin. Ayarları doğrulayın, sonra **çözüm için ek yapılandırma gerekir**.

   ![ek yapılandırma](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>2. Adım: aracıları yükleyip yapılandırma

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2,1: aracı kurulum dosyasını indirin

1. Kaynağınızın **ağ performansı İzleyicisi yapılandırma** sayfasının **ortak ayarlar** sekmesine gidin. **Log Analytics aracıları Yükle** bölümünde sunucunuzun işlemcisine karşılık gelen aracıya tıklayın ve kurulum dosyasını indirin.
2. Sonra, **çalışma alanı kimliği** ve **birincil anahtarı** Not defteri 'ne kopyalayın.
3. **TCP protokolünü kullanarak izleme için Log Analytics aracılarını Yapılandır** bölümünde, PowerShell betiğini indirin. PowerShell betiği, TCP işlemleri için ilgili güvenlik duvarı bağlantı noktasını açmanıza yardımcı olur.

   ![PowerShell betiği](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2,2: izleme aracısını her izleme sunucusuna (izlemek istediğiniz her VNET 'te) yükler

Yedeklilik için ExpressRoute bağlantısının her bir tarafına en az iki aracı yüklemenizi öneririz (örneğin, şirket içi, Azure sanal ağları). Aracının bir Windows Server 'a (2008 SP1 veya üzeri) yüklenmesi gerekir. Windows masaüstü işletim sistemi ve Linux IŞLETIM sistemini kullanarak ExpressRoute devresine izleme desteklenmez. Aracıları yüklemek için aşağıdaki adımları kullanın:
   
  >[!NOTE]
  >SCOM tarafından gönderilen aracılar ( [MMA](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))'yı içerir), Azure 'da barındırılıyorsa konumlarını sürekli olarak algılayamayabilir. ExpressRoute izlemek için bu aracıları Azure sanal ağları 'nda kullanmanızı öneririz.
  >

1. ExpressRoute 'U izlemek için kullanmak istediğiniz her sunucuya aracıyı yüklemek için **kurulumu** çalıştırın. İzleme için kullandığınız sunucu, bir VM ya da şirket içi olabilir ve Internet erişimi olmalıdır. Şirket içinde en az bir aracı ve Azure 'da izlemek istediğiniz her ağ kesimine bir aracı yüklemeniz gerekir.
2. **Hoş Geldiniz** sayfasında **İleri**'ye tıklayın.
3. **Lisans koşulları** sayfasında, Lisansı okuyun ve ardından **kabul** ediyorum ' a tıklayın.
4. **Hedef klasör** sayfasında, varsayılan yükleme klasörünü değiştirin veya koruyun ve ardından **İleri**' ye tıklayın.
5. **Aracı kurulum seçenekleri** sayfasında, aracıyı Azure izleyici günlüklerine veya Operations Manager bağlamayı seçebilirsiniz. Ya da, aracıyı daha sonra yapılandırmak istiyorsanız seçimleri boş bırakabilirsiniz. Seçiminizi yaptıktan sonra **İleri**' ye tıklayın.

   * **Azure Log Analytics**'e bağlanmayı seçerseniz, önceki bölümde not defteri ' ne kopyaladığınız **çalışma alanı kimliğini** ve **çalışma alanı anahtarını** (birincil anahtar) yapıştırın. Ardından **İleri**' ye tıklayın.

     ![KIMLIK ve anahtar](./media/how-to-npm/8.png)
   * **Operations Manager**' ye bağlanmayı seçerseniz, **Yönetim grubu yapılandırması** sayfasında **Yönetim grubu adı**, **Yönetim sunucusu** ve **Yönetim sunucusu bağlantı noktasını** yazın. Ardından **İleri**' ye tıklayın.

     ![Operations Manager](./media/how-to-npm/9.png)
   * **Aracı eylem hesabı** sayfasında, **yerel sistem** hesabı veya **etki alanı ya da yerel bilgisayar hesabı**' nı seçin. Ardından **İleri**' ye tıklayın.

     ![Hesap](./media/how-to-npm/10.png)
6. **Yüklemeye hazırlanma** sayfasında, seçimlerinizi gözden geçirin ve ardından **yükler**' i tıklatın.
7. **Yapılandırma başarıyla tamamlandı** sayfasında **Son**'a tıklayın.
8. Tamamlandığında, Microsoft Monitoring Agent Denetim Masası 'nda görüntülenir. Yapılandırmanızı burada gözden geçirebilir ve aracının Azure Izleyici günlüklerine bağlı olduğunu doğrulayabilirsiniz. Bağlanıldığında, aracı şöyle bir ileti görüntüler: **Microsoft Monitoring Agent Microsoft Operations Management Suite hizmetine başarıyla bağlandı**.

9. İzlenmesi gereken her VNET için bu yordamı tekrarlayın.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2,3: proxy ayarlarını yapılandırma (isteğe bağlı)

Internet 'e erişmek için bir Web Proxy kullanıyorsanız, Microsoft Monitoring Agent proxy ayarlarını yapılandırmak için aşağıdaki adımları kullanın. Her sunucu için bu adımları gerçekleştirin. Yapılandırmanız gereken birden çok sunucu olması durumunda, bu işlemi otomatikleştirmek için bir betik kullanmak sizin için daha kolay olabilir. Bu durumda, [bir betik kullanarak Microsoft Monitoring Agent için proxy ayarlarını yapılandırmak için](../azure-monitor/agents/agent-windows.md)bölümüne bakın.

Denetim Masası 'nı kullanarak Microsoft Monitoring Agent proxy ayarlarını yapılandırmak için:

1. **Denetim masasını** açın.
2. **Microsoft İzleme Aracısı**'nı açın.
3. **Ara Sunucu Ayarları** sekmesine tıklayın.
4. **Proxy sunucusu kullan** ' ı seçin ve gerekirse URL 'yi ve bağlantı noktası numarasını yazın. Ara sunucunuz kimlik doğrulaması gerektiriyorsa ara sunucuya erişmek için kullanıcı adını ve parolayı yazın.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2,4: aracı bağlantısını doğrulama

Aracılarınızın iletişim olup olmadığını kolayca doğrulayabilirsiniz.

1. İzleme Aracısı olan bir sunucuda, **denetim masasını** açın.
2. **Microsoft Monitoring Agent** açın.
3. **Azure Log Analytics** sekmesine tıklayın.
4. **Durum** sütununda, aracının Azure izleyici günlüklerine başarıyla bağlandığını görmeniz gerekir.

   ![durum](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2,5: izleme Aracısı sunucularında güvenlik duvarı bağlantı noktalarını açın

TCP protokolünü kullanmak için, izleme aracılarının iletişim kurabildiğinden emin olmak için güvenlik duvarı bağlantı noktalarını açmanız gerekir.

Ağ Performansı İzleyicisi için gereken kayıt defteri anahtarlarını oluşturmak için bir PowerShell betiği çalıştırabilirsiniz. Bu betik, izleme aracılarının birbirleriyle TCP bağlantıları oluşturmalarına izin veren Windows güvenlik duvarı kurallarını da oluşturur. Betiği tarafından oluşturulan kayıt defteri anahtarları, hata ayıklama günlüklerinin kaydedilip edilmeyeceğini ve günlük dosyasının yolunu belirtir. Ayrıca, iletişim için kullanılan aracı TCP bağlantı noktasını tanımlar. Bu anahtarların değerleri otomatik olarak komut dosyası tarafından ayarlanır. Bu anahtarları el ile değiştirmemelisiniz.

8084 numaralı bağlantı noktası varsayılan olarak açılır. Betiğe ' portNumber ' parametresini sağlayarak özel bir bağlantı noktası kullanabilirsiniz. Ancak bunu yaparsanız, komut dosyasını çalıştırdığınız tüm sunucular için aynı bağlantı noktasını belirtmeniz gerekir.

>[!NOTE]
>' EnableRules ' PowerShell betiği, yalnızca betiğin çalıştırıldığı sunucuda Windows güvenlik duvarı kurallarını yapılandırır. Bir ağ güvenlik duvarınız varsa, Ağ Performansı İzleyicisi tarafından kullanılan TCP bağlantı noktası için giden trafiğe izin verdiğinden emin olmanız gerekir.
>
>

Aracı sunucularında, yönetici ayrıcalıklarına sahip bir PowerShell penceresi açın. [Enablerules](https://aka.ms/npmpowershellscript) PowerShell betiğini çalıştırın (daha önce indirdiğiniz). Herhangi bir parametre kullanmayın.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>3. Adım: ağ güvenlik grubu kurallarını yapılandırma

Azure 'daki aracı sunucularını izlemek için, yapay işlemler için NPM tarafından kullanılan bağlantı noktasında TCP trafiğine izin vermek üzere ağ güvenlik grubu (NSG) kurallarını yapılandırmanız gerekir. Varsayılan bağlantı noktası 8084 ' dir. Bu, bir Azure VM üzerinde yüklü bir izleme aracısının şirket içi izleme aracısıyla iletişim kurmasını sağlar.

NSG hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](../virtual-network/tutorial-filter-network-traffic.md).

>[!NOTE]
>Aracıları (Şirket içi Sunucu Aracısı ve Azure Sunucu Aracısı) yüklediğinizden ve bu adımla devam etmeden önce PowerShell betiğini çalıştırdığınızdan emin olun.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>4. Adım: eşleme bağlantılarını bulma

1. **Tüm kaynaklar** sayfasına giderek ağ performansı İzleyicisi genel bakış kutucuğuna gidin ve ardından ALLOWLISTELENMIŞ NPM çalışma alanına tıklayın.

   ![NPM çalışma alanı](./media/how-to-npm/npm.png)
2. Panoyu açmak için **ağ performansı İzleyicisi** genel bakış kutucuğuna tıklayın. Pano, ExpressRoute 'un ' yapılandırılmamış durum ' içinde olduğunu gösteren bir ExpressRoute sayfası içerir. Ağ Performansı İzleyicisi yapılandırma sayfasını açmak için **özellik kurulumu** ' na tıklayın.

   ![Özellik kurulumu](./media/how-to-npm/npm2.png)
3. Yapılandırma sayfasında, sol taraftaki bölmede bulunan ' ExpressRoute Peerler ' sekmesine gidin. Sonra **Şimdi keşfet**' e tıklayın.

   ![bulmak](./media/how-to-npm/13.png)
4. Bulma tamamlandığında, aşağıdaki öğeleri içeren bir liste görürsünüz:
   * Tüm Microsoft eşleme bağlantıları, bu abonelikle ilişkili olan ExpressRoute bağlantı hattı (ler) i.
   * Bu abonelikle ilişkili sanal ağlara bağlanan tüm özel eşleme bağlantıları.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>5. Adım: izleyicileri yapılandırma

Bu bölümde, izleyicileri yapılandırırsınız. İzlemek istediğiniz eşleme türü için şu adımları izleyin: **özel eşleme** veya **Microsoft eşleme**.

### <a name="private-peering"></a>Özel eşleme

Özel eşleme için, bulma tamamlandığında, benzersiz **devre adı** ve **VNET adı** için kurallar görürsünüz. Başlangıçta, bu kurallar devre dışı bırakılmıştır.

![rules](./media/how-to-npm/14.png)

1. **Bu eşlemeyi izle** onay kutusunu işaretleyin.
2. **Bu eşleme Için sistem durumu Izlemeyi etkinleştir** onay kutusunu seçin.
3. İzleme koşullarını seçin. Eşik değerlerini yazarak sistem durumu olayları oluşturmak için özel eşikler ayarlayabilirsiniz. Koşulun değeri seçili ağ/alt ağ çifti için seçili eşiğin üstünde olduğunda bir sistem durumu olayı oluşturulur.
4. Özel eşleme bağlantısını izlemek istediğiniz şirket içi sunucuları eklemek için ŞIRKET içi ARACıLAR **aracıları Ekle** düğmesine tıklayın. Yalnızca adım 2 ' nin bölümünde belirttiğiniz Microsoft hizmet uç noktasına bağlantısı olan aracıları seçtiğinizden emin olun. Şirket içi aracıların ExpressRoute bağlantısını kullanarak uç noktaya erişebilmesi gerekir.
5. Ayarları kaydedin.
6. Kuralları etkinleştirdikten ve izlemek istediğiniz değerleri ve aracıları seçtikten sonra, değerlerin doldurulmasının başlaması ve **ExpressRoute izleme** kutucuklarının kullanılabilir hale gelmesi için yaklaşık 30-60 dakika bekleyin.

### <a name="microsoft-peering"></a>Microsoft eşlemesi

Microsoft eşlemesi için, izlemek istediğiniz Microsoft eşleme bağlantısına tıklayın ve ayarları yapılandırın.

1. **Bu eşlemeyi izle** onay kutusunu işaretleyin. 
2. Seçim Hedef Microsoft hizmet uç noktasını değiştirebilirsiniz. Varsayılan olarak NPM hedef olarak bir Microsoft hizmet uç noktası seçer. NPM, ExpressRoute aracılığıyla şirket içi sunucularınızdan bu hedef uç noktaya bağlantıyı izler. 
    * Bu hedef uç noktasını değiştirmek için **hedef:**' ın altındaki **(Düzenle)** bağlantısına tıklayın ve URL listesinden başka bir Microsoft hizmet hedefi uç noktası seçin.
      ![hedefi Düzenle](./media/how-to-npm/edit_target.png)<br>

    * Özel bir URL veya IP adresi kullanabilirsiniz. Bu seçenek, Azure depolama, SQL veritabanları ve genel IP adreslerinde sunulan Web siteleri gibi Azure PaaS hizmetleriyle bağlantı kurmak için Microsoft eşlemesi 'ni kullanıyorsanız özellikle ilgilidir. Bunu yapmak için, URL listesinin alt kısmındaki bağlantıya tıklayın **(Bunun yerine özel URL veya IP adresi kullanın)** ve ardından ExpressRoute Microsoft eşlemesi aracılığıyla bağlı olan Azure PaaS hizmeti 'nin Genel uç noktasını girin.
    ![Özel URL](./media/how-to-npm/custom_url.png)<br>

    * Bu isteğe bağlı ayarları kullanıyorsanız, burada yalnızca Microsoft hizmet uç noktasının seçildiğinden emin olun. Uç noktanın ExpressRoute 'a bağlanması ve şirket içi aracılar tarafından erişilebilir olması gerekir.
3. **Bu eşleme Için sistem durumu Izlemeyi etkinleştir** onay kutusunu seçin.
4. İzleme koşullarını seçin. Eşik değerlerini yazarak sistem durumu olayları oluşturmak için özel eşikler ayarlayabilirsiniz. Koşulun değeri seçili ağ/alt ağ çifti için seçili eşiğin üstünde olduğunda bir sistem durumu olayı oluşturulur.
5. Microsoft eşleme bağlantısını izlemek istediğiniz şirket içi sunucuları eklemek için ŞIRKET içi ARACıLAR **aracıları Ekle** düğmesine tıklayın. Yalnızca adım 2 için bölümünde belirttiğiniz Microsoft hizmet uç noktalarına bağlantısı olan aracıları seçtiğinizden emin olun. Şirket içi aracıların ExpressRoute bağlantısını kullanarak uç noktaya erişebilmesi gerekir.
6. Ayarları kaydedin.
7. Kuralları etkinleştirdikten ve izlemek istediğiniz değerleri ve aracıları seçtikten sonra, değerlerin doldurulmasının başlaması ve **ExpressRoute izleme** kutucuklarının kullanılabilir hale gelmesi için yaklaşık 30-60 dakika bekleyin.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>6. Adım: izleme kutucuklarını görüntüleme

İzleme kutucuklarını gördüğünüzde ExpressRoute devrelerinizi ve bağlantı kaynaklarınızı NPM tarafından izleniyor. Microsoft eşleme bağlantıları 'nın durumunu incelemek için Microsoft eşleme kutucuğuna tıklayabilirsiniz.

![kutucukları izleme](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Ağ Performansı İzleyicisi sayfası

NPM sayfasında, ExpressRoute bağlantı hattı ve eşleme sistem durumunun bir özetini gösteren bir ExpressRoute sayfası bulunur.

![Ekran görüntüsünde, ExpressRoute devreleri ve eşlemelerinin sistem durumuna genel bakış içeren bir pano gösterilir.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Devreler listesi

Tüm izlenen ExpressRoute devrelerinin listesini görüntülemek için **ExpressRoute devreleri** kutucuğuna tıklayın. Bir devre seçebilir ve sistem durumunu, paket kaybı için eğilim grafiklerini, bant genişliği kullanımını ve gecikmeyi görüntüleyebilirsiniz. Grafikler etkileşimlidir. Grafikleri çizmek için özel bir zaman penceresi seçebilirsiniz. Farenizi yakınlaştırmak için grafikteki bir alanın üzerine sürükleyebilirsiniz ve hassas veri noktalarını görebilirsiniz.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Kayıp, gecikme süresi ve aktarım hızı eğilimi

Bant genişliği, gecikme süresi ve kayıp grafikleri etkileşimlidir. Fare denetimlerini kullanarak bu grafiklerin herhangi bir bölümüne yakınlaştırabilirsiniz. Ayrıca, sol üstteki Eylemler düğmesinin altında bulunan **Tarih/saat**' i tıklatarak diğer aralıklar için bant genişliği, gecikme süresi ve kayıp verilerini de görebilirsiniz.

![imin](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Eşleme listesi

Özel eşleme üzerinden sanal ağlara yönelik tüm bağlantıların listesini görüntülemek için, panoda **özel** eşlemeler kutucuğuna tıklayın. Burada, bir sanal ağ bağlantısı seçebilir ve sistem durumunu, paket kaybı için eğilim grafiklerini, bant genişliği kullanımını ve gecikme süresini görüntüleyebilirsiniz.

![devre listesi](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Düğümler görünümü

Seçilen ExpressRoute eşleme bağlantısı için şirket içi düğümler ve Azure VM 'Leri/Microsoft hizmet uç noktaları arasındaki tüm bağlantıların listesini görüntülemek için, **düğüm bağlantılarını görüntüle**' ye tıklayın. Her bağlantının sistem durumunu ve bunlarla ilişkili kaybın ve gecikme süresinin eğilimlerini görüntüleyebilirsiniz.

![düğümler görünümü](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Devre topolojisi

Devre topolojisini görüntülemek için **topoloji** kutucuğuna tıklayın. Bu, sizi seçili Devre veya eşlemenin topoloji görünümüne götürür. Topoloji diyagramı, ağdaki her segment için gecikme süresi sağlar. Her katman 3 atlama, diyagramın bir düğümü ile temsil edilir. Bir atlamaya tıkladığınızda atlama hakkında daha fazla ayrıntı ortaya koyarlar.

Kaydırıcı çubuğunu **filtrelerin** altına taşıyarak şirket içi atlamaları dahil etmek için görünürlük düzeyini artırabilirsiniz. Kaydırıcı çubuğunu sola veya sağa taşımak, topoloji grafiğindeki atlama sayısını artırır/azaltır. Her bir kesimde gecikme süresi görünür ve bu, ağınızda yüksek gecikme süreli segmentlerin daha hızlı yalıtımına olanak tanır.

![filtreler](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Devrenin ayrıntılı topoloji görünümü

Bu görünüm VNet bağlantılarını gösterir.
![ayrıntılı topoloji](./media/how-to-npm/17.png)