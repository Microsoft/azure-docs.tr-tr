---
title: Azure DevTest Labs Uzak Masaüstü Ağ Geçidi kullanmak için laboratuvar yapılandırma
description: RDP bağlantı noktasını açığa çıkarmak zorunda kalmadan laboratuvar VM 'lerine güvenli erişim sağlamak için Azure DevTest Labs bir laboratuvarı Uzak Masaüstü ağ geçidiyle nasıl yapılandıracağınızı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b15d4d39199c1a30eae292ece67f4553b656f530
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639594"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Azure DevTest Labs ' de laboratuvarınızı Uzak Masaüstü Ağ geçidini kullanacak şekilde yapılandırma
Azure DevTest Labs, laboratuvarınız için bir Uzak Masaüstü Ağ Geçidi yapılandırarak, RDP bağlantı noktasını açığa çıkarmak zorunda kalmadan laboratuvar sanal makinelerine (VM 'Ler) güvenli erişim sağlayabilirsiniz. Laboratuvar, laboratuvar kullanıcılarınızın erişimi olan tüm sanal makineleri görüntülemesi ve bunlara bağlanabilmesi için merkezi bir yer sağlar. **Sanal makine** sayfasındaki **Bağlan** düğmesi, makineye bağlanmak için AÇABILECEĞINIZ makineye özel bir RDP dosyası oluşturur. Laboratuvarınızı Uzak Masaüstü ağ geçidine bağlayarak RDP bağlantısını daha da özelleştirebilir ve koruyabilirsiniz. 

Laboratuvar kullanıcısı ağ geçidi makinesinde doğrudan kimlik doğrulaması yaptığından veya makinelere bağlanmak için etki alanına katılmış bir ağ geçidi makinesinde Şirket kimlik bilgilerini kullanabileceğinden bu yaklaşım daha güvenlidir. Laboratuvar Ayrıca, kullanıcıların İnternet 'e açık olan RDP bağlantı noktası olmadan laboratuvar sanal makinelerine bağlanmasına izin veren ağ geçidi makinesine belirteç kimlik doğrulamasının kullanılmasını da destekler. Bu makalede, laboratuvar makinelerine bağlanmak için belirteç kimlik doğrulaması kullanan bir laboratuvarın nasıl ayarlanacağı hakkında bir örnek gösterilmektedir.

## <a name="architecture-of-the-solution"></a>Çözümün mimarisi

![Çözümün mimarisi](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. **Bağlan** düğmesini SEÇTIĞINIZDE, [RDP dosyası içeriklerini al](/rest/api/dtl/virtualmachines/getrdpfilecontents) eylemi çağrılır. 1. 
1. RDP dosyası içeriklerini al eylemi, `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` bir kimlik doğrulama belirteci istemek için çağırır.
    1. `{gateway-hostname}` , Azure portal laboratuvarınızın **Laboratuvar ayarları** sayfasında belirtilen ağ geçidi ana bilgisayar adıdır. 
    1. `{lab-machine-name}` , bağlanmaya çalıştığınız makinenin adıdır.
    1. `{port-number}` , bağlantının yapılması gereken bağlantı noktasıdır. Genellikle bu bağlantı noktası 3389 ' dir. Laboratuvar VM 'si DevTest Labs 'de [PAYLAŞıLAN IP](devtest-lab-shared-ip.md) özelliğini kullanıyorsa, bağlantı noktası farklı olur.
1. Uzak Masaüstü Ağ Geçidi, `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` kimlik doğrulama belirtecini oluşturmak için bir Azure işlevine çağrıyı erteler. DevTest Labs hizmeti, istek üst bilgisinde işlev anahtarını otomatik olarak ekler. İşlev anahtarı, laboratuvarın anahtar kasasına kaydedilir. Laboratuvar için **Laboratuvar ayarları** sayfasında **ağ geçidi belirteci parolası** olarak gösterilecek gizli anahtar adı.
1. Azure işlevinin, ağ geçidi makinesine sertifika tabanlı belirteç kimlik doğrulaması için bir belirteç döndürmesi beklenmektedir.  
1. RDP dosyası içeriklerini al eylemi, kimlik doğrulama bilgileri de dahil olmak üzere tüm RDP dosyalarını döndürür.
1. RDP dosyasını tercih ettiğiniz RDP bağlantı programınızı kullanarak açarsınız. Tüm RDP bağlantı programlarının belirteç kimlik doğrulamasını desteklemediğini unutmayın. Kimlik doğrulama belirtecinin, işlev uygulaması tarafından ayarlanan bir sona erme tarihi vardır. Belirtecin süresi dolmadan önce laboratuvar VM 'sine bağlantı oluşturun.
1. Uzak Masaüstü Ağ Geçidi makinesi RDP dosyasındaki belirtecin kimliğini doğruladıktan sonra, bağlantı laboratuvar makinenize iletilir.

### <a name="solution-requirements"></a>Çözüm gereksinimleri
DevTest Labs belirteç kimlik doğrulama özelliğiyle çalışmak için, ağ geçidi makineleri, etki alanı adı hizmetleri (DNS) ve işlevleri için birkaç yapılandırma gereksinimi vardır.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Uzak Masaüstü Ağ Geçidi makineleri için gereksinimler
- HTTPS trafiğini işlemek için ağ geçidi makinesinde TLS/SSL sertifikası yüklü olmalıdır. Sertifika, ağ geçidi grubu için yük dengeleyicinin tam etki alanı adı (FQDN) veya yalnızca bir makine varsa makinenin FQDN 'SI ile aynı olmalıdır. Joker karakter-kart TLS/SSL sertifikaları çalışmıyor.  
- Ağ Geçidi makinbir imzalama sertifikası yüklendi. [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) betiği kullanarak bir imza sertifikası oluşturun.
- Uzak Masaüstü Ağ Geçidi için belirteç kimlik doğrulamasını destekleyen [takılabilir kimlik doğrulama](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) modülünü yükler. Bu tür bir modüle bir örnek, `RDGatewayFedAuth.msi` [System Center VIRTUAL MACHINE Manager (VMM) görüntüleriyle](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true)birlikte gelir. System Center hakkında daha fazla bilgi için bkz. [System Center belgeleri](/system-center/) ve [fiyatlandırma ayrıntıları](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Ağ Geçidi sunucusu, öğesine yapılan istekleri işleyebilir `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    Ağ geçidi-ana bilgisayar adı, yalnızca bir makine varsa, ağ geçidi grubunun yük dengeleyicinin FQDN 'sidir veya makinenin kendisi FQDN 'sidir. , `{lab-machine-name}` Bağlanmaya çalıştığınız laboratuvar makinesinin adı ve `{port-number}` bağlantının oluşturulacağı bağlantı noktasıdır...  Varsayılan olarak, bu bağlantı noktası 3389 ' dir.  Ancak, sanal makine DevTest Labs 'de [PAYLAŞıLAN IP](devtest-lab-shared-ip.md) özelliğini kullanıyorsa, bağlantı noktası farklı olur.
- Internet Information Server (IIS) için [uygulama yönlendirme isteği](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) modülü, `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` istekleri kimlik doğrulama için belirteç alma isteğini işleyen Azure işlevine yönlendirmek için kullanılabilir.


## <a name="requirements-for-azure-function"></a>Azure işlevi için gereksinimler
Azure işlevi, biçimine sahip isteği işler `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` ve ağ geçidi makinelerinde yüklü olan imzalama sertifikasına göre kimlik doğrulama belirtecini döndürür. , `{function-app-uri}` İşlevine erişmek için kullanılan URI 'dir. İşlev anahtarı, isteğin üstbilgisine otomatik olarak geçirilir. Örnek bir işlev için bkz [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) .. 


## <a name="requirements-for-network"></a>Ağ gereksinimleri

- Ağ Geçidi makinelerinde yüklü olan TLS/SSL sertifikasıyla ilişkili FQDN için DNS, trafiği ağ geçidi makinesine veya ağ geçidi makine grubunun yük dengeleyicisine yönlendirmelidir.
- Laboratuvar makinesi özel IP 'Ler kullanıyorsa, aynı sanal ağı paylaşarak veya eşlenmiş sanal ağları kullanarak ağ geçidi makinesinden laboratuvar makinesine bir ağ yolu olmalıdır.

## <a name="configure-the-lab-to-use-token-authentication"></a>Laboratuvar belirtecini belirteç kimlik doğrulaması kullanacak şekilde yapılandırma 
Bu bölümde, bir laboratuvarın belirteç kimlik doğrulamasını destekleyen bir Uzak Masaüstü Ağ Geçidi makinesi kullanmak üzere nasıl yapılandırılacağı gösterilmektedir. Bu bölüm, Uzak Masaüstü Ağ Geçidi grubunun kendisini ayarlamayı kapsamaz. Bu bilgiler için, bu makalenin sonundaki [Uzak Masaüstü Ağ geçidi oluşturma](#sample-to-create-a-remote-desktop-gateway) bölümüne bakın. 

Laboratuvar ayarlarını güncelleştirmeden önce, laboratuvarın anahtar kasasında bir kimlik doğrulama belirteci döndürmek için işlevi başarıyla yürütmek üzere gereken anahtarı saklayın. Azure portal işlevin **Yönetim** sayfasında işlev anahtarı değerini alabilirsiniz. Bir anahtar kasasında gizli dizi kaydetme hakkında daha fazla bilgi için bkz. [Key Vault için gizli dizi ekleme](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Daha sonra kullanmak üzere gizli dizinin adını kaydedin.

Laboratuvarın anahtar kasasının KIMLIĞINI bulmak için aşağıdaki Azure CLı komutunu çalıştırın: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Aşağıdaki adımları kullanarak Laboratuvarı, belirteç kimlik doğrulamasını kullanacak şekilde yapılandırın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
1. Laboratuvarlar listesinden **laboratuvarınızı** seçin.
1. Laboratuvarın sayfasında **yapılandırma ve ilkeler**' i seçin.
1. Sol taraftaki menüde, **Ayarlar** bölümünde **Laboratuvar ayarları**' nı seçin.
1. **Uzak Masaüstü** bölümünde, **ağ geçidi ana bilgisayar** adı alanı için Uzak Masaüstü Hizmetleri ağ geçidi makinesi veya grubu için tam etkı alanı adını (FQDN) veya IP adresini girin. Bu değer, ağ geçidi makinelerinde kullanılan TLS/SSL sertifikasının FQDN 'siyle aynı olmalıdır.

    ![Laboratuvar ayarlarındaki uzak masaüstü seçenekleri](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. **Uzak Masaüstü** bölümünde, **ağ geçidi belirteci** parolası için, daha önce oluşturulan gizli dizi adını girin. Bu değer, işlev anahtarının kendisi değil, laboratuvarın anahtar kasasındaki işlev anahtarını tutan gizli dizi adı değildir.

    ![Laboratuvar ayarlarında ağ geçidi belirteci gizli dizisi](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Kaydet** Değişikliklerine.

    > [!NOTE] 
    > **Kaydet**'e tıklayarak [Uzak Masaüstü Ağ Geçidi lisans koşullarını](https://www.microsoft.com/licensing/product-licensing/products)kabul etmiş olursunuz. Uzak ağ geçidi hakkında daha fazla bilgi için bkz. [hoş geldiniz Uzak Masaüstü Hizmetleri](/windows-server/remote/remote-desktop-services/Welcome-to-rds) ve [uzak masaüstü ortamınızı dağıtma](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Laboratuvarı Otomasyon aracılığıyla yapılandırıyorsanız, **ağ geçidi konak adı** ve **ağ geçidi belirteci gizli** dizi ayarlarını ayarlamak için örnek bir PowerShell betiği için [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) bakın. [GitHub deposu Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) Ayrıca **ağ geçidi ana bilgisayar adı** ve **ağ geçidi belirteci gizli** ayarları ile bir laboratuvar oluşturan veya güncelleştiren bir Azure Resource Manager şablonu sağlar.

## <a name="configure-network-security-group"></a>Ağ güvenlik grubunu yapılandırma
Laboratuvarın daha güvenli olmasını sağlamak için, laboratuvar sanal makineleri tarafından kullanılan sanal ağa bir ağ güvenlik grubu (NSG) eklenebilir. NSG 'yi ayarlama hakkında yönergeler için bkz. [ağ güvenlik grubu oluşturma, değiştirme veya silme](../virtual-network/manage-network-security-group.md).

Burada yalnızca, ağ geçidinden laboratuar makinelerine ulaşmaya yönelik trafiğe izin veren bir NSG örneği verilmiştir. Bu kuraldaki kaynak tek ağ geçidi makinesinin IP adresidir veya ağ geçidi makinelerinin önünde yük dengeleyicinin IP adresidir.

![Ağ güvenlik grubu-kurallar](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Uzak Masaüstü Ağ geçidi oluşturmak için örnek

> [!NOTE] 
> Örnek şablonları kullanarak [Uzak Masaüstü Ağ Geçidi lisans koşullarını](https://www.microsoft.com/licensing/product-licensing/products)kabul etmiş olursunuz. Uzak ağ geçidi hakkında daha fazla bilgi için bkz. [hoş geldiniz Uzak Masaüstü Hizmetleri](/windows-server/remote/remote-desktop-services/Welcome-to-rds) ve [uzak masaüstü ortamınızı dağıtma](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[GitHub deposu Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) , belirteç kimlik doğrulaması ve Uzak Masaüstü Ağ geçidini DevTest Labs ile kullanmak için gereken kaynakları ayarlamaya yardımcı olmak üzere birkaç örnek sağlar. Bu örnekler, ağ geçidi makineleri, laboratuvar ayarları ve işlev uygulaması için Azure Resource Manager şablonlar içerir.

Uzak Masaüstü Ağ Geçidi grubuna yönelik örnek bir çözüm kurmak için aşağıdaki adımları izleyin.

1. İmza sertifikası oluşturun.  [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)çalıştırın. Oluşturulan sertifikanın parmak izini, parolasını ve Base64 kodlamasını kaydedin.
2. Bir TLS/SSL sertifikası alın. TLS/SSL sertifikası ile ilişkili FQDN, denetlediğiniz etki alanı için olmalıdır. Bu sertifika için parmak izini, parolayı ve Base64 kodlamasını kaydedin. PowerShell kullanarak parmak izini almak için aşağıdaki komutları kullanın.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    PowerShell kullanarak Base64 kodlamayı almak için aşağıdaki komutu kullanın.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. Dosyaları şuradan indirin [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    Şablon, aynı temel URI 'deki diğer Kaynak Yöneticisi şablonlarına ve ilgili kaynaklara erişim gerektirir. [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)Ve RDGatewayFedAuth.msi tüm dosyaları depolama hesabındaki bir blob kapsayıcısına kopyalayın.  
4. **Üzerindeazuredeploy.js** dağıtın [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . Şablon aşağıdaki parametreleri alır:
    - adminUsername: gereklidir.  Ağ Geçidi makineleri için Yönetici Kullanıcı adı.
    - adminPassword: gerekli. Ağ Geçidi makineleri için yönetici hesabının parolası.
    - InstanceCount: oluşturulacak ağ geçidi makinesi sayısı.  
    - alwaysOn – oluşturulan Azure Işlevleri uygulamasının bir ısınma durumunda tutulup tutulmayacağını belirtir. Azure Işlevleri uygulamasının tutulması, kullanıcıların kendi laboratuvar VM 'lerine ilk kez bağlanmaya çalıştıklarında gecikme yaşamalarını önler, ancak maliyet etkilerine neden olur.  
    - tokenLifetime: oluşturulan belirtecin geçerli olacağı sürenin uzunluğu. Biçim ss: DD: SS şeklindedir.
    - sslCertificate: ağ geçidi makinesi için TLS/SSL sertifikasının Base64 kodlaması.
    - sslCertificatePassword: ağ geçidi makinesi için TLS/SSL sertifikasının parolası.
    - sslCertificateThumbprint-TLS/SSL sertifikasının yerel sertifika deposunda kimlik için sertifika parmak izi.
    - signCertificate: ağ geçidi makinesi için imza sertifikası için Base64 kodlaması.
    - signCertificatePassword: ağ geçidi makinesi için imza sertifikası parolası.
    - signCertificateThumbprint-imza sertifikasının yerel sertifika deposunda kimlik için sertifika parmak izi.
    - _artifactsLocation – tüm destekleyici kaynakların bulunabileceği URI konumu. Bu değer, göreli bir yol değil, tam nitelikli bir uar olmalıdır.
    - _artifactsLocationSasToken: konum bir Azure depolama hesabsa, destekleyici kaynaklara erişmek için kullanılan paylaşılan erişim Imzası (SAS) belirteci.

    Şablon, aşağıdaki komutu kullanarak Azure CLı kullanılarak dağıtılabilir:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Parametrelerin açıklamaları aşağıda verilmiştir:

    - {Storage-Account-Endpoint}, çalıştırılarak elde edilebilir `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  {Storage-ACCT-Name}, karşıya yüklediğiniz dosyaları tutan depolama hesabının adıdır.  
    - {Container-Name}, karşıya yüklediğiniz dosyaları tutan {Storage-ACCT-Name} içindeki kapsayıcının adıdır.  
    - {SAS-Token}, çalıştırılarak elde edilebilir `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - {Storage-ACCT-Name}, karşıya yüklediğiniz dosyaları tutan depolama hesabının adıdır.  
        - {Container-Name}, karşıya yüklediğiniz dosyaları tutan {Storage-ACCT-Name} içindeki kapsayıcının adıdır.  
        - {UTC-sona erme tarihi}, UTC olarak SAS belirtecinin süresinin dolacağı ve SAS belirtecinin depolama hesabına erişmek için artık kullanılamayan tarihtir.

    GatewayFQDN ve Gatewayıp 'nin değerlerini şablon dağıtım çıktısından kaydedin. Ayrıca, işlev [uygulaması ayarları](../azure-functions/functions-how-to-use-azure-function-app-settings.md) sekmesinde bulunan yeni oluşturulan işlev için işlev anahtarının değerini kaydetmeniz gerekir.
5. DNS 'yi, TLS/SSL sertifikası FQDN 'sinin önceki adımdan Gatewayıp IP adresine yönlendirdiği şekilde yapılandırın.

    Uzak Masaüstü Ağ Geçidi grubu oluşturulduktan ve uygun DNS güncelleştirmeleri yapıldıktan sonra, DevTest Labs 'de bir laboratuvar tarafından kullanılmak üzere hazırız. **Ağ geçidi ana bilgisayar adı** ve **ağ geçidi belirteci gizli** ayarları, dağıttığınız ağ geçidi makineleri kullanacak şekilde yapılandırılmış olmalıdır. 

    > [!NOTE]
    > Laboratuvar makinesi özel IP 'Ler kullanıyorsa, aynı sanal ağı paylaşarak veya eşlenmiş bir sanal ağ kullanarak ağ geçidi makinesinden laboratuvar makinesine bir ağ yolu olmalıdır.

    Hem ağ geçidi hem de laboratuvar yapılandırıldıktan sonra, laboratuvar **kullanıcısı bağlantıda** tıkladığı zaman oluşturulan bağlantı dosyası otomatik olarak belirteç kimlik doğrulaması kullanarak bağlanmak için gereken bilgileri içerir.     

## <a name="next-steps"></a>Sonraki adımlar
Uzak Masaüstü Hizmetleri hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın: [Uzak Masaüstü Hizmetleri belgeleri](/windows-server/remote/remote-desktop-services/Welcome-to-rds)