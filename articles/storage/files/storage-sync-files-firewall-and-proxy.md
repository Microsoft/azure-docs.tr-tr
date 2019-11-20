---
title: Şirket içi güvenlik duvarı ve proxy ayarlarını Azure Dosya Eşitleme | Microsoft Docs
description: Şirket içi ağ yapılandırması Azure Dosya Eşitleme
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4f37c54699329f43a5bbdd5c4543ae3a7b2166f5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048841"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure Dosya Eşitleme proxy’si ve güvenli duvarı ayarları
Azure Dosya Eşitleme, şirket içi sunucularınızı Azure dosyalarına bağlayarak çok siteli eşitlemeyi ve bulut katmanlama özelliklerini etkinleştirir. Bu nedenle, bir şirket içi sunucu internet 'e bağlı olmalıdır. BT yöneticisinin, sunucunun Azure Cloud Services 'e ulaşması için en iyi yolu karar vermesini gerektirir.

Bu makale, başarıyla sunulan belirli gereksinimlere ve seçeneklere ilişkin öngörüler sağlar ve sunucunuzu Azure Dosya Eşitleme için güvenli bir şekilde bağlayın.

## <a name="overview"></a>Genel Bakış
Azure Dosya Eşitleme, Windows sunucunuz, Azure dosya paylaşımınız ve diğer birçok Azure hizmeti arasında, eşitleme grubunuzda açıklandığı gibi verileri eşitlemek için bir Orchestration hizmeti görevi görür. Azure Dosya Eşitleme düzgün şekilde çalışması için sunucularınızı aşağıdaki Azure hizmetleriyle iletişim kuracak şekilde yapılandırmanız gerekecektir:

- Azure Storage
- Azure Dosya Eşitleme
- Azure Resource Manager
- Kimlik doğrulama hizmetleri

> [!Note]  
> Windows Server 'daki Azure Dosya Eşitleme Aracısı, bulut hizmetlerine yönelik tüm istekleri başlatır ve yalnızca bir güvenlik duvarı perspektifinden giden trafiği göz önünde bulundurmasına neden olur. <br /> Azure hizmeti Azure Dosya Eşitleme aracısıyla bir bağlantı başlatır.

## <a name="ports"></a>Bağlantı Noktaları
Azure Dosya Eşitleme dosya verilerini ve meta verileri HTTPS üzerinden özel olarak taşımalıdır ve 443 numaralı bağlantı noktasını açık giden şekilde gerektirir.
Sonuç olarak tüm trafik şifrelenir.

## <a name="networks-and-special-connections-to-azure"></a>Azure ile ağlar ve özel bağlantılar
Azure Dosya Eşitleme aracısının [ExpressRoute](../../expressroute/expressroute-introduction.md)vb. gibi özel kanallarla ilgili hiçbir gereksinimi yoktur.

Azure Dosya Eşitleme, Azure 'a erişime izin veren ve bant genişliği, gecikme süresi gibi çeşitli ağ özelliklerine otomatik olarak uyum sağlayan ve ince ayar için yönetici denetimi sunan tüm yollarla çalışacaktır. Şu anda tüm özellikler kullanılamaz. Belirli davranışı yapılandırmak isterseniz, [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670)aracılığıyla bize bilgi verin.

## <a name="proxy"></a>Ara sunucu
Azure Dosya Eşitleme, uygulamaya özgü ve makine genelindeki proxy ayarlarını destekler.

**Uygulamaya özgü ara sunucu ayarları** , bir proxy 'nin özel olarak Azure dosya eşitleme trafiği yapılandırmasına izin verir. Uygulamaya özgü ara sunucu ayarları, aracı sürümü 4.0.1.0 veya daha yeni bir sürümde desteklenir ve aracı yüklemesi sırasında veya set-StorageSyncProxyConfiguration PowerShell cmdlet 'i kullanılarak yapılandırılabilir.

Uygulamaya özgü ara sunucu ayarlarını yapılandırmak için PowerShell komutları:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Sunucunun tüm trafiği proxy üzerinden yönlendirilirken, **makine genelindeki proxy ayarları** Azure dosya eşitleme aracısına saydamdır.

