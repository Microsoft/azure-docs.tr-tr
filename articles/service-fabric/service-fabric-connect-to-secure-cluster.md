---
title: Azure Service Fabric kümesine güvenli bir şekilde bağlanma | Microsoft Docs
description: Service Fabric kümesine istemci erişiminin kimliğini doğrulamak ve istemcilerle küme arasındaki iletişimin güvenliğini sağlamak açıklanmaktadır.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2019
ms.author: atsenthi
ms.openlocfilehash: c350b53b2d0b235c5e34431386205f090f37b482
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599721"
---
# <a name="connect-to-a-secure-cluster"></a>Güvenli bir kümeye bağlanma

İstemci bir Service Fabric küme düğümüne bağlanırsa, istemci kimlik doğrulaması yapılabilir ve sertifika güvenliği veya Azure Active Directory (AAD) kullanılarak kurulan güvenli iletişim olabilir. Bu kimlik doğrulaması, kümeye ve dağıtılan uygulamalara yalnızca yetkili kullanıcıların erişip yönetim görevlerini gerçekleştirebilmesini sağlar.  Küme oluşturulduğunda, sertifika veya AAD güvenliğinin kümede daha önce etkinleştirilmiş olması gerekir.  Küme güvenliği senaryoları hakkında daha fazla bilgi için bkz. [küme güvenliği](service-fabric-cluster-security.md). Sertifikalarla güvenliği sağlanmış bir kümeye bağlanıyorsanız, kümeye bağlanan bilgisayarda [istemci sertifikasını ayarlayın](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) . 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLı (sfctl) kullanarak güvenli bir kümeye bağlanma

Service Fabric CLı (sfctl) kullanarak güvenli bir kümeye bağlanmanın birkaç farklı yolu vardır. Kimlik doğrulaması için bir istemci sertifikası kullanıyorsanız sertifika bilgilerinin küme düğümlerine dağıtılmış olan bir sertifikayla eşleşmesi gerekir. Sertifikanızın sertifika yetkilileri (CA) varsa, güvenilen CA 'Ları da belirtmeniz gerekir.

`sfctl cluster select` Komutunu kullanarak bir kümeye bağlanabilirsiniz.

İstemci sertifikaları, sertifika ve anahtar çifti olarak ya da tek bir PFX dosyası olarak iki farklı Fashions içinde belirtilebilir. Parola korumalı ped dosyaları için otomatik olarak parolayı girmeniz istenir. İstemci sertifikasını bir PFX dosyası olarak aldıysanız, önce aşağıdaki komutu kullanarak PFX dosyasını bir PEı dosyasına dönüştürün. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

. Pfx dosyanız parola korumalı değilse, son parametre için-passin pass: kullanın.

İstemci sertifikasını bir pek dosyası olarak belirtmek için, `--pem` bağımsız değişkeninde dosya yolunu belirtin. Örneğin:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Parola korumalı ped dosyaları, herhangi bir komut çalıştırılmadan önce parola ister.

Bir sertifika belirtmek için, anahtar çifti her ilgili `--cert` dosyanın `--key` dosya yollarını belirtmek için ve bağımsız değişkenlerini kullanın.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Bazen test veya geliştirme kümelerinin güvenliğini sağlamak için kullanılan sertifikaların sertifika doğrulaması başarısız olur. Sertifika doğrulamayı atlamak için, `--no-verify` seçeneğini belirtin. Örneğin:

> [!WARNING]
> Üretim Service Fabric kümelerine bağlanırken `no-verify` seçeneğini kullanmayın.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Ayrıca, güvenilir CA sertifikaları veya ayrı sertifikalar için yollar belirtebilirsiniz. Bu yolları belirtmek için `--ca` bağımsız değişkenini kullanın. Örneğin:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Bağlandıktan sonra, kümeyle etkileşim kurmak için [diğer sfctl komutlarını çalıştırabilmelisiniz](service-fabric-cli.md) .

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>PowerShell kullanarak bir kümeye bağlanma
PowerShell aracılığıyla bir küme üzerinde işlem gerçekleştirmeden önce, önce kümeye bir bağlantı kurun. Küme bağlantısı, belirtilen PowerShell oturumunda sonraki tüm komutlar için kullanılır.

