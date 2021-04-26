---
title: Azure HDInsight 'ta Data Lake depolama dosyalarına erişilemiyor
description: Azure HDInsight 'ta Data Lake depolama dosyalarına erişilemiyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.openlocfilehash: f4c5a23b604334952730fcc4cf1fcb3fcbed6237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944398"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Azure HDInsight 'ta Data Lake depolama dosyalarına erişilemiyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue-acl-verification-failed"></a>Sorun: ACL doğrulaması başarısız oldu

Şuna benzer bir hata iletisi alıyorsunuz:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Nedeni

Kullanıcı, dosyalar/klasörler üzerinde hizmet sorumlusu (SP) izinlerini iptal etmiş olabilir.

### <a name="resolution"></a>Çözüm

1. SP 'nin yol üzerinde çapraz geçiş yapmak için ' x ' iznine sahip olduğundan emin olun. Daha fazla bilgi için bkz. [izinler](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). `dfs`Data Lake depolama hesabındaki dosyalara/klasörlere erişimi denetlemek için örnek komut:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Gerçekleştirilen okuma/yazma işlemine göre yola erişmek için gerekli izinleri ayarlayın. Çeşitli dosya sistemi işlemleri için gereken izinler için buraya bakın.

---

## <a name="issue-service-principal-certificate-expiry"></a>Sorun: hizmet sorumlusu sertifikası süre sonu

Şuna benzer bir hata iletisi alıyorsunuz:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Nedeni

Hizmet sorumlusu erişimi için belirtilen sertifikanın geçerliliği bitmiş olabilir.

1. Yayın düğümüne SSH. Aşağıdaki komutu kullanarak depolama hesabına erişimi denetleyin `dfs` :

    ```
    hdfs dfs -ls /
    ```

1. Hata iletisinin aşağıdaki çıktıya benzer olduğunu onaylayın:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. URL 'lerden birini alın `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. OAuth belirtecini almak için aşağıdaki kıvrımlı komutunu çalıştırın.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Geçerli bir hizmet sorumlusu için çıkış, şöyle bir şekilde olmalıdır:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Hizmet sorumlusu sertifikasının süresi dolmuşsa, çıkış aşağıdakine benzer şekilde görünür:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Diğer Azure Active Directory ilgili hatalar/sertifikayla ilgili hatalar, OAuth belirtecini almak için ağ geçidi URL 'si ile ping işlemi gerçekleştirerek tanınır.

1. HDI kümesinden ADLS 'a erişmeye çalışırken aşağıdaki hatayı alıyorsanız. Yukarıda bahsedilen adımları izleyerek sertifikanın süresi dolmuşsa emin olun.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Çözüm

Aşağıdaki PowerShell betiğini kullanarak yeni bir sertifika oluşturun veya var olan sertifikayı atayın:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Mevcut sertifikayı atamak için bir sertifika oluşturun,. pfx dosyası ve parola hazırlayın. AppID 'yi kullanarak, sertifikayı kümenin oluşturulduğu hizmet sorumlusu ile ilişkilendirin.

Parametreleri gerçek değerlerle değiştirdikten sonra PowerShell komutunu yürütün.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]