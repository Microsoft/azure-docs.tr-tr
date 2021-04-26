---
title: Azure 'da Red Hat Enterprise Linux görüntülerini yerinde yükseltme
description: Red Hat Enterprise 7. x görüntülerinden en son 8. x sürümüne yerinde yükseltme yapmayı öğrenin.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676074"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Yerinde yükseltmeleri Red Hat Enterprise Linux

Bu makalede Red Hat Enterprise Linux (RHEL) 7 ' den Red Hat Enterprise Linux 8 ' e yerinde yükseltmenin nasıl yapılacağı hakkında yönergeler sağlanmaktadır. Yönergeler, `leapp` Azure 'daki aracı kullanır. Yerinde yükseltme sırasında, var olan RHEL 7 işletim sistemi RHEL 8 sürümü ile değiştirilmiştir.

>[!Note] 
> Red Hat Enterprise Linux SQL Server teklifleri, Azure 'da yerinde yükseltmeleri desteklemez.

## <a name="what-to-expect-during-the-upgrade"></a>Yükseltme sırasında beklenmeniz gerekenler
Yükseltme sırasında sistem birkaç kez yeniden başlatılır. Son yeniden başlatma, VM 'yi RHEL 8 en son küçük sürümüne yükseltir. 

Yükseltme işlemi 20 dakikadan 2 saate kadar herhangi bir zaman alabilir. Toplam süre, VM boyutu ve sistemde yüklü paketlerin sayısı gibi çeşitli faktörlere bağlıdır.

## <a name="preparations"></a>Lıkların
Red Hat ve Azure, bir sistemi sonraki ana sürüme geçirmeye yönelik yerinde yükseltme kullanılmasını önerir. 

Yükseltmeye başlamadan önce aşağıdaki noktaları göz önünde bulundurun. 

>[!Important] 
> Yükseltmeye başlamadan önce görüntünün anlık görüntüsünü alın.

* En son RHEL 7 sürümünü kullandığınızdan emin olun. Şu anda en son sürüm RHEL 7,9 ' dir. Kilitli bir sürüm kullanıyorsanız ve RHEL 7,9 sürümüne yükseltirsiniz, [Bu adımları izleyerek BIR EUS (genişletilmiş güncelleştirme desteği) deposuna geçiş yapın](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Yükseltinizi denetlemek ve başarıyla bitirilip bitirilmeyeceğini görmek için aşağıdaki komutu çalıştırın. Komutun */var/log/liapp/leapp-report.txt* dosyası oluşturması gerekir. Bu dosya, işlemi, neler olduğunu ve yükseltmenin mümkün olup olmadığını açıklar.

    >[!NOTE]
    > Bu makaledeki komutları çalıştırmak için kök hesabını kullanın. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Seri konsolunun işlevsel olduğundan emin olun. Yükseltme işlemi sırasında bu konsolu izlemek için kullanacaksınız.

* */Etc/ssh/sshd_config*'de SSH kök erişimini etkinleştir:
    1. */Etc/ssh/sshd_config* dosyasını açın.
    1. `#PermitRootLogin yes` arayın.
    1. Dizenin açıklamasını kaldırmak için sayı işaretini ( `#` ) kaldırın.

## <a name="upgrade-steps"></a>Yükseltme adımları

Bu adımları dikkatle izleyin. Üretim örneklerinde denemeden önce bir test makinesinde yükseltmeyi denemenizi öneririz.

1. `yum`En son istemci paketlerini getirmek için bir güncelleştirme yapın.
    ```bash
    yum update -y
    ```

1. Yüklemesini yapın `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. [Red Hat portalında](https://access.redhat.com/articles/3664871) *repomap.csv* ve *pes-events.js* içeren *leapp-Data. tar. gz* dosyasını edinin. *Leapp-Data. tar. gz* dosyasını ayıklayın.
    1. *Leapp-Data. tar. gz* dosyasını indirin.
    1. İçeriği ayıklayın ve dosyayı kaldırın. Aşağıdaki komutu kullanın:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. İçin bir `answers` dosya ekleyin `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Yükseltmeyi başlatın.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade`Komut başarıyla tamamlandıktan sonra, işlemi gerçekleştirmek için sistemi el ile yeniden başlatın. Sistem birkaç kez yeniden başlatıldığı için kullanılamaz. Seri konsolunu kullanarak işlemi izleyin.

1.  Yükseltmenin başarıyla tamamlandığını doğrulayın.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Yükseltme tamamlandığında kök SSH erişimini kaldırın:
    1. */Etc/ssh/sshd_config* dosyasını açın.
    1. `#PermitRootLogin yes` arayın.
    1. Dizeyi açıklamaya yönelik bir sayı işareti ( `#` ) ekleyin.

1. Değişiklikleri uygulamak için SSHD hizmetini yeniden başlatın.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Sık karşılaşılan sorunlar

Aşağıdaki hatalar genellikle `leapp preupgrade` işlem başarısız olduğunda ya da işlem başarısız olduğunda meydana gelir `leapp upgrade` :

* **Hata**: aşağıdaki devre dışı eklenti desenleri için eşleşme bulunamadı.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Çözüm**: Abonelik Yöneticisi eklentisini devre dışı bırakın. */Etc/yum/pluginconf.d/Subscription-Manager.conf* dosyasını düzenleyerek ve olarak değiştirerek devre dışı bırakın `enabled` `enabled=0` .

    Bu hata, `yum` etkin olan abonelik Yöneticisi eklentisi VM 'ler için kullanılmazsa oluşur `PAYG` .

* **Hata**: kök kullanılarak uzaktan oturum açmada olası sorunlar.

    Başarısız olduğunda şu hatayla karşılaşabilirsiniz `leapp preupgrade` :

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Çözüm**: */etc/sshd_config* içinde kök erişimi etkinleştirin.

    Bu hata, */etc/sshd_config* IÇINDE kök SSH erişimi etkinleştirilmediğinde meydana gelir. Daha fazla bilgi için bu makaledeki [hazırlıklar](#preparations) bölümüne bakın. 


## <a name="next-steps"></a>Sonraki adımlar
* [Azure 'Da Red Hat görüntüleri](./redhat-images.md)hakkında daha fazla bilgi edinin.
* [Red Hat güncelleştirme altyapısı](./redhat-rhui.md)hakkında daha fazla bilgi edinin.
* [RHEL BYOS teklifi](./byos.md)hakkında daha fazla bilgi edinin.
* Red Hat yerinde yükseltme işlemi hakkında daha fazla bilgi edinmek için Red Hat belgelerindeki [RHEL 7 ' den RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) ' e yükseltme konusuna bakın.
* Tüm RHEL sürümleri için Red Hat destek ilkeleri hakkında daha fazla bilgi edinmek için Red Hat belgelerindeki [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) ' ne bakın.