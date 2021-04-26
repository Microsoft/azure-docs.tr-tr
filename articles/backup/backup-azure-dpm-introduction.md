---
title: DPM sunucusunu iş yüklerini yedekleyecek şekilde hazırlama
description: Bu makalede, Azure Backup hizmetini kullanarak System Center Data Protection Manager (DPM) yedeklemelerini Azure 'a nasıl hazırlayacağınızı öğrenin.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 823b23d99959df5f2eed20cf4136254e1702fe89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98985640"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>System Center DPM ile iş yüklerini Azure 'a yedeklemeye hazırlanma

Bu makalede, Azure Backup hizmeti kullanılarak System Center Data Protection Manager (DPM) yedeklemelerini Azure 'a nasıl hazırlayacağınız açıklanır.

Makale şunları sağlar:

- Azure Backup ile DPM dağıtmaya genel bakış.
- DPM ile Azure Backup kullanmaya yönelik önkoşullar ve sınırlamalar.
- Kurtarma Hizmetleri yedek Kasası ayarlama ve isteğe bağlı olarak kasa için Azure depolama türünü değiştirme dahil olmak üzere Azure 'u hazırlama adımları.
- Kasa kimlik bilgilerini indirme, Azure Backup aracısını yükleme ve DPM sunucusunu kasaya kaydetme dahil olmak üzere DPM sunucusunu hazırlama adımları.
- Sık karşılaşılan hatalar için sorun giderme ipuçları.

## <a name="why-back-up-dpm-to-azure"></a>DPM neden Azure 'a yedeklenir?

[System Center DPM](/system-center/dpm/dpm-overview) , dosya ve uygulama verilerini yedekler. DPM, Azure Backup ile Şu şekilde etkileşime girer:

- **Fiziksel sunucuda veya şirket IÇI VM 'de çalışan DPM** ; disk ve bant yedeklemesinin yanı sıra verileri Azure 'Daki bir yedekleme kasasına yedekleyebilirsiniz.
- **Azure VM üzerinde çalışan DPM** -güncelleştirme 3 veya sonraki sürümleri Ile System Center 2012 R2 'den DPM 'Yi BIR Azure sanal makinesinde dağıtabilirsiniz. SANAL makineye bağlı Azure disklerine veri yedekleyebilir veya verileri bir yedekleme kasasına yedeklemek için Azure Backup kullanabilirsiniz.

DPM sunucularını Azure 'a yedeklemenin iş avantajları şunlardır:

- Şirket içi DPM için Azure Backup, banda uzun süreli dağıtıma alternatif sağlar.
- Azure VM üzerinde çalışan DPM için Azure Backup Azure diskinden depolamayı boşaltmasını sağlar. Eski verilerin bir yedekleme kasasında depolanması, yeni verileri diske depolayarak işinizi ölçeklendirmenize olanak tanır.

## <a name="prerequisites-and-limitations"></a>Önkoşullar ve sınırlamalar

