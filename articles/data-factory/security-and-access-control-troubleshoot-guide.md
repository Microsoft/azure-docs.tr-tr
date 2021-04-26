---
title: Güvenlik ve erişim denetimi sorunlarını giderme
description: Azure Data Factory güvenlik ve erişim denetimi sorunlarını giderme hakkında bilgi edinin.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: 5e94ea989002d3d3c6d0e96123d5b8ddb5f078c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568051"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Azure Data Factory güvenlik ve erişim denetimi sorunlarını giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede Azure Data Factory içindeki güvenlik ve erişim denetimi için genel sorun giderme yöntemleri incelenmektedir.

## <a name="common-errors-and-messages"></a>Sık karşılaşılan hatalar ve mesajlar

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Bulut veri deposunun kopyalama etkinliğinde bağlantı sorunu

#### <a name="symptoms"></a>Belirtiler

Kaynak veya havuz veri deposunda bağlantı sorunları oluştuğunda çeşitli hata iletileri döndürülebilir.

#### <a name="cause"></a>Nedeni 

Soruna genellikle aşağıdaki faktörlerden biri neden olur:

* Şirket içinde barındırılan bir IR kullanıyorsanız, şirket içinde barındırılan tümleştirme çalışma zamanı (IR) düğümündeki proxy ayarı.

* Şirket içinde barındırılan IR düğümündeki güvenlik duvarı ayarı, kendi kendine barındırılan bir IR kullanıyorsanız.

* Bulut veri deposundaki güvenlik duvarı ayarı.

#### <a name="resolution"></a>Çözüm

* Bunun bir bağlantı sorunu olduğundan emin olmak için aşağıdaki noktaları kontrol edin:

   - Hata, kaynak veya havuz bağlayıcılarından oluşur.
   - Hata kopyalama etkinliğinin başlangıcında.
   - Düğümlerden yalnızca birinde sorun varsa, birden çok düğümlü bir şirket içinde barındırılan IR 'de rastgele bir hata olabileceğinden hata, tek bir düğüm içeren Azure IR veya şirket içinde barındırılan IR için tutarlıdır.

* **Şirket içinde barındırılan BIR IR** kullanıyorsanız, bir Azure IR kullanıyorsanız aynı veri deposuna bağlanma işlemi başarılı olabileceğinden proxy, güvenlik duvarı ve ağ ayarlarınızı kontrol edin. Bu senaryonun sorunlarını gidermek için bkz.:

   * [Şirket içinde barındırılan IR bağlantı noktaları ve güvenlik duvarları](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage Bağlayıcısı](./connector-azure-data-lake-store.md)
  
