---
title: VMware için Azure geçişi gereci ayarlama
description: VMware ortamındaki sunucuları değerlendirmek ve geçirmek için bir Azure geçiş gereci ayarlamayı öğrenin.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 685d7f0a0aaab2f38967e0eb6c32c3fb4067dbe3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612832"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>VMware ortamındaki sunucular için bir gereç ayarlama

Azure geçişi [: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) aracı ile değerlendirme Için Azure geçişi gereci ayarlamak ve [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) aracını kullanarak aracısız geçiş yapmak için bu makaleyi izleyin.

[Azure geçişi](migrate-appliance.md) gereci, Azure geçişi tarafından kullanılan hafif bir gerecdir: vCenter Server çalıştıran sunucuları bulmak için bulma ve değerlendirme ve sunucu geçişi, Azure 'a sunucu yapılandırma ve performans meta verileri gönderme ve aracısız geçiş kullanarak sunucuların çoğaltılması.

Gereci birkaç yöntem kullanarak dağıtabilirsiniz:

- İndirilen bir OVA şablonunu kullanarak vCenter Server bir sunucu oluşturun. Bu, bu makalede açıklanan yöntemdir.
- Bir PowerShell yükleyici betiği kullanarak var olan bir sunucuda gereç ayarlayın. [Bu yöntem](deploy-appliance-script.md) , ova şablonunu kullansanız veya Azure Kamu kullanıyorsanız kullanılmalıdır.

Gereci oluşturduktan sonra Azure geçişi: bulma ve değerlendirme 'a bağlanıp bağlanamadıktan sonra projeyi projeye kaydeder ve gereci bulmayı başlatacak şekilde yapılandırın.

## <a name="deploy-with-ova"></a>OVA ile dağıtma

Bir OVA şablonunu kullanarak gereci ayarlamak için:

1. Portalda bir gereç adı sağlayın ve bir proje anahtarı oluşturun.
1. Bir OVA şablon dosyasını indirip vCenter Server içe aktarın. OVA 'nın güvenli olduğunu doğrulayın.
1. OVA dosyasından gereç sanal makinesini oluşturun ve Azure geçişi 'ne bağlanıp bağlanamadığından emin olun.
1. Gereci ilk kez yapılandırın ve proje anahtarını kullanarak projeyi projeye kaydedin.

### <a name="1-generate-the-project-key"></a>1. proje anahtarı oluştur

1. **Geçiş hedefleri**  >  **sunucularında**  >  **Azure geçişi: bulma ve değerlendirme** bölümünde **bul**' u seçin.
2. Sunucularınızdaki **sunucular**  >  **sanallaştırılmış mı?**, **VMware vSphere hiper yöneticiyle Evet '** i seçin.
3. **1: proje anahtarı oluştur**' da, VMware ortamınızda sunucu keşfi Için ayarladığınız Azure geçiş gereci için bir ad sağlayın. Ad 14 karakter veya daha kısa bir harf olmalıdır.
1. Gerekli Azure kaynaklarını oluşturmaya başlamak için **anahtar oluştur** ' a tıklayın. Kaynak oluşturma sırasında bul sayfasını kapatmayın.
1. Azure kaynakları başarıyla oluşturulduktan sonra bir proje anahtarı * * oluşturulur.
1. Yapılandırma sırasında gereç kaydını tamamlamamak için gerekli olacak şekilde anahtarı kopyalayın.

### <a name="2-download-the-ova-template"></a>2. OVA şablonunu indirin

**2: Azure geçişi yükleme gereci indirin**, öğesini seçin. OVA dosyası ve **İndir**' e tıklayın.

### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtım yapmadan önce OVA dosyasının güvenli olup olmadığını denetleyin:

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. OVA dosyasının karmasını oluşturmak için aşağıdaki komutu çalıştırın:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Örnek kullanım: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. En son gereç sürümlerini ve karma değerlerini doğrulayın:

    - Azure genel bulutu için:
    
        **Algoritma** | **İndir** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-create-the-appliance-server"></a>3. gereç sunucusunu oluşturma

İndirilen dosyayı içeri aktarın ve VMware ortamında bir sunucu oluşturun