**Ayar** | **Gereksinim**
--- | ---
Azure VM 'de DPM | DPM 2012 R2 güncelleştirme paketi 3 veya üzeri olan System Center 2012 R2.
Fiziksel sunucuda DPM | System Center 2012 SP1 veya üzeri; System Center 2012 R2.
Hyper-V VM üzerinde DPM | System Center 2012 SP1 veya üzeri; System Center 2012 R2.
VMware VM üzerinde DPM | Güncelleştirme paketi 5 veya sonraki sürümleri ile System Center 2012 R2.
Bileşenler | DPM sunucusunda Windows PowerShell ve .NET Framework 4,5 yüklü olmalıdır.
Desteklenen uygulamalar | DPM 'nin neleri yedekleyebileceklerini [öğrenin](/system-center/dpm/dpm-protection-matrix) .
Desteklenen dosya türleri | Bu dosya türleri Azure Backup yedeklenebilir:<br> <li>Şifrelenmiş (yalnızca tam yedeklemeler)<li> Sıkıştırılmış (artımlı yedeklemeler desteklenir) <li> Seyrek (artımlı yedeklemeler desteklenir)<li> Sıkıştırılmış ve seyrek (seyrek olarak kabul edilir)
Desteklenmeyen dosya türleri | <li>Büyük/küçük harfe duyarlı dosya sistemlerindeki sunucular<li> sabit bağlantılar (atlandı)<li> yeniden ayrıştırma noktaları (atlandı)<li> şifrelenmiş ve sıkıştırılmış (atlandı)<li> şifrelenmiş ve seyrek (atlandı)<li> Sıkıştırılmış akış<li> ayrıştırma akışı
Yerel depolama | Yedeklemek istediğiniz her makinenin, yedeklenen verilerin boyutunun en az %5 ' i kadar yerel boş depolama alanı olmalıdır. Örneğin, 100 GB veri yedeklenirken karalama konumunda en az 5 GB boş alan gerekir.
Kasa depolaması | Bir Azure Backup kasasına yedekleyebileceğiniz veri miktarına yönelik bir sınır yoktur, ancak bir veri kaynağının boyutu (örneğin bir sanal makine veya veritabanı) 54.400 GB 'ı aşmamalıdır.
Azure ExpressRoute | Azure ExpressRoute üzerinden verilerinizi, genel eşleme (eski devreler için kullanılabilir) ve Microsoft eşlemesi ile birlikte yedekleyebilirsiniz. Özel eşleme üzerinde yedekleme desteklenmez.<br/><br/> **Ortak eşleme ile**: aşağıdaki etki alanlarına/adreslere erişim sağlayın:<br/><br/> Adresleri<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>IP adresleri<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/>**Microsoft eşlemesi ile** aşağıdaki hizmetleri/bölgeleri ve ilgili topluluk değerlerini seçin:<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure bölgesi (Kurtarma Hizmetleri kasanızın konumuna göre)<br/><br/>-Azure depolama (Kurtarma Hizmetleri kasanızın konumuna göre)<br/><br/>Daha fazla bilgi için bkz. [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md).<br/><br/>**Note**: genel eşleme, yeni devreler için kullanım dışıdır.
Azure Backup aracısı | DPM, System Center 2012 SP1 üzerinde çalışıyorsa, DPM SP1 için paketi 2 veya sonraki bir sürümü çalıştırın. Bu, aracı yüklemesi için gereklidir.<br/><br/> Bu makalede, Microsoft Azure kurtarma hizmeti (MARS) Aracısı olarak da bilinen Azure Backup aracısının en son sürümünün nasıl dağıtılacağı açıklanır. Daha önceki bir sürümü dağıttıysanız yedeklemenin beklendiği gibi çalıştığından emin olmak için en son sürüme güncelleştirin.

