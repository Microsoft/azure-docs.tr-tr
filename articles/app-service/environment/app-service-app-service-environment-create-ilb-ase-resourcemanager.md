---
title: Azure Resource Manager şablonlarını kullanarak ıLB as oluşturma-App Service | Microsoft Docs
description: Azure Resource Manager şablonlarını kullanarak iç yük dengeleyici oluşturmayı öğrenin.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 862887e1e530bfdca4359e914b9a81c9360ac4dd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070420"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Azure Resource Manager Şablonlarını kullanarak ILB ASE oluşturma

> [!NOTE] 
> Bu makale App Service Ortamı v1 ile ilgilidir. Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış
App Service ortamlar, genel VIP yerine bir sanal ağ iç adresiyle oluşturulabilir.  Bu iç adres, iç yük dengeleyici (ıLB) adlı bir Azure bileşeni tarafından sağlanır.  Azure portal kullanarak bir ıLB Ao oluşturulabilir.  Ayrıca, Azure Resource Manager şablonları aracılığıyla Otomasyon kullanılarak da oluşturulabilir.  Bu makalede, Azure Resource Manager şablonlarıyla bir ıLB ATıCı oluşturmak için gereken adımlar ve söz dizimi gösterilmektedir.

ILB Ao 'nun oluşturulmasına neden olan üç adım vardır:

1. İlk olarak, genel bir VIP yerine iç yük dengeleyici adresi kullanılarak sanal bir ağda bir sanal ağ oluşturulur.  Bu adımın bir parçası olarak ıLB Ao 'ya bir kök etki alanı adı atanır.
2. ILB Ao oluşturulduktan sonra bir SSL sertifikası karşıya yüklenir.  
3. Karşıya yüklenen SSL sertifikası, "varsayılan" SSL sertifikası olarak ıLB Ao 'ya açıkça atanır.  Bu SSL sertifikası, uygulamalar Ao 'ya atanan ortak kök etki alanı kullanılarak giderilebilse ıLB Ao 'daki uygulamalara SSL trafiği için kullanılacaktır (ör. https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Temel ıLB ATıCı oluşturma
[Burada][quickstartilbasecreate]GitHub 'da bir örnek Azure Resource Manager şablonu ve ilişkili parametreler dosyası mevcuttur.

*Azuredeploy. Parameters. JSON* dosyasındaki parametrelerin çoğu, hem ILB ASE 'nin hem de genel bir VIP 'ye ait olan ASE 'lerin oluşturulması için ortaktır.  Aşağıdaki liste, özel notun parametrelerini çağırır veya bir ıLB Ao oluştururken benzersizdir:

* *internalLoadBalancingMode*:  Çoğu durumda bunu 3 ' e ayarlar, bu da 80/443 bağlantı noktalarında HTTP/HTTPS trafiği ve ASE 'de FTP hizmeti tarafından dinlenen denetim/veri kanalı bağlantı noktaları, ıLB tarafından ayrılmış bir sanal ağ iç adresine bağlanacak.  Bu özellik 2 olarak ayarlandıysa, yalnızca FTP hizmetiyle ilgili bağlantı noktaları (denetim ve veri kanalları) bir ıLB adresine bağlanır, ancak HTTP/HTTPS trafiği genel VIP üzerinde kalır.
* *DnsSuffix*:  Bu parametre Ao 'ya atanacak varsayılan kök etki alanını tanımlar.  Azure App Service ortak çeşitlemesiyle, tüm Web Apps için varsayılan kök etki alanı *azurewebsites.net*' dir.  Bununla birlikte, bir ıLB asa bir müşterinin sanal ağı için dahili olduğundan, ortak hizmetin varsayılan kök etki alanını kullanmak mantıklı değildir.  Bunun yerine, bir ıLB ASE 'nin bir şirketin iç sanal ağı içinde kullanım açısından anlamlı bir varsayılan kök etki alanı olması gerekir.  Örneğin, bir kuramsal contoso şirketi yalnızca contoso 'nun sanal ağı 'nda çözümlenebilecek ve erişilebilir olması amaçlanan uygulamalar için varsayılan *internal-contoso.com* kök etki alanını kullanabilir. 
* *ıpssladdresscount*:  ILB ASE yalnızca tek bir ıLB adresine sahip olduğundan, bu parametre *azuredeploy. JSON* dosyasında otomatik olarak 0 değerine ayarlanır.  ILB ASE için açık IP SSL adresi yoktur ve bu nedenle bir ıLB ASE için IP-SSL adres havuzu sıfır olarak ayarlanmalıdır, aksi takdirde bir sağlama hatası oluşur. 

