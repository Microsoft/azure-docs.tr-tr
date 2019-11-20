---
title: Bir sanal makineyi önceden doğrulamaya yönelik kendi kendine test istemcisi | Azure Marketi
description: Azure Marketi için bir sanal makine görüntüsünü önceden doğrulamak üzere bir Self-Test istemcisi oluşturma.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pabutler
ms.openlocfilehash: fc62875873f38630e592c79aebd6a138665ed6e4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809204"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Bir Azure sanal makine görüntüsünü önceden doğrulamak için bir Self-Test istemcisi oluşturma

Bu makaleyi, kendi kendine test API 'sini tüketen bir istemci hizmeti oluşturmak için kılavuz olarak kullanın. En son Azure Marketi yayımlama gereksinimlerini karşıladığından emin olmak için bir sanal makineyi (VM) önceden doğrulamak üzere kendi kendine test API 'sini kullanabilirsiniz. Bu istemci hizmeti, Microsoft sertifikası için teklifinizi göndermeden önce bir VM 'yi test etmenizi sağlar.

## <a name="development-and-testing-overview"></a>Geliştirme ve test genel bakış

Kendi kendine test sürecinin bir parçası olarak, Azure aboneliğinizde çalışan bir VM 'yi doğrulamak üzere Azure Market 'e bağlanan bir yerel istemci oluşturacaksınız. VM, Windows veya Linux işletim sistemini çalıştırıyor olabilir.

Yerel istemci, kendi kendine test API 'SI ile kimlik doğrulaması yapan, bağlantı bilgilerini gönderen ve test sonuçlarını alan bir komut dosyasını çalıştırır.

Bir Self-Test istemcisi oluşturmak için üst düzey adımlar şunlardır:

1. Uygulamanız için Azure Active Directory (AD) kiracıyı seçin.
2. İstemci uygulamasını kaydedin.
3. İstemci Azure AD uygulaması için bir belirteç oluşturun.
4. Belirteci kendi kendine test API 'sine geçirin.

İstemciyi oluşturduktan sonra sanal makinenize karşı test edebilirsiniz.

### <a name="self-test-client-authorization"></a>Self-Test istemci yetkilendirmesi

Aşağıdaki diyagramda, kimlik doğrulama hizmetinin istemci kimlik bilgileri (paylaşılan gizlilik veya sertifika) kullanılarak hizmet için nasıl çalıştığı gösterilmektedir.

