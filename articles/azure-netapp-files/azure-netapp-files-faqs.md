---
title: Azure NetApp Files hakkında SSS | Microsoft Docs
description: Ağ, güvenlik, performans, kapasite yönetimi ve veri geçişi/koruma gibi Azure NetApp Files hakkında sık sorulan soruları gözden geçirin.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: 6cef4860184b217e96e8967ab24a3befc632e316
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811859"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp Files hakkında SSS

Bu makalede Azure NetApp Files hakkında sık sorulan sorular (SSS) yanıtlanmaktadır. 

## <a name="networking-faqs"></a>Ağ oluşturma SSS

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>NFS veya SMB veri yolu Internet üzerinden mi çalışıyor?  

Hayır. NFS veya SMB için veri yolu, Internet üzerinden geçmiyor. Azure NetApp Files, hizmetin kullanılabilir olduğu Azure sanal ağına (VNet) dağıtılan bir Azure yerel hizmetidir. Azure NetApp Files, temsilcili bir alt ağ kullanır ve doğrudan VNet üzerinde bir ağ arabirimi sağlar. 

Ayrıntılar için [Azure NetApp Files ağ planlaması kılavuzlarına](./azure-netapp-files-network-topologies.md) bakın.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Zaten oluşturduğum bir VNet 'i Azure NetApp Files hizmetine bağlanabilir miyim?

Evet, oluşturduğunuz VNET 'leri hizmete bağlayabilirsiniz. 

Ayrıntılar için [Azure NetApp Files ağ planlaması kılavuzlarına](./azure-netapp-files-network-topologies.md) bakın.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Azure NetApp Files NFS birimini DNS FQDN adı kullanarak bağlayabilir miyim?

Evet, gerekli DNS girdilerini oluşturursanız, bunu yapabilirsiniz. Azure NetApp Files sağlanan birim için hizmet IP 'si sağlar. 

> [!NOTE] 
> Azure NetApp Files, hizmet için gerektiğinde ek IP 'Ler dağıtabilir.  DNS girdilerinin düzenli olarak güncelleştirilmeleri gerekebilir.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Bir Azure NetApp Files birimi için kendi IP adresini ayarlayabilir veya seçebilir miyim?  

Hayır. Azure NetApp Files birimlerine IP ataması dinamiktir. Statik IP ataması desteklenmiyor. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files ikili yığın (IPv4 ve IPv6) VNet 'i destekliyor mu?

Hayır, Azure NetApp Files Şu anda çift yığın (IPv4 ve IPv6) VNet 'i desteklemiyor.  
 
## <a name="security-faqs"></a>Güvenlik SSS

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM ve depolama arasındaki ağ trafiği şifrelenir mi?

NFSv 4.1 istemcileri ve Azure NetApp Files birimleri arasındaki veri trafiği, AES-256 şifrelemesi ile Kerberos kullanılarak şifrelenir. Ayrıntılar için bkz. [NFSv 4.1 Kerberos şifrelemeyi yapılandırma Azure NetApp Files](configure-kerberos-encryption.md) .   

Azure NetApp Files birimler için NFSv3 veya SMB3 istemcileri arasındaki veri trafiği şifrelenmemiştir. Bununla birlikte, bir Azure VM 'den (NFS veya SMB istemcisi çalıştıran) Azure NetApp Files, diğer tüm Azure-VM-VM trafiği kadar güvenlidir. Bu trafik Azure veri merkezi ağı için yereldir. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Depolama alanı bekleyen olarak şifrelenebilir mi?

Tüm Azure NetApp Files birimleri FIPS 140-2 standardı kullanılarak şifrelenir. Tüm anahtarlar Azure NetApp Files hizmeti tarafından yönetilir. 

### <a name="how-are-encryption-keys-managed"></a>Şifreleme anahtarları nasıl yönetilir? 