1. VSphere istemci konsolunda,   >  **ovf şablonu dosya dağıt**' a tıklayın.
2. OVF şablonu Dağıtma Sihirbazı > **kaynak** bölümünde ova dosyasının konumunu belirtin.
3. **Ad** ve **konum** bölümünde sunucu için bir kolay ad belirtin. Sunucunun barındırılacak envanter nesnesini seçin.
5. **Konak/küme**' da, sunucunun çalıştırılacağı konağı veya kümeyi belirtin.
6. **Depolama** alanında, sunucu için depolama hedefini belirtin.
7. **Disk Biçimi**’nde disk türünü ve boyutunu belirtin.
8. **Ağ eşlemesinde**, sunucunun bağlanacağı ağı belirtin. Ağ, Azure geçişi 'ne meta veri göndermek için internet bağlantısı gerektirir.
9. Ayarları gözden geçirip onayladıktan sonra **Son**’a tıklayın.

### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gereç sunucusunun [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutları için Azure URL 'lerine bağlanabildiğinizden emin olun.

### <a name="4-configure-the-appliance"></a>4. gereci yapılandırma

Gereci ilk kez ayarlayın.

> [!NOTE]
> Gereci, indirilen OVA yerine bir [**PowerShell betiği**](deploy-appliance-script.md) kullanarak ayarlarsanız, bu yordamın ilk iki adımı ilgili değildir.

1. VSphere Istemci konsolunda sunucuya sağ tıklayın ve ardından **Konsolu Aç**' ı seçin.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. Gereç sunucusuna bağlanabilecek herhangi bir sunucuda bir tarayıcı açın ve gereç Yapılandırma Yöneticisi 'nin URL 'sini açın: `https://appliance name or IP address: 44368` .

   Alternatif olarak, Configuration Manager kısayolunu seçerek, Gereç sunucu masaüstündeki Configuration Manager ' ı açabilirsiniz.
1. **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
1. Configuration Manager > **önkoşulları ayarlama** bölümünde şunları yapın:
   - **Bağlantı**: gereç, sunucunun internet erişimi olup olmadığını denetler. Sunucu bir proxy kullanıyorsa:
     - Form  `http://ProxyIPAddress` veya `http://ProxyFQDN` dinleme bağlantı noktasında proxy adresini belirtmek için kurulum Proxy 'ye tıklayın.
     - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
     - Yalnızca HTTP proxy’si desteklenir.
     - Proxy ayrıntıları eklediyseniz veya proxy ve/veya kimlik doğrulamasını devre dışı bırakırsanız, bağlantıyı tetiklemek için **Kaydet** 'e tıklayarak bağlantı denetimini yeniden başlatın.
   - **Zaman eşitleme**: bulmanın düzgün çalışması için gereç üzerindeki zaman internet ile eşitlenmiş olmalıdır.
   - **Güncelleştirmeleri yükleme**: gereç en son güncelleştirmelerin yüklü olmasını sağlar. Denetim tamamlandıktan sonra, Gereç sunucusunda çalışan hizmetlerin durumunu ve sürümlerini görmek için **gereç hizmetlerini görüntüle** ' ye tıklayabilirsiniz.
   - **VDDK 'Yi yükleme**: gereç, VMware vSphere sanal disk geliştirme seti 'nın (VDDK) yüklü olduğunu denetler. Yüklü değilse, VMware 'den VDDK 6,7 ' i indirin ve indirilen ZIP içeriğini **yükleme yönergelerindeki** belirtilen konuma ayıklayın.

     Azure geçişi sunucu geçişi, Azure 'a geçiş sırasında sunucuları çoğaltmak için VDDK 'yi kullanır. 
1. İsterseniz, gerecin tüm önkoşulları karşılayıp karşılamadığını denetlemek için gereç yapılandırması sırasında dilediğiniz zaman **Önkoşulları yeniden çalıştırabilirsiniz** .

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Gereç Yapılandırma Yöneticisi 'nde Panel 1":::

## <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. Portaldan kopyalanmış **Proje anahtarını** yapıştırın. Anahtarınız yoksa, **bulma ve değerlendirme ' ya gidin> var olan gereçlerini bulmak> yönetin**, anahtar oluşturma sırasında verdiğiniz gereç adını seçin ve karşılık gelen anahtarı kopyalayın.
1. Azure ile kimlik doğrulaması yapmak için bir cihaz koduna ihtiyacınız olacaktır. **Oturum** açmak için tıkladığınızda, aşağıda gösterildiği gibi cihaz koduyla kalıcı olarak açılır.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Cihaz kodunu kalıcı olarak gösterme":::

1. Cihaz kodunu kopyalamak ve yeni bir tarayıcı sekmesinde bir Azure oturum açma istemi açmak için **kodu kopyala & oturum** aç ' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
1. Yeni sekmede, Azure Kullanıcı adınızı ve parolanızı kullanarak cihaz kodunu yapıştırın ve oturum açın.
   
   PIN ile oturum açma desteklenmez.
