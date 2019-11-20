---
title: Sistem durumu ve tam kurtarma koruması
description: Sistem durumunu yedeklemek ve tam kurtarma (BMR) koruması sağlamak için Azure Backup Sunucusu kullanın.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2940ef5b8c0c2a7d751c46209253d4f4dbe6d13f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172258"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Sistem durumunu yedekleme ve Azure Backup Sunucusu ile çıplak sisteme geri yükleme

Azure Backup Sunucusu sistem durumunu yedekler ve tam kurtarma (BMR) koruması sağlar.

* **Sistem durumu yedeklemesi**: bir bilgisayar başladığında kurtarabilmeniz için işletim sistemi dosyalarını yedekler, ancak sistem dosyaları ve kayıt defteri kaybedilir. Bir sistem durumu yedeklemesi şunları içerir:
  * Etki alanı üyesi: önyükleme dosyaları, COM+ sınıf kaydı veritabanı, kayıt defteri
  * Etki alanı denetleyicisi: Windows Server Active Directory (NTDS), önyükleme dosyaları, COM+ sınıf kaydı veritabanı, kayıt defteri, sistem birimi (SYSVOL)
  * Küme hizmetlerini çalıştıran bilgisayar: küme sunucusu meta verileri
  * Sertifika Hizmetleri 'ni çalıştıran bilgisayar: sertifika verileri
* **Çıplak yedekleme**: işletim sistemi dosyalarını ve kritik birimlerdeki tüm verileri (Kullanıcı verileri hariç) yedekler. Tanım olarak bir BMR yedeklemesi, sistem durumu yedeklemesi içerir. Bir bilgisayar başlamadığınızda ve her şeyi kurtarmanız gerektiğinde koruma sağlar.

Aşağıdaki tabloda neleri yedekleyebileceğiniz ve kurtarabileceğiniz özetlenmektedir. Sistem durumu ve BMR ile korunabilen uygulama sürümleri hakkında ayrıntılı bilgi için bkz. [ne Azure Backup sunucusu yedeklenecek?](backup-mabs-protection-matrix.md).