Makine genelindeki proxy ayarlarını yapılandırmak için aşağıdaki adımları izleyin: 

1. .NET uygulamaları için proxy ayarlarını yapılandırma 

   - Şu iki dosyayı düzenleyin:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Machine. config dosyalarına < System. net > bölümünü ekleyin (< System. serviceModel > bölümünün altında).  127.0.01:8888 öğesini, proxy sunucu için IP adresine ve bağlantı noktasına değiştirin. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. WinHTTP proxy ayarlarını ayarla 

   - Mevcut proxy ayarını görmek için, yükseltilmiş bir komut isteminden veya PowerShell 'den aşağıdaki komutu çalıştırın:   

     Netsh WinHTTP proxy göster

   - Proxy ayarını ayarlamak için yükseltilmiş bir komut isteminden veya PowerShell 'den aşağıdaki komutu çalıştırın (127.0.01:8888 öğesini ara sunucu için IP adresini ve bağlantı noktasını değiştirin):  

     Netsh WinHTTP set proxy 127.0.0.1:8888

3. Yükseltilmiş bir komut isteminden veya PowerShell 'ten aşağıdaki komutu çalıştırarak depolama eşitleme Aracısı hizmetini yeniden başlatın: 

      net stop filesyncsvc

      Note: depolama eşitleme Aracısı (filesyncsvc) hizmeti durdurulduktan sonra otomatik olarak başlayacak.

## <a name="firewall"></a>Güvenlik duvarı
Önceki bölümde belirtildiği gibi, 443 numaralı bağlantı noktasının giden trafik açık olması gerekir. Veri merkezinizdeki, dalınızdaki veya bölgenizdeki ilkelere bağlı olarak, bu bağlantı noktası üzerinden trafiği belirli etki alanlarına kısıtlamak istenebilir veya gerekli olabilir.

Aşağıdaki tabloda iletişim için gerekli etki alanları açıklanmaktadır:

| Hizmet | Genel bulut uç noktası | Azure Kamu uç noktası | Kullanım |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Herhangi bir kullanıcı çağrısı (PowerShell gibi), ilk sunucu kayıt çağrısı da dahil olmak üzere bu URL 'ye gider. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | Azure Resource Manager çağrılarının kimliği doğrulanmış bir kullanıcı tarafından yapılması gerekir. Başarılı olmak için, bu URL kullanıcı kimlik doğrulaması için kullanılır. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Azure Dosya Eşitleme dağıtmanın bir parçası olarak, aboneliğin Azure Active Directory bir hizmet sorumlusu oluşturulur. Bu URL bunun için kullanılır. Bu asıl, Azure Dosya Eşitleme hizmetine en düşük düzeyde haklar vermek için kullanılır. Azure Dosya Eşitleme ilk kurulumunu gerçekleştiren kullanıcı, abonelik sahibi ayrıcalıklarına sahip kimliği doğrulanmış bir kullanıcı olmalıdır. |
| **Azure depolama alanı** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Sunucu bir dosyayı indirdiğinde, sunucu bu veri hareketini depolama hesabındaki Azure dosya paylaşımından doğrudan görüşüp daha verimli bir şekilde gerçekleştirir. Sunucuda yalnızca hedeflenen dosya paylaşımında erişime izin veren bir SAS anahtarı vardır. |
| **Azure Dosya Eşitleme** | &ast;.one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | İlk sunucu kaydından sonra, sunucu, bu bölgedeki Azure Dosya Eşitleme hizmet örneği için bölgesel bir URL alır. Sunucu, eşitlemesini işleme örneği ile doğrudan ve verimli bir şekilde iletişim kurmak için URL 'YI kullanabilir. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Azure Dosya Eşitleme aracısı yüklendikten sonra, Azure Dosya Eşitleme hizmeti ve Azure dosya paylaşımıyla iletişim kurmak için gereken ara sertifikaları indirmek için PKI URL 'SI kullanılır. Bir sertifikanın durumunu denetlemek için OCSP URL 'SI kullanılır. |

> [!Important]
> Trafiğe izin verirken, sunucudan yalnızca eşitleme hizmetinden daha fazlasına trafik &ast;. Alt etki alanları altında çok daha fazla Microsoft hizmeti mevcuttur.

