---
title: Kaynak Yöneticisi şablonuyla App Service ortam oluşturma-Azure
description: Bir Kaynak Yöneticisi şablonu kullanarak dış veya ıLB Azure App Service ortamının nasıl oluşturulduğunu açıklar.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 60c9d89bc0ab7c63e779a7cadece863540e827aa
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470592"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak ATıCı oluşturma

## <a name="overview"></a>Genel Bakış

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service ortamları (ASEs), bir Azure sanal ağı 'ndaki (VNet) bir iç adres üzerinde internet erişimli bir uç nokta veya bir uç nokta ile oluşturulabilir. İç uç nokta ile oluşturulduğunda, bu uç nokta iç yük dengeleyici (ıLB) adlı bir Azure bileşeni tarafından sağlanır. Bir iç IP adresindeki Ao 'un bir ıLB Ao adı verilir. Ortak bir uç noktaya sahip Ao 'nun dış Ao adı verilir. 

Ao, Azure portal veya Azure Resource Manager şablonu kullanılarak oluşturulabilir. Bu makalede, Kaynak Yöneticisi şablonları ile bir dış aya veya ıLB ate oluşturmak için gereken adımlar ve söz dizimi gösterilmektedir. Azure portal bir AO oluşturma hakkında bilgi edinmek için bkz. [bir dış aya][MakeExternalASE] oluşturma veya [ILB aşiri yapma][MakeILBASE].

Azure portal bir ASE oluşturduğunuzda, sanal ortamınızı aynı anda oluşturabilir veya dağıtmak için önceden var olan bir sanal ağı seçebilirsiniz. Bir şablondan bir AO oluşturduğunuzda şunları yapmanız gerekir: 

* Kaynak Yöneticisi VNet.
* Bu sanal ağdaki bir alt ağ. Gelecekteki büyüme ve ölçekleme ihtiyaçlarına uyum sağlamak için 256 adresi ile `/24` Ao alt ağ boyutunu öneririz. ATıCı oluşturulduktan sonra boyutu değiştiremezsiniz.
* Sanal ağınızdan kaynak KIMLIĞI. Bu bilgileri, sanal ağ özelliklerindeki Azure portal edinebilirsiniz.
* Dağıtmak istediğiniz abonelik.
* Dağıtmak istediğiniz konum.

ATıCı oluşturma işlemini otomatikleştirmek için:

1. Bir şablondan ATıCı oluşturun. Bir dış Ao oluşturursanız, bu adımdan sonra işiniz tamamlanmış olursunuz. ILB aşırı oluşturursanız, birkaç şey daha vardır.

2. ILB ASE 'niz oluşturulduktan sonra, ıLB ASE etki alanınız ile eşleşen bir SSL sertifikası karşıya yüklenir.

3. Karşıya yüklenen SSL sertifikası, "varsayılan" SSL sertifikası olarak ıLB AŞIRE atanır.  Bu sertifika, Ao 'ya atanan ortak kök etki alanını (örneğin, https://someapp.mycustomrootdomain.com)) kullandıklarında ıLB Ao üzerindeki uygulamalara SSL trafiği için kullanılır.


## <a name="create-the-ase"></a>ATıCı oluşturma
Bir ATıCı ve ilişkili parametreler dosyası oluşturan bir Kaynak Yöneticisi şablonu, GitHub 'daki [bir örnekte][quickstartasev2create] mevcuttur.

ILB Ao oluşturmak istiyorsanız bu Kaynak Yöneticisi şablonu [örneklerini][quickstartilbasecreate]kullanın. Bu kullanım örneğine katarlar. *Azuredeploy. Parameters. JSON* dosyasındaki parametrelerin çoğu ILB ases ve dış ASE oluşturma için ortaktır. Aşağıdaki liste, özel notun parametrelerini çağırır ya da benzersiz olan bir ıLB AI oluşturduğunuzda:

* *internalLoadBalancingMode*: çoğu durumda bunu 3 ' e ayarlayın, bu da 80/443 bağlantı noktalarında HTTP/HTTPS trafiği ve ASE 'de FTP hizmeti tarafından dinlenen denetim/veri kanalı bağlantı noktaları, ILB tarafından ayrılan bir sanal ağ iç adresine bağlanacaktır. Bu özellik 2 olarak ayarlandıysa, yalnızca FTP hizmetiyle ilgili bağlantı noktaları (denetim ve veri kanalları) bir ıLB adresine bağlanır. HTTP/HTTPS trafiği, genel VIP üzerinde kalır.
* *DnsSuffix*: Bu parametre Ao 'ya atanan varsayılan kök etki alanını tanımlar. Azure App Service ortak çeşitlemesiyle, tüm Web Apps için varsayılan kök etki alanı *azurewebsites.net*' dir. ILB asa, müşterinin sanal ağı için dahili olduğundan, ortak hizmetin varsayılan kök etki alanını kullanmak mantıklı değildir. Bunun yerine, bir ıLB ASE 'nin bir şirketin iç sanal ağı içinde kullanım açısından anlamlı bir varsayılan kök etki alanı olması gerekir. Örneğin, contoso Corporation, yalnızca contoso 'nun sanal ağı dahilinde çözümlenebilmeleri ve erişilebilir olması amaçlanan uygulamalar için varsayılan *internal-contoso.com* kök etki alanını kullanabilir. 
* *ıpssladdresscount*: ILB ASE yalnızca tek BIR ILB adresine sahip olduğundan, bu parametre *azuredeploy. JSON* dosyasında varsayılan değeri 0 olarak belirler. ILB ATıCı için açık IP SSL adresi yoktur. Bu nedenle, bir ıLB asa için IP-SSL adres havuzu sıfır olarak ayarlanmalıdır. Aksi takdirde, sağlama hatası oluşur. 

