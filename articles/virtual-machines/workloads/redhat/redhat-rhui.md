---
title: Red Hat güncelleştirme altyapısı | Microsoft Docs
description: Microsoft Azure içindeki isteğe bağlı Red Hat Enterprise Linux örnekleri için Red Hat güncelleştirme altyapısı hakkında bilgi edinin
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 968377ed09996b9a717e0739a3de8355d1c8d88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677134"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure 'da isteğe bağlı Red Hat Enterprise Linux VM 'Ler için Red Hat güncelleştirme altyapısı
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (rhuı), Azure gibi bulut sağlayıcılarının Red Hat ile barındırılan depo içeriğini yansıtmasını, Azure 'a özgü içerikle özel depolar oluşturmasını ve Son Kullanıcı VM 'leri için kullanılabilir olmasını sağlar.

Red Hat Enterprise Linux (RHEL) Kullandıkça Öde (PAYG) görüntüleri, Azure RHUı 'a erişmek için önceden yapılandırılmış olarak gelir. Ek yapılandırma gerekmez. En son güncelleştirmeleri almak için, `sudo yum update` RHEL örneğinizi hazırladıktan sonra çalıştırın. Bu hizmet RHEL PAYG yazılım masraflarının bir parçası olarak dahil edilmiştir.

Azure 'da yayımlama ve bekletme ilkeleri de dahil olmak üzere RHEL görüntüleriyle ilgili ek bilgiler [burada](./redhat-images.md)bulunabilir.

Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.

> [!IMPORTANT]
> RHUı yalnızca Kullandıkça Öde (PAYG) görüntüleri için tasarlanmıştır. Kendi aboneliklerini getir (BYOS) olarak da bilinen özel ve altın görüntülerde, güncelleştirmelerin alınması için sistemin RHSM veya uydu 'e bağlanması gerekir. Daha fazla bilgi için bkz. [Red Hat makalesi](https://access.redhat.com/solutions/253273) .


## <a name="important-information-about-azure-rhui"></a>Azure RHUı hakkında önemli bilgiler

* Azure RHUı, Azure 'da oluşturulan tüm RHEL PAYG sanal makinelerini destekleyen güncelleştirme altyapısıdır. Bu, PAYG RHEL VM 'lerinizi abonelik Yöneticisi veya uydu ya da diğer güncelleştirme kaynakları ile kaydetmektir, ancak bunu bir PAYG VM ile yapmanız dolaylı çift faturalandırmaya neden olur. Ayrıntılar için aşağıdaki noktaya bakın.
* Azure 'da barındırılan RHUı erişim, RHEL PAYG görüntü fiyatına dahildir. Bir PAYG RHEL VM 'yi, sanal makineyi kendi lisansını getir (KLG) VM türüne dönüştürmez Azure 'da barındırılan RHUı kaydını kaldırırsanız. Aynı VM 'yi başka bir güncelleştirme kaynağıyla birlikte kaydettiğinizde _dolaylı_ çift ücretlere tabi olabilirsiniz. Azure RHEL yazılım ücreti için ilk kez ücretlendirilirsiniz. Daha önce satın alınan Red Hat abonelikleri için ikinci kez ücretlendirilirsiniz. Azure 'da barındırılan RHUı dışında bir güncelleştirme altyapısını sürekli olarak kullanmanız gerekiyorsa, [RHEL BYOS görüntülerini](./byos.md)kullanmaya kaydolmayı göz önünde bulundurun.

* Azure 'daki RHEL SAP PAYG görüntüleri (RHEL for SAP, RHEL for SAP HANA ve RHEL for SAP Business Applications), SAP sertifikası için gereken belirli RHEL alt sürümünde kalan adanmış RHUı kanallarına bağlanır.

* Azure 'da barındırılan RHUı erişim, [Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653)Içindeki VM 'lerle sınırlıdır. Tüm VM trafiğini şirket içi ağ altyapısı aracılığıyla Proxy kullanıyorsanız, Azure RHUı erişmek için RHEL PAYG VM 'Leri için Kullanıcı tanımlı yollar ayarlamanız gerekebilir. Bu durumda, _Tüm_ rhuı IP adresleri için Kullanıcı tanımlı yolların eklenmesi gerekecektir.


## <a name="image-update-behavior"></a>Görüntü güncelleştirme davranışı

