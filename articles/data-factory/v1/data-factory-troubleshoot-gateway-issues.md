---
title: Veri Yönetimi ağ geçidi sorunlarını giderme
description: Veri Yönetimi ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları sağlar.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 34930061189c11c9cea9c2cd0feb2ede9aade74d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682333"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Veri Yönetimi Ağ Geçidi kullanımıyla ilgili sorunları giderme
Bu makalede Veri Yönetimi ağ geçidi kullanmayla ilgili sorunları giderme hakkında bilgi sağlanır.

> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory içinde şirket içinde barındırılan tümleştirme çalışma zamanı](../create-self-hosted-integration-runtime.md).

Ağ Geçidi hakkında ayrıntılı bilgi için [veri yönetimi Gateway](data-factory-data-management-gateway.md) makalesine bakın. Şirket içi bir SQL Server veritabanından ağ geçidini Microsoft Azure Blob depolama alanına taşıma hakkında yönergeler için bkz. Şirket [içi ve bulut aracılığıyla verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="failed-to-install-or-register-gateway"></a>Ağ Geçidi yüklenemedi veya kaydedilemedi
### <a name="1-problem"></a>1. sorun
Ağ geçidini yüklerken ve kaydederken ağ geçidi yükleme dosyasını indirirken bu hata iletisini görürsünüz.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Nedeni
Ağ geçidini yüklemeye çalıştığınız makine, bir ağ sorunu nedeniyle en son ağ geçidi yükleme dosyasını indirme merkezinden indiremedi.

#### <a name="resolution"></a>Çözünürlük
Ayarların bilgisayardan [indirme merkezine](https://download.microsoft.com/)ağ bağlantısını engellemediğinden ve ayarları uygun şekilde güncelleştirip güncelleştirmediğini görmek için güvenlik duvarı ara sunucu ayarlarınızı denetleyin.

Alternatif olarak, indirme merkezi 'ne erişebilen diğer makinelere [indirme merkezinden](https://www.microsoft.com/download/details.aspx?id=39717) en son ağ geçidi için yükleme dosyasını indirebilirsiniz. Ardından, ağ geçidini yüklemek ve güncelleştirmek için ağ geçidi ana bilgisayarına yükleyici dosyasını kopyalayabilir ve el ile çalıştırabilirsiniz.

### <a name="2-problem"></a>2. sorun
Azure portal bir ağ geçidini yüklemeye çalışırken, **Bu bilgisayarda doğrudan bu bilgisayara Install** ' a tıklayarak bu hatayı görürsünüz.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Nedeni
Makinede zaten bir ağ geçidi yüklü.

#### <a name="resolution"></a>Çözünürlük
Makinede var olan ağ geçidini kaldırın ve **doğrudan bu bilgisayara yükleme** bağlantısına tıklayın.

### <a name="3-problem"></a>3. sorun
Yeni bir ağ geçidi kaydederken bu hatayla karşılaşabilirsiniz.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Nedeni
Aşağıdaki nedenlerden biri için bu iletiyi görebilirsiniz:

* Ağ Geçidi anahtarının biçimi geçersiz.
* Ağ Geçidi anahtarı geçersiz kılındı.
* Ağ Geçidi anahtarı portaldan yeniden üretildi.  

#### <a name="resolution"></a>Çözünürlük
Portaldan doğru ağ geçidi anahtarını kullanıp kullanmayacağınızı doğrulayın. Gerekirse, bir anahtarı yeniden oluşturun ve ağ geçidini kaydetmek için anahtarı kullanın.

### <a name="4-problem"></a>4. sorun
Bir ağ geçidini kaydederken aşağıdaki hata iletisini görebilirsiniz.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Anahtarın içeriği veya biçimi geçersiz](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Nedeni
Giriş ağ geçidi anahtarının içeriği veya biçimi yanlış. Nedenlerden biri, portaldan yalnızca anahtarın bir kısmını kopyaladınız ya da geçersiz bir anahtar kullanıyor olabilir.

#### <a name="resolution"></a>Çözünürlük
Portalda bir ağ geçidi anahtarı oluşturun ve tüm anahtarı kopyalamak için Kopyala düğmesini kullanın. Ardından, ağ geçidini kaydetmek için bu pencerede yapıştırın.

### <a name="5-problem"></a>5. sorun
Bir ağ geçidini kaydederken aşağıdaki hata iletisini görebilirsiniz.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Ağ Geçidi anahtarı geçersiz veya boş](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Nedeni
Ağ Geçidi anahtarı yeniden üretildi veya Azure portal Ağ Geçidi silindi. Veri Yönetimi ağ geçidi kurulumu en son değilse de oluşabilir.

#### <a name="resolution"></a>Çözünürlük
Veri Yönetimi ağ geçidi kurulumunun en son sürümü olup olmadığını denetleyin, Microsoft [indirme merkezi](https://go.microsoft.com/fwlink/p/?LinkId=271260)' nde en son sürümü bulabilirsiniz.

Kurulum güncel/en son ve ağ geçidi portalda hala mevcutsa, Azure portal ağ geçidi anahtarını yeniden oluşturun ve tüm anahtarı kopyalamak için Kopyala düğmesini kullanın ve ardından ağ geçidini kaydetmek için bu pencereye yapıştırın. Aksi takdirde, ağ geçidini yeniden oluşturun ve baştan başlayın.

### <a name="6-problem"></a>6. sorun
Bir ağ geçidini kaydederken aşağıdaki hata iletisini görebilirsiniz.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Ağ Geçidi anahtarı geçersiz veya boş](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Nedeni
Bu hata, ağ geçidi silindiğinden veya ilişkili ağ geçidi anahtarı yeniden üretildiğinden oluşabilir.

#### <a name="resolution"></a>Çözünürlük
Ağ Geçidi silinmişse, portaldan ağ geçidini yeniden oluşturun, **Kaydet**' e tıklayın, anahtarı portaldan kopyalayın, yapıştırın ve ağ geçidini kaydetmeyi deneyin.

Ağ geçidi hala mevcutsa ancak anahtarı yeniden üretildiğinde, ağ geçidini kaydetmek için yeni anahtarı kullanın. Anahtarınız yoksa portaldan anahtarı yeniden oluşturun.

### <a name="7-problem"></a>7. sorun
Bir ağ geçidini kaydederken, bir sertifika için yol ve parola girmeniz gerekebilir.

![Sertifika belirtin](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Nedeni
Ağ Geçidi daha önce diğer makinelere kaydedildi. Bir ağ geçidinin ilk kaydı sırasında ağ geçidiyle bir şifreleme sertifikası ilişkilendirilir. Sertifika, ağ geçidi tarafından kendi kendine oluşturulmuş veya Kullanıcı tarafından sağlanmış olabilir.  Bu sertifika, veri deposunun (bağlı hizmet) kimlik bilgilerini şifrelemek için kullanılır.  

![Sertifikayı dışarı aktarma](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Farklı bir ana makinedeki ağ geçidini geri yüklerken, Kayıt Sihirbazı bu sertifikayı daha önce bu sertifikayla şifrelenen kimlik bilgilerinin şifresini çözmesini ister.  Bu sertifika olmadan, kimlik bilgilerinin yeni ağ geçidi tarafından şifresi çözülür ve bu yeni ağ geçidiyle ilişkili sonraki kopyalama etkinliği yürütmeleri başarısız olur.  

#### <a name="resolution"></a>Çözünürlük
Kimlik bilgisi sertifikasını, Veri Yönetimi ağ geçidi Configuration Manager **Ayarlar** sekmesindeki **dışarı aktar** düğmesini kullanarak özgün ağ geçidi makinesinden dışarı aktardıysanız buradaki sertifikayı kullanın.

Bir ağ geçidini kurtarırken bu aşamayı atlayamazsınız. Sertifika eksikse, portaldan ağ geçidini silmeniz ve yeni bir ağ geçidini yeniden oluşturmanız gerekir.  Ayrıca, kimlik bilgilerini yeniden girerek ağ geçidiyle ilgili tüm bağlı hizmetleri güncelleştirin.

### <a name="8-problem"></a>8. sorun
Aşağıdaki hata iletisini görebilirsiniz.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Nedeni
Bu hata, ağ geçidiniz Internet kaynaklarına erişmek için bir HTTP proxy 'si gerektiren bir ortamda olduğunda veya proxy 'nizin kimlik doğrulama parolasının değiştirilmesi, ancak ağ geçidiniz tarafından buna uygun şekilde güncelleştirilemeyebilir oluşur.

#### <a name="resolution"></a>Çözünürlük
Bu makalenin ara sunucu konuları bölümündeki yönergeleri izleyin ve proxy ayarlarını Veri Yönetimi ağ geçidi Configuration Manager ile yapılandırın.

## <a name="gateway-is-online-with-limited-functionality"></a>Ağ Geçidi, sınırlı işlevlerle çevrimiçi
### <a name="1-problem"></a>1. sorun
Ağ geçidinin durumunu sınırlı işlevlerle çevrimiçi olarak görürsünüz.

#### <a name="cause"></a>Nedeni
Aşağıdaki nedenlerden biri için ağ geçidinin durumunu sınırlı işlevlerle çevrimiçi olarak görürsünüz:

* Ağ Geçidi Azure Service Bus aracılığıyla bulut hizmetine bağlanamaz.
* Bulut hizmeti Service Bus aracılığıyla ağ geçidine bağlanamaz.

Ağ Geçidi, sınırlı işlevlerle çevrimiçi olduğunda, verileri şirket içi veri depolarına kopyalamak için veri işlem hatları oluşturmak üzere Data Factory kopyalama Sihirbazı 'nı kullanamazsınız. Geçici bir çözüm olarak, portalda, Visual Studio 'da veya Azure PowerShell Data Factory düzenleyici kullanabilirsiniz.

#### <a name="resolution"></a>Çözünürlük
Bu soruna yönelik çözüm (sınırlı işlevlerle çevrimiçi olarak), ağ geçidinin bulut hizmetine veya diğer bir yönteme bağlanamama yöntemine göre yapılır. Aşağıdaki bölümlerde bu çözümler sağlanmaktadır.

### <a name="2-problem"></a>2. sorun
Aşağıdaki hatayı görürsünüz.

`Error: Gateway cannot connect to cloud service through service bus`

![Ağ Geçidi, bulut hizmetine bağlanamıyor](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Nedeni
Ağ Geçidi Service Bus aracılığıyla bulut hizmetine bağlanamaz.

#### <a name="resolution"></a>Çözünürlük
Ağ geçidini yeniden çevrimiçi olarak almak için aşağıdaki adımları izleyin:

1. Ağ Geçidi makinesi ve şirket güvenlik duvarında IP adresine giden kurallara izin verin. Windows olay günlüğünden IP adreslerini bulabilirsiniz (KIMLIK = = 401): erişim izinleri XX tarafından yasaklanmış bir şekilde bir yuvaya erişme girişiminde bulunuldu. XX. XX. XX: 9350.
1. Ağ geçidinde ara sunucu ayarlarını yapılandırın. Ayrıntılar için ara sunucu konuları bölümüne bakın.
1. Ağ Geçidi makinesindeki Windows güvenlik duvarında ve şirket güvenlik duvarında 5671 ve 9350-9354 giden bağlantı noktalarını etkinleştirin. Ayrıntılar için bağlantı noktaları ve güvenlik duvarı bölümüne bakın. Bu adım isteğe bağlıdır, ancak performans değerlendirmesi yapmanızı öneririz.

### <a name="3-problem"></a>3. sorun
Aşağıdaki hatayı görürsünüz.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Nedeni
Ağ bağlantısında geçici bir hata oluştu.

#### <a name="resolution"></a>Çözünürlük
Ağ geçidini yeniden çevrimiçi olarak almak için aşağıdaki adımları izleyin:

1. Birkaç dakika bekleyin, hata geldiğinde bağlantı otomatik olarak kurtarılır.
1. Hata devam ederse, Ağ Geçidi hizmetini yeniden başlatın.

## <a name="failed-to-author-linked-service"></a>Bağlı hizmet yazılamadı
### <a name="problem"></a>Sorun
Yeni bir bağlı hizmet için kimlik bilgilerini girmek üzere portalda kimlik bilgileri Yöneticisi 'ni kullanmaya çalıştığınızda veya mevcut bir bağlı hizmet için kimlik bilgilerini güncelleştirdiğinizde bu hatayı görebilirsiniz.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Bu hatayı gördüğünüzde Veri Yönetimi ağ geçidi Configuration Manager ayarlar sayfası aşağıdaki ekran görüntüsünde görünebilir.

![Veritabanına ulaşılamıyor](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Nedeni
Ağ Geçidi makinesinde SSL sertifikası kaybolmuş olabilir. Ağ geçidi bilgisayarı şu anda SSL şifrelemesi için kullanılan sertifikayı yükleyemez. Ayrıca olay günlüğünde aşağıdaki iletiye benzer bir hata iletisi görebilirsiniz.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Çözünürlük
Sorunu çözmek için aşağıdaki adımları izleyin:

1. Veri Yönetimi ağ geçidi Configuration Manager başlatın.
2. **Ayarlar** sekmesine geçin.  
3. SSL sertifikasını değiştirmek için **Değiştir** düğmesine tıklayın.

   ![Sertifikayı Değiştir düğmesi](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. SSL sertifikası olarak yeni bir sertifika seçin. Siz veya herhangi bir kuruluş tarafından oluşturulan herhangi bir SSL sertifikasını kullanabilirsiniz.

   ![Sertifika belirtin](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopyalama etkinliği başarısız oluyor
### <a name="problem"></a>Sorun
Portalda bir işlem hattı ayarladıktan sonra aşağıdaki "UserErrorFailedToConnectToSqlserver" hatasını fark edebilirsiniz.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Nedeni
Bu durum farklı nedenlerle ortaya çıkabilir ve risk azaltma buna göre farklılık gösterir.

#### <a name="resolution"></a>Çözünürlük
SQL veritabanına bağlanmadan önce Veri Yönetimi ağ geçidi istemci tarafında TCP/1433 bağlantı noktası üzerinden giden TCP bağlantılarına izin verin.

Hedef veritabanı bir Azure SQL veritabanı ise, Azure için SQL Server güvenlik duvarı ayarlarını da kontrol edin.

Şirket içi veri deposuyla bağlantıyı test etmek için aşağıdaki bölüme bakın.

## <a name="data-store-connection-or-driver-related-errors"></a>Veri deposu bağlantısı veya sürücü ile ilgili hatalar
Veri deposu bağlantısı veya sürücü ile ilgili hatalar görürseniz, aşağıdaki adımları izleyin:

1. Ağ Geçidi makinesinde Veri Yönetimi ağ geçidi Configuration Manager başlatın.
2. **Tanılama** sekmesine geçin.
3. **Test bağlantısı**' nda Ağ Geçidi grubu değerlerini ekleyin.
4. Bağlantı bilgilerini ve kimlik bilgilerini kullanarak ağ geçidi makinesinden şirket içi veri kaynağına bağlanıp bağlanamadıysanız bkz. **Test** ' e tıklayın. Bir sürücü yükledikten sonra bağlantı testi yine başarısız olursa, ağ geçidini son değişiklikleri alması için yeniden başlatın.

![Tanılama sekmesinde Bağlantıyı Sına](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Ağ Geçidi günlükleri
### <a name="send-gateway-logs-to-microsoft"></a>Ağ Geçidi günlüklerini Microsoft 'a gönder
Ağ Geçidi sorunlarını gidermeye yönelik yardım almak için Microsoft Desteği ile iletişim kurmak istediğinizde, ağ geçidi günlüklerinizi paylaşmanız istenebilir. Ağ geçidinin yayımlanmasından sonra, gerekli ağ geçidi günlüklerini Veri Yönetimi ağ geçidi Configuration Manager iki düğme tıklamasıyla paylaşabilirsiniz.    

1. Veri Yönetimi ağ geçidi Configuration Manager **Tanılama** sekmesine geçin.

    ![Veri Yönetimi ağ geçidi tanılama sekmesi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Aşağıdaki iletişim kutusunu görmek için **günlükleri Gönder** ' e tıklayın.

    ![Veri Yönetimi ağ geçidi gönderme günlükleri](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Seçim Olay Görüntüleyicisi 'nde günlükleri gözden geçirmek için **günlükleri görüntüle** ' ye tıklayın.
4. Seçim Microsoft Web Hizmetleri Gizlilik bildirimini gözden geçirmek için **Gizlilik** ' e tıklayın.
5. Karşıya yüklemek üzere olduğunuz şeyi karşıladığınızda, sorun giderme için günlükleri **Gönder** ' e tıklayarak günlükleri son yedi günden Microsoft 'a gönderin. Aşağıdaki ekran görüntüsünde gösterildiği gibi, gönderme günlüğü işleminin durumunu görmeniz gerekir.

    ![Veri Yönetimi ağ geçidi gönderme günlükleri durumu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. İşlem tamamlandıktan sonra, aşağıdaki ekran görüntüsünde gösterildiği gibi bir iletişim kutusu görürsünüz.

    ![Veri Yönetimi ağ geçidi gönderme günlükleri durumu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. **Rapor kimliğini** kaydedin ve Microsoft desteği ile paylaşabilirsiniz. Rapor KIMLIĞI, sorun giderme için karşıya yüklediğiniz ağ geçidi günlüklerini bulmak için kullanılır.  Rapor KIMLIĞI olay görüntüleyicisine da kaydedilir.  "25" olay KIMLIĞINE bakarak bu dosyayı bulabilir ve Tarih ve saati kontrol edebilirsiniz.

    ![Veri Yönetimi ağ geçidi gönderme günlükleri rapor KIMLIĞI](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Ağ Geçidi konak makinesinde arşiv ağ geçidi günlükleri
Ağ Geçidi sorunlarından oluşan bazı senaryolar vardır ve ağ geçidi günlüklerini doğrudan paylaşamazsınız:

* Ağ geçidini el ile yükler ve ağ geçidini kaydedersiniz.
* Ağ geçidini Veri Yönetimi ağ geçidi Configuration Manager yeniden üretilen bir anahtarla kaydetmeye çalışırsınız.
* Günlükleri göndermeye çalışırsınız ve ağ geçidi ana bilgisayar hizmeti bağlanamaz.

Bu senaryolar için, ağ geçidi günlüklerini bir zip dosyası olarak kaydedebilir ve Microsoft desteği ile iletişime geçerek paylaşabilirsiniz. Örneğin, ağ geçidini aşağıdaki ekran görüntüsünde gösterildiği gibi kaydettiğinizde bir hata alırsanız.   

![Veri Yönetimi ağ geçidi kayıt hatası](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

**Arşivi Arşivle ve Kaydet bağlantısına tıklayın** ve ardından ZIP dosyasını Microsoft desteği ile paylaşabilirsiniz.

![Veri Yönetimi ağ geçidi arşiv günlükleri](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Ağ Geçidi günlüklerini bulma
Ayrıntılı ağ geçidi günlük bilgilerini Windows olay günlüklerinde bulabilirsiniz.

1. Windows **Olay Görüntüleyicisi**başlatın.
2. **Uygulama ve hizmet günlükleri** > **veri yönetimi ağ geçidi** klasöründeki günlükleri bulun.

   Ağ geçidine ilişkin sorunlarda sorun giderirken, Olay Görüntüleyicisi 'nde hata düzeyi olayları arayın.

![Olay Görüntüleyicisi 'nde ağ geçidi günlüklerini Veri Yönetimi](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
