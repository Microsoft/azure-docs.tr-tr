---
title: Sertifikaları kullanarak Windows 'da küme güvenliğini sağlama
description: Azure Service Fabric tek başına veya şirket içi küme içinde ve istemciler ile küme arasında güvenli iletişim.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791046"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>X. 509.440 sertifikalarını kullanarak Windows 'da tek başına kümeyi güvenli hale getirme
Bu makalede, tek başına Windows kümenizin çeşitli düğümleri arasındaki iletişimin nasıl güvenliği sağlanacağı açıklanır. Ayrıca, X. 509.440 sertifikalarını kullanarak bu kümeye bağlanan istemcilerin kimliğini nasıl doğrulayacağınızı açıklar. Kimlik doğrulaması yalnızca yetkili kullanıcıların kümeye ve dağıtılan uygulamalara erişip yönetim görevlerini gerçekleştirmesini sağlar. Küme oluşturulduğunda, kümede sertifika güvenliği etkinleştirilmelidir.  

Düğüm-düğüm güvenliği, istemciden düğüme güvenlik ve rol tabanlı erişim denetimi gibi küme güvenliği hakkında daha fazla bilgi için bkz. [küme güvenliği senaryoları](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Hangi sertifikalara ihtiyacınız var?
İle başlamak için, [Windows Server paketi Service Fabric](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) kümenizdeki düğümlerden birine indirin. İndirilen pakette, dosyasında bir ClusterConfig.X509.MultiMachine.jsbulabilirsiniz. Dosyasını açın ve Özellikler bölümünde güvenlik bölümünü gözden geçirin:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Bu bölümde, tek başına Windows kümenizin güvenliğini sağlamak için ihtiyacınız olan sertifikalar açıklanmaktadır. Bir küme sertifikası belirtirseniz, ClusterCredentialType değerini _x509_ olarak ayarlayın. Dış bağlantılar için bir sunucu sertifikası belirtirseniz, ServerCredentialType öğesini _x509_ olarak ayarlayın. Zorunlu olmasa da, düzgün bir şekilde güvenli bir küme için bu sertifikaların her ikisine de sahip olmanız önerilir. Bu değerleri *x509* olarak ayarlarsanız, bunlara karşılık gelen sertifikaları belirtmeniz gerekir veya Service Fabric bir özel durum oluşturur. Bazı senaryolarda yalnızca _Clientcertificateparmak izlerini_ veya _smarproxycertificate_ öğesini belirtmek isteyebilirsiniz. Bu senaryolarda, _clustercredentialtype_ veya _servercredentialtype_ ' ı _x509_ olarak ayarlamanız gerekmez.


> [!NOTE]
> [Parmak izi](https://en.wikipedia.org/wiki/Public_key_fingerprint) , bir sertifikanın birincil kimliğidir. Oluşturduğunuz sertifikaların parmak izini öğrenmek için bkz. [bir sertifikanın parmak Izini alma](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
> 
> 

Aşağıdaki tabloda, küme kurulumunda ihtiyacınız olan sertifikalar listelenmektedir:

| **Certificateınformation ayarı** | **Açıklama** |
| --- | --- |
| ClusterCertificate |Test ortamı için önerilir. Bu sertifika, bir kümedeki düğümler arasındaki iletişimin güvenliğini sağlamak için gereklidir. Yükseltme için, birincil ve ikincil olmak üzere iki farklı sertifika kullanabilirsiniz. Parmak Izi bölümünde birincil sertifikanın parmak izini ve parmak izi Ikincil değişkenlerinde ikincil değeri ayarlayın. |
| Clustercertificatecommonadları |Üretim ortamı için önerilir. Bu sertifika, bir kümedeki düğümler arasındaki iletişimin güvenliğini sağlamak için gereklidir. Bir veya iki küme sertifikası ortak adı kullanabilirsiniz. Certificateıssuerparmak Izi, bu sertifikayı verenin parmak izine karşılık gelir. Aynı ortak ada sahip birden fazla sertifika kullanılırsa, birden çok verenin parmak izleri belirtebilirsiniz.|
| Clustercertificateıssuermağazaları |Üretim ortamı için önerilir. Bu sertifika, küme sertifikasını veren öğesine karşılık gelir. ClusterCertificateCommonNames altında veren parmak izini belirtmek yerine, bu bölümde verenin ortak adını ve karşılık gelen depo adını sağlayabilirsiniz.  Bu, küme veren sertifikalarının kolayca geçişine olanak sağlar. Birden çok küme sertifikası kullanılıyorsa birden fazla veren belirtilebilir. Boş bir ıssuercommonname, X509StoreNames altında belirtilen ilgili depolardaki tüm sertifikalara izin verir.|
| Sunucusertifikası |Test ortamı için önerilir. Bu sertifika, bu kümeye bağlanmayı denediğinde istemciye sunulur. Daha kolay bir şekilde, ClusterCertificate ve ServerCertificate için aynı sertifikayı kullanmayı tercih edebilirsiniz. Yükseltme için birincil ve ikincil olmak üzere iki farklı sunucu sertifikası kullanabilirsiniz. Parmak Izi bölümünde birincil sertifikanın parmak izini ve parmak izi Ikincil değişkenlerinde ikincil değeri ayarlayın. |
| ServerCertificateCommonNames |Üretim ortamı için önerilir. Bu sertifika, bu kümeye bağlanmayı denediğinde istemciye sunulur. Certificateıssuerparmak Izi, bu sertifikayı verenin parmak izine karşılık gelir. Aynı ortak ada sahip birden fazla sertifika kullanılırsa, birden çok verenin parmak izleri belirtebilirsiniz. Daha kolay bir şekilde, ClusterCertificateCommonNames ve ServerCertificateCommonNames için aynı sertifikayı kullanmayı tercih edebilirsiniz. Bir veya iki sunucu sertifikası ortak adı kullanabilirsiniz. |
| Servercertificateıssuermağazalarında |Üretim ortamı için önerilir. Bu sertifika, sunucu sertifikasını veren öğesine karşılık gelir. ServerCertificateCommonNames altında veren parmak izini belirtmek yerine, bu bölümde verenin ortak adını ve karşılık gelen depo adını sağlayabilirsiniz.  Bu, sunucu veren sertifikalarının kolayca geçişine olanak sağlar. Birden fazla sunucu sertifikası kullanılıyorsa birden çok veren belirlenebilir. Boş bir ıssuercommonname, X509StoreNames altında belirtilen ilgili depolardaki tüm sertifikalara izin verir.|
| Clientcertificateparmak Izleri |Kimliği doğrulanmış istemcilere bu sertifika kümesini yükler. Kümeye erişime izin vermek istediğiniz makinelere farklı sayıda istemci sertifikanız yüklü olabilir. CertificateThumbprint değişkeninde her bir sertifikanın parmak izini ayarlayın. IsAdmin 'i *true* olarak ayarlarsanız, bu sertifikaya sahip istemci, kümede yönetici yönetim etkinliklerini gerçekleştirebilir. Isadmin *false* ise, bu sertifikaya sahip istemci yalnızca Kullanıcı erişim hakları için izin verilen eylemleri genellikle salt okunurdur. Roller hakkında daha fazla bilgi için bkz. [rol tabanlı erişim denetimi Service Fabric](service-fabric-cluster-security.md#service-fabric-role-based-access-control). |
| ClientCertificateCommonNames |CertificateCommonName için ilk istemci sertifikasının ortak adını ayarlayın. Certificateıssuerparmak Izi, bu sertifikanın vereninin parmak izine ait. Ortak adlar ve veren hakkında daha fazla bilgi edinmek için bkz. [sertifikalarla çalışma](/dotnet/framework/wcf/feature-details/working-with-certificates). |
| Clientcertificateıssuermağazalarında |Üretim ortamı için önerilir. Bu sertifika, istemci sertifikasını veren (yönetici ve yönetici olmayan roller) öğesine karşılık gelir. ClientCertificateCommonNames altında veren parmak izini belirtmek yerine, bu bölümde verenin ortak adını ve karşılık gelen depo adını sağlayabilirsiniz.  Bu, istemci veren sertifikalarının kolayca geçişine olanak sağlar. Birden fazla istemci sertifikası kullanılıyorsa birden çok veren belirlenebilir. Boş bir ıssuercommonname, X509StoreNames altında belirtilen ilgili depolardaki tüm sertifikalara izin verir.|
| Smarproxycertificate |Test ortamı için önerilir. Bu isteğe bağlı sertifika, [ters proxy](service-fabric-reverseproxy.md)'nizin güvenliğini sağlamak istiyorsanız belirlenebilir. Bu sertifikayı kullanıyorsanız, düğüm türlerinde Smarproxyendpointport ayarlandığından emin olun. |
| Smarproxycertificatecommonnames |Üretim ortamı için önerilir. Bu isteğe bağlı sertifika, [ters proxy](service-fabric-reverseproxy.md)'nizin güvenliğini sağlamak istiyorsanız belirlenebilir. Bu sertifikayı kullanıyorsanız, düğüm türlerinde Smarproxyendpointport ayarlandığından emin olun. |

Küme, sunucu ve istemci sertifikalarının sağlandığı örnek bir küme yapılandırması aşağıda verilmiştir. Küme/sunucu/Smarproxy sertifikaları için, parmak izi ve ortak ad aynı sertifika türü için birlikte yapılandırılamaz.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Sertifika geçişi
Parmak izi yerine sertifika ortak adı kullandığınızda, sertifika geçişi bir küme yapılandırma yükseltmesi gerektirmez. Verenin parmak izi yükseltmeleri için yeni parmak izi listesinin eski listeyle kesişmesini sağlayın. İlk olarak yeni veren parmak izleriyle bir yapılandırma yükseltmesi yapmanız ve sonra yeni sertifikaları (küme/sunucu sertifikası ve veren sertifikaları) depoya yüklemeniz gerekir. Yeni veren sertifikasını yükledikten sonra sertifika deposundaki eski veren sertifikayı en az iki saat boyunca tutun.
Verenin mağazalarını kullanıyorsanız, verenin sertifika geçişi için hiçbir yapılandırma yükseltmesinin gerçekleştirilmesi gerekmez. Yeni veren sertifikasını ilgili sertifika deposunda son sona erme tarihi ile yükleyip, eski veren sertifikayı birkaç saat sonra kaldırın.

## <a name="acquire-the-x509-certificates"></a>X. 509.440 sertifikalarını alma
Küme içindeki iletişimin güvenliğini sağlamak için, önce Küme düğümleriniz için X. 509.440 sertifikaları edinmeniz gerekir. Ayrıca, bu kümeyle olan bağlantıyı yetkili makinelerle/kullanıcılarla sınırlandırmak için, istemci makinelere yönelik sertifikalar edinmeniz ve yüklemeniz gerekir.

Üretim iş yüklerini çalıştıran kümeler için, kümeyi güvenli hale getirmek için bir [sertifika yetkilisi (CA)](https://en.wikipedia.org/wiki/Certificate_authority)tarafından imzalanan X. 509.440 sertifikası kullanın. Bu sertifikaları edinme hakkında daha fazla bilgi için bkz. [sertifika edinme](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

Sertifikanın düzgün çalışması için sahip olması gereken birçok özellik vardır:

* Sertifikanın sağlayıcısı **Microsoft IYILEŞTIRILMIŞ RSA ve AES şifreleme sağlayıcısı** olmalıdır

* Bir RSA anahtarı oluştururken, anahtarın **2048 bit** olduğundan emin olun.

* Anahtar kullanımı uzantısının dijital Imzaya sahip bir değeri vardır **, anahtar şifreleme (a0)**

* Gelişmiş anahtar kullanımı uzantısında **sunucu kimlik doğrulaması** (OID: 1.3.6.1.5.5.7.3.1) ve **istemci kimlik doğrulaması** (OID: 1.3.6.1.5.5.7.3.2) değerleri bulunur

Test amaçları için kullandığınız kümeler için otomatik olarak imzalanan bir sertifika kullanmayı tercih edebilirsiniz.

Diğer sorular için [sık sorulan sertifika sorularını](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions)inceleyin.

## <a name="optional-create-a-self-signed-certificate"></a>İsteğe bağlı: otomatik olarak imzalanan sertifika oluşturma
Doğru şekilde güvenliği sağlanabilen otomatik olarak imzalanan bir sertifika oluşturmanın bir yolu, C:\Program Files\Microsoft SDKs\Service Fabric\clustersetup\securedizinindeki Service Fabric SDK klasöründe CertSetup.ps1 betiğini kullanmaktır. Sertifikanın varsayılan adını değiştirmek için bu dosyayı düzenleyin. (CN = ServiceFabricDevClusterCert değerini arayın.) Bu betiği olarak çalıştırın `.\CertSetup.ps1 -Install` .

Şimdi sertifikayı korumalı bir parolayla bir. pfx dosyasına dışarı aktarın. İlk olarak, sertifikanın parmak izini alın. 
1. **Başlat** menüsünde **bilgisayar sertifikalarını Yönet**' i çalıştırın. 

2. **Yerel bilgisayar \ kişisel** klasörüne gidin ve oluşturduğunuz sertifikayı bulun. 

3. Açmak için sertifikaya çift tıklayın, **Ayrıntılar** sekmesini seçin ve **parmak izi** alanına gidin. 

4. Alanları kaldırın ve parmak izi değerini aşağıdaki PowerShell komutuna kopyalayın. 

5. `String`Bunu korumak için değeri uygun bir güvenli parolayla değiştirin ve PowerShell 'de aşağıdakileri çalıştırın:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Makinede yüklü bir sertifikanın ayrıntılarını görmek için aşağıdaki PowerShell komutunu çalıştırın:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternatif olarak, bir Azure aboneliğiniz varsa [Azure Resource Manager kullanarak Service Fabric kümesi oluşturma](service-fabric-cluster-creation-via-arm.md)bölümündeki adımları izleyin.

## <a name="install-the-certificates"></a>Sertifikaları yükler
Sertifikalarınızın ardından bunları küme düğümlerine yükleyebilirsiniz. Düğümlerinizin en son Windows PowerShell 3. x üzerinde yüklü olması gerekir. Küme ve sunucu sertifikaları ve tüm ikincil sertifikalar için her düğüm üzerinde bu adımları yineleyin.

1. . Pfx dosyasını veya dosyalarını düğüme kopyalayın.

2. Yönetici olarak bir PowerShell penceresi açın ve aşağıdaki komutları girin. *$PSWD* , bu sertifikayı oluşturmak için kullandığınız parolayla değiştirin. *$PfxFilePath* , bu düğüme kopyalanmış. pfx tam yoluyla değiştirin.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Ağ hizmeti hesabı altında çalışan Service Fabric işlemin aşağıdaki betiği çalıştırarak kullanabilmesi için, bu sertifikadaki erişim denetimini ayarlayın. Hizmet hesabı için sertifika ve **ağ hizmetinin** parmak izini girin. Sertifika üzerindeki ACL 'lerin,   >  **bilgisayar sertifikalarını Yönet** ' de sertifikayı açarak ve   >  **özel anahtarları Yönet**' in tüm görevlere bakarak doğru olup olmadığını kontrol edebilirsiniz.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Her sunucu sertifikası için önceki adımları tekrarlayın. Ayrıca, bu adımları, kümeye erişime izin vermek istediğiniz makinelere istemci sertifikalarını yüklemek için de kullanabilirsiniz.

## <a name="create-the-secure-cluster"></a>Güvenli kümeyi oluşturma
Dosyadaki ClusterConfig.X509.MultiMachine.jsgüvenlik bölümünü yapılandırdıktan sonra, düğümleri yapılandırmak ve tek başına kümeyi oluşturmak için [küme oluşturma](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) bölümüne devam edebilirsiniz. Kümeyi oluştururken ClusterConfig.X509.MultiMachine.jsdosyayı kullanmayı unutmayın. Örneğin, komutunuz aşağıdaki gibi görünebilir:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Güvenli tek başına Windows kümesinin başarıyla çalışmasını ve bu sunucuya bağlanmak için kimliği doğrulanmış istemcileri ayarlamayı doğruladıktan sonra, bağlanmak için [PowerShell kullanarak bir kümeye bağlanma](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) bölümündeki adımları izleyin. Örnek:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Daha sonra bu kümeyle çalışmak için diğer PowerShell komutlarını çalıştırabilirsiniz. Örneğin, bu güvenli kümedeki düğümlerin listesini göstermek için [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) komutunu çalıştırabilirsiniz.


Kümeyi kaldırmak için, Service Fabric paketini indirdiğiniz kümedeki düğüme bağlanın, bir komut satırı açın ve paket klasörüne gidin. Şimdi şu komutu çalıştırın:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Yanlış sertifika yapılandırması, kümenin dağıtım sırasında çalışmasını engelleyebilir. Güvenlik sorunlarını kendi kendine tanılamak için Olay Görüntüleyicisi grup **Uygulamaları ve Hizmetleri günlüklerine** bakın  >  **Microsoft-Service Fabric**.
> 
> 
