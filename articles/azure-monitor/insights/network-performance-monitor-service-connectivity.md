---
title: Azure Log Analytics Ağ Performansı İzleyicisi çözümü | Microsoft Docs
description: Açık bir TCP bağlantı noktası olan herhangi bir uç noktaya ağ bağlantısını izlemek için Ağ Performansı İzleyicisi ' deki hizmet bağlantısı Izleyicisi özelliğini kullanın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: dd02dad6d0f5b42181ad0bb27201384a6336ed6e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898818"
---
# <a name="service-connectivity-monitor"></a>Hizmet Bağlantısı İzleyicisi

Açık bir TCP bağlantı noktası olan herhangi bir uç noktaya ağ bağlantısını izlemek için [ağ performansı İzleyicisi](network-performance-monitor.md) ' deki hizmet bağlantısı İzleyicisi özelliğini kullanabilirsiniz. Bu uç noktalar, Web siteleri, SaaS uygulamaları, PaaS uygulamaları ve SQL veritabanlarını içerir. 

Hizmet bağlantısı Izleyicisi ile aşağıdaki işlevleri gerçekleştirebilirsiniz: 

- Birden çok şube ofislerinden veya konumlarından uygulamalarınızın ve ağ hizmetlerinizin ağ bağlantısını izleyin. Uygulamalar ve ağ hizmetleri Office 365, Dynamics CRM, iç iş kolu uygulamaları ve SQL veritabanlarını içerir.
- Office 365 ve Dynamics 365 uç noktalarına ağ bağlantısını izlemek için yerleşik testleri kullanın. 
- Uç noktaya bağlanılırken yanıt süresini, ağ gecikmesini ve paket kaybını tespit edin.
- Ağ veya uygulama sağlayıcının sonundaki bazı sorunlar nedeniyle zayıf uygulama performansının olup olmadığını belirleme.
- Ağ üzerinde, bir topoloji eşlemesindeki her bir atlama tarafından katkıda bulunulan gecikme süresini görüntüleyerek kötü uygulama performansına neden olabilecek etkin noktaları belirler.


