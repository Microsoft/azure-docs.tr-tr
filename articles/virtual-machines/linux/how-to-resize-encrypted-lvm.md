---
title: Azure disk şifrelemesi kullanılarak şifrelenmiş diskleri yeniden boyutlandırma
description: Bu makalede, mantıksal birim yönetimini kullanarak ADE şifrelenmiş disklerin yeniden boyutlandırılmasına yönelik yönergeler sağlanmaktadır.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 92e3be9111abe5712549ba85ef4550a9ac1473f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596816"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Azure disk şifrelemesi kullanan mantıksal birim yönetimi cihazlarını yeniden boyutlandırma

Bu makalede, Azure disk şifrelemesi kullanan veri disklerini yeniden boyutlandırmayı öğreneceksiniz. Diskleri yeniden boyutlandırmak için Linux üzerinde mantıksal birim yönetimi (LVM) kullanacaksınız. Adımlar birden çok senaryo için geçerlidir.

Bu yeniden boyutlandırma işlemini aşağıdaki ortamlarda kullanabilirsiniz:

- Linux dağıtımları:
    - Red Hat Enterprise Linux (RHEL) 7 veya üzeri
    - Ubuntu 16 veya üzeri
    - SUSE 12 veya üzeri
- Azure disk şifrelemesi sürümleri: 
    - Tek pass uzantısı
    - Çift Pass uzantısı

## <a name="prerequisites"></a>Önkoşullar

Bu makalede sahip olduğunuz varsayılır:

- Mevcut bir LVM yapılandırması. Daha fazla bilgi için bkz. [LINUX VM 'de LVM yapılandırma](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- Azure Disk Şifrelemesi tarafından zaten şifrelenmiş diskler. Daha fazla bilgi için bkz. [şifrelenmiş cihazlarda LVM ve RAID yapılandırma](how-to-configure-lvm-raid-on-crypt.md).

- Linux ve LVM kullanma deneyimi.

- Azure 'da veri diskleri için */dev/disk/scsi1/* yollarını kullanma deneyimi. Daha fazla bilgi için bkz. [LINUX VM cihaz adı sorunlarını giderme](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Senaryolar

Bu makaledeki yordamlar aşağıdaki senaryolar için geçerlidir:

- Geleneksel LVM ve LVM-şifreli yapılandırma
- Geleneksel LVM şifrelemesi 
- LVM-şifreli 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Geleneksel LVM ve LVM-şifreli yapılandırma

Geleneksel LVM ve LVM-Crypt yapılandırması, birim grubunda (VG) kullanılabilir alan olduğunda bir mantıksal birimi (LV) genişletir.

### <a name="traditional-lvm-encryption"></a>Geleneksel LVM şifrelemesi 

Geleneksel LVM şifrelemesi içinde, LVs şifrelenir. Tüm disk şifrelenmedi.

Geleneksel LVM şifrelemesini kullanarak şunları yapabilirsiniz:

- Yeni bir fiziksel birim (BD) eklediğinizde LV 'yi genişletin.
- Var olan bir BD 'i yeniden boyutlandırdığınızda LV 'yi genişletin.

### <a name="lvm-on-crypt"></a>LVM-şifreli 

Disk şifrelemesi için önerilen yöntem, şifreleme-sanal ' dir. Bu yöntem yalnızca LV değil, tüm diski şifreler.

LVM-on-Crypt kullanarak şunları yapabilirsiniz: 

- Yeni bir BD eklediğinizde LV 'yi genişletin.
- Var olan bir BD 'i yeniden boyutlandırdığınızda LV 'yi genişletin.

> [!NOTE]
> Aynı VM 'de geleneksel LVM şifrelemesini ve LVM 'yi şifreli olarak karıştırmaya öneremiyoruz.

Aşağıdaki bölümlerde, LVM ve LVM-on-Crypt ' i nasıl kullanacağınızı gösteren örnekler sağlanmaktadır. Örnekler, diskler, PVs, VGs, LVs, dosya sistemleri, evrensel benzersiz tanımlayıcılar (UUID 'ler) ve bağlama noktaları için önceden varolan değerleri kullanır. Ortamınıza uyması için bu değerleri kendi değerlerinizle değiştirin.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>VG 'ın kullanılabilir alanı olduğunda bir LV uzat

