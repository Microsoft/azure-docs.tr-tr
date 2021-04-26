---
title: Sistem durumu ve tam kurtarma koruması
description: Sistem durumunu yedeklemek ve çıplak kurtarma (BMR) koruması sağlamak için Azure Backup Sunucusu kullanın.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021631"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Azure Backup Sunucusu kullanarak sistem durumunu yedekleme ve çıplak sisteme geri yükleme

Azure Backup Sunucusu sistem durumunu yedekler ve tam kurtarma (BMR) koruması sağlar.

* **Sistem durumu yedeklemesi**: işletim sistemi dosyalarını yedekler. Bu yedekleme, bir bilgisayar başladığında kurtarmanızı sağlar, ancak sistem dosyaları ve kayıt defteri kaybedilir. Bir sistem durumu yedeklemesi aşağıdaki öğeleri içerir:
  * Etki alanı üyesi: Önyükleme dosyaları, COM + sınıf kaydı veritabanı, kayıt defteri
  * Etki alanı denetleyicisi: Windows Server Active Directory (NTDS), önyükleme dosyaları, COM+ sınıf kaydı veritabanı, kayıt defteri, sistem birimi (SYSVOL)
  * Küme hizmetlerini çalıştıran bilgisayar: küme sunucusu meta verileri
  * Sertifika Hizmetleri 'ni çalıştıran bilgisayar: sertifika verileri
* **Çıplak yedekleme**: Kullanıcı verileri dışında, işletim sistemi dosyalarını ve kritik birimlerdeki tüm verileri yedekler. Tanım olarak bir BMR yedeklemesi, sistem durumu yedeklemesi içerir. Bir bilgisayar başlamadığınızda ve her şeyi kurtarmanız gerektiğinde koruma sağlar.

Aşağıdaki tabloda neleri yedekleyebileceğiniz ve kurtarabileceğiniz özetlenmektedir. Sistem durumu ve BMR 'nin koruduğu uygulama sürümleri hakkında bilgi için bkz. [ne Azure Backup sunucusu yedeklenecek?](backup-mabs-protection-matrix.md).

|Backup|Sorun|Azure Backup Sunucusu yedekten kurtar|Sistem durumu yedeklemesinden kurtarma|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dosya verileri**<br /><br />Normal veri yedekleme<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp dosya verileri|E|N|N|
|**Dosya verileri**<br /><br />Azure Backup Sunucusu dosya verileri yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp veya bozuk işletim sistemi|N|E|E|
|**Dosya verileri**<br /><br />Azure Backup Sunucusu dosya verileri yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp sunucu (veri birimleri bozulmamış)|N|N|E|
|**Dosya verileri**<br /><br />Azure Backup Sunucusu dosya verileri yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp sunucu (Veri birimleri kayıp)|E|N|E<br /><br />BMR, ardından yedeklenen dosya verilerinin normal kurtarılması|
|**SharePoint verileri**<br /><br />Azure Backup Sunucusu grubu verileri yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp site, listeler, liste öğeleri, belgeler|E|N|N|
|**SharePoint verileri**<br /><br />Azure Backup Sunucusu grubu verileri yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp veya bozuk işletim sistemi|N|E|E|
|**SharePoint verileri**<br /><br />Azure Backup Sunucusu grubu verileri yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Olağanüstü durum kurtarma|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />BMR/konak sistem durumu yedeklemesi|Kayıp VM|E|N|N|
|Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />BMR/konak sistem durumu yedeklemesi|Kayıp veya bozuk işletim sistemi|N|E|E|
|Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />BMR/konak sistem durumu yedeklemesi|Kayıp Hyper-V konağı (VM’ler bozulmamış)|N|N|E|
|Hyper-V<br /><br />Hyper-V konağının veya konuğun yedeklemesini Azure Backup Sunucusu<br /><br />BMR/konak sistem durumu yedeklemesi|Kayıp Hyper-V konağı (VM’ler kayıp)|N|N|E<br /><br />BMR, ardından normal Azure Backup Sunucusu kurtarma|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp uygulama verileri|E|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp veya bozuk işletim sistemi|N|E|E|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp sunucu (veritabanı/işlem günlüğü dosyaları bozulmamış)|N|N|E|
|SQL Server/Exchange<br /><br />Azure Backup Sunucusu uygulama yedeklemesi<br /><br />BMR/Sistem durumu yedeklemesi|Kayıp sunucu (veritabanı/işlem günlüğü dosyaları kayıp)|N|N|E<br /><br />BMR kurtarma, ardından normal Azure Backup Sunucusu kurtarma|

