---
title: Azure Site Recovery kullanarak, VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma sırasında yapılandırma sunucusuyla ilgili sorunları giderin | Microsoft Docs
description: Bu makalede, Azure Site Recovery kullanarak, VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için yapılandırma sunucusunu dağıtmaya ilişkin sorun giderme bilgileri sağlanmaktadır.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 25e2b488d3b6e7e5cabd1a71d1489efaf01231b3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748543"
---
# <a name="troubleshoot-configuration-server-issues"></a>Yapılandırma sunucusu sorunlarını giderme

Bu makale, [Azure Site Recovery](site-recovery-overview.md) yapılandırma sunucusunu dağıtırken ve yönetirken sorunları gidermenize yardımcı olur. Yapılandırma sunucusu bir yönetim sunucusu işlevi görür. Site Recovery kullanarak şirket içi VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma ayarlamak için yapılandırma sunucusunu kullanın. Aşağıdaki bölümlerde, yeni bir yapılandırma sunucusu eklediğinizde ve bir yapılandırma sunucusunu yönetirken karşılaşabileceğiniz en yaygın sorunlar ele alınmaktadır.

## <a name="registration-failures"></a>Kayıt hataları

Mobility aracısını yüklediğinizde kaynak makine, yapılandırma sunucusuna kaydolur. Bu yönergeleri izleyerek bu adım sırasında hata ayıklaması yapabilirsiniz:

1. C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log dosyasını açın. (ProgramData klasörü gizli bir klasör olabilir. ProgramData klasörünü görmüyorsanız, dosya Gezgini 'nde, **Görünüm** sekmesindeki **Göster/Gizle** bölümünde **gizli öğeler** onay kutusunu seçin.) Hatalara birden çok sorun neden olmuş olabilir.

2. **Geçerli BIR IP adresi bulunamadı**dizesini arayın. Dize bulunursa:
   1. İstenen konak KIMLIĞININ, kaynak makinenin ana bilgisayar KIMLIĞIYLE aynı olduğunu doğrulayın.
   2. Kaynak makinenin fiziksel NIC 'ye atanmış en az bir IP adresi olduğunu doğrulayın. Yapılandırma sunucusunun başarılı olması için aracı kaydı için, kaynak makinenin fiziksel NIC 'ye atanmış en az bir geçerli IP v4 adresi olması gerekir.
   3. Kaynak makinenin tüm IP adreslerini almak için kaynak makinede aşağıdaki komutlardan birini çalıştırın:
      - Windows için: `> ipconfig /all`
      - Linux için: `# ifconfig -a`

3. Dize geçerli bir **IP adresi bulunamadıysa** , string **= > null**dizesini arayın. Bu hata, kaynak makine yapılandırma sunucusuna kaydolmak için boş bir konak kullanıyorsa oluşur. Dize bulunursa:
    - Sorunları çözdükten sonra, kaydı el ile yeniden denemek için [kaynak makineyi yapılandırma sunucusuna kaydetme](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) bölümündeki yönergeleri izleyin.

4. Dize **nedeni = > null** bulunamazsa, kaynak makinede C:\programdata\asrsetuplogs\uploadedlogs\asrunifiedagentınstaller.log dosyasını açın. (ProgramData klasörü gizli bir klasör olabilir. ProgramData klasörünü görmüyorsanız, dosya Gezgini 'nde, **Görünüm** sekmesindeki **Göster/Gizle** bölümünde **gizli öğeler** onay kutusunu seçin.) Hatalara birden çok sorun neden olmuş olabilir. 

5. Dize **POST isteğini arayın: (7)-sunucuya bağlanılamadı**. Dize bulunursa:
    1. Kaynak makine ile yapılandırma sunucusu arasındaki ağ sorunlarını çözün. Yapılandırma sunucusuna, ping, izleme yolu veya bir Web tarayıcısı gibi ağ araçlarını kullanarak kaynak makineden erişilebildiğini doğrulayın. Kaynak makinenin, 443 bağlantı noktası üzerinden yapılandırma sunucusuna erişebildiğinden emin olun.
    2. Kaynak makinedeki herhangi bir Güvenlik Duvarı kurallarının, kaynak makine ile yapılandırma sunucusu arasındaki bağlantıyı engellemediğinden emin olun. Bağlantı sorunlarının engelini kaldırmak için ağ yöneticilerle çalışın.
    3. [Virüsten koruma programlarından Site Recovery klasör dışlamaları](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) ' nda listelenen klasörlerin virüsten koruma yazılımından çıkarıldığından emin olun.
    4. Ağ sorunları çözümlendiğinde, [kaynak makineyi yapılandırma sunucusuna kaydetme](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)konusundaki yönergeleri izleyerek kaydı yeniden deneyin.

6. Dize **POST isteği: (7)-sunucuya bağlanamadıysa** , aynı günlük dosyasında, dize **isteğini arayın: (60)-belirtilen CA sertifikalarıyla eş sertifika kimliği**doğrulanamıyor. Bu hata, yapılandırma sunucusu sertifikasının süresi dolduğunda veya kaynak makine TLS 1,0 veya üzeri SSL protokollerini desteklemediğinden meydana gelebilir. Ayrıca, bir güvenlik duvarı, kaynak makine ile yapılandırma sunucusu arasında SSL iletişimini engelliyorsa de oluşabilir. Dize bulunursa: 
    1. Çözümlemek için, kaynak makinede bir Web tarayıcısı kullanarak yapılandırma sunucusu IP adresine bağlanın. URI https:\/\/< yapılandırma sunucusu IP adresi\>: 443/) kullanın. Kaynak makinenin, 443 bağlantı noktası üzerinden yapılandırma sunucusuna erişebildiğinden emin olun.
    2. Kaynak makinenin yapılandırma sunucusuyla iletişim kurmasını sağlamak için kaynak makinede herhangi bir güvenlik duvarı kuralı eklenmesi veya kaldırılması gerekip gerekmediğini denetleyin. Kullanımda olabilecek çeşitli güvenlik duvarı yazılımları nedeniyle, tüm gerekli güvenlik duvarı yapılandırmalarının listesini listeleyemiyorum. Bağlantı sorunlarının engelini kaldırmak için ağ yöneticilerle çalışın.
    3. [Virüsten koruma programlarından Site Recovery klasör dışlamaları](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) ' nda listelenen klasörlerin virüsten koruma yazılımından çıkarıldığından emin olun.  
    4. Sorunları çözdükten sonra, [kaynak makineyi yapılandırma sunucusuna kaydetme](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)bölümündeki yönergeleri izleyerek kaydı yeniden deneyin.

7. Linux 'ta < INSTALLATION_DIR platform değeri\>/etc/drscout.exe bozuksa, kayıt başarısız olur. Bu sorunu belirlemek için/var/log/ua_install. log dosyasını açın. **VM_PLATFORM değeri null olduğu veya VMware/Azure olmadığından, yapılandırmayı iptal**eden dizeyi arayın. Platform **VMware** ya da **Azure**olarak ayarlanmalıdır. Drscout. conf dosyası bozuksa, [Mobility aracısını kaldırmanızı](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) ve ardından Mobility aracısını yeniden yüklemenizi öneririz. Kaldırma başarısız olursa, aşağıdaki adımları izleyin: a. Installation_Directory/Uninstall.exe dosyasını açın ve **StopServices** işlevine olan çağrıyı not edin.
    b. Installation_Directory/VX/bin/Uninstall.sh dosyasını açın ve **stop_services** işlevi çağrısını not edin.
    c. Installation_Directory/FX/Uninstall.exe dosyasını açın ve FX hizmetini durdurmaya çalışan tüm bölümün açıklamasını inceleyin.
    d. Mobility aracısını [kaldırın](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) . Başarıyla kaldırıldıktan sonra sistemi yeniden başlatın ve ardından Mobility aracısını yeniden yüklemeyi deneyin.

## <a name="installation-failure-failed-to-load-accounts"></a>Yükleme hatası: hesaplar yüklenemedi

Bu hata, hizmet, Mobility aracısını yüklerken ve yapılandırma sunucusuna kaydolurken aktarım bağlantısından verileri okuyamediğinde oluşur. Sorunu çözmek için, kaynak makinenizde TLS 1,0 ' nin etkinleştirildiğinden emin olun.

## <a name="vcenter-discovery-failures"></a>vCenter bulma sorunları

VCenter bulma başarısızlıklarını çözümlemek için, vCenter Server 'ı atlama listesi proxy 'si ayarlarına ekleyin. 

- Sistem Kullanıcı içeriğine erişmek için PsExec aracını [buradan](https://aka.ms/PsExec) indirin.
- Aşağıdaki komut satırı PsExec-s-i "%ProgramFiles%\Internet Explorer\iexplore.exe" komut satırını çalıştırarak Internet Explorer 'ı sistem Kullanıcı içeriğinde açın
- IE 'de proxy ayarları ekleyin ve tmanssvc hizmetini yeniden başlatın.
- DRA proxy ayarlarını yapılandırmak için CD C:\Program Files\Microsoft Azure Site Recovery sağlayıcısı 'nı çalıştırın
- Sonra, DRCONFIGURATOR yürütün. EXE/configure/AddBypassUrls [yapılandırma **vCenter Server/vSphere ESXi sunucu** adımının [yapılandırma sunucusu dağıtımı](vmware-azure-deploy-configuration-server.md#configure-settings)adımı sırasında belirtilen vCenter Server IP adresi/FQDN 'si Ekle]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Yapılandırma sunucusunun IP adresini değiştirme

Bir yapılandırma sunucusunun IP adresini değiştirmemenizi önemle tavsiye ederiz. Yapılandırma sunucusuna atanan tüm IP adreslerinin statik IP adresleri olduğundan emin olun. DHCP IP adreslerini kullanmayın.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML belirteci geçersiz

Bu hatayı önlemek için, sistem saatinizdeki sürenin 15 dakikadan uzun bir süredir yerel saatten farklı olmamasını sağlayın. Kayıt işlemini tamamlamak için yükleyiciyi yeniden çalıştırın.

## <a name="failed-to-create-a-certificate"></a>Sertifika oluşturulamadı

Site Recovery kimlik doğrulaması için gereken bir sertifika oluşturulamıyor. Kurulumu yerel yönetici olarak çalıştırmayı doğruladıktan sonra kurulumu yeniden çalıştırın.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Sunucu standart DEĞERLENDIRMESINDEN sunucu standardına Windows lisansı etkinleştirilemedi

1. OVF aracılığıyla yapılandırma sunucusu dağıtımının bir parçası olarak, 180 gün için geçerli olan bir değerlendirme lisansı kullanılır. Bu lisansı, bu süre dolmadan önce etkinleştirmeniz gerekir. Aksi takdirde, bu, yapılandırma sunucusunun sıklıkla kapatılmasını sağlayabilir ve bu sayede hindrance çoğaltma etkinliklerine neden olur.
2. Windows lisansını etkinleştiremiyorsanız sorunu çözmek için [Windows destek ekibine](https://aka.ms/Windows_Support) ulaşın.

## <a name="register-source-machine-with-configuration-server"></a>Kaynak makineyi yapılandırma sunucusuna Kaydet

### <a name="if-the-source-machine-runs-windows"></a>Kaynak makine Windows çalıştırıyorsa

Kaynak makinede aşağıdaki komutu çalıştırın:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Ayar | Ayrıntılar
--- | ---
Kullanım | UnifiedAgentConfigurator. exe/CSEndPoint < yapılandırma sunucusu IP adresi\>/parola sefilepath < parola dosyası yolu\>
Aracı yapılandırma günlükleri | %ProgramData%\asrsetuplogs\asrunifiedagentconfigurator.log altında bulunur.
/CSEndPoint | Zorunlu parametre. Yapılandırma sunucusunun IP adresini belirtir. Herhangi bir geçerli IP adresi kullanın.
/PassphraseFilePath |  Zorunlu. Parolanın konumu. Geçerli bir UNC veya yerel dosya yolu kullanın.

### <a name="if-the-source-machine-runs-linux"></a>Kaynak makine Linux çalıştırıyorsa

Kaynak makinede aşağıdaki komutu çalıştırın:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Ayar | Ayrıntılar
--- | ---
Kullanım | CD/usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh-ı < yapılandırma sunucusu IP adresi\>-P < parola dosyası yolu\>
-ı | Zorunlu parametre. Yapılandırma sunucusunun IP adresini belirtir. Herhangi bir geçerli IP adresi kullanın.
-P |  Zorunlu. Parolasının kaydedildiği dosyanın tam dosya yolu. Geçerli bir klasör kullanın.

## <a name="unable-to-configure-the-configuration-server"></a>Yapılandırma sunucusu yapılandırılamıyor

Sanal makinedeki yapılandırma sunucusundan farklı uygulamalar yüklüyorsanız, ana hedefi yapılandıramayabilir. 

Yapılandırma sunucusunun tek bir amaç sunucusu olması ve paylaşılan sunucu olarak kullanılması desteklenmez. 

Daha fazla bilgi için [yapılandırma sunucusu dağıtma](vmware-azure-deploy-configuration-server.md#faqs)konusundaki SSS bölümüne bakın. 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Korunan öğeler için eski girdileri yapılandırma sunucusu veritabanından kaldır 

Yapılandırma sunucusundaki eski korumalı makineyi kaldırmak için aşağıdaki adımları kullanın. 
 
1. Eski girdinin kaynak makinesini ve IP adresini belirleme: 

    1. MYSQL komut satırı ' nı yönetici modunda açın. 
    2. Aşağıdaki komutları yürütün. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Bu, kayıtlı makinelerin listesini, IP adresleriyle ve son kupa ile birlikte döndürür. Eski çoğaltma çiftlerine sahip Konağı bulun.

2. Yükseltilmiş bir komut istemi açın ve C:\ProgramData\ASR\home\svsystems\bin. adresine gidin. 
4. Kayıtlı ana bilgisayar ayrıntılarını ve eski giriş bilgilerini yapılandırma sunucusundan kaldırmak için, kaynak makineyi ve eski girdinin IP adresini kullanarak aşağıdaki komutu çalıştırın. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    IP adresi 10.0.0.4 olan "Onpree-VM01" kaynak sunucu girdiniz varsa, bunun yerine aşağıdaki komutu kullanın.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Yapılandırma sunucusu ile yeniden kaydettirmek için kaynak makinede aşağıdaki hizmetleri yeniden başlatın. 
 
    - InMage Scout uygulama hizmeti
    - InMage Scout, aracı-Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Hizmetler duramazsa yükseltme başarısız olur

Yapılandırma sunucusu yükseltmesi, bazı hizmetler durdurulduğunda başarısız olur. 

Sorunu belirlemek için yapılandırma sunucusunda C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile adresine gidin. Aşağıdaki hataları bulursanız, sorunu gidermek için aşağıdaki adımları kullanın: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Sorunu çözmek için:

Aşağıdaki hizmetleri el ile durdurun:

- cxprocessserver
- InMage Scout, aracı – Sentinel/Outpost, 
- Microsoft Azure Kurtarma Hizmetleri Aracısı, 
- Microsoft Azure Site Recovery hizmeti, 
- tmansvc
  
Yapılandırma sunucusunu güncelleştirmek için [Birleşik kurulumu](service-updates-how-to.md#links-to-currently-supported-update-rollups) yeniden çalıştırın.

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory uygulama oluşturma hatası

[Açık Sanallaştırma uygulaması (OVA)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) şablonunu kullanarak Azure ACTIVE DIRECTORY (AAD) içinde bir uygulama oluşturmak için yeterli izniniz yok.

Sorunu çözmek için Azure portal oturum açın ve aşağıdakilerden birini yapın:

- AAD 'de uygulama geliştirici rolü isteyin. Uygulama geliştirici rolü hakkında daha fazla bilgi için [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)bölümüne bakın.
- **Kullanıcının uygulama bayrağını oluşturbildiğini** , AAD 'de *true* olarak ayarlandığını doğrulayın. Daha fazla bilgi için bkz. [nasıl yapılır: portalı kullanarak kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>İşlem sunucusu/ana hedef, yapılandırma sunucusu ile iletişim kuramıyor 

İşlem sunucusu (PS) ve ana hedef (MT) modülleri yapılandırma sunucusu (CS) ile iletişim kuramıyor ve durumları Azure portal bağlı değil olarak gösterilir.

Genellikle bu, bağlantı noktası 443 ' deki bir hata nedeniyle yapılır. Bağlantı noktasının engelini kaldırmak ve CS ile iletişimi yeniden etkinleştirmek için aşağıdaki adımları kullanın.

**MARS aracısının ana hedef aracı tarafından çağrıldığından emin olun**

Ana hedef aracısının yapılandırma sunucusu IP 'si için bir TCP oturumu oluşturup oluşturbildiğini doğrulamak için, ana hedef Aracısı günlüklerinde aşağıdakine benzer bir izleme arayın:

TCP \<IP 'yi CS IP ile değiştirin >: 52739 \<IP 'yi burada CS IP ile değiştirin >: 443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT//IP 'yi burada CS IP ile değiştirin

MT aracı günlüklerinde aşağıdakine benzer izlemeler bulursanız, MT Aracısı 443 numaralı bağlantı noktasında hata bildiriyor:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Bu hata, diğer uygulamalar bağlantı noktası 443 ' i kullanırken veya bağlantı noktasını engelleyen bir güvenlik duvarı ayarından dolayı bu hatayla karşılaşılabilir.

Sorunu çözmek için:

- 443 numaralı bağlantı noktasının güvenlik duvarınız tarafından engellenmediğinden emin olun.
- Bu bağlantı noktasını kullanan başka bir uygulama nedeniyle bağlantı noktasına erişilemezse, uygulamayı durdurun ve kaldırın.
  - Uygulamayı durdurmak uygun değilse, yeni bir temiz CS ayarlayın.
- Yapılandırma sunucusunu yeniden başlatın.
- IIS hizmetini yeniden başlatın.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Yapılandırma sunucusu yanlış UUID girdileri nedeniyle bağlı değil

Bu hata, veritabanında birden çok yapılandırma sunucusu (CS) örnek UUID girişi olduğunda meydana gelebilir. Sorun genellikle yapılandırma sunucusu VM 'sini kopyaladığınızda oluşur.

Sorunu çözmek için:

1. Eski/eski CS VM 'leri vCenter 'dan kaldırın. Daha fazla bilgi için bkz. [sunucuları kaldırma ve korumayı devre dışı bırakma](site-recovery-manage-registration-and-protection.md).
2. Yapılandırma sunucusu VM 'de oturum açın ve MySQL svsdb1 veritabanına bağlanın. 
3. Aşağıdaki sorguyu yürütün:

    > [!IMPORTANT]
    >
    > Kopyalanmış yapılandırma sunucusunun UUID ayrıntılarını veya artık sanal makineleri korumak için kullanılmayan yapılandırma sunucusunun eski girdisini girdiğinizi doğrulayın. Yanlış bir UUID girilmesi, var olan tüm korumalı öğelerin bilgilerini kaybetmeyle sonuçlanır.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Portal sayfasını yenileyin.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Kimlik bilgilerinizi girerken sonsuz oturum açma döngüsü oluşur

Yapılandırma sunucusu OVF 'ye doğru Kullanıcı adını ve parolayı girdikten sonra, Azure oturum açma doğru kimlik bilgilerini sormaya devam eder.

Bu sorun, sistem saati yanlış olduğunda meydana gelebilir.

Sorunu çözmek için:

Bilgisayarda doğru zamanı ayarlayın ve oturum açma işlemini yeniden deneyin. 
 