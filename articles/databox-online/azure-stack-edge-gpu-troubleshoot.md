---
title: GPU ile Azure Stack Edge Pro sorunlarını gidermek için Azure portal kullanma | Microsoft Docs
description: Azure Stack Edge Pro GPU sorunlarını nasıl giderebileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 87e75d771c2cc269eaae81c2433f445eb65a17a9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314159"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazındaki sorunları giderme 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack Edge Pro GPU cihazınızdaki sorunların nasıl giderileceği açıklanır. 


## <a name="run-diagnostics"></a>Tanılama çalıştırma

Cihaz hatalarını tanılamak ve gidermek için tanılama testlerini çalıştırabilirsiniz. Tanılama testlerini çalıştırmak için cihazınızın yerel web arabiriminde aşağıdaki adımları gerçekleştirin.

1. Yerel web arabiriminde **Sorun giderme > Tanılama testleri** sayfasına gidin. Çalıştırmak istediğiniz testi seçin ve **Test Çalıştır**' ı seçin. Test ağınız, cihazınız, Web proxy, zaman veya bulut ayarlarınızdaki olası sorunları tanılar. Cihazın testleri çalıştırdığı bildirilir.

    ![Testleri seçin ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Testler tamamlandıktan sonra sonuçlar görüntülenir. 

    ![Test sonuçlarını görüntüle](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Test başarısız olursa önerilen eylem URL'si gösterilir. Önerilen eylemi görüntülemek için URL 'YI seçin.
 
    ![Başarısız testler için uyarıları gözden geçirin](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Destek paketi toplama

Günlük paketi, Microsoft Desteğinin cihaz sorunlarını giderme konusunda yardımcı olabilmesi için ihtiyaç duyabileceği günlüklerin tamamını içerir. Günlük paketi oluşturmak için yerel web arabirimini kullanabilirsiniz.

Destek paketi toplamak için aşağıdaki adımları gerçekleştirin. 

1. Yerel web arabiriminde **Sorun giderme > Destek** sayfasına gidin. **Destek paketi oluştur**' u seçin. Sistem, destek paketini toplamaya başlar. Paketin toplanması birkaç dakika sürebilir.

    ![Kullanıcı Ekle ' yi seçin](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Destek paketi oluşturulduktan sonra **destek paketini indir**' i seçin. Seçtiğiniz yola .zip uzantılı bir paket indirilir. Paketi açıp sistem günlüğü dosyalarını görüntüleyebilirsiniz.

    ![Kullanıcı Ekle 2 ' yi seçin](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Gelişmiş güvenlik günlükleri toplayın

Gelişmiş güvenlik günlükleri, Azure Stack Edge Pro cihazınız için yazılım veya donanıma yönelik erişim günlükleri olabilir.

### <a name="software-intrusion-logs"></a>Yazılım erişim günlükleri

Yazılım yetkisiz erişimi veya varsayılan güvenlik duvarı günlükleri gelen ve giden trafik için toplanır. 

- Cihaz fabrikada yansıma oluşturulduğunda, varsayılan güvenlik duvarı günlüğü etkinleştirilir. Bu Günlükler, yerel kullanıcı arabirimi veya cihazın Windows PowerShell arabirimi aracılığıyla bir destek paketi oluşturduğunuzda varsayılan olarak destek paketine paketlenmiştir.

- Cihazda herhangi bir yazılım (NW) erişimi gözden geçirmek için destek paketinde yalnızca güvenlik duvarı günlükleri gerekliyse, `-Include FirewallLog` destek paketini oluştururken seçeneğini kullanın. 

- Belirli bir içerme seçeneği sağlanmazsa, destek paketinde varsayılan olarak güvenlik duvarı günlüğü eklenir.

- Destek paketinde güvenlik duvarı günlüğü, `pfirewall.log` kök klasörde bulunur ve olur. Azure Stack Edge Pro cihazı için yazılım yetkisiz giriş günlüğüne bir örnek aşağıda verilmiştir. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Donanımla izinsiz giriş günlükleri

Cihazda herhangi bir donanımı izinsiz olarak belirlemek için, şu anda kasa açma veya kapatma gibi tüm kasa olayları günlüğe kaydedilir. 

- Cihazdaki sistem olay günlüğü, `racadm` cmdlet 'i kullanılarak okundu. Bu olaylar daha sonra bir dosya için ' de kasaya ilgili olay için filtrelenir `HWIntrusion.txt` .

- Destek paketinde yalnızca donanıma yetkisiz giriş günlüğü almak için, `-Include HWSelLog` destek paketini oluştururken seçeneğini kullanın. 

- Belirli bir içerme seçeneği sağlanmazsa, donanım yetkisiz giriş günlüğü destek paketinde varsayılan olarak dahil edilir.

- Destek paketinde, donanım yetkisiz giriş günlüğü, `HWIntrusion.txt` kök klasörde bulunur ve olur. Azure Stack Edge Pro cihazı için bir donanım yetkisiz giriş günlüğüne bir örnek aşağıda verilmiştir. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Sorun gidermek için günlükleri kullanma

Yükleme ve yenileme işlemlerinde karşılaşılan hatalar ilgili hata dosyalarına yazılır.

1. Hata dosyalarını görüntülemek için paylaşımınıza gidin ve içeriği görüntülemek için paylaşıma seçin. 


2. _Microsoft Data Box Edge klasörünü_ seçin. Bu klasör iki alt klasör içerir:

    - Karşıya yükleme hatalarının bulunduğu Upload klasörü.
    - Yenileme sırasında karşılaşılan hataların bulunduğu Refresh klasörü.

    Yenileme günlük dosyası örneği burada gösterilmiştir.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Bu dosyada bir hata gördüğünüzde (örnekte vurgulanmıştır) hata kodunu yazın (burada 16001). Hata kodunun açıklamasını aşağıdaki hata başvurusu bölümünde bulabilirsiniz.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Sorun gidermek için hata listelerini kullanma

Hata listeleri, tanımlı senaryolardan derlenir ve kendi kendine tanılama ve sorun giderme işlemleri için kullanılabilir. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Cihazınıza erişmek için Azure Resource Manager yapılandırması sırasında oluşabilecek hatalar şunlardır. 

| **Sorun/hatalar** |  **Çözünürlük** | 
|------------|-----------------|
|Genel sorunlar|<li>[Sınır cihazının düzgün şekilde yapılandırıldığını doğrulayın](#verify-the-device-is-configured-properly).<li> [İstemcinin düzgün yapılandırıldığını doğrulama](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: istek gönderilirken bir hata oluştu.<br>Şu satırda: 1 char: 1<br>+ Add-AzureRmEnvironment-adı Az3-Ermenistan " https://management.dbe ...|Bu hata, Azure Stack Edge Pro cihazınızın ulaşılamaz veya düzgün şekilde yapılandırılmadığı anlamına gelir. Sınır cihazının ve istemcisinin doğru şekilde yapılandırıldığını doğrulayın. Rehberlik için, bu tablodaki **genel sorunlar** satırına bakın.|
|Hizmet hata döndürdü. Daha fazla ayrıntı için InnerException öğesine bakın: temel alınan bağlantı kapatıldı: SSL/TLS güvenli kanalı için güven ilişkisi kurulamadı. |   Bu hata, büyük olasılıkla kendi sertifika adımlarınızı bir veya daha fazla getirme işlemi yanlış gerçekleştirdi. Kılavuza [buradan](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)ulaşabilirsiniz. |
|İşlem geçersiz bir durum kodu döndürdü ' ServiceUnavailable ' <br> Yanıt durum kodu başarılı olduğunu göstermiyor: 503 (hizmet kullanılamıyor). | Bu hata, bu koşullardan herhangi birinin sonucu olabilir.<li>ArmStsPool durdurulmuş durumda.</li><li>Azure Resource Manager/güvenlik belirteci Hizmetleri Web sitelerinden biri çalışmıyor.</li><li>Azure Resource Manager küme kaynağı çalışmıyor.</li><br><strong>Note:</strong> Gereci yeniden başlatmak sorunu çözebilir, ancak daha fazla hata ayıklayabilmeniz için destek paketini toplamanız gerekir.|
|AADSTS50126: Geçersiz Kullanıcı adı veya parola.<br>İzleme KIMLIĞI: 29317dav9-52fc-4ba0-9778-446ae5625e5a<br>Bağıntı KIMLIĞI: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Zaman damgası: 2019-11-15 09:21:57Z: uzak sunucu bir hata döndürdü: (400) hatalı Istek.<br>Şu satırda: 1 char: 1 |Bu hata, bu koşullardan herhangi birinin sonucu olabilir.<li>Geçersiz Kullanıcı adı ve parola için, [buradaki](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password) adımları izleyerek ve ardından doğru parolayı kullanarak müşterinin Azure Portal parolayı değiştirdiğini doğrulayın.<li>Geçersiz bir kiracı KIMLIĞI için kiracı KIMLIĞI sabit bir GUID 'dir ve şu şekilde ayarlanmalıdır `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: kaynak devre dışı veya yok. Erişmeye çalıştığınız kaynağın tam kaynak URL 'sini belirttiğinizden emin olmak için uygulamanızın kodunu kontrol edin.<br>İzleme KIMLIĞI: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Bağıntı KIMLIĞI: 75c8ef5a-830e-48B5-b039-595a96488ff9 zaman damgası: 2019-11-18 07:00:51Z: uzak sunucu bir hata döndürdü: (400) kötü |Komutta kullanılan kaynak uç noktaları `Add-AzureRmEnvironment` yanlış.|
|Buluttan uç noktalar alınamıyor.<br>Lütfen ağ bağlantınız olduğundan emin olun. Hata ayrıntısı: HTTPSConnectionPool (Konak = ' Management. dbg-of4k6suvm.microsoftdatabox.com ', bağlantı noktası = 30005): en fazla yeniden deneme URL:/Metadata/endpoints ile aşıldı? api-Version = 2015-01-01 (SSLError ("Hatalı el sıkışma: hata ([(' SSL yordamları ', ' tls_process_server_certificate ', ' sertifika doğrulaması başarısız ')],)",),) |Bu hata çoğunlukla bir Mac/Linux ortamında görünür ve aşağıdaki sorunlardan kaynaklanır:<li>Python sertifika deposuna bir pek biçim sertifikası eklenmedi.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Cihazın düzgün yapılandırıldığını doğrulama

1. Yerel kullanıcı arabiriminden, cihaz ağının doğru şekilde yapılandırıldığını doğrulayın.

2. [Burada](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)bahsedilen tüm uç noktalar için sertifikaların güncelleştirildiğinden emin olun.

3. Yerel Kullanıcı arabirimindeki **cihaz** sayfasından Azure Resource Manager Yönetimi ve oturum açma uç noktası alın.

4. Cihazın etkinleştirildiğini ve Azure 'da kayıtlı olduğunu doğrulayın.


### <a name="verify-the-client-is-configured-properly"></a>İstemcinin düzgün yapılandırıldığını doğrulama

1. [Burada](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)bahsedilen doğru PowerShell sürümünün yüklü olduğunu doğrulayın.

2. Doğru PowerShell modüllerinin [burada](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)bahsedilen şekilde yüklendiğini doğrulayın.

3. Azure Resource Manager ve oturum açma uç noktalarına erişilebildiğini doğrulayın. Uç noktalara ping yapmayı deneyebilirsiniz. Örnek:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Ulaşılamıyorsa, [burada](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)belirtilen DNS/ana bilgisayar dosya girdilerini ekleyin.
   
4. İstemci sertifikalarının [burada](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)bahsedilen şekilde yüklendiğini doğrulayın.

5. Müşteri PowerShell kullanıyorsa, bu PowerShell komutunu çalıştırarak ayrıntılı iletileri görmek için hata ayıklama tercihini etkinleştirmeniz gerekir. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Cihazda BLOB depolama alanı 

Azure Stack Edge Pro/Data Box Gateway cihazında blob depolamayla ilgili hatalar aşağıda verilmiştir.

| **Sorun/hatalar** |  **Çözünürlük** | 
|--------------------|-----------------|
|Alt kaynaklar alınamıyor. HTTP başlıklarındaki bir değer doğru biçimde değil.| **Düzenle** menüsünde **hedef Azure Stack API 'leri**' ni seçin. Sonra Azure Depolama Gezgini yeniden başlatın.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Uç nokta adının `<accountname>.blob.<serialnumber>.microsoftdatabox.com` Bu yoldaki Hosts dosyasına eklendiğinden emin olun: `C:\Windows\System32\drivers\etc\hosts` Windows veya `/etc/hosts` Linux üzerinde.|
|Alt kaynaklar alınamıyor.<br> Ayrıntılar: otomatik olarak imzalanan sertifika |Cihazınızın SSL sertifikasını Azure Depolama Gezgini içine aktarın: <ol><li>Azure portal sertifikayı indirin. Daha fazla bilgi için bkz. [sertifikayı indirme](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>**Düzenle** menüsünde SSL sertifikaları ' nı seçin ve ardından **sertifikaları içeri aktar**' ı seçin.</li></ol>|
|AzCopy komutu, bu hatayı görüntülemeden önce bir dakika boyunca yanıt vermeyi durdurmuş gibi görünüyor:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Uç nokta adının `<accountname>.blob.<serialnumber>.microsoftdatabox.com` Şu adreste bulunan Hosts dosyasına eklendiğinden emin olun: `C:\Windows\System32\drivers\etc\hosts` .|
|AzCopy komutu, bu hatayı görüntülemeden önce bir dakika boyunca yanıt vermeyi durdurmuş gibi görünüyor:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Cihazınızın SSL sertifikasını sistemin sertifika deposuna aktarın. Daha fazla bilgi için bkz. [sertifikayı indirme](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|AzCopy komutu, bu hatayı görüntülemeden önce 20 dakika boyunca yanıt vermeyi durdurmuş gibi görünüyor:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Uç nokta adının `<accountname>.blob.<serialnumber>.microsoftdatabox.com` Şu adreste bulunan Hosts dosyasına eklendiğinden emin olun: `/etc/hosts` .|
|AzCopy komutu, bu hatayı görüntülemeden önce 20 dakika boyunca yanıt vermeyi durdurmuş gibi görünüyor:<br>`Error parsing source location… The SSL connection could not be established`. |Cihazınızın SSL sertifikasını sistemin sertifika deposuna aktarın. Daha fazla bilgi için bkz. [sertifikayı indirme](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|AzCopy komutu, bu hatayı görüntülemeden önce 20 dakika boyunca yanıt vermeyi durdurmuş gibi görünüyor:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Uç nokta adının `<accountname>.blob.<serialnumber>.microsoftdatabox.com` Şu adreste bulunan Hosts dosyasına eklendiğinden emin olun: `/etc/hosts` .|
|AzCopy komutu, bu hatayı görüntülemeden önce 20 dakika boyunca yanıt vermeyi durdurmuş gibi görünüyor: `Error parsing source location… The SSL connection could not be established` .|Cihazınızın SSL sertifikasını sistemin sertifika deposuna aktarın. Daha fazla bilgi için bkz. [sertifikayı indirme](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|HTTP başlıklarındaki bir değer doğru biçimde değil.|Python için Microsoft Azure Depolama kitaplığının yüklü sürümü Data Box tarafından desteklenmiyor. Desteklenen sürümler için Azure Data Box BLOB depolama gereksinimleri bölümüne bakın.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Python 'u çalıştırmadan önce, REQUESTS_CA_BUNDLE ortam değişkenini Base64 kodlamalı SSL sertifika dosyasının yoluna ayarlayın (bkz. [sertifikayı indirme](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Örnek:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Alternatif olarak, sertifikayı sistemin sertifika deposuna ekleyin ve bu ortam değişkenini bu deponun yoluna ayarlayın. Örneğin, Ubuntu üzerinde:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Bağlantı zaman aşımına uğrar.|Azure Stack Edge Pro 'da oturum açın ve sonra kilidinin açık olup olmadığını kontrol edin. Cihaz her yeniden başlatıldığında, birisi oturum açana kadar kilitli kalır.|

## <a name="troubleshoot-iot-edge-errors"></a>IoT Edge hatalarında sorun giderme

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Sonraki adımlar

- [Cihaz etkinleştirme sorunlarını giderme](azure-stack-edge-gpu-troubleshoot-activation.md)hakkında daha fazla bilgi edinin.
