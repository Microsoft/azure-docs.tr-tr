---
title: Azure Site Recovery kullanarak çok katmanlı bir SharePoint uygulaması için olağanüstü durum kurtarma
description: Bu makalede, Azure Site Recovery özellikleri kullanılarak çok katmanlı bir SharePoint uygulaması için olağanüstü durum kurtarmanın nasıl ayarlanacağı açıklanır.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: cc72cb4134e6492478805421e448df26a8dc4554
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622420"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Azure Site Recovery kullanarak olağanüstü durum kurtarma için çok katmanlı bir SharePoint uygulaması için olağanüstü durum kurtarmayı ayarlama

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak bir SharePoint uygulamasını nasıl koruyabileceğiniz ayrıntılı bilgiler açıklanmaktadır.


## <a name="overview"></a>Genel Bakış

Microsoft SharePoint, bir grup veya departmanın bilgileri düzenleme, işbirliği yapma ve paylaşma konusunda yardımcı olabilecek güçlü bir uygulamadır. SharePoint, intranet portalları, belge ve dosya yönetimi, işbirliği, sosyal ağlar, extranet 'ler, Web siteleri, kurumsal arama ve iş zekası sağlayabilir. Ayrıca sistem tümleştirmesi, işlem tümleştirme ve iş akışı Otomasyonu özellikleri de vardır. Genellikle, kuruluşlar bunu kapalı kalma süresi ve veri kaybına duyarlı bir katman 1 uygulaması olarak kabul etyordu.

Günümüzde Microsoft SharePoint, kullanıma hazır bir olağanüstü durum kurtarma özelliği sağlamıyor. Bir olağanüstü durumdan oluşan tür ve ölçeğe bakılmaksızın kurtarma, grubu kurtarabileceğiniz bir bekleme veri merkezi kullanımını içerir. Yerel yedekli sistemlerin ve yedeklemelerin birincil veri merkezindeki kesintiden kurtulamadığında senaryolar için bekleme veri merkezleri gerekir.

İyi bir olağanüstü durum kurtarma çözümü, SharePoint gibi karmaşık uygulama mimarilerinin etrafında kurtarma planlarının modellenmesi için izin almalıdır. Ayrıca, çeşitli katmanlar arasında uygulama eşlemelerini işlemek için özelleştirilmiş adımlar ekleyebilmelidir ve bu nedenle olağanüstü bir durum oluşması durumunda tek tıklamayla bir yük devretme işlemi daha düşüktür.

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak bir SharePoint uygulamasını nasıl koruyabileceğiniz ayrıntılı bilgiler açıklanmaktadır. Bu makalede, üç katmanlı bir SharePoint uygulamasını Azure 'a çoğaltmaya yönelik en iyi uygulamalar ele alınmaktadır, olağanüstü durum kurtarma detayına nasıl gidebileceğiniz ve uygulamanın Azure 'da nasıl yük devredebileceğiniz açıklanır.

Çok katmanlı bir uygulamayı Azure 'a kurtarmak için aşağıdaki videoyu izleyebilirsiniz.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce, aşağıdakileri anladığınızdan emin olun:

1. [Bir sanal makineyi Azure 'a çoğaltma](site-recovery-vmware-to-azure.md)
2. [Kurtarma ağını tasarlama](site-recovery-network-design.md)
3. [Azure 'a yük devretme testi yapma](site-recovery-test-failover-to-azure.md)
4. [Azure 'a yük devretme işlemi yapma](site-recovery-failover.md)
5. [Bir etki alanı denetleyicisini çoğaltma](site-recovery-active-directory.md)
6. [SQL Server çoğaltma](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint mimarisi

SharePoint, belirli hedefleri ve hedefleri karşılayan bir grup tasarımı uygulamak için katmanlı topolojiler ve sunucu rolleri kullanılarak bir veya daha fazla sunucuya dağıtılabilir. Çok sayıda eşzamanlı kullanıcıyı ve çok sayıda içerik öğesini destekleyen tipik bir büyük ve yüksek talebe sahip SharePoint sunucu grubu, ölçeklenebilirlik stratejilerinin bir parçası olarak hizmet gruplama 'yı kullanır. Bu yaklaşım adanmış sunucularda hizmetleri çalıştırmayı, bu Hizmetleri birlikte gruplandırmayı ve sonra sunucuları bir grup olarak ölçeklendirmeyi içerir. Aşağıdaki topolojide üç katmanlı bir SharePoint sunucu grubu için hizmet ve sunucu gruplandırması gösterilmektedir. Farklı SharePoint topolojileriyle ilgili ayrıntılı yönergeler için lütfen SharePoint belgelerine ve ürün hattı mimarilerine bakın. [Bu belgede](https://technet.microsoft.com/library/cc303422.aspx)SharePoint 2013 dağıtımı hakkında daha fazla ayrıntı bulabilirsiniz.



![Dağıtım kalıbı 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery desteği

Site Recovery, uygulama belirsiz ve desteklenen bir makinede çalışan herhangi bir SharePoint sürümüyle birlikte çalışmalıdır. Bu makaleyi oluşturmak için, Windows Server 2012 R2 Enterprise ile VMware sanal makineleri kullanılmıştır. SharePoint 2013 Enterprise Edition ve SQL Server 2014 Enterprise Edition kullanıldı.

### <a name="source-and-target"></a>Kaynak ve hedef

**Senaryo** | **İkincil bir siteye** | **Azure’a**
--- | --- | ---
**Hyper-V** | Evet | Evet
**VMware** | Evet | Evet
**Fiziksel sunucu** | Evet | Evet
**Azure** | NA | Evet


### <a name="things-to-keep-in-mind"></a>Göz önünde bulundurmanız gerekenler

Uygulamanızda herhangi bir katman olarak paylaşılan disk tabanlı bir küme kullanıyorsanız, bu sanal makineleri çoğaltmak için Site Recovery çoğaltma kullanamazsınız. Uygulama tarafından sağlanmış yerel çoğaltmayı kullanabilir ve sonra tüm katmanların yükünü devretmek için bir [kurtarma planı](site-recovery-create-recovery-plans.md) kullanabilirsiniz.

## <a name="replicating-virtual-machines"></a>Sanal makineleri çoğaltma

Sanal makineyi Azure 'a Çoğaltmaya başlamak için [Bu kılavuzu](site-recovery-vmware-to-azure.md) izleyin.

* Çoğaltma tamamlandıktan sonra her bir katmanın her bir sanal makinesine gitdiğinizden ve ' çoğaltılan öğe > ayarları > Özellikler > Işlem ve ağ ' ' de aynı Kullanılabilirlik kümesini seçtiğinizden emin olun. Örneğin, Web katmanınızda 3 VM varsa, tüm 3 VM 'Lerin Azure 'da aynı Kullanılabilirlik kümesinin parçası olacak şekilde yapılandırıldığından emin olun.

    ![Set-AVAILABILITY-set](./media/site-recovery-sharepoint/select-av-set.png)

* Active Directory ve DNS 'yi koruma hakkında yönergeler için bkz. [koruma Active Directory ve DNS](site-recovery-active-directory.md) belgesi.

* SQL Server üzerinde çalışan veritabanı katmanını koruma hakkında yönergeler için bkz. [koruma SQL Server](site-recovery-active-directory.md) belgesi.

## <a name="networking-configuration"></a>Ağ yapılandırması

### <a name="network-properties"></a>Ağ özellikleri

* Uygulama ve Web katmanı VM 'Leri için, VM 'Lerin yük devretmeden sonra doğru DR ağına bağlanması için Azure portal ağ ayarlarını yapılandırın.

    ![Ağ seçin](./media/site-recovery-sharepoint/select-network.png)


* Statik IP kullanıyorsanız, sanal makinenin **hedef IP** alanında yerine gelmesı istediğiniz IP 'yi belirtin

    ![Statik IP 'yi ayarla](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS ve trafik yönlendirme

İnternet 'e yönelik siteler için, Azure aboneliğinde [' Priority ' türünde bir Traffic Manager profili oluşturun](../traffic-manager/traffic-manager-create-profile.md) . Ve sonra DNS ve Traffic Manager profilinizi aşağıdaki şekilde yapılandırın.


| **Olmadığı** | **Kaynak** | **Hedef**|
| --- | --- | --- |
| Genel DNS | SharePoint siteleri için genel DNS <br/><br/> Örn.: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Şirket içi DNS | sharepointonprem.contoso.com | Şirket içi grupta genel IP |


Traffic Manager profilinde, [birincil ve kurtarma uç noktalarını oluşturun](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Şirket içi uç nokta için dış uç noktayı ve Azure uç noktası için genel IP 'yi kullanın. Önceliğin şirket içi uç noktayla daha yüksek ayarlandığından emin olun.

Kullanılabilirlik sonrası yük devretmeyi otomatik olarak algılamak üzere Traffic Manager için SharePoint Web katmanındaki belirli bir bağlantı noktasında (örneğin, 800) bir sınama sayfası barındırın. Bu, SharePoint sitelerinizin hiçbirinde anonim kimlik doğrulamasını etkinleştirebilmeniz durumunda geçici bir çözümdür.

[Traffic Manager profilini](../traffic-manager/traffic-manager-configure-priority-routing-method.md) aşağıdaki ayarlarla yapılandırın.

* Yönlendirme yöntemi-' Priority '
* DNS yaşam süresi (TTL)-' 30 saniye '
* Uç nokta izleyici ayarları-anonim kimlik doğrulamasını etkinleştirebiliyorsanız, belirli bir Web sitesi uç noktası sağlayabilirsiniz. Ya da, belirli bir bağlantı noktasında bir sınama sayfası kullanabilirsiniz (örneğin, 800).

## <a name="creating-a-recovery-plan"></a>Kurtarma planı oluşturma

Kurtarma planı, çok katmanlı bir uygulamadaki çeşitli katmanların yük devretmesini sıralamayı sağlar, bu nedenle uygulama tutarlılığını koruyun. Çok katmanlı bir Web uygulaması için kurtarma planı oluştururken aşağıdaki adımları izleyin. [Kurtarma planı oluşturma hakkında daha fazla bilgi edinin](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Yük devretme gruplarına sanal makineler ekleme

1. Uygulama ve Web katmanı VM 'lerini ekleyerek bir kurtarma planı oluşturun.
2. VM 'Leri gruplandırmak için ' Özelleştir 'e tıklayın. Varsayılan olarak, tüm VM 'Ler ' Grup 1 ' in bir parçasıdır.

    ![RP 'yi özelleştirme](./media/site-recovery-sharepoint/rp-groups.png)

3. Başka bir grup (Grup 2) oluşturun ve Web katmanı VM 'lerini yeni gruba taşıyın. Uygulama katmanı sanal makinelerinizin ' Grup 1 ' in parçası olması ve Web katmanı VM 'lerinin ' Group 2 ' ın parçası olması gerekir. Bu, uygulama katmanı VM 'lerinin önce Web katmanı VM 'Leri tarafından önyüklemesinin ardından olmasını sağlamaktır.


### <a name="adding-scripts-to-the-recovery-plan"></a>Kurtarma planına betikler ekleme

En yaygın olarak kullanılan Azure Site Recovery betikleri aşağıdaki ' Azure 'a Dağıt ' düğmesine tıklayarak Otomasyon hesabınıza dağıtabilirsiniz. Yayımlanmış herhangi bir betiği kullanırken, betikteki yönergeleri izlediğinizden emin olun.

[![Azure’a dağıtma](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. SQL kullanılabilirlik grubu yük devretmesi için ' Group 1 ' öğesine bir ön eylem betiği ekleyin. Örnek betikte yayımlanan ' ASR-SQL-FailoverAG ' betiğini kullanın. Betikteki yönergeleri izlediğinizden ve betikteki gerekli değişiklikleri uygun şekilde seçtiğinizden emin olun.

    ![Add-AG-SCRIPT-adım-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-SCRIPT-adım-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Yük dengeleyiciyi web katmanının yükü devredilen sanal makinelere (Grup 2) eklemek için bir post eylem betiği ekleyin. Örnek betikte yayımlanan ' ASR-AddSingleLoadBalancer ' betiğini kullanın. Betikteki yönergeleri izlediğinizden ve betikteki gerekli değişiklikleri uygun şekilde seçtiğinizden emin olun.

    ![Add-LB-SCRIPT-adım-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-SCRIPT-adım-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. DNS kayıtlarını Azure 'daki yeni gruba işaret etmek üzere güncelleştirmek için el ile bir adım ekleyin.

    * İnternet 'e yönelik siteler için, yük devretme sonrası bir DNS güncelleştirmesi gerekmez. Traffic Manager yapılandırmak için ' Ağ Kılavuzu ' bölümünde açıklanan adımları izleyin. Traffic Manager profili önceki bölümde açıklandığı gibi ayarlandıysa, Azure VM 'de kukla bağlantı noktasını (örnekteki 800) açmak için bir komut dosyası ekleyin.

    * İç kullanıma yönelik siteler için, DNS kaydını yeni Web katmanı VM 'sinin yük dengeleyici IP 'sine işaret etmek üzere güncelleştirmek için el ile bir adım ekleyin.

4. Arama uygulamasını bir yedekten geri yüklemek veya yeni bir arama hizmeti başlatmak için el ile bir adım ekleyin.

5. Arama hizmeti uygulamasını bir yedekten geri yüklemek için aşağıdaki adımları izleyin.

    * Bu yöntem, Arama Hizmeti uygulamasının bir yedeğinin çok zararlı olaydan önce gerçekleştirildiğini ve yedeklemenin DR sitesinde kullanılabilir olduğunu varsayar.
    * Bu, yedeklemenin (örneğin, her gün) planlanarak ve yedeklemenin DR sitesine yerleştirileceği bir kopyalama yordamı kullanılarak kolayca elde edilebilir. Kopyalama yordamları AzCopy (Azure kopyası) veya DFSR (dağıtılmış dosya hizmetleri çoğaltması) gibi betikleştirilmiş programları içerebilir.
    * Artık SharePoint grubu çalışıyor olduğuna göre, Yönetim Merkezi, ' yedekleme ve geri yükleme ' ' ye gidin ve geri yükle ' yi seçin. Geri yükleme, yedekleme konumunu belirtilen şekilde yedekler (değeri güncelleştirmeniz gerekebilir). Geri yüklemek istediğiniz Arama Hizmeti uygulama yedeklemesini seçin.
    * Arama geri yüklendi. Geri yüklemenin aynı topolojiyi (aynı sayıda sunucu) ve bu sunuculara atanmış sabit sürücü harflerini bulmasını beklediğini aklınızda bulundurun. Daha fazla bilgi için, bkz. [' SharePoint 2013 'de arama hizmeti uygulaması geri yükleme '](https://technet.microsoft.com/library/ee748654.aspx) belgesi.


6. Yeni bir arama hizmeti uygulamasıyla başlamak için aşağıdaki adımları izleyin.

    * Bu yöntem, DR sitesinde "arama yönetimi" veritabanının bir yedeğinin bulunduğunu varsayar.
    * Diğer Arama Hizmeti uygulama veritabanları çoğaltılmadığından, yeniden oluşturulması gerekir. Bunu yapmak için, Merkezi Yönetim ' e gidin ve Arama Hizmeti uygulamasını silin. Arama dizinini barındıran tüm sunucularda dizin dosyalarını silin.
    * Arama Hizmeti uygulamasını yeniden oluşturun ve bu veritabanlarını yeniden oluşturur. Tüm eylemleri GUI aracılığıyla gerçekleştirmek mümkün olmadığından, bu hizmet uygulamasını yeniden oluşturan hazırlanmış bir betiğin olması önerilir. Örneğin, Dizin sürücüsü konumunu ayarlama ve arama topolojisini yapılandırma yalnızca SharePoint PowerShell cmdlet 'leri kullanılarak yapılabilir. Windows PowerShell cmdlet 'ini geri yükleme-SPEnterpriseSearchServiceApplication kullanın ve günlük ile gönderilen ve çoğaltılan arama Yönetim veritabanını Search_Service__DB. Bu cmdlet, arama yapılandırmasını, şemayı, yönetilen özellikleri, kuralları ve kaynakları verir ve diğer bileşenlerin varsayılan bir kümesini oluşturur.
    * Arama Hizmeti uygulama yeniden oluşturulduktan sonra, Arama Hizmeti geri yüklemek için her bir içerik kaynağı için tam bir gezinme başlatmanız gerekir. Arama önerileri gibi şirket içi gruptaki bazı analiz bilgilerini kaybedersiniz.

7. Tüm adımlar tamamlandıktan sonra kurtarma planını kaydedin ve son kurtarma planı aşağıdaki gibi görünür.

    ![Kayıtlı RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Yük devretme testi yapma
Yük devretme testi yapmak için [Bu kılavuzu](site-recovery-test-failover-to-azure.md) izleyin.

1.  Azure portal gidin ve kurtarma hizmeti kasanızı seçin.
2.  SharePoint uygulaması için oluşturulan kurtarma planına tıklayın.
3.  ' Test yük devretmesi ' seçeneğine tıklayın.
4.  Yük devretme testi işlemini başlatmak için kurtarma noktasını ve Azure sanal ağını seçin.
5.  İkincil ortam kurulduktan sonra, doğrulamaları gerçekleştirebilirsiniz.
6.  Doğrulamalar tamamlandıktan sonra kurtarma planında ' test yük devretmesini Temizle ' düğmesine tıklayabilirsiniz ve yük devretme testi ortamı temizlenir.

AD ve DNS için yük devretme testi yapmaya yönelik yönergeler için bkz. [ad ve DNS belgesi Için test yük devretmesi konuları](site-recovery-active-directory.md#test-failover-considerations) .

SQL Always ON kullanılabilirlik grupları için yük devretme testi gerçekleştirme hakkında yönergeler için bkz. [Azure Site Recovery Ile uygulama kurtarma ve yük devretme testi belgesi gerçekleştirme](site-recovery-sql.md#disaster-recovery-of-an-application) .

## <a name="doing-a-failover"></a>Yük devretme yapma
Yük devretme işlemi gerçekleştirmek için [Bu yönergeleri](site-recovery-failover.md) izleyin.

1.  Azure portal gidin ve kurtarma hizmetleri kasanızı seçin.
2.  SharePoint uygulaması için oluşturulan kurtarma planına tıklayın.
3.  ' Yük devretme ' seçeneğine tıklayın.
4.  Yük devretme işlemini başlatmak için kurtarma noktası ' nı seçin.

## <a name="next-steps"></a>Sonraki adımlar
Site Recovery kullanarak [diğer uygulamaları çoğaltma](site-recovery-workload.md) hakkında daha fazla bilgi edinebilirsiniz.