ILB AI için *azuredeploy. Parameters. JSON* dosyası doldurulduktan sonra ıLB Ao daha sonra aşağıdaki PowerShell kod parçacığı kullanılarak oluşturulabilir.  Dosya yollarını, Azure Resource Manager şablon dosyalarının makinenizde bulunduğu yere uyacak şekilde değiştirin.  Ayrıca, Azure Resource Manager dağıtım adı ve kaynak grubu adı için kendi değerlerinizi sağlamayı unutmayın.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure Resource Manager şablonu gönderildikten sonra ıLB ATıCı 'in oluşturulması birkaç saat sürer.  Oluşturma işlemi tamamlandıktan sonra ıLB Ao, dağıtımı tetikleyen aboneliğin App Service ortamları listesindeki Portal UX 'ında görünür.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>"Varsayılan" SSL sertifikasını karşıya yükleme ve yapılandırma
ILB Ao oluşturulduktan sonra, uygulamalara SSL bağlantıları kurmak için kullanılan "varsayılan" SSL sertifikası ile birlikte bir SSL sertifikası ile ilişkilendirilmesi gerekir.  Kuramsal contoso Corporation örneği ile devam ederseniz, atıcı 'nin varsayılan DNS son eki *internal-contoso.com*ise, bir bağlantı *https://some-random-app.internal-contoso.com* , * *. internal-contoso.com*için geçerli bir SSL sertifikası gerektirir. 

İç CA 'Lar dahil, bir dış veren sertifikayı satın alma ve kendinden imzalı bir sertifika kullanma gibi geçerli bir SSL sertifikası almak için kullanabileceğiniz çeşitli yollar vardır.  SSL sertifikasının kaynağından bağımsız olarak, aşağıdaki sertifika özniteliklerinin doğru şekilde yapılandırılması gerekir:

* *Konu*:  Bu öznitelik * *. Your-root-Domain-here.com* olarak ayarlanmalıdır
* *Konu diğer adı*:  Bu öznitelik hem * *. Your-root-Domain-here.com*hem de * *. scm.Your-root-Domain-here.com*içermelidir.  İkinci girdinin nedeni, her uygulamayla ilişkili SCM/kudu sitesine yönelik SSL bağlantılarının *Your-app-name.scm.Your-root-Domain-here.com*formunun bir adresi kullanılarak yapılır.

Birlikte geçerli bir SSL sertifikasıyla, iki ek hazırlık adımı gerekir.  SSL sertifikasının bir. pfx dosyası olarak dönüştürülmesi/kaydedilmesi gerekir.  . Pfx dosyasının tüm ara ve kök sertifikaları içermesi gerektiğini ve ayrıca bir parolayla güvenli hale getirilmesi gerektiğini unutmayın.

Ardından, bir Azure Resource Manager şablonu kullanılarak SSL sertifikası karşıya yüklenebileceğinden, sonuç. pfx dosyasının Base64 dizesine dönüştürülmesi gerekir.  Azure Resource Manager şablonlar metin dosyaları olduğundan,. pfx dosyasının bir Base64 dizesine dönüştürülmesi gerekir, bu nedenle şablonun bir parametresi olarak eklenebilir.

Aşağıdaki PowerShell kod parçacığı, otomatik olarak imzalanan bir sertifika oluşturma, sertifikayı bir. pfx dosyası olarak dışarı aktarma,. pfx dosyasını Base64 kodlamalı bir dizeye dönüştürme ve sonra Base64 kodlamalı dizeyi ayrı bir dosyaya kaydetme örneği gösterir.  Base64 kodlaması için PowerShell kodu, [PowerShell betikleri blogundan][examplebase64encoding]uyarlanmıştır.

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

