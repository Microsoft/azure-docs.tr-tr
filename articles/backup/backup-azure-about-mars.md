---
title: MARS Aracısı hakkında
description: MARS aracısının yedekleme senaryolarını nasıl desteklediğini öğrenin
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517751"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı hakkında

Bu makalede, Azure Backup hizmetinin dosyaları, klasörleri ve birim ya da sistem durumunu şirket içi bir bilgisayardan Azure 'a yedeklemek ve geri yüklemek için Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı nasıl kullandığı açıklanır.

## <a name="backup-scenarios"></a>Yedekleme senaryoları

MARS Aracısı aşağıdaki yedekleme senaryolarını destekler:

![MARS yedekleme senaryoları](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Dosyalar ve klasörler**: Windows dosya ve klasörlerini seçmeli olarak koruyun.
- **Birim düzeyi**: makinenizin bir Windows biriminin tamamını koruyun.
- **Sistem düzeyi**: tüm Windows sistem durumunu koruyun.

### <a name="additional-scenarios"></a>İlave senaryolar

- **Azure sanal makineler 'de belirli dosya ve klasörleri** yedekleme: Azure sanal makinelerini (VM) yedeklemeye yönelik BIRINCIL Yöntem VM 'de bir Azure Backup uzantısı kullanmaktır. Uzantı tüm VM 'yi yedekler. Bir VM içindeki belirli dosya ve klasörleri yedeklemek istiyorsanız, Azure VM 'lerine MARS Aracısı 'nı yükleyebilirsiniz. Daha fazla bilgi için bkz. [mimari: yerleşik Azure VM yedeklemesi](./backup-architecture.md#architecture-built-in-azure-vm-backup).

- **Çevrimdışı dengeli dağıtım**: Azure 'a verilerin ilk tam yedeklemeleri genellikle büyük miktarda veriyi aktarır ve daha fazla ağ bant genişliği gerektirir. Sonraki yedeklemeler yalnızca Delta veya artımlı, veri miktarını aktarır. Azure Backup ilk yedeklemeleri sıkıştırır. Azure Backup *çevrimdışı dağıtım* sürecinde, sıkıştırılmış ilk yedekleme verilerini çevrimdışı olarak Azure 'a yüklemek için diskleri kullanabilir. Daha fazla bilgi için bkz. [Azure Data Box kullanarak çevrimdışı yedekleme Azure Backup](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Geri yükleme senaryoları

MARS Aracısı aşağıdaki geri yükleme senaryolarını destekler:

![MARS kurtarma senaryoları](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Aynı sunucu**: yedeklemenin ilk olarak oluşturulduğu sunucu.
  - **Dosyalar ve klasörler**: geri yüklemek istediğiniz tek tek dosyaları ve klasörleri seçin.
  - **Birim düzeyi**: geri yüklemek istediğiniz birimi ve kurtarma noktasını seçin. Daha sonra aynı konuma veya aynı makinede alternatif bir konuma geri yükleyin.  Mevcut dosyaların bir kopyasını oluşturun, varolan dosyaların üzerine yazın veya var olan dosyaları kurtarmayı atlayın.
  - **Sistem düzeyi**: belirtilen konumdaki aynı makineye geri yüklemek için sistem durumu ve kurtarma noktasını seçin.

- **Alternatif sunucu**: yedeklemenin alındığı sunucudan başka bir sunucu.
  - **Dosyalar ve klasörler**: kurtarma noktasını bir hedef makineye geri yüklemek istediğiniz tek tek dosyaları ve klasörleri seçin.
  - **Birim düzeyi**: başka bir konuma geri yüklemek istediğiniz birimi ve kurtarma noktasını seçin. Mevcut dosyaların bir kopyasını oluşturun, varolan dosyaların üzerine yazın veya var olan dosyaları kurtarmayı atlayın.
  - **Sistem düzeyi**: alternatif bir makineye sistem durumu dosyası olarak geri yüklemek için sistem durumu ve kurtarma noktasını seçin.

## <a name="backup-process"></a>Yedekleme işlemi

1. Azure portal, bir [Kurtarma Hizmetleri Kasası](install-mars-agent.md#create-a-recovery-services-vault)oluşturun ve **yedekleme hedeflerinden** dosyalar, klasörler ve sistem durumu ' nu seçin.
2. [Kurtarma Hizmetleri Kasası kimlik bilgilerini ve Aracı yükleyicisini](./install-mars-agent.md#download-the-mars-agent) şirket içi bir makineye indirin.

3. [Aracıyı yükleyip](./install-mars-agent.md#install-and-register-the-agent) indirilen kasa kimlik bilgilerini kullanarak makineyi kurtarma hizmetleri kasasına kaydedin.
4. İstemcideki Aracı konsolundan, yedeklemeyi, ne zaman yedeklenmek gerektiğini, ne zaman yedeklemeniz gerektiğini (bekletme ilkesi) ve korumaya başlamak üzere [yapılandırın](./backup-windows-with-mars-agent.md#create-a-backup-policy) .

![Azure Backup aracı diyagramı](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Ek bilgiler

- **Ilk yedekleme** (ilk yedekleme), yedekleme ayarlarınıza göre çalışır.  MARS Aracısı, yedekleme için seçilen birimlerin bir noktadan noktaya anlık görüntüsünü almak için VSS 'yi kullanır. Aracı, anlık görüntüyü yakalamak için yalnızca Windows sistem yazıcısı işlemini kullanır. Hiçbir uygulama VSS yazıcısını kullanmaz ve uygulamayla tutarlı anlık görüntüleri yakalamaz. VSS ile anlık görüntüyü aldıktan sonra, MARS Aracısı, yedeklemeyi yapılandırdığınız sırada belirttiğiniz önbellek klasöründe bir sanal sabit disk (VHD) oluşturur. Aracı Ayrıca her veri bloğu için sağlama toplamlarını depolar.

- **Artımlı yedeklemeler** (sonraki yedeklemeler) belirttiğiniz zamanlamaya göre çalışır. Artımlı yedeklemeler sırasında, değiştirilen dosyalar tanımlanır ve yeni bir VHD oluşturulur. VHD sıkıştırılır ve şifrelenir ve ardından kasaya gönderilir. Artımlı yedekleme bittikten sonra, yeni VHD ilk çoğaltmadan sonra oluşturulan VHD ile birleştirilir. Bu birleştirilmiş VHD, devam eden yedekleme için kullanılmak üzere en son durumu sağlar.

- MARS Aracısı, tüm birimi tarayarak dizin veya dosyalardaki değişiklikleri denetleyerek, USN (sıra numarası güncelleştirme) değişiklik günlüğünü veya **en iyi duruma getirilmiş modu** kullanarak yedekleme işini **iyileştirilmiş modda** çalıştırabilir. En iyi duruma getirilmiş mod, aracının birimdeki her dosyayı tarayabilmesi ve değiştirilen dosyaları tespit etmek için meta verilere göre karşılaştırılabilmesi nedeniyle daha yavaştır.  **İlk yedekleme** her zaman iyileştirilmemiş modda çalışacaktır. Önceki yedekleme başarısız olduysa, sonraki zamanlanmış yedekleme işi iyileştirilmemiş modda çalışacaktır. Bu modlar ve bunların nasıl doğrulanabileceği hakkında daha fazla bilgi edinmek için [Bu makaleye](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[MARS aracısı destek matrisi](./backup-support-matrix-mars-agent.md)

[MARS Aracısı SSS](./backup-azure-file-folder-backup-faq.yml)
