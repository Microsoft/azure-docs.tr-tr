---
title: Hyper-V için Azure geçişi gereci ayarlama
description: Hyper-V ' d e sunucuları değerlendirmek ve geçirmek için bir Azure geçiş gereci ayarlamayı öğrenin.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 8f0e7ec2566928897d2b84357b599506520e8d95
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612033"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Hyper-V üzerinde sunucular için bir gereç ayarlama

[Azure geçişi: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) aracı ile Hyper-V ' d e sunucu bulma ve değerlendirme Için Azure geçişi gereci ayarlamak için bu makaleyi izleyin.

Azure [geçişi](migrate-appliance.md)  gereci, Hyper-V ' d e şirket içi sunucuları bulmak ve Azure 'a sunucu meta verileri/performans verileri göndermek Için Azure geçişi: bulma ve değerlendirme/geçiş tarafından kullanılan hafif bir gereç.

Gereci birkaç yöntem kullanarak dağıtabilirsiniz:

- İndirilen bir VHD 'yi kullanarak Hyper-V üzerinde bir sunucuda ayarlama yapın. Bu yöntem bu makalede açıklanmıştır.
- Bir PowerShell yükleyici betiği ile Hyper-V veya fiziksel sunucu üzerinde bir sunucuda ayarlama yapın. [Bu yöntem](deploy-appliance-script.md) , bir VHD kullanarak bir sunucu ayarlayamıyorum veya Azure Kamu kullanıyorsanız kullanılmalıdır.

Gereci oluşturduktan sonra Azure geçişi 'ne bağlanıp bağlanamadıktan sonra, bulma ve değerlendirme, ilk kez yapılandırma ve projeye kaydetme işlemini kontrol edebilirsiniz.

## <a name="appliance-deployment-vhd"></a>Gereç dağıtımı (VHD)

Bir VHD şablonu kullanarak gereci ayarlamak için:

- Portalda bir gereç adı sağlayın ve bir proje anahtarı oluşturun.
- Azure portal sıkıştırılmış bir Hyper-V VHD 'sini indirin.
- Gereci oluşturun ve Azure geçişi: bulma ve değerlendirme 'a bağlanıp bağlanamadığından emin olun.
- Gereci ilk kez yapılandırın ve proje anahtarını kullanarak projeyi projeye kaydedin.

### <a name="generate-the-project-key"></a>Proje anahtarını oluştur

1. **Geçiş hedefleri**  >  **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** bölümünde **bul**' u seçin.
2. Sunucuları **bul**' da  >  **sunucularınız sanallaştırılır mi?**, **Hyper-V ile Evet '** i seçin.
3. **1: proje anahtarı oluştur**' da, Hyper-V ' d i sunucu keşfi Için ayarladığınız Azure geçiş gereci için bir ad sağlayın. Ad 14 karakter veya daha kısa bir harf olmalıdır.
1. Gerekli Azure kaynaklarını oluşturmaya başlamak için **anahtar oluştur** ' a tıklayın. Kaynak oluşturma sırasında sunucuları keşfet sayfasını kapatmayın.
1. Azure kaynakları başarıyla oluşturulduktan sonra bir **Proje anahtarı** oluşturulur.
1. Yapılandırma sırasında gereç kaydını tamamlamamak için gerekli olacak şekilde anahtarı kopyalayın.

### <a name="download-the-vhd"></a>VHD 'YI indirin

**2: Azure geçişi yükleme gereci indirin**, öğesini seçin. VHD dosyası ve **İndir**' e tıklayın.

   ![Bulma sunucuları için seçimler](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Anahtar oluştur seçimleri](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)

### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. VHD için karma oluşturmak üzere aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