3. Oturum açmadan oturum açma sekmesini yanlışlıkla kapatırsanız, oturum açma düğmesini yeniden etkinleştirmek için gereç Yapılandırma Yöneticisi 'nin tarayıcı sekmesini yenilemeniz gerekir.
1. Başarıyla oturum açtıktan sonra, Gereç Yapılandırma Yöneticisi ile önceki sekmeye geri dönün.
1. Günlüğe kaydetme için kullanılan Azure Kullanıcı hesabının, anahtar üretimi sırasında oluşturulan Azure kaynakları üzerinde doğru izinleri varsa, Gereç kaydı başlatılır.
1. Gereç başarıyla kaydedildikten sonra, **Ayrıntıları görüntüle**' ye tıklayarak kayıt ayrıntılarına bakabilirsiniz.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Gereç Yapılandırma Yöneticisi 'nde panel 2":::

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

### <a name="provide-vcenter-server-details"></a>vCenter Server ayrıntılarını sağlayın

Gereçinin, sunucuların yapılandırma ve performans verilerini bulması için vCenter Server 'e bağlanması gerekir.

1. **1. Adım: kimlik bilgilerini vCenter Server sağlayın**' de kimlik bilgileri için kolay bir ad belirtmek üzere **kimlik bilgileri ekle** ' ye tıklayın, gerecin vCenter Server çalıştıran sunucuları bulmaya kullanacağı vCenter Server hesabı Için **Kullanıcı adı** ve **parola** ekleyin.
    - Yukarıdaki bu makalede ele alınan gerekli izinlere sahip bir hesap ayarlamış olmanız gerekir.
    - Bulma işlemini belirli VMware nesneleri (vCenter Server veri merkezleri, kümeler, bir küme klasörü, konaklar, konaklar klasörü veya ayrı sunucular) için kapsama eklemek istiyorsanız, Azure geçişi tarafından kullanılan hesabı kısıtlamak için [Bu makaledeki](set-discovery-scope.md) yönergeleri gözden geçirin.
1. **2. Adım: vCenter Server ayrıntıları belirtin** bölümünde, açılan listeden kimlik bilgileri için kolay ad seçmek üzere **bulma kaynağı Ekle** ' ye tıklayın, vCenter Server **IP adresini/FQDN** 'yi belirtin. **Bağlantı noktasını** varsayılan (443) olarak bırakabilir veya vCenter Server dinlediği ve **Kaydet**' e tıklayarak özel bir bağlantı noktası belirtebilirsiniz.
1. **Kaydet**'e tıklanınca, Gereç vCenter Server bağlantısını doğrulamayı dener ve tablodaki **doğrulama durumunu** vCenter Server IP adresine/FQDN 'ye göre gösterir.
1. Bulmayı başlatmadan önce vCenter Server bağlantıyı **yeniden doğrulayabilirsiniz** .

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server Ayrıntılar için gereç Yapılandırma Yöneticisi paneli":::

### <a name="provide-server-credentials"></a>Sunucu kimlik bilgilerini belirtin

**3. Adım: yazılım envanterini gerçekleştirmek için sunucu kimlik bilgilerini sağlama, aracısız bağımlılık Analizi ve SQL Server örneklerinin ve veritabanlarının bulunması** için, birden çok sunucu kimlik bilgilerini sağlamayı seçebilir veya bu özelliklerden yararlanmak istemiyorsanız, adımı atlayıp vCenter Server bulmaya devam edebilirsiniz. Amacınızı daha sonra istediğiniz zaman değiştirebilirsiniz.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Sunucu ayrıntıları için gereç Yapılandırma Yöneticisi 'nde panel 3":::


Bu özelliklerden yararlanmak istiyorsanız aşağıdaki adımları izleyerek sunucu kimlik bilgileri sağlayabilirsiniz. Gereç, bulma özelliklerini gerçekleştirmek için kimlik bilgilerini sunucularla otomatik olarak eşlemeye çalışır.