|Backup|Sorun|Azure Backup Sunucusu yedekten kurtar|Sistem durumu yedeklemesinden kurtarma|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dosya verileri**<br /><br />Normal veri yedekleme<br /><br />BMR/sistem durumu yedeklemesi|Kayıp dosya verileri|E|N|N|
|**Dosya verileri**<br /><br />Azure Backup Sunucusu dosya verileri yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp veya hasarlı işletim sistemi|N|E|E|
|**Dosya verileri**<br /><br />Azure Backup Sunucusu dosya verileri yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp sunucu (veri birimleri bozulmamış)|N|N|E|
|**Dosya verileri**<br /><br />Azure Backup Sunucusu dosya verileri yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp sunucu (veri birimleri kayıp)|E|Hayır|Evet (BMR, ardından yedeklenen dosya verilerinin normal kurtarması)|
|**SharePoint verileri**:<br /><br />Azure Backup Sunucusu grubu verileri yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp site, listeler, liste öğeleri, belgeler|E|N|N|
|**SharePoint verileri**:<br /><br />Azure Backup Sunucusu grubu verileri yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp veya hasarlı işletim sistemi|N|E|E|
|**SharePoint verileri**:<br /><br />Azure Backup Sunucusu grubu verileri yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Olağanüstü durum kurtarma|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />Ana bilgisayarın BMR/sistem durumu yedeklemesi|Kayıp VM|E|N|N|
|Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />Ana bilgisayarın BMR/sistem durumu yedeklemesi|Kayıp veya hasarlı işletim sistemi|N|E|E|
|Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />Ana bilgisayarın BMR/sistem durumu yedeklemesi|Kayıp Hyper-V konağı (VM 'Ler bozulmadan)|N|N|E|
|Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />Ana bilgisayarın BMR/sistem durumu yedeklemesi|Kayıp Hyper-V konağı (VM 'Ler kayıp)|N|N|E<br /><br />BMR, ardından normal Azure Backup Sunucusu kurtarma|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp uygulama verileri|E|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp veya hasarlı işletim sistemi|N|Y|E|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp sunucu (veritabanı/işlem günlükleri bozulmamış)|N|N|E|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/sistem durumu yedeklemesi|Kayıp sunucu (veritabanı/işlem günlükleri kayıp)|N|N|E<br /><br />BMR kurtarma, ardından normal Azure Backup Sunucusu kurtarma|

## <a name="how-system-state-backup-works"></a>Sistem durumu yedeklemesinin nasıl çalıştığı

Bir sistem durumu yedeklemesi çalıştığında, yedekleme sunucusu sunucunun sistem durumunun bir yedeğini istemek için Windows Server Yedekleme ile iletişim kurar. Varsayılan olarak, yedekleme sunucusu ve Windows Server Yedekleme en fazla kullanılabilir boş alana sahip olan sürücüyü kullanır. Bu sürücü hakkındaki bilgiler PSDataSourceConfig. xml dosyasına kaydedilir. Bu, Windows Server Yedekleme yedeklemeler için kullanılan sürücüdür.

Yedekleme sunucusunun sistem durumu yedeklemesi için kullandığı sürücüyü özelleştirebilirsiniz. Korunan sunucuda C:\Program Files\Microsoft Data Protection Manager\mabs\datasourcesdizinine gidin. Düzenlenmek üzere PSDataSourceConfig. xml dosyasını açın. Sürücü harfi için \<FilesToProtect\> değerini değiştirin. Dosyayı kaydedin ve kapatın. Bilgisayarın sistem durumunu korumak üzere ayarlanmış bir koruma grubu varsa bir tutarlılık denetimi çalıştırın. Bir uyarı oluşturulursa, uyarıdaki **koruma grubunu değiştir** ' i seçin ve ardından Sihirbazı doldurun. Ardından başka bir tutarlılık denetimi çalıştırın.

Koruma sunucusu bir kümedeyse, en fazla boş alana sahip olan sürücü olarak bir küme sürücüsünün seçilebileceğini unutmayın. Bu sürücü sahipliği başka bir düğüme dönüştürüldüğünde ve sistem durumu yedeklemesi çalıştırılıyorsa, sürücü kullanılamaz ve yedekleme başarısız olur. Bu senaryoda, PSDataSourceConfig. xml ' yi yerel bir sürücüye işaret etmek üzere değiştirin.

Sonra, Windows Server Yedekleme restore klasörünün kökünde WindowsImageBackup adlı bir klasör oluşturur. Windows Server Yedekleme yedekleme oluştururken tüm veriler bu klasöre yerleştirilir. Yedekleme tamamlandığında, dosya yedekleme sunucusu bilgisayarına aktarılır. Aşağıdaki bilgileri not edin:

* Yedekleme veya aktarım bittiğinde bu klasör ve içeriği temizlenmez. Bunu düşünmenin en iyi yolu, bir sonraki yedeklemenin tamamlanışında alanın ayrılmaktır.
* Klasör her yedekleme yapıldığında oluşturulur. Saat ve tarih damgası, son sistem durumu yedeklemenizin zamanını yansıtır.

## <a name="bmr-backup"></a>BMR yedekleme

BMR için (bir sistem durumu yedeklemesi dahil), yedekleme işi doğrudan yedekleme sunucusu bilgisayarındaki bir paylaşıma kaydedilir. Korumalı sunucudaki bir klasöre kaydedilmez.

Yedekleme sunucusu Windows Server Yedekleme çağırır ve bu BMR yedekleme için çoğaltma birimini paylaşır. Bu durumda, en fazla boş alana sahip olan sürücüyü kullanmak Windows Server Yedekleme söylemez. Bunun yerine, iş için oluşturulan paylaşımdan yararlanır.

Yedekleme tamamlandığında, dosya yedekleme sunucusu bilgisayarına aktarılır. Günlükler C:\windows\logs\windowsserverbackupkonumunda depolanır.

## <a name="prerequisites-and-limitations"></a>Önkoşullar ve sınırlamalar

* BMR, Windows Server 2003 çalıştıran bilgisayarlar veya bir istemci işletim sistemi çalıştıran bilgisayarlar için desteklenmez.

* Farklı koruma gruplarındaki aynı bilgisayar için BMR 'yi ve sistem durumunu koruyamazsınız.

* Bir yedekleme sunucusu bilgisayarı BMR için kendisini koruyamaz.

* Banda kısa vadeli koruma (diskten banda veya D2T) BMR için desteklenmez. Banda uzun vadeli depolama (diskten diske ve banda veya D2D2T) desteklenir.

* BMR koruması için, Windows Server Yedekleme korunan bilgisayarda yüklü olmalıdır.

* BMR koruması için, sistem durumu korumasından farklı olarak, yedekleme sunucusunda korunan bilgisayarda herhangi bir alan gereksinimi yoktur. Windows Server Yedekleme yedeklemeleri yedekleme sunucusu bilgisayarına doğrudan aktarır. Yedekleme aktarma işi yedekleme sunucusu **işleri** görünümünde görünmüyor.

* Backup Server, BMR için çoğaltma biriminde 30 GB alan ayırır. Bunu, koruma grubunu değiştirme Sihirbazı 'ndaki **disk ayırma** sayfasında veya Get-DatasourceDiskAllocation ve set-DatasourceDiskAllocation PowerShell cmdlet 'lerini kullanarak değiştirebilirsiniz. Kurtarma noktası biriminde BMR koruması, beş gün bekletme için yaklaşık 6 GB gerektirir.
  * Çoğaltma birimi boyutunun 15 GB 'tan az olamayacağını unutmayın.
  * Yedekleme sunucusu BMR veri kaynağının boyutunu hesaplamaz. Tüm sunucular için 30 GB olduğunu varsayar. Ortamınızda beklediğinizi BMR yedeklemelerinin boyutuna göre değeri değiştirin. Bir BMR yedeklemesinin boyutu kabaca, tüm kritik birimlerde kullanılan alanın toplamı olarak hesaplanabilir. Kritik birimler = Önyükleme birimi + sistem birimi + Active Directory gibi sistem durumu verilerini barındıran birim.

* Sistem durumu korumasından BMR korumasına geçiş yaparsanız, BMR koruması, *Kurtarma noktası biriminde*daha az alan gerektirir. Ancak, birimdeki ek alan geri kazanılır. Koruma grubunu değiştirme Sihirbazı 'nın **disk ayırmayı Değiştir** sayfasında birim boyutunu el ile küçültebilirsiniz veya Get-DatasourceDiskAllocation ve set-DatasourceDiskAllocation PowerShell cmdlet 'lerini kullanabilirsiniz.

    Sistem durumu korumasından BMR korumasına geçiş yaparsanız, BMR koruması, *Çoğaltma biriminde*daha fazla alan gerektirir. Birim otomatik olarak genişletilir. Varsayılan alan ayırmalarını değiştirmek istiyorsanız, modify-DiskAllocation PowerShell cmdlet 'ini kullanın.

* BMR korumasından sistem durumu korumasına geçiş yaparsanız, kurtarma noktası biriminde daha fazla alana sahip olmanız gerekir. Yedekleme sunucusu birimi otomatik olarak artırmayı deneyebilir. Depolama havuzunda yeterli alan yoksa bir hata oluşur.

    BMR korumasından sistem durumu korumasına geçiş yaparsanız, korunan bilgisayarda alan gerekir. Bunun nedeni, sistem durumu korumasının önce çoğaltmayı yerel bilgisayara yazması ve ardından bunu yedekleme sunucusu bilgisayarına aktarmasıdır.

## <a name="before-you-begin"></a>Başlamadan önce

1. **Azure Backup sunucusu dağıtın**. Yedekleme sunucusunun doğru şekilde dağıtıldığını doğrulayın. Daha fazla bilgi için bkz.
    * [Azure Backup Sunucusu için sistem gereksinimleri](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Yedekleme sunucusu koruma matrisi](backup-mabs-protection-matrix.md)

2. **Depolamayı ayarlayın**. Yedekleme verilerini diskte, bantta ve Azure ile bulutta saklayabilirsiniz. Daha fazla bilgi için bkz. [veri depolamayı hazırlama](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3. **Koruma aracısını ayarlayın**. Koruma aracısını yedeklemek istediğiniz bilgisayara kurun. Daha fazla bilgi için bkz. [DPM koruma aracısını dağıtma](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Sistem durumunu yedekleme ve tam kurtarma

Koruma [gruplarını dağıtma](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)bölümünde açıklandığı gibi bir koruma grubu ayarlayın. Farklı gruplardaki aynı bilgisayar için BMR 'yi ve sistem durumunu koruyamayacağınızı unutmayın. Ayrıca, BMR 'yi seçtiğinizde sistem durumu otomatik olarak etkinleştirilir.

1. Yedekleme sunucusu Yönetici Konsolu **yeni koruma grubu** oluşturma Sihirbazı 'nı açmak için koruma > **Eylemler** > **koruma grubu oluştur**' u seçin.

2. **Koruma grubu türünü seçin** sayfasında **sunucular**' ı seçin ve ardından **İleri**' yi seçin.

3. **Grup üyelerini seçin** sayfasında, bilgisayarı genişletin ve sonra **BMR** veya **sistem durumu**' nu seçin.

    Farklı gruplardaki aynı bilgisayar için BMR ve sistem durumunu koruyamayacağınızı unutmayın. Ayrıca, BMR 'yi seçtiğinizde sistem durumu otomatik olarak etkinleştirilir. Daha fazla bilgi için bkz. [koruma gruplarını dağıtma](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4. **Veri koruma yöntemini seçin** sayfasında, kısa vadeli ve uzun vadeli yedeklemeyi nasıl işlemek istediğinizi seçin. Kısa süreli yedekleme, Azure Backup (kısa dönem veya uzun dönem) kullanarak diskten Azure bulutuna yedekleme seçeneğiyle her zaman ilk olarak diske yapılır. Buluta uzun süreli yedeklemeye alternatif olarak, bir tek başına bant cihazına veya yedekleme sunucusuna bağlı bant kitaplığına uzun süreli yedekleme ayarlanalım.

5. **Kısa vadeli hedefleri seçin** sayfasında, diskte kısa süreli depolamaya nasıl yedekleme yapmak istediğinizi seçin:
    1. **Bekletme aralığı**için, verileri diskte ne kadar süreyle saklamak istediğinizi seçin.
    2. **Eşitleme sıklığı**için, diskte artımlı bir yedeklemeyi ne sıklıkta çalıştırmak istediğinizi seçin. Bir yedekleme aralığı ayarlamak istemiyorsanız, **bir kurtarma noktası seçeneğinden hemen önce** ' yi kontrol edebilirsiniz. Yedekleme sunucusu, her bir kurtarma noktası zamanlanmadan hemen önce bir hızlı ve tam yedekleme çalıştırır.

6. Verileri uzun vadeli depolama için banda depolamak istiyorsanız, **uzun vadeli hedefleri belirtin** sayfasında, bant verilerini ne kadar süreyle saklamak istediğinizi seçin (1-99 yıl).
    1. **Yedekleme sıklığı**için, banttaki yedeklemenin ne sıklıkta çalışacağını seçin. Sıklık, seçtiğiniz bekletme aralığına göre belirlenir:
        * Bekletme aralığı 1-99 yıl olduğunda, yedeklemeleri günlük, haftalık, iki haftada bir, aylık, üç aylık, yarı yıllık veya yıllık gerçekleşecek şekilde seçebilirsiniz.
        * Bekletme aralığı 1-11 ay olduğunda, yedeklemeleri günlük, haftalık, iki haftada bir veya aylık gerçekleşecek şekilde seçebilirsiniz.
        * Bekletme aralığı 1-4 hafta olduğunda, yedeklemeleri günlük veya haftalık gerçekleşecek şekilde seçebilirsiniz.

    2. **Bant ve kitaplık ayrıntılarını Seç** sayfasında, kullanılacak bant ve kitaplığı ve verilerin sıkıştırılması ve şifrelenmesi gerekip gerekmediğini seçin.

7. **Disk ayırmayı İncele** sayfasında, koruma grubu için ayrılan depolama havuzu disk alanını gözden geçirin.

    1. **Toplam veri boyutu** , yedeklemek istediğiniz verilerin boyutudur.
    2. **Azure Backup sunucusu sağlanacak disk alanı** , yedekleme sunucusunun koruma grubu için önerdiği alandır. Yedekleme sunucusu, ayarlara bağlı olarak ideal yedekleme birimini seçer. Ancak, **disk ayırma ayrıntılarında**yedekleme birimi seçimlerini düzenleyebilirsiniz.
    3. İş yükleri için açılan menüden tercih edilen depolamayı seçin. Düzenlemeleriniz, **kullanılabilir disk depolama** bölmesinde **toplam depolama** ve **boş depolama** değerlerini değiştirir. Yetersiz sağlanan alan, kesintisiz yedeklemeler sağlamak için yedekleme sunucusunun birime eklemenizi önerdiği depolama miktarıdır.

8. **Çoğaltma oluşturma yöntemini seçin** sayfasında, ilk tam veri çoğaltmasını nasıl işlemek istediğinizi seçin. Ağ üzerinden çoğaltmayı seçerseniz, yoğun olmayan bir zaman seçmeniz önerilir. Büyük miktarlarda veri veya en iyi durumda olmayan ağ koşulları için, çıkarılabilir medya kullanarak verileri çevrimdışı olarak çoğaltmayı göz önünde bulundurun.

9. **Tutarlılık denetimi seçenekleri seçin** sayfasında, tutarlılık denetimlerinin nasıl otomatikleştirilmesi istediğinizi seçin. Yalnızca çoğaltma verileri tutarsız hale geldiğinde veya bir zamanlamaya göre bir denetim çalıştırmayı seçebilirsiniz. Otomatik tutarlılık denetimini yapılandırmak istemiyorsanız, istediğiniz zaman el ile denetim gerçekleştirebilirsiniz. El ile denetim çalıştırmak için, yedekleme sunucusu Yönetici Konsolu **koruma** alanında, koruma grubuna sağ tıklayın ve ardından **tutarlılık denetimi gerçekleştir**' i seçin.

10. Azure Backup kullanarak buluta yedeklemeyi seçtiyseniz, **çevrimiçi koruma verilerini belirtin** sayfasında, Azure 'a yedeklemek istediğiniz iş yüklerini seçtiğinizden emin olun.

11. **Çevrimiçi yedekleme zamanlamasını belirtin** sayfasında, Azure 'a yönelik artımlı yedeklemelerin nasıl olacağını seçin. Yedeklemeleri her gün, hafta, ay ve yıl çalışacak şekilde zamanlayabilir ve çalıştırılacağı saati ve tarihi seçebilirsiniz. Yedeklemeler günde en fazla iki kez bulunabilir. Yedekleme her çalıştığında, yedekleme sunucusu diskinde depolanan yedekleme verilerinin kopyasından Azure 'da bir veri kurtarma noktası oluşturulur.

12. **Çevrimiçi saklama Ilkesini belirtin** sayfasında, günlük, haftalık, aylık ve yıllık yedeklerden oluşturulan kurtarma noktalarının Azure 'da nasıl korunduğunu seçin.

13. **Çevrimiçi çoğaltma Seç** sayfasında, verilerin ilk tam çoğaltmasının nasıl gerçekleşeceğini seçin. Ağ üzerinden çoğaltma yapabilir veya çevrimdışı yedekleme yapabilirsiniz (çevrimdışı dengeli dağıtım). Çevrimdışı yedekleme, Azure Içeri aktarma özelliğini kullanır. Daha fazla bilgi için bkz. [Azure Backup çevrimdışı yedekleme iş akışı](backup-azure-backup-import-export.md).

14. **Özet** sayfasında, ayarlarınızı gözden geçirin. **Grup Oluştur**' u seçtikten sonra, verilerin ilk çoğaltması oluşur. Veri çoğaltma tamamlandığında, **durum** sayfasında, koruma grubu durumu **Tamam**' dır. Yedekleme, koruma grubu ayarlarına göre gerçekleşir.

## <a name="recover-system-state-or-bmr"></a>Sistem durumu veya BMR kurtarma

BMR 'yi veya sistem durumunu bir ağ konumuna kurtarabilirsiniz. BMR 'yi yedeklediyseniz, sisteminizi başlatmak ve ağa bağlamak için Windows kurtarma ortamı 'nı (WinRE) kullanın. Ardından, ağ konumundan kurtarmak için Windows Server Yedekleme kullanın. Sistem durumunu yedeklediyseniz, ağ konumundan kurtarmak için Windows Server Yedekleme kullanmanız yeterlidir.

### <a name="restore-bmr"></a>BMR 'yi geri yükle

Yedekleme sunucusu bilgisayarında kurtarma çalıştırın:

1. **Kurtarma** bölmesinde, kurtarmak istediğiniz bilgisayarı bulun ve **tam kurtarma**' yı seçin.

2. Kullanılabilir kurtarma noktaları, takvimde kalın olarak gösterilir. Kullanmak istediğiniz kurtarma noktasının tarihini ve saatini seçin.

3. **Kurtarma türünü seçin** sayfasında **bir ağ klasörüne kopyala** ' yı seçin.

4. **Hedef belirtin** sayfasında, verileri kopyalamak istediğiniz yeri seçin. Seçili hedefin yeterli yere sahip olması gerektiğini unutmayın. Yeni bir klasör oluşturmanızı öneririz.

5. **Kurtarma seçeneklerini belirtin** sayfasında, uygulanacak güvenlik ayarlarını seçin. Daha sonra, daha hızlı kurtarma için depolama alanı ağı (SAN) tabanlı donanım anlık görüntülerini kullanmak isteyip istemediğinizi seçin. (Bu, yalnızca bu işlevselliğe sahip bir SAN 'a sahipseniz ve bir kopyayı yazılabilir yapmak için bölme yeteneği olduğunda bir seçenektir. Ayrıca, korumalı bilgisayar ve yedek sunucu bilgisayarının aynı ağa bağlı olması gerekir.)

6. Bildirim seçeneklerini ayarlayın. **Onay** sayfasında, **kurtar**' ı seçin.

Paylaşma konumunu ayarlayın:

1. Geri yükleme konumunda, yedeği olan klasöre gidin.

2. Paylaşılan klasörün kökünün WindowsImageBackup klasörü olabilmesi için WindowsImageBackup üzerindeki bir düzey klasörü paylaşabilirsiniz. Bunu yapmazsanız, geri yükleme yedeği bulamaz. Windows kurtarma ortamı 'nı (WinRE) kullanarak bağlanmak için, WinRE 'de doğru IP adresi ve kimlik bilgileriyle erişebileceğiniz bir paylaşıma ihtiyacınız vardır.

Sistemi geri yükleme:

1. Geri yüklemekte olduğunuz sistem için Windows DVD 'sini kullanarak resmi geri yüklemek istediğiniz bilgisayarı başlatın.

2. İlk sayfada dili ve yerel ayarları doğrulayın. **Install** sayfasında, **Bilgisayarınızı onarın**' ı seçin.

3. **Sistem kurtarma seçenekleri** sayfasında, **daha önce oluşturduğunuz bir sistem görüntüsünü kullanarak bilgisayarınızı geri yükle**' yi seçin.

4. **Bir sistem görüntüsü yedeklemesi seçin** sayfasında, **Gelişmiş** >  > **sistem görüntüsü Seç** ' i seçin ve **ağ üzerinde bir sistem görüntüsü arayın**. Bir uyarı görünürse **Evet**' i seçin. Paylaşma yoluna gidin, kimlik bilgilerini girin ve kurtarma noktasını seçin. Bu, kurtarma noktasında kullanılabilir olan belirli yedekleri tarar. Kullanmak istediğiniz kurtarma noktasını seçin.

5. **Yedeklemenin nasıl geri yükleneceğini seçin** sayfasında, **Diskleri biçimlendir ve yeniden bölümle**' yı seçin. Sonraki sayfada ayarları doğrulayın.

6. Geri yüklemeyi başlatmak için **son**' u seçin. Yeniden başlatma gerekiyor.

### <a name="restore-system-state"></a>Sistem durumunu geri yükle

Yedekleme sunucusunda kurtarmayı Çalıştır:

1. **Kurtarma** bölmesinde, kurtarmak istediğiniz bilgisayarı bulun ve **tam kurtarma**' yı seçin.

2. Kullanılabilir kurtarma noktaları, takvimde kalın olarak gösterilir. Kullanmak istediğiniz kurtarma noktasının tarihini ve saatini seçin.

3. **Kurtarma türünü seçin** sayfasında **bir ağ klasörüne kopyala**' yı seçin.

4. **Hedef belirtin** sayfasında, verileri kopyalamak istediğiniz yeri seçin. Seçili hedefin yeterli yere ihtiyacı olduğunu unutmayın. Yeni bir klasör oluşturmanızı öneririz.

5. **Kurtarma seçeneklerini belirtin** sayfasında, uygulanacak güvenlik ayarlarını seçin. Daha sonra, daha hızlı kurtarma için SAN tabanlı donanım anlık görüntülerini kullanmak isteyip istemediğinizi seçin. (Bu, yalnızca bu işlevselliğe sahip bir SAN 'a sahipseniz ve bir kopyayı yazılabilir yapmak için bölme özelliği olan bir seçenektir. Ayrıca, korumalı bilgisayar ve yedek sunucu sunucusunun aynı ağa bağlı olması gerekir.)

6. Bildirim seçeneklerini ayarlayın. **Onay** sayfasında, **kurtar**' ı seçin.

Windows Server Yedekleme Çalıştır:

1. **Bu sunucuya** > **kurtarmak** > **Eylemler** ' i > **İleri**' ye tıklayın.

2. **Başka bir sunucu**seçin, **konum türünü belirtin** sayfasını seçin ve ardından **uzak paylaşılan klasör**' i seçin. Kurtarma noktasını içeren klasörün yolunu girin.

3. **Kurtarma türünü seçin** sayfasında **sistem durumu**' nu seçin.

4. **Sistem durumu kurtarma Için konum seçin** sayfasında, **orijinal konum**' u seçin.

5. **Onay** sayfasında, **kurtar**' ı seçin. Geri yüklemeden sonra sunucuyu yeniden başlatın.

6. Ayrıca sistem durumu geri yüklemesini bir komut isteminde çalıştırabilirsiniz. Bunu yapmak için, kurtarmak istediğiniz bilgisayarda Windows Server Yedekleme başlatın. Sürüm tanımlayıcısını almak için, bir komut isteminde şunu girin: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Sistem durumu geri yüklemeyi başlatmak için sürüm tanımlayıcıyı kullanın. Komut isteminde şunu girin: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Kurtarmayı başlatmak istediğinizi onaylayın. İşlemi komut Istemi penceresinde görebilirsiniz. Geri yükleme günlüğü oluşturulur. Geri yüklemeden sonra sunucuyu yeniden başlatın.
