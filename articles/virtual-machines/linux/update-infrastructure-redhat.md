---
title: Red Hat Güncelleştirme Altyapısı
description: Microsoft azure'da isteğe bağlı Red Hat Enterprise Linux örnekleri için Red Hat Update Infrastructure hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: b19ccad5254418092446aaf781d49fa7edf0e4f4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034316"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure'da isteğe bağlı Red Hat Enterprise Linux VM'ler için Red Hat güncelleştirme altyapısı
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) gibi Red Hat barındırılan depo içeriğini yansıtmak için özel depolar ile Azure özgü içerik oluşturmak ve son kullanıcı VM'ler için kullanılabilir hale getirmek amacıyla bulut sağlayıcıları sağlar.

Red Hat Enterprise Linux (RHEL) Kullandıkça Öde (PAYG) görüntüleri Azure RHUI erişmek için önceden yapılandırılmış olarak gelir. Ek bir yapılandırma gerekmez. En son güncelleştirmeleri almak için çalıştırın `sudo yum update` RHEL örneğinizin hazır olduktan sonra. Bu hizmet, RHEL PAYG yazılım ücretleri bir parçası olarak dahil edilir.

Azure 'da yayımlama ve bekletme ilkeleri de dahil olmak üzere RHEL görüntüleriyle ilgili ek bilgiler [burada](./rhel-images.md)bulunabilir.

Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.

## <a name="important-information-about-azure-rhui"></a>Azure RHUI hakkında önemli bilgiler