- **Kimlik bilgileri ekle** düğmesine tıklayarak sunucu kimlik bilgilerini ekleyebilirsiniz. Bu işlem, açılan listeden **kimlik bilgileri türünü** seçebileceğiniz kalıcı bir şekilde açılır.
- Etki alanı/Windows (etki alanı olmayan)/Linux (etki alanı olmayan)/SQL Server kimlik doğrulama kimlik bilgileri sağlayabilirsiniz. Kimlik bilgilerini ve bunları nasıl işleyeceğinizi [öğrenmek hakkında daha fazla bilgi edinin](add-server-credentials.md) .
- Her kimlik bilgileri türü için kimlik bilgileri için kolay bir ad belirtmeniz, **Kullanıcı adı** ve **parola** eklemeniz ve **Kaydet**' e tıklamanız gerekir.
- Etki alanı kimlik bilgilerini seçerseniz, etki alanının FQDN 'sini de belirtmeniz gerekir. Bu etki alanının Active Directory kimlik bilgilerinin orijinalliğini doğrulamak için FQDN gereklidir.
- Yüklenen uygulamaların bulunması, aracısız bağımlılık analizi veya SQL Server örneklerinin ve veritabanlarının bulunması için hesapta [gerekli izinleri](add-server-credentials.md#required-permissions) gözden geçirin.
- Aynı anda birden çok kimlik bilgisi eklemek istiyorsanız, kaydetmek için **daha fazla Ekle** ' ye tıklayın ve daha fazla kimlik bilgisi ekleyin.
- **Kaydet** veya **daha fazla Ekle**' ye tıkladığınızda, Gereç, etki alanı kimlik bilgilerini, etki alanının Active Directory kimliğini doğrular. Bu, Gereç kimlik bilgilerini ilgili sunucularla eşlemek için birden çok yineleme yaparken hesap kilitlenmelerini önlemek için yapılır.
- Kimlik bilgileri tablosundaki tüm etki alanı kimlik bilgileri için **doğrulama durumunu** görebilirsiniz. Yalnızca etki alanı kimlik bilgileri doğrulanacak.
- Doğrulama başarısız olursa, karşılaşılan hatayı görmek için **başarısız** durumuna tıklayabilir ve başarısız etki alanı kimlik bilgilerini yeniden doğrulamak için sorunu düzelttikten sonra **kimlik bilgilerini yeniden doğrula** ' ya tıklayabilirsiniz.
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Birden çok kimlik bilgisi sağlamak için gereç Yapılandırma Yöneticisi 'nde panel 3":::

### <a name="start-discovery"></a>Bulmayı başlat

1. VCenter Server bulmayı **başlatmak için bulmayı Başlat**' a tıklayın. Bulma işlemi başarılı bir şekilde başlatıldıktan sonra, kaynak tablosundaki vCenter Server IP adresine/FQDN 'ye karşı bulma durumunu kontrol edebilirsiniz.
1. Sunucu kimlik bilgilerini sağladıysanız, vCenter Server bulma işlemi tamamlandıktan sonra yazılım envanteri (yüklü uygulamaların keşfi) otomatik olarak başlatılır. Yazılım envanteri her 12 saatte bir gerçekleştirilir.
1. [Yazılım envanteri](how-to-discover-applications.md) , sunucularda çalışan SQL Server örnekleri tanımlar ve bilgileri kullanarak, Gereç üzerinde sunulan Windows kimlik doğrulaması veya SQL Server kimlik doğrulama kimlik bilgileri aracılığıyla örneklere bağlanmaya çalışır ve SQL Server veritabanlarına ve bunların özelliklerine veri toplar. SQL bulma her 24 saatte bir gerçekleştirilir.
1. Yazılım envanteri sırasında, eklenen sunucu kimlik bilgileri sunuculara göre tekrarlanacaktır ve aracısız bağımlılık analizi için onaylanır. Sunucular için aracısız bağımlılık analizini portaldan etkinleştirebilirsiniz. Aracısız bağımlılık analizini etkinleştirmek için yalnızca doğrulamanın başarılı olduğu sunucular seçilebilir.

Bulma işlemi aşağıdaki gibi çalışmaktadır:

- Keşfedilen sunucular envanterinin portalda görünmesi 15 dakika sürer.
- Yüklü uygulamaların bulunması biraz zaman alabilir. Süre, bulunan sunucu sayısına bağlıdır. 500 sunucularında, bulunan stokun Azure geçişi portalında görünmesi yaklaşık bir saat sürer.
- Sunucuları bulma işlemi tamamlandıktan sonra, portaldan sunucular üzerinde aracısız bağımlılık analizini etkinleştirebilirsiniz.
- SQL Server örnekleri ve veritabanı verileri, bulma başlatma işleminden itibaren 24 saat içinde portalda görünmeye başlar.

## <a name="next-steps"></a>Sonraki adımlar

[VMware değerlendirmesi](./tutorial-assess-vmware-azure-vm.md) ve [aracısız geçiş](tutorial-migrate-vmware.md)öğreticilerini gözden geçirin.
