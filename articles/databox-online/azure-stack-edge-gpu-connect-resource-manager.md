---
title: Azure Stack Edge Pro GPU cihazınızda Azure Resource Manager bağlanma
description: Azure PowerShell kullanarak Azure Stack Edge Pro GPU 'unuzda çalışan Azure Resource Manager nasıl bağlanabileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e244c93fbe809c52eff1c42c284ce2e8389bbcc4
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553592"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro cihazınızda Azure Resource Manager bağlanma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager, Azure aboneliğinizdeki kaynakları oluşturmanızı, güncelleştirmenizi ve silmenizi sağlayan bir yönetim katmanı sunar. Azure Stack Edge Pro cihazı, yerel bir abonelikte VM 'Ler oluşturmak, güncelleştirmek ve silmek için aynı Azure Resource Manager API 'Leri destekler. Bu destek, cihazı bulutla tutarlı bir şekilde yönetmenizi sağlar. 

Bu öğreticide, Azure PowerShell kullanarak Azure Resource Manager aracılığıyla Azure Stack Edge Pro cihazınızdan yerel API 'lere nasıl bağlanabileceği açıklanmaktadır.

## <a name="about-azure-resource-manager"></a>Azure Resource Manager hakkında

Azure Resource Manager, Azure Stack Edge Pro cihaz API 'sini çağırmak ve VM oluşturma, güncelleştirme ve silme gibi işlemleri gerçekleştirmek için tutarlı bir yönetim katmanı sağlar. Azure Resource Manager mimarisi aşağıdaki diyagramda ayrıntılıdır.