![Hizmet Bağlantısı İzleyicisi](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Yapılandırma 
Ağ Performansı İzleyicisi yapılandırmasını açmak için [ağ performansı İzleyicisi çözümünü](network-performance-monitor.md) açın ve **Yapılandır**' ı seçin.

![Ağ Performansı İzleyicisi’ni Yapılandırma](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>İzleme için Log Analytics aracılarını yapılandırma
İzleme için kullanılan düğümlerde aşağıdaki güvenlik duvarı kurallarını etkinleştirerek, çözümün düğümünüzden hizmet uç noktasına yönelik topolojiyi keşfedebilmesini sağlayın: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Hizmet bağlantısı Izleyicisi testleri oluşturma 

Hizmet uç noktalarına ağ bağlantısını izlemek için testlerinizi oluşturmaya başlayın.

1. **Hizmet bağlantısı İzleyicisi** sekmesini seçin.
2. **Test Ekle**' yi seçin ve test adını ve açıklamasını girin. Çalışma alanı başına en fazla 450 test oluşturabilirsiniz. 
3. Test türünü seçin:<br>

    * Outlook.office365.com veya bing.com gibi HTTP/S isteklerine yanıt veren bir hizmete bağlantıyı izlemek için **Web** ' i seçin.<br>
    * TCP isteklerine yanıt veren ancak SQL Server, FTP sunucusu veya SSH bağlantı noktası gibi HTTP/S isteklerine yanıt vermeyen bir hizmete bağlantıyı izlemek için **ağ** ' ı seçin. 
    * Örneğin: bir BLOB depolama hesabına Web testi oluşturmak Için **Web** ' i seçin ve hedefi *yourstorageaccount*. blob.Core.Windows.net olarak girin. Benzer şekilde, [Bu bağlantıyı](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints) kullanarak diğer tablo depolama, kuyruk depolama ve Azure dosyaları için testler de oluşturabilirsiniz.
4. Ağ gecikmesi, paket kaybı ve topoloji bulma gibi ağ ölçümleri gerçekleştirmek istemiyorsanız **ağ ölçülerini gerçekleştir** onay kutusunu temizleyin. Özellikten en yüksek avantajdan yararlanmak için seçili kalsın. 
5. **Hedef**bölümünde, ağ bağlantısını Izlemek istediğiniz URL/FQDN/IP adresini girin.
6. **Bağlantı noktası numarası**alanına hedef hizmetin bağlantı noktası numarasını girin. 
7. **Sınama sıklığı**' nda, testin ne sıklıkta çalışmasını istediğinizi gösteren bir değer girin. 
8. Hizmetine ağ bağlantısını izlemek istediğiniz düğümleri seçin. Test başına eklenen aracıların sayısının 150 ' den küçük olduğundan emin olun. Herhangi bir aracı en fazla 150 uç noktası/Aracısı test edebilir.

    >[!NOTE]
    > Windows Server tabanlı düğümler için, yetenek ağ ölçümlerini gerçekleştirmek için TCP tabanlı istekleri kullanır. Windows istemci tabanlı düğümler için, yetenek ağ ölçümlerini gerçekleştirmek üzere ıCMP tabanlı istekleri kullanır. Bazı durumlarda, hedef uygulama, düğümler Windows istemci tabanlı olduğunda gelen ıCMP tabanlı istekleri engeller. Çözüm ağ ölçümleri gerçekleştiremiyor. Bu gibi durumlarda Windows Server tabanlı düğümleri kullanmanızı öneririz. 

9. Seçtiğiniz öğeler için sistem durumu olayları oluşturmak istemiyorsanız, **Bu testin kapsadığı hedeflerde sistem durumu Izlemeyi etkinleştir**' i temizleyin. 
10. İzleme koşullarını seçin. Eşik değerlerini girerek sistem durumu olayı oluşturma için özel eşikler ayarlayabilirsiniz. Koşulun değeri seçili ağ veya alt ağ çifti için seçili eşiğin üstünde olduğunda bir sistem durumu olayı oluşturulur. 
11. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin. 

    ![Hizmet bağlantısı Izleyicisi test yapılandırması](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Kılavuz 

Ağ Performansı İzleyicisi Pano görünümüne gidin. Oluşturduğunuz farklı testlerin sistem durumunun özetini almak için **hizmet bağlantısı İzleyicisi** sayfasına bakın. 

![Hizmet bağlantısı Izleyicisi sayfası](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

**Testler sayfasında testlerin** ayrıntılarını görüntülemek için kutucuğu seçin. Soldaki tabloda, tüm testler için hizmet yanıt süresinin, ağ gecikmesi ve paket kaybı 'nın zaman içindeki durumunu ve değerini görüntüleyebilirsiniz. Ağ anlık görüntüsünü geçmişteki bir zamanda görüntülemek için ağ durumu kaydedici denetimini kullanın. Araştırmak istediğiniz tablodaki testi seçin. Sağdaki bölmedeki grafiklerde, kayıp, gecikme süresi ve yanıt süresi değerlerinin geçmiş eğilimini görebilirsiniz. Her düğümdeki performansı görüntülemek için **test ayrıntıları** bağlantısını seçin.

![Hizmet bağlantısı Izleyicisi testleri](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

**Test düğümleri** görünümünde, her düğümden ağ bağlantısını gözlemleyebilirsiniz. Performans düşüşüne sahip düğümü seçin. Bu, uygulamanın yavaş çalıştığını gözlemlediği düğümdür.

Uygulamanın yanıt süresi ve ağ gecikmesi arasındaki bağıntıyı gözlemleyerek ağ veya uygulama sağlayıcısının sonundaki bir sorun nedeniyle zayıf uygulama performansının olup olmadığını belirleme. 

* **Uygulama sorunu:** Yanıt süresi içinde bir ani artış, ancak ağ gecikmesi içinde tutarlılık, ağın sorunsuz çalıştığını ve sorun uygulamanın sonunda bir sorundan kaynaklanıyor olabilir. 

    ![Hizmet bağlantısı Izleyicisi uygulama sorunu](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Ağ sorunu:** Ağ gecikme süresi içinde karşılık gelen ani yanıt süresi ile birlikte bir ani artış, yanıt süresi artışının ağ gecikmesi artışının bir artması nedeniyle kaynaklanabilir. 

    ![Hizmet bağlantısı Izleyicisi ağ sorunu](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Sorunun ağ nedeniyle olduğunu belirledikten sonra, topoloji eşlemesindeki sorunlu atağını belirlemek için **topoloji** görünümü bağlantısını seçin. Aşağıdaki görüntüde bir örnek gösterilmektedir. Düğüm ile uygulama uç noktası arasındaki 105-MS toplam gecikme süresi, 96 MS, kırmızı olarak işaretlenen atlama nedeniyle yapılır. Sorunlu atyı tanımladıktan sonra düzeltici eylem gerçekleştirebilirsiniz. 

![Hizmet bağlantısı Izleyicisi testleri](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Tanılama 

Bir tamsayı gözlemlerseniz, şu adımları izleyin:

* Hizmet yanıt süresi, ağ kaybı ve gecikme yok olarak gösteriliyorsa aşağıdaki nedenlerden biri veya birkaçı nedeni olabilir:

    - Uygulama çalışmıyor.
    - Hizmete ağ bağlantısını denetlemek için kullanılan düğüm çalışmıyor.
    - Test yapılandırmasına girilen hedef yanlış.
    - Düğümün hiçbir ağ bağlantısı yok.

* Geçerli bir hizmet yanıt süresi gösterilirse, ancak gecikme süresi de dahil olmak üzere ağ kaybı, aşağıdaki nedenlerden biri veya birkaçı nedeni olabilir:

    - Hizmete ağ bağlantısını denetlemek için kullanılan düğüm bir Windows istemci makinese, hedef hizmet ıCMP isteklerini engelliyor veya bir ağ güvenlik duvarı düğümden kaynaklanan ıCMP isteklerini engelliyor.
    - **Ağ ölçümlerini Uygula** onay kutusu, test yapılandırmasında boştur. 

* Hizmet yanıt süresi yoksa ancak ağ kaybı ve gecikme süresi geçerliyse, hedef hizmet bir Web uygulaması olmayabilir. Test yapılandırmasını düzenleyin ve test türünü **Web**yerine **ağ** olarak seçin. 

* Uygulama yavaş çalışıyorsa, ağ veya uygulama sağlayıcısının sonundaki bir sorun nedeniyle zayıf uygulama performansının olup olmadığını saptayın.

## <a name="gcc-office-urls-for-us-government-customers"></a>ABD kamu müşterileri için GCC ofisi URL 'Leri
ABD kamu Virginia bölgesi için yalnızca DOD URL 'Leri yerleşik NPM ' dir. GCC URL 'Lerini kullanan müşterilerin özel testler oluşturması ve her bir URL 'YI tek tek eklemesi gerekir.

| Alan | GCC |
|:---   |:--- |
| Office 365 Portalı ve paylaşılan | portal.apps.mil |
| Office 365 kimlik doğrulaması ve kimliği | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS takımları | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Sonraki adımlar
Ayrıntılı ağ performansı veri kayıtlarını görüntülemek için [günlüklerde arama](../../azure-monitor/log-query/log-query-overview.md) yapın.