SSL sertifikası başarılı bir şekilde oluşturulup Base64 kodlamalı bir dizeye dönüştürüldükten sonra [varsayılan SSL sertifikasını yapılandırmak][configuringDefaultSSLCertificate] için GitHub 'daki örnek Azure Resource Manager şablonu kullanılabilir.

*Azuredeploy. Parameters. JSON* dosyasındaki parametreler aşağıda listelenmiştir:

* *Appserviceenvironmentname*:  Yapılandırılan ıLB Ao 'nun adı.
* *Existingaselocation*:  ILB ATıCı 'nin dağıtıldığı Azure bölgesini içeren metin dizesi.  Örneğin:  "Orta Güney ABD".
* *Pfxblobstring*:  . Pfx dosyasının based64 kodlamalı dize temsili.  Daha önce gösterilen kod parçacığını kullanarak, "exportedcert. pfx. B64" içinde bulunan dizeyi kopyalayın ve bunu *Pfxblobstring* özniteliğinin değeri olarak içine yapıştırın.
* *parola*:  . Pfx dosyasının güvenliğini sağlamak için kullanılan parola.
* *certificateThumbprint*:  Sertifikanın parmak izi.  Bu değeri PowerShell 'den alırsanız (ör. *$Certificate.* Önceki kod parçacığındaki parmak izi), değerini olduğu gibi kullanabilirsiniz.  Ancak, Windows sertifikası iletişim kutusundan değeri kopyalarsanız, gereksiz alanları sökmesini unutmayın.  *CertificateThumbprint* şöyle görünmelidir:  AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *CertificateName*:  Sertifikayı kimlik belirlemek için kullanılan seçtiğiniz kolay dize tanımlayıcısı.  Ad, SSL sertifikasını temsil eden *Microsoft. Web/Certificates* varlığı için benzersiz Azure Resource Manager tanımlayıcısının bir parçası olarak kullanılır.  Ad şu soneke sahip **olmalıdır** : \_yourASENameHere_InternalLoadBalancingASE.  Bu sonek, portal tarafından, bir ıLB özellikli bir AO 'nun güvenliğini sağlamak için sertifikanın kullanıldığı bir gösterge olarak kullanılır.

Kısaltılmış bir örnek *azuredeploy. Parameters. JSON* örneği aşağıda gösterilmiştir:

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

*Azuredeploy. Parameters. JSON* dosyası doldurulduktan sonra varsayılan SSL sertifikası aşağıdaki PowerShell kod parçacığı kullanılarak yapılandırılabilir.  Dosya yollarını, Azure Resource Manager şablon dosyalarının makinenizde bulunduğu yere uyacak şekilde değiştirin.  Ayrıca, Azure Resource Manager dağıtım adı ve kaynak grubu adı için kendi değerlerinizi sağlamayı unutmayın.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure Resource Manager şablonu gönderildikten sonra, değişikliği uygulamak için Ao ön ucu başına yaklaşık 40 dakika sürer.  Örneğin, iki ön uç kullanan varsayılan boyutta bir ATıCı ile, şablonun tamamlanması bir saat ve Yirmi dakika sürer.  Şablon çalışırken Ao ölçeği ölçeklenmez.  

Şablon tamamlandığında, ıLB ASE 'deki uygulamalara HTTPS üzerinden erişilebilir ve bağlantılar varsayılan SSL sertifikası kullanılarak güvenliği sağlanır.  Varsayılan SSL sertifikası, ıLB Ao 'daki uygulamalar uygulama adının bir birleşimi ve varsayılan ana bilgisayar adı kullanılarak giderildiği zaman kullanılacaktır.  Örneğin *https://mycustomapp.internal-contoso.com* , * *. internal-contoso.com*için varsayılan SSL sertifikasını kullanır.

Ancak, genel çok kiracılı hizmette çalışan uygulamalar gibi geliştiriciler de tek tek uygulamalar için özel ana bilgisayar adları yapılandırabilir ve ardından ayrı uygulamalar için benzersiz SNI SSL sertifika bağlamaları yapılandırabilir.  

## <a name="getting-started"></a>Başlarken
App Service ortamlarıyla çalışmaya başlamak için bkz. [giriş App Service ortamı](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