### <a name="connect-to-an-unsecure-cluster"></a>Güvenli olmayan bir kümeye bağlanma

Güvenli olmayan bir kümeye bağlanmak için **Connect-ServiceFabricCluster** komutuna küme uç noktası adresini sağlayın:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory kullanarak güvenli bir kümeye bağlanma

Küme Yöneticisi erişimini yetkilendirmek için Azure Active Directory kullanan güvenli bir kümeye bağlanmak için, küme sertifikası parmak izini sağlayın ve *AzureActiveDirectory* bayrağını kullanın.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>İstemci sertifikası kullanarak güvenli bir kümeye bağlanma
Yönetici erişimini yetkilendirmek için istemci sertifikaları kullanan güvenli bir kümeye bağlanmak için aşağıdaki PowerShell komutunu çalıştırın. 

#### <a name="connect-using-certificate-common-name"></a>Sertifika ortak adını kullanarak bağlan
Küme yönetimi için izin verilen istemci sertifikasının ortak adını ve ortak adını sağlayın. Sertifika ayrıntılarının küme düğümlerindeki bir sertifikayla eşleşmesi gerekir.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*Servercommonname* , küme düğümlerinde yüklü olan sunucu sertifikasının ortak adıdır. *FindValue* , yönetici istemci sertifikasının ortak adıdır. Parametreler doldurulduğunda, komut aşağıdaki örneğe benzer şekilde görünür:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Sertifika parmak izini kullanarak bağlan
Küme sertifikası parmak izini ve küme yönetimi için izin verilen istemci sertifikasının parmak izini sağlayın. Sertifika ayrıntılarının küme düğümlerindeki bir sertifikayla eşleşmesi gerekir.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*Servercertparmak izi* , küme düğümlerinde yüklü olan sunucu sertifikasının parmak izdir. *FindValue* , yönetici istemci sertifikasının parmak izdir.  Parametreler doldurulduğunda, komut aşağıdaki örneğe benzer şekilde görünür:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Windows Active Directory kullanarak güvenli bir kümeye bağlanma
Tek başına kümeniz AD güvenliği kullanılarak dağıtılmışsa, "WindowsCredential" anahtarını ekleyerek kümeye bağlanın.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>FabricClient API 'Lerini kullanarak bir kümeye bağlanma
Service Fabric SDK, küme yönetimi için [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) sınıfını sağlar. FabricClient API 'Lerini kullanmak için Microsoft. ServiceFabric NuGet paketini alın.

### <a name="connect-to-an-unsecure-cluster"></a>Güvenli olmayan bir kümeye bağlanma

Uzaktan güvenli olmayan bir kümeye bağlanmak için bir FabricClient örneği oluşturun ve küme adresini sağlayın:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Bir küme içinden çalışan kod için, örneğin, güvenilir bir hizmette, küme adresini belirtmeden bir *FabricClient oluşturun* . FabricClient, kod şu anda üzerinde çalıştığı düğümde yerel yönetim ağ geçidine bağlanır, ek bir ağ atlamasını önler.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>İstemci sertifikası kullanarak güvenli bir kümeye bağlanma

Kümedeki düğümlerin, [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)'da ayarlanmış olan [remotecommonnames özelliğinde](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) bulunan ortak adı veya DNS adı San 'da geçerli sertifikalara sahip olması gerekir. Bu işlemin ardından, istemci ve küme düğümleri arasında karşılıklı kimlik doğrulaması etkinleştirilir.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Azure Active Directory kullanarak güvenli bir kümeye etkileşimli olarak bağlanma

Aşağıdaki örnek, sunucu kimliği için istemci kimliği ve sunucu sertifikası için Azure Active Directory kullanır.

Kümeye bağlandıktan sonra etkileşimli oturum açma için bir iletişim kutusu penceresi otomatik olarak açılır.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Güvenli bir kümeye etkileşimli olmayan Azure Active Directory kullanarak bağlanma