Başlamadan önce Azure Backup özelliği etkinleştirilmiş bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. [Azure Backup fiyatlandırması](https://azure.microsoft.com/pricing/details/backup/)hakkında bilgi edinin.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Depolama ayarlarını değiştirme

Coğrafi olarak yedekli depolama ve yerel olarak yedekli depolama arasında seçim yapabilirsiniz.

- Varsayılan olarak, kasanız coğrafi olarak yedekli depolamaya sahiptir.
- Kasa birincil yedeklemenizin varsa, seçeneği coğrafi olarak yedekli depolama olarak ayarlayın. Gerçekten dayanıklı olmayan bir ucuz seçeneği istiyorsanız yerel olarak yedekli depolamayı yapılandırmak için aşağıdaki yordamı kullanın.
- [Azure depolama](../storage/common/storage-redundancy.md)ve [coğrafi](../storage/common/storage-redundancy.md#geo-redundant-storage)olarak yedekli, [yerel olarak yedekli](../storage/common/storage-redundancy.md#locally-redundant-storage) ve [bölge yedekli](../storage/common/storage-redundancy.md#zone-redundant-storage) depolama seçenekleri hakkında bilgi edinin.
- Depolama ayarlarını ilk yedeklemeden önce değiştirin. Zaten bir öğe yedeklediyseniz, depolama ayarlarını değiştirmeden önce kasada yedeklemeyi durdurun.

Depolama çoğaltma ayarını düzenlemek için:

1. Kasa panosunu açın.

2. **Yönet** bölümünde **Yedekleme altyapısı**' nı seçin.

3. **Yedekleme yapılandırması** menüsünde, kasa için bir depolama seçeneği belirleyin.

    ![Yedekleme kasalarının listesi](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Kasa kimlik bilgilerini indirme

Kasada DPM sunucusunu kaydettiğinizde kasa kimlik bilgilerini kullanırsınız.

- Kasa kimlik bilgileri dosyası, her bir yedekleme kasası için portal tarafından oluşturulan bir sertifikadır.
- Portal daha sonra ortak anahtarı Access Control Service'e (ACS) yükler.
- Makine kayıt iş akışı sırasında, sertifikanın özel anahtarı, makinenin kimliğini doğrulayan Kullanıcı tarafından kullanılabilir hale getirilir.
- Kimlik doğrulamasına göre Azure Backup hizmeti verileri tanımlanan kasaya gönderir.

### <a name="best-practices-for-vault-credentials"></a>Kasa kimlik bilgileri için en iyi yöntemler

Kimlik bilgilerini almak için kasa kimlik bilgileri dosyasını Azure portal güvenli bir kanaldan indirin:

- Kasa kimlik bilgileri yalnızca kayıt iş akışı sırasında kullanılır.
- Kasa kimlik bilgileri dosyasının güvenli ve güvenliği aşılmış olduğundan emin olmak sizin sorumluluğunuzdadır.
  - Kimlik bilgileri denetimi kaybolursa, diğer makineleri kasaya kaydetmek için kasa kimlik bilgileri kullanılabilir.
  - Ancak, yedekleme verileri size ait bir parola kullanılarak şifrelenir, bu nedenle mevcut yedekleme verilerinin güvenliği tehlikeye alınamaz.
- Dosyanın DPM sunucusundan erişilebilen bir konuma kaydedildiğinden emin olun. Dosya paylaşımında/SMB 'de depolanıyorsa, erişim izinleri olup olmadığını denetleyin.
- Kasa kimlik bilgilerinin süresi 48 sonra dolar. Yeni kasa kimlik bilgilerini gerektiği kadar birçok kez indirebilirsiniz. Ancak kayıt iş akışı sırasında yalnızca en son kasa kimlik bilgisi dosyası kullanılabilir.
- Azure Backup hizmeti sertifikanın özel anahtarını bilmez ve özel anahtar portalda veya hizmette kullanılamaz.

Kasa kimlik bilgileri dosyasını aşağıdaki gibi bir yerel makineye indirin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. DPM sunucusunu kaydetmek istediğiniz kasayı açın.
3. **Ayarlar**' da **Özellikler**' i seçin.

    ![Kasa menüsünü açma](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. **Özellikler**  >  **yedekleme kimlik bilgileri**' nde **İndir**' i seçin. Portal kasa adının ve geçerli tarihin bir birleşimini kullanarak kasa kimlik bilgileri dosyasını oluşturur ve bu dosyayı indirme için kullanılabilir hale getirir.

    ![Kimlik bilgilerini indir](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kasa kimlik bilgilerini klasöre indirmek için **Kaydet** ' i seçin veya **farklı kaydedin** ve bir konum belirtin. Dosyanın oluşturulması bir dakika kadar sürer.

## <a name="install-the-backup-agent"></a>Yedekleme aracısını yükler

Azure Backup tarafından yedeklenen her makinede Yedekleme aracısı (Ayrıca, Microsoft Azure kurtarma hizmeti (MARS) Aracısı olarak da bilinir) yüklü olmalıdır. Aracıyı DPM sunucusuna aşağıdaki gibi yükler:

1. DPM sunucusunu kaydetmek istediğiniz kasayı açın.
2. **Ayarlar**' da **Özellikler**' i seçin.

    ![Kasa ayarlarını aç](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. **Özellikler** sayfasında Azure Backup aracısını indirin.

    ![İndir](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. İndirdikten sonra MARSAgentInstaller.exe çalıştırın. Aracıyı DPM makinesine yüklemek için.
5. Aracı için bir yükleme klasörü ve önbellek klasörü seçin. Önbellek konumu boş alanı, yedekleme verilerinin en az %5 ' i olmalıdır.
6. İnternet 'e bağlanmak için bir proxy sunucu kullanıyorsanız, **proxy yapılandırması** ekranında ara sunucu ayrıntılarını girin. Kimliği doğrulanmış bir ara sunucu kullanıyorsanız, bu ekranda Kullanıcı adı ve parola ayrıntılarını girin.
7. Azure Backup Aracısı yüklemeyi tamamlaması için .NET Framework 4,5 ve Windows PowerShell 'i (yüklenmemişse) yüklüyor.
8. Aracı yüklendikten sonra pencereyi **kapatın** .

    ![Kapat](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>DPM sunucusunu kasaya kaydetme

1. DPM Yönetici Konsolu > **yönetimi**'nde **çevrimiçi**' i seçin. **Kaydet**’i seçin. Sunucu kaydetme Sihirbazı 'Nı açar.
2. **Ara sunucu yapılandırması**' nda, proxy ayarlarını gereken şekilde belirtin.

    ![Ara sunucu yapılandırması](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. **Yedekleme Kasası**' nda, indirdiğiniz kasa kimlik bilgileri dosyasına gidin ve seçin.

    ![Kasa kimlik bilgileri](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. **Daraltma ayarında**, yedeklemeler için isteğe bağlı olarak bant genişliği azaltmayı etkinleştirebilirsiniz. Çalışma saatlerini ve günleri belirtmek için hız sınırlarını ayarlayabilirsiniz.

    ![Daraltma ayarı](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. **Kurtarma klasörü ayarında**, veri kurtarma sırasında kullanılabilecek bir konum belirtin.

    - Azure Backup, kurtarılan veriler için geçici bir saklama alanı olarak bu konumu kullanır.
    - Veri kurtarmayı tamamladıktan sonra Azure Backup, bu alandaki verileri temizler.
    - Konumda, paralel olarak kurtarmayı düşündüğünüz öğeleri tutmak için yeterli alan olmalıdır.

    ![Kurtarma klasörü ayarı](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. **Şifreleme ayarında**, bir parola oluşturun veya sağlayın.

    - Parola, buluta yedeklemeleri şifrelemek için kullanılır.
    - En az 16 karakter belirtin.
    - Dosyayı güvenli bir konuma kaydedin, kurtarma için gereklidir.

    ![Şifreleme](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Şifreleme parolası size aittir ve Microsoft bunun üzerinde görünürlük sahibi değildir.
    > Parola kaybolur veya unuttuysanız, Microsoft, yedekleme verilerini kurtarmaya yardımcı olamaz.

7. DPM sunucusunu kasaya kaydetmek için **Kaydet** ' i seçin.

Sunucu kasada başarıyla kaydedildikten sonra, Microsoft Azure yedeklemeye başlamaya hazırsınız demektir. İş yüklerini Azure 'a yedeklemek için DPM konsolundaki koruma grubunu yapılandırmanız gerekir. Koruma gruplarını dağıtmayı [öğrenin](/system-center/dpm/create-dpm-protection-groups) .

## <a name="troubleshoot-vault-credentials"></a>Kasa kimlik bilgileri sorunlarını giderme

### <a name="expiration-error"></a>Süre sonu hatası

Kasa kimlik bilgileri dosyası yalnızca 48 saat için geçerlidir (portaldan indirildikten sonra). Bu ekranda herhangi bir hatayla karşılaşırsanız (örneğin, "belirtilen kasa kimlik bilgileri dosyası süresi dolmuşsa"), Azure portal oturum açın ve kasa kimlik bilgileri dosyasını yeniden indirin.

### <a name="access-error"></a>Erişim hatası

Kasa kimlik bilgileri dosyasının kurulum uygulaması tarafından erişilebilen bir konumda kullanılabildiğinden emin olun. Erişim ile ilgili hatalarla karşılaşırsanız, kasa kimlik bilgileri dosyasını bu makinede geçici bir konuma kopyalayın ve işlemi yeniden deneyin.

### <a name="invalid-credentials-error"></a>Geçersiz kimlik bilgileri hatası

Geçersiz bir kasa kimlik bilgisi hatasıyla karşılaşırsanız (örneğin, "geçersiz kasa kimlik bilgileri sağlanmış"), dosya bozuk veya kurtarma hizmetiyle ilişkili en son kimlik bilgilerine sahip değil.

- Portaldan yeni bir kasa kimlik bilgileri dosyası indirdikten sonra işlemi yeniden deneyin.
- Bu hata genellikle, Azure portal hızlı bir şekilde art arda iki kez **kasa kimlik bilgilerini indir** seçeneğini belirlediğinizde görülür. Bu durumda, yalnızca ikinci kasa kimlik bilgisi dosyası geçerlidir.