&ast;. one.microsoft.com çok geniş ise, Azure dosya eşitleme hizmeti 'nin yalnızca açık bölgesel örneklerine iletişime izin vererek sunucunun iletişimini sınırlayabilirsiniz. Hangi örnek seçme, dağıttığınız ve sunucusuna kaydettiğiniz depolama eşitleme hizmeti bölgesine bağlıdır. Bu bölgeye aşağıdaki tabloda "birincil uç nokta URL 'SI" adı verilir.

İş sürekliliği ve olağanüstü durum kurtarma (BCDR) nedenleriyle Azure dosya paylaşımlarınızı küresel olarak yedekli (GRS) depolama hesabında belirtmiş olabilirsiniz. Böyle bir durum söz konusu ise, Azure dosya paylaşımlarınız, coğrafi bölge kesintisi durumunda eşleştirilmiş bölgeye yük devreder. Azure Dosya Eşitleme depolama ile aynı bölgesel eşleştirmeleri kullanır. Bu nedenle, GRS depolama hesapları kullanıyorsanız, sunucunuzun Azure Dosya Eşitleme eşleştirilmiş bölge ile iletişim kurmasına izin vermek için ek URL 'Ler etkinleştirmeniz gerekir. Aşağıdaki tablo bu "eşleştirilmiş bölgeyi" çağırır. Ayrıca, aynı zamanda etkinleştirilmesi gereken bir Traffic Manager profil URL 'SI vardır. Bu, ağ trafiğinin yük devretme olayında eşleştirilmiş bölgeye sorunsuz bir şekilde yeniden yönlendirilmesini ve aşağıdaki tabloda "keşif URL 'SI" olarak adlandırılmasına olanak sağlar.

