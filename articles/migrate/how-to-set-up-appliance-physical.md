---
title: Azure geçişi sunucu değerlendirmesi ile fiziksel sunucu değerlendirmesi için bir gereç ayarlama
description: Azure geçişi sunucu değerlendirmesini kullanarak fiziksel sunucu değerlendirmesi için bir gereç ayarlamayı açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/11/2019
ms.author: raynew
ms.openlocfilehash: 1e67c36753239940b15e3cb47b5b75820ea43689
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158353"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Fiziksel sunucular için bir gereç ayarlama

Bu makalede, Azure geçişi: Sunucu değerlendirmesi aracı ile fiziksel sunucuları değerlendirmek için Azure geçişi gerecinin nasıl ayarlanacağı açıklanır.

> [!NOTE]
> Burada henüz Azure geçişi portalında görmeyeceğiniz Özellikler bahsedildiğinde, ' ı kapatın. Bu, sonraki hafta içinde görünürler.

Azure geçişi gereci, aşağıdakileri yapmak için Azure geçişi sunucu değerlendirmesi tarafından kullanılan hafif bir gereç.

- Şirket içi sunucuları bulun.
- Bulunan sunucular için meta verileri ve performans verilerini Azure geçişi sunucu değerlendirmesi ' ne gönderin.

Azure geçişi gereci hakkında [daha fazla bilgi edinin](migrate-appliance.md) .


## <a name="appliance-deployment-steps"></a>Gereç dağıtım adımları

Gereci kurmak için şunları yapın:
- Azure geçişi yükleyicisi komut dosyasıyla Azure portal sıkıştırılmış bir dosyayı indirin.
- Sıkıştırılmış dosyadan içerikleri ayıklayın. Yönetim ayrıcalıklarıyla PowerShell konsolunu başlatın.
- Gereç Web uygulamasını başlatmak için PowerShell betiğini yürütün.
- Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.

## <a name="download-the-installer-script"></a>Yükleyici betiğini indir

Gereç için daraltılmış dosyayı indirin.

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** > **geçiş hedeflerde** **bul**' a tıklayın.
2. **Makineleriniz > ** makinelerde **sanallaştırılmış mı?** , **sanallaştırılmamış/diğer**' e tıklayın.
3. Sıkıştırılmış dosyayı indirmek için **İndir** ' e tıklayın.

    ![VM 'yi indir](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.
2. VHD için karma oluşturmak üzere aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  En son gereç sürümü için, oluşturulan karma bu ayarlarla eşleşmelidir.

  **Algoritma** | **Karma değeri**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f



## <a name="run-the-azure-migrate-installer-script"></a>Azure geçişi yükleyici betiğini çalıştırma
= Yükleyici betiği şunları yapar:

- Fiziksel sunucu keşfi ve değerlendirmesi için aracıları ve bir Web uygulamasını kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini yükler.
- Bir IIS yeniden yazılabilir modülünü indirip yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayar ayrıntılarıyla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Yolun altında aşağıdaki dosyaları oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği aşağıdaki gibi çalıştırın:

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın.
2. Yönetim (yükseltilmiş) ayrıcalığıyla yukarıdaki sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan içeriğin ayıklandığı klasör olarak değiştirin.
4. Aşağıdaki komutu çalıştırarak betiği çalıştırın:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
Betik, başarıyla tamamlandığında gereç Web uygulamasını başlatacaktır.



### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gereç VM 'sinin gerekli [Azure URL 'lerine](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)bağlanabildiğinizden emin olun.

## <a name="configure-the-appliance"></a>Gereci yapılandırma

Gereci ilk kez ayarlayın.

1. VM 'ye bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı masaüstünden açabilirsiniz.
2. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Lisans**: lisans koşullarını kabul edin ve üçüncü taraf bilgilerini okuyun.
    - **Bağlantı**: uygulama, sanal makinenin internet erişimi olup olmadığını denetler. VM bir proxy kullanıyorsa:
        - **Ara sunucu ayarları**' na tıklayın ve proxy adresini ve dinleme bağlantı noktasını, http://ProxyIPAddress veya http://ProxyFQDNbiçiminde belirtin.
        - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
        - Yalnızca HTTP proxy’si desteklenir.
    - **Zaman eşitleme**: Saat doğrulandı. VM bulmanın düzgün çalışması için gereç süresi internet saatine eşit olmalıdır.
    - **Güncelleştirmeleri yükleme**: Azure geçişi sunucu değerlendirmesi, gerecin en son güncelleştirmelerin yüklü olduğunu denetler.

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. **Oturum aç**' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
2. Yeni sekmede, Azure kimlik bilgilerinizi kullanarak oturum açın.
    - Kullanıcı adınızla ve parolanızla oturum açın.
    - PIN ile oturum açma desteklenmez.
3. Başarıyla oturum açtıktan sonra Web uygulamasına geri dönün.
4. Azure geçişi projesinin oluşturulduğu aboneliği seçin. Ardından projeyi seçin.
5. Gereç için bir ad belirtin. Ad 14 karakter veya daha az olmalıdır.
6. **Kaydol**' a tıklayın.


## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Gerecden fiziksel sunuculara bağlanın ve bulmayı başlatın.

1. Gerecin sunucuları keşfetme için kullanacağı hesap kimlik bilgilerini belirtmek için **kimlik bilgileri ekle** ' ye tıklayın.  
2. Kimlik bilgileri, **Kullanıcı adı** ve **parola** için **işletim sistemini**, kolay adı belirtin ve **Ekle**' ye tıklayın.
Her biri Windows ve Linux sunucuları için bir kimlik bilgileri kümesi ekleyebilirsiniz.
4. Sunucuya bağlanmak için sunucu **Ekle**' ye tıklayın ve sunucu ayrıntılarını BELIRTIN-FQDN/IP adresi ve kimlik bilgilerinin kolay adı (satır başına bir giriş).
3. **Doğrula**' ya tıklayın. Doğrulamadan sonra, keşfedilebilir sunucu listesi gösterilir.
    - Bir sunucu için doğrulama başarısız olursa, **durum** sütunundaki simgenin üzerine gelerek hatayı gözden geçirin. Sorunları giderin ve yeniden doğrulayın.
    - Bir sunucuyu kaldırmak için > **Sil**' i seçin.
4. Doğrulamadan sonra, bulma işlemini başlatmak için **Kaydet ve bulmayı Başlat** ' a tıklayın.

Bu, bulmayı başlatır. Bulunan VM 'lerin meta verilerinde Azure portal görünmesi 15 dakika sürer.

## <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulma işlemi tamamlandıktan sonra, sunucuların portalda göründüğünü doğrulayabilirsiniz.

1. Azure geçişi panosunu açın.
2. **Azure geçişi-sunucular** > **Azure geçişi: Sunucu değerlendirmesi** sayfasında, **bulunan sunucuların**sayısını görüntüleyen simgeye tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi sunucu değerlendirmesi ile [fiziksel sunucu değerlendirmesi](tutorial-assess-physical.md) yapmayı deneyin.
