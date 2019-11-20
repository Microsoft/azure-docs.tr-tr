---
title: Azure disk şifrelemesi sorun giderme kılavuzu
description: Bu makalede, Windows VM 'Leri için Microsoft Azure disk şifrelemesi için sorun giderme ipuçları sunulmaktadır.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d4e76f4d02b0287770243bfddf995a19f90d232
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749455"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure disk şifrelemesi sorun giderme kılavuzu

Bu kılavuz BT uzmanları, bilgi güvenliği analistleri ve kuruluşları tarafından Azure disk şifrelemesi kullanan bulut yöneticileri içindir. Bu makale, disk şifrelemesiyle ilgili sorunları gidermenize yardımcı olur.

Aşağıdaki adımlardan herhangi birini gerçekleştirmeden önce, şifrelemeye çalıştığınız VM 'Lerin [desteklenen VM boyutları ve işletim sistemleri](disk-encryption-overview.md#supported-vms-and-operating-systems)arasında olduğundan ve tüm önkoşulları karşıladığınızı doğrulayın:

- [Ağ gereksinimleri](disk-encryption-overview.md#networking-requirements)
- [Grup İlkesi gereksinimleri](disk-encryption-overview.md#group-policy-requirements)
- [Şifreleme anahtarı depolama gereksinimleri](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Güvenlik duvarının arkasında Azure disk şifrelemesi sorunlarını giderme

Bağlantı bir güvenlik duvarı, proxy gereksinimi veya ağ güvenlik grubu (NSG) ayarları tarafından kısıtlanmışsa, gerekli görevleri gerçekleştirme uzantısının kesintiye uğramayabilir. Bu kesinti, "sanal makine üzerinde uzantı durumu kullanılamıyor" gibi durum iletileri oluşmasına neden olabilir. Beklenen senaryolarda, şifreleme tamamlanamamaktadır. Aşağıdaki bölümlerde araştırmanız gerekebilecek bazı yaygın güvenlik duvarı sorunları bulunmaktadır.

### <a name="network-security-groups"></a>Ağ güvenlik grupları
Uygulanan tüm ağ güvenlik grubu ayarları, uç noktanın disk şifrelemesi için belgelenen ağ yapılandırması [önkoşullarını](disk-encryption-overview.md#networking-requirements) karşıladığından hala izin vermelidir.

### <a name="azure-key-vault-behind-a-firewall"></a>Bir güvenlik duvarının arkasında Azure Key Vault

Şifreleme, [Azure AD kimlik bilgileriyle](disk-encryption-windows-aad.md#)etkinleştirildiğinde, hedef VM hem Azure Active Directory uç noktalarına hem de Key Vault uç noktalarına bağlantı kurulmasına izin vermelidir. Geçerli Azure Active Directory kimlik doğrulama uç noktaları, [Office 365 URL 'leri ve IP adresi aralıkları](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) belgelerinin 56 ve 59 bölümlerinde saklanır. Key Vault yönergeler, [bir güvenlik duvarının arkasındaki Azure Key Vault erişme](../../key-vault/key-vault-access-behind-firewall.md)hakkındaki belgelerde sunulmaktadır.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
VM, yalnızca VM içinden erişilebilen, iyi bilinen yönlendirilemeyen IP adresini (`169.254.169.254`) kullanan [Azure örnek meta veri hizmeti](../windows/instance-metadata-service.md) uç noktasına erişebilmelidir.  Yerel HTTP trafiğini bu adrese dönüştüren ara sunucu (örneğin, X-Iletilmiş-for üstbilgisi ekleme) desteklenmez.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core sorunlarını giderme

Windows Server 2016 Server Core 'da BdeHdCfg bileşeni varsayılan olarak kullanılabilir değildir. Bu bileşen Azure disk şifrelemesi için gereklidir. Sistem birimini, VM 'nin yaşam süresi boyunca yalnızca bir kez yapılan işletim sistemi biriminden ayırmak için kullanılır. Bu ikili dosyalar sonraki şifreleme işlemleri sırasında gerekli değildir.

Bu sorunu geçici olarak çözmek için, aşağıdaki dört dosyayı bir Windows Server 2016 veri merkezi VM 'sinden sunucu çekirdeği üzerinde aynı konuma kopyalayın:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Aşağıdaki komutu kullanın:

   ```
   bdehdcfg.exe -target default
   ```

1. Bu komut, 550 MB 'lik bir sistem bölümü oluşturur. Sistemi yeniden başlatın.

1. Birimleri denetlemek için DiskPart 'ı kullanın ve ardından devam edin.  

Örneğin:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Şifreleme durumu sorunlarını giderme 

Portal, sanal makine içinde şifrelenmemiş olduktan sonra bile bir disk şifreli olarak görüntülenebilir.  Bu durum, daha yüksek düzeyde Azure disk şifrelemesi yönetim komutları kullanmak yerine, diskin VM içinden doğrudan şifresini kaldırmak için düşük düzey komutlar kullanıldığında meydana gelebilir.  Üst düzey komutlar yalnızca VM 'nin içinden diskin şifresini kaldıramaz, ancak VM 'nin dışında, önemli platform düzeyi şifreleme ayarlarını ve VM ile ilişkili uzantı ayarlarını da güncelleştirir.  Bunlar hizalamayla tutulmazsa, Platform şifreleme durumunu bildiremez veya VM 'yi düzgün şekilde sağlayamaz.   

Azure disk şifrelemesini PowerShell ile devre dışı bırakmak için [Disable-azvmdiskencryption](/powershell/module/az.compute/disable-azvmdiskencryption) ' ı ve ardından [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)' i kullanın. Şifreleme devre dışı olmadan önce Remove-AzVMDiskEncryptionExtension çalıştırma başarısız olur.

CLı ile Azure disk şifrelemesini devre dışı bırakmak için [az VM Encryption Disable](/cli/azure/vm/encryption)seçeneğini kullanın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure disk şifrelemesi 'nde bazı yaygın sorunlar ve bu sorunları nasıl giderebileceğiniz hakkında daha fazla bilgi edindiniz. Bu hizmet ve özellikleri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Güvenlik Merkezi 'nde disk şifrelemeyi uygulama](../../security-center/security-center-apply-disk-encryption.md)
- [Bekleyen Azure veri şifrelemesi](../../security/fundamentals/encryption-atrest.md)