![İstemci yetkilendirme işlemi](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Self-Test istemcisi API 'SI

Self-Test API 'SI yalnızca POST yöntemini destekleyen tek bir uç nokta içerir.  Aşağıdaki yapıya sahiptir.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

Aşağıdaki tabloda API alanları açıklanmaktadır.


|      Alan         |    Açıklama    |
|  ---------------   |  ---------------  |
|  Yetkilendirme     |  "Taşıyıcı xxxx-xxxx-xxxx-xxxxx" dizesi, PowerShell kullanılarak oluşturulabilen Azure Active Directory (AD) istemci belirtecini içerir.          |
|  DNSName           |  Sınanacak sanal makinenin DNS adı    |
|  Kullanıcı              |  VM 'de oturum açmak için Kullanıcı adı         |
|  Parola          |  VM 'de oturum açmak için parola          |
|  İşletim Sistemi                |  VM 'nin işletim sistemi: `Linux` ya da `Windows`          |
|  PortNo            |  SANAL makineye bağlanmak için bağlantı noktası numarasını açın. Bağlantı noktası numarası genellikle Linux ve Windows için `5986` `22`.          |
|  |  |

## <a name="consuming-the-api"></a>API 'YI kullanma

PowerShell veya kıvrımlı kullanarak kendi kendine test API 'sini kullanabilirsiniz.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Linux IŞLETIM sisteminde API 'yi kullanmak için PowerShell 'i kullanma

PowerShell 'de API 'yi çağırmak için aşağıdaki adımları izleyin:

1. API 'YI çağırmak için `Invoke-WebRequest` komutunu kullanın.
2. Aşağıdaki kod örneğinde ve ekran yakalama bölümünde gösterildiği gibi, yöntemi, post ve içerik türü JSON olur.
3. JSON biçiminde gövde parametrelerini belirtin.

Aşağıdaki kod örneği, API 'ye yönelik bir PowerShell çağrısını gösterir.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
Aşağıdaki ekran yakalama, PowerShell 'de API çağırma için bir örnek gösterir.

![Linux işletim sistemi için PowerShell ile API çağırma](./media/stclient-call-api-ps-linuxvm.png)

Önceki örneği kullanarak, JSON 'u alabilir ve aşağıdaki ayrıntıları almak için ayrıştırın.

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

`$res.Content`gösteren aşağıdaki ekran yakalama, size JSON biçiminde test sonuçlarının ayrıntılarını verir.

![Linux 'a PowerShell çağrısından gelen JSON sonuçları](./media/stclient-pslinux-rescontent-json.png)

Aşağıdaki ekran yakalama, çevrimiçi bir JSON görüntüleyicisinde (örneğin, [Code Beautify](https://codebeautify.org/jsonviewer) veya [JSON VIEWER](https://jsonformatter.org/json-viewer)) görüntülenen JSON test sonuçlarının bir örneğini gösterir.

![Linux VM 'ye PowerShell çağrısından gelen JSON sonuçları](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Windows işletim sisteminde API 'yi kullanmak için PowerShell 'i kullanma

PowerShell 'de API 'yi çağırmak için aşağıdaki adımları izleyin:

1. API 'YI çağırmak için `Invoke-WebRequest` komutunu kullanın.
2. Aşağıdaki kod örneğinde ve ekran yakalama bölümünde gösterildiği gibi, yöntemi, post ve içerik türü JSON olur.
3. JSON biçiminde gövde parametreleri oluşturun.

Aşağıdaki kod örneği, API 'ye yönelik bir PowerShell çağrısını gösterir.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

Aşağıdaki ekran yakalama, PowerShell 'de API çağırma için bir örnek gösterir.

![Windows VM için PowerShell ile API çağırma](./media/stclient-call-api-ps-windowsvm.png)

Önceki örneği kullanarak, JSON 'u alabilir ve aşağıdaki ayrıntıları almak için ayrıştırın.

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

`$res.Content`gösteren aşağıdaki ekran yakalama, size JSON biçiminde test sonuçlarının ayrıntılarını verir.

![PowerShell çağrısından Windows 'a JSON sonuçları](./media/stclient-pswindows-rescontent-json.png)

Aşağıdaki ekran yakalama, çevrimiçi bir JSON görüntüleyicisinde görüntülenen test sonuçlarını gösterir.
(örneğin, [Code Beautify](https://codebeautify.org/jsonviewer), [JSON Görüntüleyici](https://jsonformatter.org/json-viewer))

![PowerShell çağrısından Windows VM 'ye JSON sonuçları](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Linux IŞLETIM sisteminde API 'yi kullanmak için kıvrımlı kullanın

API 'yi kıvrımlı ile çağırmak için aşağıdaki adımları izleyin:

1. API 'YI çağırmak için kıvrımlı komutunu kullanın.
2. Aşağıdaki kod parçacığında gösterildiği gibi, yöntemi, post ve içerik türü JSON olur.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Aşağıdaki ekranda, API 'YI çağırmak için kıvrımlı kullanma örneği gösterilmektedir.

![Kıvrımlı komutu kullanarak API çağırma](./media/stclient-consume-api-curl.png)

Aşağıdaki ekran yakalama, kıvrımlı çağrının JSON sonuçlarını gösterir.

![Kıvrımlı çağrının JSON sonuçları](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Uygulama için Azure AD kiracısını seçin

Uygulamanızı oluşturmak istediğiniz Azure AD kiracısını seçmek için aşağıdaki adımları kullanın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Üstteki menü çubuğunda hesabınızı seçin ve dizin listesinde, uygulamanızı kaydetmek istediğiniz Active Directory kiracıyı seçin. Ya da, genel abonelik filtresini görmek için **Dizin + abonelik** simgesini seçin. Aşağıdaki ekran yakalama, bu filtrenin bir örneğini gösterir.

   ![Abonelik filtresini seçin](./media/stclient-subscription-filter.png)

3. Sol taraftaki Gezinti çubuğunda **tüm hizmetler** ' i seçin ve ardından **Azure Active Directory**' yi seçin.

   Aşağıdaki adımlarda, kiracı adına (veya dizin adına) veya kiracı KIMLIĞINE (veya dizin KIMLIĞINE) ihtiyacınız olabilir.

   **Kiracı bilgilerini almak için:**

   **Azure Active Directory genel bakış**' da, "Özellikler" i arayın ve ardından **Özellikler**' i seçin. Örnek olarak aşağıdaki ekran yakalamayı kullanma:

   - **Ad** -kiracı adı veya dizin adı
   - **DIZIN kimliği** -Kiracı kimliği veya dizin kimliği ya da özellikleri bulmak için kaydırma çubuğunu kullanın.

   ![Azure Active Directory Özellikler sayfası](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>İstemci uygulamasını kaydetme

İstemci uygulamasını kaydetmek için aşağıdaki adımları kullanın.

1. Sol taraftaki Gezinti çubuğunda **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' yi seçin.
2. **Uygulama kayıtları**altında **+ Yeni uygulama kaydı**' nı seçin.
3. **Oluştur**altında, aşağıdaki alanlar için gereken bilgileri girin:

   - **Ad** -uygulama için kolay bir ad girin. Örneğin, "SelfTestClient".
   - **Uygulama türü** - **Web uygulaması/API** seçin
   - **Oturum açma URL 'si** -tür "https:\//isvapp.azurewebsites.net/selftest-VM"

4. **Oluştur**'u seçin.
5. **Uygulama kayıtları** veya **kayıtlı**uygulama altında **uygulama kimliği**' ni kopyalayın.

   ![Uygulama KIMLIĞINI al](./media/stclient-app-id.png)

6. Kayıtlı uygulama araç çubuğunda **Ayarlar**' ı seçin.
7. Uygulamanız için izinleri yapılandırmak üzere **gerekli izinleri** seçin.
8. **Gerekli izinler**altında **+ Ekle**' yi seçin.
9. **API erişimi ekle**' nin altında, **bir API Seç**' i seçin.
10. **BIR API seçin**altında, API 'yi aramak Için "Windows Azure klasik dağıtım modeli" yazın.
11. Arama sonuçlarında, **Microsoft Azure klasik dağıtım modeli** ' ni seçin ve ardından **Seç**' e tıklayın.

    ![Çok kiracılı uygulama için yapılandırma](./media/stclient-select-api.png)

12. **API erişimi ekle**' nin altında, **seçim Izinleri**' ni seçin.
13. **"Windows Azure hizmet yönetim API'si" erişimini**seçin.

    ![Uygulama için API erişimini etkinleştirme](./media/stclient-enable-api-access.png)

14. **Seç**'e tıklayın.
15. **Done** (Bitti) öğesini seçin.
16. **Ayarlar** bölümünde **Özellikler**’i seçin.
17. **Özellikler**altında, **çok**kiracılı olarak aşağı kaydırın. **Evet**' i seçin.

    ![Çok kiracılı uygulama için yapılandırma](./media/stclient-yes-multitenant.png)

18. **Kaydet**’i seçin.
19. **Ayarlar**altında **anahtarlar**' ı seçin.
20. Anahtar **açıklaması** metin kutusunu seçerek bir gizli anahtar oluşturun. Aşağıdaki alanları yapılandırın:

    - Anahtar adı yazın. Örneğin, "selftestclient"
    - **Süre sonu** açılan listesinde "1 yıl" seçeneğini belirleyin.
    - Anahtarı oluşturmak için **Kaydet** ' i seçin.
    - **Değer**altında anahtarı kopyalayın.

      >[!Important]
      >**Anahtarlar** formundan çıktıktan sonra anahtar değeri göremezsiniz.

    ![Anahtar değer formu](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>İstemci uygulaması için belirteç oluşturma

OAuth REST API kullanarak bir belirteç oluşturmak ve almak için aşağıdaki programlardan herhangi birini kullanabilirsiniz:

- Postman
- Linux 'ta kıvrımlı
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Postman kullanarak bir belirteç oluşturmak ve almak için

 Yetkili uygulamalarınızdan herhangi biri için Auth0 sormak için, aşağıdaki biçimde bir yük ile [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) uç noktasına bir POST işlemi gerçekleştirin:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Istek gövdesine şu parametreleri geçirin:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Istek üst bilgisinde aşağıdaki parametreleri geçirin:

```
Content-Type: application/x-www-form-urlencoded
```

Aşağıdaki ekran yakalama, bir belirteci almak için Postman kullanmanın bir örneğini gösterir.

![Postman ile belirteç al](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Linux 'ta kıvrımlı kullanarak bir belirteç oluşturmak ve almak için

Yetkili uygulamalarınızdan herhangi biri için Auth0 sormak için, aşağıdaki biçimde bir yük ile [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) uç noktasına bir POST işlemi gerçekleştirin:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Aşağıdaki ekran yakalama, bir belirteci almak için kıvrımlı komutunun kullanılmasına bir örnek gösterir.

![Kıvrımlı komutuyla belirteç al](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>C kullanarak bir belirteç oluşturmak ve almak için&#35;

Yetkili uygulamalarınızdan herhangi biri için Auth0 sormak için, aşağıdaki biçimde bir yük ile https:\//soamtenant.auth0.com/oauth/token uç noktasına bir POST işlemi gerçekleştirin:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>PowerShell kullanarak bir belirteç oluşturmak ve almak için

Yetkili uygulamalarınızdan herhangi biri için Auth0 sormak için, aşağıdaki biçimde bir yük ile https:\//soamtenant.auth0.com/oauth/token uç noktasına bir POST işlemi gerçekleştirin:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>İstemci uygulama belirtecini API 'ye geçirme

Kimlik doğrulama üstbilgisinde aşağıdaki kodu kullanarak belirteci kendi kendine test API 'sine geçirin:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Kendi kendine test istemcinizi test edin

İstemciyi test etmek için şu adımları izleyin:

1. Test etmek istediğiniz VM 'yi dağıtın.
2. İstemci uygulama belirtecinizi yetkilendirme için kullanarak kendi kendine test API 'sini çağırın.
3. Test sonuçlarını JSON biçiminde alın.

### <a name="test-result-examples"></a>Test sonucu örnekleri

Aşağıdaki kod parçacıkları, JSON biçiminde test sonuçlarını gösterir.

**Windows VM için test sonuçları:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Linux VM için test sonuçları:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal makinenizi başarıyla test ettikten sonra [teklifi yayımlayabilirsiniz](./cpp-publish-offer.md).