| Bulut  | Bölge | Birincil uç nokta URL 'SI | Eşleştirilmiş bölge | Bulma URL 'SI |
|--------|--------|----------------------|---------------|---------------|
| Genel |Avustralya Doğu | https:\//kailani-aue.one.microsoft.com | Avustralya Güneydoğu | https:\//tm-kailani-aue.one.microsoft.com |
| Genel |Avustralya Güneydoğu | https:\//kailani-aus.one.microsoft.com | Avustralya Doğu | https:\//tm-kailani-aus.one.microsoft.com |
| Genel | Güney Brezilya | https:\//brazilsouth01.afs.azure.net | Orta Güney ABD | https:\//tm-brazilsouth01.afs.azure.net |
| Genel | Orta Kanada | https:\//kailani-cac.one.microsoft.com | Doğu Kanada | https:\//tm-kailani-cac.one.microsoft.com |
| Genel | Doğu Kanada | https:\//kailani-cae.one.microsoft.com | Orta Kanada | https:\//tm-kailani.cae.one.microsoft.com |
| Genel | Orta Hindistan | https:\//kailani-cin.one.microsoft.com | Güney Hindistan | https:\//tm-kailani-cin.one.microsoft.com |
| Genel | Orta ABD | https:\//kailani-cus.one.microsoft.com | Doğu ABD 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Genel | Doğu Asya | https:\//kailani11.one.microsoft.com | Güneydoğu Asya | https:\//tm-kailani11.one.microsoft.com |
| Genel | Doğu ABD | https:\//kailani1.one.microsoft.com | Batı ABD | https:\//tm-kailani1.one.microsoft.com |
| Genel | Doğu ABD 2 | https:\//kailani-ess.one.microsoft.com | Orta ABD | https:\//tm-kailani-ess.one.microsoft.com |
| Genel | Japonya Doğu | https:\//japaneast01.afs.azure.net | Japonya Batı | https:\//tm-japaneast01.afs.azure.net |
| Genel | Japonya Batı | https:\//japanwest01.afs.azure.net | Japonya Doğu | https:\//tm-japanwest01.afs.azure.net |
| Genel | Kore Orta | https:\//koreacentral01.afs.azure.net/ | Kore Güney | https:\//tm-koreacentral01.afs.azure.net/ |
| Genel | Kore Güney | https:\//koreasouth01.afs.azure.net/ | Kore Orta | https:\//tm-koreasouth01.afs.azure.net/ |
| Genel | Orta Kuzey ABD | https:\//northcentralus01.afs.azure.net | Orta Güney ABD | https:\//tm-northcentralus01.afs.azure.net |
| Genel | Kuzey Avrupa | https:\//kailani7.one.microsoft.com | Batı Avrupa | https:\//tm-kailani7.one.microsoft.com |
| Genel | Orta Güney ABD | https:\//southcentralus01.afs.azure.net | Orta Kuzey ABD | https:\//tm-southcentralus01.afs.azure.net |
| Genel | Güney Hindistan | https:\//kailani-sin.one.microsoft.com | Orta Hindistan | https:\//tm-kailani-sin.one.microsoft.com |
| Genel | Güneydoğu Asya | https:\//kailani10.one.microsoft.com | Doğu Asya | https:\//tm-kailani10.one.microsoft.com |
| Genel | Birleşik Krallık Güney | https:\//kailani-uks.one.microsoft.com | Birleşik Krallık Batı | https:\//tm-kailani-uks.one.microsoft.com |
| Genel | Birleşik Krallık Batı | https:\//kailani-ukw.one.microsoft.com | Birleşik Krallık Güney | https:\//tm-kailani-ukw.one.microsoft.com |
| Genel | Batı Orta ABD | https:\//westcentralus01.afs.azure.net | Batı ABD 2 | https:\//tm-westcentralus01.afs.azure.net |
| Genel | Batı Avrupa | https:\//kailani6.one.microsoft.com | Kuzey Avrupa | https:\//tm-kailani6.one.microsoft.com |
| Genel | Batı ABD | https:\//kailani.one.microsoft.com | Doğu ABD | https:\//tm-kailani.one.microsoft.com |
| Genel | Batı ABD 2 | https:\//westus201.afs.azure.net | Batı Orta ABD | https:\//tm-westus201.afs.azure.net |
| Devlet | ABD Devleti Arizona | https:\//usgovarizona01.afs.azure.us | ABD Devleti Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Devlet | ABD Devleti Texas | https:\//usgovtexas01.afs.azure.us | ABD Devleti Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Yerel olarak yedekli (LRS) veya bölge yedekli (ZRS) depolama hesapları kullanıyorsanız, yalnızca "birincil uç nokta URL 'SI" altında listelenen URL 'YI etkinleştirmeniz gerekir.

- Küresel olarak yedekli (GRS) depolama hesapları kullanıyorsanız, üç URL 'yi etkinleştirin.

**Örnek:** `"West US"` ' de bir depolama eşitleme hizmeti dağıtır ve sunucunuzu bu sunucuya kaydedersiniz. Sunucunun bu durum için iletişim kurmasına izin veren URL 'Ler şunlardır:

> - https:\//kailani.one.microsoft.com (birincil uç nokta: Batı ABD)
> - https:\//kailani1.one.microsoft.com (eşleştirilmiş başarısız bölge: Doğu ABD)
> - https:\//tm-kailani.one.microsoft.com (birincil bölgenin bulma URL 'SI)

## <a name="summary-and-risk-limitation"></a>Özet ve risk sınırlaması
Bu belgede daha önce bulunan listeler, şu anda iletişim kuran Azure Dosya Eşitleme URL 'Leri içerir. Güvenlik duvarlarının bu etki alanlarına giden trafiğe izin alabilmesi gerekir. Microsoft bu listeyi güncel tutmaya devam eden bir çaba harcar.

Etki alanı sınırlandırma güvenlik duvarı kurallarını ayarlamak, güvenliği artırmak için bir ölçü olabilir. Bu güvenlik duvarı yapılandırmalarının kullanılması durumunda, URL 'Lerin ekleneceğini ve zaman içinde değişebileceğini göz önünde bulundurmanız gerekir. Bu makaleye düzenli olarak bakın.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Dosya Eşitleme dağıtımı planlama](storage-sync-files-planning.md)
- [Azure Dosya Eşitleme’yi dağıtma](storage-sync-files-deployment-guide.md)
- [Azure Dosya Eşitleme’yi izleme](storage-sync-files-monitoring.md)