2019 Nisan itibariyle Azure, genişletilmiş güncelleştirme desteği (EUS) depolarına bağlı olan RHEL görüntülerini varsayılan olarak normal (EUS olmayan) depolara bağlı olan RHEL görüntülerini sağlar. RHEL EUS hakkında daha fazla ayrıntı, Red Hat 'in [Sürüm yaşam döngüsü belgelerinde](https://access.redhat.com/support/policy/updates/errata) ve [EUS belgelerinde](https://access.redhat.com/articles/rhel-eus)bulunabilir. Varsayılan davranışı, farklı `sudo yum update` depolara farklı görüntüler bağlı olduğundan, hangi RHEL görüntüsüne sahip olduğunuza bağlı olarak değişir.

Tam görüntü listesi için `az vm image list --publisher redhat --all` Azure CLI kullanarak çalıştırın.

### <a name="images-connected-to-non-eus-repositories"></a>DUYMSUZ depolara bağlı görüntüler

Bir RHEL görüntüsünden sanal olmayan depolara bağlı bir VM sağlarsanız, çalıştırdığınızda en son RHEL alt sürümüne yükseltilecektir `sudo yum update` . Örneğin, bir RHEL 7,4 PAYG görüntüsünden bir VM sağlarsanız ve çalıştırırsanız `sudo yum update` , BIR rhel 7,8 sanal makinesi (RHEL7 ailesinden en son ikincil sürüm) ile biter.

EUS depolarından bağlantılı olan görüntüler SKU 'da küçük bir sürüm numarası içermez. SKU, URN 'deki üçüncü öğedir (görüntünün tam adı). Örneğin, aşağıdaki tüm görüntüler EUS depolarından ekli olarak sunulur:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

SKU 'Ların 7-LVM ya da 7-RAW olduğunu unutmayın. İkincil sürüm, bu görüntülerin sürümünde (URN 'deki dördüncü öğe) belirtilir.

### <a name="images-connected-to-eus-repositories"></a>EUS depolarıyla bağlantılı görüntüler

Bir RHEL görüntüsünden, EUS depolarında bağlı bir VM sağlarsanız, çalıştırdığınızda en son RHEL alt sürümüne yükseltmeyecektir `sudo yum update` . Bunun nedeni, EUS depolarına bağlı görüntülerin aynı zamanda belirli alt sürümlerine de sürüm kilitleneceğini sağlamalarıdır.

EUS depolarıyla bağlantılı görüntüler SKU 'da küçük bir sürüm numarası içerecektir. Örneğin, aşağıdaki resimlerin hepsi, EUS depolarında ekli olarak sunulur:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS ve sürüm kilitleme RHEL VM 'Leri

Genişletilmiş güncelleştirme desteği (EUS) depoları, VM sağlamaktan sonra RHEL VM 'lerini belirli bir RHEL alt sürümüne kilitlemek isteyen müşteriler tarafından kullanılabilir. Depoları genişletilmiş güncelleştirme desteği depolarına işaret etmek üzere güncelleştirerek RHEL VM 'nizi belirli bir alt sürüme taşıyabilirsiniz. Ayrıca, EUS sürüm kilitleme işlemini geri alabilirsiniz.