## <a name="how-system-state-backup-works"></a>Sistem durumu yedeklemesinin çalışma şekli

Bir sistem durumu yedeklemesi çalıştığında, yedekleme sunucusu sunucunun sistem durumunun bir yedeğini istemek için Windows Server Yedekleme ile iletişim kurar. Varsayılan olarak, yedekleme sunucusu ve Windows Server Yedekleme en fazla kullanılabilir boş alana sahip olan sürücüyü kullanır. Bu sürücüyle ilgili bilgiler *PSDataSourceConfig.xml* dosyasına kaydedilir.

Yedekleme sunucusunun sistem durumu yedeklemesi için kullandığı sürücüyü özelleştirebilirsiniz:

1. Korunan sunucuda *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources* dizinine gidin.
1. *PSDataSourceConfig.xml* dosyasını düzenlenmek üzere açın.
1. \<FilesToProtect\> değerini sürücü harfi için değiştirin.
1. Dosyayı kaydedin ve kapatın.

Bir koruma grubu bilgisayarın sistem durumunu korumak üzere ayarlandıysa, bir tutarlılık denetimi çalıştırın. Bir uyarı oluşturulursa, uyarıdaki **koruma grubunu değiştir** ' i seçin ve ardından sihirbazdaki sayfaları doldurun. Ardından başka bir tutarlılık denetimi çalıştırın.

Koruma sunucusu bir kümedeyse, en fazla boş alana sahip olan sürücü olarak bir küme sürücüsü seçilebilir. Bu sürücü sahipliği başka bir düğüme geçiş yaptığında ve sistem durumu yedeklemesi çalışırsa, sürücü kullanılamaz ve yedekleme başarısız olur. Bu senaryoda *PSDataSourceConfig.xml* yerel bir sürücüye işaret etmek üzere değiştirin.

Sonra, Windows Server Yedekleme restore klasörünün kökünde *WindowsImageBackup* adlı bir klasör oluşturur. Windows Server Yedekleme yedekleme oluştururken tüm veriler bu klasöre yerleştirilir. Yedekleme tamamlandığında, dosya yedekleme sunucusu bilgisayarına aktarılır. Aşağıdaki bilgileri not edin:

* Yedekleme veya aktarım bittiğinde bu klasör ve içeriği temizlenmez. Bunu düşünmenin en iyi yolu, bir sonraki yedeklemenin tamamlanışında alanın ayrılmaktır.
* Klasör her yedekleme için oluşturulur. Saat ve tarih damgası, son sistem durumu yedeklemenizin zamanını yansıtır.

## <a name="how-bmr-backup-works"></a>BMR yedeklemesi nasıl çalışacaktır?

BMR için (bir sistem durumu yedeklemesi dahil), yedekleme işi doğrudan yedekleme sunucusu bilgisayarındaki bir paylaşıma kaydedilir. Korumalı sunucudaki bir klasöre kaydedilmez.

Yedekleme sunucusu Windows Server Yedekleme çağırır ve bu BMR yedekleme için çoğaltma birimini paylaşır. Bu durumda, en fazla boş alana sahip olan sürücüyü kullanmak Windows Server Yedekleme gerektirmez. Bunun yerine, iş için oluşturulan paylaşımdan yararlanır.

