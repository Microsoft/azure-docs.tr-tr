---
title: StorSimple 8000 serisi destek paketi oluşturma
description: StorSimple 8000 serisi cihazınız için bir destek paketi oluşturmayı, şifrelerini çözmeyi ve düzenlemenizi öğrenin.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017177"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>StorSimple 8000 serisi için destek paketi oluşturma ve yönetme

## <a name="overview"></a>Genel Bakış

StorSimple destek paketi, tüm StorSimple cihaz sorunlarını gidermeye yönelik Microsoft Desteği yardımcı olacak tüm ilgili günlükleri toplayan kullanımı kolay bir mekanizmadır. Toplanan Günlükler şifrelenir ve sıkıştırılır.

Bu öğretici, StorSimple 8000 serisi cihazınız için destek paketini oluşturmak ve yönetmek için adım adım yönergeleri içerir. Bir StorSimple Sanal dizisi ile çalışıyorsanız, [günlük paketi oluştur ' a](storsimple-ova-web-ui-admin.md#generate-a-log-package)gidin.

## <a name="create-a-support-package"></a>Destek paketi oluşturma

Bazı durumlarda, StorSimple için Windows PowerShell aracılığıyla destek paketini el ile oluşturmanız gerekir. Örnek:

* Microsoft Desteği ile paylaşmadan önce, günlük dosyalarınızda hassas bilgileri kaldırmanız gerekirse.
* Bağlantı sorunları nedeniyle paketi karşıya yüklerken güçlük çekiyorsanız.

El ile oluşturulan destek paketinizi, e-posta üzerinden Microsoft Desteği paylaşabilirsiniz. StorSimple için Windows PowerShell ' de bir destek paketi oluşturmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell ' de bir destek paketi oluşturmak için

1. Bir Windows PowerShell oturumunu StorSimple cihazınıza bağlanmak için kullanılan uzak bilgisayarda yönetici olarak başlatmak için aşağıdaki komutu girin:
   
    `Start PowerShell`
2. Windows PowerShell oturumunda cihazınızın SSAdmin konsoluna bağlanın:
   
   1. Komut isteminde şunu girin:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Açılan iletişim kutusunda, cihaz yönetici parolanızı girin. Varsayılan parola _Parola1_' dir.
     
      ![PowerShell kimlik bilgileri iletişim kutusu](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. **Tamam**’ı seçin.
   4. Komut isteminde şunu girin:
     
      `Enter-PSSession $MS`
3. Açılan oturumda, uygun komutu girin.
   
   * Parola korumalı ağ paylaşımları için şunu girin:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Parola ve şifreleme parolası girmeniz istenir (destek paketi şifrelenir). Daha sonra varsayılan klasörde (Cihaz adı geçerli tarih ve saate eklenerek) bir destek paketi oluşturulur.
   * Parola korumalı olmayan paylaşımlar için parametreye ihtiyacınız yoktur `-Credential` . Aşağıdakileri girin:
     
       `Export-HcsSupportPackage`
     
       Destek paketi, her iki denetleyici için de varsayılan klasörde oluşturulur. Paket, sorun giderme için Microsoft Desteği gönderilebilecek şifreli, sıkıştırılmış bir dosyadır. Daha fazla bilgi için bkz. [iletişim Microsoft desteği](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage cmdlet parametreleri

Export-HcsSupportPackage cmdlet 'i ile aşağıdaki parametreleri kullanabilirsiniz.

| Parametre | Gerekli/İsteğe Bağlı | Açıklama |
| --- | --- | --- |
| `-Path` |Gerekli |Destek paketinin yerleştirildiği ağ paylaşılan klasörünün konumunu sağlamak için kullanın. |
| `-EncryptionPassphrase` |Gerekli |Destek paketini şifrelemeye yardımcı olması için bir parola sağlamak üzere kullanın. |
| `-Credential` |İsteğe Bağlı |Ağ paylaşılan klasörü için erişim kimlik bilgilerini sağlamak üzere kullanın. |
| `-Force` |İsteğe Bağlı |Şifreleme parolası onay adımını atlamak için kullanın. |
| `-PackageTag` |İsteğe Bağlı |Destek paketinin yerleştirildiği *yol* altında bir dizin belirtmek için kullanın. Varsayılan değer [Cihaz adı]-[geçerli tarih ve Saat: yyyy-aa-gg-HH-mm-ss] şeklindedir. |
| `-Scope` |İsteğe Bağlı |Her iki denetleyici için bir destek paketi oluşturmak üzere **küme** olarak belirtin (varsayılan). Yalnızca geçerli denetleyici için bir paket oluşturmak istiyorsanız, **denetleyiciyi** belirtin. |

## <a name="edit-a-support-package"></a>Destek paketini düzenleme

Bir destek paketi oluşturduktan sonra, hassas bilgileri kaldırmak için paketi düzenlemeniz gerekebilir. Bu, günlük dosyalarından birim adlarını, cihaz IP adreslerini ve yedek adlarını içerebilir.

> [!IMPORTANT]
> Yalnızca StorSimple için Windows PowerShell aracılığıyla oluşturulmuş bir destek paketini düzenleyebilirsiniz. StorSimple Device Manager hizmetiyle Azure portal oluşturulan bir paketi düzenleyemezsiniz.

Bir destek paketini Microsoft Desteği siteye yüklemeden önce düzenlemek için önce destek paketinin şifresini çözün, dosyaları düzenleyin ve ardından yeniden şifreleyin. Aşağıdaki adımları uygulayın.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell bir destek paketini düzenlemek için

1. [StorSimple için Windows PowerShell ' de bir destek paketi oluşturmak için](#to-create-a-support-package-in-windows-powershell-for-storsimple), ' da daha önce açıklandığı gibi bir destek paketi oluşturun.
2. [Betiği,](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) istemcinizdeki yerel olarak indirin.
3. Windows PowerShell modülünü içeri aktarın. Betiği indirdiğiniz yerel klasörün yolunu belirtin. Modülünü içeri aktarmak için şunu girin:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Tüm dosyalar sıkıştırılmış ve şifrelenmiş *. AES* dosyalarıdır. Dosyaların sıkıştırmasını açıp şifrelerini çözmek için şunu girin:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Gerçek dosya uzantılarının artık tüm dosyalar için görüntülendiğini unutmayın.
   
    ![Destek paketini Düzenle](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Şifreleme parolası sorulduğunda, destek paketi oluşturulurken kullandığınız parolayı girin.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Günlük dosyalarını içeren klasöre gidin. Günlük dosyaları artık açıldı ve şifresi çözülemediğinden, bunlar özgün dosya uzantılarına sahip olur. Birim adları ve cihaz IP adresleri gibi müşteriye özgü tüm bilgileri kaldırmak için bu dosyaları değiştirin ve dosyaları kaydedin.
7. Dosyaları, gzip ile sıkıştırmak ve AES-256 ile şifrelemek için kapatın. Bu, destek paketini bir ağ üzerinden aktarmaya yönelik hız ve güvenlik içindir. Dosyaları sıkıştırmak ve şifrelemek için aşağıdakileri girin:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Destek paketini Düzenle 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. İstendiğinde, değiştirilen destek paketi için bir şifreleme parolası girin.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. İstendiğinde Microsoft Desteği paylaşmak için yeni parolayı yazın.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Örnek: parola korumalı bir paylaşımdaki destek paketindeki dosyaları düzenlemeyle

Aşağıdaki örnekte bir destek paketinin şifresini çözme, düzenleme ve yeniden şifreleme işlemlerinin nasıl yapılacağı gösterilmektedir.

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Sonraki adımlar

* [Destek paketinde toplanan bilgiler](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs) hakkında bilgi edinin
* [Cihaz dağıtımınızın sorunlarını gidermek için destek paketlerini ve cihaz günlüklerini nasıl kullanacağınızı](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)öğrenin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini nasıl kullanacağınızı](storsimple-8000-manager-service-administration.md)öğrenin.

