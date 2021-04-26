---
title: Azure Depolama Gezgini sorun giderme kılavuzu | Microsoft Docs
description: Azure Depolama Gezgini için hata ayıklama tekniklerine genel bakış
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568548"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Depolama Gezgini sorun giderme kılavuzu

Microsoft Azure Depolama Gezgini; Windows, macOS ve Linux’ta Azure Depolama ile çalışmayı kolaylaştıran bir tek başına uygulamadır. Uygulama Azure, ulusal bulutlar ve Azure Stack üzerinde barındırılan depolama hesaplarına bağlanabilir.

Bu kılavuzda, Depolama Gezgini yaygın olarak görülen sorunlara yönelik çözümler özetlenmektedir.

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC izinleri sorunları

Azure rol tabanlı erişim denetimi [Azure RBAC](../../role-based-access-control/overview.md) , _rol_ olarak izin kümelerini birleştirerek Azure kaynakları için yüksek düzeyde ayrıntılı erişim yönetimine izin verebilir. Azure RBAC çalışma Depolama Gezgini en iyi şekilde yararlanmak için bazı stratejiler aşağıda verilmiştir.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Nasıl yaparım? Depolama Gezgini kaynaklarıma eriş mi?

Azure RBAC aracılığıyla depolama kaynaklarına erişirken sorun yaşıyorsanız, uygun rollere atanmamıştır. Aşağıdaki bölümlerde, şu anda depolama kaynaklarınıza erişim için gereken Depolama Gezgini izinleri açıklanır. Uygun rollere veya izinlere sahip olduğunuzdan emin değilseniz Azure hesap yöneticinize başvurun.

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

Azure rolleri, size yönetim veya veri katmanı erişimi için izin verebilir. Örneğin, okuyucu rolü, yönetim katmanı kaynaklarına salt okuma erişimi verir.

Tamamen konuşuyor, okuyucu rolü veri katmanı izinleri sağlamaz ve veri katmanına erişmek için gerekli değildir.

Depolama Gezgini, Azure kaynaklarınıza bağlanmak için gerekli bilgileri toplayıp kaynaklarınıza erişmeyi kolaylaştırır. Örneğin, blob Kapsayıcılarınızı göstermek için Depolama Gezgini blob hizmeti uç noktasına bir "liste kapsayıcıları" isteği gönderir. Bu uç noktayı almak için Depolama Gezgini, erişiminiz olan aboneliklerin ve depolama hesaplarının listesini arar. Aboneliklerinizi ve depolama hesaplarınızı bulmak için Depolama Gezgini yönetim katmanına erişime de ihtiyaç duyuyor.

Herhangi bir yönetim katmanı izni veren bir rolünüz yoksa Depolama Gezgini veri katmanına bağlanması için gereken bilgileri alamaz.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Yöneticimde ihtiyacım olan yönetim katmanı izinlerini alamazsanız ne yapmalıyım?

Blob kapsayıcılarına, ADLS 2. kapsayıcılara veya dizinlere veya kuyruklara erişmek istiyorsanız, Azure kimlik bilgilerinizi kullanarak bu kaynaklara iliştirebilirsiniz.

1. Bağlan iletişim kutusunu açın.
1. Bağlanmak istediğiniz kaynak türünü seçin.
1. **Azure Active Directory (Azure AD) kullanarak oturum aç '** ı seçin. **İleri**’yi seçin.
1. İliştirmekte olduğunuz kaynakla ilişkili kullanıcı hesabı ve kiracıyı seçin. **İleri**’yi seçin.
1. Kaynağın URL 'sini girin ve bağlantı için benzersiz bir görünen ad girin. **İleri** ' yi ve ardından **Bağlan**' ı seçin

Diğer kaynak türleri için şu anda Azure RBAC ile ilgili bir çözümünüz yoktur. Geçici bir çözüm olarak, aşağıdaki adımları izleyerek bir SAS URL 'SI isteyebilir ve sonra kaynağına iliştirebilirsiniz:

1. Bağlan iletişim kutusunu açın.
1. Bağlanmak istediğiniz kaynak türünü seçin.
1. **Paylaşılan erişim imzasını (SAS)** seçin. **İleri**’yi seçin.
1. Aldığınız SAS URL 'sini girin ve bağlantı için benzersiz bir görünen ad girin. **İleri** ' yi ve ardından **Bağlan**' ı seçin
 
