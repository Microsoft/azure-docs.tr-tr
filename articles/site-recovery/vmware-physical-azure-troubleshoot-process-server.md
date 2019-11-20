---
title: Azure Site Recovery işlem sunucusu sorunlarını giderme
description: Bu makalede Azure Site Recovery işlem sunucusu ile ilgili sorunların nasıl giderileceği açıklanmaktadır
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813416"
---
# <a name="troubleshoot-the-process-server"></a>İşlem sunucusunda sorun giderme

[Site Recovery](site-recovery-overview.md) işlem sunucusu, şirket Içi VMware VM 'leri ve fiziksel sunucular için Azure 'da olağanüstü durum kurtarmayı ayarlarken kullanılır. Bu makalede, çoğaltma ve bağlantı sorunları da dahil olmak üzere işlem sunucusu ile ilgili sorunların nasıl giderileceği açıklanmaktadır.

İşlem sunucusu hakkında [daha fazla bilgi edinin](vmware-physical-azure-config-process-server-overview.md) .

## <a name="before-you-start"></a>Başlamadan önce

Sorun gidermeye başlamadan önce:

1. [İşlem sunucularını nasıl izleyeceğinizi](vmware-physical-azure-monitor-process-server.md)anladığınızdan emin olun.
2. Aşağıdaki en iyi uygulamaları gözden geçirin.
3. [Kapasite konularını](site-recovery-plan-capacity-vmware.md#capacity-considerations)izlediğinizden emin olun ve [yapılandırma sunucusu](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) veya [tek başına işlem sunucuları](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)için boyutlandırma kılavuzunu kullanın.

## <a name="best-practices-for-process-server-deployment"></a>İşlem sunucusu dağıtımı için en iyi yöntemler

İşlem sunucularının en iyi performansı için, çok sayıda genel en iyi uygulama özetlenmektedir.

**En iyi uygulama** | **Ayrıntılar**
--- |---
**Kullanım** | Yapılandırma sunucusu/tek başına işlem sunucusunun yalnızca amaçlanan amaç için kullanıldığından emin olun. Makinede başka bir şey çalıştırmayın.
**IP adresi** | İşlem sunucusunun statik bir IPv4 adresi olduğundan ve NAT 'nin yapılandırılmadığından emin olun.
**Belleği/CPU kullanımını denetleme** |CPU ve bellek kullanımını% 70 altında tutun.
**Boş alan sağlayın** | Boş alan, işlem sunucusundaki önbellek disk alanını gösterir. Çoğaltma verileri Azure 'a yüklenmeden önce önbellekte depolanır.<br/><br/> Boş alanı% 25 üzerinde tutun. % 20 ' nin altına gittiğinde işlem sunucusuyla ilişkili çoğaltılan makineler için çoğaltma kısıtlanacaktır.

## <a name="check-process-server-health"></a>İşlem sunucusu sistem durumunu denetle

Sorun gidermenin ilk adımı, işlem sunucusunun sistem durumunu ve durumunu denetleyemiyoruz. Bunu yapmak için tüm uyarıları gözden geçirin, gerekli hizmetlerin çalıştığından emin olun ve işlem sunucusundan bir sinyal olduğunu doğrulayın. Bu adımlar, adımları gerçekleştirmenize yardımcı olmak için aşağıdaki grafikte özetlenmektedir.

![İşlem sunucusu sistem durumu sorunlarını giderme](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1\. adım: İşlem sunucusu sistem durumu uyarıları sorunlarını giderme

İşlem sunucusu bir dizi sistem durumu uyarısı oluşturur. Bu uyarılar ve önerilen eylemler aşağıdaki tabloda özetlenmiştir.

**Uyarı türü** | **Hata:** | **Sorun giderme**
--- | --- | --- 
![Sorunsuz][green] | Yok.  | İşlem sunucusu bağlandı ve sağlıklı.
![Uyarı][yellow] | Belirtilen hizmetler çalışmıyor. | 1. Hizmetlerin çalışıp çalışmadığını denetleyin.<br/> 2. Hizmetler beklendiği gibi çalışıyorsa, [bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.
![Uyarı][yellow]  | Son 15 dakika boyunca% 80 > CPU kullanımı. | 1. Yeni makine eklemeyin.<br/>2. İşlem sunucusunu kullanan VM sayısının [tanımlı sınırlara](site-recovery-plan-capacity-vmware.md#capacity-considerations)göre hizalanacağını kontrol edin ve [ek bir işlem sunucusu](vmware-azure-set-up-process-server-scale.md)ayarlamayı deneyin.<br/>3. [Bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.
![Kritik][red] |  Son 15 dakika boyunca% 95 > CPU kullanımı. | 1. Yeni makine eklemeyin.<br/>2. İşlem sunucusunu kullanan VM sayısının [tanımlı sınırlara](site-recovery-plan-capacity-vmware.md#capacity-considerations)göre hizalanacağını kontrol edin ve [ek bir işlem sunucusu](vmware-azure-set-up-process-server-scale.md)ayarlamayı deneyin.<br/>3. [Bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.<br/> 4. Sorun devam ederse, VMware/fiziksel sunucu çoğaltması için [dağıtım planlayıcısı](https://aka.ms/asr-v2a-deployment-planner) çalıştırın.
![Uyarı][yellow] | Son 15 dakika boyunca bellek kullanımı >% 80. |  1. Yeni makine eklemeyin.<br/>2. İşlem sunucusunu kullanan VM sayısının [tanımlı sınırlara](site-recovery-plan-capacity-vmware.md#capacity-considerations)göre hizalanacağını kontrol edin ve [ek bir işlem sunucusu](vmware-azure-set-up-process-server-scale.md)ayarlamayı deneyin.<br/>3. Uyarıyla ilişkili yönergeleri izleyin.<br/> 4. Sorun devam ederse, [bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.
![Kritik][red] | Son 15 dakika boyunca bellek kullanımı >% 95. | 1. Yeni makineler eklemeyin ve [ek bir işlem sunucusu](vmware-azure-set-up-process-server-scale.md)ayarlamayı düşünün.<br/> 2. Uyarıyla ilişkili yönergeleri izleyin.<br/> 3.4. Sorun devam ederse, [bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.<br/> 4. Sorun devam ederse, VMware/fiziksel sunucu çoğaltma sorunları için [dağıtım planlayıcısı](https://aka.ms/asr-v2a-deployment-planner) çalıştırın.
![Uyarı][yellow] | Son 15 dakika boyunca% 30 < önbellek klasörü boş alanı. | 1. Yeni makineler eklemeyin ve [ek bir işlem sunucusu](vmware-azure-set-up-process-server-scale.md)ayarlamayı düşünün.<br/>2. İşlem sunucusunu kullanan VM sayısının [yönergelere](site-recovery-plan-capacity-vmware.md#capacity-considerations)göre hizalandığını denetleyin.<br/> 3. [Bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.
![Kritik][red] |  Son 15 dakika için < boş alan% 25 | 1. Bu sorun için uyarıyla ilişkili yönergeleri izleyin.<br/> 2.3. [Bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.<br/> 3. Sorun devam ederse, VMware/fiziksel sunucu çoğaltması için [dağıtım planlayıcısı](https://aka.ms/asr-v2a-deployment-planner) çalıştırın.
![Kritik][red] | 15 dakika veya daha fazlası için işlem sunucusundan sinyal yok. Tmansvs hizmeti yapılandırma sunucusuyla iletişim kurmuyor. | 1) işlem sunucusunun çalışır ve çalışıyor olduğunu denetleyin.<br/> 2. Tmassvc 'in işlem sunucusunda çalışıp çalışmadığını denetleyin.<br/> 3. [Bağlantı ve çoğaltma sorunlarını gidermek](#check-connectivity-and-replication)için aşağıdaki yönergeleri izleyin.


![Tablo anahtarı](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2\. adım: İşlem sunucusu hizmetlerini denetleyin

İşlem sunucusunda çalışması gereken hizmetler aşağıdaki tabloda özetlenmiştir. Hizmetler 'de, işlem sunucusunun nasıl dağıtıldığına bağlı olarak küçük farklılıklar vardır. 

Microsoft Azure Kurtarma Hizmetleri Aracısı (obengine) dışındaki tüm hizmetlerde, StartType 'ın **Otomatik** veya **Otomatik (Gecikmeli başlatma)** olarak ayarlandığından emin olun.
 
**Dağıtım** | **Çalışan hizmetler**
--- | ---
**Yapılandırma sunucusunda işlem sunucusu** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage Pushınstall; Günlük karşıya yükleme hizmeti (LogUpload); InMage Scout uygulama hizmeti; Microsoft Azure Kurtarma Hizmetleri Aracısı (obengine); InMage Scout, aracı-Sentinel/Outpost (svagents); tmansvc World Wide Web yayımlama hizmeti (W3SVC); MySQL Microsoft Azure Site Recovery hizmeti (DRA)
**Tek başına sunucu olarak çalışan işlem sunucusu** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage Pushınstall; Günlük karşıya yükleme hizmeti (LogUpload); InMage Scout uygulama hizmeti; Microsoft Azure Kurtarma Hizmetleri Aracısı (obengine); InMage Scout, aracı-Sentinel/Outpost (svagents); tmansvc.
**Azure 'da yeniden çalışma için dağıtılan işlem sunucusu** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage Pushınstall; Günlük karşıya yükleme hizmeti (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3\. adım: İşlem sunucusu sinyalini denetleyin

İşlem sunucusundan sinyal yoksa (hata kodu 806) şunları yapın:

1. İşlem sunucusu VM 'sinin çalışır ve çalışır olduğunu doğrulayın.
2. Bu günlüklere hata olup olmadığını denetleyin.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log C\ProgramData\ASR\home\svsystems\monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Bağlantıyı ve çoğaltmayı denetleme

 İlk ve devam eden çoğaltma hataları genellikle kaynak makineler ile işlem sunucusu arasındaki bağlantı sorunlarından veya işlem sunucusu ile Azure arasında oluşur. Bu adımlar, adımları gerçekleştirmenize yardımcı olmak için aşağıdaki grafikte özetlenmektedir.

![Bağlantı ve çoğaltma sorunlarını giderme](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4\. Adım: Kaynak makinedeki zaman eşitlemesini doğrula

Çoğaltılan makinenin sistem tarihinin/saatinin eşitlenmiş olduğundan emin olun. [Daha fazla bilgi edinin](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5\. Adım: Kaynak makinedeki Anti-Virus yazılımını denetle

Çoğaltılan makinede virüsten koruma yazılımının Site Recovery engellemediğinden emin olun. Virüsten koruma programlarından Site Recovery dışlayamazsınız, [Bu makaleyi](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)gözden geçirin.

## <a name="step-6-check-connectivity-from-source-machine"></a>6\. Adım: Kaynak makineden bağlantıyı denetle


1. Gerekirse, [Telnet istemcisini](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) kaynak makineye yükleyebilirsiniz. Ping kullanmayın.
2. Kaynak makineden, HTTPS bağlantı noktasındaki işlem sunucusuna Telnet ile ping gönderin. Varsayılan olarak 9443, çoğaltma trafiği için HTTPS bağlantı noktasıdır.

    `telnet <process server IP address> <port>`

3. Bağlantının başarılı olup olmadığını doğrulayın.


**Bağlantı** | **Ayrıntılar** | **Eylem**
--- | --- | ---
**Başarısız** | Telnet boş bir ekran gösterir ve işlem sunucusuna erişilebilir. | Başka eylem gerekmiyor.
**Dır** | Bağlanamazsınız | İşlem sunucusunda 9443 numaralı bağlantı noktasına izin verildiğinden emin olun. Örneğin, bir çevre ağınız veya bir denetimli alt ağınız varsa. Bağlantıyı yeniden denetleyin.
**Kısmen başarılı** | Bağlanabilirsiniz, ancak kaynak makine işlem sunucusuna ulaşılamadığından rapor verebilir. | Bir sonraki sorun giderme yordamıyla devam edin.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7\. Adım: Erişilemeyen bir işlem sunucusunda sorun giderme

İşlem sunucusuna kaynak makineden erişilemiyorsa, 78186 hatası görüntülenir. Bu sorun giderilmemişse, hem uygulamayla tutarlı hem de çökme ile tutarlı kurtarma noktalarının beklendiği gibi üretilmemesine neden olur.

Kaynak makinenin işlem sunucusunun IP adresine erişip erişemeyeceğini denetleyerek sorun giderin ve uçtan uca bağlantıyı denetlemek için kaynak makinede cxpsclient aracını çalıştırın.


### <a name="check-the-ip-connection-on-the-process-server"></a>İşlem sunucusundaki IP bağlantısını denetleyin

Telnet başarılıysa, ancak kaynak makine işlem sunucusuna ulaşılamadığından, işlem sunucusunun IP adresine erişip erişemeyeceğini kontrol edin.

1. Bir Web tarayıcısında, https://< PS_IP >: < PS_Data_Port >/IP adresine erişmeyi deneyin.
2. Bu denetim, bir HTTPS sertifika hatası gösteriyorsa, bu normaldir. Hatayı yoksayarsanız, 400 hatalı bir Istek görmeniz gerekir. Bu, sunucu tarayıcı isteğine hizmeti veremeyeceği ve standart HTTPS bağlantısının iyi durumda olmadığı anlamına gelir.
3. Bu denetim çalışmazsa, tarayıcı hata iletisine göz atın. Örneğin, bir 407 hatası proxy kimlik doğrulamasıyla ilgili bir sorun olduğunu gösterir.

### <a name="check-the-connection-with-cxpsclient"></a>Cxpsclient ile bağlantıyı denetleme

Ayrıca, uçtan uca bağlantıyı denetlemek için cxpsclient aracını çalıştırabilirsiniz.

1. Aracı aşağıdaki gibi çalıştırın:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. İşlem sunucusunda, şu klasörlerdeki oluşturulan günlükleri kontrol edin:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Karşıya yükleme hataları için kaynak VM günlüklerine bakın (hata 78028)

Kaynak makinelerden işlem hizmetine engellenen veri karşıya yüklemelerle ilgili sorun, kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarının üretilmesiyle sonuçlanabilir. 

1. Ağ yükleme hatalarını gidermek için, bu günlükteki hataları arayabilirsiniz:

    C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents *. log 

2. Bu makaledeki yordamın geri kalanını kullanın, veri yükleme sorunlarını çözmenize yardımcı olabilir.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8\. Adım: İşlem sunucusunun verileri iletme işlemini denetleyin

İşlem sunucusunun, verileri Azure 'a etkin bir şekilde iletme konusunda kontrol edin.

  1. İşlem sunucusunda, Görev Yöneticisi 'ni açın (Ctrl + Shift + ESC tuşlarına basın).
  2. **Kaynak İzleyicisi açmak**> **performans** sekmesini seçin.
  3. **Kaynak İzleyicisi** sayfasında **ağ** sekmesini seçin. **Ağ etkinliği olan süreçler**' ın altında, cbengine. exe ' nin büyük hacimli verileri etkin bir şekilde gönderip göndermediğini denetleyin.

       ![Ağ etkinliği ile işlem kapsamındaki birimler](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Cbengine. exe büyük miktarda veri göndermemişse, aşağıdaki bölümlerdeki adımları izleyin.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9\. Adım: İşlem sunucusu bağlantısını Azure Blob depolama ile denetleyin

1. Kaynak İzleyicisi, **cbengine. exe**' yi seçin.
2. **TCP bağlantıları**altında, Işlem sunucusundan Azure depolama 'ya bağlantı olup olmadığını denetleyin.

  ![Cbengine. exe ve Azure Blob depolama URL 'SI arasında bağlantı](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Hizmetleri denetle

İşlem sunucusundan Azure Blob depolama URL 'sine bağlantı yoksa, hizmetlerin çalışıp çalışmadığını denetleyin.

1. Denetim Masası 'nda **Hizmetler**' i seçin.
2. Aşağıdaki hizmetlerin çalıştığını doğrulayın:

    - cxprocessserver
    - InMage Scout, aracı – Sentinel/Outpost
    - Microsoft Azure Kurtarma Hizmetleri Aracısı
    - Microsoft Azure Site Recovery Hizmeti
    - tmansvc

3. Çalışmayan herhangi bir hizmeti başlatın veya yeniden başlatın.
4. İşlem sunucusunun bağlı ve erişilebilir olduğunu doğrulayın. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10. Adım: işlem sunucusu bağlantısını Azure genel IP adresi ile denetleyin

1. İşlem sunucusunda, **%ProgramFiles%\Microsoft Azure Recovery Services fılestemp**bölümünde en son CBEngineCurr. errlog dosyasını açın.
2. Dosyada, **443**için arama yapın veya dize **bağlantısı girişimi başarısız oldu**.

  ![Geçici klasörde hata günlükleri](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Sorun görürseniz, 443 numaralı bağlantı noktasını kullanarak Azure genel IP adresinizi CBEngineCurr. currLog dosyasında bulabilirsiniz:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. İşlem sunucusundaki komut satırında, Azure genel IP adresinizi ping etmek için Telnet ' i kullanın.
6. Bağlanamıyorsanız, sonraki yordamı izleyin.

## <a name="step-11-check-process-server-firewall-settings"></a>Adım 11: İşlem sunucusu güvenlik duvarı ayarlarını kontrol edin. 

İşlem sunucusundaki IP adresi tabanlı güvenlik duvarının erişimi engelleyip engellemediğini denetleyin.

1. IP adresi tabanlı güvenlik duvarı kuralları için:

    a) [Microsoft Azure veri MERKEZI IP aralıklarının](https://www.microsoft.com/download/details.aspx?id=41653)tüm listesini indirin.

    b) güvenlik duvarının Azure (ve varsayılan HTTPS bağlantı noktası, 443) iletişimine izin verdiğinden emin olmak için, IP adresi aralıklarını Güvenlik Duvarı yapılandırmanıza ekleyin.

    c) aboneliğinizin Azure bölgesi ve Azure Batı ABD bölgesi için IP adresi aralıklarına Izin verin (erişim denetimi ve kimlik yönetimi için kullanılır).

2. URL tabanlı güvenlik duvarları için aşağıdaki tabloda listelenen URL 'Leri Güvenlik Duvarı yapılandırmasına ekleyin.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12. Adım: İşlem sunucusu proxy ayarlarını doğrulama 

1. Proxy sunucusu kullanıyorsanız, proxy sunucusu adının DNS sunucusu tarafından çözümlendiğinden emin olun. Configuration Server 'ı **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure site Recovery\ProxySettings**kayıt defteri anahtarında ayarlarken verdiğiniz değeri denetleyin.
2. Verileri göndermek için Azure Site Recovery Aracısı tarafından aynı ayarların kullanıldığından emin olun.

    a) **Microsoft Azure Backup**için arama yapın.

    b) **Microsoft Azure Backup**açın ve **eylem** > **Değiştir Özellikler**' i seçin.

    c) ara sunucu **yapılandırması** sekmesinde, proxy adresi, kayıt defteri ayarlarında gösterilen ara sunucu adresiyle aynı olmalıdır. Aksi takdirde, aynı adresle değiştirin.

## <a name="step-13-check-bandwidth"></a>13. Adım: Bant genişliğini denetle

İşlem sunucusu ile Azure arasındaki bant genişliğini artırın ve sorun olup olmadığını kontrol edin.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla yardıma ihtiyacınız varsa sorunuzu [Azure Site Recovery forumuna](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)gönderin. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
