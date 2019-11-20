---
title: Uyarı ve Azure Işlevleri ile öngörülü ağ izleme yapmak için paket yakalamayı kullanın | Microsoft Docs
description: Bu makalede, Azure ağ Izleyicisi ile bir uyarı tetiklenen paket yakalama oluşturma açıklanmaktadır
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: d894fabf3cfd4c6949aba94d558751bf007356d9
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165149"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Uyarılar ve Azure Işlevleri ile öngörülü ağ izleme için paket yakalamayı kullanma

Ağ Izleyicisi paket yakalama, sanal makinelerin içinde ve dışında trafiği izlemek için yakalama oturumları oluşturur. Yakalama dosyası, yalnızca izlemek istediğiniz trafiği izlemek için tanımlanan bir filtreye sahip olabilir. Bu veriler daha sonra bir Depolama Blobu veya Konuk makinede yerel olarak depolanır.

Bu özellik, Azure Işlevleri gibi diğer otomasyon senaryolarından uzaktan başlatılabilir. Paket yakalama, tanımlı ağ bozuklukları temelinde proaktif yakalamaları çalıştırma yeteneği sağlar. Diğer kullanımlar arasında ağ istatistiklerini toplama, ağ izinsiz kullanım hakkında bilgi alma, istemci-sunucu iletişimlerinde hata ayıklama ve daha fazlası yer alır.

Azure 'da dağıtılan kaynaklar 24/7 çalıştırın. Siz ve çalışanlarınız 24/7 tüm kaynakların durumunu etkin bir şekilde izleyemezsiniz. Örneğin, 2 ' de bir sorun oluşursa ne olur?

Azure ekosistemi içinden ağ Izleyicisi, uyarı ve işlevleri kullanarak ağınızdaki sorunları çözümlemek için veri ve araçlarla proaktif olarak yanıt verebilirsiniz.

