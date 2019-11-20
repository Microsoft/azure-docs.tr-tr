---
title: Yapılandırma SSS-Azure App Service | Microsoft Docs
description: Azure App Service Web Apps özelliği için yapılandırma ve yönetim sorunları hakkında sık sorulan soruların yanıtlarını alın.
services: app-service\web
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 111ee6cda46677b3b0fc39f5a84268e6ac192da9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470537"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Azure 'da Web Apps için yapılandırma ve yönetim SSS

Bu makalede, [Azure App Service Web Apps özelliğinin](https://azure.microsoft.com/services/app-service/web/)yapılandırma ve yönetim sorunları hakkında sık sorulan soruların (SSS) yanıtları bulunur.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>App Service kaynaklarını taşımak istersem dikkat etmeniz gereken sınırlamalar var mı?

App Service kaynaklarını yeni bir kaynak grubuna veya aboneliğe taşımayı planlıyorsanız, bilmeniz için bazı sınırlamalar vardır. Daha fazla bilgi için bkz. [App Service sınırlamaları](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Nasıl yaparım? Web uygulamam için özel bir etki alanı adı mı kullanıyorsunuz?

Azure Web uygulamanızla özel bir etki alanı adı kullanmayla ilgili yaygın soruların yanıtları için bkz. yedi dakikalık videomız [özel etki alanı adı ekleme](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Video, özel bir etki alanı adının nasıl ekleneceğini gösteren bir yol sunar. App Service Web uygulamanız ile *. azurewebsites.net URL 'SI yerine kendi URL 'nizin nasıl kullanılacağını açıklar. Ayrıca, [özel bir etki alanı adının nasıl eşlendiğini](app-service-web-tutorial-custom-domain.md)gösteren ayrıntılı bir anlatım de görebilirsiniz.


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Web uygulamam için yeni bir özel etki alanı satın Nasıl yaparım? mı?

App Service Web uygulamanız için özel bir etki alanı satın alma ve ayarlama hakkında bilgi edinmek için, bkz. [App Service için özel etki alanı adı satın alma ve yapılandırma](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Web uygulamam için var olan bir SSL sertifikasını karşıya yüklemek ve yapılandırmak Nasıl yaparım? mı?

Var olan bir özel SSL sertifikasını karşıya yükleme ve ayarlama hakkında bilgi edinmek için bkz. [App Service UYGULAMANıZA SSL sertifikası ekleme](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Nasıl yaparım? Web uygulamam için Azure 'da yeni bir SSL sertifikası satın alıp yapılandırın mi?

App Service Web uygulamanız için bir SSL sertifikası satın alma ve ayarlama hakkında bilgi edinmek için, bkz. [App Service UYGULAMANıZA SSL sertifikası ekleme](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Application Insights kaynakları Nasıl yaparım? mi?

Azure Application Insights Şu anda taşıma işlemini desteklemiyor. Özgün kaynak grubunuz bir Application Insights kaynağı içeriyorsa, bu kaynağı taşıyamazsınız. Bir App Service uygulamasını taşımaya çalıştığınızda Application Insights kaynağını eklerseniz, tüm taşıma işlemi başarısız olur. Ancak, Application Insights ve App Service planının, uygulamanın düzgün çalışması için uygulamayla aynı kaynak grubunda olması gerekmez.

Daha fazla bilgi için bkz. [App Service sınırlamaları](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Bir kılavuz denetim listesini nerede bulabilirim ve kaynak taşıma işlemleri hakkında daha fazla bilgi edinebilirsiniz?

[App Service sınırlamalar](../azure-resource-manager/move-limitations/app-service-move-limitations.md) , kaynakları yeni bir aboneliğe veya aynı abonelikte yeni bir kaynak grubuna nasıl taşıyabileceğinizi gösterir. Kaynak taşıma denetim listesi hakkında bilgi alabilir, hangi hizmetlerin taşıma işlemini desteklediğini öğrenin ve App Service sınırlamalar ve diğer konular hakkında daha fazla bilgi edinebilirsiniz.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Web uygulamamın sunucu saat dilimini Nasıl yaparım? mı?

Web uygulamanız için sunucu saat dilimini ayarlamak için:

1. Azure portal, App Service aboneliğinizdeki **uygulama ayarları** menüsüne gidin.
2. **Uygulama ayarları**' nın altında şu ayarı ekleyin:
    * Anahtar = WEBSITE_TIME_ZONE
    * Değer = *istediğiniz saat dilimi*
3. **Kaydet**’i seçin.

Windows üzerinde çalışan uygulama hizmetleri için, kabul edilen değerler için [varsayılan saat dilimleri](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) makalesindeki **saat dilimi** sütununa bakın. Linux üzerinde çalışan uygulama hizmetleri için, [TZ veritabanı adını](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) saat dilimi değeri olarak ayarlayın. Aşağıda, TZ veritabanı adı örneği verilmiştir: Amerika/adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Sürekli Web Işleriniz neden bazen başarısız oluyor?

Varsayılan olarak, Web Apps, belirlenen süre boyunca boşta kalırsa kaldırılır. Bu, sistemin kaynakları tasarruf etmenizi sağlar. Temel ve standart planlarda, Web uygulamasının her zaman yüklenmesini sağlamak için **her zaman açık** ayarını açabilirsiniz. Web uygulamanız sürekli Web Işleri çalıştırıyorsa, **her zaman açık**' i açmanız gerekir, aksi takdirde Web işleri güvenilir bir şekilde çalışmayabilir. Daha fazla bilgi için bkz. [sürekli çalışan bir WebJob oluşturma](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Web uygulamamın giden IP adresini almak Nasıl yaparım? mı?

Web uygulamanıza giden IP adreslerinin listesini almak için:

1. Azure portal, Web uygulaması dikey penceresinde, **Özellikler** menüsüne gidin.
2. **Giden IP adreslerini**arayın.

Giden IP adresleri listesi görüntülenir.

Web siteniz bir App Service Ortamı barındırılıyorsa giden IP adresini alma hakkında bilgi edinmek için bkz. [giden ağ adresleri](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Web uygulamam için ayrılmış veya özel bir gelen IP adresi mi Nasıl yaparım??

Azure uygulama Web sitenize yapılan gelen çağrılar için adanmış veya ayrılmış bir IP adresi ayarlamak için, IP tabanlı bir SSL sertifikası yükleyip yapılandırın.

Gelen çağrılar için adanmış veya ayrılmış bir IP adresi kullanmak için App Service planınızın temel veya daha yüksek bir hizmet planında olması gerektiğini unutmayın.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>App Service sertifikamı, başka bir yerde barındırılan bir Web sitesi gibi Azure dışında kullanmak üzere dışarı aktarabilir miyim? 

Evet, bunları Azure dışında kullanmak üzere dışarı aktarabilirsiniz. Daha fazla bilgi için bkz. [App Service sertifikaları ve özel etki alanları Için SSS](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>App Service sertifikamı diğer Azure bulut hizmetleriyle kullanmak üzere dışarı aktarabilir miyim?

Portal, App Service uygulamalara Azure Key Vault aracılığıyla bir App Service sertifikası dağıtmaya yönelik birinci sınıf bir deneyim sağlar. Ancak, bu sertifikaları Azure sanal makineler gibi App Service platformun dışında kullanmak için müşterilerden gelen istekleri alıyoruz. Sertifikayı diğer Azure kaynaklarıyla kullanabilmeniz için App Service sertifikanızın yerel bir PFX kopyasını oluşturma hakkında bilgi edinmek için, bkz. [bir App Service sertifikasının yerel PFX kopyası oluşturma](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Daha fazla bilgi için bkz. [App Service sertifikaları ve özel etki alanları Için SSS](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Web uygulamamı yedeklemeye çalıştığımda neden "kısmen başarılı oldu" iletisini görüyorum?

Yedekleme hatasının yaygın bir nedeni, bazı dosyaların uygulama tarafından kullanımda olmasına neden olur. Yedekleme gerçekleştirirken kullanımda olan dosyalar kilitlenir. Bu, bu dosyaların yedeklenmesini ve "kısmen başarılı" durumuna neden olabileceğini önler. Dosyaları yedekleme işleminden dışlayarak bunun oluşmasını önleyebilirsiniz. Yalnızca gerekli olanları yedeklemeyi seçebilirsiniz. Daha fazla bilgi için bkz. [Azure Web Apps ile yalnızca sitenizin önemli kısımlarını yedekleme](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Nasıl yaparım? HTTP yanıtından bir üst bilgi kaldırılsın mı?

HTTP yanıtından üstbilgileri kaldırmak için sitenizin Web. config dosyasını güncelleştirin. Daha fazla bilgi için bkz. [Azure Web sitelerindeki standart sunucu üstbilgilerini kaldırma](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>App Service, PCI standardı 3,0 ve 3,1 ile uyumludur mi?

Şu anda Azure App Service Web Apps özelliği, PCI veri güvenliği standardı (DSS) sürüm 3,0 düzey 1 ile uyumlu. PCI DSS sürüm 3,1, yol haritamız üzerinde. En son standardın nasıl kullanılacağına ilişkin planlama zaten devam edecektir.

PCI DSS sürüm 3,1 sertifikası, Aktarım Katmanı Güvenliği 'nin (TLS) 1,0 devre dışı bırakılmasını gerektirir. Şu anda, TLS 1,0 devre dışı bırakılması çoğu App Service planı için bir seçenek değildir. Ancak App Service Ortamı kullanıyorsanız veya iş yükünüzü App Service Ortamı geçirmek istiyorsanız ortamınızda daha fazla denetim sağlayabilirsiniz. Bu, Azure desteği ile iletişim kurarak TLS 1,0 devre dışı bırakılmasını içerir. Yakın gelecekte bu ayarları kullanıcılar için erişilebilir hale getirmek için planlıyoruz.

Daha fazla bilgi için bkz. [PCI Standard 3,0 ve 3,1 ile Microsoft Azure App Service Web uygulaması uyumluluğu](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hazırlama ortamı ve dağıtım yuvaları Nasıl yaparım? mı kullanıyorsunuz?

Standart ve Premium App Service planlarında, Web uygulamanızı App Service dağıtırken, varsayılan üretim yuvası yerine ayrı bir dağıtım yuvasına dağıtım yapabilirsiniz. Dağıtım yuvaları, kendi ana bilgisayar adlarına sahip Canlı Web uygulamalardır. Web uygulaması içeriği ve yapılandırma öğeleri, üretim yuvası dahil olmak üzere iki dağıtım yuvası arasında değiştirilebilir.

Dağıtım yuvalarını kullanma hakkında daha fazla bilgi için bkz. [App Service hazırlama ortamı ayarlama](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Nasıl yaparım? Access ve WebJob günlüklerine bakın

WebJob günlüklerini gözden geçirmek için:

1. [Kudu Web sitenizde](https://*yourwebsitename*.scm.azurewebsites.net)oturum açın.
2. WebJob 'u seçin.
3. **Çıkışı geç** düğmesini seçin.
4. Çıkış dosyasını indirmek için **indirme** bağlantısını seçin.
5. Bireysel çalıştırmalar için **tek tek Invoke**' ı seçin.
6. **Çıkışı geç** düğmesini seçin.
7. İndirme bağlantısını seçin.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>SQL Server Karma Bağlantılar kullanmaya çalışıyorum. "System. OverflowException: aritmetik işlemi bir taşma ile sonuçlandı" iletisini neden görüyorum?

SQL Server erişmek için Karma Bağlantılar kullanırsanız, 10 Mayıs 2016 ' de bir Microsoft .NET güncelleştirme, bağlantıların başarısız olmasına neden olabilir. Şu iletiyi görebilirsiniz:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Çözünürlük

Bu durum, bu yana Karma Bağlantı Yöneticisi düzeltilen bir sorun nedeniyle oluştu. Bu sorunu çözmek için [karma bağlantı Yöneticisi güncelleştirdiğinizden](https://go.microsoft.com/fwlink/?LinkID=841308) emin olun.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Nasıl yaparım? URL yeniden yazma kuralı eklensin mi?

URL yeniden yazma kuralı eklemek için, **Wwwroot** klasöründe ilgili yapılandırma girişleriyle bir Web. config dosyası oluşturun. Daha fazla bilgi için bkz. [Azure Uygulama Hizmetleri: URL yeniden yazmayı anlama](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>App Service gelen trafiği denetlemek Nasıl yaparım??

Site düzeyinde, App Service gelen trafiği denetlemeye yönelik iki seçeneğiniz vardır:

* Dinamik IP kısıtlamalarını açın. Dinamik IP kısıtlamalarını nasıl kullanacağınızı öğrenmek için bkz. [Azure Web siteleri Için IP ve etki alanı kısıtlamaları](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Modül güvenliğini açın. Modül güvenliğini açma hakkında bilgi edinmek için bkz. [Azure Web siteleri 'Nde ModSecurity Web uygulaması güvenlik duvarı](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

App Service Ortamı kullanıyorsanız [Barbcuda güvenlik duvarını](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)kullanabilirsiniz.

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>App Service bir Web uygulamasındaki bağlantı noktaları Nasıl yaparım? engellensin mi?

App Service paylaşılan kiracı ortamında, altyapının doğası nedeniyle belirli bağlantı noktalarını engellemek mümkün değildir. 4020, 4022 ve 4024 TCP bağlantı noktaları, Visual Studio uzaktan hata ayıklama için de açık olabilir.

App Service Ortamı, gelen ve giden trafik üzerinde tam denetime sahip olursunuz. Belirli bağlantı noktalarını kısıtlamak veya engellemek için ağ güvenlik gruplarını kullanabilirsiniz. App Service Ortamı hakkında daha fazla bilgi için bkz. [App Service ortamı tanıtımı](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Nasıl yaparım? bir F12 izlemesi mi yakala?

Bir F12 izlemeyi yakalamak için iki seçeneğiniz vardır:

* F12 HTTP izlemesi
* F12 konsol çıkışı

### <a name="f12-http-trace"></a>F12 HTTP izlemesi

1. Internet Explorer 'da Web sitenize gidin. Sonraki adımları gerçekleştirmeden önce oturum açmanız önemlidir. Aksi halde, F12 izlemesi hassas oturum açma verilerini yakalar.
2. F12 tuşuna basın.
3. **Ağ** sekmesinin seçili olduğunu doğrulayın ve ardından yeşil **yürütme** düğmesini seçin.
4. Sorunu yeniden oluşturmak için gereken adımları uygulayın.
5. Kırmızı **Durdur** düğmesini seçin.
6. **Kaydet** düğmesini (disk simgesi) SEÇIN ve har dosyasını (Internet Explorer ve Microsoft Edge 'de) KAYDEDIN *veya* har dosyasına sağ tıklayın ve ardından **içeriğe sahip har olarak kaydet** (Chrome 'da) seçeneğini belirleyin.

### <a name="f12-console-output"></a>F12 konsol çıkışı

1. **Konsol** sekmesini seçin.
2. Sıfırdan fazla öğe içeren her sekme için sekmeyi (**hata**, **Uyarı**veya **bilgi**) seçin. Sekme seçili değilse, imleci konumundan uzağa taşıdığınızda sekme simgesi gri veya siyah olur.
3. Bölmenin ileti alanına sağ tıklayın ve ardından **Tümünü Kopyala**' yı seçin.
4. Kopyalanmış metni bir dosyaya yapıştırın ve dosyayı kaydedin.

Bir HAR dosyasını görüntülemek için, [har görüntüleyicisini](https://www.softwareishard.com/har/viewer/)kullanabilirsiniz.

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>App Service Web uygulamasını ExpressRoute 'a bağlı bir sanal ağa bağlamayı denediğimde neden hata alıyorum?

Bir Azure Web uygulamasını Azure ExpressRoute 'a bağlı bir sanal ağa bağlamayı denerseniz, başarısız olur. Şu ileti görüntülenir: "ağ geçidi bir VPN ağ geçidi değil."

Şu anda ExpressRoute 'a bağlı bir sanal ağa Noktadan siteye VPN bağlantılarına sahip olabilirsiniz. Aynı sanal ağ için Noktadan siteye VPN ve ExpressRoute birlikte bulunamaz. Daha fazla bilgi için bkz. [ExpressRoute ve siteden sıteye VPN bağlantıları sınırları ve sınırlamaları](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>App Service bir Web uygulamasını statik yönlendirme (ilke tabanlı) ağ geçidine sahip bir sanal ağa bağlama Nasıl yaparım?.

Şu anda, bir App Service Web uygulamasını statik yönlendirme (ilke tabanlı) ağ geçidi olan bir sanal ağa bağlama desteklenmez. Hedef sanal ağınız zaten varsa, bir uygulamaya bağlanmadan önce, dinamik yönlendirme ağ geçidi ile noktadan siteye VPN 'nin etkin olması gerekir. Ağ geçidinizin statik yönlendirme olarak ayarlandıysa, Noktadan siteye VPN 'yi etkinleştiremezsiniz. 

Daha fazla bilgi için bkz. bir [uygulamayı Azure sanal ağı Ile tümleştirme](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>App Service Ortamı, iki çalışanımız olmasına rağmen neden yalnızca bir App Service planı oluşturabilirim?

App Service Ortamı, hata toleransı sağlamak için her çalışan havuzunun en az bir ek işlem kaynağına ihtiyacı olmasını gerektirir. Ek işlem kaynağına bir iş yükü atanamaz.

Daha fazla bilgi için bkz. [App Service ortamı oluşturma](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>App Service Ortamı oluşturmaya çalıştığımda neden zaman aşımları görüyorum?

Bazen App Service Ortamı oluşturma işlemi başarısız olur. Bu durumda, etkinlik günlüklerinde aşağıdaki hatayı görürsünüz:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Bu sorunu çözmek için aşağıdaki koşullardan hiçbirinin doğru olmadığından emin olun:
* Alt ağ çok küçük.
* Alt ağ boş değil.
* ExpressRoute bir App Service Ortamı ağ bağlantısı gereksinimlerini engeller.
* Hatalı bir ağ güvenlik grubu, bir App Service Ortamı ağ bağlantısı gereksinimlerini engeller.
* Zorlamalı tünel açık.

Daha fazla bilgi için bkz. [Yeni bir Azure App Service ortamı dağıtma (oluşturma) hakkında sık karşılaşılan sorunlar](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>App Service planımı Neden silemiyorum?

App Service planıyla ilişkili App Service uygulamalar varsa App Service planı silemezsiniz. Bir App Service planını silmeden önce, tüm ilişkili App Service uygulamalarını App Service planından kaldırın.

## <a name="how-do-i-schedule-a-webjob"></a>WebJob Nasıl yaparım? zamanlamak istiyor musunuz?

Cron ifadelerini kullanarak zamanlanmış bir WebJob oluşturabilirsiniz:

1. Bir Settings. job dosyası oluşturun.
2. Bu JSON dosyasında bir cron ifadesi kullanarak bir zamanlama özelliği ekleyin: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Zamanlanmış Web Işleri hakkında daha fazla bilgi için bkz. [cron ifadesi kullanarak zamanlanmış WebJob oluşturma](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>App Service uygulamam için Nasıl yaparım? sızma testi gerçekleştir mi?

Sızma testi gerçekleştirmek için [bir istek gönderebilirsiniz](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Traffic Manager kullanan bir App Service Web uygulaması için özel etki alanı adı Nasıl yaparım? yapılandırmak.

Yük Dengeleme için Azure Traffic Manager kullanan App Service bir uygulamayla özel bir etki alanı adı kullanmayı öğrenmek için bkz. [Traffic Manager ile bir Azure Web uygulaması için özel etki alanı adı yapılandırma](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>App Service sertifikam sahtekarlık için işaretlendi. Nasıl yaparım? bunu çözmek mi istiyorsunuz?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir App Service sertifikası satın alma sırasında etki alanı doğrulaması sırasında aşağıdaki iletiyi görebilirsiniz:

"Sertifikanız olası sahtekarlık için işaretlendi. İstek şu anda gözden geçirme aşamasındadır. Sertifika 24 saat içinde kullanılabilir duruma gelmezse lütfen Azure desteğine başvurun. "

İletinin gösterdiği gibi, bu sahtekarlık doğrulama işleminin tamamlanması 24 saate kadar sürebilir. Bu süre boyunca iletiyi görmeye devam edersiniz.

App Service sertifikanız bu iletiyi 24 saat sonra göstermeye devam ederse, lütfen aşağıdaki PowerShell betiğini çalıştırın. Betik, sorunu çözmek için doğrudan [sertifika sağlayıcısıyla](https://www.godaddy.com/) iletişim kurar.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>App Service kimlik doğrulaması ve yetkilendirme nasıl çalışır?

App Service kimlik doğrulaması ve yetkilendirmeyle ilgili ayrıntılı belgeler için bkz. çeşitli sağlayıcı oturum açma işlemleri için docs:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft Hesabı](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Nasıl yaparım? varsayılan *. azurewebsites.net etki alanını Azure Web uygulamamın özel etki alanına yeniden yönlendir mi?

Azure 'da Web Apps kullanarak yeni bir Web sitesi oluşturduğunuzda, sitenize varsayılan bir *SiteName*. azurewebsites.net etki alanı atanır. Sitenize özel bir ana bilgisayar adı ekler ve kullanıcıların varsayılan *. azurewebsites.net etki alanına erişmesini istemiyorsanız varsayılan URL 'YI yeniden yönlendirebilirsiniz. Web sitenizin varsayılan etki alanındaki tüm trafiği özel etki alanına yeniden yönlendirmeyi öğrenmek için bkz. [varsayılan etki alanını Azure Web Apps 'te özel etki alanına yönlendirme](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>App Service hangi .NET sürümü sürümünün yüklendiğini Nasıl yaparım?.

App Service yüklenen Microsoft .NET sürümünü bulmanın en hızlı yolu, kudu konsolunu kullanmaktır. Kudu konsoluna portaldan veya App Service uygulamanızın URL 'sini kullanarak erişebilirsiniz. Ayrıntılı yönergeler için bkz. [App Service yüklü .NET sürümünü belirleme](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Otomatik ölçeklendirme beklenen şekilde çalışmıyor mu?

Azure otomatik ölçeklendirme, Web uygulaması örneğinin içinde ölçeklendirilmemiş veya ölçeklendirildiği şekilde ölçeklendirmemişse, "Flama" nedeniyle sonsuz bir döngüden kaçınmak üzere kasıtlı olarak ölçeklendirmemek üzere seçtiğimiz bir senaryoya çalışıyor olabilirsiniz. Bu genellikle, ölçek genişletme ve ölçek genişletme eşikleri arasında yeterli bir kenar boşluğu olmadığında gerçekleşir. "Flatma" ve diğer otomatik ölçeklendirme en iyi uygulamaları hakkında bilgi edinmek için bkz. [Otomatik ölçeklendirme en iyi yöntemleri](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Otomatik ölçeklendirme neden bazen kısmen ölçeklenmelidir?

Ölçümler önceden yapılandırılmış sınırları aştığında otomatik ölçeklendirme tetiklenir. Bazen kapasitenin yalnızca beklendiklerinize kıyasla kısmen doldurulduğunu fark edebilirsiniz. Bu durum, istediğiniz örnek sayısı kullanılabilir olmadığında ortaya çıkabilir. Bu senaryoda, otomatik ölçeklendirme, kullanılabilir örnek sayısı ile kısmen doldurulur. Daha sonra daha fazla kapasite sağlamak için otomatik ölçeklendirme mantığını çalıştırır. Kalan örnekleri ayırır. Bu işlem birkaç dakika sürebilir.

Birkaç dakika sonra beklenen sayıda örnek görmüyorsanız, bu durum, ölçümleri sınırlar içinde getirmek için kısmi dolum için yeterince olması olabilir. Ya da, daha düşük ölçüm sınırına ulaştığından otomatik ölçeklendirme ölçeği ölçeklenmeyebilir.

Bu koşulların hiçbiri geçerli değilse ve sorun devam ederse, bir destek isteği gönderebilirsiniz.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>İçeriğimde HTTP sıkıştırmasını açmak Nasıl yaparım? mı?

Hem statik hem de dinamik içerik türleri için sıkıştırmayı açmak üzere, uygulama düzeyi Web. config dosyasına aşağıdaki kodu ekleyin:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Sıkıştırmak istediğiniz belirli dinamik ve statik MIME türlerini de belirtebilirsiniz. Daha fazla bilgi için bkz. [basit bir Azure Web sitesinde httpCompression ayarlarındaki](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)bir forum sorusuna yanıt verme.

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Şirket içi ortamdan App Service geçiş Nasıl yaparım? istiyor musunuz?

Siteleri Windows ve Linux Web sunucularından App Service geçirmek için, Azure App Service Geçiş Yardımcısı ' nı kullanabilirsiniz. Geçiş Aracı gerektiğinde Azure 'da Web uygulamaları ve veritabanları oluşturur ve sonra içeriği yayımlar. Daha fazla bilgi için bkz. [Azure App Service Geçiş Yardımcısı](https://www.migratetoazure.net/).
