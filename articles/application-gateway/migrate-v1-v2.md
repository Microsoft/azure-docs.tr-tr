---
title: V1 'den v2 'ye geçiş-Azure Application Gateway
description: Bu makalede, Azure Application Gateway ve Web uygulaması güvenlik duvarı 'Nı v1 'den v2 'ye nasıl geçirebileceğiniz gösterilmektedir
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 4757a8237aa6226b78e7c1e79ba50710e31d28e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594274"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Azure Application Gateway ve Web uygulaması güvenlik duvarını v1 'den v2 'ye geçirme

[Azure Application Gateway ve Web uygulaması güvenlik duvarı (WAF) v2](application-gateway-autoscaling-zone-redundant.md) artık kullanılabilir, otomatik ölçeklendirme ve kullanılabilirlik bölgesi artıklığı gibi ek özellikler sunar. Bununla birlikte mevcut v1 ağ geçitleri v2'ye otomatik olarak yükseltilmez. V1 'den v2 'ye geçiş yapmak istiyorsanız, bu makaledeki adımları izleyin.

Bir geçişte iki aşama vardır:

1. Yapılandırmayı geçirme
2. İstemci trafiğini geçirme

Bu makalede yapılandırma geçişi ele alınmaktadır. İstemci trafiği geçişi, özel ortamınıza bağlı olarak farklılık gösterir. Ancak, bazı üst düzey genel öneriler [sağlanır](#migrate-client-traffic).

## <a name="migration-overview"></a>Geçişe genel bakış

Aşağıdakileri gerçekleştiren bir Azure PowerShell betiği vardır:

* Belirttiğiniz bir sanal ağ alt ağında yeni bir Standard_v2 veya WAF_v2 ağ geçidi oluşturur.
* V1 standardı veya WAF ağ geçidi ile ilişkili yapılandırmayı, yeni oluşturulan Standard_V2 veya WAF_V2 ağ geçidine sorunsuzca kopyalar.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Yeni v2 ağ geçidinde yeni ortak ve özel IP adresleri vardır. Mevcut v1 ağ geçidiyle ilişkili IP adreslerini sorunsuz bir şekilde v2 'ye taşımak mümkün değildir. Ancak, yeni V2 ağ geçidine var olan (ayrılmamış) ortak veya özel IP adresi ayırabilirsiniz.
* V1 ağ geçidinizin bulunduğu sanal ağınız içindeki başka bir alt ağ için bir IP adresi alanı sağlamanız gerekir. Betik, zaten v1 ağ geçidine sahip olan mevcut alt ağlarda V2 ağ geçidini oluşturamaz. Ancak, var olan alt ağda zaten bir V2 ağ geçidi varsa, bu, yeterli IP adresi alanı sağlanabileceği için hala çalışabilir.
* V2 ağ geçidi alt ağıyla ilişkili bir ağ güvenlik grubunuz veya Kullanıcı tanımlı yollar varsa, başarılı bir geçiş için [NSG gereksinimlerine](../application-gateway/configuration-infrastructure.md#network-security-groups) ve [UDR gereksinimlerine](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) uydıklarından emin olun
* [Sanal ağ hizmeti uç noktası ilkeleri](../virtual-network/virtual-network-service-endpoint-policies-overview.md) şu anda Application Gateway bir alt ağda desteklenmiyor.
* Bir TLS/SSL yapılandırmasını geçirmek için v1 ağ geçidinizdeki kullanılan tüm TLS/SSL sertifikalarını belirtmeniz gerekir.
* V1 ağ geçidiniz için FIPS modunu etkinleştirdiyseniz, bu, yeni V2 ağ geçidize geçirilmez. FIPS modu v2 'de desteklenmez.
* v2 IPv6 desteklemez, bu nedenle IPv6 etkin v1 ağ geçitleri geçirilmez. Betiği çalıştırırsanız, bu tamamlanmamış olabilir.
* V1 ağ geçidinde yalnızca bir özel IP adresi varsa, betik bir genel IP adresi ve yeni V2 ağ geçidi için özel bir IP adresi oluşturur. V2 ağ geçitleri Şu anda yalnızca özel IP adreslerini desteklemez.
* Harf, rakam, kısa çizgi ve alt çizgi dışında bir şey içeren adlara sahip üstbilgiler uygulamanıza geçirilmiyor. Bu, üst bilgi değerleri değil yalnızca üst bilgi adları için geçerlidir. Bu, v1 'den bir son değişiklik.

## <a name="download-the-script"></a>Betiği indir

[PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureAppGWMigration)geçiş betiğini indirin.

## <a name="use-the-script"></a>Betiği kullan

Yerel PowerShell ortamınız kuruluma ve tercihlerinize bağlı olarak sizin için iki seçenek vardır:

* Azure az modules yüklü değilse veya Azure az modüllerini kaldırmayı bilmiyorsanız, en iyi seçenek `Install-Script` betiği çalıştırmak için seçeneğini kullanmaktır.
* Azure az modules tutmanız gerekiyorsa, en iyi sonuç, betiği indirmek ve doğrudan çalıştırmak olacaktır.

Azure az modules yüklü olup olmadığınızı öğrenmek için çalıştırın `Get-InstalledModule -Name az` . Yüklü az modül görmüyorsanız, `Install-Script` yöntemini kullanabilirsiniz.

### <a name="install-using-the-install-script-method"></a>Install-Script yöntemi kullanarak Install

Bu seçeneği kullanmak için, bilgisayarınızda Azure az modules yüklü olmamalıdır. Yüklüyse, aşağıdaki komut bir hata görüntüler. Azure az modüller ' i kaldırabilir veya betiği el ile indirmek ve çalıştırmak için diğer seçeneği kullanabilirsiniz.
  
En son sürümü almak için betiği aşağıdaki komutla çalıştırın:

`Install-Script -Name AzureAppGWMigration -Force`

Bu komut ayrıca gerekli az modülleri de yüklüyor.  

### <a name="install-using-the-script-directly"></a>Betiği kullanarak doğrudan yüklemeyi

Bazı Azure az modülleriniz varsa ve bunları kaldıramıyorsanız (veya kaldırmak istemiyorsanız), betik indirme bağlantısındaki **El Ile indir** sekmesini kullanarak betiği el ile indirebilirsiniz. Betik, ham nupkg dosyası olarak indirilir. Betiği bu nupkg dosyasından yüklemek için bkz. [El Ile paket indirme](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Betiği çalıştırmak için:

1. `Connect-AzAccount`Azure 'a bağlanmak için kullanın.

1. `Import-Module Az`Az modülleri içeri aktarmak için kullanın.

1. `Get-Help AzureAppGWMigration.ps1`Gerekli parametreleri incelemek için ' i çalıştırın:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Betik için Parametreler:
   * **RESOURCEID: [dize]: gerekli** -bu, mevcut standart v1 veya WAF v1 ağ geçidiniz Için Azure kaynak kimliğidir. Bu dize değerini bulmak için Azure portal gidin, Application Gateway veya WAF kaynağını seçin ve ağ geçidinin **Özellikler** bağlantısına tıklayın. Kaynak KIMLIĞI bu sayfada bulunur.

     Kaynak KIMLIĞINI almak için aşağıdaki Azure PowerShell komutlarını da çalıştırabilirsiniz:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **Subnetaddressrange: [dize]: gereklidir** -bu, yeni V2 ağ geçidinizi içeren yeni bir alt ağ için ayrılan (veya ayırmak ISTEDIĞINIZ) IP adres alanıdır. Bu, CıDR gösteriminde belirtilmelidir. Örneğin: 10.0.0.0/24. Bu alt ağı önceden oluşturmanız gerekmez. Bu komut dosyası, yoksa sizin için oluşturur.
   * **Appgwname: [dize]: Isteğe bağlı**. Bu, yeni Standard_v2 veya ağ geçidi WAF_v2 için ad olarak kullanmak üzere belirttiğiniz bir dizedir. Bu parametre sağlanmazsa, mevcut v1 ağ geçidinizin adı, sonek *_v2* eklenmiş olarak kullanılır.
   * **Sslcertificates: [PSApplicationGatewaySslCertificate]: Isteğe bağlı**.  V1 ağ Geçidinizden TLS/SSL sertifikalarını temsil etmek için oluşturduğunuz PSApplicationGatewaySslCertificate nesnelerinin virgülle ayrılmış bir listesi, yeni V2 ağ geçidine yüklenmiş olmalıdır. Standart v1 veya WAF v1 ağ geçidiniz için yapılandırılmış her bir TLS/SSL sertifikalarınızın her biri için burada gösterilen komut aracılığıyla yeni bir PSApplicationGatewaySslCertificate nesnesi oluşturabilirsiniz `New-AzApplicationGatewaySslCertificate` . TLS/SSL sertifika dosyanızın ve parolanın yolu gereklidir.

     Bu parametre yalnızca v1 Gateway veya WAF için HTTPS dinleyicilerinin yapılandırılmış olması durumunda isteğe bağlıdır. En az bir HTTPS dinleyicisi kurulumuna sahipseniz, bu parametreyi belirtmeniz gerekir.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     `$mySslCert1, $mySslCert2`Önceki örnekte, betikteki Bu parametre için değer olarak (virgülle ayrılmış) geçiş yapabilirsiniz.
   * **Trustedrootcertificates: [PSApplicationGatewayTrustedRootCertificate]: Isteğe bağlı**. V2 ağ Geçidinizden arka uç örneklerinizin kimlik doğrulaması için [güvenilir kök sertifikaları](ssl-overview.md) temsil etmek üzere oluşturduğunuz PSApplicationGatewayTrustedRootCertificate nesnelerinin virgülle ayrılmış bir listesi.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      PSApplicationGatewayTrustedRootCertificate nesnelerinin bir listesini oluşturmak için, bkz. [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate).
   * **Privateıpaddress: [dize]: Isteğe bağlı**. Yeni v2 ağ geçidiniz ile ilişkilendirmek istediğiniz belirli bir özel IP adresi.  Bu, yeni V2 ağ geçidiniz için ayırdığınız VNet 'ten olmalıdır. Bu belirtilmemişse, betik V2 ağ geçidiniz için özel bir IP adresi ayırır.
   * **Publicıpresourceıd: [dize]: Isteğe bağlı**. Yeni v2 ağ geçidine ayırmak istediğiniz aboneliğinizdeki mevcut genel IP adresi (Standart SKU) kaynağının RESOURCEID değeri. Bu belirtilmemişse, komut dosyası aynı kaynak grubunda yeni bir genel IP ayırır. Ad, V2 ağ geçidinin adı *-IP* olarak eklenir.
   * **Validatemigration: [anahtar]: Isteğe bağlı**. Betiğin V2 ağ geçidi oluşturulduktan ve yapılandırma kopyasından sonra bazı temel yapılandırma karşılaştırma doğrulamaları kullanmasını istiyorsanız bu parametreyi kullanın. Varsayılan olarak, doğrulama yapılmaz.
   * **Enableotomatik ölçeklendirme: [anahtar]: Isteğe bağlı**. Betiğin oluşturulduktan sonra yeni V2 ağ geçidinde otomatik ölçeklendirmeyi etkinleştirmesini istiyorsanız bu parametreyi kullanın. Varsayılan olarak otomatik ölçeklendirme devre dışıdır. Daha sonra yeni oluşturulan V2 ağ geçidinde el ile etkinleştirebilirsiniz.

1. Uygun parametreleri kullanarak betiği çalıştırın. Tamamlanması beş ila yedi dakika sürebilir.

    **Örnek**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>İstemci trafiğini geçirme

İlk olarak, betiğin v1 ağ Geçidinizden başarıyla geçirilen tam yapılandırmayla yeni bir V2 ağ geçidini başarıyla oluşturduğunu kontrol edin. Bunu Azure portal doğrulayabilirsiniz.

Ayrıca, V2 ağ geçidinden el ile test olarak küçük miktarda trafik gönderebilirsiniz.
  
Burada, geçerli uygulama ağ geçidinizin (Standart) istemci trafiği alabileceği ve her biri için önerdiğimiz birkaç senaryo verilmiştir:

* **Standart v1 veya WAF v1 ağ geçidiniz ile ilişkili ön uç IP adresini (örneğin, bir kayıt kullanarak) gösteren özel BIR DNS bölgesi (örneğin, contoso.com)**.

    DNS kaydınızı, Standard_v2 Application Gateway ile ilişkili olan ön uç IP 'sini veya DNS etiketini işaret etmek üzere güncelleştirebilirsiniz. DNS kaydınıza yapılandırılan TTL 'ye bağlı olarak, tüm istemci trafiğiniz yeni V2 ağ geçidinize geçirilmesi biraz zaman alabilir.
* **V1 ağ geçidiniz ile ILIŞKILI DNS etiketine işaret eden (örneğin: CNAME kaydı kullanarak *myappgw.eastus.cloudapp.Azure.com* ) özel bir DNS bölgesi**.

   İki seçeneğiniz vardır:

  * Uygulama ağ geçidiniz üzerinde genel IP adresleri kullanıyorsanız, trafiği (ağırlıklı trafik yönlendirme yöntemi) yeni V2 ağ geçidine artımlı yönlendirmek için bir Traffic Manager profili kullanarak denetimli, ayrıntılı bir geçiş yapabilirsiniz.

    Bu, hem v1 hem de v2 uygulama ağ geçitlerinin DNS etiketlerini [Traffic Manager profiline](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)ekleyerek ve özel DNS kaydınızı (örneğin, `www.contoso.com` contoso.trafficmanager.net) Traffic Manager etki alanına (örneğin,) ekleyerek yapabilirsiniz.
  * Ya da, özel etki alanı DNS kaydınızı, yeni v2 Application Gateway 'in DNS etiketini işaret etmek üzere güncelleştirebilirsiniz. DNS kaydınıza yapılandırılan TTL 'ye bağlı olarak, tüm istemci trafiğiniz yeni V2 ağ geçidinize geçirilmesi biraz zaman alabilir.
* **İstemcileriniz, uygulama ağ geçidinizin ön uç IP adresine bağlanır**.

   İstemcilerinizi, yeni oluşturulan v2 uygulama ağ geçidiyle ilişkili IP adreslerini kullanacak şekilde güncelleştirin. IP adreslerini doğrudan kullanmanızı öneririz. Kendi özel DNS bölgeniz için CNAME olarak kullanabileceğiniz (örneğin, contoso.com), uygulama ağ geçidiniz ile ilişkili DNS adı etiketini (örneğin, yourgateway.eastus.cloudapp.azure.com) kullanmayı göz önünde bulundurun.

## <a name="common-questions"></a>Sık sorulan sorular

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Yapılandırmayı v1 'den v2 'ye geçirmek için Azure PowerShell betiğiyle ilgili herhangi bir sınırlama var mı?

Evet. Bkz. [Uyarılar/sınırlamalar](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Bu makale ve Azure PowerShell betiği Application Gateway WAF ürünü için de geçerlidir mi? 

Evet.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell betiği, v1 ağ geçidden yeni oluşturulan V2 ağ geçidine trafik üzerinde de geçiş yapar mi?

Hayır. Azure PowerShell betiği yalnızca yapılandırmayı geçirir. Gerçek trafik geçişi, sizin ve denetiminizin sorumluluğundadır.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>, Azure PowerShell betiği tarafından oluşturulan yeni V2 ağ geçidi, şu anda v1 ağ geçidim tarafından sunulan tüm trafiği işleyecek şekilde boyutlandırılabilir mı?

Azure PowerShell betiği, mevcut v1 ağ geçidinizdeki trafiği işlemek için uygun boyutta yeni bir V2 ağ geçidi oluşturur. Otomatik ölçeklendirme varsayılan olarak devre dışıdır, ancak betiği çalıştırdığınızda otomatik ölçeklendirmeyi etkinleştirebilirsiniz.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Azure depolama 'ya Günlükler göndermek için v1 ağ Geçidimi yapılandırdım. Komut dosyası bu yapılandırmayı v2 için de çoğaltsın mı?

Hayır. Betik, v2 için bu yapılandırmayı çoğaltmaz. Günlük yapılandırmasını geçirilen V2 ağ geçidine ayrı olarak eklemeniz gerekir.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Bu betik, Azure Anahtar Kasası 'na yüklenen sertifikaları destekliyor mu?

Hayır. Şu anda betik, anahtar kasasındaki sertifikaları desteklemez. Bununla birlikte, gelecekteki bir sürüm için bu kabul edilir.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Bu betiği kullanmayla ilgili bazı sorunlarla karşılaştım. Nasıl yardım alabilirim?
  
"Yapılandırma ve kurulum/v2 SKU 'suna geçiş" konusunun altında Azure desteğine başvurabilirsiniz. [Burada Azure desteği](https://azure.microsoft.com/support/options/)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

[Application Gateway v2 hakkında bilgi edinin](application-gateway-autoscaling-zone-redundant.md)