* **Azure IR** kullanıyorsanız, veri deposunun güvenlik duvarı ayarını devre dışı bırakmayı deneyin. Bu yaklaşım aşağıdaki iki durumda sorunları çözebilir:
  
   * [Azure IR IP adresleri](./azure-integration-runtime-ip-addresses.md) izin verilenler listesinde değil.
   * [Azure Blob depolama](./connector-azure-blob-storage.md#supported-capabilities) Için *Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver* özelliği kapalı ve [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * *Azure hizmetlerine erişime Izin ver* ayarı Azure Data Lake Storage 1. için etkinleştirilmemiş.

Yukarıdaki yöntemlerin hiçbiri işe yarar olursa yardım için Microsoft 'a başvurun.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Genel ağ erişimi devre dışı bırakıldıktan sonra geçersiz veya boş kimlik doğrulama anahtarı sorunu

#### <a name="symptoms"></a>Belirtiler

Data Factory için genel ağ erişimini devre dışı bıraktıktan sonra, şirket içinde barındırılan tümleştirme çalışma zamanı şu hatayı oluşturur: "kimlik doğrulama anahtarı geçersiz veya boş."

#### <a name="cause"></a>Nedeni

Sorun büyük olasılıkla bir etki alanı adı sistemi (DNS) çözümleme sorunundan kaynaklanıyor, çünkü genel bağlantıyı devre dışı bırakmak ve özel bir uç nokta kurmak yeniden bağlanmayı önler.

Data Factory tam etki alanı adının (FQDN) genel IP adresi olarak çözümlenip çözümlenmediğini doğrulamak için aşağıdakileri yapın:

1. Azure sanal makinesini (VM) Data Factory özel uç noktayla aynı sanal ağda oluşturduğunuzdan emin olun.

2. Azure VM 'den Data Factory FQDN 'sine PsPing ve PING komutunu çalıştırın:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > PsPing komutu için bir bağlantı noktası belirtmeniz gerekir. 443 numaralı bağlantı noktası burada gösteriliyor ancak gerekli değildir.

3. Her iki komutun belirtilen bölgeyi temel alan Azure Data Factory genel bir IP 'ye çözümlenip çözümlenmediğini denetleyin. IP aşağıdaki biçimde olmalıdır: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Çözüm

Sorunu gidermek için şunları yapın:

- As seçeneği, "özel bağlantı DNS bölgesi" Data Factory altına el ile "sanal ağ bağlantısı" eklemenizi öneririz. Ayrıntılar için [Azure özel bağlantısına Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) makalesine bakın. Yönerge, özel DNS bölgesini veya özel DNS sunucusunu Data Factory FQDN 'yi özel bir IP adresine çözümlemek üzere yapılandırmaya yöneliktir. 

- Ancak, özel DNS bölgesini veya özel DNS sunucusunu yapılandırmak istemiyorsanız, aşağıdaki geçici çözümü deneyin:

  1. Windows 'da konak dosyasını değiştirin ve özel IP 'yi (Azure Data Factory özel uç noktası) Azure Data Factory FQDN ile eşleyin.
  
     Azure VM 'de ' e gidin `C:\Windows\System32\drivers\etc` ve ardından *ana bilgisayar* dosyasını Not defteri 'nde açın. Özel IP 'yi eşleyen satırı, dosyanın sonundaki FQDN 'ye ekleyin ve değişikliği kaydedin.
     
     ![Özel IP 'yi konağa eşleme ekran görüntüsü.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Özel IP 'yi içermesi gereken yanıtı denetlemek için yukarıdaki doğrulama adımlarında aynı komutları yeniden çalıştırın.

  1. Şirket içinde barındırılan tümleştirme çalışma zamanını yeniden kaydedin ve sorun çözümlenmelidir.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Özel bağlantı nedeniyle, şirket içinde barındırılan VM 'Lere IR kimlik doğrulama anahtarı kaydedilemiyor

#### <a name="symptoms"></a>Belirtiler

Özel bağlantı etkinleştirildiğinden, kendi kendine barındırılan sanal makineye IR kimlik doğrulama anahtarını kaydedemedik. Aşağıdaki hata iletisini alırsınız:

"Anahtara * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * 0,1250079 İkincisi, hata kodu: ınvalidgatewaykey, ActivityId: XXXXXXX ve ayrıntılı hata iletisi, Istemci IP adresi geçerli bir özel IP değil, veri fabrikasının ortak ağa erişimi başarısız olmasına neden olur."

#### <a name="cause"></a>Nedeni

Sorun, şirket içinde barındırılan IR 'yi yüklemeye çalıştığınız VM 'den kaynaklanabilir. Buluta bağlanmak için genel ağ erişiminin etkinleştirildiğinden emin olun.

#### <a name="resolution"></a>Çözüm

**Çözüm 1**
 
Sorunu gidermek için şunları yapın:

1. [Fabrikalar-Güncelleştir](/rest/api/datafactory/Factories/Update) sayfasına gidin.

1. Sağ üst köşedeki **deneyin** düğmesini seçin.
1. **Parametreler** altında gerekli bilgileri doldurun. 
1. **Gövde** altında aşağıdaki özelliği yapıştırın:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. İşlevi çalıştırmak için **Çalıştır** ' ı seçin. 

1. **Parametreler** altında gerekli bilgileri doldurun. 

1. **Gövde** altında aşağıdaki özelliği yapıştırın:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. İşlevi çalıştırmak için **Çalıştır** ' ı seçin. 
1. **Yanıt kodu: 200** ' nin görüntülendiğini onaylayın. Yapıştırdığınız özelliğin JSON tanımında de görüntülenmesi gerekir.

1. IR kimlik doğrulama anahtarını tümleştirme çalışma zamanına yeniden ekleyin.

**Çözüm 2**

Sorunu çözmek için [Azure Data Factory Için Azure özel bağlantısı](./data-factory-private-link.md)' na gidin.

Aşağıdaki ekran görüntüsünde gösterildiği gibi, Kullanıcı arabiriminde ortak ağ erişimini etkinleştirmeyi deneyin:

![Ağ bölmesinde "ortak ağ erişimine Izin ver" için "etkin" denetimin ekran görüntüsü.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>ADF özel DNS bölgesi Azure Resource Manager geçersiz kılmaları DNS çözümlemesi ' bulunamadı ' hatasına neden oluyor

#### <a name="cause"></a>Nedeni
Hem Azure Resource Manager hem de ADF, müşterinin özel DNS 'sinde, Azure Resource Manager kayıtlarının bulunamadığı bir senaryoya karşı olası bir çakışma oluşturan aynı özel bölgeyi kullanıyor.

#### <a name="solution"></a>Çözüm
1. Azure portal **özel DNS bölgelerini bulun** .
![Özel DNS bölgelerini bulma ekran görüntüsü.](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. **ADF** kaydında bir kayıt olup olmadığını kontrol edin.
![Kaydın ekran görüntüsü.](media/security-access-control-troubleshoot-guide/a-record.png)
3.  **Sanal ağ bağlantıları**' na gidin, tüm kayıtları silin.
![Sanal ağ bağlantısının ekran görüntüsü.](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Azure portal veri fabrikanıza gidin ve Azure Data Factory Portal için özel uç noktasını yeniden oluşturun.
![Özel uç nokta yeniden oluşturma ekran görüntüsü.](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Özel DNS bölgelere geri dönün ve yeni bir özel DNS bölgesi **Privatelink.adf.Azure.com** olup olmadığını kontrol edin.
![Yeni DNS kaydının ekran görüntüsü.](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Genel uç noktada bağlantı hatası

#### <a name="symptoms"></a>Belirtiler

Azure Blob depolama hesabı genel erişimi ile veri kopyalarken işlem hattı çalıştırmaları aşağıdaki hata ile rastgele başarısız olur.

Örneğin: Azure Blob depolama havuzu Azure IR (genel, yönetilmeyen VNet değil) kullanıyor ve Azure SQL veritabanı kaynağı yönetilen VNet IR 'yi kullanıyor. Ya da kaynak/havuz yalnızca depolama genel erişimiyle yönetilen VNet IR kullanır.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Nedeni

ADF hala yönetilen VNet IR kullanabilir, ancak yönetilen VNet 'teki Azure Blob depolama alanına genel uç nokta, test sonucuna göre güvenilir olmadığından ve Azure Blob depolama ve Azure Data Lake Gen2 ' nin, [yönetilen sanal ağ & yönetilen özel uç noktalarına](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)göre ADF tarafından yönetilen sanal ağdan ortak uç nokta ile bağlı olması desteklenmediği için bu hatayla karşılaşabilirsiniz.

#### <a name="solution"></a>Çözüm

- Yönetilen VNet IR kullanılırken kaynak üzerinde özel uç nokta ve ayrıca havuz tarafında etkin olma.
- Hala genel uç noktasını kullanmak istiyorsanız, kaynak ve havuz için yönetilen VNet IR kullanmak yerine yalnızca ortak IR 'ye geçebilirsiniz. Ortak IR 'ye geri döndüğünüzde, ADF, yönetilen VNet IR hala orada ise, yönetilen VNet IR 'yi kullanmaya devam edebilir.

## <a name="next-steps"></a>Sonraki adımlar

Sorun giderme konusunda daha fazla yardım için aşağıdaki kaynakları deneyin:

*  [Data Factory için özel bağlantı](data-factory-private-link.md)
*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q bir sayfa&](/answers/topics/azure-data-factory.html)
*  [Data Factory için yığın taşma Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)