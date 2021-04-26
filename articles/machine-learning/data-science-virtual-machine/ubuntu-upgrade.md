---
title: Veri Bilimi Sanal Makinesi Ubuntu 18,04 ' ye yükseltme
titleSuffix: Azure Data Science Virtual Machine
description: CentOS ve Ubuntu 16,04 ' dan en son Ubuntu 18,04 Veri Bilimi Sanal Makinesi yükseltmeyi öğrenin.
keywords: derin öğrenme, AI, veri bilimi araçları, veri bilimi sanal makinesi, ekip veri bilimi işlemi
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101659469"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Veri Bilimi Sanal Makinenizi Ubuntu 18.04’e yükseltme

Ubuntu 16,04 veya CentOS gibi eski bir sürümü çalıştıran bir Veri Bilimi Sanal Makinesi varsa DSVM 'nizi Ubuntu 18,04 ' ye geçirmeniz gerekir. Geçiş, en son işletim sistemi düzeltme eklerini, sürücüleri, önceden yüklenmiş yazılımları ve kitaplık sürümlerini almanızı sağlayacaktır. Bu belgede, Ubuntu veya CentOS 'ın eski sürümlerinden nasıl geçiş yapılacağı açıklanır. 

## <a name="prerequisites"></a>Önkoşullar

- SSH ve Linux komut satırıyla benzerlik

## <a name="overview"></a>Genel Bakış

İki olası geçiş yöntemi vardır:

- Yerinde geçiş, "aynı sunucu" geçişi olarak da adlandırılır. Bu geçiş, mevcut VM 'yi yeni bir sanal makine oluşturmadan yükseltir. Yerinde geçiş, Ubuntu 16,04 ' den Ubuntu 18,04 ' ye geçişin daha kolay yoludur.
- Yan yana geçiş, "sunucular arası" geçiş olarak da adlandırılır. Bu geçiş, mevcut sanal makineden yeni oluşturulan bir VM 'ye veri aktarır. Yan yana geçiş, CentOS 'dan Ubuntu 18,04 'e geçişin yoludur. Eski yüklemenizi gereksiz bir şekilde karışık hale geçirdiğinizi düşünüyorsanız, Ubuntu sürümleri arasında yükseltme için yan yana geçişi tercih edebilirsiniz.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>VM 'nizi geri almanız gereken durumlarda anlık görüntü yapın

Azure portal, **anlık görüntüler** işlevini bulmak için arama çubuğunu kullanın. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Azure portal ve arama çubuğunun gösterildiği, * * anlık görüntülerle * * vurgulanmış ekran görüntüsü":::

1. **Ekle**' yi seçerek **anlık görüntü oluşturma** sayfasına götürür. Sanal makinenizin aboneliğini ve kaynak grubunu seçin. **Bölge** için, hedef depolamanın bulunduğu bölgeyi seçin. DSVM depolama diskini ve ek yedekleme seçeneklerini belirleyin. **Standart HDD** , bu yedekleme senaryosu için uygun bir depolama türüdür.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="' Anlık görüntü oluşturma ' seçeneklerini gösteren ekran görüntüsü":::

2. Tüm ayrıntılar doldurulduktan ve doğrulama başarılı olduktan sonra, anlık görüntüyü doğrulamak ve oluşturmak için **gözden geçir + oluştur** ' u seçin. Anlık görüntü başarıyla tamamlandığında, dağıtımın tamamlandığını bildiren bir ileti görürsünüz.

## <a name="in-place-migration"></a>Yerinde geçiş

