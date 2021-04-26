---
title: Azure Market için Azure sanal makine görüntüsünü test etme
description: Azure Market 'te Azure sanal makine teklifini test etme ve gönderme hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 467b7d605b57c479d84fc995b4e0dc53b3ac5275
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558304"
---
# <a name="test-a-virtual-machine-image"></a>Sanal makine görüntüsünü test etme

Bu konuda, Azure Marketi 'nde dağıtım için bir sanal makine (VM) görüntüsünü test etme adımları ele alınacaktır.

## <a name="deploy-an-azure-vm"></a>Azure VM dağıtma

Paylaşılan görüntü Galerisi görüntüsünden bir VM dağıtmak için:

1. Paylaşılan görüntü Galerisi görüntü sürümüne git
1. VM oluştur 'a tıklayın
1. Bir sanal makine adı sağlayın ve bir VM boyutu seçin
1. Gözden geçir + oluştur ' a tıklayın. Doğrulama başarılı olduktan sonra Oluştur ' a tıklayın.

> [!NOTE]
> Bir VHD dosyasından bir VM oluşturmanız gerekiyorsa, aşağıdaki makalelerdeki yönergeleri izleyin, [Azure Resource Manager şablonu hazırlayın](#connect-the-certification-tool-to-a-vm-image) veya [PowerShell kullanarak bir Azure VM dağıtın](#how-to-use-powershell-to-consume-the-self-test-api).

Bu makalede, en son Azure Marketi yayımlama gereksinimlerini karşıladığından emin olmak için ticari Market 'te bir sanal makine (VM) görüntüsünün nasıl test edileceğini ve gönderileceği açıklanır.

VM teklifinizi göndermeden önce şu adımları uygulayın:

- Genelleştirilmiş görüntünüzü kullanarak bir Azure VM dağıtın; bkz. [onaylanan temel kullanarak sanal makine oluşturma](azure-vm-create-using-approved-base.md) veya [kendi görüntünüzü kullanarak sanal makine oluşturma](azure-vm-create-using-own-image.md).
- Doğrulamaları çalıştırın.

## <a name="run-validations"></a>Doğrulamaları Çalıştır

Dağıtılan görüntüde doğrulamaları çalıştırmanın iki yolu vardır.

### <a name="use-certification-test-tool-for-azure-certified"></a>Azure Sertifikalı sertifika sınama aracını kullanma

#### <a name="download-and-run-the-certification-test-tool"></a>Sertifika test aracını indirme ve çalıştırma

Azure Sertifikalı sertifika test aracı yerel bir Windows makinesinde çalışır, ancak Azure tabanlı bir Windows veya Linux VM 'yi sınar. Kullanıcı VM Görüntünüzün Microsoft Azure birlikte kullanılabileceğini ve VHD 'nizi hazırlama konusunda rehberlik ve gereksinimlerin karşılandığını gösterir.

1. [Azure Sertifikalı en son sertifika test aracı](https://www.microsoft.com/download/details.aspx?id=44299)'nı indirip yükleyin.
2. Sertifika aracını açın ve ardından **Yeni test Başlat**' ı seçin.
3. Test bilgileri ekranında, test çalıştırması için bir **Test adı** girin.
4. **Windows Server** veya **Linux** VM 'niz için platformu seçin. Platform seçiminiz, kalan seçenekleri etkiler.
5. VM 'niz bu veritabanı hizmetini kullanıyorsa **Azure SQL veritabanı Için test** onay kutusunu seçin.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Sertifika aracını bir VM görüntüsüne bağlama

1. SSH kimlik doğrulama modunu seçin: parola kimlik doğrulaması veya anahtar dosyası kimlik doğrulaması.
2. Parola tabanlı kimlik doğrulaması kullanıyorsanız, **VM DNS adı**, **Kullanıcı adı** ve **parola** değerlerini girin. Varsayılan SSH bağlantı noktası numarasını da değiştirebilirsiniz.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="VM test bilgilerinin seçimini gösterir.":::

3. Anahtar dosya tabanlı kimlik doğrulaması kullanıyorsanız, VM DNS adı, Kullanıcı adı ve özel anahtar konumu değerlerini girin. Ayrıca bir parola dahil edebilir veya varsayılan SSH bağlantı noktası numarasını değiştirebilirsiniz.
4. Tam VM DNS adını (örneğin, MyVMName.Cloudapp.net) girin.
5. **Kullanıcı adını** ve **parolayı** girin.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="VM Kullanıcı adı ve parolasının seçimini gösterir.":::

6. **İleri**’yi seçin.

#### <a name="run-a-certification-test"></a>Sertifika testi çalıştırma

VM Görüntünüz için sertifika aracında parametre değerlerini verdikten sonra, sanal makinenize geçerli bir bağlantı oluşturmak için Bağlantıyı Sına ' yı seçin. Bir bağlantı doğrulandıktan sonra, testi başlatmak için **İleri** ' yi seçin. Test tamamlandığında, sonuçlar bir tabloda görüntülenir. Durum sütununda her test için (başarılı/başarısız/uyarı) gösterilir. Testlerin herhangi biri başarısız olursa, görüntünüz sertifikalı değildir. Bu durumda, gereksinimleri ve hata iletilerini gözden geçirin, önerilen değişiklikleri yapın ve testi yeniden çalıştırın.

Otomatik test tamamlandıktan sonra, soru formu ekranının, Genel değerlendirme ve çekirdek özelleştirmenin iki sekmesinde VM Görüntünüz hakkında ek bilgiler sağlayın ve ardından Ileri ' yi seçin.

Son ekran, bir Linux VM görüntüsü için SSH erişim bilgileri gibi daha fazla bilgi ve özel durumlar arıyorsanız, başarısız olan değerlendirmelerin bir açıklamasını sağlar.

Son olarak, yürütülen test çalışmalarının test sonuçlarını ve günlük dosyalarını, soru formuna yönelik yanıtlarınızla birlikte indirmek için rapor oluştur ' u seçin.
> [!Note]
> VM 'lerde yüklü olan güvenlik duvarları gibi yazılımlar olduğundan, birkaç Yayımcı, sanal makinelerin kilitlenebileceği senaryolar vardır. Bu durumda, [sertifikalı test aracını](https://aka.ms/AzureCertificationTestTool) buraya indirin ve raporu Iş Ortağı Merkezi [desteği](https://aka.ms/marketplacepublishersupport)' ne gönderebilirsiniz.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Self-Test API 'sini kullanmak için PowerShell 'i kullanma

### <a name="on-linux-os"></a>Linux IŞLETIM sisteminde

PowerShell 'de API 'YI çağırın:

1. API 'YI çağırmak için Invoke-WebRequest komutunu kullanın.
2. Aşağıdaki kod örneğinde ve ekran yakalama bölümünde gösterildiği gibi, yöntemi, post ve içerik türü JSON olur.
3. JSON biçiminde gövde parametrelerini belirtin.

Aşağıdaki örnek, API 'ye yönelik bir PowerShell çağrısını göstermektedir:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>PowerShell 'de API çağırma örneği aşağıda verilmiştir:

[![API 'yi PowerShell 'de çağırmaya yönelik ekran örneği.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Önceki örneği kullanarak, JSON 'u alabilir ve aşağıdaki ayrıntıları almak için ayrıştırın.

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Bunu gösteren bu örnek ekran, `$res.Content` JSON biçiminde Test sonuçlarınızın ayrıntılarını gösterir:

[![Test sonuçlarının ayrıntıları ile PowerShell 'de API çağırma için ekran örneği.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Çevrimiçi bir JSON görüntüleyicisinde ( [Code Beautify](https://codebeautify.org/jsonviewer) veya [JSON Viewer](https://jsonformatter.org/json-viewer)gibi) görüntülenen JSON test sonuçlarının bir örneği aşağıda verilmiştir.

![Çevrimiçi bir JSON görüntüleyicisine daha fazla test sonucu.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows işletim sistemi üzerinde

PowerShell 'de API 'YI çağırın:

1. API 'YI çağırmak için Invoke-WebRequest komutunu kullanın.
2. Aşağıdaki kod örneğinde ve örnek ekranda gösterildiği gibi, yöntemi, post ve içerik türü JSON olur.
3. JSON biçiminde gövde parametreleri oluşturun.

Bu kod örneği, API 'ye yönelik bir PowerShell çağrısını gösterir:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Bu örnek ekranlar, PowerShell 'de API çağırma örneği gösterir:

**SSH anahtarıyla**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="PowerShell 'de API 'yi bir SSH anahtarıyla çağırma.":::

Şu **parolayla**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="PowerShell 'de API 'yi bir parolayla çağırma.":::

<br>Önceki örneği kullanarak, JSON 'u alabilir ve aşağıdaki ayrıntıları almak için ayrıştırın.

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Bu ekran, `$res.Content` Test SONUÇLARıNıN JSON biçiminde ayrıntılarını gösterir:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="JSON biçimindeki test sonuçlarının ayrıntıları.":::

<br>Çevrimiçi bir JSON görüntüleyicisinde ( [Code Beautify](https://codebeautify.org/jsonviewer) veya [JSON Viewer](https://jsonformatter.org/json-viewer)gibi) görüntülenen test sonuçlarının bir örneği aşağıda verilmiştir.

![Çevrimiçi bir JSON görüntüleyicisindeki sonuçları test edin.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Linux IŞLETIM sisteminde Self-Test API 'sini kullanmak için KıVRıMLı kullanma

Bu örnekte, Azure Active Directory ve Self-Host VM 'sine yönelik bir POST API çağrısı yapmak için kıvrımlı kullanılacaktır.

1. Self-Host VM 'de kimlik doğrulaması yapmak için bir Azure AD belirteci isteyin

   Doğru değerlerin, kıvrımlı istekte bulunduğundan emin olun.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   İstekten alınan yanıta bir örnek aşağıda verilmiştir:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Kendi kendine test VM 'si için bir istek gönderme

   Taşıyıcı belirtecinin ve parametrelerinin doğru değerlerle birlikte bulunduğundan emin olun.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Self-Test VM API çağrısından örnek yanıt

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Sonraki adımlar

- [Iş Ortağı Merkezi](https://partner.microsoft.com/)' nde oturum açın.
