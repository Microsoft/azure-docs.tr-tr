---
title: Azure Data Factory içinde şirket içinde barındırılan tümleştirme çalışma zamanı sorunlarını giderme
description: Azure Data Factory ' de şirket içinde barındırılan tümleştirme çalışma zamanı sorunlarını giderme hakkında bilgi edinin.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907288"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı sorunlarını giderme

Bu makalede Azure Data Factory içindeki şirket içinde barındırılan tümleştirme çalışma zamanı için genel sorun giderme yöntemleri incelenmektedir.

## <a name="common-errors-and-resolutions"></a>Yaygın hatalar ve çözümler

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Hata iletisi: şirket içinde barındırılan tümleştirme çalışma zamanı bulut hizmetine bağlanamıyor.

- **Belirti**: 

    ![Şirket içinde barındırılan IR bağlantı sorunu](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Neden**: şirket içinde barındırılan tümleştirme çalışma zamanı, Data Factory hizmetine (arka uç) bağlanamıyor. Genellikle, güvenlik duvarında ağ ayarlarından kaynaklanmasından kaynaklanmıştır.

- **Çözüm**: 

    1. "Integration Runtime hizmeti" Windows hizmetinin çalışıp çalışmadığını denetleyin.
    
        ![Şirket içinde barındırılan IR hizmeti çalıştırma durumu](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. [1] içinde gösterilen Windows hizmeti çalışıyorsa, uygun şekilde aşağıdaki yönergeleri izleyin:

        1. Şirket içinde barındırılan tümleştirme çalışma zamanı 'nda "proxy" yapılandırılmamışsa (varsayılan ayarlar proxy yapılandırması değildir), şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu makinede aşağıdaki PowerShell komutunu çalıştırın: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > Hizmet URL 'SI, Veri Fabrikası konumunuza göre farklılık gösterebilir. Hizmet URL 'sini ADF Kullanıcı arabirimi-> Bağlantılar-> tümleştirme çalışma zamanları-> Şirket içinde barındırılan IR-> düğümlerini Düzenle-> görüntüleme hizmeti URL 'Lerini bulabilirsiniz.
            
            Beklenen yanıt aşağıda verilmiştir:
            
            ![PowerShell komut yanıtı](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Yanıt farklıysa, aşağıdaki yönergeleri uygun şekilde uygulayın:
            
            * "Uzak ad çözümlenemedi" hatasını alırsanız DNS ile ilgili bir sorun vardır. DNS çözümlemesi sorununu çözmek için lütfen ağ ekibine ulaşın! 
            * "SSL/TLS sertifikası güvenilir değil" hatasını alırsanız lütfen "https://wu2.frontend.clouddatahub.net/" sertifikasının makinede güvenilir olup olmadığını denetleyin, bu sorunu hafifletmek için Sertifika Yöneticisi 'ni kullanarak ortak sertifikayı yüklemeyin.
            * Windows-> Olay Görüntüleyicisi 'ni (Günlükler)-> uygulamalar ve hizmet günlükleri ' ni (genellikle DNS, güvenlik duvarı kuralı ve şirketin ağ ayarlarından kaynaklanan bir hata için Integration Runtime >) denetleyin. Bu sorun için, her şirkette ağ ayarları özelleştirildiği için lütfen daha fazla troubleshot için ağ ekibinize katılın.

        2. Şirket içinde barındırılan tümleştirme çalışma zamanı üzerinde "proxy" yapılandırılmışsa, proxy sunucunuzun hizmet uç noktanıza erişip erişemeyeceğini doğrulayın. Örnek bir komut için, [buna](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)bakın.    
                
            ```powershell
            $user = $env:username
            $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
            Settings').ProxyServer
            $pwd = Read-Host "Password?" -assecurestring
            $proxy = new-object System.Net.WebProxy
            $proxy.Address = $webproxy
            $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
            $proxy.credentials = $account
            $url = "https://wu2.frontend.clouddatahub.net/"
            $wc = new-object system.net.WebClient
            $wc.proxy = $proxy
            $webpage = $wc.DownloadData($url)
            $string = [System.Text.Encoding]::ASCII.GetString($webpage)
            $string
            ```

            Beklenen yanıt aşağıda verilmiştir:
            
            ![PowerShell komut yanıtı 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Ara sunucu konuları:
            > * Ara sunucunun beyaz listeye alma gerektirip gerektirmediğini denetleyin. Varsa, [Bu etki alanlarını](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) beyaz listeye ekleyin.
            > * "Wu2.frontend.clouddatahub.net/" için TLS/SSL sertifikası proxy sunucuda güvenilir ' i denetleyin.
            > * Proxy 'de Active Directory kimlik doğrulaması kullanıyorsanız, hizmet hesabını "Integration Runtime hizmeti" olarak proxy 'ye erişebilen kullanıcı hesabı olarak değiştirin.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Hata iletisi: şirket içinde barındırılan tümleştirme çalışma zamanı düğümü/mantıksal dolgu, etkin olmayan/"çalışıyor (sınırlı)" durumunda

- **Neden**: aşağıdaki ekran görüntüsünde gösterildiği gibi, şirket IÇINDE barındırılan IR düğümünü etkin olmayan durumda görebilirsiniz:

    ![Etkin olmayan şirket içinde barındırılan IR düğümü](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Düğümler birbirleriyle iletişim kuramadığında, bu durum oluşur. 

- **Çözüm**: 

    Düğüm barındırılan VM 'de oturum açın ve olay görünümü 'nü açın, uygulama ve hizmet günlükleri-> Integration Runtime altında tüm hata günlüklerini filtreleyin. 

     1. Hata günlüğü şunu içeriyorsa: 
    
        **Hata günlüğü**: System. ServiceModel. EndpointNotFoundException: net. TCP://xxxxxxx.bwld.com: 8060/externalservice. svc/workermanager ile bağlantı kurulamadı. 00:00:00.9940994 zaman aralığı için bağlantı denemesi. TCP hata kodu 10061: hedef makine etkin bir şekilde reddettiğinden bağlantı kurulamadı 10.2.4.10:8060.  ---> System .net. Sockets. SocketException: hedef makine etkin bir şekilde reddettiğinden bağlantı kurulamadı: 8060
    
           System .net. Sockets. Socket. DoConnect konumunda (EndPoint endPointSnapshot, SocketAddress socketAddress)
           
           System .net. Sockets. Socket. Connect (EndPoint remoteEP) konumunda
           
           System. ServiceModel. Channels. Socketconnectionınıtıator. Connect (Uri URI, TimeSpan zaman aşımı) konumunda
    
        **Çözüm:** komut satırını başlatın: telnet 10.2.4.10 8060
        
        Aşağıdaki hatayla karşılaşırsanız, bu sorunu gidermeye yönelik yardım için lütfen BT arkadaşlarınızla iletişime geçin. Başarılı bir şekilde Telnet 'i başardıktan sonra, IR düğümü durumu için sorun yaşıyorsanız Microsoft desteği 'ne başvurun.
        
        ![Komut satırı hatası](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Hata günlüğü şunu içeriyorsa:
     
        **Hata günlüğü:** Worker Manager 'a bağlanılamıyor: net. TCP:/8060/ExternalService. svc/Host azranlcir01r1 için DNS girişi yok. Böyle bir konak bilinen özel durum ayrıntısı değil: System. ServiceModel. EndpointNotFoundException: Ana bilgisayar xxxxx için DNS girişi yok. ---> System .net. Sockets. SocketException: Bu tür bir konak System .net. DNS. GetAddrInfo (dize adı) adresinde System .net. DNS. ınternalgethostbyname (dize ana bilgisayar adı, Boolean includeIPv6) adresinde bilinir. DNS. GetHostEntry (dize hostNameOrAddress ) System. ServiceModel. Channels. DnsCache. Resolve (Uri URI)---iç özel durum yığın izleme---sunucu yığın izlemesi: System. ServiceModel. Channels. DnsCache. Resolve (URI URI) konumunda 
    
        **Çözüm:** Aşağıdaki iki eylemden biri sorunu çözmeye yardımcı olabilir:
         1. Tüm düğümleri aynı etki alanına yerleştirin.
         2. Barındırılan tüm VM 'nin ana bilgisayar dosyasındaki konak eşlemesine IP ekleyin.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forumu](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
*  [ADF eşleme veri akışları performans Kılavuzu](concepts-data-flow-performance.md)