Eski bir Ubuntu sürümünü geçiriyorsanız yerinde geçiş yapmak isteyebilirsiniz. Bu geçiş, yeni bir sanal makine oluşturmaz ve yan yana geçişe göre daha az adım içerir.  Daha fazla denetim veya farklı bir dağıtıma geçiş yaptığınız için (örneğin, CentOS) yan yana geçiş yapmak istiyorsanız, yan [yana geçiş](#side-by-side-migration) bölümüne atlayın. 

1. Azure portal, DSVM 'nizi başlatın ve SSH kullanarak oturum açın. Bunu yapmak için **Bağlan** ve **SSH** ' ı seçin ve bağlantı yönergelerini izleyin. 

1. DSVM 'nizin bir terminal oturumuna bağlandıktan sonra aşağıdaki yükseltme komutunu çalıştırın:

    ```bash
    sudo do-release-upgrade
    ```

Yükseltme işleminin tamamlanması biraz zaman alır. Bu, üzerine geldiğinde, program sanal makineyi yeniden başlatmak için izin ister. **Evet** yanıtını verin. Sistem yeniden başlatıldıktan sonra SSH oturumuyla bağlantınız kesilecek.

### <a name="if-necessary-regenerate-ssh-keys"></a>Gerekirse, SSH anahtarlarını yeniden oluşturun

> [!IMPORTANT] 
> Yükseltme ve yeniden başlatma işleminden sonra SSH anahtarlarınızı yeniden oluşturmanız gerekebilir.

VM 'niz yükseltildikten ve yeniden başlatıldıktan sonra, SSH aracılığıyla yeniden erişmeyi deneyin. Yeniden başlatma sırasında IP adresi değişmiş olabilir, bu nedenle bağlanmayı denemeden önce onaylayın.

**Uzak ana BILGISAYAR KIMLIĞININ değiştiği** bir hata alırsanız, SSH kimlik bilgilerinizi yeniden oluşturmanız gerekir.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Uzak ana bilgisayar kimliği değişti uyarısını gösteren PowerShell ekran görüntüsü":::

Bunu yapmak için, yerel makinenizde komutunu çalıştırın:

```bash
ssh-keygen -R "your server hostname or ip"
```

Artık SSH ile bağlantı kurabiliyor olmalısınız. Hala sorun yaşıyorsanız, **Bağlan** SAYFASıNDA, **SSH bağlantı sorunlarını gidermek** için bağlantıyı izleyin.

## <a name="side-by-side-migration"></a>Yan yana geçiş

CentOS 'dan geçiş yapıyorsanız veya temiz bir işletim sistemi yüklemesi isterseniz, yan yana geçiş yapabilirsiniz. Bu geçiş türü daha fazla adıma sahiptir, ancak tam olarak hangi dosyaların devralınacağı üzerinde denetim elde etmenizi sağlar.

Aynı yukarı akış kaynak paketleri kümesine bağlı olarak diğer sistemlerden geçişler görece basittir, örneğin, [SSS/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

FileSystem 'ın işletim sistemi parçalarını yükseltmeyi ve yerinde olduğu gibi Kullanıcı dizinlerini bırakmayı tercih edebilirsiniz `/home` . Eski Kullanıcı giriş dizinlerini yerinde bırakırsanız, GNOME/KDE menüleri ve diğer masaüstü öğeleriyle ilgili bazı sorunlar olmasını bekler. Yeni Kullanıcı hesapları oluşturmak ve geçişten sonra kullanıcıların malzemesini başvuru, kopyalama veya bağlama için dosya sisteminde başka bir yere bağlamak en kolay olabilir.

### <a name="migration-at-a-glance"></a>Bir bakışta geçiş

1.  Daha önce açıklandığı gibi var olan sanal makinenizin anlık görüntüsünü oluşturun

1.  Bu anlık görüntüden bir disk oluştur

1.  Yeni bir Ubuntu Veri Bilimi Sanal Makinesi oluşturma

1.  Yeni sanal makinede Kullanıcı hesaplarını yeniden oluştur

1.  Anlık görüntüyle ilgili VM 'nin diskini yeni Veri Bilimi Sanal Makinesi bir veri diski olarak bağlayın

1.  İstenen verileri el ile kopyalayın

### <a name="create-a-disk-from-your-vm-snapshot"></a>VM anlık görüntüsünden bir disk oluşturma

Daha önce açıklandığı gibi zaten bir VM anlık görüntüsü oluşturmadıysanız, bunu yapın. 

1. Azure portal **disk** sayfasını açmak için **diskler** ' i arayın ve **Ekle**' yi seçin.

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Diskleri ara sayfasını ve Ekle düğmesini gösteren Azure portal ekran görüntüsü":::

2. **Abonelik**, **kaynak grubu** ve **bölgeyi** VM anlık görüntüsünün değerlerine ayarlayın. Oluşturulacak disk için bir **ad** seçin.

3. **Kaynak türünü** **anlık görüntü** olarak seçin ve **kaynak anlık** görüntüsü olarak VM anlık görüntüsünü seçin. Diski gözden geçirin ve oluşturun. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Seçenekleri gösteren disk oluşturma iletişim kutusunun ekran görüntüsü":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Yeni bir Ubuntu Veri Bilimi Sanal Makinesi oluşturma

[Azure Portal](https://portal.azure.com) veya [ARM şablonunu](./dsvm-tutorial-resource-manager.md)kullanarak yeni bir Ubuntu veri bilimi sanal makinesi oluşturun. 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Yeni Veri Bilimi Sanal Makinesi Kullanıcı hesaplarını yeniden oluşturun

Yalnızca eski bilgisayarınızdan verileri kopyalayacaksınız, yeni makinede kullanmak istediğiniz kullanıcı hesaplarını ve yazılım ortamlarını yeniden oluşturmanız gerekir.

Linux, eski makinenizi izlemek üzere yeni yüklemenizde dizin ve yolları özelleştirmenize olanak tanımak için yeterince esnektir. Genel olarak, modern Ubuntu 'nun tercih edilen düzeninin kullanılması ve Kullanıcı ortamınızı ve betiklerinizi uyarlamak için değiştirmek daha kolaydır.

Daha fazla bilgi için bkz. [hızlı başlangıç: Linux için veri bilimi sanal makinesi ayarlama (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Anlık görüntüyle ilgili VM 'nin diskini yeni Veri Bilimi Sanal Makinesi bir veri diski olarak bağlayın

1. Azure portal, Veri Bilimi Sanal Makinesi çalıştığından emin olun.

2. Azure portal, Veri Bilimi Sanal Makinesi sayfasına gidin. Sol Köl üzerindeki **diskler** dikey penceresini seçin. **Mevcut diskleri Ekle** öğesini seçin.

3. **Disk adı** açılır listesinde, eski sanal makinenizin anlık görüntüsünden oluşturduğunuz diski seçin.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Disk ek seçeneklerini gösteren DSVM seçenekleri sayfasının ekran görüntüsü":::

4. Sanal makinenizi güncelleştirmek için **Kaydet** ' i seçin.

> [!Important]
> Veri diskini iliştirmeye çalışırken sanal makinenizin çalışıyor olması gerekir. VM çalışmıyorsa, diskler yanlış bir sıraya eklenebilir, önde gelen ve önyükleme yapılamayan bir sisteme gelebilir. Veri diskini VM ile birlikte eklerseniz, veri diskinin yanındaki **X** ' i SEÇIN, VM 'yi başlatın ve yeniden ekleyin.

### <a name="manually-copy-the-wanted-data"></a>İstenen verileri el ile kopyalayın 

1. SSH kullanarak çalışan sanal makinenizde oturum açın.

2. Aşağıdaki komutu çalıştırarak eski sanal makinenizin anlık görüntüsünden oluşturulan diski iliştirildiğini doğrulayın:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Sonuçlar aşağıdaki görüntüye benzer bir şekilde görünmelidir. Görüntüde disk, `sda1` köke bağlanır ve `sdb2` `/mnt` karalama disktir. Eski sanal makinenizin anlık görüntüsünden oluşturulan veri diski `sdc1` , bir bağlama konumunun olmaması halinde belirlenir, ancak henüz kullanılabilir değildir. Sonuçlarınız farklı tanımlayıcılara sahip olabilir, ancak benzer bir model görmeniz gerekir.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Çıkarılmış veri sürücüsünü gösteren lsblk çıktısının ekran görüntüsü":::
    
3. Veri sürücüsüne erişmek için bir konum oluşturun ve bağlayın. `/dev/sdc1`Tarafından döndürülen uygun değerle değiştirin `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Şimdi, `/datadrive` eski veri bilimi sanal makinesi dizinlerini ve dosyalarını içerir. Veri sürücüsünden istediğiniz dizinleri veya dosyaları istediğiniz şekilde yeni VM 'ye taşıyın veya kopyalayın.

Daha fazla bilgi için bkz. [bir LINUX sanal makinesine veri diski eklemek için portalını kullanma](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Sürüm yükseltmesini bağlama ve onaylama

Yerinde veya yan yana geçiş gerçekleştirmişseniz, başarıyla yükseltmeyeceğinizi onaylayın. Terminal oturumundan şunu çalıştırın: 

```bash
cat /etc/os-release
```

Ubuntu 18,04 kullandığınızı görmeniz gerekir.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="İşletim sistemi sürüm verilerini gösteren Ubuntu terminalinin ekran görüntüsü":::

Sürüm değişikliği de Azure portal gösterilmiştir.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="İşletim sistemi sürümü dahil DSVM özelliklerini gösteren portalın ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da Ubuntu veri bilimi makinesiyle veri bilimi](./linux-dsvm-walkthrough.md)
- [Azure Veri Bilimi Sanal Makinesi'ne dahil olan araçlar hangileridir?](./tools-included.md)