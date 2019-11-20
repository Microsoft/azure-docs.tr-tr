---
title: Azure Depolama Gezgini sorun giderme kılavuzu | Microsoft Docs
description: Azure Depolama Gezgini için hata ayıklama tekniklerine genel bakış
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 4aa9e93831b902ff9f0a0659c650cd2ca123b1a3
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74124013"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Depolama Gezgini sorun giderme kılavuzu

Microsoft Azure Depolama Gezgini, Windows, macOS ve Linux 'ta Azure Depolama verileriyle çalışmayı kolaylaştıran tek başına bir uygulamadır. Uygulama, Azure, Ulusal bulutlar ve Azure Stack barındırılan depolama hesaplarına bağlanabilir.

Bu kılavuzda, Depolama Gezgini yaygın olarak görülen sorunlara yönelik çözümler özetlenmektedir.

## <a name="rbac-permissions-issues"></a>RBAC izinleri sorunları

Rol tabanlı erişim denetimi [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) , _Roller_Için izin kümelerini birleştirerek Azure kaynakları için yüksek düzeyde ayrıntılı erişim yönetimine izin vermez. İşte Depolama Gezgini ortamında RBAC çalışma en iyi şekilde yararlanmak için bazı stratejiler aşağıda verilmiştir.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Nasıl yaparım? Depolama Gezgini kaynaklarıma eriş mi?

RBAC aracılığıyla depolama kaynaklarına erişirken sorun yaşıyorsanız uygun rollere atanmamıştır. Aşağıdaki bölümlerde, şu anda depolama kaynaklarınıza erişim için gereken Depolama Gezgini izinleri açıklanır. Uygun rollere veya izinlere sahip olduğunuzdan emin değilseniz Azure hesap yöneticinize başvurun.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Okuma: depolama hesaplarını Listele/al" izinleri sorunu

Depolama hesaplarını listelemek için izninizin olması gerekir. Bu izni almak için _okuyucu_ rolüne atanmalısınız.

#### <a name="list-storage-account-keys"></a>Depolama hesabı anahtarlarını Listele

Depolama Gezgini, isteklerin kimliğini doğrulamak için hesap anahtarlarını da kullanabilir. _Katkıda bulunan_ rolü gibi daha güçlü roller aracılığıyla hesap anahtarlarına erişim sağlayabilirsiniz.