![Senaryo][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Azure PowerShell](/powershell/azure/install-Az-ps)en son sürümü.
* Var olan bir ağ Izleyicisi örneği. Henüz bir tane yoksa, [Ağ İzleyicisi 'nin bir örneğini oluşturun](network-watcher-create.md).
* [Windows uzantısı](../virtual-machines/windows/extensions-nwa.md) veya [Linux sanal makine uzantısıyla](../virtual-machines/linux/extensions-nwa.md)Ağ İzleyicisi ile aynı bölgedeki mevcut bir sanal makine.

## <a name="scenario"></a>Senaryo

Bu örnekte, sanal ağınız normalden daha fazla TCP kesimi gönderiyor ve uyarı almak istiyorsunuz. TCP kesimleri burada bir örnek olarak kullanılır, ancak herhangi bir uyarı koşulunu kullanabilirsiniz.

Uyarı aldığınızda, iletişimin neden arttığını anlamak için paket düzeyi verileri almak istersiniz. Ardından, sanal makineyi normal iletişime döndürmek için adımları izleyebilirsiniz.

Bu senaryoda, mevcut bir ağ Izleyicisi örneğine ve geçerli bir sanal makineye sahip bir kaynak grubuna sahip olduğunuz varsayılır.

Aşağıdaki listede yer alan iş akışına genel bir bakış verilmiştir:

1. VM 'niz üzerinde bir uyarı tetiklenir.
1. Uyarı, Azure işlevinizi bir Web Kancası aracılığıyla çağırır.
1. Azure işleviniz uyarıyı işler ve bir ağ Izleyicisi paket yakalama oturumu başlatır.
1. Paket yakalama, VM üzerinde çalışır ve trafik toplar.
1. Paket yakalama dosyası, gözden geçirme ve Tanılama için bir depolama hesabına yüklenir.

Bu işlemi otomatikleştirmek için, VM 'imizde olay gerçekleştiğinde tetiklenecek bir uyarı oluşturur ve bağladık. Ağ izleyicisine çağrı yapmak için de bir işlev oluşturacağız.

Bu senaryo şunları yapar:

* Paket yakalamayı Başlatan bir Azure işlevi oluşturur.
* Bir sanal makinede bir uyarı kuralı oluşturur ve uyarı kuralını Azure işlevini çağıracak şekilde yapılandırır.

## <a name="create-an-azure-function"></a>Azure işlevi oluşturma

İlk adım, uyarıyı işlemek ve bir paket yakalama oluşturmak için bir Azure işlevi oluşturmaktır.

1. [Azure Portal](https://portal.azure.com) **kaynak** > oluşturmaişlemişlevuygulaması > seçin.

    ![İşlev uygulaması oluşturma][1-1]

2. **İşlev uygulaması** dikey penceresinde aşağıdaki değerleri girin ve ardından uygulamayı oluşturmak için **Tamam** ' ı seçin:

    |**Ayar** | **Değer** | **Ayrıntılar** |
    |---|---|---|
    |**Uygulama adı**|PacketCaptureExample|İşlev uygulamasının adı.|
    |**Abonelik**|[Aboneliğiniz] İşlev uygulamasının oluşturulacağı abonelik.||
    |**Kaynak Grubu**|PacketCaptureRG|İşlev uygulamasını içerecek kaynak grubu.|
    |**Barındırma Planı**|Tüketim Planı| İşlev uygulamanızın kullandığı planın türü. Seçenekler, tüketim veya Azure App Service planlardır. |
    |**Location**|Orta ABD| İşlev uygulamasının oluşturulacağı bölge.|
    |**Depolama Hesabı**|otomatik olarak oluşturulan| Azure Işlevlerinin genel amaçlı depolama için ihtiyaç duyacağı depolama hesabı.|

3. **Packetcaptureexample işlevi uygulamalar** dikey penceresinde **işlevler** > **özel işlev** > **+** ' i seçin.

4. **Httptrigger-PowerShell**' i seçin ve ardından kalan bilgileri girin. Son olarak, işlevi oluşturmak için **Oluştur**' u seçin.

    |**Ayar** | **Değer** | **Ayrıntılar** |
    |---|---|---|
    |**Senaryo**|Deneysel|Senaryonun türü|
    |**İşlevinizi adlandırın**|AlertPacketCapturePowerShell|İşlevin adı|
    |**Yetkilendirme düzeyi**|İşlev|İşlevin Yetkilendirme düzeyi|

![İşlevler örneği][functions1]

> [!NOTE]
> PowerShell şablonu deneysel ve tam desteği yok.

Bu örnek için özelleştirmeler gereklidir ve aşağıdaki adımlarda açıklanmıştır.

### <a name="add-modules"></a>Modül Ekle

Ağ Izleyicisi PowerShell cmdlet 'lerini kullanmak için, işlev uygulamasına en son PowerShell modülünü yükleyin.

1. En son Azure PowerShell modüller yüklü olan yerel makinenizde aşağıdaki PowerShell komutunu çalıştırın:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Bu örnek size Azure PowerShell modüllerinizin yerel yolunu sağlar. Bu klasörler sonraki bir adımda kullanılır. Bu senaryoda kullanılan modüller şunlardır:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell klasörleri][functions5]

1. **İşlev uygulaması ayarları** > seçin**App Service Düzenleyicisi git**.

    ![İşlev uygulaması ayarları][functions2]

1. **Alertpacketcapturepowershell** klasörüne sağ tıklayın ve ardından **azuremodules**adlı bir klasör oluşturun. 

4. İhtiyaç duyduğunuz her modül için bir alt klasör oluşturun.

    ![Klasör ve alt klasörler][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. **Az. Network** alt klasörüne sağ tıklayın ve ardından **dosyaları karşıya yükle**' yi seçin. 

6. Azure modülleriniz sayfasına gidin. Yerel **az. Network** klasöründe klasöründeki tüm dosyalar ' ı seçin. Sonra **Tamam**’ı seçin. 

7. **Az. Accounts** ve **az. resources**için bu adımları tekrarlayın.

    ![Dosyaları karşıya yükleme][functions6]

1. İşiniz bittiğinde, her klasörün yerel makinenizden PowerShell modülü dosyaları olmalıdır.

    ![PowerShell dosyaları][functions7]

### <a name="authentication"></a>Authentication

PowerShell cmdlet 'lerini kullanmak için kimlik doğrulaması yapmanız gerekir. İşlev uygulamasında kimlik doğrulamasını yapılandırırsınız. Kimlik doğrulamasını yapılandırmak için, ortam değişkenlerini yapılandırmanız ve şifrelenmiş bir anahtar dosyasını işlev uygulamasına yüklemeniz gerekir.

> [!NOTE]
> Bu senaryo, Azure Işlevleri ile kimlik doğrulamanın nasıl uygulanacağı hakkında yalnızca bir örnek sağlar. Bunu yapmak için başka yollar vardır.

#### <a name="encrypted-credentials"></a>Şifrelenmiş kimlik bilgileri

Aşağıdaki PowerShell betiği, **Passencryptkey. Key**adlı bir anahtar dosyası oluşturur. Ayrıca, sağlanan parolanın şifrelenmiş bir sürümünü de sağlar. Bu parola, kimlik doğrulaması için kullanılan Azure Active Directory uygulaması için tanımlanan parolanın aynısını kullanır.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

İşlev uygulamasının App Service Düzenleyicisi, **Alertpacketcapturepowershell**altında **anahtarlar** adlı bir klasör oluşturun. Ardından, önceki PowerShell örneğinde oluşturduğunuz **Passencryptkey. Key** dosyasını karşıya yükleyin.

![İşlevler anahtarı][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Ortam değişkenlerinin değerlerini al

Son gereksinim, kimlik doğrulama için değerlere erişmesi gereken ortam değişkenlerini ayarlamak içindir. Aşağıdaki listede, oluşturulan ortam değişkenleri gösterilmektedir:

* Azureclıtıd

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>Azureclıtıd

İstemci KIMLIĞI, Azure Active Directory bir uygulamanın uygulama KIMLIĞIDIR.

1. Zaten kullanabileceğiniz bir uygulamanız yoksa, bir uygulama oluşturmak için aşağıdaki örneği çalıştırın.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Uygulamayı oluştururken kullandığınız parolanın, anahtar dosyasını kaydederken daha önce oluşturduğunuz parolayla aynı olması gerekir.

1. Azure portal **abonelikler**' i seçin. Kullanılacak aboneliği seçin ve ardından **erişim denetimi (IAM)** öğesini seçin.

    ![İşlev ıAM][functions9]

1. Kullanılacak hesabı seçin ve ardından **Özellikler**' i seçin. Uygulama KIMLIĞINI kopyalayın.

    ![İşlevler uygulama KIMLIĞI][functions10]

#### <a name="azuretenant"></a>AzureTenant

Aşağıdaki PowerShell örneğini çalıştırarak kiracı KIMLIĞINI edinin:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

AzureCredPassword ortam değişkeninin değeri, aşağıdaki PowerShell örneğini çalıştırmadan aldığınız değerdir. Bu örnek, önceki **şifreli kimlik bilgileri** bölümünde gösterilen bir örnektir. Gereken değer, `$Encryptedpassword` değişkenin çıktıdır.  Bu, PowerShell betiğini kullanarak şifrelediğiniz hizmet sorumlusu parolasıdır.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Ortam değişkenlerini depolayın

1. İşlev uygulamasına gidin. Ardından **işlev uygulama ayarları** > **uygulama ayarlarını yapılandır**' ı seçin.

    ![Uygulama ayarlarını yapılandırma][functions11]

1. Ortam değişkenlerini ve değerlerini uygulama ayarlarına ekleyin ve ardından **Kaydet**' i seçin.

    ![Uygulama ayarları][functions12]

### <a name="add-powershell-to-the-function"></a>İşleve PowerShell ekleyin

Azure işlevinin içinden ağ izleyicisine çağrı yapmak artık zaman alabilir. Gereksinimlere bağlı olarak, bu işlevin uygulanması farklılık gösterebilir. Ancak, kodun genel akışı aşağıdaki gibidir:

1. İşlem giriş parametreleri.
2. Sınırları doğrulamak ve ad çakışmalarını çözmek için mevcut paket yakalamalarını sorgulayın.
3. Uygun parametrelerle bir paket yakalama oluşturun.
4. Zamanlayıcı, tamamlanana kadar düzenli aralıklarla yakalama.
5. Kullanıcıya, paket yakalama oturumunun tamamlandığını bildirin.

Aşağıdaki örnek, işlevinde kullanılabilen PowerShell kodudur. **SubscriptionID**, **Resourcegroupname**ve **storageAccountName**için değiştirilmeleri gereken değerler vardır.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>İşlev URL 'sini al 
1. İşlevinizi oluşturduktan sonra, uyarınızı işlevle ilişkili URL 'YI çağırmak üzere yapılandırın. Bu değeri almak için, işlev URL 'sini işlev uygulamanızdan kopyalayın.

    ![İşlev URL 'sini bulma][functions13]

2. İşlev uygulamanızın işlev URL 'sini kopyalayın.

    ![İşlev URL 'SI kopyalanıyor][2]

Web kancası GÖNDERI isteği yükünde özel özellikler gerekiyorsa, [Azure ölçüm uyarısında Web kancası yapılandırma](../azure-monitor/platform/alerts-webhooks.md)konusuna bakın.

## <a name="configure-an-alert-on-a-vm"></a>VM 'de uyarı yapılandırma

Uyarılar, belirli bir ölçüm kendisine atanan bir eşiğe girdiğinde bireylere bildirmek üzere yapılandırılabilir. Bu örnekte, uyarı gönderilen TCP kesimleridir, ancak uyarı diğer birçok ölçüm için tetiklenebilir. Bu örnekte, işlevi çağırmak için bir Web kancasını çağırmak üzere bir uyarı yapılandırılmıştır.

### <a name="create-the-alert-rule"></a>Uyarı kuralı oluşturma

Var olan bir sanal makineye gidin ve bir uyarı kuralı ekleyin. Uyarıları yapılandırma hakkında daha ayrıntılı belgeler, [Azure hizmetleri Için Azure izleyici 'de uyarı oluşturma ' da bulunabilir-Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md). **Uyarı kuralı** dikey penceresinde aşağıdaki değerleri girin ve **Tamam**' ı seçin.

  |**Ayar** | **Değer** | **Ayrıntılar** |
  |---|---|---|
  |**Name**|TCP_Segments_Sent_Exceeded|Uyarı kuralının adı.|
  |**Açıklama**|Gönderilen TCP kesimleri eşiği aştı|Uyarı kuralının açıklaması.|
  |**Ölçüm**|Gönderilen TCP kesimleri| Uyarıyı tetiklemek için kullanılacak ölçüm. |
  |**Koşul**|Büyüktür| Ölçüm değerlendirilirken kullanılacak koşul.|
  |**Eşiği**|100| Uyarıyı tetikleyen ölçümün değeri. Bu değer, ortamınız için geçerli bir değer olarak ayarlanmalıdır.|
  |**Dönemini**|Son beş dakika boyunca| Ölçüm üzerindeki eşiğin aranacağı süreyi belirler.|
  |**Web kancası**|[işlev uygulamasından Web kancası URL 'SI]| Önceki adımlarda oluşturulan işlev uygulamasındaki Web kancası URL 'SI.|

> [!NOTE]
> TCP kesimleri ölçümü varsayılan olarak etkin değildir. [İzleme ve tanılamayı etkinleştir ' i](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)ziyaret ederek ek ölçümleri etkinleştirme hakkında daha fazla bilgi edinin.

## <a name="review-the-results"></a>Sonuçları gözden geçirin

Uyarının ölçütlerine sonra bir paket yakalama oluşturulur. Ağ Izleyicisi ' ne gidin ve ardından **paket yakalama**' yı seçin. Bu sayfada, paket yakalamayı indirmek için paket yakalama dosyası bağlantısını seçebilirsiniz.

![Paket yakalamayı görüntüle][functions14]

Yakalama dosyası yerel olarak depolanıyorsa, sanal makinede oturum açarak alabilirsiniz.

Azure depolama hesaplarından dosya indirme hakkında yönergeler için bkz. [.NET kullanarak Azure Blob depolamayı kullanmaya başlama](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Kullanabileceğiniz başka bir araç da [Depolama Gezgini](https://storageexplorer.com/).

Yakalandıktan sonra, bir **. Cap** dosyası okuyabilen herhangi bir aracı kullanarak görüntüleyebilirsiniz. Bu araçların ikisi için bağlantılar aşağıda verilmiştir:

- [Microsoft Ileti Çözümleyicisi](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Sonraki adımlar

Paket [yakalama analizini Wireshark ile](network-watcher-deep-packet-inspection.md)ziyaret ederek paket yakalamalarını görüntülemeyi öğrenin.


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