Aşağıdaki örnek Microsoft. IdentityModel. clients. ActiveDirectory, sürüm: öğesine dayanır. 2.19.208020213.

AAD belirteci alma hakkında daha fazla bilgi için bkz. [Microsoft. IdentityModel. clients. ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Azure Active Directory kullanarak önceden meta veri bilgisi olmadan güvenli bir kümeye bağlanma

Aşağıdaki örnek etkileşimli olmayan belirteç alımı kullanır, ancak aynı yaklaşım özel bir etkileşimli belirteç alma deneyimi oluşturmak için de kullanılabilir. Belirteç alımı için gereken Azure Active Directory meta verileri küme yapılandırmasından okundu.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer kullanarak güvenli bir kümeye bağlanma
Belirli bir küme için [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) erişmek için tarayıcınızı şu şekilde işaretleyin:

`http://<your-cluster-endpoint>:19080/Explorer`

Tam URL, Azure portal küme temelleri bölmesinde de mevcuttur.

Bir tarayıcı kullanarak Windows veya OS X 'te güvenli bir kümeye bağlanmak için, istemci sertifikasını içeri aktarabilirsiniz ve tarayıcı, kümeye bağlanmak için kullanılacak sertifikayı ister.  Linux makinelerde, sertifika, gelişmiş tarayıcı ayarları kullanılarak içeri aktarılmalıdır (her tarayıcı farklı mekanizmalarla) ve bunu diskteki sertifika konumuna işaret eder. Daha fazla bilgi için [istemci sertifikası ayarlama](#connectsecureclustersetupclientcert) makalesini okuyun.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory kullanarak güvenli bir kümeye bağlanma

AAD ile güvenli hale getirilmiş bir kümeye bağlanmak için tarayıcınızı şu şekilde işaretleyin:

`https://<your-cluster-endpoint>:19080/Explorer`

Otomatik olarak AAD ile oturum açmanız istenir.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>İstemci sertifikası kullanarak güvenli bir kümeye bağlanma

Sertifikalarla güvenliği sağlanmış bir kümeye bağlanmak için tarayıcınızı şu şekilde işaretleyin:

`https://<your-cluster-endpoint>:19080/Explorer`

Otomatik olarak bir istemci sertifikası seçmeniz istenir.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Uzak bilgisayarda istemci sertifikası ayarlama

Küme ve sunucu sertifikası ve istemci erişimi için bir tane olmak üzere, kümenin güvenliğini sağlamak için en az iki sertifika kullanılmalıdır.  Ek ikincil sertifikaları ve istemci erişim sertifikalarını da kullanmanızı öneririz.  Sertifika güvenliği kullanarak bir istemci ve küme düğümü arasındaki iletişimin güvenliğini sağlamak için öncelikle istemci sertifikası edinmeniz ve yüklemeniz gerekir. Sertifika, yerel bilgisayarın veya geçerli kullanıcının kişisel (My) deposuna yüklenebilir.  Ayrıca, istemcinin kümenin kimliğini doğrulayabilmesi için sunucu sertifikasının parmak izine ihtiyacınız vardır.

* Windows'da: PFX dosyasına çift tıklayın ve sertifikayı kişisel deponuza `Certificates - Current User\Personal\Certificates`yüklemek için istemleri izleyin. Alternatif olarak, PowerShell komutunu kullanabilirsiniz:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Otomatik olarak imzalanan bir sertifika ise, bu sertifikayı güvenli bir kümeye bağlamak için kullanabilmeniz için makinenizin "güvenilir kişiler" deposuna aktarmanız gerekir.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac'te: PFX dosyasını çift tıklayın ve anahtarlığınıza sertifikayı yüklemek için istemleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Küme yükseltme işlemini ve beklentilerini Service Fabric](service-fabric-cluster-upgrade.md)
* [Visual Studio 'da Service Fabric uygulamalarınızı yönetme](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric sistem durumu modeli tanıtımı](service-fabric-health-introduction.md)
* [Uygulama güvenliği ve RunAs](service-fabric-application-runas-security.md)
* [Service Fabric CLI ile çalışmaya başlama](service-fabric-cli.md)
