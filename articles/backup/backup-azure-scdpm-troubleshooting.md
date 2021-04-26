---
title: System Center Data Protection Manager sorunlarını giderme
description: Bu makalede, System Center Data Protection Manager kullanırken karşılaşabileceğiniz sorunlara yönelik çözümler bulun.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: 84646f34d905b570855f655465529eb19b717cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86513821"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>System Center Data Protection Manager sorunlarını giderme

Bu makalede Data Protection Manager kullanırken karşılaşabileceğiniz sorunlara yönelik çözümler açıklanmaktadır.

System Center Data Protection Manager için en son sürüm notları için bkz. [System Center belgeleri](/system-center/dpm/dpm-release-notes). [Bu matristeki](/system-center/dpm/dpm-protection-matrix)Data Protection Manager için destek hakkında daha fazla bilgi edinebilirsiniz.

## <a name="error-replica-is-inconsistent"></a>Hata: çoğaltma tutarsız

Çoğaltma, aşağıdaki nedenlerden dolayı tutarsız olabilir:

- Çoğaltma oluşturma işi başarısız olur.
- Değişiklik günlüğünde sorunlar var.
- Birim düzeyi filtresi bit eşlem hata içeriyor.
- Kaynak makine beklenmedik şekilde kapanıyor.
- Eşitleme günlüğü taşıyor.
- Çoğaltma gerçekten tutarsız.

Bu sorunu çözmek için aşağıdaki eylemleri gerçekleştirin:

- Tutarsız durumu kaldırmak için tutarlılık denetimini el ile çalıştırın veya günlük tutarlılık denetimi zamanlayın.
- Microsoft Azure Backup Server ve Data Protection Manager 'ın en son sürümünü kullandığınızdan emin olun.
- **Otomatik tutarlılık** ayarının etkinleştirildiğinden emin olun.
- Hizmetleri komut isteminden yeniden başlatmayı deneyin. `net stop dpmra`Komutunu ve ardından öğesini kullanın `net start dpmra` .
- Ağ bağlantısı ve bant genişliği gereksinimlerini karşıladığınızı doğrulayın.
- Kaynak makinenin beklenmedik şekilde kapandığından emin olun.
- Diskin sağlıklı olduğundan ve çoğaltma için yeterli alan olduğundan emin olun.
- Aynı anda çalışan yinelenen yedekleme işlerinin bulunmadığından emin olun.

## <a name="error-online-recovery-point-creation-failed"></a>Hata: çevrimiçi kurtarma noktası oluşturma başarısız oldu

Bu sorunu çözmek için aşağıdaki eylemleri gerçekleştirin:

- Azure Backup aracısının en son sürümünü kullandığınızdan emin olun.
- Koruma görev alanında el ile bir kurtarma noktası oluşturmayı deneyin.
- Veri kaynağında tutarlılık denetimi çalıştırtığınızdan emin olun.
- Ağ bağlantısı ve bant genişliği gereksinimlerini karşıladığınızı doğrulayın.
- Çoğaltma verileri tutarsız bir durumda olduğunda, bu veri kaynağının bir disk kurtarma noktası oluşturun.
- Çoğaltmanın mevcut olduğundan ve eksik olmadığından emin olun.
- Çoğaltmanın, güncelleştirme sıra numarası (USN) günlüğü oluşturmak için yeterli alana sahip olduğundan emin olun.

## <a name="error-unable-to-configure-protection"></a>Hata: koruma yapılandırılamıyor

Data Protection Manager sunucusu korunan sunucuyla iletişim kuramadığından bu hata oluşur.

Bu sorunu çözmek için aşağıdaki eylemleri gerçekleştirin:

- Azure Backup aracısının en son sürümünü kullandığınızdan emin olun.
- Data Protection Manager sunucunuz ve korumalı sunucu arasında bağlantı (ağ/güvenlik duvarı/proxy) olduğundan emin olun.
- SQL Server 'ı koruyorsanız, **oturum açma özelliklerinin**  >  **NT AUTHORITY\SYSTEM** özelliğinin etkinleştirilmiş **sysadmin** ayarını belirttiğinden emin olun.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Hata: sunucu, kasa kimlik bilgisi dosyasında belirtildiği şekilde kaydedilmedi

Bu hata, Data Protection Manager/Azure Backup sunucu verileri için kurtarma işlemi sırasında oluşur. Kurtarma işleminde kullanılan kasa kimlik bilgileri dosyası Data Protection Manager/Azure Backup sunucusu için kurtarma hizmetleri kasasına ait değil.

Bu sorunu çözmek için şu adımları uygulayın:

1. Data Protection Manager/Azure Backup sunucusunun kaydedildiği kurtarma hizmetleri kasasından kasa kimlik bilgileri dosyasını indirin.
2. En son indirilen kasa kimlik bilgileri dosyasını kullanarak sunucuyu kasayla kaydetmeyi deneyin.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Hata: kurtarılabilir veri yok veya seçili sunucu Data Protection Manager sunucusu değil

Bu hata aşağıdaki nedenlerden dolayı oluşur:

- Kurtarma Hizmetleri kasasına kayıtlı başka Data Protection Manager/Azure Backup sunucusu yok.
- Sunucular henüz meta verileri karşıya yüklemedi.
- Seçili sunucu bir Data Protection Manager/Azure Backup sunucusu değil.

Diğer Data Protection Manager/Azure Backup sunucuları kurtarma hizmetleri kasasına kaydedildiğinde, sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. En son Azure Backup aracısının yüklü olduğundan emin olun.
2. En son aracının yüklendiğinden emin olduktan sonra, kurtarma işlemine başlamadan önce bir gün bekleyin. Gecelik yedekleme işi tüm korumalı yedeklemelerin meta verilerini buluta yükler. Yedekleme verileri daha sonra kurtarma için kullanılabilir.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Hata: belirtilen şifreleme parolası sunucu parolası ile eşleşmiyor

Bu hata, Data Protection Manager/Azure Backup sunucu verilerini kurtarırken şifreleme işlemi sırasında oluşur. Kurtarma işleminde kullanılan şifreleme parolası, sunucunun şifreleme parolası ile eşleşmiyor. Sonuç olarak, aracı verilerin şifresini çözemez ve kurtarma başarısız olur.

> [!IMPORTANT]
> Şifreleme parolasını unutur veya kaybederseniz, verileri kurtarmaya yönelik başka bir yöntem yoktur. Tek seçenek, parolayı yeniden oluşturmak içindir. Gelecekteki yedekleme verilerini şifrelemek için yeni parolayı kullanın.
>
> Verileri kurtarırken, her zaman Data Protection Manager/Azure Backup sunucusuyla ilişkili şifreleme parolasını sağlayın.
>