>[!NOTE]
> RHEL ek özellikleri üzerinde EUS desteklenmez. Bu, genellikle RHEL ek özellikler kanalından kullanılabilen bir paketi yüklüyorsanız, duyurken bunu yapamazsınız. Red Hat ek özellikleri ürün yaşam döngüsü [burada](https://access.redhat.com/support/policy/updates/extras/)ayrıntılı olarak verilmiştir.

Bu yazma sırasında, RHEL <= 7,4 için EUS desteği sona ermiştir. Daha fazla bilgi için [Red Hat belgelerindeki](https://access.redhat.com/support/policy/updates/errata/#Long_Support) "genişletilmiş bakım Red Hat Enterprise Linux" bölümüne bakın.
* RHEL 7,4 EUS desteği 31 Ağustos 2019 ' de bitiyor
* RHEL 7,5 EUS desteği 30 Nisan 2020 ' de bitiyor
* RHEL 7,6 EUS desteği 31 Mayıs 2021 ' de bitiyor
* RHEL 7,7 EUS desteği 30 Ağustos 2021 ' de bitiyor

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM 7. x ' i EUS 'ye (sürüm-kilit, belirli bir alt sürüme) değiştirme
Bir RHEL 7. x VM 'yi belirli bir alt sürüme (farklı çalıştır root) kilitlemek için aşağıdaki yönergeleri kullanın:

>[!NOTE]
> Bu yalnızca EUS 'nin kullanılabildiği RHEL 7. x sürümleri için geçerlidir. Bu yazma sırasında, RHEL 7.2-7.7 içerir. Daha fazla ayrıntı [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.

1. EUS dışı depoları devre dışı bırak:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS depoları ekleyin:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Değişkeni kilitle `releasever` (farklı çalıştır kökü):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Yukarıdaki yönerge, RHEL ikincil sürümünü geçerli küçük sürüme kilitler. Yükseltmek ve en son olmayan sonraki bir alt sürüme kilitlemek istiyorsanız, belirli bir ikincil sürüm girin. Örneğin, `echo 7.5 > /etc/yum/vars/releasever` RHEL sürümünüzü rhel 7,5 olarak kilitleyecek.

1. RHEL VM 'nizi güncelleştirme
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM 8. x ' i EUS 'ye (sürüm-kilit, belirli bir alt sürüme) değiştirme
Bir RHEL 8. x sanal makinesini belirli bir alt sürüme (farklı çalıştır root) kilitlemek için aşağıdaki yönergeleri kullanın:

>[!NOTE]
> Bu yalnızca EUS 'nin kullanılabildiği RHEL 8. x sürümleri için geçerlidir. Bu yazma sırasında, RHEL 8.1-8.2 ' i içerir. Daha fazla ayrıntı [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.

1. EUS dışı depoları devre dışı bırak:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. EUS depoları yapılandırma dosyasını alın:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. EUS depoları ekleyin:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. Değişkeni kilitle `releasever` (farklı çalıştır kökü):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Yukarıdaki yönerge, RHEL ikincil sürümünü geçerli küçük sürüme kilitler. Yükseltmek ve en son olmayan sonraki bir alt sürüme kilitlemek istiyorsanız, belirli bir ikincil sürüm girin. Örneğin, `echo 8.1 > /etc/yum/vars/releasever` RHEL sürümünüzü rhel 8,1 olarak kilitleyecek.

    >[!NOTE]
    > Releasever 'e erişim için izin sorunları varsa, ' nano/etc/yum/VARS/releaseve ' kullanarak dosyayı düzenleyebilir ve görüntü sürümü ayrıntılarını ekleyip kaydedebilirsiniz (' Ctrl + o ') ve ardından ENTER tuşuna basabilir ve ardından ' Ctrl + x ').  

1. RHEL VM 'nizi güncelleştirme
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL 7. x VM 'sini EUS olmayan bir değere değiştirme (sürüm kilidini kaldırma)
Aşağıdakileri kök olarak çalıştırın:
1. Dosyayı kaldır `releasever` :
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS depoları devre dışı bırak:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM 'yi yapılandırma
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. RHEL VM 'nizi güncelleştirme
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL 8. x VM 'sini EUS olmayan bir değere değiştirme (sürüm kilidini kaldırma)
Aşağıdakileri kök olarak çalıştırın:
1. Dosyayı kaldır `releasever` :
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS depoları devre dışı bırak:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. Düzenli depoları yapılandırma dosyasını al:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. EUS depoları ekleyin:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. RHEL VM 'nizi güncelleştirme
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUı içerik teslimi sunucularının IP 'Leri

RHUı, RHEL isteğe bağlı görüntülerinin kullanılabildiği tüm bölgelerde kullanılabilir. Şu anda [Azure Durum Panosu](https://azure.microsoft.com/status/) sayfasında, Azure ABD kamu ve Microsoft Azure Almanya bölgelerinde listelenen tüm ortak bölgeleri içerir.

RHEL PAYG VM 'lerinden erişimi daha fazla kısıtlamak için bir ağ yapılandırması kullanıyorsanız, aşağıdaki IP 'Lerin, `yum update` içindeki ortama bağlı olarak çalışmasına izin verildiğinden emin olun:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```
>[!NOTE]
>Yeni Azure ABD kamu görüntüleri, Ocak 2020 itibariyle yukarıdaki Azure genel üst bilgisinde bahsedilen genel IP 'yi kullanacaktır.

>[!NOTE]
>Ayrıca, Azure Almanya 'nın genel Almanya bölgelerinde kullanım dışı olduğunu unutmayın. Azure Almanya müşterileri için öneri [buradaki](#manual-update-procedure-to-use-the-azure-rhui-servers)adımları kullanarak public rhuı 'a işaret ediyor.

## <a name="azure-rhui-infrastructure"></a>Azure RHUı altyapısı


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>VM 'de RHUı istemci sertifikasını güncelleştirme zaman aşımına uğradı

Daha eski bir RHEL VM görüntüsü kullanıyorsanız (örneğin, RHEL 7,4 (görüntü URN: `RedHat:RHEL:7.4:7.4.2018010506` ), süresi dolan BIR TLS/SSL istemci sertifikası nedenıyle rhuı ile bağlantı sorunlarıyla karşılaşırsınız. Gördüğünüz hata _"SSL eşi sertifikanıza süre geçmiş olarak reddedildi"_ veya _"hata: depo meta verileri alınamıyor (repomd.xml):... Lütfen yolunu doğrulayıp yeniden deneyin "_. Bu sorunu çözmek için lütfen aşağıdaki komutu kullanarak VM 'deki RHUı istemci paketini güncelleştirin:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternatif olarak, `sudo yum update` aynı zamanda diğer depolar için göreceğiniz "zaman aşımına UĞRADı SSL sertifikası" hatalarına rağmen, çalışan istemci sertifika paketini (RHEL sürümünüze bağlı olarak) güncelleştirebilir. Bu güncelleştirme başarılı olursa diğer RHUı depolarıyla normal bağlantı geri yüklenmelidir, bu nedenle başarıyla çalıştırabileceksiniz `sudo yum update` .

Çalıştıran bir 404 hatasıyla çalıştırırsanız `yum update` , yum önbelleğinizi yenilemek için aşağıdakileri deneyin:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Azure RHUı ile bağlantı sorunlarını giderme
Azure RHEL PAYG VM 'nizden Azure RHUı ile bağlantı sorunlarıyla karşılaşırsanız, şu adımları izleyin:

1. Azure RHUı uç noktası için VM yapılandırmasını inceleyin:

    1. Dosyanın `/etc/yum.repos.d/rh-cloud.repo` `rhui-[1-3].microsoft.com` , `baseurl` dosyanın bölümünde öğesine bir başvuru içerip içerne olduğunu denetleyin `[rhui-microsoft-azure-rhel*]` . Bu, yeni Azure RHUı kullanıyorsunuz.

    1. Aşağıdaki düzene sahip bir konuma işaret ediyorsa, `mirrorlist.*cds[1-4].cloudapp.net` bir yapılandırma güncelleştirmesi gerekir. Eski VM anlık görüntüsünü kullanıyorsunuz ve yeni Azure RHUı işaret etmek için güncelleştirmeniz gerekiyor.

1. Azure 'da barındırılan RHUı erişim, [Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653)Içindeki VM 'lerle sınırlıdır.

1. Yeni yapılandırmayı kullanıyorsanız, VM 'nin Azure IP aralığından bağlandığını doğruladınız ve yine de Azure RHUı 'a bağlanamadığınızı, Microsoft veya Red Hat ile bir destek talebi dosyayı çözemiyorum.

### <a name="infrastructure-update"></a>Altyapı Güncelleştirmesi

Eylül 2016 ' de, güncelleştirilmiş bir Azure RHUı dağıttık. 2017 Nisan 'da eski Azure RHUı 'i kapattık. Eylül 2016 veya sonraki bir sürümünün RHEL PAYG görüntülerini (veya anlık görüntülerini) kullanıyorsanız, otomatik olarak yeni Azure RHUı 'e bağlanırsınız. Ancak, sanal makinelerinizde daha eski anlık görüntüleriniz varsa, aşağıdaki bölümde açıklandığı gibi, Azure RHUı 'a erişmek için yapılandırmalarını el ile güncelleştirmeniz gerekir.

Yeni Azure RHUı sunucuları, [azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)ile dağıtılır. Traffic Manager, tek bir uç nokta (rhui-1.microsoft.com), bölgeden bağımsız olarak tüm VM 'ler tarafından kullanılabilir.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUı sunucularını kullanmak için el ile güncelleştirme yordamı
Bu yordam yalnızca başvuru için verilmiştir. RHEL PAYG görüntüleri, Azure RHUı 'e bağlanmak için doğru yapılandırmaya zaten sahip. Yapılandırmayı Azure RHUı sunucularını kullanacak şekilde el ile güncelleştirmek için aşağıdaki adımları izleyin:

- RHEL 6 için:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- RHEL 7 için:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- RHEL 8 için:
    1. Bir yapılandırma dosyası oluşturun:
        ```bash
        vi rhel8.config
        ```
    1. Aşağıdaki içeriği yapılandırma dosyasına ekleyin:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Dosyayı kaydedin ve şu komutu çalıştırın:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. VM 'nizi güncelleştirme
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Sonraki adımlar
* Azure Marketi PAYG görüntüsünden bir Red Hat Enterprise Linux VM oluşturmak ve Azure 'da barındırılan RHUı kullanmak için [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6)' ne gidin.
* Azure 'daki Red Hat görüntüleri hakkında daha fazla bilgi edinmek için [Belgeler sayfasına](./redhat-images.md)gidin.
* Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.
