---
title: Azure VMware çözümünde Web uygulamalarınızı korumak için Azure Application Gateway kullanma
description: Azure VMware çözümünde çalışan Web uygulamalarınızı güvenli bir şekilde sunmak için Azure Application Gateway 'i yapılandırın.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: f92027ee46fdaae275939acaea10e144b6bde101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601925"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Azure VMware çözümünde Web uygulamalarınızı korumak için Azure Application Gateway kullanma

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) , Web uygulamalarınıza yönelik trafiği yönetmenizi sağlayan bir katman 7 Web trafiği yük dengeleyicidir. Azure VMware Çözüm v 1.0 ve v 2.0 ile birlikte sunulur. Her iki sürüm de Azure VMware çözümünde çalışan Web Apps ile test edilmiştir.

Özellikleri şunlardır: 
- Tanımlama bilgisi tabanlı oturum benzeşimi
- URL tabanlı yönlendirme
- Web Uygulaması Güvenlik Duvarı (WAF)

Özelliklerin tamamı listesi için bkz. [Azure Application Gateway özellikleri](../application-gateway/features.md). 

Bu makalede, Azure VMware çözümünde çalışan bir Web uygulamasını korumak için bir Web sunucusu grubunun önündeki Application Gateway nasıl kullanacağınız gösterilmektedir. 

## <a name="topology"></a>Topoloji
Diyagram, Azure IaaS sanal makinelerini (VM), Azure sanal makine ölçek kümelerini veya şirket içi sunucuları korumak için Application Gateway nasıl kullanıldığını gösterir. Application Gateway, Azure VMware Çözüm VM 'lerini şirket içi sunucular olarak değerlendirir. 

