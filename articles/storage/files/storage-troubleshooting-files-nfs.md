---
title: Azure NFS dosya paylaşma sorunlarını giderme-Azure dosyaları
description: Azure NFS dosya paylaşma sorunlarını giderin.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99574474"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Azure NFS dosya paylaşımlarında sorun giderme

Bu makalede, Azure NFS dosya paylaşımları ile ilgili bazı yaygın sorunlar listelenmektedir. Bu sorunlarla karşılaşıldığında olası nedenleri ve geçici çözümleri sağlar.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" başarısız oldu: geçersiz bağımsız değişken (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Neden 1: ıdmapping devre dışı değil
Azure dosyaları alfasayısal UID/GID 'ye izin vermez. Bu nedenle ıdmapping devre dışı bırakılmalıdır. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Neden 2: ıdmapping devre dışı bırakıldı, ancak hatalı dosya/dizin adıyla karşılaşduktan sonra yeniden etkinleştirildi
Idmapping doğru şekilde devre dışı bırakılsa bile, bazı durumlarda ıdmapping 'i devre dışı bırakma ayarları geçersiz kılınır. Örneğin, Azure dosyaları hatalı bir dosya adıyla karşılaştığında bir hata geri gönderir. Bu özel hata kodunu gördükten sonra, NFS v 4,1 Linux istemcisi ıdmapping 'i yeniden etkinleştirmeye karar verir ve gelecekteki istekler alfasayısal UID/GID ile yeniden gönderilir. Azure dosyalarında desteklenmeyen karakterlerin bir listesi için, bu [makaleye](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)bakın. İki nokta üst üste, desteklenmeyen karakterlerden biridir. 

### <a name="workaround"></a>Geçici çözüm
Idmapping 'in devre dışı bırakılıp bırakılmadığını denetleyin ve hiçbir şey yeniden etkinleştirin ve ardından aşağıdakileri yapın:

- Paylaşımın bağlantısını kaldır
- # Echo Y >/sys/Module/NFS/Parameters/nfs4_disable_idmapping ile kimlik eşlemeyi devre dışı bırak
- Paylaşımdan geri bağlama
- Rsync çalıştırıyorsa, herhangi bir hatalı dizin/dosya adına sahip olmayan dizinden "— numeric-IDS" bağımsız değişkeniyle rsync ' i çalıştırın.

## <a name="unable-to-create-an-nfs-share"></a>NFS paylaşma oluşturulamıyor

### <a name="cause-1-subscription-is-not-enabled"></a>Neden 1: Abonelik etkin değil

Aboneliğiniz Azure dosyaları NFS önizlemesi için kaydedilmemiş olabilir. Özelliği etkinleştirmek için Cloud Shell veya yerel terminalden birkaç ek cmdlet 'i çalıştırmanız gerekir.

> [!NOTE]
> Kaydın tamamlanabilmesi için 30 dakikaya kadar beklemeniz gerekebilir.


#### <a name="solution"></a>Çözüm

Özelliği ve kaynak sağlayıcısını kaydetmek için aşağıdaki betiği kullanın, `<yourSubscriptionIDHere>` betiği çalıştırmadan önce değiştirin:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Neden 2: desteklenmeyen depolama hesabı ayarları

NFS yalnızca aşağıdaki yapılandırmaya sahip depolama hesaplarında kullanılabilir:

- Katman-Premium
- Hesap türü-dosya depolaması
- Bölgeler- [desteklenen bölgelerin listesi](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Çözüm

Makalemizdeki yönergeleri izleyin: [NFS paylaşma oluşturma](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Neden 3: depolama hesabı kayıt tamamlanmadan önce oluşturulmuştur

Bir depolama hesabının özelliği kullanabilmesi için, aboneliğin NFS kaydını tamamladıktan sonra oluşturulması gerekir. Kaydın tamamlanması 30 dakika kadar sürebilir.

#### <a name="solution"></a>Çözüm

Kayıt tamamlandığında, makalemizdeki yönergeleri izleyin: [NFS paylaşma oluşturma](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Azure NFS dosya paylaşımında bağlantı veya bağlama yapılamaz

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Neden 1: Istek güvenilmeyen bir ağ/güvenilir olmayan bir istemciden kaynaklı bir istemciden kaynaklanıp

SMB 'nin aksine, NFS Kullanıcı tabanlı kimlik doğrulamasına sahip değildir. Bir paylaşımın kimlik doğrulaması, ağ güvenliği kural yapılandırmanızı temel alır. Bu nedenle, NFS paylaşımınızda yalnızca güvenli bağlantıların kurulduğundan emin olmak için hizmet uç noktasını veya özel uç noktaları kullanmanız gerekir. Özel uç noktalara ek olarak şirket içi paylaşımlara paylaşımlara erişmek için VPN veya ExpressRoute 'u ayarlamanız gerekir. Güvenlik Duvarı için depolama hesabının izin verilenler-listesine eklenen IP 'Ler yok sayılır. NFS paylaşımının erişimini ayarlamak için aşağıdaki yöntemlerden birini kullanmanız gerekir:


- [Hizmet uç noktası](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Ortak uç nokta tarafından erişilen
    - Yalnızca aynı bölgede kullanılabilir.
    - VNet eşlemesi, paylaşımınıza erişim vermez.
    - Her bir sanal ağ veya alt ağın, izin verilenler-listesine tek tek eklenmesi gerekir.
    - Şirket içi erişim için, hizmet uç noktaları ExpressRoute, Noktadan siteye ve siteden siteye VPN 'Ler ile kullanılabilir ancak daha güvenli olduğundan özel uç nokta kullanmanızı öneririz.

Aşağıdaki diyagramda genel uç noktalar kullanılarak bağlantı gösterilmektedir.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Genel uç nokta bağlantısının diyagramı." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Özel uç nokta](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Erişim, hizmet uç noktasından daha güvenlidir.
    - Özel bağlantı aracılığıyla NFS paylaşımının erişimine, depolama hesabının Azure bölgesinin içinden ve dışından (Şirket içi, şirket içi) erişilebilir
    - Özel uç noktada barındırılan sanal ağlarla sanal ağ eşlemesi, NFS 'nin eşlenmiş sanal ağlardaki istemcilere erişimini sağlar.
    - Özel uç noktalar ExpressRoute, Noktadan siteye ve siteden siteye VPN 'Ler ile kullanılabilir.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Özel uç nokta bağlantısının diyagramı." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Neden 2: güvenli aktarım gereklidir

Çift şifreleme henüz NFS paylaşımları için desteklenmiyor. Azure, MACSec kullanarak Azure veri merkezleri arasında geçiş halindeki tüm veriler için bir şifreleme katmanı sağlar. NFS paylaşımlarına yalnızca güvenilen sanal ağlardan ve VPN tünelleri üzerinden erişilebilir. NFS paylaşımlarında ek aktarım katmanı şifrelemesi yok.

#### <a name="solution"></a>Çözüm

Depolama hesabınızın yapılandırma dikey penceresinde gerekli olan güvenli aktarımı devre dışı bırakın.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Depolama hesabı yapılandırma dikey penceresinin ekran görüntüsü, güvenli aktarım devre dışı bırakılıyor.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Neden 3: NFS-ortak paket yüklü değil
Mount komutunu çalıştırmadan önce aşağıdan belirli bir komutu çalıştırarak paketi yüklemelisiniz.

NFS paketinin yüklü olup olmadığını denetlemek için şunu çalıştırın: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Çözüm

Paket yüklü değilse, paketi dağıtıma yükler.

##### <a name="ubuntu-or-debian"></a>Ubuntu veya deni

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

DNF paket yöneticisini kullanın: `sudo dnf install nfs-common` .

Red Hat Enterprise Linux ve CentOS 'ın eski sürümleri yum paket yöneticisini kullanır: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Her paket yöneticisi için zypper kullanın: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Neden 4: güvenlik duvarı engelleme bağlantı noktası 2049

NFS protokolü, bağlantı noktası 2049 üzerinden sunucusuyla iletişim kurar, bu bağlantı noktasının depolama hesabına (NFS sunucusu) açık olduğundan emin olun.

#### <a name="solution"></a>Çözüm

Aşağıdaki komutu çalıştırarak 2049 numaralı bağlantı noktasının istemcisinde açık olduğunu doğrulayın: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Bağlantı noktası açık değilse, açın.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls (liste dosyaları) hatalı/tutarsız sonuçları gösterir

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Neden: dosya tanıtıcısı açıkken, önbelleğe alınmış değerler ve sunucu dosyası meta verileri değerleri arasında tutarsızlık
Bazen "liste dosyaları" komutu, beklenen şekilde sıfır olmayan bir boyut görüntüler ve çok sonraki liste dosyaları komutunda, boyut 0 veya çok eski bir zaman damgası gösterilir. Dosya açıkken dosya meta verileri değerlerinin tutarsız şekilde önbelleğe alınması nedeniyle bu bilinen bir sorundur. Bu sorunu çözmek için aşağıdaki geçici çözümlerden birini kullanabilirsiniz:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Geçici çözüm 1: dosya boyutunu getirmek Için ls-l yerine WC-c kullanın
WC-c kullanmak her zaman sunucudan en son değeri getirecek ve herhangi bir tutarsızlık olmayacaktır.

#### <a name="workaround-2-use-noac-mount-flag"></a>Geçici çözüm 2: "NOAC" bağlama bayrağını kullanın
Mount komutunuz ile "NOAC" bayrağını kullanarak dosya sistemini uzaktan bağlayın. Bu, her zaman tüm meta veri değerlerini sunucudan getirir. Bu geçici çözüm kullanılırsa tüm meta veri işlemleri için bazı küçük performans yükü olabilir.

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun.
Hala yardıma ihtiyacınız varsa, sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