Yedekleme tamamlandığında, dosya yedekleme sunucusu bilgisayarına aktarılır. Günlükler *C:\Windows\Logs\WindowsServerBackup*' da depolanır.

## <a name="prerequisites-and-limitations"></a>Önkoşullar ve sınırlamalar

* BMR, Windows Server 2003 çalıştıran bilgisayarlar veya bir istemci işletim sistemi çalıştıran bilgisayarlar için desteklenmez.

* Farklı koruma gruplarındaki aynı bilgisayar için BMR'yi ve sistem durumunu koruyamazsınız.

* Bir yedekleme sunucusu bilgisayarı BMR için kendisini koruyamaz.

* BMR için banda (diskten banda veya D2T) kısa süreli koruma desteklenmez. Banda uzun vadeli depolama (diske diskten banda veya D2D2T) desteklenir.

* BMR koruması için, Windows Server Yedekleme korunan bilgisayarda yüklü olmalıdır.

* BMR koruması için, sistem durumu korumasından farklı olarak, yedekleme sunucusunda korunan bilgisayarda bir alan gereksinimi yoktur. Windows Server Yedekleme yedeklemeleri yedekleme sunucusu bilgisayarına doğrudan aktarır. Yedekleme aktarma işi yedekleme sunucusu **işleri** görünümünde görünmüyor.

* Backup Server, BMR için çoğaltma biriminde 30 GB alan ayırır. Bu alan servis birimini, koruma grubunu değiştirme Sihirbazı 'ndaki **disk ayırma** sayfasında değiştirebilirsiniz. Ya da Get-DatasourceDiskAllocation ve Set-DatasourceDiskAllocation PowerShell cmdlet 'lerini kullanabilirsiniz. Kurtarma noktası biriminde BMR koruması, beş gün bekletme için yaklaşık 6 GB gerektirir.
  * Çoğaltma birimi boyutunu 15 GB 'tan az azaltamaz.
  * Yedekleme sunucusu BMR veri kaynağının boyutunu hesaplamaz. Tüm sunucular için 30 GB olduğunu varsayar. Ortamınızda beklediğinizi BMR yedeklemelerinin boyutuna göre değeri değiştirin. Bir BMR yedeklemesinin boyutunu kabaca hesaplayabilirsiniz, tüm kritik birimlerde kullanılan alanın toplamı olarak hesaplayabilirsiniz. Kritik birimler = Önyükleme birimi + sistem birimi + Active Directory gibi sistem durumu verilerini barındıran birim.

* Sistem durumu korumasından BMR korumasına geçiş yaparsanız, BMR koruması, *Kurtarma noktası biriminde* daha az alan gerektirir. Ancak, birimdeki ek alan geri kazanılır. Koruma grubunu değiştirme Sihirbazı 'nın **disk ayırmayı Değiştir** sayfasında birim boyutunu el ile küçültebilirsiniz. Ya da Get-DatasourceDiskAllocation ve Set-DatasourceDiskAllocation PowerShell cmdlet 'lerini kullanabilirsiniz.

    Sistem durumu korumasından BMR korumasına geçiş yaparsanız BMR koruması, *Çoğaltma biriminde* daha fazla alan gerektirir. Birim otomatik olarak genişletilir. Varsayılan alan ayırmalarını değiştirmek istiyorsanız, Modify-DiskAllocation PowerShell cmdlet 'ini kullanın.

* BMR korumasından sistem durumu korumasına geçiş yaparsanız, kurtarma noktası biriminde daha fazla alana ihtiyacınız vardır. Yedekleme sunucusu birimi otomatik olarak artırmayı deneyebilir. Depolama havuzunda yeterli alan yoksa bir hata oluşur.

    BMR korumasından sistem durumu korumasına geçiş yaparsanız, korunan bilgisayarda alan gerekir. Sistem durumu koruması, çoğaltmayı öncelikle yerel bilgisayara yazdığından ve sonra çoğaltmayı yedekleme sunucusu bilgisayarına aktardığından alanın olması gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

