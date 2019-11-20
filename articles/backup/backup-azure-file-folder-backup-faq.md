---
title: Dosya ve klasörleri yedekleme-sık sorulan sorular
description: Azure Backup ile dosya ve klasörleri yedekleme hakkında sık sorulan sorulara yöneliktir.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b66eb7bca3c9a57f6b44697aa0340cd852fc3db4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173053"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Dosya ve klasörleri yedekleme hakkında sık sorulan sorular

Bu makalede, [Azure Backup](backup-overview.md) hizmetindeki MICROSOFT Azure kurtarma HIZMETLERI (mars) aracısıyla dosya ve klasörleri yedeklemeye yönelik yaygın soruların yanıtları bulunur.

## <a name="configure-backups"></a>Yedeklemeleri yapılandırma

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>MARS aracısının en son sürümünü nereden indirebilirim?

Windows Server makineleri, System Center DPM ve Microsoft Azure Backup sunucusu yedeklenirken kullanılan en son MARS Aracısı [indirilebilir](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Kasa kimlik bilgileri ne kadar süreyle geçerlidir?

Kasa kimlik bilgilerinin süresi 48 sonra dolar. Kimlik bilgileri dosyasının süresi dolarsa, Azure portal dosyayı yeniden indirin.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Dosyaları ve klasörleri hangi sürücülerden yedekleyebilirim?

Aşağıdaki sürücü ve birim türlerini yedekleyemiyoruz:

* Çıkarılabilir medya: tüm yedekleme öğesi kaynakları sabit olarak raporlanmalıdır.
* Salt okuma birimleri: birim gölge kopyası hizmeti (VSS) işlevinin çalışması için birimin yazılabilir olması gerekir.
* Çevrimdışı birimler: VSS 'nin çalışması için birimin çevrimiçi olması gerekir.
* Ağ paylaşımları: birim, çevrimiçi yedekleme kullanılarak yedeklenecek sunucuda yerel olmalıdır.
* BitLocker korumalı birimler: yedeklemenin gerçekleşebilmesi için birimin kilidinin açılması gerekir.
* Dosya Sistemi Tanımı: NTFS, desteklenen tek dosya sistemidir.

### <a name="what-file-and-folder-types-are-supported"></a>Hangi dosya ve klasör türleri desteklenir?

Yedekleme için desteklenen dosya ve klasörlerin türleri hakkında [daha fazla bilgi edinin](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) .

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Azure VM 'de dosya ve klasörleri yedeklemek için MARS aracısını kullanabilir miyim?  

Evet. Azure Backup, Azure VM aracısına yönelik VM uzantısını kullanarak Azure VM 'Leri için VM düzeyinde yedekleme sağlar. VM 'deki Konuk Windows işletim sistemi üzerindeki dosya ve klasörleri yedeklemek istiyorsanız, bunu yapmak için MARS aracısını yükleyebilirsiniz.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Azure VM 'nin geçici depolamadaki dosya ve klasörleri yedeklemek için MARS aracısını kullanabilir miyim?

Evet. MARS aracısını yükleyip Konuk Windows işletim sistemi üzerindeki dosya ve klasörleri geçici depolama alanına yedekleyin.

* Geçici depolama verileri silinmeden yedekleme işleri başarısız olur.
* Geçici depolama verileri silinirse, yalnızca geçici olmayan depolamaya geri yükleyebilirsiniz.

### <a name="how-do-i-register-a-server-to-another-region"></a>Nasıl yaparım? sunucuyu başka bir bölgeye kaydetmek istiyor musunuz?

Yedekleme verileri, sunucunun kayıtlı olduğu kasadaki veri merkezine gönderilir. Veri merkezini değiştirmenin en kolay yolu, aracıyı kaldırmak ve yeniden yüklemek ve ardından makineyi ihtiyacınız olan bölgede yeni bir kasaya kaydetmeniz gerekir.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS Aracısı Windows Server 2012 Yinelenenleri kaldırmayı destekliyor mu?

Evet. MARS Aracısı, yinelenenleri kaldırılmış verileri, yedekleme işlemini hazırlarken normal verilere dönüştürür. Ardından verileri yedekleme için iyileştirir, verileri şifreler ve sonra şifreli verileri kasaya gönderir.

## <a name="manage-backups"></a>Yedekleri yönetme

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Yedekleme için yapılandırılmış bir Windows makinesini yeniden adlandırdığımda ne olur?

Bir Windows makinesini yeniden adlandırdığınızda, şu anda yapılandırılmış tüm yedeklemeler durdurulur.

* Yeni makine adını yedekleme kasasıyla kaydetmeniz gerekir.
* Yeni adı kasayla kaydettiğinizde, ilk işlem *tam* yedekleme olur.
* Eski sunucu adıyla kasaya yedeklenen verileri kurtarmanız gerekiyorsa, verileri kurtar sihirbazındaki alternatif bir konuma geri yükleme seçeneğini kullanın. [Daha fazla bilgi edinin](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Yedekleme için en fazla dosya yolu uzunluğu nedir?

MARS Aracısı NTFS kullanır ve [Windows API 'siyle](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)sınırlı olan FilePath length belirtimini kullanır. Korumak istediğiniz dosyalar izin verilen değerden uzunsa, üst klasörü veya disk sürücüsünü yedekleyin.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Dosya yollarında hangi karakterlere izin veriliyor?

MARS Aracısı NTFS kullanır ve dosya adlarında/yollarında [desteklenen karakterlere](/windows/desktop/FileIO/naming-a-file#naming-conventions) izin verir.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>"Azure yedeklemeleri bu sunucu için yapılandırılmamış" uyarısı görünür.

Bu uyarı, yerel sunucuda depolanan yedekleme zamanlaması ayarları, yedekleme kasasında depolanan ayarlarla aynı olmadığında da bir yedekleme ilkesi yapılandırdığınız halde görünebilir.

* Sunucu veya ayarlar bilinen iyi bir duruma kurtarıldığı zaman, yedekleme zamanlamaları eşitlenmemiş hale gelebilir.
* Bu uyarıyı alırsanız, yedekleme ilkesini yeniden [yapılandırın](backup-azure-manage-windows-server.md) ve ardından yerel sunucuyu Azure ile yeniden eşitlemek için isteğe bağlı bir yedekleme çalıştırın.

## <a name="manage-the-backup-cache-folder"></a>Yedekleme önbelleği klasörünü yönetme

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Önbellek klasörü için minimum boyut gereksinimini nedir?

Önbellek klasörünün boyutu, yedeklediğiniz veri miktarını belirler.

* Önbellek klasörü birimlerinde toplam yedekleme verileri boyutunun en az% 5-10 ' ü eşit olan boş alan bulunmalıdır.
* Birimde %5 ' ten az boş alan varsa, birim boyutunu artırın ya da önbellek klasörünü yeterli alana sahip bir birime taşıyın.
* Windows sistem durumunu yedeklemenizin, önbellek klasörünü içeren birimde ek 30-35 GB boş alana ihtiyacınız olacaktır.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Karalama klasörünün geçerli ve erişilebilir olup olmadığını denetleme

1. Varsayılan olarak karalama klasörü `\Program Files\Microsoft Azure Recovery Services Agent\Scratch` konumunda bulunur
2. Karalama klasörü konumunun yolunun aşağıda gösterilen kayıt defteri anahtarı girişlerinin değerleriyle eşleştiğinden emin olun:

  | Kayıt defteri yolu | Kayıt Defteri Anahtarı | Değer |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Yeni önbellek klasörü konumu* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Yeni önbellek klasörü konumu* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Nasıl yaparım? MARS aracısının önbellek konumu değiştirilsin mi?

1. Yedekleme altyapısını durdurmak için bu komutu yükseltilmiş bir komut isteminde çalıştırın:

    ```Net stop obengine```

2. Sistem durumu yedeklemesini yapılandırdıysanız, disk yönetimi 'ni açın ve `"CBSSBVol_<ID>"`biçimdeki disk (ler) i çıkarın.
3. Dosyaları taşımayın. Bunun yerine, önbellek alanı klasörünü yeterli alana sahip farklı bir sürücüye kopyalayın.
4. Aşağıdaki kayıt defteri girdilerini yeni önbellek klasörünün yoluyla güncelleştirin.

    | Kayıt defteri yolu | Kayıt Defteri Anahtarı | Değer |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Yeni önbellek klasörü konumu* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Yeni önbellek klasörü konumu* |

5. Yedekleme altyapısını yükseltilmiş bir komut isteminde yeniden başlatın:

  ```command
  Net stop obengine

  Net start obengine
  ```

6. İsteğe bağlı yedekleme çalıştırın. Yedekleme işlemi yeni konumu kullanarak başarılı bir şekilde tamamlandıktan sonra özgün önbellek klasörünü kaldırabilirsiniz.

### <a name="where-should-the-cache-folder-be-located"></a>Önbellek klasörü nerede konumlandırılmalıdır?

Önbellek klasörü için aşağıdaki konumlar önerilmez:

* Ağ paylaşma/çıkarılabilir medya: önbellek klasörü, çevrimiçi yedekleme kullanılarak yedeklenmesi gereken sunucu için yerel olmalıdır. Ağ konumları veya USB sürücüleri gibi çıkarılabilir medya desteklenmez
* Çevrimdışı birimler: önbellek klasörü, Azure Backup Aracısı kullanılarak beklenen yedekleme için çevrimiçi olmalıdır

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Önbellek klasörünün desteklenmeyen öznitelikleri var mı?

Aşağıdaki öznitelikler veya bunların bileşimleri, önbellek klasörü için desteklenmez:

* Şifreli
* Yinelenenleri kaldırma işlemi uygulanmış
* Sıkıştırılmış
* Seyrek
* Yeniden Ayrıştırma Noktası

Önbellek klasörü ve meta veri VHD’si, Azure Backup aracısı için gerekli özniteliklere sahip değildir.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Yedekleme için kullanılan bant genişliği miktarını ayarlamak için bir yol var mı?

Evet, bant genişliğini ve zamanlamayı ayarlamak için MARS aracısındaki **Özellikleri Değiştir** seçeneğini kullanabilirsiniz. [Daha fazla bilgi edinin](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Geri Yükleme

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Devam eden bir geri yükleme işini iptal etdiğimde ne olur?

Devam eden bir geri yükleme işi iptal edilirse, geri yükleme işlemi duraklar. İptal etmeden önce tüm dosyalar, geri alma yapılmadan yapılandırılmış hedefte (orijinal veya alternatif konum) kalır.

## <a name="next-steps"></a>Sonraki adımlar

Bir Windows makinesini nasıl [yedekleyeceğinizi öğrenin](tutorial-backup-windows-server-to-azure.md) .
