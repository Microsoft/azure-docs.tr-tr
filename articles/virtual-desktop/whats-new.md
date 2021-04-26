---
title: Windows sanal masaüstündeki yenilikler nelerdir? - Azure
description: Windows sanal masaüstü için yeni özellikler ve ürün güncelleştirmeleri.
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 2712115f19c7cc64a0475061e134d6be6de5d1ca
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867412"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows sanal masaüstündeki yenilikler nelerdir?

Windows sanal masaüstü güncelleştirmeleri düzenli olarak güncelleştirilir. Bu makalede hakkında bilgi edinebilirsiniz:

- En son güncelleştirmeler
- Yeni özellikler
- Mevcut özelliklerde iyileştirmeler
- Hata düzeltmeleri

Bu makale aylık olarak güncelleştirilir. Yeni güncelleştirmeleri kullanmaya devam etmek için bu adımları en sık kontrol ettiğinizden emin olun.

## <a name="client-updates"></a>İstemci güncelleştirmeleri

Windows sanal masaüstü ve Uzak Masaüstü Hizmetleri istemcilerimize yönelik güncelleştirmeler hakkında bilgi edinmek için şu makalelere göz atın:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Windows sanal masaüstü Aracısı güncelleştirmeleri

Windows sanal masaüstü Aracısı ayda en az bir kez güncelleştirilir.

Windows sanal masaüstü aracısında şu şekilde değişiklik yapılır:

- Sürüm 1.0.2990.800: Bu güncelleştirme 13 2021 Nisan tarihinde yayımlanmıştır ve aşağıdaki değişikliklere sahiptir:
    - Aracı hata iletileri güncelleştirildi.
    - Windows 7 VM 'lerine Windows 7 olmayan aracıların yüklenmesini önleyen bir özel durum ekler.
    - Sinyal hizmeti mantığını güncelleştirmiş.
- Sürüm 1.0.2944.1400: Bu güncelleştirme 7 Nisan 2021 ' de yayımlanmıştır ve aşağıdaki değişikliklere sahiptir:
    - Windows sanal masaüstü Aracısı sorun giderme kılavuzu 'nda, aracı hataları için Olay Görüntüleyici günlüklerinde bağlantılar yerleştirildi.
    - Daha iyi hata işleme için ek bir özel durum eklendi.
    - Müşterilerin erişebileceği gerekli URL 'Leri denetlemesini sağlayan WVDAgentUrlTool.exe eklendi.
- Sürüm 1.0.2866.1500: Bu güncelleştirme 26 Mart 2021 ' de yayımlanmıştır ve yığın sistem durumu denetiminde bir sorunu düzeltir.
- Sürüm 1.0.2800.2802: Bu güncelleştirme 10 Mart 2021 ' de yayımlanmıştır ve genel iyileştirmeler ve hata düzeltmeleri içerir.
- Sürüm 1.0.2800.2800: Bu güncelleştirme 2 Mart 2021 ' de yayımlanmıştır ve ters bir bağlantı sorununu düzeltir.
- Sürüm 1.0.2800.2700: Bu güncelleştirme 10 Şubat 2021 ' de yayımlanmıştır ve genel iyileştirmeler ve hata düzeltmeleri içerir.
- Sürüm 1.0.2800.2700: Bu güncelleştirme 4 Şubat 2021 ' de yayımlanmıştır ve erişim reddedildi düzenleme sorununu düzeltir.

## <a name="fslogix-updates"></a>FSLogix güncelleştirmeleri

FSLogix için en son güncelleştirmeler mi merak ediyorsunuz? [FSLogix](/fslogix/whats-new)'teki yenilikleri inceleyin.

## <a name="march-2021"></a>Mart 2021

Mart 2021 ' de değiştirilen özellikler aşağıda verilmiştir.

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure portal Kullanıcı arabirimindeki güncelleştirmeler

Azure portal için Windows sanal masaüstü için aşağıdaki güncelleştirmeleri yaptık:

- Konak havuzları oluşturmak ve VM 'Leri eklemek için iş akışlarının yeni kullanılabilirlik seçeneklerini (kullanılabilirlik kümesi ve bölgeleri) etkinleştirdik.
- "Yardım gerekiyor" durumuna sahip bir konağın kullanılamaz olarak göründükleri bir sorunu düzelttik. Artık konağın yanında bir uyarı simgesi olacaktır.
- Etkin oturumlar için sıralamayı etkinleştirdik.
- Artık ana bilgisayar ayrıntıları sekmesinde belirli kullanıcılara ileti gönderebilir veya oturumu kapatabilirsiniz.
- Maksimum oturum sınırı alanını değiştirdik.
- Bir konak havuzu oluşturmak için iş akışına bir OU doğrulama yolu ekledik.
- Kişisel bir konak havuzu oluştururken artık Windows 10 görüntüsünün en son sürümünü kullanabilirsiniz.