![Azure Resource Manager diyagramı](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro cihazındaki uç noktalar

Aşağıdaki tabloda, cihazınızda sunulan çeşitli uç noktalar, desteklenen protokoller ve bu uç noktalara erişmek için bağlantı noktaları özetlenmektedir. Makalenin tamamında, bu uç noktalara yönelik başvuruları bulacaksınız.

| # | Uç Nokta | Desteklenen protokoller | Kullanılan bağlantı noktası | Kullanıldığı yerler |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Otomasyon için Azure Resource Manager 'e bağlanmak için |
| 2. | Güvenlik belirteci hizmeti | https | 443 | Erişim ve yenileme belirteçleri aracılığıyla kimlik doğrulaması yapmak için |
| 3. | Blob | https | 443 | REST aracılığıyla blob depolamaya bağlanmak için |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Azure Resource Manager iş akışına bağlanılıyor

Azure Resource Manager kullanarak cihazın yerel API 'Lerine bağlanma işlemi aşağıdaki adımları gerektirir:

| Indan # | Bu adımı gerçekleştirirsiniz... | .. Bu konumda. |
| --- | --- | --- |
| 1. | [Azure Stack Edge Pro cihazınızı yapılandırma](#step-1-configure-azure-stack-edge-pro-device) | Yerel Web Kullanıcı arabirimi |
| 2. | [Sertifika oluşturma ve yüklemeyi](#step-2-create-and-install-certificates) | Windows istemcisi/yerel Web Kullanıcı arabirimi |
| 3. | [Önkoşulları gözden geçirin ve yapılandırın](#step-3-install-powershell-on-the-client) | Windows istemcisi |
| 4. | [İstemcide Azure PowerShell ayarlama](#step-4-set-up-azure-powershell-on-the-client) | Windows istemcisi |
| 5. | [Uç nokta adı çözümlemesi için konak dosyasını Değiştir](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows istemcisi veya DNS sunucusu |
| 6. | [Uç nokta adının çözümlendiğini denetleyin](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows istemcisi |
| 7. | [Azure Resource Manager bağlantısını doğrulamak için Azure PowerShell cmdlet 'lerini kullanın](#step-7-set-azure-resource-manager-environment) | Windows istemcisi |

Aşağıdaki bölümler Azure Resource Manager bağlanmada yukarıdaki adımların her birini ayrıntılandırır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, Azure Resource Manager aracılığıyla cihaza bağlanmak için kullanılan istemcinin TLS 1,2 kullanmasını sağlayın. Daha fazla bilgi için [Windows ISTEMCISINDE TLS 1,2 yapılandırma Azure Stack Edge Pro cihazına erişme](azure-stack-edge-gpu-configure-tls-settings.md)bölümüne gidin.

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>1. Adım: Azure Stack Edge Pro cihazını yapılandırma 

Azure Stack Edge Pro cihazınızın yerel Web Kullanıcı arabiriminde aşağıdaki adımları uygulayın.

1. Azure Stack Edge Pro cihazınız için ağ ayarlarını doldurun. 

    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfası](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Cihazın IP adresini bir yere göz önünde alın. Daha sonra bu IP 'yi kullanacaksınız.

2. Cihaz adı ve DNS etki alanını **cihaz** sayfasından yapılandırın. Daha sonra kullanacağınız cihaz adını ve DNS etki alanını bir yere göz önünde bulabilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "cihaz" sayfası](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Cihaz adı, DNS etki alanı, ortaya çıkarılan uç noktaları oluşturmak için kullanılır.
    > Yerel Web Kullanıcı arabirimindeki **cihaz** sayfasından Azure Resource Manager ve BLOB uç noktalarını kullanın.


## <a name="step-2-create-and-install-certificates"></a>2. Adım: sertifika oluşturma ve yüklemeyi

Sertifikalar, iletişiminizin güvenilir olmasını güvence altına aldığından emin olur. Azure Stack Edge Pro cihazında, otomatik olarak imzalanan gereç, blob ve Azure Resource Manager sertifikaları otomatik olarak oluşturulur. İsteğe bağlı olarak, kendi imzalı Blobun ve Azure Resource Manager sertifikaları da alabilirsiniz.

Kendi imzalı bir sertifikayı aldığınızda, sertifikanın karşılık gelen imzalama zinciri de gereklidir. Cihazdaki imzalama zinciri, Azure Resource Manager ve BLOB sertifikaları için, istemci makinesinde karşılık gelen sertifikalara da kimlik doğrulaması ve cihazla iletişim kurmak için ihtiyaç duyarsınız.

Azure Resource Manager bağlanmak için, imzalama zinciri ve uç nokta sertifikaları oluşturmanız veya almanız, bu sertifikaları Windows istemcinize aktarmanız ve son olarak bu sertifikaları cihaza yüklemeniz gerekecektir.

### <a name="create-certificates-optional"></a>Sertifika Oluştur (Isteğe bağlı)

Yalnızca test ve geliştirme kullanımı için, yerel sisteminizde sertifika oluşturmak üzere Windows PowerShell 'i kullanabilirsiniz. İstemci için sertifikaları oluştururken şu yönergeleri izleyin:

1. Önce imzalama zinciri için bir kök sertifika oluşturmanız gerekir. Daha fazla bilgi için bkz. [imzalama zinciri sertifikaları oluşturma](azure-stack-edge-gpu-manage-certificates.md#create-signing-chain-certificate)adımları.

2. Blob ve Azure Resource Manager için uç nokta sertifikalarını daha sonra oluşturabilirsiniz. Bu uç noktaları, yerel Web Kullanıcı arabirimindeki **cihaz** sayfasından edinebilirsiniz. [Uç nokta sertifikaları oluşturma](azure-stack-edge-gpu-manage-certificates.md#create-signed-endpoint-certificates)adımlarına bakın.

3. Tüm bu sertifikalar için, konu adı ve konu diğer adının aşağıdaki yönergelere uygun olduğundan emin olun:

    |Tür |Konu adı (SN)  |Konu alternatif adı (SAN)  |Konu adı örneği |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob depolama|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Her iki uç nokta için birden çok SAN tek sertifika|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Sertifikalar hakkında daha fazla bilgi için [sertifikaları yönetme](azure-stack-edge-gpu-manage-certificates.md)sayfasına gidin.

### <a name="upload-certificates-on-the-device"></a>Cihaza sertifika yükleme

Önceki adımda oluşturduğunuz sertifikalar, istemcinizdeki kişisel mağazada yer alır. Bu sertifikaların, daha sonra cihazınıza yüklenebilen uygun biçim dosyalarına istemcinizi aktarılması gerekir.

1. Kök sertifika, *. cer* dosya uzantısına sahıp bir der biçim dosyası olarak aktarılmalıdır. Ayrıntılı adımlar için bkz [. sertifikaları. cer biçimli dosya olarak dışarı aktarma](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-der-format).

2. Uç nokta sertifikaları, özel anahtarlara sahip *. pfx* dosyaları olarak verilmelidir. Ayrıntılı adımlar için bkz [. sertifikaları özel anahtarlarla. pfx dosyası olarak dışarı aktarma](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Kök ve uç nokta sertifikaları, yerel Web Kullanıcı arabirimindeki **Sertifikalar** sayfasında **+ sertifika ekle** seçeneği kullanılarak cihaza yüklenir. Sertifikaları karşıya yüklemek için, [sertifikaları karşıya yükleme](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)' deki adımları izleyin.


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Azure PowerShell çalıştıran istemcideki sertifikaları içeri aktarın

Azure Resource Manager API 'Leri çağırabileceğiniz Windows istemcisi, cihazla güven sağlamak için gereklidir. Bu uçta, önceki adımda oluşturduğunuz sertifikaların, Windows istemcinizdeki uygun sertifika deposuna aktarılması gerekir.

1. *. Cer* UZANTıSıNA sahip der biçimi olarak verdiğiniz kök sertifika, artık Istemci sisteminizdeki güvenilen kök sertifika yetkililerine içeri aktarılmalıdır. Ayrıntılı adımlar için bkz [. sertifikaları Güvenilen kök sertifika yetkilileri deposuna aktarma.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)

2. *. Pfx* olarak verdiğiniz uç nokta sertifikaları *. cer* olarak verilmelidir. Bu *. cer* daha sonra sisteminizdeki **Kişisel** sertifika deposuna aktarılır. Ayrıntılı adımlar için bkz. [sertifikaları Kişisel depoya alma](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>3. Adım: PowerShell 'i istemciye yüklemeyi 

Windows istemcinizin aşağıdaki önkoşulları karşılaması gerekir:

1. PowerShell sürüm 5,0 ' i çalıştırın. PowerShell sürümü 5,0 olmalıdır. PowerShell Core desteklenmiyor. Sisteminizdeki PowerShell sürümünü denetlemek için aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    **Ana** sürümü karşılaştırın ve 5,0 veya sonraki bir sürüm olduğundan emin olun.

    Sürümünüz eskiyse bkz. [Windows PowerShell'in mevcut sürümünü yükseltme](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    \'PowerShell 5,0 ' i yoksa, [Windows PowerShell 'i yükleme](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true)' yi izleyin.

    Örnek çıktı aşağıda gösterilmiştir.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. PowerShell Galerisi erişebilirsiniz.

    PowerShell 'i yönetici olarak çalıştırın. ' `PSGallery` Nin bir depo olarak kaydedilip kaydedilmediği doğrulayın.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Örnek çıktı aşağıda gösterilmiştir.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Deponuzun güvenilir olmaması veya daha fazla bilgiye ihtiyacınız varsa bkz. [PowerShell Galerisi erişilebilirliği doğrulama](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>4. Adım: istemcide Azure PowerShell ayarlama 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Azure PowerShell modüllerini istemciyle birlikte çalışacak şekilde yükleyeceksiniz.

    a. PowerShell'i yönetici olarak çalıştırın. PowerShell galerisine erişmeniz gerekir. 


    b. Gerekli Azure PowerShell modüllerini PowerShell Galerisi yüklemek için şu komutu çalıştırın:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Yüklemenin sonunda çalışan Azure-RM Modül sürümü 2.5.0 sahip olduğunuzdan emin olun. 
    Gerekli sürümle eşleşmeyen Azure-RM modülünün mevcut bir sürümüne sahipseniz, aşağıdaki komutu kullanarak kaldırın:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Şimdi gerekli sürümü yeniden yüklemeniz gerekir.
   

Azurerd sürüm 2.5.0 modüllerinin başarıyla yüklendiğini gösteren örnek bir çıktı aşağıda gösterilmiştir.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>5. Adım: uç nokta adı çözümlemesi için ana bilgisayar dosyasını değiştirme 

Şimdi, cihazın yerel Web Kullanıcı arabiriminde tanımladığınız Azure tutarlı VIP 'yi şu şekilde ekleyeceksiniz:

- İstemci üzerindeki konak dosyası veya,
- DNS sunucusu yapılandırması

> [!IMPORTANT]
> Uç nokta adı çözümlemesi için DNS sunucusu yapılandırmasını değiştirmenizi öneririz.

Cihaza bağlanmak için kullandığınız Windows istemcisinde aşağıdaki adımları uygulayın:

1. **Not defteri 'ni** yönetici olarak başlatın ve ardından C:\windows\system32\drivers\etckonumunda bulunan **Hosts** dosyasını açın.

    ![Windows Gezgini ana bilgisayarları dosyası](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Aşağıdaki girdileri, cihazınız için uygun değerlerle değiştirerek **ana bilgisayar** dosyanıza ekleyin: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > Hosts dosyasındaki giriş, daha sonraki bir adımda Azure Resource Manager bağlanmak için belirtilen tam olarak eşleşmelidir. DNS etki alanı girişinin tümü küçük harfle geldiğinden emin olun.

    Cihaz IP 'sini önceki bir adımda Yerel Web kullanıcı arabiriminden kaydettiniz.

    Oturum açma. \<appliance name\> .\<DNS domain\> Giriş, güvenlik belirteci hizmeti (STS) bitiş noktasıdır. STS, güvenlik belirteçlerinin oluşturulması, doğrulanması, yenilenmesi ve iptalinden sorumludur. Güvenlik belirteci hizmeti, cihaz ile istemci arasındaki sürekli iletişim için kullanılan erişim belirtecini ve yenileme belirtecini oluşturmak için kullanılır.

3. Başvuru için aşağıdaki görüntüyü kullanın. **Hosts** dosyasını kaydedin.

    ![Not defteri 'nde Hosts dosyası](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>6. Adım: istemci üzerinde uç nokta adı çözümlemesini doğrulama

Azure tutarlı VIP 'ye bağlanmak için kullandığınız istemcide uç nokta adının çözümlenip çözümlenmediğini denetleyin.

1. Uç nokta adının çözümlenip çözümlenmediğini denetlemek için ping.exe komut satırı yardımcı programını kullanabilirsiniz. Bir IP adresi verildiğinde, ping komutu, yeniden izlediğiniz bilgisayarın TCP/IP ana bilgisayar adını döndürür \' .

    `-a`Aşağıdaki örnekte gösterildiği gibi anahtarı komut satırına ekleyin. Ana bilgisayar adı returnable ise, yanıtta bu büyük olasılıkla değerli bilgiler de döndürülür.

    ![Komut isteminde ping gönder](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>7. Adım: Azure Resource Manager ortamı ayarlama

Azure Resource Manager ortamını ayarlayın ve cihazınızın Azure Resource Manager aracılığıyla istemci iletişimi için düzgün çalıştığını doğrulayın. Bu doğrulama için aşağıdaki adımları uygulayın:


1. Azure Resource Manager ile doğru şekilde iletişim kurabildiğinizden ve API çağrılarının Azure Resource Manager için ayrılmış olan 443 numaralı bağlantı noktası üzerinden geçtiğinden emin olmak için `Add-AzureRmEnvironment` cmdlet'ini kullanın.

    `Add-AzureRmEnvironment` cmdlet'i, Azure Resource Manager cmdlet'lerinin yeni bir Azure Resource Manager örneğiyle bağlantı kurmasını sağlamak için uç noktalar ve meta veriler ekler. 


    > [!IMPORTANT]
    > Aşağıdaki cmdlet 'te sağladığınız Azure Resource Manager uç nokta URL 'SI büyük/küçük harfe duyarlıdır. Uç nokta URL 'sinin tamamen küçük harfle geldiğinden ve Hosts dosyasında sağladıklarınızın eşleştiğinden emin olun. Durum eşleşmiyorsa bir hata görürsünüz.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Örnek bir çıktı aşağıda gösterilmiştir:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Ortamı Azure Stack Edge Pro, Azure Resource Manager çağrıları için kullanılacak bağlantı noktasını da 443 olarak ayarlayın. Ortamı iki şekilde tanımlarsınız:

    - Ortamı ayarlayın. Aşağıdaki komutu yazın:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Daha fazla bilgi için [set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)adresine gidin.

    - Yürütmeniz gereken her cmdlet için ortamı satır içi olarak tanımlayın. Bu, tüm API çağrılarının doğru ortamda gezinmesini sağlar. Varsayılan olarak, çağrılar Azure genel kullanıma açıktır, ancak bunların Azure Stack Edge Pro cihazı için ayarladığınız ortamda gitmesini istersiniz.

    - [Azurerd ortamlarını değiştirme](#switch-environments)hakkında daha fazla bilgi için bkz..

2. Azure Resource Manager bağlantılarının kimliğini doğrulamak için yerel cihaz API'lerini çağırın. 

    1. Bu kimlik bilgileri yerel makine hesabına yöneliktir ve yalnızca API erişimi için kullanılır.

    2. `login-AzureRMAccount`Veya aracılığıyla `Connect-AzureRMAccount` komutunu bağlayabilirsiniz. 

        1. Oturum açmak için aşağıdaki komutu yazın. Bu örnekteki kiracı KIMLIĞI sabit kodlanmış-c0257de7-538f-415c-993a-1b87a031879d. Aşağıdaki Kullanıcı adını ve parolayı kullanın.

            - **Kullanıcı adı**  -  *Edgearmuser*

            - **Parola**  -  [Azure Resource Manager parolasını ayarlayın](azure-stack-edge-gpu-set-azure-resource-manager-password.md) ve oturum açmak için bu parolayı kullanın. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Oturum açmanın alternatif bir yolu da `login-AzureRmAccount` cmdlet 'ini kullanmaktır. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Komutun örnek bir çıktısı aşağıda verilmiştir. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> Azure Resource Manager bağlantı her 1,5 saatte bir dolar veya Azure Stack Edge Pro cihazınız yeniden başlatılır. Bu durumda, çalıştırdığınız tüm cmdlet 'ler artık Azure 'a bağlı olmayan etkiye hata iletileri döndürür. Yeniden oturum açmanız gerekecektir.

## <a name="switch-environments"></a>Ortamları değiştirme

`Disconnect-AzureRmAccount`Farklı bir anahtara geçmek için komutunu çalıştırın `AzureRmEnvironment` . 

`Set-AzureRmEnvironment`Kullanarak ve kullanırsanız `Login-AzureRmAccount` `Disconnect-AzureRmAccount` , ortam gerçekte geçiş yapmamıştır.  

Aşağıdaki örneklerde, iki ortam arasında nasıl geçiş yapılacağı gösterilmektedir `AzDBE1` `AzDBE2` .

İlk olarak, istemcinizdeki tüm mevcut ortamları listeleyin.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Daha sonra, Azure Resource Manager şu anda hangi ortama bağlı olduğunu alın.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Artık diğer ortama geçmeden önce geçerli ortamla bağlantısını kesmeniz gerekir.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Diğer ortamda oturum açın. Aşağıda örnek çıktı gösterilmektedir.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Hangi ortamın bağlı olduğunu doğrulamak için bu cmdlet 'i çalıştırın.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Şimdi amaçlanan ortama geçtiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro cihazınızda VM 'Leri dağıtın](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