Azure NetApp Files için anahtar yönetimi, hizmet tarafından işlenir. Her birim için benzersiz bir XTS-AES-256 veri şifreleme anahtarı oluşturulur. Tüm birim anahtarlarını şifrelemek ve korumak için bir şifreleme anahtarı hiyerarşisi kullanılır. Bu şifreleme anahtarları hiçbir şekilde, şifrelenmemiş bir biçimde gösterilmez veya bildirilmez. Şifreleme anahtarları bir birim silindiğinde hemen silinir.

Azure ayrılmış HSM kullanılarak müşteri tarafından yönetilen anahtarlar (Kendi Anahtarını Getir) için destek, Doğu ABD, Orta Güney ABD, Batı ABD 2 ve US Gov Virginia bölgelerinde denetimli bir şekilde kullanılabilir. Adresinden erişim isteyebilirsiniz [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Kapasite kullanılabilir hale geldiğinde istekler onaylanır.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Azure NetApp Files hizmeti bağlama hedefine erişimi denetlemek için NFS dışarı aktarma ilkesi kurallarını yapılandırabilir miyim?

Evet, tek bir NFS dışarı aktarma ilkesinde en fazla beş kural yapılandırabilirsiniz.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files ağ güvenlik gruplarını destekliyor mu?

Hayır, şu anda ağ güvenlik gruplarını, Azure NetApp Files için temsilci alt ağına veya hizmet tarafından oluşturulan ağ arabirimlerine uygulayamamaktadır.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Azure RBAC 'i Azure NetApp Files kullanabilir miyim?

Evet, Azure RBAC özelliklerini destekler Azure NetApp Files. Yerleşik Azure rolleriyle birlikte Azure NetApp Files için [özel roller de oluşturabilirsiniz](../role-based-access-control/custom-roles.md) . 

Azure NetApp Files izinlerinin tamamı listesi için bkz. Azure Kaynak sağlayıcısı işlemleri [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) .

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Azure etkinlik günlükleri Azure NetApp Files destekleniyor mu?

Azure NetApp Files bir Azure yerel hizmetidir. Azure NetApp Files için tüm PUT, POST ve DELETE API 'Leri günlüğe kaydedilir. Örneğin, Günlükler, anlık görüntüyü kimin oluşturduğunu, kimin birimi değiştirildiğini ve bu şekilde devam eden etkinlikleri gösterir.

API işlemlerinin tüm listesi için bkz. [Azure NetApp Files REST API](/rest/api/netapp/).

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Azure ilkelerini Azure NetApp Files kullanabilir miyim?

Evet, [özel Azure ilkeleri](../governance/policy/tutorials/create-custom-policy-definition.md)oluşturabilirsiniz. 

Ancak, Azure NetApp Files arabiriminde Azure ilkeleri (Özel adlandırma ilkeleri) oluşturamazsınız. [Azure NetApp Files ağ planlama yönergelerine](azure-netapp-files-network-topologies.md#considerations)bakın.

## <a name="performance-faqs"></a>Performans hakkında SSS

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files performansını iyileştirmek veya ayarlamak için ne yapmam gerekir?

Performans gereksinimlerine göre aşağıdaki eylemleri gerçekleştirebilirsiniz: 
- Sanal makinenin uygun şekilde boyutlandırıldığından emin olun.
- VM için hızlandırılmış ağı etkinleştirin.
- Kapasite havuzunun istenen hizmet düzeyini ve boyutunu seçin.
- Kapasite ve performans için istenen kota boyutuyla bir birim oluşturun.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files işleme tabanlı hizmet düzeylerini ıOPS 'ye Nasıl yaparım? dönüştürmek istiyor musunuz?

Aşağıdaki formülü kullanarak MB/s 'yi ıOPS 'ye dönüştürebilirsiniz:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Nasıl yaparım? bir birimin hizmet düzeyini değiştirmek mi istiyorsunuz?

Birimi, birimi için istediğiniz [hizmet düzeyini](azure-netapp-files-service-levels.md) kullanan başka bir kapasite havuzuna taşıyarak mevcut bir birimin hizmet düzeyini değiştirebilirsiniz. Bkz. [bir birimin hizmet düzeyini dinamik olarak değiştirme](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Nasıl yaparım? Azure NetApp Files performans izlemesi yapılsın mı?

Azure NetApp Files, birim performans ölçümleri sağlar. Azure Izleyici 'yi, Azure NetApp Files kullanım ölçümlerini izlemek için de kullanabilirsiniz.  Azure NetApp Files için performans ölçümlerinin listesi için bkz. [Azure NetApp Files ölçümleri](azure-netapp-files-metrics.md) .

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv 4.1 üzerinde Kerberos 'un performans etkisi nedir?

NFSv 4.1 için güvenlik seçenekleri, test edilen performans vektörleri ve beklenen performans etkisi hakkında bilgi için bkz. [NFSv 4.1 birimlerinde Kerberos 'un performans etkisi](performance-impact-kerberos.md) . 

## <a name="nfs-faqs"></a>NFS hakkında SSS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Bir Azure VM başlatıldığında veya yeniden başlatıldığında bir birimin otomatik olarak bağlanmasını istiyorum.  Nasıl yaparım? kalıcı NFS birimleri için ana bilgisayarımı yapılandırın mi?

VM başlatma veya yeniden başlatma sırasında bir NFS biriminin otomatik olarak bağlanması için konaktaki dosyaya bir giriş ekleyin `/etc/fstab` . 

Ayrıntılar için bkz. [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>NFS istemcisindeki DF komutu neden sağlanan birim boyutunu göstermiyor?

DF olarak bildirilen birim boyutu, Azure NetApp Files biriminin büyüyebileceği en büyük boyutum boyutudur. DF komutunda Azure NetApp Files birimin boyutu, birimin kotasının veya boyutunun yansıtımsıdır.  Azure NetApp Files birim boyutunu veya kotasını Azure portal veya API aracılığıyla edinebilirsiniz.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files NFS sürümü ne destekler?

Azure NetApp Files, NFSv3 ve NFSv 4.1 destekler. NFS sürümünü kullanarak [bir birim oluşturabilirsiniz](azure-netapp-files-create-volumes.md) . 

### <a name="how-do-i-enable-root-squashing"></a>Nasıl yaparım? kök ele geçirme etkinleştirilsin mi?

Birimin dışa aktarma ilkesini kullanarak, kök hesabın birime erişip erişemeyeceğini belirtebilirsiniz. Ayrıntılar için bkz. [NFS için dışarı aktarma Ilkesini yapılandırma](azure-netapp-files-configure-export-policy.md) .

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Birden çok birim için aynı dosya yolunu (birim oluşturma belirteci) kullanabilir miyim?

Evet, yazabilirsiniz. Ancak, dosya yolu farklı bir abonelikte ya da farklı bir bölgede kullanılmalıdır.   

Örneğin, adlı bir birim oluşturursunuz `vol1` . Daha sonra `vol1` farklı bir kapasite havuzunda, ancak aynı abonelikte ve bölgede adlı başka bir birim de oluşturabilirsiniz. Bu durumda, aynı birim adının kullanılması `vol1` hataya neden olur. Aynı dosya yolunu kullanmak için, ad farklı bir bölgede veya abonelikte olmalıdır.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>NFS birimlerine bir Windows istemcisi aracılığıyla erişmeyi denediğimde, istemci klasör ve alt klasörleri aramak için uzun zaman alır mi?

`CaseSensitiveLookup`Klasörlerin ve alt klasörlerin arama hızını hızlandırmak Için Windows istemcisinde etkinleştirildiğinden emin olun:

1. CaseSensitiveLookup etkinleştirmek için aşağıdaki PowerShell komutunu kullanın:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Windows Server 'da birimi bağlayın.   
    Örnek:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp Files NFSv 4.1 dosya kilitlemeyi nasıl destekler? 

NFSv 4.1 istemcileri için Azure NetApp Files, kira tabanlı bir model altındaki tüm dosya kilitlerinin durumunu tutan NFSv 4.1 dosya kilitleme mekanizmasını destekler. 

RFC 3530 başına Azure NetApp Files, bir NFS istemcisi tarafından tutulan tüm durum için tek bir kira süresi tanımlar. İstemci, tanımlanan süre içinde kiralamasını yenilemezse, istemcinin kirası ile ilişkili tüm durumlar sunucu tarafından serbest bırakılır.  

Örneğin, bir birimi bağlamak zaman aşımlarının dışında yanıt vermeyen veya kilitlenirse, kilitler serbest bırakılır. İstemci, bir dosyayı okumak gibi işlemleri gerçekleştirerek kirayı açıkça veya örtük olarak yenileyebilir.   

Yetkisiz kullanım süresi, istemcilerin bir sunucu kurtarması sırasında kilitleme durumunu geri kazanmak için deneyebileceği özel bir işleme süresini tanımlar. Kiralamalar için varsayılan zaman aşımı, 45 saniyelik yetkisiz kullanım süresi ile 30 saniyedir. Bu süreden sonra, istemcinin kirası serbest bırakılır.   

## <a name="smb-faqs"></a>SMB hakkında SSS

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Hangi SMB sürümleri Azure NetApp Files tarafından destekleniyor?

Azure NetApp Files, SMB 2,1 ve SMB 3,1 (SMB 3,0 desteğini içerir) desteği sunar.    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB erişimi için bir Active Directory bağlantısı gerekiyor mu? 

Evet, bir SMB birimini dağıtabilmeniz için önce bir Active Directory bağlantısı oluşturmanız gerekir. Başarılı bir bağlantı için, belirtilen etki alanı denetleyicilerine Azure NetApp Files Temsilcili alt ağı tarafından erişilebilir olmalıdır.  Ayrıntılar için bkz. [SMB birimi oluşturma](./azure-netapp-files-create-volumes-smb.md) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Kaç Active Directory bağlantı destekleniyor?

Azure NetApp Files, AD bağlantıları farklı NetApp hesaplarında olsa bile, tek bir *bölgede* birden çok ACTIVE DIRECTORY (ad) bağlantısını desteklemez. Ancak, AD bağlantıları farklı bölgelerde olduğu sürece, tek bir *abonelikte* bırden fazla ad bağlantısına sahip olabilirsiniz. Tek bir bölgede birden çok AD bağlantısına ihtiyacınız varsa, bunu yapmak için ayrı abonelikler kullanabilirsiniz. 

NetApp hesabı başına bir AD bağlantısı yapılandırılır; AD bağlantısı yalnızca içinde oluşturulduğu NetApp hesabı aracılığıyla görülebilir.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files Azure Active Directory destekliyor mu? 

[Azure Active Directory (ad) etki alanı Hizmetleri](../active-directory-domain-services/overview.md) ve [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) desteklenir. Mevcut Active Directory etki alanı denetleyicilerini Azure NetApp Files ile birlikte kullanabilirsiniz. Etki alanı denetleyicileri, Azure 'da sanal makineler veya ExpressRoute ya da S2S VPN aracılığıyla şirket içi olarak bulunabilir. Azure NetApp Files Şu anda [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) için ad katılmayı desteklemez.

Azure Active Directory Domain Services ile Azure NetApp Files kullanıyorsanız, kuruluş birimi yolu, `OU=AADDC Computers` NetApp hesabınız için Active Directory yapılandırdığınızda olur.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Windows Server Active Directory sürümleri destekleniyor mu?

Azure NetApp Files, Active Directory Domain Services Windows Server 2008r2SP1-2019 sürümlerini destekler.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>SMB istemcimde kullanılabilir alan, sağlanan boyutu neden göstermiyor?

SMB istemcisi tarafından bildirilen birim boyutu, Azure NetApp Files biriminin büyüyebileceği en büyük boyutum boyutudur. SMB istemcisinde gösterildiği gibi Azure NetApp Files birimin boyutu, birimin kotasının veya boyutunun yansıtımsıdır. Azure NetApp Files birim boyutunu veya kotasını Azure portal veya API aracılığıyla edinebilirsiniz.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>SMB paylaşımma bağlanırken sorun yaşıyorum. Ne yapmalıyım?

En iyi uygulama olarak, bilgisayar saati eşitlemesi için maksimum toleransı beş dakikaya ayarlayın. Daha fazla bilgi için bkz. [bilgisayar saati eşitlemesi Için maksimum tolerans](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>`SMB Shares`,, `Sessions` Ve `Open Files` Bilgisayar Yönetim Konsolu 'nu (MMC) yönetebilir miyim?

`SMB Shares`, `Sessions` Ve `Open Files` Ile bilgisayar YÖNETIMI Konsolu (MMC) yönetimi şu anda desteklenmiyor.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Portal aracılığıyla bir SMB biriminin IP adresini nasıl alabilirim?

Birime Genel Bakış bölmesinde **JSON görünümü** bağlantısını kullanın ve **Özellikler** bağlamahedefleri altında **startip** tanımlayıcısı ' nı arayın  ->  .

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Bir Azure NetApp Files SMB paylaşımının DFS ad alanı (DFS-N) kökü işlevi görebilir mi?

Hayır. Ancak, Azure NetApp Files SMB paylaşımları, DFS ad alanı (DFS-N) klasörü hedefi olarak görev yapabilir.   
DFS-N klasör hedefi olarak bir Azure NetApp Files SMB paylaşımının kullanılması için, [DFS klasör hedefi ekleme](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target) yordamını kullanarak Azure NetApp Files SMB paylaşımının evrensel adlandırma KURALı (UNC) bağlama yolunu sağlayın.  

### <a name="smb-encryption-faqs"></a>SMB şifreleme SSS

Bu bölümde, SMB şifrelemesi (SMB 3,0 ve SMB 3.1.1) hakkında sıkça sorulan sorular yanıtlanmaktadır.

#### <a name="what-is-smb-encryption"></a>SMB şifrelemesi nedir?  

[SMB şifrelemesi](/windows-server/storage/file-server/smb-security) , SMB verileri için uçtan uca şifrelemeyi sağlar ve verileri güvenilmeyen ağlarda izinsiz bir yerden bırakarak verileri korur. SMB şifreleme, SMB 3,0 ve üzeri bir sürümü destekler. 

#### <a name="how-does-smb-encryption-work"></a>SMB şifrelemesi nasıl çalışır?

Depolama alanına bir istek gönderilirken istemci, isteğin şifresini çözdüğü için isteği şifreler. Yanıtlar sunucu tarafından benzer şekilde şifrelenir ve istemci tarafından şifresi çözülür.

#### <a name="which-clients-support-smb-encryption"></a>Hangi istemciler SMB şifrelemesini destekliyor?

Windows 10, Windows 2012 ve sonraki sürümler SMB şifrelemesini destekler.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>Azure NetApp Files, SMB şifrelemesi hangi katmanda etkinleştirilmiştir?  

SMB şifrelemesi, paylaşma düzeyinde etkindir.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Azure NetApp Files hangi SMB şifrelemesi biçimlerini kullanır?

SMB 3,0 AES-CCM algoritmasını kullanır, SMB 3.1.1 AES-GCM algoritmasını kullanır

#### <a name="is-smb-encryption-required"></a>SMB şifrelemesi gerekli mi?

SMB şifrelemesi gerekli değildir. Bu nedenle, yalnızca Kullanıcı Azure NetApp Files etkinleştirmek isterse, belirli bir paylaşıma yönelik olarak etkinleştirilir. Azure NetApp Files paylaşımlar Internet 'e hiçbir şekilde gösterilmez. Yalnızca belirli bir sanal ağ içinden VPN veya Express Route üzerinden erişilebilir, bu nedenle Azure NetApp Files paylaşımlar doğal olarak güvenlidir. SMB şifrelemesini etkinleştirme seçimi tamamen kullanıcıya tamamıyla yapılır. Bu özelliği etkinleştirmeden önce beklenen performans cezası hakkında dikkat edin.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>İstemci iş yükleri üzerinde SMB şifrelemenin beklenen etkisi nedir?

SMB şifrelemesi hem istemciye (iletileri şifrelemek ve şifrelerini çözmek için CPU ek yükü) hem de depolama (aktarım hızı ile indirimleri) üzerinde etkilense de aşağıdaki tabloda yalnızca depolama etkisi vurgulanmaktadır. İş yüklerini üretime dağıtmaya başlamadan önce kendi uygulamalarınıza karşı şifreleme performansı etkisini test etmelisiniz.

|     G/ç profili       |     Etki        |
|-  |-  |
|     İş yüklerini okuma ve yazma      |     %10 ila %15        |
|     Meta veriler yoğun        |     %5    |

## <a name="capacity-management-faqs"></a>Kapasite Yönetimi SSS

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Kapasite havuzu ve Azure NetApp Files hacmi için Nasıl yaparım? İzleyicisi kullanımı İzmi? 

Azure NetApp Files kapasite havuzu ve toplu kullanım ölçümleri sağlar. Azure Izleyici 'yi, Azure NetApp Files kullanımını izlemek için de kullanabilirsiniz. Ayrıntılar için bkz. [Azure NetApp Files ölçümleri](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure Depolama Gezgini Azure NetApp Files yönetebilir miyim?

Hayır. Azure NetApp Files Azure Depolama Gezgini tarafından desteklenmez.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Nasıl yaparım?, bir dizinin sınır boyutuna yaklaşıp yaklaştığını mi belirlemektir?

Bir `stat` dizinin dizin meta verileri için en büyük boyut sınırına yaklaşıp yaklaşmadığını görmek için bir istemciden komutunu kullanabilirsiniz (320 MB).   

320 MB 'lik bir dizin için, blok sayısı 655360 ' dir ve her blok boyutu 512 bayttır.  (Yani, 320x1024x1024/512.)  Bu sayı, 320 MB 'lik bir dizin için yaklaşık 4.000.000 dosya için en fazla. Ancak, dizinde ASCII olmayan karakterler içeren dosya sayısı gibi etkenlere bağlı olarak, en fazla dosya sayısının gerçek sayısı daha düşük olabilir. Bu nedenle, `stat` dizininizin sınırına yaklaşıp yaklaştığını öğrenmek için komutunu aşağıdaki şekilde kullanmalısınız.  

Örnekler:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Veri geçişi ve koruma SSS

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Nasıl yaparım? verileri Azure NetApp Files geçirmek istiyor musunuz?
Azure NetApp Files NFS ve SMB birimleri sağlar.  Hizmete veri geçirmek için herhangi bir dosya tabanlı kopyalama aracı kullanabilirsiniz. 

NetApp, SaaS tabanlı bir çözüm sunar, [NetApp bulut eşitlemesi](https://cloud.netapp.com/cloud-sync-service).  Çözüm, NFS veya SMB verilerini Azure NetApp Files NFS dışarı aktarmaları veya SMB paylaşımlarına çoğaltmanıza olanak sağlar. 

Ayrıca, verileri kopyalamak için geniş bir ücretsiz araç dizisi de kullanabilirsiniz. NFS için, [rsync](https://rsync.samba.org/examples.html) gibi iş yükü araçlarını kullanarak kaynak verileri Azure NetApp Files bir birimde kopyalayabilir ve eşitleyebilirsiniz. SMB için iş yükleri [Robocopy](/windows-server/administration/windows-commands/robocopy) 'ni aynı şekilde kullanabilirsiniz.  Bu araçlar, dosya veya klasör izinlerini de çoğaltabilir. 

Şirket içinden Azure NetApp Files veri geçişi gereksinimleri aşağıdaki gibidir: 

- Hedef Azure bölgesinde Azure NetApp Files kullanılabildiğinden emin olun.
- Kaynak ve Azure NetApp Files hedef birim IP adresi arasındaki ağ bağlantısını doğrulayın. Şirket içi ve Azure NetApp Files hizmeti arasında veri aktarımı ExpressRoute üzerinden desteklenir.
- Hedef Azure NetApp Files birimini oluşturun.
- Tercih ettiğiniz dosya kopyalama aracını kullanarak kaynak verileri hedef birime aktarın.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Başka bir Azure bölgesinde Azure NetApp Files biriminin bir kopyasını oluşturmak Nasıl yaparım? misiniz?
    
Azure NetApp Files NFS ve SMB birimleri sağlar.  Tüm dosya tabanlı kopyalama araçları, Azure bölgeleri arasında veri çoğaltmak için kullanılabilir. 

NetApp, SaaS tabanlı bir çözüm sunar, [NetApp bulut eşitlemesi](https://cloud.netapp.com/cloud-sync-service).  Çözüm, NFS veya SMB verilerini Azure NetApp Files NFS dışarı aktarmaları veya SMB paylaşımlarına çoğaltmanıza olanak sağlar. 

Ayrıca, verileri kopyalamak için geniş bir ücretsiz araç dizisi de kullanabilirsiniz. NFS için, [rsync](https://rsync.samba.org/examples.html) gibi iş yükü araçlarını kullanarak kaynak verileri Azure NetApp Files bir birimde kopyalayabilir ve eşitleyebilirsiniz. SMB için iş yükleri [Robocopy](/windows-server/administration/windows-commands/robocopy) 'ni aynı şekilde kullanabilirsiniz.  Bu araçlar, dosya veya klasör izinlerini de çoğaltabilir. 

Azure NetApp Files bir birimi başka bir Azure bölgesine çoğaltmak için gerekenler şunlardır: 
- Hedef Azure bölgesinde Azure NetApp Files kullanılabildiğinden emin olun.
- Her bölgedeki sanal ağlar arasındaki ağ bağlantısını doğrulayın. Şu anda VNET 'ler arasındaki genel eşleme desteklenmiyor.  Bir ExpressRoute bağlantı hattı ile veya S2S VPN bağlantısı kullanarak sanal ağlar arasında bağlantı kurabilirsiniz. 
- Hedef Azure NetApp Files birimini oluşturun.
- Tercih ettiğiniz dosya kopyalama aracını kullanarak kaynak verileri hedef birime aktarın.

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box geçişi destekleniyor mu?

Hayır. Azure Data Box Şu anda Azure NetApp Files desteklemiyor. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Içeri/dışarı aktarma hizmeti ile geçiş destekleniyor mu?

Hayır. Azure Içeri/dışarı aktarma hizmeti şu anda Azure NetApp Files desteklemiyor.

## <a name="product-faqs"></a>Ürün SSS

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware çözümü (AVS) ile Azure NetApp Files NFS veya SMB birimleri kullanabilir miyim?

AVS Windows VM 'lerine veya Linux VM 'lerine Azure NetApp Files NFS birimleri bağlayabilirsiniz. AVS Windows VM 'lerinde Azure NetApp Files SMB paylaşımlarını eşleyebilirsiniz. Daha ayrıntılı bilgi için bkz. [Azure VMware çözümü ile Azure NetApp Files]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware çözümü (AVS) ile Azure NetApp Files NFS veya SMB birimi kullanımı için hangi bölgeler desteklenir?

Azure NetApp Files NFS veya AVS ile SMB birimleri kullanmak, aşağıdaki bölgelerde Doğu ABD, Batı ABD, Batı Avrupa ve Avustralya Doğu desteklenir.

## <a name="next-steps"></a>Sonraki adımlar  

- [Microsoft Azure ExpressRoute SSS](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Sanal Ağ SSS](../virtual-network/virtual-networks-faq.md)
- [Azure destek isteği oluşturma](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Azure NetApp Files için SMB performansı hakkında SSS](azure-netapp-files-smb-performance.md)