### <a name="generation-2-images-and-trusted-launch"></a>2. nesil görüntüler ve güvenilir başlatma

Azure Marketi 'nde artık Windows 10 Enterprise ve Windows 10 Enterprise çoklu oturum için 2. nesil görüntü vardır. Bu görüntüler, güvenilir başlatma VM 'lerini kullanmanıza olanak sağlar. 2. nesil VM 'Ler hakkında daha fazla bilgi için [1. nesil veya 2 sanal makine oluşturmalıyım](../virtual-machines/generation-2.md). Windows sanal masaüstü güvenilir başlatma sanal makinelerini sağlama hakkında bilgi edinmek için bkz. [TechCommunity gönderimize](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)bakın.

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix artık Windows 10 Enterprise çoklu oturum görüntülerine önceden yüklendi

Müşteri geri bildirimlerine bağlı olarak, bir FSLogix yapılandırılmamış sürümü zaten yüklü olan Windows 10 Enterprise çoklu oturum görüntüsünün yeni bir sürümünü ayarladık. Bu, Windows sanal masaüstü dağıtımınızı daha kolay hale getiriyor.

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>Windows sanal masaüstü için Azure Izleyici, genel kullanıma sunuldu

Windows sanal masaüstü için Azure Izleyici, genel kullanıma sunulmuştur. Bu özellik, dağıtımlarınızı izleyen ve olayları, sistem durumunu ve sorun giderme önerilerini tek bir yerde görüntülemenize olanak tanıyan otomatikleştirilmiş bir hizmettir. Daha fazla bilgi için bkz. [belgelerimize](azure-monitor.md) bakın veya [techcommunity gönderimize](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)göz atın.

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>Windows sanal masaüstündeki takımlar için Mart 2021 güncelleştirmeleri

Windows sanal masaüstü 'nde takımlar için aşağıdaki güncelleştirmeleri yaptık:

- Aramalar ve 2x2 modunda video kalitesi performansını geliştirdik.
- Video işleme (XVP) donanım yük boşaltma kullanarak CPU kullanımını% 5-10 oranında azalttık (CPU oluşturmaya bağlı olarak).
- Daha eski makineler artık, daha fazla gelen video akışını 2x2 modunda düzgünce göstermek için XVP ve donanım kodu çözme kullanabilir.
- Daha iyi AV eşitleme performansı ve daha az geçici sorun için M74 ile M88 arasındaki WebRTC yığınını güncelleştirdik.
- Yazılım H264 kodumuzu, giden kameranın video kalitesini arttığı OpenH264 (Web 'de ekiplerde kullanılan OSS) ile değiştirdik.
- 30 Mart 'ta genel kullanıma yönelik takımlar sunucusu için 2x2 modunu etkinleştirdik. 2x2 modu, aynı anda dört adede kadar gelen video akışını gösterir.

### <a name="start-vm-on-connect-public-preview"></a>Connect genel önizlemede VM 'yi Başlat

Yeni konak havuzu ayarı, Connect 'te VM 'yi Başlat, artık genel önizlemede kullanıma sunuldu. Bu ayar, ihtiyacınız olduğunda sanal makinelerinizi açmanızı sağlar. Maliyetleri kaydetmek istiyorsanız, Azure Işlem ayarlarınızı yapılandırarak sanal makinelerinizi serbest getirmeniz gerekir. Daha fazla bilgi için [blog gönderimize](https://aka.ms/wvdstartvmonconnect) ve [belgelerimize](start-virtual-machine-connect.md)göz atın.

### <a name="windows-virtual-desktop-specialty-certification"></a>Windows sanal masaüstü özel sertifikası

AZ-140 sınavın, Azure 'da Windows sanal masaüstündeki uzmanlığınızı kanıtlamanızı sağlayacak bir beta sürümünü yayımladık. Daha fazla bilgi edinmek için [TechCommunity gönderimize](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)göz atın.

## <a name="february-2021"></a>Şubat 2021

Şubat 2021 ' de değiştirilen özellikler aşağıda verilmiştir.

### <a name="portal-experience"></a>Portal deneyimi

Azure portal deneyimini aşağıdaki yollarla geliştirdik:

- Oturum Ana bilgisayar Kılavuzu sekmesindeki konaklarda toplu boşaltma modu. 
- MSIX uygulaması iliştirme artık genel önizlemeye sunuldu.
- Koyu mod için sabit konak havuzuna genel bakış bilgileri.

### <a name="eu-metadata-storage-now-in-public-preview"></a>AB meta veri depolaması artık genel önizlemeye sunuldu

Windows sanal masaüstündeki hizmet meta verileri için bir depolama seçeneği olarak Avrupa (AB) Coğrafya genel önizlemesini barındırıyoruz. Müşteriler, hizmet nesnelerini oluştururken Batı veya Kuzey Avrupa arasında seçim yapabilir. Konak havuzlarının hizmet nesneleri ve meta verileri her bölge ile ilişkili Azure Coğrafya 'da depolanır. Daha fazla bilgi edinmek için [genel önizlemeyi duyuran blog gönderimizi](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)okuyun.

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Windows sanal masaüstü eklentisi güncelleştirmelerinde takımlar

Ekranın aniden karanlık veya video ve ses eşitlenme gibi en sık bildirilen sorunları ele alarak Windows sanal masaüstü eklentisindeki video arama kalitesini geliştirdik. Bu geliştirmeler, etkin konuşmacı değiştirme ile tek video görünümünün performansını artırmalıdır. Ayrıca, özel karakterleri olan donanım cihazlarının ekiplerde kullanılamadığı bir sorunu düzelttik.

## <a name="january-2021"></a>Ocak 2021

Ocak 2021 ' de değiştirilen özellikler:

### <a name="new-windows-virtual-desktop-offer"></a>Yeni Windows sanal masaüstü teklifi

Yeni müşteriler, yerel Microsoft çözümünü kullanırken 90 güne kadar, D serisi ve BS serisi sanal makineler için Windows sanal masaüstü işlem maliyetlerinde yüzde 30 tasarruf kazanın. Bu teklifi 31 Mart 2021 ' den önce Azure portal kullanabilirsiniz. [Windows sanal masaüstü teklifi sayfamız](https://azure.microsoft.com/services/virtual-desktop/offer/)hakkında daha fazla bilgi edinin.

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules değer değişikliği 

Azure Resource Manager iç içe olan şablonda, networkSecurityGroupRules için varsayılan değeri bir nesneden bir diziye değiştirdik. Bu, networkSecurityGroupRules için bir değer belirtmeden managedDisks-customimagevm.jskullandığınızda hataları engeller. Bu bir son değişiklik değildi ve geriye dönük olarak uyumludur.

### <a name="fslogix-hotfix-update"></a>FSLogix düzeltme güncelleştirmesi

Önceki sürümdeki sorunları çözmek için FSLogix, sürüm 2009 HF_01 (2.9.7654.46150) yayımladık (2.9.7621.30127). Önceki sürümü kullanmayı durdurmanızı ve FSLogix ' i mümkün olan en kısa sürede güncelleştirmeniz önerilir.

Daha fazla bilgi için bkz. [FSLogix 'teki](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)yenilikler içindeki sürüm notları.

### <a name="azure-portal-experience-improvements"></a>Azure portal deneyim iyileştirmeleri

Azure portal deneyiminde aşağıdaki iyileştirmeleri yaptık:

- Artık, Active Directory etki alanına ekleme hesabı kimlik bilgileriyle oluşturulmuş bir yerel hesap eklemek yerine doğrudan yerel VM Yöneticisi kimlik bilgilerini ekleyebilirsiniz.
- Kullanıcılar artık bireysel kullanıcılar ve gruplar için ayrı sekmelerde hem bireysel hem de grup atamalarını listeleyebilir.
- Windows sanal masaüstü aracısının sürüm numarası artık konak havuzları için sanal makineye genel bakış bölümünde görülebilir.
- Konak havuzları ve uygulama grupları için toplu silme eklendi.
- Artık bir konak havuzundaki birden çok oturum ana bilgisayarı için boşaltma modunu etkinleştirebilir veya devre dışı bırakabilirsiniz.
- Ortak IP alanı, VM ayrıntıları sayfasından kaldırıldı.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Windows sanal masaüstü Aracısı sorunlarını giderme

Kısa süre önce, yaygın sorunlarla karşılaşan müşterilere yardımcı olmak için [Windows sanal masaüstü Aracısı sorun giderme kılavuzunu](troubleshoot-agent.md) ayarladık.

### <a name="microsoft-defender-for-endpoint-integration"></a>Uç nokta tümleştirmesi için Microsoft Defender

Uç nokta tümleştirmesi için Microsoft Defender genel kullanıma sunuldu. Bu özellik, Windows Sanal Masaüstü sanal makinelerinizi yerel bir Windows 10 makinesiyle aynı araştırma deneyimini sağlar. Windows 10 Enterprise çoklu oturum kullanıyorsanız, uç nokta için Microsoft Defender, en fazla 50 eşzamanlı kullanıcı bağlantısı destekleyecektir. bu sayede, Windows 10 Enterprise çoklu oturum 'nın maliyet tasarrufları ve uç nokta için Microsoft Defender 'ın güvenilirliği sağlarsınız. Daha fazla bilgi için [blog gönderimize](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)göz atın.

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure Güvenlik temeli

Yakın zamanda ilgilenmeniz gereken Windows sanal masaüstü için [Azure Güvenlik temeli hakkında bir makale](security-baseline.md) yayımladık. Bu yönergeler, Azure Güvenlik kıyaslaması, sürüm 2,0 ' nin Windows sanal masaüstüne nasıl uygulanacağı hakkında bilgi içerir. Azure Güvenlik kıyaslaması, Azure 'da bulut çözümlerinizin güvenliğini sağlamak için kullanmanızı önerdiğimiz ayarları ve uygulamaları açıklar.

## <a name="december-2020"></a>Aralık 2020

Aralık 2020 ' de değiştirilen özellikler: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure Izleyici

Windows sanal masaüstü için Azure Izleyici için genel önizleme kullanıma sunuldu. Bu yeni özellik, BT uzmanlarının Windows sanal masaüstü ortamlarını anlamasına yardımcı olmak üzere Azure Izleyici çalışma kitaplarının üzerine oluşturulmuş sağlam bir Pano içerir. Daha fazla ayrıntı için [Blogumuzdaki duyuruya](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) göz atın. 

### <a name="azure-resource-manager-template-change"></a>Şablon değişikliğini Azure Resource Manager 

En son güncelleştirmede, konak havuzlarının oluşturulması ve sağlanması için Azure Resource Manager şablonundan tüm genel IP adresi parametrelerini kaldırdık. Dağıtımınızın güvenliğini sağlamak için Windows sanal masaüstü için genel IP 'Leri kullanmaktan kaçınmanızı öneririz. Dağıtımınız genel IP 'lerde güvense, bunun yerine özel IP 'Leri kullanmak için yeniden yapılandırmanız gerekir, aksi takdirde dağıtımınız düzgün çalışmaz.

### <a name="msix-app-attach-public-preview"></a>MALTı uygulama iliştirme genel önizlemesi 

MSIX uygulama iliştirme, bu ay genel önizlemeyi başlaeden başka bir hizmettir. MSIX uygulama iliştirme, Windows sanal masaüstü oturumu ana bilgisayar VM 'lerinize dinamik olarak MALTÝ uygulama sunan bir hizmettir. Daha fazla ayrıntı için [Blogumuzdaki duyuruya](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) göz atın. 

### <a name="screen-capture-protection"></a>Ekran yakalama koruması 

Bu ay Ayrıca ekran yakalama koruması için genel önizlemenin başlangıcını de işaretledi. Bu özelliği, önemli bilgilerin istemci uç noktalarında yakalanmasını engellemek için kullanabilirsiniz. [Bu sayfaya](https://aka.ms/WVDScreenCaptureProtection)giderek ekran yakalama korumasına bir deneme sunun.  

### <a name="built-in-roles"></a>Yerleşik roller

Yönetici izinleri için Windows sanal masaüstü için yeni yerleşik roller ekledik. Daha fazla bilgi için bkz. [Windows sanal masaüstü Için yerleşik roller](rbac.md). 

### <a name="application-group-limit-increase"></a>Uygulama grubu sınırı artışı

Azure Active Directory kiracının başına varsayılan uygulama grubu sınırını 200 grup olarak artırdık.

### <a name="client-updates-for-december-2020"></a>Aralık 2020 için istemci güncelleştirmeleri

Aşağıdaki istemcilerin yeni sürümlerini yayımladık: 

- Android
- macOS
- Windows

İstemci güncelleştirmeleri hakkında daha fazla bilgi için bkz. [istemci güncelleştirmeleri](whats-new.md#client-updates).

## <a name="november-2020"></a>Kasım 2020

### <a name="azure-portal-experience"></a>Azure portal deneyim

Azure portal kullanıcı deneyiminde iki hata düzeltildi:

- "VM Ekle" iş akışında masaüstü uygulamasının kolay adı artık üzerine yazılmaz.
- Oturum ana bilgisayarları ölçek kümelerinin parçasıysa, oturum ana bilgisayarı sekmesi şimdi yüklenir.

### <a name="fslogix-client-version-2009"></a>FSLogix istemcisi, sürüm 2009 

Çeşitli düzeltmeler ve geliştirmeler ile FSLogix istemcisinin yeni bir sürümünü yayımladık. [Blog gönderimiz](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)hakkında daha fazla bilgi edinin.

### <a name="rdp-shortpath-public-preview"></a>RDP ShortPath genel önizlemesi

RDP ShortPath, Noktadan siteye ve siteden siteye VPN 'Leri ve ExpressRoute kullanarak Windows sanal masaüstü oturum ana bilgisayarınıza doğrudan bağlantı sağlar. Ayrıca URCP aktarma protokolünü tanıtır. RDP ShortPath, Kullanıcı deneyimini geliştirmek için gecikme süresini ve ağ atlamalarını azaltmak üzere tasarlanmıştır. [Windows sanal masaüstü RDP Shortfılepath](shortpath.md)'de daha fazla bilgi edinin.

### <a name="azdesktopvirtualization-version-201"></a>Az. DesktopVirtualization, sürüm 2.0.1

Windows sanal masaüstü cmdlet 'lerinin sürüm 2.0.1'i yayımladık. Bu güncelleştirme, MSIX uygulama ekleme 'yi yönetmenizi sağlayacak cmdlet 'leri içerir. Yeni sürümü [PowerShell galerisinde](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)indirebilirsiniz.

### <a name="azure-advisor-updates"></a>Azure Advisor güncelleştirmeleri

Azure Advisor artık Windows sanal masaüstündeki yakınlık Kılavuzu için yeni bir önerilere ve yüksek öncelikli yük dengeli konak havuzlarında performansı iyileştirmeye yönelik yeni bir önerisine sahiptir. [Azure Web sitesinden](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)daha fazla bilgi edinin.

## <a name="october-2020"></a>Ekim 2020

Ekim 2020 ' de değiştirilen özellikler:

### <a name="improved-performance"></a>Geliştirilmiş performans

- Aşağıdaki Azure coğrafi bölgelerde bağlantı gecikmesini azaltarak performansı en iyi duruma getiriyoruz:
    - İsviçre
    - Kanada

Artık bu alanlardaki Kullanıcı deneyimi kalitesini tahmin etmek için [deneyim Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) ' i kullanabilirsiniz.

### <a name="azure-government-cloud-availability"></a>Azure Kamu Bulutu kullanılabilirliği

Azure Kamu Bulutu artık genel kullanıma sunulmuştur. [Blog gönderimiz](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)hakkında daha fazla bilgi edinin.

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows sanal masaüstü Azure portal güncelleştirmeleri

Windows sanal masaüstü Azure portal bazı güncelleştirmeler yaptık:

- Kullanıcıların "oturumlar" sekmesini açmasını önleyen bir RESOURCEID hatası düzeltildi.
- "Oturum Konakları" sekmesinde Kullanıcı arabirimini kolaylaştırın.
- RDP özellikleri altında "varsayılanlar," "kullanılabilirlik" ve "Varsayılanları geri yükle" ayarları düzeltildi.
- Tüm sekmelerde tutarlı "Kaldır" ve "Sil" işlevleri yapıldı.
- Portal şimdi "uygulama ekleme" iş akışında uygulama adlarını doğrular.
- Oturum ana bilgisayarının dışarı aktarma verileri sütunlarda hizalanmadığı bir sorun düzeltildi.
- Portalın kullanıcı oturumlarını alamadığında bir sorun düzeltildi.
- Oturum Ana bilgisayar alımı, sanal makinenin farklı bir kaynak grubunda oluşturulması durumunda meydana gelen bir sorun düzeltildi.
- Hem etkin hem de bağlantısı kesik oturumları listelemek için "oturum ana bilgisayarı" sekmesi güncelleştirildi.
- "Uygulamalar" sekmesinde artık sayfalar vardır.
- "Komut satırı gerektirir" metninin "uygulama listesi" sekmesinde doğru şekilde görüntülemediği bir sorun düzeltildi.
- Paylaşılan görüntü galerisinin Almanca dil sürümünü kullanırken portalın konak havuzlarını veya sanal makineleri dağıtmaması durumunda sorun düzeltildi.

### <a name="client-updates-for-october-2020"></a>2020 Ekim için istemci güncelleştirmeleri

İstemcilerin yeni sürümlerini yayımladık. Daha fazla bilgi için şu makalelere göz atın:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Diğer istemciler hakkında daha fazla bilgi için bkz. [istemci güncelleştirmeleri](#client-updates).

## <a name="september-2020"></a>Eylül 2020

Eylül 2020 ' de değiştirilen özellikler:

- Aşağıdaki Azure coğrafi bölgelerde bağlantı gecikmesini azaltarak performansı en iyi duruma getiriyoruz:
    - Almanya
    - Güney Afrika (yalnızca doğrulama ortamları için)

Artık bu alanlardaki Kullanıcı deneyimi kalitesini tahmin etmek için [deneyim Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) ' i kullanabilirsiniz.

- Windows sanal masaüstü için Windows Masaüstü istemcisinin 1.2.1364 sürümünü yayımladık. Bu güncelleştirmede aşağıdaki değişiklikleri yaptık:
    - Çoklu oturum açma (SSO) Windows 7 ' de çalışmayan bir sorun düzeltildi.
    - Ekipler için medya iyileştirmesini etkinleştiren bir Kullanıcı, başka bir uygulama özel modda açık bir ses akışına sahip olduğunda bir takım toplantısına çağrı veya katılmayı denediğinde, istemcinin bağlantısının kesilip kesilmesine neden olan bir sorun düzeltildi.
    - Ekipler için medya iyileştirmesi etkinleştirildiğinde ekiplerin ses veya video cihazlarını numaralandırmadığımızdan oluşan bir sorun düzeltildi.
    - "Ayarlarla ilgili yardım gerekiyor mu?" eklendi Masaüstü ayarları sayfasına bağlantı.
    - Yüksek karşıtlıklı koyu temalar kullanılırken gerçekleşen "abone ol" düğmesiyle ilgili bir sorun düzeltildi.
    
- Kullanıcılarımızın çok fazla olması sayesinde, Microsoft Store uzak masaüstü istemcisi için iki kritik sorunu düzelttik. İstemcinin aşamalı sürümümüzü dünya çapındaki daha fazla kullanıcıya genişlettiğimiz için geri bildirimi gözden geçirip sorunları gidermeye devam edeceğiz.
    
- VM konumunu, görüntüsünü, kaynak grubunu, önek adını, ağ yapılandırmasını, Azure portal dağıtımınıza bir VM eklemek için iş akışının bir parçası olarak değiştirmenize olanak tanıyan yeni bir özellik ekledik.

- BT uzmanları artık Microsoft Endpoint Manager 'ı kullanarak karma Azure Active Directory katılmış Windows 10 Enterprise VM 'lerini yönetebilir. Daha fazla bilgi edinmek için [blog gönderimize](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)bakın.

## <a name="august-2020"></a>Ağustos 2020

Ağustos 2020 ' de değiştirilen özellikler:

- Aşağıdaki Azure bölgelerinde bağlantı gecikmesini azaltmak için performansı geliştirdik: 

    - Birleşik Krallık
    - Fransa
    - Norveç
    - Güney Kore

   Bu değişikliklerin kullanıcılarınızı nasıl etkileyeceğini gösteren genel bir fikir edinmek için [deneyim Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) ' i kullanabilirsiniz.

- Microsoft Store uzak masaüstü Istemcisi (v 10.2.1522 +) artık genel kullanıma sunulmuştur! Microsoft Store uzak masaüstü Istemcisinin bu sürümü Windows sanal masaüstü ile uyumludur. Ayrıca, geliştirilmiş kullanıcı deneyimleri için yenilenmiş UI akışları geliştirdik. Bu güncelleştirme, akıcı tasarımı, hafif ve koyu modlarını ve diğer birçok heyecan verici değişikliği içerir. Ayrıca, istemciyi iOS, macOS ve Android istemcileri ile aynı temel Uzak Masaüstü Protokolü (RDP) altyapısını kullanacak şekilde de buraya verdik. Bu, tüm platformlarda daha hızlı bir hızda yeni özellikler sunmamızı sağlar. [Istemcisini indirip](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) deneyin!

- Takımlar masaüstü istemcisinde (sürüm 1.3.00.21759), istemcinin yalnızca sohbet, kanallar ve takvimdeki UTC saat dilimini gösterdiğimiz bir sorunu düzelttik. Güncelleştirilmiş istemci artık bunun yerine uzak oturumun saat dilimini gösterir.

- Azure Advisor artık Windows sanal masaüstü 'nün bir parçasıdır. Windows sanal masaüstüne Azure portal aracılığıyla eriştiğinizde, Windows sanal masaüstü ortamınızı iyileştirmek için öneriler görebilirsiniz. [Azure Danışmanı](azure-advisor.md)hakkında daha fazla bilgi edinin.

- Azure CLı artık Windows `az desktopvirtualization` sanal masaüstü dağıtımlarınızı otomatikleştirmenize yardımcı olmak Için Windows sanal masaüstü 'nü () desteklemektedir. Uzantı komutlarının bir listesi için [desktopvirtualization](/cli/azure/) 'a göz atın.

- Dağıtım şablonlarımızı Windows sanal masaüstü Azure Resource Manager arabirimleriyle tamamen uyumlu hale getirmek için güncelleştirdik. Şablonları [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)' da bulabilirsiniz.

- Windows sanal masaüstü US Gov portalı artık genel önizlemeye sunuldu. Daha fazla bilgi edinmek için [duyurumuza](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)bakın.

## <a name="july-2020"></a>Temmuz 2020  

Temmuz, Azure Kaynak Yönetimi tümleştirmesiyle Windows sanal masaüstü 'Nün genel kullanıma sunulmasına gelmiştir.

Bu yeni sürümle değiştirilen özellikler şunlardır: 

- "Spring 2020 sürümü" artık "Windows sanal masaüstü (klasik)" olarak bilinir, ancak "Spring yayını" artık yalnızca "Windows sanal masaüstü" olur. 2019 Daha fazla bilgi için [Bu blog gönderisine](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)göz atın. 

Yeni özellikler hakkında daha fazla bilgi edinmek için [Bu blog gönderisine](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)göz atın. 

### <a name="autoscaling-tool-update"></a>Otomatik ölçeklendirme aracı güncelleştirmesi

Otomatik ölçeklendirme aracının Önizlemedeki en son sürümü genel kullanıma sunulmuştur. Bu araç, bir konak havuzu içindeki oturum ana bilgisayarı sanal makinelerini (VM 'Ler) otomatik olarak kapatmak ve yeniden başlatmak için bir Azure Otomasyonu hesabı ve Azure Logic app kullanır ve altyapı maliyetlerini azaltır. [Azure Otomasyonu 'nu kullanarak ölçek oturum Konakları](set-up-scaling-script.md)hakkında daha fazla bilgi edinin.

### <a name="azure-portal"></a>Azure portalı

Artık Windows sanal masaüstündeki Azure portal aşağıdaki işlemleri yapabilirsiniz: 

- Kullanıcıları doğrudan kişisel masaüstü oturumu konaklarına atama  
- Konak havuzları için doğrulama ortamı ayarını değiştirme 

### <a name="diagnostics"></a>Tanılama

Log Analytics çalışma alanı için yeni önceden oluşturulmuş sorgular yayımladık. Sorgulara erişmek için **günlüklere** gidin ve **Kategori** altında **Windows sanal masaüstü**' nü seçin. [Tanılama özelliği için Log Analytics kullanma](diagnostics-log-analytics.md)hakkında daha fazla bilgi edinin.

### <a name="update-for-remote-desktop-client-for-android"></a>Android için Uzak Masaüstü istemcisi güncelleştirmesi

[Android Için uzak masaüstü istemcisi](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) artık Windows sanal masaüstü bağlantılarını desteklemektedir. 10.0.7 sürümünden itibaren, Android istemcisi geliştirilmiş Kullanıcı deneyimi için yeni bir kullanıcı arabirimi sunar. İstemci ayrıca Windows sanal masaüstü çalışma alanlarına abone olurken koşullu erişimi etkinleştirmek için Android cihazlarda Microsoft Authenticator ile tümleşir.  

Uzak Masaüstü istemcisinin önceki sürümü artık "Uzak Masaüstü 8" olarak adlandırılmaktadır. İstemcinin önceki sürümünde bulunan mevcut tüm bağlantılar yeni istemciye sorunsuz bir şekilde aktarılır. Yeni istemci iOS ve macOS istemcileri ile aynı temel RDP Core altyapısına yeniden yazıldı ve tüm platformlarda yeni özelliklerin daha hızlı sürümüdür. 

### <a name="teams-update"></a>Takımlar güncelleştirmesi

Windows sanal masaüstü için Microsoft ekiplerinde iyileştirmeler yaptık. En önemlisi Windows sanal masaüstü artık Windows Masaüstü istemcisi için ses ve video iyileştirmeyi desteklemektedir. Yeniden yönlendirme, çağrılar ve toplantılar halinde ses veya video kullandıklarında kullanıcılar arasında doğrudan yollar oluşturarak gecikme süresini geliştirir. Daha az mesafe daha az sıçrama anlamına gelir, bu da aramaların daha yumuşak görünmesini sağlar. [Windows Sanal Masaüstü ' nde kullanım ekipleri](teams-on-wvd.md)hakkında daha fazla bilgi edinin.

## <a name="june-2020"></a>Haziran 2020

Son ay, önizleme aşamasında Windows sanal masaüstü Azure Resource Manager tümleştirmede tanıtıldık. Bu güncelleştirme, hakkında bilgi almak için çok sevdiğimiz birçok yeni özellik içerir. Windows sanal masaüstü 'nün bu sürümü için yenilikler aşağıda verilmiştir.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows sanal masaüstü artık Azure Resource Manager ile tümleşiktir

Windows sanal masaüstü artık Azure Resource Manager ile tümleşiktir. En son güncelleştirmede, tüm Windows sanal masaüstü nesneleri artık kaynaklar Azure Resource Manager. Bu güncelleştirme ayrıca Azure rol tabanlı erişim denetimi (Azure RBAC) ile tümleşiktir. Daha fazla bilgi için bkz. [Azure Resource Manager nedir?](../azure-resource-manager/management/overview.md) .

Bu değişiklik sizin için aşağıdaki gibidir:

- Windows sanal masaüstü artık Azure portal tümleşiktir. Bu, her şeyi portalda doğrudan yönetebileceğiniz, PowerShell, Web uygulamaları veya üçüncü taraf araçları gerekmeden yönetebileceğinizi gösterir. Başlamak için [Azure Portal bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md)öğreticimize göz atın.

- Bu güncelleştirmeden önce, yalnızca bireysel kullanıcılara yönelik RemoteApps ve masaüstleri yayımlayabilirsiniz. Azure Resource Manager, artık kaynakları Azure Active Directory gruplara yayımlayabilirsiniz.

- Windows sanal masaüstü 'nün önceki sürümünde, bir kiracıya veya konak havuzuna atayabileceğiniz dört yerleşik yönetici rolü vardı. Bu roller artık [Azure rol tabanlı erişim denetimi 'nde (Azure RBAC)](../role-based-access-control/overview.md). Bu rolleri, tam ve zengin bir temsilciliizin veren her Windows sanal masaüstü Azure Resource Manager nesnesine uygulayabilirsiniz.

- Bu güncelleştirmede, bir konak havuzunu genişletmek için artık Azure Marketi 'ni veya GitHub şablonunu sürekli olarak çalıştırmanız gerekmez. Bir konak havuzunu genişletmelisiniz, ek oturum Konakları dağıtmak için Azure portal ana bilgisayar havuzunuza gitmeniz ve **+ Ekle** ' yi seçmeniz gerekir.

- Konak havuzu dağıtımı artık [Azure Paylaşılan görüntü galerisiyle](../virtual-machines/shared-image-galleries.md)tamamen tümleşiktir. Paylaşılan görüntü Galerisi, görüntü sürümü oluşturma da dahil olmak üzere sanal makine (VM) görüntü tanımlarını depolayan ayrı bir Azure hizmetidir. Ayrıca, yerel dağıtım için görüntülerinizi kopyalamak ve diğer Azure bölgelerine göndermek için genel çoğaltma kullanabilirsiniz.

- PowerShell veya tanılama hizmeti Web uygulaması tarafından yapılması için kullanılan izleme işlevleri artık Azure portal Log Analytics öğesine taşınmıştır. Artık raporlarınızı görselleştirmek için iki seçeneğiniz de vardır. Görsel raporlar oluşturmak için kusto sorguları çalıştırabilir ve çalışma kitaplarını kullanabilirsiniz.

- Artık Windows sanal masaüstü 'Nü kullanmak için Azure Active Directory (Azure AD) onayını tamamlamayı gerekli değilsiniz. Bu güncelleştirmede, Azure aboneliğinizdeki Azure AD kiracısı kullanıcılarınızın kimliğini doğrular ve yöneticiler için Azure RBAC denetimleri sağlar.

### <a name="powershell-support"></a>PowerShell desteği

Bu güncelleştirmeyle Azure PowerShell az Module 'e yeni AzWvd cmdlet 'leri ekledik. Bu yeni modül, .NET Core üzerinde çalışan PowerShell Core 'da desteklenir.

Modülünü yüklemek için [Windows sanal masaüstü Için PowerShell modülünü ayarlama](powershell-module.md)bölümündeki yönergeleri izleyin.

[Azwvd PowerShell başvurusunda](/powershell/module/az.desktopvirtualization/#desktopvirtualization)kullanılabilir komutların listesini de görebilirsiniz.

Yeni özellikler hakkında daha fazla bilgi için [blog gönderimize](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)göz atın.

### <a name="additional-gateways"></a>Ek ağ geçitleri

Bağlantı gecikmesini azaltmak için Güney Afrika 'ya yeni bir ağ geçidi kümesi ekledik.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows sanal masaüstündeki Microsoft ekipleri (Önizleme)

Windows sanal masaüstü için Microsoft ekiplerinde bazı geliştirmeler yaptık. En önemlisi Windows sanal masaüstü artık çağrılar için ses ve görsel yeniden yönlendirmeyi desteklemektedir. Yeniden yönlendirme, kullanıcılar ses veya video kullanarak çağrı yaparken doğrudan yollar oluşturarak gecikme süresini geliştirir. Daha az mesafe daha az sıçrama anlamına gelir, bu da aramaların daha yumuşak görünmesini sağlar.

Daha fazla bilgi edinmek için [blog gönderimize](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Windows sanal masaüstü yol haritası Microsoft 365](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)gelecek planlar hakkında bilgi edinin.