*Azuredeploy. Parameters. JSON* dosyası doldurulduktan sonra, PowerShell kod PARÇACıĞıNı kullanarak Ao 'yu oluşturun. Dosya yollarını, makinenizde Kaynak Yöneticisi Şablon dosyası konumlarıyla eşleşecek şekilde değiştirin. Kaynak Yöneticisi dağıtım adı ve kaynak grubu adı için kendi değerlerinizi sağlamayı unutmayın:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

ATıCı 'nin oluşturulması yaklaşık bir saat sürer. Daha sonra ASE, dağıtımı tetikleyen abonelik için ASE 'lerin listesinde görüntülenir.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>"Varsayılan" SSL sertifikasını karşıya yükleme ve yapılandırma
Bir SSL sertifikası, uygulamalar için SSL bağlantıları kurmak için kullanılan "varsayılan" SSL sertifikası olarak ATıCı ile ilişkilendirilmelidir. ATıCı 'in varsayılan DNS son eki *internal-contoso.com*ise, https://some-random-app.internal-contoso.com bir bağlantı, * *. internal-contoso.com*için geçerli bir SSL sertifikası gerektirir. 

İç sertifika yetkililerini kullanarak, bir dış verenden sertifika satın alarak veya otomatik olarak imzalanan bir sertifika kullanarak geçerli bir SSL sertifikası elde edin. SSL sertifikasının kaynağından bağımsız olarak, aşağıdaki sertifika özniteliklerinin düzgün şekilde yapılandırılması gerekir:

* **Konu**: Bu özniteliğin * *. Your-root-Domain-here.com*olarak ayarlanması gerekir.
* **Konu alternatif adı**: Bu öznitelik hem * *. Your-root-Domain-here.com* hem de * *. scm.Your-root-Domain-here.com*içermelidir. Her uygulamayla ilişkili SCM/kudu sitesine yönelik SSL bağlantıları, *Your-app-name.scm.Your-root-Domain-here.com*formunun bir adresini kullanır.

Birlikte geçerli bir SSL sertifikasıyla, iki ek hazırlık adımı gerekir. SSL sertifikasını .pfx dosyası olarak dönüştürün/kaydedin. . Pfx dosyasının tüm ara ve kök sertifikaları içermesi gerektiğini unutmayın. Bir parola ile güvenli hale getirin.

SSL sertifikası bir Kaynak Yöneticisi şablonu kullanılarak yüklendiğinden,. pfx dosyasının Base64 dizesine dönüştürülmesi gerekir. Kaynak Yöneticisi şablonlar metin dosyaları olduğundan,. pfx dosyasının Base64 dizesine dönüştürülmesi gerekir. Bu şekilde, şablonun bir parametresi olarak dahil edilebilir.

Aşağıdaki PowerShell kod parçacığını kullanarak şunları yapın:

* Kendinden imzalı bir sertifika oluşturun.
* Sertifikayı bir. pfx dosyası olarak dışarı aktarın.
* . Pfx dosyasını Base64 kodlamalı bir dizeye dönüştürün.
* Base64 kodlamalı dizeyi ayrı bir dosyaya kaydedin. 

Base64 kodlaması için bu PowerShell kodu [PowerShell betikleri blogundan][examplebase64encoding]uyarlanmıştır:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

SSL sertifikası başarılı bir şekilde oluşturulup Base64 kodlamalı bir dizeye dönüştürüldükten sonra, GitHub 'da [varsayılan SSL sertifikasını yapılandırmak][quickstartconfiguressl] Kaynak Yöneticisi şablonu örneğini kullanın. 

*Azuredeploy. Parameters. JSON* dosyasındaki parametreler burada listelenmiştir:

* *Appserviceenvironmentname*: yapılandırılmakta olan ıLB atıcı 'in adı.
* *Existingaselocation*: ıLB atıcı 'Nin dağıtıldığı Azure bölgesini içeren metin dizesi.  Örneğin: "Orta Güney ABD".
* *Pfxblobstring*:. pfx dosyasının based64 kodlu dize temsili. Daha önce gösterilen kod parçacığını kullanın ve "exportedcert. pfx. B64" içinde bulunan dizeyi kopyalayın. Öğesini *Pfxblobstring* özniteliğinin değeri olarak yapıştırın.
* *parola*:. pfx dosyasının güvenliğini sağlamak için kullanılan parola.
* *certificateThumbprint*: sertifikanın parmak izi. Bu değeri PowerShell 'den alırsanız (örneğin, *$Certificate.* Önceki kod parçacığındaki parmak izi), değeri olduğu gibi kullanabilirsiniz. Windows sertifikası iletişim kutusundan değeri kopyalarsanız, gereksiz alanları sökmesini unutmayın. *CertificateThumbprint* , AF3143EB61D43F6727842115BB7F17BBCECAECAE gibi görünmelidir.
* *CertificateName*: sertifikayı belirlemek için kullanılan seçtiğiniz kolay bir dize tanımlayıcısı. Ad, SSL sertifikasını temsil eden *Microsoft. Web/sertifikaları* varlığı için benzersiz kaynak yöneticisi tanımlayıcısının bir parçası olarak kullanılır. *Ad şu soneke bitmelidir:* \_yourASENameHere_InternalLoadBalancingASE. Azure portal, bu son eki sertifikanın ıLB özellikli bir AO 'nun güvenliğini sağlamak için kullanılan bir gösterge olarak kullanır.

Kısaltılmış bir *azuredeploy. Parameters. JSON* örneği burada gösterilmektedir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

*Azuredeploy. Parameters. JSON* dosyası doldurulduktan sonra, PowerShell kod parçacığını kullanarak varsayılan SSL sertifikasını yapılandırın. Dosya yollarını, Kaynak Yöneticisi şablon dosyalarının makinenizde bulunduğu yere uyacak şekilde değiştirin. Kaynak Yöneticisi dağıtım adı ve kaynak grubu adı için kendi değerlerinizi sağlamayı unutmayın:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Değişikliği uygulamak için Ao ön ucu başına yaklaşık 40 dakika sürer. Örneğin, iki ön uç kullanan varsayılan boyutlu bir asa için, şablon bir saat ve 20 dakika sürer. Şablon çalışırken ATıCı ölçeklendiremez.  

Şablon bittikten sonra, ıLB ASE 'deki uygulamalara HTTPS üzerinden erişilebilir. Bağlantılar, varsayılan SSL sertifikası kullanılarak güvenli hale getirilir. Varsayılan SSL sertifikası, ıLB Ao 'daki uygulamalar uygulama adının ve varsayılan ana bilgisayar adının bir birleşimi kullanılarak giderildiği zaman kullanılır. Örneğin, https://mycustomapp.internal-contoso.com * *. internal-contoso.com*IÇIN varsayılan SSL sertifikasını kullanır.

Ancak, genel çoklu kiracı hizmetinde çalışan uygulamalar gibi geliştiriciler de tek tek uygulamalar için özel ana bilgisayar adları yapılandırabilir. Ayrıca, bireysel uygulamalar için benzersiz SNI SSL sertifika bağlamaları da yapılandırabilir.

## <a name="app-service-environment-v1"></a>App Service Ortamı v1 ##
App Service Ortamının iki sürümü vardır: ASEv1 ve ASEv2. Yukarıdaki bilgiler ASEv2’yi temel alır. Bu bölümde ASEv1 ile ASEv2 arasındaki farklar gösterilmektedir.

ASEv1 ' de tüm kaynakları el ile yönetirsiniz. Buna ön uçlar, çalışanlar ve IP tabanlı SSL için kullanılan IP adresleri dahildir. App Service planınızı ölçeklendirebilmeniz için önce, barındırmak istediğiniz çalışan havuzunu ölçeklendirmeniz gerekir.

ASEv1, ASEv2’den farklı bir fiyatlandırma modeli kullanır. ASEv1’de ayrılmış her vCPU için ücret ödersiniz. Bu, ön uçlar veya herhangi bir iş yükünü barındırmayan çalışanlar için kullanılan vCPU 'Ları içerir. ASEv1’de bir ASE’nin varsayılan en büyük ölçek boyutu toplam 55 konaktır. Buna çalışanlar ve ön uçlar dahildir. ASEv1’in bir avantajı, klasik bir sanal ağa ve bir Resource Manager sanal ağına dağıtılabilmesidir. ASEv1 hakkında daha fazla bilgi edinmek için bkz. [App Service ortamı v1 girişi][ASEv1Intro].

Bir Kaynak Yöneticisi şablonu kullanarak bir ASEv1 oluşturmak için, bkz. [bir kaynak yöneticisi şablonuyla ILB Ao v1 oluşturma][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