> [!NOTE]
> Erişim tuşları, bunları tutan herkese Kısıtlanmamış izinler verir. Bu nedenle, kullanıcılara hesap atamak için bu anahtarları izlemenizi önermiyoruz. Erişim anahtarlarını iptal etmeniz gerekirse, [Azure Portal](https://portal.azure.com/)yeniden oluşturabilirsiniz.

#### <a name="data-roles"></a>Veri rolleri

Kaynaklardan veri okuma erişimi veren en az bir rol atanması gerekir. Örneğin, Blobları listelemek veya indirmek isterseniz, en azından _Depolama Blobu veri okuyucusu_ rolüne sahip olmanız gerekir.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Kaynaklarımı Depolama Gezgini görmek için neden bir yönetim katmanı rolüne ihtiyacım var?

Azure Storage iki erişim katmanına sahiptir: _Yönetim_ ve _veri_. Abonelikler ve depolama hesaplarına yönetim katmanı üzerinden erişilir. Kapsayıcılar, Bloblar ve diğer veri kaynaklarına veri katmanı üzerinden erişilir. Örneğin, Azure 'daki depolama hesaplarınızın bir listesini almak istiyorsanız Yönetim uç noktasına bir istek gönderirsiniz. Bir hesapta blob kapsayıcıları listesini isterseniz, uygun hizmet uç noktasına bir istek gönderirsiniz.

RBAC rolleri, yönetim veya veri katmanı erişimi için izinleri içerebilir. Örneğin, okuyucu rolü, yönetim katmanı kaynaklarına salt okuma erişimi verir.

Tamamen konuşuyor, okuyucu rolü veri katmanı izinleri sağlamaz ve veri katmanına erişmek için gerekli değildir.

Depolama Gezgini, Azure kaynaklarınıza bağlanmak için gerekli bilgileri toplayıp kaynaklarınıza erişmeyi kolaylaştırır. Örneğin, blob Kapsayıcılarınızı göstermek için Depolama Gezgini blob hizmeti uç noktasına bir "liste kapsayıcıları" isteği gönderir. Bu uç noktayı almak için Depolama Gezgini, erişiminiz olan aboneliklerin ve depolama hesaplarının listesini arar. Aboneliklerinizi ve depolama hesaplarınızı bulmak için Depolama Gezgini yönetim katmanına erişime de ihtiyaç duyuyor.

Herhangi bir yönetim katmanı izni veren bir rolünüz yoksa Depolama Gezgini veri katmanına bağlanması için gereken bilgileri alamaz.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Yöneticimde ihtiyacım olan yönetim katmanı izinlerini alamazsanız ne yapmalıyım?

Şu anda bu sorun için RBAC ile ilgili bir çözümünüz yok. Geçici bir çözüm olarak, [kaynağına eklemek](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)IÇIN BIR SAS URI 'si isteyebilirsiniz.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hata: sertifika zincirinde otomatik olarak imzalanan sertifika (ve benzer hatalar)

Sertifika hataları genellikle aşağıdaki durumlardan birinde oluşur:

- Uygulama, bir saydam proxy üzerinden bağlanır, yani bir sunucu (örneğin, şirket sunucunuz) HTTPS trafiğini kesintiye _uğratır_, şifresini çözerek otomatik olarak imzalanan bir sertifika kullanarak bunu şifreler.
- Aldığınız HTTPS iletilerine otomatik olarak imzalanan bir SSL sertifikası ekleme bir uygulama çalıştırıyorsunuz. Sertifika ekleme uygulamaları, virüsten koruma ve ağ trafiği İnceleme yazılımlarını içerir.

Depolama Gezgini kendinden imzalı veya güvenilmeyen bir sertifikayı gördüğünde, alınan HTTPS iletisinin değiştirilip değiştirilmediğini artık bilmez. Otomatik olarak imzalanan sertifikanın bir kopyasına sahipseniz, aşağıdaki adımları izleyerek Depolama Gezgini güveneceği konusunda talimat verebilirsiniz:

1. Sertifikanın Base-64 kodlamalı bir X. 509.440 (. cer) kopyasını edinin.
2.  > **SSL sertifikalarını** **Düzenle** > **sertifikaları içeri aktar**' a gidin ve ardından dosya seçiciyi kullanarak. cer dosyasını bulun, seçin ve açın.

Bu sorun, birden fazla sertifika (kök ve ara) varsa da oluşabilir. Bu hatayı onarmak için her iki sertifikanın de eklenmesi gerekir.

Sertifikanın nereden geldiği konusunda emin değilseniz, bulmak için aşağıdaki adımları izleyin:

1. OpenSSL 'yi yükler.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): tüm hafif sürümler yeterli olmalıdır.
    * Mac ve Linux: işletim sisteminize eklenmelidir.
2. OpenSSL 'yi çalıştırın.
    * Windows: yükleme dizinini açın, **/bin/** seçin ve **OpenSSL. exe**' ye çift tıklayın.
    * Mac ve Linux: terminalden `openssl` çalıştırın.
3. `s_client -showcerts -connect microsoft.com:443` öğesini çalıştırın.
4. Otomatik olarak imzalanan sertifikaları bulun. Hangi sertifikaların kendinden imzalandığından emin değilseniz, konu `("s:")` ve veren `("i:")` aynı olan her yerde dikkat edin.
5. Her biri için otomatik olarak imzalanan sertifikalar bulduğunuzda, (ve dahil) `-----BEGIN CERTIFICATE-----` her şeyi `-----END CERTIFICATE-----` yeni bir. cer dosyasına kopyalayıp yapıştırın.
6. Depolama Gezgini açın ve **sertifikaları Içeri aktar** > **SSL sertifikalarını** > **Düzenle** ' ye gidin. Ardından, oluşturduğunuz. cer dosyalarını bulmak, seçmek ve açmak için dosya seçiciyi kullanın.

Bu adımları izleyerek kendinden imzalı bir sertifika bulamıyorsanız, geri bildirim aracı aracılığıyla bizimle iletişim kurun. Ayrıca, `--ignore-certificate-errors` bayrağını kullanarak komut satırından Depolama Gezgini açabilirsiniz. Bu bayrağıyla açıldığında, Depolama Gezgini sertifika hatalarını yoksayar.

## <a name="sign-in-issues"></a>Oturum açma sorunları

### <a name="blank-sign-in-dialog-box"></a>Boş oturum açma iletişim kutusu

Active Directory Federasyon Hizmetleri (AD FS) (AD FS), elektron tarafından desteklenmeyen bir yeniden yönlendirme gerçekleştirmeyi Depolama Gezgini istem yaparken, boş oturum açma iletişim kutuları çoğu zaman oluşur. Bu sorunu geçici olarak çözmek için, oturum açma için cihaz kod akışını kullanmayı deneyebilirsiniz. Bunu yapmak için aşağıdaki adımları izleyin:

1. Menüde **Önizleme** ' ye gidin > **cihaz kodu oturumu kullanın**.
2. **Bağlan** iletişim kutusunu açın (sol taraftaki dikey çubukta bulunan tak simgesi veya hesap panelinde **Hesap Ekle** ' yi seçerek).
3. Oturum açmak istediğiniz ortamı seçin.
4. **Oturum aç '** ı seçin.
5. Sonraki bölmede yer alan yönergeleri izleyin.

Varsayılan tarayıcınız zaten farklı bir hesapta oturum açmış olduğu için kullanmak istediğiniz hesapta oturum açamazsınız, aşağıdakilerden birini yapın:

- Bağlantıyı ve kodu tarayıcınızın özel oturumuna el ile kopyalayın.
- Bağlantıyı ve kodu farklı bir tarayıcıya el ile kopyalayın.

### <a name="reauthentication-loop-or-upn-change"></a>Yeniden kimlik doğrulama döngüsü veya UPN değişikliği

Bir yeniden kimlik doğrulaması döngüsünde veya hesaplarınızdan birinin UPN 'sini değiştirdiyseniz, şu adımları izleyin:

1. Tüm hesapları kaldırın ve ardından Depolama Gezgini kapatın.
2. Öğesini silin. Makinenizden IdentityService klasörü. Windows 'da, klasör `C:\users\<username>\AppData\Local`konumunda bulunur. Mac ve Linux için, klasörü Kullanıcı dizininizin kökünde bulabilirsiniz.
3. Mac veya Linux çalıştırıyorsanız, işletim sisteminizin keystore ' dan Microsoft. Developer. IdentityService girişini de silmeniz gerekir. Mac üzerinde, anahtar deposu *GNOME Anahtarlık* uygulamasıdır. Linux 'ta, uygulama genellikle _kimlik anahtarlığı_olarak adlandırılır ancak ad, dağıtıma bağlı olarak farklılık gösterebilir.

### <a name="conditional-access"></a>Koşullu Erişim

Depolama Gezgini tarafından kullanılan Azure AD kitaplığındaki bir sınırlama nedeniyle, Windows 10, Linux veya macOS 'ta Depolama Gezgini kullanılırken koşullu erişim desteklenmez.

## <a name="mac-keychain-errors"></a>Mac Anahtarlık hataları

MacOS anahtarlığı bazen Depolama Gezgini kimlik doğrulama kitaplığı için soruna neden olan bir durum girebilir. Anahtarlığı bu durumdan dışarı almak için aşağıdaki adımları izleyin:

1. Depolama Gezgini kapatın.
2. Anahtarlık açın (komut + ara çubuğuna basın, **Anahtarlık**yazın ve ENTER tuşuna basın).
3. "Oturum açma" Anahtarışını seçin.
4. Anahtarlık kilidini kilitlemek için asma kilit simgesini seçin. (İşlem tamamlandığında, asma kilidi kilitli olarak görünür. Bu işlem, açtığınız uygulamalara bağlı olarak birkaç saniye sürebilir).

    ![Asma kilit simgesi](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Depolama Gezgini'ni açın.
6. "Hizmet hub 'ı anahtarlığa erişmek istiyor." gibi bir iletiyle karşılaşıyoruz. Mac yönetici hesabı parolanızı girip **her zaman Izin ver** ' i seçin (veya **her zaman izin** ver ' **i seçerseniz)** .
7. Oturum açmayı deneyin.

### <a name="general-sign-in-troubleshooting-steps"></a>Genel oturum açma sorunlarını giderme adımları

* MacOS kullanıyorsanız ve oturum açma penceresi **kimlik doğrulaması Için bekleniyor** iletişim kutusunda hiçbir şekilde yoksa, [aşağıdaki adımları](#mac-keychain-errors)deneyin.
* Depolama Gezgini yeniden başlatın.
* Kimlik doğrulama penceresi boşsa, kimlik doğrulama iletişim kutusunu kapatmadan önce en az bir dakika bekleyin.
* Proxy ve sertifika ayarlarınızın hem makineniz hem de Depolama Gezgini için düzgün yapılandırıldığından emin olun.
* Windows çalıştırıyorsanız ve aynı makinede ve oturum açma kimlik bilgilerinde Visual Studio 2019 ' e erişiminiz varsa, Visual Studio 2019 ' de oturum açmayı deneyin. Visual Studio 2019 ' de başarılı bir oturum açma işleminden sonra, Depolama Gezgini açıp hesabınızı hesap panelinde görebilirsiniz.

Bu yöntemlerin hiçbiri işe çalışmadıysanız, [GitHub 'da bir sorun açın](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Eksik abonelikler ve bozuk kiracılar

Başarıyla oturum açtıktan sonra aboneliklerinizi alamadıysanız aşağıdaki sorun giderme yöntemlerini deneyin:

* Hesabınızın, bekleyen aboneliklere erişimi olduğunu doğrulayın. Kullanmaya çalıştığınız Azure ortamı için portalda oturum açarak erişiminizi doğrulayabilirsiniz.
* Doğru Azure ortamında (Azure, Azure Çin 21Vianet, Azure Almanya, Azure ABD kamu veya özel ortam) oturum açtığınızdan emin olun.
* Bir ara sunucunun arkasındaysanız, Depolama Gezgini proxy 'yi doğru yapılandırdığınızdan emin olun.
* Hesabı kaldırıp yeniden eklemeyi deneyin.
* "Daha fazla bilgi" bağlantısı varsa, başarısız olan kiracılar için hangi hata iletilerinin raporlanmakta olduğunu kontrol edin. Hata iletilerine yanıt verme konusunda emin değilseniz, [GitHub 'da bir sorun açmayı](https://github.com/Microsoft/AzureStorageExplorer/issues)ücretsiz olarak kullanabilirsiniz.

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Eklenmiş bir hesap veya depolama kaynağı kaldırılamıyor

Kullanıcı arabirimi aracılığıyla ekli bir hesabı veya depolama kaynağını kaldıramıyorum, aşağıdaki klasörleri silerek tüm bağlı kaynakları el ile silebilirsiniz:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Bu klasörleri silmeden önce Depolama Gezgini kapatın.

> [!NOTE]
> Herhangi bir SSL sertifikası aldıysanız, `certs` dizininin içeriğini yedekleyin. Daha sonra, SSL sertifikalarınızı yeniden içeri aktarmak için yedekleme 'yi kullanabilirsiniz.

## <a name="proxy-issues"></a>Proxy sorunları

İlk olarak, girdiğiniz aşağıdaki bilgilerin doğru olduğundan emin olun:

* Proxy URL 'SI ve bağlantı noktası numarası
* Ara sunucu için gerekliyse Kullanıcı adı ve parola

> [!NOTE]
> Depolama Gezgini, proxy ayarlarını yapılandırmak için proxy otomatik yapılandırma dosyalarını desteklemez.

### <a name="common-solutions"></a>Ortak çözümler

Sorun yaşamaya devam ediyorsanız, aşağıdaki sorun giderme yöntemlerini deneyin:

* Proxy 'nizi kullanmadan internet 'e bağlanabildiğinizi, Depolama Gezgini proxy ayarları etkin olmadan çalıştığını doğrulayın. Bu durumda, ara sunucu ayarlarınızda bir sorun olabilir. Sorunları belirlemek için yöneticinizle birlikte çalışın.
* Proxy sunucusunu kullanan diğer uygulamaların beklendiği gibi çalıştığını doğrulayın.
* Kullanmaya çalıştığınız Azure ortamı için portala bağlanabildiğinizi doğrulayın.
* Hizmet uç noktaınızdan yanıt alabildiğinizi doğrulayın. Tarayıcınızın uç nokta URL 'Lerinden birini girin. Bağlantı kurmak için InvalidQueryParameterValue veya benzer bir XML yanıtı almalısınız.
* Başka biri de ara sunucu ile Depolama Gezgini kullanıyorsa, bağlanabildiğini doğrulayın. Mümkünse, proxy sunucu yöneticinize başvurmanız gerekebilir.

### <a name="tools-for-diagnosing-issues"></a>Sorunları tanılamaya yönelik araçlar

Windows için Fiddler gibi ağ araçlarınız varsa, sorunları aşağıdaki gibi tanılayabilirsiniz:

* Proxy 'niz üzerinden çalışmanız gerekiyorsa, ağ iletişimi aracınızı proxy üzerinden bağlanacak şekilde yapılandırmanız gerekebilir.
* Ağ aracınız tarafından kullanılan bağlantı noktası numarasını kontrol edin.
* Depolama Gezgini ' deki proxy ayarları olarak yerel ana bilgisayar URL 'sini ve ağ aracının bağlantı noktası numarasını girin. Bunu doğru yaptığınızda, ağ aracınız, yönetim ve hizmet uç noktalarına Depolama Gezgini tarafından yapılan ağ isteklerini günlüğe kaydetmeye başlar. Örneğin, bir tarayıcıda blob uç noktanız için `https://cawablobgrs.blob.core.windows.net/` girin ve aşağıdakine benzer bir yanıt alırsınız:

  ![Kod örneği](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Bu yanıt, erişemeseniz de kaynağın mevcut olduğunu önerir.

### <a name="contact-proxy-server-admin"></a>Proxy Sunucu Yöneticisi ile iletişim kurun

Proxy ayarlarınız doğruysa, proxy sunucu yöneticinizle iletişime geçerek şunları yapabilirsiniz:

* Proxy 'nizin Azure yönetimine veya kaynak uç noktalarına giden trafiği engellemediğinden emin olun.
* Proxy sunucunuz tarafından kullanılan kimlik doğrulama protokolünü doğrulayın. Depolama Gezgini Şu anda NTLM proxy 'leri desteklemiyor.

## <a name="unable-to-retrieve-children-error-message"></a>"Alt öğeler alınamıyor" hata iletisi

Azure 'a bir proxy üzerinden bağlıysanız, proxy ayarlarınızın doğru olduğundan emin olun. Aboneliğin veya hesabın sahibinden bir kaynağa erişim izni verildiyse, bu kaynak için okuma veya listeleme izinlerine sahip olduğunuzu doğrulayın.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Bağlantı dizesinin yapılandırma ayarları Tamam

Bu hata iletisini alırsanız, depolama hesabınız için anahtarları almak için gerekli izinlere sahip olmanız mümkün değildir. Bu durumun bu olduğunu onaylamak için portala gidin ve depolama hesabınızı bulun. Bunu, depolama hesabınız için düğüme sağ tıklayıp **portalda aç '** ı seçerek yapabilirsiniz. Ardından, **erişim tuşları** dikey penceresine gidin. Anahtarları görüntüleme izniniz yoksa, "erişiminiz yok" iletisini görürsünüz. Bu sorunu geçici olarak çözmek için, hesap anahtarını başka bir kişiden edinebilir ve ad ve anahtar aracılığıyla iliştirebilir ya da bir kullanıcıdan depolama hesabı için bir SAS isteyebilir ve depolama hesabını eklemek için onu kullanabilirsiniz.

Hesap anahtarlarını görürseniz, sorunu çözmenize yardımcı olması için GitHub 'da bir sorun çözün.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Yeni bağlantı eklenirken hata oluştu: TypeError: tanımsız olan ' version ' özelliği okunamıyor

Özel bir bağlantı eklemeye çalıştığınızda bu hata iletisini alırsanız, yerel kimlik bilgileri Yöneticisi 'nde depolanan bağlantı verileri bozulmuş olabilir. Bu sorunu geçici olarak çözmek için bozuk yerel bağlantılarınızı silmeyi ve sonra yeniden eklemeyi deneyin:

1. Depolama Gezgini başlatın. Menüden **yardım** > **Geliştirici Araçları değiştirme**' ye gidin.
2. Açılan pencerede, **uygulama** sekmesinde, **File://** > **yerel depolama** (sol taraf) seçeneğine gidin.
3. Sorun yaşadığınız bağlantı türüne bağlı olarak, anahtarını bulun ve sonra değerini bir metin düzenleyicisine kopyalayın. Değer, aşağıdaki gibi özel bağlantı adlarınızın bir dizisidir:
    * Depolama hesapları
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob kapsayıcıları
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Dosya paylaşımları
        * `StorageExplorer_CustomConnections_Files_v1`
    * Kuyruklar
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tablolar
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Geçerli bağlantı adlarınızı kaydettikten sonra, Geliştirici Araçları değerini `[]`olarak ayarlayın.

Bozulan bağlantıları korumak istiyorsanız, bozuk bağlantıları bulmak için aşağıdaki adımları kullanabilirsiniz. Var olan tüm bağlantıları kaybetmezseniz, bu adımları atlayabilir ve bağlantı verilerinizi temizlemek için platforma özgü yönergeleri izleyebilirsiniz.

1. Bir metin düzenleyicisinden her bağlantı adını Geliştirici Araçları yeniden ekleyin ve sonra bağlantının çalışıp çalışmadığını denetleyin.
2. Bir bağlantı doğru çalışıyorsa, bu bozuk değildir ve güvenli bir şekilde bırakabilirsiniz. Bir bağlantı çalışmıyorsa, Geliştirici Araçları değerini kaldırın ve daha sonra tekrar ekleyebilmek için kaydedin.
3. Tüm bağlantılarınızı incelene kadar tekrarlayın.

Tüm bağlantılarınız bittikten sonra, geri eklenmemiş tüm bağlantı adları için, bozulmuş verileri (varsa) temizlemeniz ve Depolama Gezgini içindeki standart adımları kullanarak geri eklemeniz gerekir:

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. **Başlat** menüsünde **kimlik bilgileri Yöneticisi** ' ni arayın ve açın.
2. **Windows kimlik bilgileri**' ne gidin.
3. **Genel kimlik bilgileri**altında, `<connection_type_key>/<corrupted_connection_name>` anahtarına sahip girdileri arayın (örneğin, `StorageExplorer_CustomConnections_Accounts_v1/account1`).
4. Bu girişleri silip bağlantıları yeniden ekleyin.

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Spotlight (komut + ara çubuğu) öğesini açın ve **Anahtarlık erişimi**arayın.
2. `<connection_type_key>/<corrupted_connection_name>` anahtara sahip girdileri arayın (örneğin, `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Bu girişleri silip bağlantıları yeniden ekleyin.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Yerel kimlik bilgisi yönetimi, Linux dağıtımına bağlı olarak farklılık gösterir. Linux yönetiyoruz yerel kimlik bilgileri yönetimi için yerleşik bir GUI aracı sağlamıyorsa, yerel kimlik bilgilerinizi yönetmek için bir üçüncü taraf araç yükleyebilirsiniz. Örneğin, Linux yerel kimlik bilgilerini yönetmek için açık kaynaklı bir GUI aracı olan [Mevsimat](https://wiki.gnome.org/Apps/Seahorse/)'ı kullanabilirsiniz.

1. Yerel kimlik bilgileri yönetim aracınızı açın ve kaydettiğiniz kimlik bilgilerinizi bulun.
2. `<connection_type_key>/<corrupted_connection_name>` anahtara sahip girdileri arayın (örneğin, `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Bu girişleri silip bağlantıları yeniden ekleyin.
---

Bu adımları çalıştırdıktan sonra yine de bu hatayla karşılaşırsanız veya ne olursa şüphelendiğiniz bağlantıları paylaşmak istiyorsanız GitHub sayfamızda [bir sorun açın](https://github.com/microsoft/AzureStorageExplorer/issues) .

## <a name="issues-with-sas-url"></a>SAS URL 'SI ile ilgili sorunlar

Bir güvenlik alanına bir SAS URL 'SI aracılığıyla bağlanıyorsanız ve bir hata yaşıyorsanız:

* URL 'nin kaynakları okumak veya listelemek için gerekli izinleri sağladığını doğrulayın.
* URL 'nin süresi dolmadığından emin olun.
* SAS URL 'SI bir erişim ilkesini temel alıyorsa, erişim ilkesinin iptal edilmediğini doğrulayın.

Yanlışlıkla geçersiz bir SAS URL 'SI kullanarak eklediyseniz ve bundan sonra ayrılamaz, şu adımları izleyin:

1. Depolama Gezgini çalıştırırken, Geliştirici Araçları penceresini açmak için F12 tuşuna basın.
2. **Uygulama** sekmesinde, sol taraftaki ağaçta **yerel depolama** > **File://** ' u seçin.
3. Sorunlu SAS URI 'sinin hizmet türüyle ilişkili anahtarı bulun. Örneğin, hatalı SAS URI 'SI bir blob kapsayıcısı için ise, `StorageExplorer_AddStorageServiceSAS_v1_blob`adlı anahtarı bulun.
4. Anahtarın değeri bir JSON dizisi olmalıdır. Hatalı URI ile ilişkili nesneyi bulun ve silin.
5. Depolama Gezgini yeniden yüklemek için CTRL + R tuşlarına basın.

## <a name="linux-dependencies"></a>Linux bağımlılıkları

Depolama Gezgini 1.10.0 ve üzeri, Snap mağazasından bir snap olarak sunulmaktadır. Depolama Gezgini Snap, tüm bağımlılıklarını otomatik olarak yüklüyor ve bir ek bileşenin yeni bir sürümü kullanılabilir olduğunda güncellenir. Depolama Gezgini Snap 'in yüklenmesi önerilen yükleme yöntemidir.

Depolama Gezgini, Depolama Gezgini doğru çalışmadan önce el ile bağlanmanız gerekebilecek bir parola yöneticisinin kullanılmasını gerektirir. Aşağıdaki komutu çalıştırarak, Depolama Gezgini sisteminizin parola yöneticisine bağlayabilirsiniz:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Uygulamayı. tar. gz dosyası olarak da indirebilirsiniz, ancak bağımlılıkları el ile yüklemelisiniz.

> [!IMPORTANT]
> . Tar. gz indirmesi içinde sağlandığı gibi Depolama Gezgini yalnızca Ubuntu dağıtımları için desteklenir. Diğer dağıtımlar doğrulanmadı ve alternatif veya ek paketler gerektirebilir.

Bu paketler, Linux üzerinde Depolama Gezgini için en yaygın gereksinimlerdir:

* [.NET Core 2,0 çalışma zamanı](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` veya `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Depolama Gezgini Version 1.7.0 ve önceki sürümleri .NET Core 2,0 gerektirir. .NET Core 'un daha yeni bir sürümü yüklüyse, [Depolama Gezgini yama](#patching-storage-explorer-for-newer-versions-of-net-core)yapmanız gerekir. Depolama Gezgini 1.8.0 veya sonraki bir sürümü çalıştırıyorsanız, .NET Core 2,2 ' e kadar kullanabilmeniz gerekir. 2,2 ' den fazla sürüm şu anda çalışacak şekilde doğrulanmadı.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19,04](#tab/1904)

1. Depolama Gezgini indirin.
2. [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)yükler.
3. Şu komutu çalıştırın:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Depolama Gezgini indirin.
2. [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)yükler.
3. Şu komutu çalıştırın:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16,04](#tab/1604)

1. Depolama Gezgini indirin.
2. [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)yükler.
3. Şu komutu çalıştırın:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14,04](#tab/1404)

1. Depolama Gezgini indirin.
2. [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)yükler.
3. Şu komutu çalıştırın:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>.NET Core 'un daha yeni sürümleri için Depolama Gezgini düzeltme eki uygulama

Depolama Gezgini 1.7.0 veya daha önceki bir sürümü için Depolama Gezgini tarafından kullanılan .NET Core sürümüne yama yapmanız gerekebilir:

1. [NuGet 'Den](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)StreamJsonRpc 'nin 1.5.43 sürümünü indirin. Sayfanın sağ tarafındaki "paketi Indir" bağlantısını bulun.
2. Paketi indirdikten sonra, `.nupkg` dosya uzantısını `.zip`olarak değiştirin.
3. Paketi sıkıştırmayı açın.
4. Açık `streamjsonrpc.1.5.43/lib/netstandard1.1/` klasör.
5. `StreamJsonRpc.dll` Depolama Gezgini klasöründeki aşağıdaki konumlara kopyalayın:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure portal işe yaramazsa "Gezgin 'de aç"

Azure portal **Explorer 'Da aç** düğmesi işe yaramazsa, uyumlu bir tarayıcı kullandığınızdan emin olun. Aşağıdaki tarayıcılar uyumluluk için sınanmıştır:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Sonraki adımlar

Bu çözümlerin hiçbiri sizin için işe çalışmadıysanız, [GitHub 'da bir sorun açın](https://github.com/Microsoft/AzureStorageExplorer/issues). Bunu, sol alt köşedeki **GitHub 'a sorun raporla** düğmesine seçerek de yapabilirsiniz.

![Geri Bildirim](./media/storage-explorer-troubleshooting/feedback-button.PNG)