* Azure RHUı, Azure 'da oluşturulan tüm RHEL PAYG sanal makinelerini destekleyen güncelleştirme altyapısıdır. Bu, PAYG RHEL VM 'lerinizi abonelik Yöneticisi veya uydu ya da diğer güncelleştirme kaynakları ile kaydetmektir, ancak bunu bir PAYG VM ile yapmanız dolaylı çift faturalandırmaya neden olur. Ayrıntılar için aşağıdaki noktaya bakın.
* Azure'da barındırılan RHUI erişimi PAYG RHEL görüntüsü fiyatına dahildir. Azure'da barındırılan RHUI PAYG RHEL VM'den kaydını kaldırırsanız, sanal makinenin bir VM Getir-kendi lisansını (KLG) türü dönüştürmez. Başka bir güncelleştirme kaynağı ile aynı VM kaydolursanız, neden olabilecek _dolaylı_ çift ücretleri. İlk kez Azure RHEL yazılım ücreti karşılığında ücret ödersiniz. İkinci kez önceden satın alınan Red Hat abonelikler için ücret ödersiniz. Azure 'da barındırılan RHUı dışında bir güncelleştirme altyapısını sürekli olarak kullanmanız gerekiyorsa, [RHEL BYOS görüntülerini](https://aka.ms/rhel-byos)kullanmaya kaydolmayı göz önünde bulundurun.

* Azure 'daki RHEL SAP PAYG görüntüleri (RHEL for SAP, RHEL for SAP HANA ve RHEL for SAP Business Applications), SAP sertifikası için gereken belirli RHEL alt sürümünde kalan adanmış RHUı kanallarına bağlanır.

* İçindeki VM'ler için Azure'da barındırılan RHUI erişim sınırlıdır [Azure veri merkezi IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653). Proxy kullanıyorsanız tüm VM trafiğe bir şirket içi ağ altyapısı aracılığıyla RHEL PAYG sanal makinelerin Azure RHUI erişmek kullanıcı tanımlı rotalar ayarlama gerekebilir. Bu durumda, _Tüm_ rhuı IP adresleri için Kullanıcı tanımlı yolların eklenmesi gerekecektir.

## <a name="image-update-behavior"></a>Görüntü güncelleştirme davranışı

2019 Nisan itibariyle Azure, genişletilmiş güncelleştirme desteği (EUS) depolarına bağlı olan RHEL görüntülerini varsayılan olarak normal (EUS olmayan) depolara bağlı olan RHEL görüntülerini sağlar. RHEL EUS hakkında daha fazla ayrıntı, Red Hat 'in [Sürüm yaşam döngüsü belgelerinde](https://access.redhat.com/support/policy/updates/errata) ve [EUS belgelerinde](https://access.redhat.com/articles/rhel-eus)bulunabilir. `sudo yum update` varsayılan davranışı, farklı depolara farklı görüntüler bağlı olduğundan, hangi RHEL görüntüsüne sahip olduğunuza bağlı olarak değişir.

Tam görüntü listesi için Azure CLı kullanarak `az vm image list --publisher redhat --all` çalıştırın.

### <a name="images-connected-to-non-eus-repositories"></a>DUYMSUZ depolara bağlı görüntüler

Bir RHEL görüntüsünden, EUS depolarından bağlanan bir sanal makine sağlarsanız, `sudo yum update`çalıştırdığınızda en son RHEL alt sürümüne yükseltilir. Örneğin, bir RHEL 7,4 PAYG görüntüsünden bir VM sağlayıp `sudo yum update`çalıştırırsanız, bir RHEL 7,7 sanal makinesi (RHEL7 ailesinden en son ikincil sürüm) ile biter.

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

Bir RHEL görüntüsünden, EUS depolarına bağlı bir VM sağlarsanız, `sudo yum update`çalıştırdığınızda en son RHEL alt sürümüne yükseltmeyecektir. Bunun nedeni, EUS depolarına bağlı görüntülerin aynı zamanda belirli alt sürümlerine de sürüm kilitleneceğini sağlamalarıdır.

EUS depolarıyla bağlantılı görüntüler SKU 'da küçük bir sürüm numarası içerecektir. Örneğin, aşağıdaki resimlerin hepsi, EUS depolarında ekli olarak sunulur:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS ve sürüm kilitleme RHEL VM 'Leri

Bazı müşteriler VM sağlamaktan sonra RHEL VM 'lerini belirli bir RHEL alt sürümüne kilitlemek isteyebilir. Depoları genişletilmiş güncelleştirme desteği depolarına işaret etmek üzere güncelleştirerek RHEL VM 'nizi belirli bir alt sürüme taşıyabilirsiniz. Ayrıca, EUS sürüm kilitleme işlemini geri alabilirsiniz.

>[!NOTE]
> RHEL ek özellikleri üzerinde EUS desteklenmez. Bu, genellikle RHEL ek özellikler kanalından kullanılabilen bir paketi yüklüyorsanız, duyurken bunu yapamazsınız. Red Hat ek özellikleri ürün yaşam döngüsü [burada](https://access.redhat.com/support/policy/updates/extras/)ayrıntılı olarak verilmiştir.

Bu yazma sırasında, RHEL < = 7,3 için EUS desteği sona ermiştir. Daha fazla bilgi için [Red Hat belgelerindeki](https://access.redhat.com/support/policy/updates/errata/) "daha uzun destek eklentileri Red Hat Enterprise Linux" bölümüne bakın.
* RHEL 7,4 EUS desteği 31 Ağustos 2019 ' de bitiyor
* RHEL 7,5 EUS desteği 30 Nisan 2020 ' de bitiyor
* RHEL 7,6 EUS desteği 31 Ekim 2020 ' de bitiyor
* RHEL 7,7 EUS desteği 30 Ağustos 2021 ' de bitiyor

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM 'yi EUS 'ye değiştirme (belirli bir alt sürüme yönelik sürüm kilidi)
Bir RHEL VM 'yi belirli bir alt sürüme (farklı çalıştır kökü) kilitlemek için aşağıdaki yönergeleri kullanın:

>[!NOTE]
> Bu yalnızca EUS 'nin kullanılabildiği RHEL sürümleri için geçerlidir. Bu yazma sırasında, RHEL 7.2-7.7 içerir. Daha fazla ayrıntı [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.

1. EUS dışı depoları devre dışı bırak:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS depoları ekleyin:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Releasever değişkenini kilitle (farklı çalıştır kök):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Yukarıdaki yönerge, RHEL ikincil sürümünü geçerli küçük sürüme kilitler. Yükseltmek ve en son olmayan sonraki bir alt sürüme kilitlemek istiyorsanız, belirli bir ikincil sürüm girin. Örneğin, `echo 7.5 > /etc/yum/vars/releasever` RHEL sürümünüzü RHEL 7,5 olarak kilitleyecek

1. RHEL VM 'nizi güncelleştirme
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL VM 'yi EUS olmayan bir değere değiştirme (sürüm kilidini kaldırma)
Aşağıdakileri kök olarak çalıştırın:
1. Releasever dosyasını Kaldır:
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

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI içerik teslim sunucular için IP'ler

RHUI RHEL isteğe bağlı görüntüleri kullanılabilir olduğu tüm bölgelerde kullanılabilir. Şu anda listelenen tüm genel bölgelerde içerir [Azure durum Panosu](https://azure.microsoft.com/status/) sayfa, Azure ABD devlet kurumları ve Microsoft Azure Almanya bölgeleri.

Daha fazla RHEL PAYG Vm'lerden erişimi kısıtlamak için bir ağ yapılandırması kullanıyorsanız, emin aşağıdaki IP'leri verilir `yum update` nde olduğunuzdan ortamına bağlı olarak çalışmak için:


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

## <a name="azure-rhui-infrastructure"></a>Azure RHUı altyapısı


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Bir VM üzerinde süresi dolmuş RHUI istemci sertifikasını güncelleştir

Daha eski bir RHEL VM görüntüsü kullanıyorsanız (örneğin, RHEL 7,4 (görüntü URN: `RedHat:RHEL:7.4:7.4.2018010506`), süresi dolan bir SSL istemci sertifikası nedeniyle RHUı ile ilgili bağlantı sorunlarıyla karşılaşırsınız. Gördüğünüz hata _"SSL eşi sertifikanıza süre geçmiş olarak reddedildi"_ veya _"hata: depo meta verileri (repomd. xml) depo için alınamıyor:... Lütfen yolunu doğrulayıp yeniden deneyin "_ . Bu sorunu çözmek için lütfen aşağıdaki komutu kullanarak VM 'deki RHUı istemci paketini güncelleştirin:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternatif olarak, `sudo yum update` çalıştırmak, diğer depolarda "zaman aşımına uğradı SSL sertifikası" hatalarına rağmen istemci sertifika paketini de (RHEL sürümünüze bağlı olarak) güncelleştirebilir. Bu güncelleştirme başarılı olursa diğer RHUı depolarıyla normal bağlantı geri yüklenmelidir, bu nedenle `sudo yum update` başarıyla çalıştırabileceksiniz.

`yum update`çalıştırırken 404 hatasıyla çalıştırırsanız, yum önbelleğinizi yenilemek için aşağıdakileri deneyin:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Azure RHUI için bağlantı sorunlarını giderme
Azure RHEL PAYG VM'den Azure RHUI bağlanma konusunda sorunlar karşılaşırsanız, aşağıdaki adımları izleyin:

1. VM yapılandırması Azure RHUI uç noktası için inceleyin:

    1. Kontrol `/etc/yum.repos.d/rh-cloud.repo` dosyasını içeren bir başvuru `rhui-[1-3].microsoft.com` içinde `baseurl` , `[rhui-microsoft-azure-rhel*]` bölümü dosyasının. Varsa, yeni Azure RHUI kullanıyorsunuz.

    1. Aşağıdaki desene sahip bir konuma işaret ediyorsa `mirrorlist.*cds[1-4].cloudapp.net`, yapılandırma güncelleştirmesi gerekli değildir. Eski VM anlık görüntüsü kullanıyorsanız ve yeni Azure RHUI işaret edecek şekilde güncelleştirmeniz gerekir.

1. İçindeki sanal makineler için Azure'da barındırılan RHUI erişim sınırlıdır [Azure veri merkezi IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653).

1. Yeni yapılandırmayı kullanıyorsanız, VM Azure IP aralığından bağlanır ve Azure RHUI, dosya Microsoft ya da Red Hat destek servis talebi için bağlantı kurulamıyor doğrulanmıştır.

### <a name="infrastructure-update"></a>Altyapı Güncelleştirmesi

Eylül 2016'da, biz güncelleştirilmiş bir Azure RHUI dağıtıldı. Nisan 2017'de biz eski Azure RHUI kapatın. Ayrıca, PAYG RHEL görüntüleri (veya bunların anlık görüntülerini) Eylül 2016'dan veya üzerini kullanıyorsanız, otomatik olarak yeni Azure RHUI bağlanırsınız. Ancak, eski anlık görüntüleri Vm'lerinizde varsa, aşağıdaki bölümde açıklandığı gibi Azure RHUI erişmek için kendi yapılandırmasını el ile güncelleştirmeniz gerekir.

Yeni Azure RHUI sunuculara dağıtılan [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). Trafik Yöneticisi'nde, tek bir uç nokta (rhuı 1.microsoft.com) uygulama bölgesine bakılmaksızın herhangi bir VM tarafından kullanılabilir.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUI sunucularını kullanmak için el ile güncelleştirme yordamı
Bu yordam, yalnızca başvuru sağlanır. PAYG RHEL görüntüleri için Azure RHUI bağlanmak için doğru yapılandırma zaten var. Azure RHUI sunucularını kullanacak şekilde yapılandırmayı el ile güncelleştirmek için aşağıdaki adımları tamamlayın:

- RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Sonraki adımlar
* Azure Market PAYG görüntüden bir Red Hat Enterprise Linux VM oluşturma ve Azure'da barındırılan RHUI kullanılacak Git [Azure Marketi](https://azure.microsoft.com/marketplace/partners/redhat/).
* Azure 'daki Red Hat görüntüleri hakkında daha fazla bilgi edinmek için [Belgeler sayfasına](./rhel-images.md)gidin.
* Tüm RHEL sürümleri için Red Hat destek ilkeleriyle ilgili bilgiler [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasında bulunabilir.
