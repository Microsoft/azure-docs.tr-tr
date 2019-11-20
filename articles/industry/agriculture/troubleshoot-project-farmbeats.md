---
title: Sorun giderme
description: Azure Farmtts sorunlarını giderme.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ad1cb3b08f92923ef45b48d79ad8bbdc3277d370
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131966"
---
# <a name="troubleshooting"></a>Sorun giderme

Aşağıdaki bölümlerde, yaygın Azure Farmants sorunları ve bunların nasıl düzeltileceğini açıklamaktadır.

Daha fazla yardım için, bu e-postada dağıtıcı. log dosyasını farmbeatssupport@microsoft.comadresinden bize yazın.

 Deployer. log dosyasını indirmek için bu adımları kullanın:

1. Vurgulanan simge ve açılan listeden **İndir** seçeneğini belirleyin.

    ![Proje grubu ları](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Sonraki ekranda, Deployer. log dosyanızın yolunu girin. Örneğin, farmbeats-Deployer. log.

## <a name="sensor-telemetry"></a>Algılayıcı telemetrisi

### <a name="telemetry-not-seen"></a>Telemetri görülmedi

**Belirti**: cihazlar/sensörler dağıtılır ve cihaz iş ortağınızla birlikte Farmtörler bağlandı; Ancak, Farmtempts üzerinde telemetri verilerini alamıyor/görüntüleyemeyeceksiniz.

**Düzeltici eylem**: Azure Portal ziyaret edin ve şu adımları izleyin:

1. Farmtts veri merkezi kaynak grubuna gidin.   
2. **Olay Hub 'ını** seçin (datafeedeventhubnamespace....)  ve gelen Ileti sayısını kontrol edin.   
3. **Gelen Ileti olmaması**durumunda cihaz iş ortağınızla iletişim kurun.  
4. **Gelen iletiler**söz konusu olduğunda, veri merkezi ve Hızlandırıcı günlükleri ve yakalanan telemetri ile farmbeatssupport@microsoft.com başvurun.

Günlüklerin nasıl indirileceği anlamak için belgenin [Günlükler bölümüne](#collect-logs-manually) bakın.    

### <a name="device-appears-offline"></a>Cihaz çevrimdışı görünüyor

**Belirtiler**: cihazlar yüklenir ve cihaz iş ortağınızla birlikte Farmtempts 'yi bağladınız. Cihazlar çevrimiçi ve telemetri verileri gönderiyor, ancak çevrimdışı görünüyor.

**Düzeltici eylem**: Bu cihaz için Raporlama aralığı yapılandırılmadı. Raporlama aralığını ayarlamak için cihaz üreticinize başvurun. 

### <a name="error-deleting-a-resource"></a>Kaynak silme hatası

Bir cihaz silinirken yaygın olarak karşılaşılan hata senaryolarından bazıları şunlardır:  

**İleti**: sensörde cihaza başvuruluyor: cihazla ilişkili bir veya daha fazla algılayıcı var. Algılayıcıları silin ve ardından cihazı silin.  

**Anlamı**: cihaz, grupta dağıtılan birden çok sensörle ilişkilendirilir.   

**Düzeltici eylem**:  

1. Hızlandırıcı aracılığıyla cihazla ilişkili Sensörlerinizi silin.  
2. Algılayıcıları farklı bir cihazla ilişkilendirmek isterseniz, cihaz iş ortağınızdan aynı şekilde çalışmasını isteyin.  
3. Zorla parametresini ' true ' olarak ayarlayarak, bir DELETE API çağrısını kullanarak cihazı silin.  

**İleti**: cihaz cihazlarda parentdeviceıd olarak başvuruluyor: alt cihazlar olarak bu cihazla ilişkili bir veya daha fazla cihaz var. Bunları silin ve ardından bu cihazı silin.  

**Anlamı**: cihazınız ile ilişkili başka cihazlar vardır  

**Düzeltici eylem**

1. Belirli cihazla ilişkili cihazları Sil  
2. Belirli bir cihazı Sil  

    > [!NOTE]
    > Sensörlerle ilişkili olan bir cihazı silemezsiniz. İlişkili algılayıcıların nasıl silineceği hakkında daha fazla bilgi için bkz. algılayıcı verilerini alma bölümünde [sensörleri silme](get-sensor-data-from-sensor-partner.md) bölümü.


## <a name="issues-with-jobs"></a>İşlerle ilgili sorunlar

### <a name="farmbeats-internal-error"></a>Farmtts iç hatası

**İleti**: farmtts iç hatası, daha fazla ayrıntı için bkz. sorun giderme kılavuzu.

**Düzeltici eylem**: bunun nedeni, veri ardışık düzeninde geçici bir hata olabilir. İşi bir kez daha oluşturun. Hata devam ederse, bu hatayı Farmtts forumundaki bir gönderiye ekleyin veya FarmBeatsSupport@microsoft.combaşvurun.

## <a name="accelerator-troubleshooting"></a>Hızlandırıcı sorunlarını giderme

### <a name="access-control"></a>Erişim Denetimi

**Rol ataması eklenirken hata oluştu**

**İleti**: eşleşen kullanıcı bulunamadı

**Düzeltici eylem**: bir rol ataması yapmaya çalıştığınız e-posta kimliğini denetleyin. E-posta KIMLIĞI, Active Directory bu kullanıcı için kayıtlı bir tam eşleşme olmalıdır. Hata devam ederse, bu hatayı Farmtts forumundaki bir gönderiye veya ilgili kişiye ekleyin FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Hızlandırıcıda oturum açılamıyor

**İleti**: hata: hizmeti çağırma yetkiniz yok. Yetkilendirme için yöneticiye başvurun.

**Düzeltici eylem**: yöneticinin Farmtts dağıtımına erişim yetkisini vermesini isteyin. Bu işlem, Roleatama API 'lerinin bir SONRASı veya hızlandırıcı 'daki ayarlar bölmesinde Access Control aracılığıyla yapılabilir.  

Zaten eklendiyse ve bu hatayla karşılaşırsanız, sayfayı yenileyerek tekrar deneyin.  Hata devam ederse, bu hatayı Farmtts forumundaki bir gönderiye ekleyin veya FarmBeatsSupport@microsoft.combaşvurun.

![Proje grubu ları](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Hızlandırıcı-bilinmeyen bir hata oluştu  

**İleti**: hata: bilinmeyen bir hata oluştu

**Düzeltme eylemi**: sayfayı çok uzun süre boşta bıraktığınızda bu durum oluşur. Sayfayı yenileyin.  

Hata devam ederse, bu hatayı Farmtts forumundaki bir gönderiye veya ilgili kişiye ekleyin FarmBeatsSupport@microsoft.com

**FarmBeatsDeployment yükseltildikten sonra bile Farmtts Hızlandırıcısı en son sürümü gösterilmiyor**

**Düzeltici eylem**: Bu, tarayıcıda hizmet çalışanı kalıcılığı nedeniyle oluşur.
Hızlandırıcının açık olduğu ve tarayıcı penceresini kapatan tüm tarayıcı sekmelerini kapatın. Tarayıcının yeni bir örneğini başlatın ve Hızlandırıcı URI 'sini yeniden yükleyin. Bu işlem hızlandırıcının yeni sürümünü yükler.

## <a name="sentinel-imagery-related-issues"></a>Sentinel imagery ile ilgili sorunlar

### <a name="sentinel-wrong-username-or-password"></a>Sentinel yanlış Kullanıcı adı veya parola

**İş hata iletisi**; Bu kaynağa erişmek için tam kimlik doğrulaması gerekir.

**Düzeltme eylemi**: doğru Kullanıcı adı ve parolayla veri hub 'ını yükseltmek için yükleyiciyi yeniden çalıştırın.

**Düzeltme eylemi**: 5-7 gün tarih aralığı için başarısız işi yeniden çalıştırın veya bir uydu endekslik işi çalıştırın ve işin başarılı olup olmadığını denetleyin.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>Sentinel hub 'ı yanlış URL veya erişilebilir değil 

**İş hatası iletisi**: bir sorun oluştu. Erişmeye çalıştığınız sayfa (geçici olarak) kullanılamıyor. 

**Düzeltici eylem**:
1.  Sentinel URL 'YI (https://scihub.copernicus.eu/dhus/) tarayıcıda açın ve Web sitesinin erişilebilir olup olmadığını denetleyin. 
2.  Web sitesi erişilebilir değilse, herhangi bir güvenlik duvarı/şirket ağı vb. Web sitesini engelleyip engellemediğini ve URL 'nin üstüne izin vermek için gerekli adımları uygulayın. 
3.  5-7 günlük bir tarih aralığı için başarısız işi yeniden çalıştırın veya bir uydu Endekslik işi çalıştırın ve işin başarılı olup olmadığını denetleyin.  

### <a name="sentinel-server-down-for-maintenance"></a>Bakım için Sentinel sunucu

**İş hatası iletisi**: Copernicus açık erişim merkezi yakında yeniden sunulacaktır! Ne yazık ki bu sırada bazı bakımda bakım yapıyoruz. Kısa süre içinde yeniden çevrimiçi olacak! 

**Düzeltici eylem**:

1.  Bu sorun, Sentinel sunucusunda herhangi bir bakım etkinliği yapıldığında meydana gelebilir. 
2.  Herhangi bir iş/işlem hattı yukarıdaki nedenlerden dolayı başarısız olursa, işi bir süre sonra yeniden gönderin. 
3.  Kullanıcı, planlı/planlanmamış Sentinel bakım etkinlikleriyle ilgili bilgileri denetlemek için https://scihub.copernicus.eu/news/ ziyaret edebilir.  
4.  5-7 günlük bir tarih aralığı için başarısız işi yeniden çalıştırın veya bir uydu Endekslik işi çalıştırın ve işin başarılı olup olmadığını denetleyin.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel en fazla bağlantı sayısına ulaşıldı

**İş hatası iletisi**: "<username>" kullanıcısı tarafından elde edilen iki eşzamanlı akış sayısı üst sınırı 

**Düzeltici eylem**
1.  Yukarıdaki nedenle herhangi bir iş başarısız olursa, başka bir dağıtımda/yazılımda aynı Sentinel hesabı kullanılır. 
2.  Kullanıcı yeni Sentinel hesabı oluşturabilir ve yeni Sentinel Kullanıcı adı ve parolasıyla veri hub 'ını yükseltmek için yükleyiciyi yeniden çalıştırabilir.  
3.  Başarısız işi yeniden çalıştırın veya 5-7 gün tarih aralığı için bir uydu Endekslik işi çalıştırın ve işin başarılı olup olmadığını denetleyin.

### <a name="sentinel-server-refused-connection"></a>Sentinel sunucu bağlantıyı reddetti 

**İş hatası iletisi**:

Sunucu bağlantıyı reddetti: http://172.30.175.69:8983/solr/dhus 

**Düzeltme eylemi**: Sentinel sunucusunda herhangi bir bakım etkinliği yapıldığında bu sorun oluşabilir. 
1.  Herhangi bir iş/işlem hattı yukarıdaki nedenlerden dolayı başarısız olursa, işi bir süre sonra yeniden gönderin. 
2.  Kullanıcı, planlı/planlanmamış Sentinel bakım etkinlikleriyle ilgili bilgileri denetlemek için https://scihub.copernicus.eu/news/ ziyaret edebilir.  
3.  Başarısız işi yeniden çalıştırın veya 5-7 gün tarih aralığı için bir uydu Endekslik işi çalıştırın ve işin başarılı olup olmadığını denetleyin.


## <a name="collect-logs-manually"></a>Günlükleri el ile topla

Azure Depolama Gezgini [yükleyip dağıtın]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) .

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Veri merkezi ADF iş günlüklerini toplama
1. https://portal.azure.com oturum açın
2. **Arama** metin kutusunda, Farmtempts veri merkezi kaynak grubunu arayın.

    > [!NOTE]
    > Farmtts dağıtımı sırasında belirtilen veri merkezi kaynak grubunu seçin.

Kaynak grubu panosunda (datahublogs....) depolama hesabı ' nı arayın. Örneğin, datahublogsmvxmq  

1.  **Depolama hesabı** panosunu görüntülemek için **ad** sütunundaki depolama hesabını seçin.
2.  (Datahubbloglar....) sayfasında, **açık Azure Depolama Gezgini** uygulamasını görüntülemek için **Explorer 'da aç** ' ı seçin.
3.  Sol bölmede (datahubbloglar...), **BLOB kapsayıcıları**' nda **iş günlükleri**' ni seçin.
4.  **İş günlükleri** sekmesinde **İndir**' i seçin.
5.  Günlüklerin makinenizde yerel bir klasöre indirileceği konumu seçin.
6.  İndirilen ZIP dosyasını farmbeatssupport@microsoft.com e-posta ile gönderin

    ![Proje grubu ları](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Hızlandırıcı ADF iş günlüklerini toplama

1.  https://portal.azure.com oturum açın
2.  **Arama** metin kutusunda, Farmtempts Hızlandırıcısı kaynak grubunu arayın.

    > [!NOTE]
    > Farmtts dağıtımı sırasında belirtilen Hızlandırıcı kaynak grubunu seçin.

3.  Kaynak grubu panosunda, depolama için arama yapın... depolama hesabı. Örneğin, storagedop4k
4.  Depolama hesabı panosunu görüntülemek için **ad** sütunundaki depolama hesabını seçin.
5.  (Depolama....) sayfasında, açık Azure Depolama Gezgini uygulamasını görüntülemek için **Gezgin 'de aç** ' ı seçin.
6.  Sol bölmede < Storage....), **BLOB kapsayıcıları**' nda **iş günlükleri**' ni seçin.
7.  **İş günlükleri** sekmesinde **İndir**' i seçin.
8.  Günlüklerin makinenizde yerel bir klasöre indirileceği konumu seçin.
9.  İndirilen ZIP dosyasını farmbeatssupport@microsoft.com e-posta ile gönderin


### <a name="how-to-collect-data-hub-app-service-logs"></a>Veri merkezi App Service günlüklerini toplama

1.  https://portal.azure.com oturum açın
2.  **Arama** metin kutusunda, Farmtempts veri merkezi kaynak grubunu arayın.

    > [!NOTE]
    > Farmtts dağıtımı sırasında belirtilen veri merkezi kaynak grubunu seçin.

3.  Kaynak grubunda (datahublogs....) depolama hesabı ' nı arayın. Örneğin, datahublogsmvxmq
4.  **Depolama hesabı** panosunu görüntülemek için **ad** sütunundaki depolama hesabını seçin.
5.  (Datahubbloglar....) sayfasında, **açık Azure Depolama Gezgini** uygulamasını görüntülemek için **Explorer 'da aç** ' ı seçin.
6.  Sol bölmede (datahubbloglar....), **BLOB kapsayıcıları**' nda appınsights-logs ' u seçin.
7.  Appınsights-logs sekmesinde **İndir**' i seçin.
8.  Günlüklerin makinenizde yerel bir klasöre indirileceği yolu seçin.
9.  İndirilen klasörü farmbeatssupport@microsoft.com e-posta ile gönderin

### <a name="how-to-collect-accelerator-app-service-logs"></a>Hızlandırıcı App Service günlüklerini toplama

1.  https://portal.azure.com oturum açın
2.  **Aramada**, Farmtts Hızlandırıcısı kaynak grubu için arama yapın.

    > [!NOTE]
    > Farmtts dağıtımı sırasında sağlanmış olan Farmtts Hızlandırıcısı kaynak grubunu seçin.

3.  Kaynak grubunda (depolama....) depolama hesabı ' nı arayın. Örneğin, storagedop4k
4.  **Depolama hesabı** panosunu görüntülemek için **ad** sütunundaki depolama hesabını seçin.
5.  (Depolama....) sayfasında, **açık Azure Depolama Gezgini** uygulamasını görüntülemek için **Gezgin 'de aç** ' ı seçin.
6.  Sol bölmede (depolama....), **BLOB kapsayıcıları**' nda appınsights-logs ' u seçin.
7.  Appınsights-logs sekmesinde **İndir**' i seçin.
8.  Günlüklerin makinenizde yerel bir klasöre indirileceği konumu seçin.
9.  İndirilen klasörü farmbeatssupport@microsoft.com e-posta ile gönderin

## <a name="known-issues"></a>Bilinen sorunlar

## <a name="batch-related-issues"></a>Batch ile ilgili sorunlar

**Hata**: belirtilen abonelik Için Batch hesaplarının bölgesel hesap kotasına ulaşıldı.

**Düzeltici eylem**: kotayı artırın veya kullanılmayan Batch hesaplarını silin ve dağıtımı yeniden çalıştırın.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory ilgili sorunlar

**Hata**: gerekli ayarları güncelleştiremedi Azure AD uygulaması d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: işlemi gerçekleştirmek için yeterli ayrıcalıklara sahip değil. Yukarıdaki ayarların Azure AD Uygulaması için düzgün şekilde yapılandırıldığından emin olun.

**Anlamı**: Azure AD uygulama kayıt yapılandırması düzgün şekilde gerçekleşmedi.  

**Düzeltici eylem**: Azure AD uygulama kaydını oluşturmak için [betiğimizi](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) kullanmak üzere BT yöneticisinden (kiracı okuma erişimi olan) sorun. Bu komut dosyası yapılandırma adımlarında da otomatik olarak ele alınacaktır.

**Hata**: yeni Active Directory "Dummyname" uygulaması bu kiracıda oluşturulamıyor: özellik tanımlayıcısı URI 'leri için aynı değere sahip başka bir nesne zaten var

**Anlamı**: aynı ada sahıp Azure AD uygulama kaydı zaten var.

**Düzeltme eylemi**: mevcut Azure AD uygulama kaydını silin veya yükleme için yeniden kullanın. Mevcut Azure AD 'yi yeniden kullanıyorsanız, uygulama KIMLIĞINI ve istemci gizli anahtarını yükleyiciye geçirin ve yeniden dağıtın.

## <a name="inputjson-related-issues"></a>Input. JSON ile ilgili sorunlar

İnput. json dosyasından giriş okunurken **hata oluştu**

**Düzeltici eylem**: Bu sorun, genellikle doğru giriş JSON yolu veya yükleyicisindeki adı belirtirken kaçırılması nedeniyle ortaya çıkar. Uygun düzeltmeleri yapıp yeniden dağıtımı yeniden deneyin.

**JSON girişi ayrıştırılırken hata oluştu**

**Düzeltici eylem**: Bu sorun genellikle giriş JSON dosyası içindeki hatalı değerler nedeniyle ortaya çıkar. Uygun düzeltmeleri yapın ve dağıtımı yeniden deneyin.

## <a name="high-cpu-usage"></a>Yüksek CPU kullanımı

**Hata**: yüksek CPU kullanımı uyarısına başvuran bir e-posta uyarısı alırsınız. 

**Düzeltici eylem**: 
1.  Farmtts veri merkezi kaynak grubuna gidin.
2.  App Service 'i seçin.  
3.  Ölçek yukarı (App Service planı) git ve uygun bir [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/app-service/windows/) seçin

## <a name="next-steps"></a>Sonraki adımlar

Sorun yaşamaya devam ediyorsanız [destek forumumuzdan](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)bizimle iletişime geçin.