Kaynaklara ekleme hakkında daha fazla bilgi için bkz. [tek bir kaynağa ekleme](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Önerilen Azure yerleşik rolleri

Depolama Gezgini kullanmak için gereken izinleri sağlayabilecek çeşitli Azure yerleşik rolleri vardır. Bu rollerden bazıları şunlardır:
- [Sahip](../../role-based-access-control/built-in-roles.md#owner): kaynaklara erişim dahil olmak üzere her şeyi yönetin.
- [Katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor): kaynaklara erişimi hariç her şeyi yönetin.
- [Okuyucu](../../role-based-access-control/built-in-roles.md#reader): kaynakları okuyun ve listeleyin.
- [Depolama hesabı katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor): depolama hesaplarının tam yönetimi.
- [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure depolama blob kapsayıcılarına ve verilerine tam erişim.
- [Depolama Blobu verileri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Azure depolama kapsayıcıları ve bloblarını okuma, yazma ve silme.
- [Depolama Blobu veri okuyucusu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Azure depolama kapsayıcıları ve bloblarını okuyun ve listeleyin.

> [!NOTE]
> Sahip, katkıda bulunan ve depolama hesabı katılımcısı rolleri hesap anahtarı erişimi verir.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Hata: sertifika zincirinde otomatik olarak imzalanan sertifika (ve benzer hatalar)

Sertifika hataları genellikle aşağıdaki durumlardan birinde oluşur:

- Uygulama, _saydam bir ara sunucu_ aracılığıyla bağlandı. Bu, bir sunucunun (örneğin, şirket sunucunuz) HTTPS trafiğini kesintiye uğradığını, şifresini çözmesini ve kendinden imzalı bir sertifika kullanarak şifrelediğinden bu anlamına gelir.
- Aldığınız HTTPS iletilerine otomatik olarak imzalanan bir TLS/SSL sertifikası ekleme bir uygulama çalıştırıyorsunuz. Sertifika ekleme uygulamaları, virüsten koruma ve ağ trafiği İnceleme yazılımlarını içerir.

Depolama Gezgini kendinden imzalı veya güvenilmeyen bir sertifikayı gördüğünde, alınan HTTPS iletisinin değiştirilip değiştirilmediğini artık bilmez. Otomatik olarak imzalanan sertifikanın bir kopyasına sahipseniz, aşağıdaki adımları izleyerek Depolama Gezgini güveneceği konusunda talimat verebilirsiniz:

1. Sertifikanın Base-64 kodlamalı bir X. 509.440 (. cer) kopyasını edinin.
2.   >  **SSL sertifikalarını** Düzenle  >  **sertifikaları içeri aktar**' a gidin ve ardından dosya seçicisini kullanarak. cer dosyasını bulun, seçin ve açın.

Bu sorun, birden fazla sertifika (kök ve ara) varsa da oluşabilir. Bu hatayı onarmak için her iki sertifikanın de eklenmesi gerekir.

Sertifikanın nereden geldiği konusunda emin değilseniz, bulmak için aşağıdaki adımları izleyin:

1. OpenSSL 'yi yükler.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): tüm hafif sürümler yeterli olmalıdır.
    * Mac ve Linux: işletim sisteminize eklenmelidir.
2. OpenSSL 'yi çalıştırın.
    * Windows: yükleme dizinini açın, **/bin/** seçin ve ardından **openssl.exe** öğesine çift tıklayın.
    * Mac ve Linux: `openssl` terminalden çalıştırın.
3. `s_client -showcerts -connect microsoft.com:443` öğesini çalıştırın.
4. Otomatik olarak imzalanan sertifikaları bulun. Hangi sertifikaların kendinden imzalandığına ilişkin emin değilseniz, konunun ve verenin aynı olduğu her yerde dikkat edin `("s:")` `("i:")` .
5. Her biri için otomatik olarak imzalanan sertifikalar bulduğunuzda, (ve dahil) her şeyi `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` Yeni bir. cer dosyasına kopyalayıp yapıştırın.
6. Depolama Gezgini açın ve   >  **SSL sertifikalarını** düzenlemek için  >  **sertifikaları içeri aktarın**' a gidin. Ardından, oluşturduğunuz. cer dosyalarını bulmak, seçmek ve açmak için dosya seçiciyi kullanın.

Bu adımları izleyerek kendinden imzalı bir sertifika bulamıyorsanız, geri bildirim aracı aracılığıyla bizimle iletişim kurun. Bayrağını kullanarak komut satırından Depolama Gezgini de açabilirsiniz `--ignore-certificate-errors` . Bu bayrağıyla açıldığında, Depolama Gezgini sertifika hatalarını yoksayar.

## <a name="sign-in-issues"></a>Oturum açma sorunları

### <a name="understanding-sign-in"></a>Oturum açma 'yı anlama

[Depolama Gezgini Için oturum açma](./storage-explorer-sign-in.md) belgesini okuduğunuzdan emin olun.

### <a name="frequently-having-to-reenter-credentials"></a>Sık kullanılan kimlik bilgilerini yeniden girin

Kimlik bilgilerinin yeniden girmesi, büyük olasılıkla AAD yöneticiniz tarafından ayarlanan koşullu erişim ilkelerinin sonucudur. Depolama Gezgini hesap panelinden kimlik bilgilerini yeniden girmeniz istediğinde, bir **hata ayrıntıları görürsünüz...** bağlantısı. Depolama Gezgini neden kimlik bilgilerini yeniden girmeniz gerektiğini görmek için burayı tıklatın. Kimlik bilgilerinin yeniden girilmesi gereken koşullu erişim ilkesi hataları aşağıdakine benzer olabilir:
- Yenileme belirtecinin süresi doldu...
- Erişmek için çok faktörlü kimlik doğrulaması kullanmanız gerekir...
- Yöneticiniz tarafından yapılan bir yapılandırma değişikliği nedeniyle...

Yukarıdaki gibi hatalar nedeniyle kimlik bilgilerini yeniden girmeniz gereken sıklığı azaltmak için AAD yöneticinizle konuşmanız gerekecektir.

### <a name="conditional-access-policies"></a>Koşullu erişim ilkeleri

Hesabınız için karşılanması gereken koşullu erişim ilkelerinize sahipseniz, **oturum açma** ayarı Için **varsayılan Web tarayıcısı** değerini kullandığınızdan emin olun. Bu ayar hakkında daha fazla bilgi için bkz. [oturum açma nerede olacağını değiştirme](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Belirteç alınamıyor, kiracı filtrelendi

Bir kiracının filtrelenmediği için bir belirtecin alınamadığından bir hata iletisi görürseniz, bu, filtrelenmemiş bir kiracıda olan bir kaynağa erişmeye çalıştığınız anlamına gelir. Kiracının filtreleneceğini kaldırmak için, **hesap paneline** gidin ve hatada belirtilen kiracı onay kutusunun işaretli olduğundan emin olun. Depolama Gezgini kiracıların filtrelenmesi hakkında daha fazla bilgi için [hesapları yönetme](./storage-explorer-sign-in.md#managing-accounts) bölümüne bakın.

## <a name="authentication-library-failed-to-start-properly"></a>Kimlik doğrulama kitaplığı düzgün başlatılamadı

Başlangıçta, Depolama Gezgini kimlik doğrulama kitaplığının düzgün başlayamadığını belirten bir hata iletisi görürsünüz ve sonra, Install ortamınızın tüm [önkoşulları](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites)karşıladığından emin olun. Önkoşulları, bu hata iletisinin en olası nedenidir.

Install ortamınızın tüm önkoşulları karşıladığını düşünüyorsanız, [GitHub 'da bir sorun açın](https://github.com/Microsoft/AzureStorageExplorer/issues/new). Sorununuzu açtığınızda şunları eklediğinizden emin olun:
- İşletim sistemi.
- Kullanmaya çalıştığınız Depolama Gezgini sürümü.
- Önkoşulları denetlediyseniz.
- Depolama Gezgini başarısız bir başlatma işleminden gelen [kimlik doğrulama günlükleri](#authentication-logs) . Ayrıntılı kimlik doğrulama günlüğü, bu tür bir hata oluştuktan sonra otomatik olarak etkinleştirilir.

### <a name="blank-window-when-using-integrated-sign-in"></a>Tümleşik oturum açma kullanılırken boş pencere

**Tümleşik oturum açma** kullanmayı seçtiyseniz ve boş bir oturum açma penceresi görüyorsanız, muhtemelen farklı bir oturum açma yöntemine geçmeniz gerekir. Yalnızca bir Active Directory Federasyon Hizmetleri (AD FS) (ADFS) sunucusu, elektron tarafından desteklenmeyen bir yeniden yönlendirme gerçekleştirmek için Depolama Gezgini istem yaparken boş oturum açma iletişim kutuları genellikle meydana gelir.

Farklı bir oturum açma yöntemine geçiş yapmak için, **Ayarlar** uygulama oturum açma altındaki ayarı **ile oturum açma** ayarını değiştirin  >    >  . Farklı oturum açma yöntemlerinin türleri hakkında daha fazla bilgi için bkz. [oturum açma işleminin nerede olacağını değiştirme](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="reauthentication-loop-or-upn-change"></a>Yeniden kimlik doğrulama döngüsü veya UPN değişikliği

Bir yeniden kimlik doğrulaması döngüsünde veya hesaplarınızdan birinin UPN 'sini değiştirdiyseniz, şu adımları deneyin:

1. Depolama Gezgini açın
2. Yardım > sıfırlamaya git
3. En az kimlik doğrulamanın denetlendiğinden emin olun. Sıfırlamak istemediğiniz diğer öğelerin işaretini kaldırabilirsiniz.
4. Sıfırla düğmesine tıklayın
5. Depolama Gezgini yeniden başlatın ve yeniden oturum açmayı deneyin.

Bir sıfırlama gerçekleştirdikten sonra sorun yaşamaya devam ederseniz şu adımları deneyin:

1. Depolama Gezgini açın
2. Tüm hesapları kaldırın ve ardından Depolama Gezgini kapatın.
3. `.IdentityService`Klasörü makinenizden silin. Windows üzerinde, klasörü konumunda bulunur `C:\users\<username>\AppData\Local` . Mac ve Linux için, klasörü Kullanıcı dizininizin kökünde bulabilirsiniz.
4. Mac veya Linux çalıştırıyorsanız, işletim sisteminizin keystore ' dan Microsoft. Developer. IdentityService girişini de silmeniz gerekir. Mac üzerinde, anahtar deposu *GNOME Anahtarlık* uygulamasıdır. Linux 'ta, uygulama genellikle _kimlik anahtarlığı_ olarak adlandırılır ancak ad, dağıtıma bağlı olarak farklılık gösterebilir.
6. Depolama Gezgini yeniden başlatın ve yeniden oturum açmayı deneyin.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: Anahtarlık hataları veya oturum açma penceresi yok

MacOS anahtarlığı bazen Depolama Gezgini kimlik doğrulama kitaplığı için soruna neden olan bir durum girebilir. Anahtarlığı bu durumdan dışarı almak için aşağıdaki adımları izleyin:

1. Depolama Gezgini kapatın.
2. Anahtarlık açın (komut + ara çubuğuna basın, **Anahtarlık** yazın ve ENTER tuşuna basın).
3. "Oturum açma" Anahtarışını seçin.
4. Anahtarlık kilidini kilitlemek için asma kilit simgesini seçin. (İşlem tamamlandığında, asma kilidi kilitli olarak görünür. Bu işlem, açtığınız uygulamalara bağlı olarak birkaç saniye sürebilir).

    ![Asma kilit simgesi](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Depolama Gezgini'ni açın.
6. "Hizmet hub 'ı anahtarlığa erişmek istiyor." gibi bir iletiyle karşılaşıyoruz. Mac yönetici hesabı parolanızı girip **her zaman Izin ver** ' i seçin (veya **her zaman izin** ver ' **i seçerseniz)** .
7. Oturum açmayı deneyin.

### <a name="default-browser-doesnt-open"></a>Varsayılan tarayıcı açık değil

Oturum açmaya çalışırken varsayılan tarayıcınız açılmadığından aşağıdaki tekniklerin hepsini deneyin:
- Depolama Gezgini yeniden Başlat
- Oturum açmayı başlatmadan önce tarayıcınızı el ile açın
- **Tümleşik oturum açma** kullanmayı deneyin, bunun nasıl yapılacağı hakkında yönergeler için [oturum açma nerede olduğunu değiştirme](./storage-explorer-sign-in.md#changing-where-sign-in-happens) konusuna bakın.

### <a name="other-sign-in-issues"></a>Diğer oturum açma sorunları

Yukarıdaki hiçbiri, oturum açma sorununuz için geçerlidir veya oturum açma sorununuzu çözemezse [GitHub 'da bir sorun açın](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Eksik abonelikler ve bozuk kiracılar

Başarıyla oturum açtıktan sonra aboneliklerinizi alamadıysanız aşağıdaki sorun giderme yöntemlerini deneyin:

* Hesabınızın, bekleyen aboneliklere erişimi olduğunu doğrulayın. Kullanmaya çalıştığınız Azure ortamı için portalda oturum açarak erişiminizi doğrulayabilirsiniz.
* Doğru Azure ortamında (Azure, Azure Çin 21Vianet, Azure Almanya, Azure ABD kamu veya özel ortam) oturum açtığınızdan emin olun.
* Bir ara sunucunun arkasındaysanız, Depolama Gezgini proxy 'yi doğru yapılandırdığınızdan emin olun.
* Hesabı kaldırıp yeniden eklemeyi deneyin.
* "Daha fazla bilgi" veya "hata ayrıntıları" bağlantısı varsa, başarısız olan kiracılar için hangi hata iletilerinin raporlanmakta olduğunu kontrol edin. Hata iletilerine yanıt verme konusunda emin değilseniz, [GitHub 'da bir sorun açmayı](https://github.com/Microsoft/AzureStorageExplorer/issues)ücretsiz olarak kullanabilirsiniz.

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Bağlı bir depolama hesabı veya kaynağı kaldırılamıyor

Kullanıcı arabirimi aracılığıyla ekli bir hesabı veya depolama kaynağını kaldıramıyorum, aşağıdaki klasörleri silerek tüm bağlı kaynakları el ile silebilirsiniz:

* Windows: `%AppData%/StorageExplorer`
* MacOS `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Bu klasörleri silmeden önce Depolama Gezgini kapatın.

> [!NOTE]
> Herhangi bir SSL sertifikası aldıysanız, dizinin içeriğini yedekleyin `certs` . Daha sonra, SSL sertifikalarınızı yeniden içeri aktarmak için yedekleme 'yi kullanabilirsiniz.

## <a name="proxy-issues"></a>Proxy sorunları

Depolama Gezgini, Azure depolama kaynaklarına bir proxy sunucu aracılığıyla bağlanmayı destekler. Ara sunucu aracılığıyla Azure 'a bağlantı sorunlarıyla karşılaşırsanız, bazı öneriler aşağıda verilmiştir.

> [!NOTE]
> Depolama Gezgini yalnızca Proxy sunucularıyla temel kimlik doğrulamasını destekler. NTLM gibi diğer kimlik doğrulama yöntemleri desteklenmez.

> [!NOTE]
> Depolama Gezgini, proxy ayarlarını yapılandırmak için proxy otomatik yapılandırma dosyalarını desteklemez.

### <a name="verify-storage-explorer-proxy-settings"></a>Depolama Gezgini proxy ayarlarını doğrulayın

**Application → Proxy → proxy yapılandırma** ayarı, proxy yapılandırmasını hangi kaynak Depolama Gezgini alır belirler.

"Ortam değişkenlerini kullan" seçeneğini belirlerseniz, veya ortam değişkenlerini ayarladığınızdan emin olun `HTTPS_PROXY` `HTTP_PROXY` (ortam değişkenleri büyük/küçük harfe duyarlıdır, bu nedenle doğru değişkenleri ayarladığınızdan emin olun). Bu değişkenler tanımsız veya geçersiz ise Depolama Gezgini proxy kullanmaz. Herhangi bir ortam değişkenini değiştirdikten sonra Depolama Gezgini yeniden başlatın.

"Uygulama proxy 'si ayarlarını kullan" seçeneğini belirlerseniz, uygulama içi proxy ayarlarının doğru olduğundan emin olun.

### <a name="steps-for-diagnosing-issues"></a>Sorunları tanılamaya yönelik adımlar

Sorun yaşamaya devam ediyorsanız, şu sorun giderme yöntemlerini deneyin:

1. Proxy 'nizi kullanmadan internet 'e bağlanabildiğinizi, Depolama Gezgini proxy ayarları etkin olmadan çalıştığını doğrulayın. Depolama Gezgini başarıyla bağlanırsa, ara sunucu ile ilgili bir sorun olabilir. Sorunları belirlemek için yöneticinizle birlikte çalışın.
2. Proxy sunucusunu kullanan diğer uygulamaların beklendiği gibi çalıştığını doğrulayın.
3. Kullanmaya çalıştığınız Azure ortamı için portala bağlanabildiğinizi doğrulayın.
4. Hizmet uç noktaınızdan yanıt alabildiğinizi doğrulayın. Tarayıcınızın uç nokta URL 'Lerinden birini girin. Bağlantı kurmak için bir `InvalidQueryParameterValue` veya benzer BIR XML yanıtı almanız gerekir.
5. Aynı proxy sunucusuyla Depolama Gezgini başka birisinin bağlanıp bağlanamayacağını denetleyin. Mümkünse, proxy sunucu yöneticinize başvurmanız gerekebilir.

### <a name="tools-for-diagnosing-issues"></a>Sorunları tanılamaya yönelik araçlar

Fiddler gibi bir ağ aracı, sorunları tanılamanıza yardımcı olabilir.

1. Ağ aracınızı yerel konakta çalışan bir ara sunucu olarak yapılandırın. Gerçek bir proxy 'nin arkasında çalışmaya devam etmeniz gerekirse, ağ aracınızı proxy üzerinden bağlanacak şekilde yapılandırmanız gerekebilir.
2. Ağ aracınız tarafından kullanılan bağlantı noktası numarasını kontrol edin.
3. Depolama Gezgini proxy ayarlarını yerel ana bilgisayarı ve ağ aracının bağlantı noktası numarasını (örneğin, "localhost: 8888") kullanacak şekilde yapılandırın.
 
Doğru şekilde ayarlandığında, ağ aracınız Depolama Gezgini tarafından yapılan ağ isteklerini yönetim ve hizmet uç noktalarına kaydeder.
 
Ağ aracınız Depolama Gezgini trafiği günlüğe kaydetme gibi görünmezse, aracınızı farklı bir uygulamayla test etmeyi deneyin. Örneğin, bir Web tarayıcısına depolama kaynaklarınızdan biri (gibi) için uç nokta URL 'sini girin `https://contoso.blob.core.windows.net/` ve şuna benzer bir yanıt alırsınız:

  ![Kod örneği](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Yanıt, erişemeseniz de kaynağın mevcut olduğunu önerir.

Ağ aracınız yalnızca diğer uygulamalardan gelen trafiği gösteriyorsa, ara sunucu ayarlarını Depolama Gezgini ayarlamanız gerekebilir. Aksi takdirde, aracınızın ayarlarını ayarlamanız gerekir.

### <a name="contact-proxy-server-admin"></a>Proxy Sunucu Yöneticisi ile iletişim kurun

Proxy ayarlarınız doğruysa, proxy sunucu yöneticinize başvurmanız gerekebilir:

* Proxy 'nizin Azure yönetimine veya kaynak uç noktalarına giden trafiği engellemediğinden emin olun.
* Proxy sunucunuz tarafından kullanılan kimlik doğrulama protokolünü doğrulayın. Depolama Gezgini yalnızca temel kimlik doğrulama protokollerini destekler. Depolama Gezgini, NTLM proxy 'lerini desteklemez.

## <a name="unable-to-retrieve-children-error-message"></a>"Alt öğeler alınamıyor" hata iletisi

Azure 'a bir proxy üzerinden bağlıysanız, proxy ayarlarınızın doğru olduğundan emin olun.

Bir aboneliğin veya hesabın sahibi size bir kaynağa erişim izni verdiyseniz, bu kaynak için okuma veya listeleme izinlerine sahip olduğunuzu doğrulayın.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Bağlantı dizesinin yapılandırma ayarları Tamam

Bu hata iletisini alırsanız, depolama hesabınız için anahtarları almak için gerekli izinlere sahip olmanız mümkün değildir. Bu durumun bu olduğunu onaylamak için portala gidin ve depolama hesabınızı bulun. Bunu, depolama hesabınız için düğüme sağ tıklayıp **portalda aç '** ı seçerek yapabilirsiniz. Ardından, **erişim tuşları** dikey penceresine gidin. Anahtarları görüntüleme izniniz yoksa, "erişiminiz yok" iletisini görürsünüz. Bu sorunu geçici olarak çözmek için, hesap anahtarını başka bir kişiden edinebilir ve ad ve anahtar aracılığıyla iliştirebilir ya da bir kullanıcıdan depolama hesabı için bir SAS isteyebilir ve depolama hesabını eklemek için onu kullanabilirsiniz.

Hesap anahtarlarını görürseniz, sorunu çözmenize yardımcı olması için GitHub 'da bir sorun çözün.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Yeni bağlantı eklenirken hata oluştu: TypeError: tanımsız olan ' version ' özelliği okunamıyor

Özel bir bağlantı eklemeye çalıştığınızda bu hata iletisini alırsanız, yerel kimlik bilgileri Yöneticisi 'nde depolanan bağlantı verileri bozulmuş olabilir. Bu sorunu geçici olarak çözmek için bozuk yerel bağlantılarınızı silmeyi ve sonra yeniden eklemeyi deneyin:

1. Depolama Gezgini başlatın. Menüden, **Yardım**  >  **Geliştirici Araçları** seçeneğine gidin.
2. Açılan pencerede, **uygulama** sekmesinde, **File://**> **yerel depolama** (sol taraf) seçeneğine gidin.
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
4. Geçerli bağlantı adlarınızı kaydettikten sonra, Geliştirici Araçları değerini olarak ayarlayın `[]` .

Bozulan bağlantıları korumak istiyorsanız, bozuk bağlantıları bulmak için aşağıdaki adımları kullanabilirsiniz. Var olan tüm bağlantıları kaybetmezseniz, bu adımları atlayabilir ve bağlantı verilerinizi temizlemek için platforma özgü yönergeleri izleyebilirsiniz.

1. Bir metin düzenleyicisinden her bağlantı adını Geliştirici Araçları yeniden ekleyin ve sonra bağlantının çalışıp çalışmadığını denetleyin.
2. Bir bağlantı doğru çalışıyorsa, bu bozuk değildir ve güvenli bir şekilde bırakabilirsiniz. Bir bağlantı çalışmıyorsa, Geliştirici Araçları değerini kaldırın ve daha sonra tekrar ekleyebilmek için kaydedin.
3. Tüm bağlantılarınızı incelene kadar tekrarlayın.

Tüm bağlantılarınız bittikten sonra, geri eklenmemiş tüm bağlantı adları için, bozulmuş verileri (varsa) temizlemeniz ve Depolama Gezgini içindeki standart adımları kullanarak geri eklemeniz gerekir:

### <a name="windows"></a>[Windows](#tab/Windows)

1. **Başlat** menüsünde **kimlik bilgileri Yöneticisi** ' ni arayın ve açın.
2. **Windows kimlik bilgileri**' ne gidin.
3. **Genel kimlik bilgileri** altında, anahtarına sahip olan girişleri arayın `<connection_type_key>/<corrupted_connection_name>` (örneğin, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Bu girişleri silip bağlantıları yeniden ekleyin.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Spotlight (komut + ara çubuğu) öğesini açın ve **Anahtarlık erişimi** arayın.
2. Anahtarına sahip olan girişleri arayın `<connection_type_key>/<corrupted_connection_name>` (örneğin, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Bu girişleri silip bağlantıları yeniden ekleyin.

### <a name="linux"></a>[Linux](#tab/Linux)

Yerel kimlik bilgisi yönetimi, Linux dağıtımına bağlı olarak farklılık gösterir. Linux yönetiyoruz yerel kimlik bilgileri yönetimi için yerleşik bir GUI aracı sağlamıyorsa, yerel kimlik bilgilerinizi yönetmek için bir üçüncü taraf araç yükleyebilirsiniz. Örneğin, Linux yerel kimlik bilgilerini yönetmek için açık kaynaklı bir GUI aracı olan [Mevsimat](https://wiki.gnome.org/Apps/Seahorse/)'ı kullanabilirsiniz.

1. Yerel kimlik bilgileri yönetim aracınızı açın ve kaydettiğiniz kimlik bilgilerinizi bulun.
2. Anahtarına sahip olan girişleri arayın `<connection_type_key>/<corrupted_connection_name>` (örneğin, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
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
2. **Uygulama** sekmesinde, soldaki ağaçta **yerel depolama**  >  **File://** ' ı seçin.
3. Sorunlu SAS URI 'sinin hizmet türüyle ilişkili anahtarı bulun. Örneğin, hatalı SAS URI 'SI bir blob kapsayıcısı için ise adlı anahtarı bulun `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. Anahtarın değeri bir JSON dizisi olmalıdır. Hatalı URI ile ilişkili nesneyi bulun ve silin.
5. Depolama Gezgini yeniden yüklemek için CTRL + R tuşlarına basın.

## <a name="linux-dependencies"></a>Linux bağımlılıkları

### <a name="snap"></a>Bileşenlerinden

Depolama Gezgini 1.10.0 ve üzeri, Snap mağazasından bir snap olarak sunulmaktadır. Depolama Gezgini Snap, tüm bağımlılıklarını otomatik olarak yüklüyor ve bir ek bileşenin yeni bir sürümü kullanılabilir olduğunda güncellenir. Depolama Gezgini Snap 'in yüklenmesi önerilen yükleme yöntemidir.

Depolama Gezgini, Depolama Gezgini doğru çalışmadan önce el ile bağlanmanız gerekebilecek bir parola yöneticisinin kullanılmasını gerektirir. Aşağıdaki komutu çalıştırarak, Depolama Gezgini sisteminizin parola yöneticisine bağlayabilirsiniz:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>. tar. gz dosyası

Uygulamayı. tar. gz dosyası olarak da indirebilirsiniz, ancak bağımlılıkları el ile yüklemelisiniz.

. Tar. gz download ' de belirtildiği gibi Depolama Gezgini, yalnızca Ubuntu 'ın aşağıdaki sürümleri için desteklenir. Depolama Gezgini, diğer Linux dağıtımları üzerinde çalışabilir, ancak resmi olarak desteklenmez.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Depolama Gezgini sisteminizde .NET Core 'un yüklü olmasını gerektirir. .NET Core 2,1 önerilir, ancak Depolama Gezgini 2,2 ile de çalışacaktır.

> [!NOTE]
> Depolama Gezgini Version 1.7.0 ve önceki sürümleri .NET Core 2,0 gerektirir. .NET Core 'un daha yeni bir sürümü yüklüyse, [Depolama Gezgini yama](#patching-storage-explorer-for-newer-versions-of-net-core)yapmanız gerekir. Depolama Gezgini 1.8.0 veya üzeri bir sürümü çalıştırıyorsanız, en az .NET Core 2,1 gerekir.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Depolama Gezgini. tar. gz dosyasını indirin.
2. [.NET Core çalışma zamanını](/dotnet/core/install/linux)yükler:
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Depolama Gezgini. tar. gz dosyasını indirin.
2. [.NET Core çalışma zamanını](/dotnet/core/install/linux)yükler:
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Depolama Gezgini. tar. gz dosyasını indirin.
2. [.NET Core çalışma zamanını](/dotnet/core/install/linux)yükler:
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Depolama Gezgini tarafından gerek duyulan birçok kitaplık, Ubuntu 'ın standart ve standart yüklemelerine önceden yüklenmiş olarak gelir. Özel ortamlarda bu kitaplıkların bazıları eksik olabilir. Depolama Gezgini başlatma sorunlarınız varsa, aşağıdaki paketlerin sisteminizde yüklü olduğundan emin olmanızı öneririz:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-Utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>.NET Core 'un daha yeni sürümleri için Depolama Gezgini düzeltme eki uygulama

Depolama Gezgini 1.7.0 veya daha önceki bir sürümü için Depolama Gezgini tarafından kullanılan .NET Core sürümüne yama yapmanız gerekebilir:

1. [NuGet 'Den](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)StreamJsonRpc 'nin 1.5.43 sürümünü indirin. Sayfanın sağ tarafındaki "paketi Indir" bağlantısını bulun.
2. Paketi indirdikten sonra, dosya uzantısını `.nupkg` olarak değiştirin `.zip` .
3. Paketi sıkıştırmayı açın.
4. Klasörünü açın `streamjsonrpc.1.5.43/lib/netstandard1.1/` .
5. `StreamJsonRpc.dll`Depolama Gezgini klasöründe aşağıdaki konumlara kopyalayın:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure portal işe yaramazsa "Gezgin 'de aç"

Azure portal **Explorer 'Da aç** düğmesi işe yaramazsa, uyumlu bir tarayıcı kullandığınızdan emin olun. Aşağıdaki tarayıcılar uyumluluk için sınanmıştır:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Günlükler toplanıyor

GitHub 'a bir sorun raporlarken, sorununuzu tanılamanıza yardımcı olması için belirli günlükleri toplamanız istenebilir.

### <a name="storage-explorer-logs"></a>Depolama Gezgini günlükleri

Sürüm 1.16.0 ile başlayarak, Depolama Gezgini kendi uygulama günlüklerinde çeşitli şeyleri günlüğe kaydeder. Günlük dizinini aç > Yardım ' a tıklayarak bu günlüklere kolayca erişebilirsiniz. Varsayılan olarak, günlükleri düşük düzeyde ayrıntı düzeyinde Depolama Gezgini. Ayrıntı düzeyini değiştirmek için, adı `STG_EX_LOG_LEVEL` ve aşağıdaki değerlerden herhangi birine sahip bir ortam değişkeni ekleyin:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (varsayılan düzey)
- `verbose`
- `debug`

Günlükler, çalıştırdığınız Depolama Gezgini her oturumunda klasörlere ayrılır. Paylaşmanız gereken günlük dosyaları için, farklı klasörlerdeki farklı oturumlardaki dosyalarla birlikte bunları bir zip arşivine yerleştirmeniz önerilir.

### <a name="authentication-logs"></a>Kimlik doğrulama günlükleri

Oturum açma veya Depolama Gezgini kimlik doğrulama kitaplığıyla ilgili sorunlar için, büyük olasılıkla kimlik doğrulama günlüklerini toplamanız gerekir. Kimlik doğrulama günlükleri şurada depolanır:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS ve Linux `~/.ServiceHub/logs`

Genellikle, günlükleri toplamak için aşağıdaki adımları izleyebilirsiniz:

1. Ayarlar > oturum açma > ayrıntılı kimlik doğrulaması günlüğü ' ne bakın. Kimlik doğrulama kitaplığındaki bir sorun nedeniyle Depolama Gezgini başlatılamadı. Bu işlem sizin için yapılır.
2. Depolama Gezgini kapatın.
1. İsteğe bağlı/önerilen: klasördeki mevcut günlükleri temizleyin `logs` . Bunu yapmak, bize göndermek için sahip olduğunuz bilgi miktarını azaltır.
4. Depolama Gezgini açın ve sorununuzu yeniden oluşturun
5. Depolama Gezgini kapat
6. Klasör içeriğini ZIP `log` .

### <a name="azcopy-logs"></a>AzCopy günlükleri

Veri aktarırken sorun yaşıyorsanız AzCopy günlüklerini almanız gerekebilir. AzCopy günlükleri, iki farklı yöntem aracılığıyla kolayca bulunabilir:
- Hala etkinlik günlüğünde başarısız olan aktarımlar için, "AzCopy günlük dosyasına git" seçeneğine tıklayın.
- Geçmişte başarısız olan aktarımlar için AzCopy logs klasörüne gidin. Bu klasör şurada bulunabilir:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS ve Linux ' ~/.exe AzCopy

### <a name="network-logs"></a>Ağ günlükleri

Bazı sorunlar için, Depolama Gezgini tarafından yapılan ağ çağrılarının günlüklerini sağlamanız gerekir. Windows 'ta, Fiddler kullanarak bunu yapabilirsiniz.

> [!NOTE]
> Fiddler izlemeleri, izlemenin toplanması sırasında tarayıcınızda girdiğiniz/gönderdiğiniz parolaları içerebilir. Fiddler izlemesini Temizleme hakkındaki yönergeleri okuduğunuzdan emin olun. Fiddler izlemelerini GitHub 'a yüklemeyin. Fiddler izinizi güvenli bir şekilde gönderebileceğiniz bildirilir.

1. kısım: Fiddler 'i yükleyip yapılandırma

1. Fiddler 'ı yükler
2. Fiddler 'ı Başlat
3. Araçlar > seçeneklere git
4. HTTPS sekmesine tıklayın
5. Yakalama bağlantılarının ve şifresinin çözülmesi için HTTPS trafiğinin işaretli olduğundan emin olun
6. Eylemler düğmesine tıklayın
7. İleri iletişim kutusunda "kök sertifikaya güven" ve ardından "Evet" seçeneğini belirleyin
8. Eylemler düğmesine yeniden tıklayın
9. "Kök sertifikayı masaüstüne dışarı aktar" seçeneğini belirleyin
10. Masaüstünüze gidin
11. FiddlerRoot. cer dosyasını bulma
12. Açmak için çift tıklayın
13. "Ayrıntılar" sekmesine gidin
14. "Dosyaya Kopyala..." seçeneğine tıklayın
15. Dışarı aktarma sihirbazında aşağıdaki seçenekleri belirleyin
    - Base-64 kodlamalı X. 509.440
    - Dosya adı için, bkz... C:\Users \<your user dir> \Appdata\roaming\storageexplorer\cert için
16. Sertifika penceresini kapat
17. Depolama Gezgini Başlat
18. > Düzenle ' ye git proxy yapılandırma
19. İletişim kutusunda "uygulama proxy 'si ayarlarını kullan" ı seçin ve URL 'yi http://localhost ve bağlantı noktasını 8888 olarak ayarlayın
20. Tamam 'A tıklayın
21. Depolama Gezgini yeniden Başlat
22. Bir işlemden gelen ağ çağrılarını `storageexplorer:` , Fiddler 'da göster ' i görmeye başlamanız gerekir

2. Bölüm: sorunu yeniden oluşturma
1. Fiddler dışındaki tüm uygulamaları kapatın
2. Fiddler günlüğünü (sol üstteki, Görünüm menüsünün yakınında bulunan X simgesine) temizleyin
3. İsteğe bağlı/önerilen: Fiddler 'in birkaç dakika için ayarlanmış olmasına izin ver ' i görürseniz, ağ aramaları görünürse, üzerine sağ tıklayın ve ' Şimdi filtrele ' seçeneğini belirleyin > ' gizle <process name> '
4. Depolama Gezgini Başlat
5. Sorunu yeniden oluşturun
6. Dosya > tüm oturumları > Kaydet... ' e tıklayın, herhangi bir yere kaydedin ve unutmayın
7. Fiddler ve Depolama Gezgini kapatma

3. kısım: Fiddler izlemesini Temizleme
1. Fiddler trace (. saz dosyasına) öğesine çift tıklayın
2. Bas `ctrl`+`f`
3. Görüntülenen iletişim kutusunda, aşağıdaki seçeneklerin ayarlanmış olduğundan emin olun: arama = Istekler ve yanıtlar, Inceleme = üstbilgiler ve gövdeler
4. Fiddler izlemesini toplarken kullandığınız herhangi bir parolayı arayın, vurgulanan tüm girişler, sağ tıklayın ve > seçili oturumları Kaldır ' ı seçin.
5. İzlemeyi toplarken, ancak Ctrl + f kullanırken bir girdi bulamazsanız ve parolalarınızı/kullandığınız parolalara başka hesaplar için kullanıldığından, bu durumda yalnızca bize. saz dosyası göndermeyi atlayabilirsiniz... Ne kadar güvenli olacak. :)
6. İzlemeyi yeni bir adla yeniden Kaydet
7. İsteğe bağlı: özgün izlemeyi Sil

## <a name="next-steps"></a>Sonraki adımlar

Bu çözümlerin hiçbiri sizin için işe çalışmadıysanız şunları yapabilirsiniz:
- Destek bileti oluşturma
- [GitHub 'da bir sorun açın](https://github.com/Microsoft/AzureStorageExplorer/issues). Bunu, sol alt köşedeki **GitHub 'a sorun raporla** düğmesine seçerek de yapabilirsiniz.

![Geri Bildirim](./media/storage-explorer-troubleshooting/feedback-button.PNG)