Yukarıdaki komutun sonucunu [burada](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#verify-security) belgelenen değerle karşılaştırarak en son karma değeri doğrulayın

## <a name="create-the-appliance"></a>Gereç oluşturma

İndirilen dosyayı içeri aktarın ve bir gereç oluşturun.

1. Sıkıştırılmış VHD dosyasını, gereci barındıracak Hyper-V konağındaki bir klasöre ayıklayın. Üç klasör ayıklanır.
2. Hyper-V Yöneticisi'ni açın. **Eylemler**' de **sanal makineyi içeri aktar**' a tıklayın.

    ![VHD dağıtma](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Sanal makineyi Içeri aktarma sihirbazında > **başlamadan önce** **İleri**' ye tıklayın.
3. **Klasörü bul**' da ayıklanan VHD 'yi içeren klasörü belirtin. Ardından **İleri**'ye tıklayın.
1. **Sanal makine seç** bölümünde **İleri**' ye tıklayın.
2. **Içeri aktarma türünü seçin** bölümünde **sanal makineyi Kopyala (yenı bir benzersiz kimlik oluştur)** seçeneğine tıklayın. Ardından **İleri**'ye tıklayın.
3. **Hedef Seç** bölümünde varsayılan ayarı bırakın. **İleri**’ye tıklayın.
4. **Depolama klasörlerinde** varsayılan ayarı bırakın. **İleri**’ye tıklayın.
5. **Ağ seçin** bölümünde, sunucunun kullanacağı sanal anahtarı belirtin. Anahtar, verileri Azure 'a göndermek için internet bağlantısı gerektirir.
6. **Özet** bölümünde ayarları gözden geçirin. Ardından, **Son**'a tıklayın.
7. Hyper-V Yöneticisi 'nde **sanal makineler**>, VM 'yi başlatın.

### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gerecin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutları için Azure URL 'lerine bağlanabildiğinizden emin olun.

### <a name="configure-the-appliance"></a>Gereci yapılandırma

Gereci ilk kez ayarlayın.

> [!NOTE]
> Gereci indirilen VHD yerine bir [PowerShell betiği](deploy-appliance-script.md) kullanarak ayarlarsanız, bu yordamın ilk iki adımı ilgili değildir.

1. Hyper-V Manager > **sanal makineler**' de **, > sunucuya** sağ tıklayın.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. Herhangi bir sistemde gereç ile bağlantı kurmak için bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı gereç masaüstünden açabilirsiniz.
1. **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
1. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Bağlantı**: uygulama, sunucunun internet erişimi olup olmadığını denetler. Sunucu bir proxy kullanıyorsa:
      - **Ara sunucu** ' ya tıklayın ve proxy adresini (form http://ProxyIPAddress veya http://ProxyFQDN) dinleme bağlantı noktası) belirtin.
      - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
      - Yalnızca HTTP proxy’si desteklenir.
      - Proxy ayrıntıları eklediyseniz veya proxy ve/veya kimlik doğrulamasını devre dışı bırakırsanız, bağlantıyı tetiklemek için **Kaydet** 'e tıklayarak bağlantı denetimini yeniden başlatın.
    - **Zaman eşitleme**: Saat doğrulandı. Sunucu bulmanın düzgün çalışması için gereç saatinin internet ile eşitlenmiş olması gerekir.
    - **Güncelleştirmeleri yükleme**: Azure geçişi: bulma ve değerlendirme, gerecin en son güncelleştirmelerin yüklü olduğunu denetler. Denetim tamamlandıktan sonra gereç **hizmetlerini görüntüle** ' ye tıklayarak gereç üzerinde çalışan bileşenlerin durumunu ve sürümlerini görebilirsiniz.

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. Portaldan kopyalanmış **Proje anahtarını** yapıştırın. Anahtarınız yoksa **Azure geçişi: bulma ve değerlendirme> var olan gereçlerini bulmak> yönetmek** için, anahtar oluşturma sırasında verdiğiniz gereç adını seçin ve karşılık gelen anahtarı kopyalayın.
1. Azure ile kimlik doğrulaması yapmak için bir cihaz koduna ihtiyacınız olacaktır. **Oturum** açmak için tıkladığınızda, aşağıda gösterildiği gibi cihaz koduyla kalıcı olarak açılır.

    ![Cihaz kodunu kalıcı olarak gösterme](./media/tutorial-discover-vmware/device-code.png)

1. Cihaz kodunu kopyalamak ve yeni bir tarayıcı sekmesinde bir Azure oturum açma istemi açmak için **kodu kopyala & oturum** aç ' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
1. Yeni sekmede, Azure Kullanıcı adınızı ve parolanızı kullanarak cihaz kodunu yapıştırın ve oturum açın.
   
   PIN ile oturum açma desteklenmez.
3. Oturum açmadan oturum açma sekmesini yanlışlıkla kapatırsanız, oturum açma düğmesini yeniden etkinleştirmek için gereç Yapılandırma Yöneticisi 'nin tarayıcı sekmesini yenilemeniz gerekir.
1. Başarıyla oturum açtıktan sonra, Gereç Yapılandırma Yöneticisi ile önceki sekmeye geri dönün.
4. Günlüğe kaydetme için kullanılan Azure Kullanıcı hesabının, anahtar üretimi sırasında oluşturulan Azure kaynakları üzerinde doğru [izinleri](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) varsa, Gereç kaydı başlatılır.
1. Gereç başarıyla kaydedildikten sonra, **Ayrıntıları görüntüle**' ye tıklayarak kayıt ayrıntılarına bakabilirsiniz.

### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD 'leri için temsilci kimlik bilgileri

SMB 'lerde VHD 'ler çalıştırıyorsanız, Gereç üzerinden Hyper-V konaklarına kimlik bilgileri temsilcisini etkinleştirmeniz gerekir. Bunu gereç içinden yapmak için:

1. Gereç üzerinde bu komutu çalıştırın. HyperVHost1/HyperVHost2, örnek ana bilgisayar adlarıdır.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Alternatif olarak, bunu gereç üzerindeki Yerel Grup İlkesi Düzenleyicisi yapın:
    - **Yerel bilgisayar ilkesi**  >  **bilgisayar yapılandırması**' nda, **Yönetim Şablonları**  >  **sistem**  >  **kimlik bilgileri temsili**' ne tıklayın.
    - **Yeni kimlik bilgileri aktarımına Izin ver**' e çift tıklayın ve **etkin**' i seçin.
    - **Seçenekler**' de **göster**' e tıklayın ve bulmayı Istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.
    - **Kimlik bilgileri temsilcisi** içinde, **yalnızca NTLM sunucu kimlik doğrulamasıyla yeni kimlik bilgileri aktarmaya izin ver**' e çift tıklayın. Daha sonra, bulmayı istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Gereç üzerinden Hyper-V konaklarına veya kümelerine bağlanın ve bulmayı başlatın.

1. **1. Adım: Hyper-V konağı kimlik bilgilerini sağlayın** bölümünde kimlik bilgileri için kolay bir ad belirtmek üzere **kimlik bilgileri ekle** ' ye tıklayın, gerecin sunucuları bulmaya yönelik kullanacağı bir Hyper-v Konağı/kümesi Için **Kullanıcı adı** ve **parola** ekleyin. **Kaydet**'e tıklayın.
1. Aynı anda birden çok kimlik bilgisi eklemek istiyorsanız, kaydetmek için **daha fazla Ekle** ' ye tıklayın ve daha fazla kimlik bilgisi ekleyin. Hyper-V ' d i sunucu keşfi için birden çok kimlik bilgisi desteklenir.
1. **2. Adım: Hyper-v Konağı/kümesi ayrıntılarını sağlama** bölümünde, Hyper-v Konağı/kümesi **IP adresini/FQDN** 'yi ve konağa/kümeye bağlanacak kimlik bilgileri için kolay adı belirtmek üzere **bulma kaynağı Ekle** ' ye tıklayın.
1. **Tek seferde tek bir öğe ekleyebilir** veya tek bir go içinde **birden fazla öğe ekleyebilirsiniz** . Ayrıca Hyper-V konağı/kümesi ayrıntılarını **Içeri aktarma CSV** aracılığıyla sağlama seçeneği de vardır.

    ![Keşif kaynağı ekleme seçimleri](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - **Tek öğe Ekle**' yi seçerseniz, kimlik bilgileri ve Hyper-V konağı/kümesi **IP adresi/FQDN** için kolay ad belirtmeniz ve **Kaydet**' e tıklamanız gerekir.
    - **Birden çok öğe Ekle** _(varsayılan olarak seçilidir)_ seçeneğini belirlerseniz, metin kutusunda kimlik bilgileri Için kolay ad ile Hyper-V konağı/KÜMESI **IP adresi/FQDN** belirterek birden çok kayıt ekleyebilirsiniz. * * Eklenen kayıtlar ' ı doğrulayın ve **Kaydet**' e tıklayın.
    - **CSV 'Yi Içeri aktar**' ı SEÇERSENIZ bir CSV şablon dosyası indirebilir, dosyayı Hyper-V konağı/kümesi **IP adresi/FQDN** ve kimlik bilgileri için kolay ad ile doldurabilirsiniz. Sonra dosyayı gereç içine aktarır, dosyadaki kayıtları **doğrulayın** ve **Kaydet**' e tıklayın.

1. Kaydet 'e tıkladığınızda gereç, eklenen Hyper-V konaklarına/kümelerine bağlantıyı doğrulamaya çalışır ve her konak/küme için tablodaki **doğrulama durumunu** gösterir.
    - Başarıyla doğrulanan konaklar/kümeler için IP adreslerine/FQDN 'ye tıklayarak daha fazla ayrıntı görüntüleyebilirsiniz.
    - Bir konakta doğrulama başarısız olursa, tablonun durum sütununda **doğrulama** ' ya tıklayarak hatayı gözden geçirin. Sorunu düzeltin ve tekrar doğrulayın.
    - Konakları veya kümeleri kaldırmak için **Sil**'e tıklayın.
    - Belirli bir konağı kümeden kaldıramazsınız. Tüm kümeyi yalnızca çıkarabilirsiniz.
    - Kümedeki belirli konaklarla ilgili sorunlar olsa bile bir küme ekleyebilirsiniz.
1. Keşfi başlatmadan önce her zaman konaklara/kümelere bağlantıyı **yeniden doğrulayabilirsiniz** .
1. Başarıyla doğrulanan konaklardan/kümelerden sunucu bulmayı **başlatmak için bulmayı Başlat**' a tıklayın. Bulma işlemi başarılı bir şekilde başlatıldıktan sonra, tablodaki her bir konağa/kümeye yönelik bulma durumunu denetleyebilirsiniz.

Bu, bulmayı başlatır. Bulunan sunucuların meta verilerinin Azure portal görünmesi için ana bilgisayar başına yaklaşık 2 dakika sürer.

## <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulma işlemi tamamlandıktan sonra, sunucuların portalda göründüğünü doğrulayabilirsiniz.

1. Azure Geçişi panosunu açın.
2. **Azure geçişi-Windows, Linux ve SQL sunucuları**  >  **Azure geçişi: bulma ve değerlendirme** sayfasında, **bulunan sunucuların** sayısını görüntüleyen simgeye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi: bulma ve değerlendirme ile [Hyper-V değerlendirmesi](tutorial-assess-hyper-v.md) yapmayı deneyin.