![Azure IaaS sanal makinelerini (VM), Azure sanal makine ölçek kümelerini veya şirket içi sunucuları Application Gateway nasıl koruduğunu gösteren diyagram.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway Şu anda Azure VMware Çözüm VM 'lerinde çalışan Web uygulamalarını kullanıma sunmak için desteklenen tek yöntemdir.

Diyagramda, Azure VMware Çözüm Web uygulamalarıyla Application Gateway doğrulamak için kullanılan test senaryosu gösterilmektedir.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Azure VMware Çözüm Web uygulamalarıyla Application Gateway doğrulamak için kullanılan test senaryosunu gösteren diyagram." border="false":::

Application Gateway örnek, Hub üzerinde ayrılmış bir alt ağda dağıtılır. Azure genel IP adresine sahiptir. Sanal ağ için [Azure DDoS koruma standardının](../ddos-protection/ddos-protection-overview.md) etkinleştirilmesi önerilir. Web sunucusu, NSX T0 ve T1 ağ geçitlerinin arkasındaki bir Azure VMware çözümü özel bulutu üzerinde barındırılır. Azure VMware çözümü, hub ve şirket içi sistemlerle iletişimi etkinleştirmek için [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) kullanır.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı.
- Azure VMware çözümü özel bulutu dağıtıldı ve çalışıyor.

## <a name="deployment-and-configuration"></a>Dağıtım ve yapılandırma

1. Azure portal, **Application Gateway** araması yapın ve **uygulama ağ geçidi oluştur**' u seçin.

2. Aşağıdaki şekilde temel ayrıntıları sağlayın; ardından Ileri ' yi seçin **: ön uçlar>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Azure portal 'de uygulama ağ geçidi oluştur sayfasını gösteren ekran görüntüsü.":::

3. Ön uç IP adresi türünü seçin. Ortak için, mevcut bir genel IP adresi seçin veya yeni bir tane oluşturun. **İleri ' yi seçin: backends>**.

    > [!NOTE]
    > Özel ön uçlar için yalnızca standart ve Web uygulaması güvenlik duvarı (WAF) SKU 'Ları desteklenir.

4. Azure VMware Çözüm altyapısında çalışan VM 'Ler için bir arka uç havuzu ekleyin. Azure VMware çözümü özel bulutu üzerinde çalışan Web sunucularının ayrıntılarını sağlayın ve **Ekle**' yi seçin.  Ardından Ileri ' yi seçin **: yapılandırma>**.

5. **Yapılandırma** sekmesinde, **yönlendirme kuralı ekle**' yi seçin.

6. **Dinleyici** sekmesinde, dinleyicinin ayrıntılarını sağlayın. HTTPS seçilirse, bir PFX dosyasından veya var olan bir Azure Key Vault sertifikasından bir sertifika sağlamanız gerekir. 

7. **Arka uç hedefleri** sekmesini seçin ve daha önce oluşturulan arka uç havuzunu seçin. **Http ayarları** alanı Için **Yeni Ekle**' yi seçin.

8. HTTP ayarlarının parametrelerini yapılandırın. **Add (Ekle)** seçeneğini belirleyin.

9. Yol tabanlı kuralları yapılandırmak istiyorsanız, **yol tabanlı bir kural oluşturmak için birden çok hedef Ekle**' yi seçin. 

10. Yol tabanlı bir kural ekleyin ve **Ekle**' yi seçin. Yol tabanlı daha fazla kural eklemek için tekrarlayın. 

11. Yol tabanlı kurallar eklemeyi bitirdiğinizde yeniden **Ekle** ' yi seçin. ardından Ileri ' yi seçin **: etiketler>**. 

12. Etiketler ekleyin ve ardından Ileri ' yi seçin **: gözden geçir + oluştur>**.

13. Application Gateway bir doğrulama çalışacaktır; başarılı olursa, dağıtmak için **Oluştur** ' u seçin.

## <a name="configuration-examples"></a>Yapılandırma örnekleri

Şimdi Azure VMware Çözüm VM 'leriyle Application Gateway aşağıdaki kullanım örnekleri için arka uç havuzları olarak yapılandıracağız: 

- [Birden çok site barındırma](#hosting-multiple-sites)
- [URL 'ye göre yönlendirme](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Birden çok site barındırma

Bu yordamda, mevcut bir uygulama ağ geçidinde Azure VMware çözümü özel bulutu üzerinde çalışan VM 'Ler kullanılarak arka uç adres havuzlarının nasıl tanımlanacağı gösterilmektedir. 

>[!NOTE]
>Bu yordamda birden çok etki alanı olduğu varsayılır, bu nedenle www.contoso.com ve www.fabrikam.com örneklerini kullanacağız.


1. Özel bulutunuzda iki farklı VM havuzu oluşturun. Biri contoso ve ikinci fabrikam 'ı temsil eder. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="VSphere Istemcisinde bir Web sunucusunun ayrıntılarının özetini gösteren ekran görüntüsü.":::

    Windows Server 2016 ' i Internet Information Services (IIS) rolü yüklenmiş olarak kullandık. VM 'Ler yüklendikten sonra, VM 'lerin her birinde IIS 'yi yapılandırmak için aşağıdaki PowerShell komutlarını çalıştırın. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Mevcut bir Application Gateway örneğinde sol menüden **arka uç havuzları** ' nı seçin,  **Ekle**' yi seçin ve yeni havuzların ayrıntılarını girin. Sağ bölmede **Ekle** ' yi seçin.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Arka uç havuzları eklemek için arka uç havuzları sayfasının ekran görüntüsü." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. **Dinleyiciler** bölümünde her Web sitesi için yeni bir dinleyici oluşturun. Her bir dinleyicinin ayrıntılarını girin ve **Ekle**' yi seçin.

4. Sol tarafta **http ayarları** ' nı seçin ve sol bölmedeki **Ekle** ' yi seçin. Ayrıntıları doldurarak yeni bir HTTP ayarı oluşturun ve **Kaydet**' i seçin.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Yeni bir HTTP ayarı oluşturmak için HTTP ayarları sayfasının ekran görüntüsü." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Sol menünün **kurallar** bölümünde kuralları oluşturun. Her kuralı ilgili dinleyiciyle ilişkilendirin. **Add (Ekle)** seçeneğini belirleyin.

6. Karşılık gelen arka uç havuzunu ve HTTP ayarlarını yapılandırın. **Add (Ekle)** seçeneğini belirleyin.

7. Bağlantıyı test edin. Tercih ettiğiniz tarayıcınızı açın ve Azure VMware Çözüm ortamınızda barındırılan farklı Web sitelerine gidin (örneğin,) http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Bağlantının başarıyla test olduğunu gösteren tarayıcı sayfasının ekran görüntüsü.":::

### <a name="routing-by-url"></a>URL 'ye göre yönlendirme

Aşağıdaki adımlar, bir Azure VMware çözümü özel bulutu üzerinde çalışan VM 'Leri kullanarak arka uç adres havuzlarını tanımlar. Özel bulut, mevcut bir Application Gateway üzerinde. Daha sonra, Web trafiğinin havuzlardaki uygun sunuculara ulaştığınızdan emin olmak için yönlendirme kuralları oluşturursunuz.

1. Özel bulutunuzda, Web grubunu temsil eden bir sanal makine havuzu oluşturun. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Başka bir VM 'nin özetini gösteren VMSphere Istemcisinde sayfanın ekran görüntüsü.":::

    Bu öğreticiyi göstermek için IIS rolü yüklü olan Windows Server 2016 kullanılmıştır. VM 'Ler yüklendikten sonra, IIS 'yi her bir VM öğreticisi için yapılandırmak üzere aşağıdaki PowerShell komutlarını çalıştırın. 

    İlk sanal makine olan contoso-Web-01, ana Web sitesini barındıracaktır.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    Contoso-Web-02 ikinci sanal makine, görüntüler sitesini barındıracaktır.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    Contoso-Web-03 olan üçüncü sanal makine, video sitesini barındıracaktır.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Mevcut bir Application Gateway örneğine üç yeni arka uç havuzu ekleyin. 

   1. Sol menüden **arka uç havuzları** ' nı seçin. 
   1. **Ekle** ' yi seçin ve ilk havuzun ( **contoso-Web**) ayrıntılarını girin. 
   1. Hedef olarak bir VM ekleyin. 
   1. **Add (Ekle)** seçeneğini belirleyin. 
   1. Hedef olarak benzersiz bir VM ekleyerek **contoso görüntüleri** ve **contoso-video** için bu işlemi tekrarlayın. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Üç yeni arka uç havuzunun eklenmesini gösteren arka uç havuzları sayfasının ekran görüntüsü." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. **Dinleyiciler** bölümünde, 8080 numaralı bağlantı noktasını kullanarak temel türünde yeni bir dinleyici oluşturun.

4. Sol gezinti çubuğunda **http ayarları** ' nı seçin ve sol bölmeden **Ekle** ' yi seçin. Ayrıntıları doldurarak yeni bir HTTP ayarı oluşturun ve **Kaydet**' i seçin.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Http ayarları yapılandırmasını gösteren HTTP ayarı Ekle sayfasının ekran görüntüsü.":::

5. Sol menünün **kurallar** bölümünde kuralları oluşturun. Her kuralı önceden oluşturulmuş dinleyiciyle ilişkilendirin. Ardından ana arka uç havuzunu ve HTTP ayarlarını yapılandırın. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Yönlendirme kurallarını bir arka uç hedefine yapılandırmak için yönlendirme kuralı ekle sayfasının ekran görüntüsü.":::

6. Yapılandırmayı test edin. Azure portal uygulama ağ geçidine erişin ve genel IP adresini **genel bakış** bölümüne kopyalayın. 

   1. Yeni bir tarayıcı penceresi açın ve URL 'YI girin `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Yapılandırmanın başarılı testini gösteren tarayıcı sayfasının ekran görüntüsü.":::

   1. URL'yi `http://<app-gw-ip-address>:8080/images/test.htm` olarak değiştirin.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Yeni URL ile başka bir başarılı testin ekran görüntüsü.":::

   1. URL 'YI tekrar olarak değiştirin `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Son URL ile başarılı testin ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki Adımlar

Azure VMware çözümünde çalışan bir Web uygulamasını korumak için Application Gateway kullanmayı da kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz:

- [Azure Application Gateway farklı senaryolar Için yapılandırma](../application-gateway/configuration-overview.md).
- [Azure VMware Çözüm iş yüklerini dengelemek için Traffic Manager dağıtma](deploy-traffic-manager-balance-workloads.md).
- [Azure NetApp Files Azure VMware Çözüm tabanlı iş yükleri Ile tümleştirme](netapp-files-with-azure-vmware-solution.md).
- [Sanal ağlarda Azure kaynaklarını koruma](../ddos-protection/ddos-protection-overview.md).