1. **Azure Backup sunucusu dağıtın**. Yedekleme sunucusunun doğru şekilde dağıtıldığını doğrulayın. Daha fazla bilgi için bkz.
    * [Azure Backup Sunucusu için sistem gereksinimleri](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Yedekleme sunucusu koruma matrisi](backup-mabs-protection-matrix.md)

1. **Depolamayı ayarlayın**. Yedekleme verilerini diskte, bantta ve Azure ile bulutta saklayabilirsiniz. Daha fazla bilgi için bkz. [veri depolamayı hazırlama](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Koruma aracısını ayarlayın**. Koruma aracısını yedeklemek istediğiniz bilgisayara kurun. Daha fazla bilgi için bkz. [DPM koruma aracısını dağıtma](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Sistem durumu yedeklemesi ve tam yedekleme

Sistem durumu ve tam yedekleme için:

1. Yeni Koruma Grubu Sihirbazı Oluştur ' u açmak için yedekleme sunucusu Yönetici Konsolu **, koruma**  >  **Eylemler**  >  **koruma grubu oluştur**' u seçin.

1. **Koruma grubu türünü seçin** sayfasında **sunucular**' ı seçin ve ardından **İleri**' yi seçin.

1. **Grup üyelerini seçin** sayfasında, bilgisayarı genişletin ve sonra **BMR** veya **sistem durumu**' nu seçin.

    Farklı gruplardaki aynı bilgisayar için BMR ve sistem durumunu koruyamayacağınızı unutmayın. Ayrıca, BMR 'yi seçtiğinizde sistem durumu otomatik olarak etkinleştirilir. Daha fazla bilgi için bkz. [koruma gruplarını dağıtma](/system-center/dpm/create-dpm-protection-groups).

1. **Veri koruma yöntemini seçin** sayfasında, kısa vadeli yedeklemenin ve uzun vadeli yedeklemenin nasıl işleneceğini seçin.

    Kısa süreli yedekleme, Azure Backup (kısa dönem veya uzun dönem) kullanarak diskten Azure 'a yedekleme seçeneğiyle her zaman ilk olarak diske yapılır. Buluta uzun süreli yedeklemeye alternatif olarak, bir tek başına bant cihazına veya yedekleme sunucusuna bağlı bant kitaplığına uzun süreli yedekleme ayarlanalım.

1. **Short-Term hedefleri seçin** sayfasında, diskte kısa süreli depolamaya nasıl yedekleme yapılacağını seçin:
    * **Bekletme aralığı** için, verilerin diskte ne kadar süreyle saklanacağını seçin.
    * **Eşitleme sıklığı** için diskte artımlı yedeklemenin ne sıklıkta çalıştırılacağını seçin. Bir yedekleme aralığı ayarlamak istemiyorsanız, **bir kurtarma noktasının hemen öncesinde** seçeneğini belirleyebilirsiniz. Yedekleme sunucusu, her kurtarma noktası zamanlanmadan hemen önce hızlı tam yedekleme çalıştırır.

1. Verileri uzun vadeli depolama için banda depolamak istiyorsanız, **Long-Term hedeflerini belirtin** sayfasında, bant verilerinin ne kadar süreyle saklanacağını seçin (1-99 yıl).
    1. **Yedekleme sıklığı** için, bantta yedeklemenin ne sıklıkta çalıştırılacağını seçin. Sıklık, seçtiğiniz bekletme aralığına göre belirlenir:
        * Bekletme aralığı 1-99 yıl olduğunda, günlük, haftalık, iki haftada bir, aylık, üç aylık, yarı yıllık veya yıllık yedekleme yapabilirsiniz.
        * Bekletme aralığı 1 ila 11 ay olduğunda, günlük, haftalık, iki haftada bir veya aylık yedekleme yapabilirsiniz.
        * Bekletme aralığı 1 ile 4 hafta arasında olduğunda, günlük veya haftalık yedekleme yapabilirsiniz.

    1. **Bant ve kitaplık ayrıntılarını seçin** sayfasında kullanılacak bant ve kitaplığı seçin. Ayrıca verilerin sıkıştırılıp şifrelenmeyeceğini seçin.

1. **Disk ayırmayı İncele** sayfasında, koruma grubu için kullanılabilir depolama havuzu disk alanını gözden geçirin.

    * **Toplam veri boyutu** , yedeklemek istediğiniz verilerin boyutudur.
    * **Azure Backup sunucusu sağlanacak disk alanı** , yedekleme sunucusunun koruma grubu için önerdiği alandır. Yedekleme sunucusu ideal yedekleme birimini seçmek için bu ayarları kullanır. **Disk ayırma ayrıntılarında** yedekleme birimi seçimlerini düzenleyebilirsiniz.
    * İş yükleri için açılan menüden tercih edilen depolamayı seçin. Düzenlemeleriniz, **Kullanılabilir Disk Depolaması** bölmesindeki **Toplam Depolama** ve **Boş Depolama** değerlerini değiştirir. Yetersiz sağlanan alan, yedekleme sunucusunun kesintisiz yedeklemeler sağlamak için birime eklemenizi önereceği depolama miktarıdır.

1. **Çoğaltma oluşturma yöntemini seçin** sayfasında, ilk tam veri çoğaltmasını nasıl işleyeceğinizi seçin.

    Ağ üzerinden çoğaltmayı seçerseniz, yoğun olmayan bir zaman seçmeniz önerilir. Büyük miktarlarda veri veya en iyi durumda olmayan ağ koşulları için, çıkarılabilir medya kullanarak verileri çevrimdışı olarak çoğaltmayı göz önünde bulundurun.

1. **Tutarlılık denetimi seçeneklerini belirleyin** sayfasında, tutarlılık denetimlerinin nasıl otomatikleştirildiğini seçin.

    Yalnızca çoğaltma verileri tutarsız hale geldiğinde veya bir zamanlamaya göre bir denetim çalıştırmayı seçebilirsiniz. Otomatik tutarlılık denetimini yapılandırmak istemiyorsanız, istediğiniz zaman el ile denetim gerçekleştirebilirsiniz. El ile denetim çalıştırmak için, yedekleme sunucusu Yönetici Konsolu **koruma** alanında, koruma grubuna sağ tıklayın ve ardından **tutarlılık denetimi gerçekleştir**' i seçin.

1. Azure Backup kullanarak buluta yedeklemeyi seçerseniz, **çevrimiçi koruma verilerini belirtin** sayfasında, Azure 'a yedeklemek istediğiniz iş yüklerini seçin.

1. **Çevrimiçi yedekleme zamanlamasını belirtin** sayfasında, Azure 'a artımlı olarak ne sıklıkta yedekleme yapılacağını seçin.

    Yedeklemeleri her gün, hafta, ay ve yılda çalışacak şekilde zamanlayabilirsiniz. Ayrıca, yedeklemelerin çalıştırılacağı saati ve tarihi de seçebilirsiniz. Yedeklemeler günde iki kez gerçekleşebilir. Her yedekleme işlemi çalıştırıldığında, yedekleme sunucusu diskinde depolanan yedekleme verilerinin kopyasından Azure 'da bir veri kurtarma noktası oluşturulur.

1. **Çevrimiçi saklama Ilkesini belirtin** sayfasında, günlük, haftalık, aylık ve yıllık yedeklerden oluşturulan kurtarma noktalarının Azure 'da nasıl tutuluyorsa seçin.

1. **Çevrimiçi çoğaltma Seç** sayfasında, verilerin ilk tam çoğaltmasının nasıl gerçekleşeceğini seçin.

    Ağ üzerinden çoğaltma yapabilir veya çevrimdışı (çevrimdışı dengeli dağıtım) yedekleyebilirsiniz. Çevrimdışı yedekleme, Azure Içeri aktarma özelliğini kullanır. Daha fazla bilgi için bkz. [Azure Backup çevrimdışı yedekleme iş akışı](offline-backup-azure-data-box.md).

1. **Özet** sayfasında, ayarlarınızı gözden geçirin. **Grup Oluştur**' u seçtikten sonra, verilerin ilk çoğaltması oluşur. Veri çoğaltma tamamlandığında, **durum** sayfasında, koruma grubu durumu **Tamam**' dır. Yedeklemeler daha sonra koruma grubu ayarlarına göre gerçekleşir.

## <a name="recover-system-state-or-bmr"></a>Sistem durumu veya BMR kurtarma

BMR'yi veya sistem durumu yedeğini ağ konumuna kurtarın. BMR 'yi yedeklediniz, sisteminizi başlatmak ve ağa bağlamak için Windows kurtarma ortamı 'nı (WinRE) kullanın. Ardından ağ konumundan kurtarmak için Windows Sunucu Yedeklemesini kullanın. Sistem durumunu yedeklediniz, ağ konumundan kurtarmak için Windows Server Yedekleme kullanmanız yeterlidir.

### <a name="restore-bmr"></a>BMR'yi geri yükleme

Kurtarma 'yi yedekleme sunucusu bilgisayarında çalıştırmak için:

1. **Kurtarma** bölmesinde, kurtarmak istediğiniz bilgisayarı bulun. Ardından **tam kurtarma**' yı seçin.

1. Kullanılabilir kurtarma noktaları, takvimde kalın olarak gösterilir. Kullanmak istediğiniz kurtarma noktasının tarihini ve saatini seçin.

1. **Kurtarma türünü seçin** sayfasında **bir ağ klasörüne kopyala**' yı seçin.

1. **Hedef belirtin** sayfasında, kopyalanmış verilerin hedefini seçin.

    Hedefin veriler için yeterli yere sahip olması gerektiğini unutmayın. Hedef için yeni bir klasör oluşturmanız önerilir.

1. **Kurtarma seçeneklerini belirtin** sayfasında güvenlik ayarları ' nı seçin. Daha hızlı kurtarma için depolama alanı ağı (SAN) tabanlı donanım anlık görüntülerini kullanıp kullanmayacağınızı seçin. Bu seçenek yalnızca şu durumlarda kullanılabilir:
    * Bu işlevselliği sağlayan bir SAN 'a sahipsiniz.
    * Bir kopyayı yazılabilir yapmak için oluşturabilir ve bölebilirsiniz.
    * Korunan bilgisayar ve yedek sunucu bilgisayarı aynı ağa bağlanır.

1. Bildirim seçeneklerini ayarlayın.
1. **Onay** sayfasında, **kurtar**' ı seçin.

Paylaşma konumunu ayarlamak için:

1. Geri yükleme konumunda, yedeği olan klasöre gidin.

1. Paylaşılan klasörün kökünün *WindowsImageBackup* klasörü olabilmesi Için *WindowsImageBackup* üzerindeki bir düzey klasörü paylaşabilirsiniz.

    Bu klasörü paylaşmıyorsanız, geri yükleme yedeği bulamaz. WinRE kullanarak bağlanmak için, WinRE 'de doğru IP adresi ve kimlik bilgileriyle erişebileceğiniz bir paylaşıma ihtiyacınız vardır.

Sistemi geri yüklemek için:

1. Geri yüklemekte olduğunuz sistem için Windows DVD 'sini kullanarak resmi geri yüklemek istediğiniz bilgisayarı başlatın.

1. İlk sayfada, dil ve yerel ayar ayarlarını doğrulayın. **Install** sayfasında, **Bilgisayarınızı onarın**' ı seçin.

1. **Sistem kurtarma seçenekleri** sayfasında, **daha önce oluşturduğunuz bir sistem görüntüsünü kullanarak bilgisayarınızı geri yükle**' yi seçin.

1. **Bir sistem görüntüsü yedeklemesi seçin** sayfasında, **sistem resmi seçin**  >  **Gelişmiş**  >  **ağda sistem görüntüsü ara**' yı seçin. Bir uyarı görünürse **Evet**' i seçin. Paylaşma yoluna gidin, kimlik bilgilerini girin ve kurtarma noktasını seçin. Sistem, bu kurtarma noktasında kullanılabilir olan belirli yedekleri tarar. Kullanmak istediğiniz kurtarma noktasını seçin.

1. **Yedeklemenin nasıl geri yükleneceğini seçin** sayfasında, **Diskleri biçimlendir ve yeniden bölümle**' yı seçin. Sonraki sayfada, ayarları doğrulayın.

1. Geri yüklemeyi başlatmak için **son**' u seçin. Yeniden başlatma gerekiyor.

### <a name="restore-system-state"></a>Sistem durumunu geri yükleme

Yedekleme sunucusunda kurtarmayı çalıştırmak için:

1. **Kurtarma** bölmesinde, kurtarmak istediğiniz bilgisayarı bulun ve **tam kurtarma**' yı seçin.

1. Kullanılabilir kurtarma noktaları, takvimde kalın olarak gösterilir. Kullanmak istediğiniz kurtarma noktasının tarihini ve saatini seçin.

1. **Kurtarma türünü seçin** sayfasında **bir ağ klasörüne kopyala**' yı seçin.

1. **Hedef belirtin** sayfasında, verilerin kopyalanacağı yeri seçin.

    Seçtiğiniz hedefin veriler için yeterli yere sahip olması gerektiğini unutmayın. Hedef için yeni bir klasör oluşturmanız önerilir.

1. **Kurtarma seçeneklerini belirtin** sayfasında güvenlik ayarları ' nı seçin. Daha hızlı kurtarma için SAN tabanlı donanım anlık görüntülerini kullanıp kullanmayacağınızı seçin. Bu seçenek yalnızca şu durumlarda kullanılabilir:
    * Bu işlevselliği sağlayan bir SAN 'a sahipsiniz.
    * Bir kopyayı yazılabilir yapmak için oluşturabilir ve bölebilirsiniz.
    * Korunan bilgisayar ve yedek sunucu sunucusu aynı ağa bağlanır.

1. Bildirim seçeneklerini ayarlayın.
1. **Onay** sayfasında, **kurtar**' ı seçin.

Windows Server Yedekleme çalıştırmak için:

1. **Eylemleri**  >    >  **Bu sunucuyu** kurtar  >  **İleri ' yi** seçin.

1. **Başka bir sunucu** seçin, **konum türünü belirtin** sayfasını seçin ve ardından **uzak paylaşılan klasör**' i seçin. Kurtarma noktasını içeren klasörün yolunu girin.

1. **Kurtarma türünü seçin** sayfasında **sistem durumu**' nu seçin.

1. **Sistem durumu kurtarma Için konum seçin** sayfasında, **orijinal konum**' u seçin.

1. **Onay** sayfasında, **kurtar**' ı seçin.

1. Geri yüklemeden sonra sunucuyu yeniden başlatın.

Ayrıca sistem durumu geri yüklemesini bir komut isteminde çalıştırabilirsiniz:

1. Kurtarmak istediğiniz bilgisayarda Windows Server Yedekleme başlatın.

1. Sürüm tanımlayıcısını almak için, bir komut isteminde şunu girin:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Sistem durumu geri yüklemeyi başlatmak için sürüm tanımlayıcıyı kullanın. Komut isteminde şunu girin:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Kurtarma işlemini başlatmak istediğinizi onaylayın. İşlemi komut Istemi penceresinde görebilirsiniz. Geri yükleme günlüğü oluşturulur.

1. Geri yüklemeden sonra sunucuyu yeniden başlatın.