LVs 'yi yeniden boyutlandırmanın geleneksel yolu, VG 'in kullanılabilir alanı olduğunda bir LV genişlemelidir. Şifrelenmemiş diskler için bu yöntemi, geleneksel LVM-şifrelenmiş birimler ve LVM-Crypt yapılandırması için kullanabilirsiniz.

1. Artırmak istediğiniz dosya sisteminin geçerli boyutunu doğrulayın:

    ``` bash
    df -h /mountpoint
    ```

    ![Dosya sisteminin boyutunu denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. VG 'in, LV 'yi artırmak için yeterli alana sahip olduğunu doğrulayın:

    ``` bash
    vgs
    ```

    ![VG üzerindeki alanı denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Ayrıca şunları da kullanabilirsiniz `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![VG üzerinde boşluk denetleyen V G görüntü kodunu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Hangi LV yeniden boyutlandırılacağını belirler:

    ``` bash
    lsblk
    ```

    ![L s b l k komutunun sonucunu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    LVM-on-Crypt için fark, bu çıkışın şifreli katmanın disk düzeyinde olduğunu gösterir.

    ![L s b l k komutunun sonucunu gösteren ekran görüntüsü. Çıktı vurgulanır. Şifrelenmiş katmanı gösterir.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. LV boyutunu kontrol edin:

    ``` bash
    lvdisplay lvname
    ```

    ![Mantıksal birim boyutunu denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. `-r`Dosya sistemini çevrimiçi olarak yeniden boyutlandırmak için kullanarak LV boyutunu artırın:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Mantıksal birimin boyutunu artıran kodu gösteren ekran görüntüsü. Komutu ve sonuçları vurgulanır.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. LV ve dosya sistemi için yeni boyutları doğrulayın:

    ``` bash
    df -h /mountpoint
    ```

    ![LV ve dosya sisteminin boyutunu doğrulayan kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Boyut çıktısı, LV ve dosya sisteminin başarıyla yeniden boyutlandırıldığını gösterir.

LV 'nin düzeyindeki değişiklikleri onaylamak için, LV bilgilerini tekrar kontrol edebilirsiniz:

``` bash
lvdisplay lvname
```

![Yeni boyutları doğrulayan kodu gösteren ekran görüntüsü. Boyutlar vurgulanır.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Yeni bir BD ekleyerek geleneksel bir LVM birimini genişletme

VG boyutunu artırmak için yeni bir disk eklemeniz gerekiyorsa, yeni bir BD ekleyerek geleneksel LVM haciminizi genişletin.

1. Artırmak istediğiniz dosya sisteminin geçerli boyutunu doğrulayın:

    ``` bash
    df -h /mountpoint
    ```

    ![Bir dosya sisteminin geçerli boyutunu denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Geçerli BD yapılandırmasını doğrulayın:

    ``` bash
    pvs
    ```

    ![Geçerli BD yapılandırmasını denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Geçerli VG bilgilerini denetleyin:

    ``` bash
    vgs
    ```

    ![Geçerli birim grubu bilgilerini denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Geçerli disk listesini denetleyin. */Dev/disk/Azure/scsi1/*' deki cihazları denetleyerek veri disklerini belirler.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Geçerli disk listesini denetleyen kodu gösteren ekran görüntüsü. Komutu ve sonuçları vurgulanır.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Çıktıyı denetleyin `lsblk` : 

    ``` bash
    lsbk
    ```

    ![L s b l k çıkışını denetleyen kodu gösteren ekran görüntüsü. Komutu ve sonuçları vurgulanır.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Bir [LINUX sanal makinesine veri diski iliştirme](attach-disk-portal.md)' deki yönergeleri izleyerek yenı diski sanal makineye ekleyin.

7. Disk listesini denetleyin ve yeni diske dikkat edin.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Disk listesini denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![L s b l k kullanarak disk listesini denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Yeni veri diskinin üstünde yeni bir BD oluşturun:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Yeni bir BD oluşturan kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Bu yöntem, bölümü olmayan bir BD olarak tüm diski kullanır. Alternatif olarak, `fdisk` bir bölüm oluşturmak için kullanabilirsiniz ve sonra bu bölümü için kullanabilirsiniz `pvcreate` .

9. BD 'in BD listesine eklendiğini doğrulayın:

    ``` bash
    pvs
    ```

    ![Fiziksel birim listesini gösteren kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Yeni BD 'e ekleyerek VG ' i genişletin:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Birim grubunu genişleten kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Yeni VG boyutunu kontrol edin:

    ``` bash
    vgs
    ```

    ![Birim grubu boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Yeniden `lsblk` boyutlandırılması gereken LV 'yi belirlemek için kullanın:

    ``` bash
    lsblk
    ```

    ![Yeniden boyutlandırılması gereken yerel birimi tanımlayan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. `-r`Dosya sistemini çevrimiçi artırmak için kullanarak LV boyutunu genişletin:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Dosya sisteminin çevrimiçi boyutunu artıran kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. LV ve dosya sisteminin yeni boyutlarını doğrulayın:

    ``` bash
    df -h /mountpoint
    ```

    ![Yerel birimin ve dosya sisteminin boyutlarını denetleyen kodu gösteren ekran görüntüsü. Komut ve sonuç vurgulanır.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Azure veri şifrelemesi geleneksel LVM yapılandırmalarında kullanıldığında, şifrelenen katman, disk düzeyinde değil, LV düzeyinde oluşturulur.
    >
    >Bu noktada, şifreli katman yeni diske genişletilir. Gerçek veri diskinin platform düzeyinde şifreleme ayarları yoktur, bu nedenle şifreleme durumu güncellenmez.
    >
    >Bunlar, LVM-her ncrypt 'in önerilen yaklaşım olmasının nedenleridir. 

15. Portaldan şifreleme bilgilerini kontrol edin:

    ![Portalda şifreleme bilgilerini gösteren ekran görüntüsü. Disk adı ve şifreleme vurgulanır.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Diskteki şifreleme ayarlarını güncelleştirmek için yeni bir LV ekleyin ve sanal makinede uzantıyı etkinleştirin.
    
16. Yeni bir LV ekleyin, üzerine bir dosya sistemi oluşturun ve ' ye ekleyin `/etc/fstab` .

17. Şifreleme uzantısını yeniden ayarlayın. Bu kez, yeni veri diskinde platform düzeyinde şifreleme ayarlarını damgayacaksınız. CLı örneği aşağıda verilmiştir:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Portaldan şifreleme bilgilerini kontrol edin:

    ![Portalda şifreleme bilgilerini gösteren ekran görüntüsü. Disk adı ve şifreleme bilgileri vurgulanır.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Şifreleme ayarları güncelleştirildikten sonra, yeni LV 'yi silebilirsiniz. Ayrıca, `/etc/fstab` oluşturduğunuz ve ' den girişi silin `/etc/crypttab` .

![Yeni mantıksal birimi silen kodu gösteren ekran görüntüsü. Silinen F S sekmesi ve Crypt sekmesi vurgulanır.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Temizlemeyi tamamlaması için şu adımları izleyin:

1. LV 'yi çıkarın:

    ``` bash
    umount /mountpoint
    ```

1. Birimin şifrelenmiş katmanını kapatın:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. LV 'yi silin:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Var olan bir BD 'i yeniden boyutlandırarak geleneksel bir LVM birimini genişletme

E-posta bazı senaryolarda, sınırlandırmalar var olan bir diski yeniden boyutlandırmanızı gerektirebilir. Aşağıdaki adımları uygulayın:

1. Şifrelenmiş disklerinizi belirler:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Şifrelenmiş diskleri tanımlayan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Şifrelenmiş diskleri tanımlayan alternatif kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. BD bilgilerini denetleyin:

    ``` bash
    pvs
    ```

    ![Fiziksel birimle ilgili bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Resimdeki sonuçlar, tüm PVs 'lerde Şu anda kullanılmakta olan tüm alanı gösterir.

3. VG bilgilerini denetleyin:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Birim grubuyla ilgili bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Disk boyutlarını denetleyin. `fdisk` `lsblk` Sürücü boyutlarını listelemek için veya kullanabilirsiniz.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Disk boyutlarını denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Burada hangi PVs 'in kullanılarak hangi LVs ile ilişkili olduğunu belirledik `lsblk -fs` . ' İ çalıştırarak ilişkilendirmeleri tanımlayabilirsiniz `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Yerel birimlerle fiziksel birim ilişkilendirmelerini belirlemenin alternatif bir yolunu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Bu durumda, tüm dört veri sürücüleri aynı VG ve tek bir LV 'nin bir parçasıdır. Yapılandırmanız farklılık gösterebilir.

5. Geçerli dosya sistemi kullanımını denetleyin:

    ``` bash
    df -h /datalvm*
    ```

    ![Dosya sistemi kullanımını denetleyen kodu gösteren ekran görüntüsü. Komutu ve sonuçları vurgulanır.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. [Azure yönetilen disk genişletme](expand-disks.md#expand-an-azure-managed-disk)bölümündeki yönergeleri izleyerek veri disklerini yeniden boyutlandırın. Portalı, CLı veya PowerShell 'i kullanabilirsiniz.

    >[!IMPORTANT]
    >VM çalışırken sanal diskleri yeniden boyutlandıramazsınız. Bu adım için sanal makineyi serbest bırakın.

7. Sanal makineyi başlatın ve kullanarak yeni boyutları denetleyin `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Disk boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Bu durumda, `/dev/sdd` 5 g 'den 20 g 'ye yeniden boyutlandırıldı.

8. Geçerli BD boyutunu denetle:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![P V boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Disk yeniden boyutlandırıldığında, BD hala önceki boyuta sahiptir.

9. BD boyutunu yeniden boyutlandır:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Fiziksel birimi yeniden boyutlandıran kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. BD boyutunu denetle:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Fiziksel birimin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Yeniden boyutlandırmak istediğiniz tüm diskler için aynı yordamı uygulayın.

11. VG bilgilerini denetleyin.

    ``` bash
    vgdisplay vgname
    ```

    ![Birim grubu için bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    Şimdi VG, LVs 'ye ayrılacak yeterli alana sahiptir.

12. LV yeniden boyutlandır:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![L V ' y i yeniden boyutlandıran kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Dosya sisteminin boyutunu kontrol edin:

    ``` bash
    df -h /datalvm2
    ```

    ![Dosya sisteminin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Yeni bir BD ekleyerek bir LVM-şifreli birimi genişletme

Ayrıca, yeni bir BD ekleyerek bir LVM-şifreli birimi genişletebilirsiniz. Bu yöntem, [şifrelenmiş cihazlarda LVM ve RAID yapılandırma](how-to-configure-lvm-raid-on-crypt.md#general-steps)içindeki adımları yakından izler. Yeni bir disk ekleme ve bir LVM-şifreli yapılandırmasında ayarlama hakkında bilgi sağlayan bölümlere bakın.

Bu yöntemi, var olan bir LV 'ye alan eklemek için kullanabilirsiniz. Ya da yeni VGs veya LVs oluşturabilirsiniz.

1. VG ' un geçerli boyutunu doğrulayın:

    ``` bash
    vgdisplay vgname
    ```

    ![Birim grubu boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Genişletmek istediğiniz dosya sisteminin ve LV 'nin boyutunu doğrulayın:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Yerel birimin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Dosya sisteminin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. VM 'ye yeni bir veri diski ekleyin ve bunu tanımlayarak yapın.

    Yeni diski eklemeden önce, diskleri kontrol edin:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Disklerin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Yeni diski eklemeden önce diskleri denetlemeye yönelik başka bir yol aşağıda verilmiştir:

    ``` bash
    lsblk
    ```

    ![Disklerin boyutunu denetleyen alternatif bir kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Yeni diski eklemek için PowerShell, Azure CLı veya Azure portal kullanabilirsiniz. Daha fazla bilgi için bkz. bir [LINUX sanal makinesine veri diski iliştirme](attach-disk-portal.md).

    Çekirdek adı şeması, yeni eklenen cihaz için geçerlidir. Yeni bir sürücü normalde kullanılabilir bir sonraki harfe atanır. Bu durumda, eklenen disk `sdd` .

4. Yeni diskin eklendiğinden emin olmak için diskleri kontrol edin:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Diskleri listeleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Çıktıda yeni eklenen diski gösteren ekran görüntüsü.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Son eklenen diskin üstünde bir dosya sistemi oluşturun. Diski bağlantılı cihazlarla eşleştirin `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Dosya sistemi oluşturan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Dosya sistemi oluşturan ve diskle bağlantılı cihazlara eşleşen ek kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Eklenen yeni disk için geçici bir bağlama noktası oluşturun:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Son oluşturulan dosya sistemini öğesine ekleyin `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Yeni oluşturulan dosya sistemini bağla:

    ``` bash
    mount -a
    ```

9. Yeni dosya sisteminin takılı olduğundan emin olun:

    ``` bash
    df -h
    ```

    ![Dosya sisteminin bağlı olduğunu doğrulayan kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Dosya sisteminin bağlı olduğunu doğrulayan ek kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Daha önce veri sürücüleri için başlattığınız şifrelemeyi yeniden başlatın.

    >[!TIP]
    >LVM-on-Crypt için kullanmanızı öneririz `EncryptFormatAll` . Aksi takdirde, ek diskler ayarladığınızda bir çift şifreleme görebilirsiniz.
    >
    >Daha fazla bilgi için bkz. [şifrelenmiş cihazlarda LVM ve RAID yapılandırma](how-to-configure-lvm-raid-on-crypt.md).

    Aşağıda bir örnek verilmiştir:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Şifreleme tamamlandığında, yeni eklenen diskte bir crypt katmanı görürsünüz:

    ``` bash
    lsblk
    ```

    ![Crypt katmanını denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Yeni diskin şifreli katmanını çıkarın:

    ``` bash
    umount ${newmount}
    ```

12. Geçerli BD bilgilerini denetleyin:

    ``` bash
    pvs
    ```

    ![Fiziksel birimle ilgili bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Diskin şifreli katmanının üstünde bir BD oluşturun. Önceki komuttan cihaz adını alın `lsblk` . `/dev/`BD oluşturmak için cihaz adının önüne bir Eşleyici ekleyin:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Şifrelenmiş katmanda fiziksel birim oluşturan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Geçerli imzayı silme hakkında bir uyarı görürsünüz `ext4 fs` . Bu uyarı beklenmektedir. Bu soruyu ile yanıtlayın `y` .

14. Yeni BD 'in LVM yapılandırmasına eklendiğini doğrulayın:

    ``` bash
    pvs
    ```

    ![Fiziksel birimin LVM yapılandırmasına eklendiğini doğrulayan kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Artırmanız gereken VG öğesine yeni BD ekleyin.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Bir birim grubuna fiziksel birim ekleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. VG 'in yeni boyutunu ve boş alanını doğrulayın:

    ``` bash
    vgdisplay vgname
    ```

    ![Birim grubunun boyutunu ve boş alanını doğrulayan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Count ve değerinin artışını dikkate alın `Total PE` `Free PE / Size` .

17. LV ve dosya sisteminin boyutunu artırın. Seçeneğini kullanın `-r` `lvextend` . Bu örnekte, VG içindeki toplam kullanılabilir alanı, verilen LV 'ye ekliyoruz.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Yerel birimin ve dosya sisteminin boyutunu artıran kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Değişikliklerinizi doğrulamak için sonraki adımları izleyin.

1. LV boyutunu doğrulayın:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Yerel birimin yeni boyutunu doğrulayan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Dosya sisteminin yeni boyutunu doğrulayın:

    ``` bash
    df -h mountpoint
    ```

    ![Dosya sisteminin yeni boyutunu doğrulayan kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. LVM katmanının şifreli katmanın üstünde olduğunu doğrulayın:

    ``` bash
    lsblk
    ```

    ![LVM katmanının şifreli katmanın üstünde olduğunu doğrulayan kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    `lsblk`Seçenek olmadan kullanırsanız, bağlama noktalarını birden çok kez görürsünüz. Komut, cihaza ve LVs 'ye göre sıralar. 

    Kullanmak isteyebilirsiniz `lsblk -fs` . Bu komutta, `-fs` bağlama noktalarının bir kez gösterilmesi için sıralama düzenini tersine çevirir. Diskler birden çok kez gösteriliyor.

    ``` bash
    lsblk -fs
    ```

    ![LVM katmanının şifreli katmanın üstünde olduğunu doğrulayan alternatif kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Var olan bir BD 'i yeniden boyutlandırarak bir crypt birimindeki LVM 'yi genişletme

1. Şifrelenmiş disklerinizi belirler:

    ``` bash
    lsblk
    ```

    ![Şifrelenmiş diskleri tanımlayan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Şifrelenmiş diskleri tanımlayan alternatif kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. BD bilgilerinizi denetleyin:

    ``` bash
    pvs
    ```

    ![Fiziksel birimler için bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. VG bilgilerinizi denetleyin:

    ``` bash
    vgs
    ```

    ![Birim grupları için bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. LV bilgilerinizi kontrol edin:

    ``` bash
    lvs
    ```

    ![Yerel birim için bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuç vurgulanır.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Dosya sistemi kullanımını denetleyin:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Dosya sisteminin ne kadarının kullanıldığını denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Disklerinizin boyutlarını denetleyin:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Disklerin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Veri diskini yeniden boyutlandırın. Portal, CLı veya PowerShell 'i kullanabilirsiniz. Daha fazla bilgi için bkz. [LINUX VM 'de sanal sabit diskler](expand-disks.md#expand-an-azure-managed-disk)bölümünde bulunan disk yeniden boyutlandırma bölümü. 

    >[!IMPORTANT]
    >VM çalışırken sanal diskleri yeniden boyutlandıramazsınız. Bu adım için sanal makineyi serbest bırakın.

8. Disk boyutlarınızı denetleyin:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Disk boyutlarını denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Bu durumda, her iki disk 2 GB ile 4 GB arasında yeniden boyutlandırıldı. Ancak dosya sisteminin, LV ve BD 'in boyutu aynı kalır.

9. Geçerli BD boyutunu denetleyin. LVM 'de, şifreli olarak, BD 'in `/dev/mapper/` cihaz değil cihaz olduğunu unutmayın `/dev/sd*` .

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Geçerli fiziksel birimin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. BD boyutunu yeniden boyutlandır:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Fiziksel birimi yeniden boyutlandıran kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Yeni BD boyutunu kontrol edin:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Fiziksel birimin boyutunu denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. BD 'de şifrelenen katmanı yeniden boyutlandırın:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Yeniden boyutlandırmak istediğiniz tüm diskler için aynı yordamı uygulayın.

13. VG bilgilerinizi denetleyin:

    ``` bash
    vgdisplay vgname
    ```

    ![Birim grubu için bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    Şimdi VG, LVs 'ye ayrılacak yeterli alana sahiptir.

14. LV bilgilerini denetleyin:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Yerel birim için bilgileri denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Dosya sistemi kullanımını denetleyin:

    ``` bash
    df -h /mountpoint
    ```

    ![Dosya sisteminin kullanımını denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. LV yeniden boyutlandır:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Yerel birimi yeniden boyutlandıran kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Burada, `-r` dosya sistemini de yeniden boyutlandırmak için seçeneğini kullanırız.

17. LV bilgilerini denetleyin:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Yerel birimle ilgili bilgileri alan kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Dosya sistemi kullanımını denetleyin:

    ``` bash
    df -h /mountpoint
    ```

    ![Dosya sistemi kullanımını denetleyen kodu gösteren ekran görüntüsü. Sonuçlar vurgulanır.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Aynı yeniden boyutlandırma yordamını, bunu gerektiren diğer bir LV uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure disk şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md)