---
title: Sertifikalar ve App Service Ortamı-Azure
description: Ao 'da sertifikalarla ilgili çok sayıda konuyu açıklayın
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a8b8e7270851c71869b1a67f0f0f0ba2187f0e87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470663"
---
# <a name="certificates-and-the-app-service-environment"></a>Sertifikalar ve App Service Ortamı 

App Service Ortamı (ASE), Azure sanal ağınız (VNet) içinde çalışan Azure App Service bir dağıtımdır. İnternet erişimli bir uygulama uç noktası veya VNet 'iniz içindeki bir uygulama uç noktası ile dağıtılabilir. Ao 'yu internet 'ten erişilebilen bir uç noktayla dağıtırsanız, bu dağıtıma dış Ao denir. ASE 'yi VNet 'iniz içindeki bir uç noktayla dağıtırsanız, bu dağıtıma ıLB ASE denir. ILB ATıCı hakkında daha fazla bilgiyi [oluşturma ve kullanma](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) hakkında bilgi edinebilirsiniz.

ATıCı, tek bir kiracı sistemidir. Tek bir kiracı olduğundan, yalnızca çok kiracılı App Service kullanılamayan bir AO ile kullanılabilen bazı özellikler vardır. 

## <a name="ilb-ase-certificates"></a>ILB ATıCı sertifikaları 

Dış Ao kullanıyorsanız, uygulamalarınıza [AppName] adresinden ulaşılırsa. [asename]. p. azurewebsites. net. Varsayılan olarak, tüm ASE 'ler, hatta ıLB ASE, bu biçimi izleyen sertifikalarla oluşturulur. ILB Ao 'a sahip olduğunuzda, uygulamalar ıLB Ao oluştururken belirttiğiniz etki alanı adına göre yapılır. Uygulamaların SSL 'yi desteklemesi için sertifikaları karşıya yüklemeniz gerekir. İç sertifika yetkililerini kullanarak, bir dış verenden sertifika satın alarak veya otomatik olarak imzalanan bir sertifika kullanarak geçerli bir SSL sertifikası elde edin. 

ILB Ao ile sertifikaları yapılandırmak için iki seçenek vardır.  ILB aşırı için bir joker karakter varsayılan sertifikası ayarlayabilir veya Ao 'daki ayrı Web uygulamalarında sertifikalar ayarlayabilirsiniz.  Yaptığınız seçim ne olursa olsun, aşağıdaki sertifika özniteliklerinin düzgün şekilde yapılandırılması gerekir:

- **Konu:** Bu öznitelik * olarak ayarlanmalıdır. [-root-Domain-Here] joker karakter ıLB AI sertifikası. Uygulamanız için sertifika oluşturuyorsanız, [AppName] olması gerekir. [root-Domain-buraya]
- **Konu diğer adı:** Bu öznitelik, her ikisini de içermelidir. [-root-Domain-Here] ve *. scm. joker karakter ıLB Ao sertifikası için [-root-Domain-Here]. Uygulamanız için sertifika oluşturuyorsanız, [AppName] olması gerekir. [root-Domain-Here] ve [AppName]. scm. [root-Domain-Here].

Üçüncü bir varyant olarak, bir joker karakter başvurusu kullanmak yerine, tek tek uygulama adlarınızın tamamını sertifikanın SAN 'ında içeren bir ıLB Ade sertifikası oluşturabilirsiniz. Bu yöntemde sorun, ASE 'ye yerleştirmekte olduğunuz uygulamaların adlarını bilmeniz veya ıLB ASE sertifikasını güncelleştirmeyi tutmanız gerekir.

### <a name="upload-certificate-to-ilb-ase"></a>Sertifikayı ıLB Ao 'ya yükle 

Portal 'da bir ıLB Ao oluşturulduktan sonra, ıLB Ao için sertifikanın ayarlanması gerekir. Sertifika ayarlanana kadar Ao, sertifikanın ayarlanmadığını belirten bir başlık gösterir.  

Karşıya yüklediğiniz sertifika bir. pfx dosyası olmalıdır. Sertifika karşıya yüklendikten sonra ATıCı, sertifikayı ayarlamak için bir ölçeklendirme işlemi gerçekleştirir. 

ASE 'yi oluşturamaz ve sertifikayı portalda tek bir eylem olarak veya bir şablonda karşıya yükleyemezsiniz. Ayrı bir eylem olarak, [bir](./create-from-template.md) şablon kullanarak bir şablonu kullanarak sertifikayı karşıya yükleyebilirsiniz.  

Test için otomatik olarak imzalanan bir sertifika oluşturmak istiyorsanız aşağıdaki PowerShell bitini kullanabilirsiniz:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Kendinden imzalı bir sertifika oluştururken, konu adının CN = {ASE_NAME_HERE} _InternalLoadBalancingASE biçiminde olduğundan emin olmanız gerekir.

## <a name="application-certificates"></a>Uygulama sertifikaları 

ASA 'da barındırılan uygulamalar, çok kiracılı App Service bulunan uygulama merkezli sertifika özelliklerini kullanabilir. Bu özellikler şunları içerir:  

- SNı sertifikaları 
- Yalnızca bir dış Ao ile desteklenen IP tabanlı SSL.  ILB ATıCı, IP tabanlı SSL 'yi desteklemez.
- Anahtar Kasası barındırılan sertifikaları 

Bu sertifikaları yükleme ve yönetmeye yönelik yönergeler [Azure App Service ' de SSL sertifikası Ekle](../configure-ssl-certificate.md)' de kullanılabilir.  Sertifikaları Web uygulamanıza atadığınız özel bir etki alanı adıyla eşleşecek şekilde yapılandırıyorsanız, bu yönergeler de yeterli olur. Bir ıLB Ao Web uygulaması için sertifikayı varsayılan etki alanı adıyla karşıya yüklüyorsanız, daha önce belirtildiği gibi sertifikanın SAN 'ında SCM sitesini belirtin. 

## <a name="tls-settings"></a>TLS ayarları 

Bir uygulama düzeyinde TLS ayarı yapılandırabilirsiniz.  

## <a name="private-client-certificate"></a>Özel istemci sertifikası 

Yaygın kullanım durumu, uygulamanızı istemci-sunucu modelinde istemci olarak yapılandırmaktır. Sunucunuzu özel bir CA sertifikasıyla güvenli hale getirmek istiyorsanız, istemci sertifikasını uygulamanıza yüklemeniz gerekecektir.  Aşağıdaki yönergeler, uygulamanızın üzerinde çalıştığı çalışanların truststore 'a sertifika yükler. Sertifikayı bir uygulamaya yüklerseniz, sertifikayı yeniden karşıya yüklemeden aynı App Service planındaki diğer uygulamalarınızla kullanabilirsiniz.

Uygulamanızı kendi AŞIRINIZDEKI uygulamanıza yüklemek için:

1. Sertifikanız için bir *. cer* dosyası oluşturun. 
2. Azure portal sertifikaya ihtiyacı olan uygulamaya git
3. Uygulamadaki SSL ayarları ' na gidin. Sertifikayı karşıya yükle ' ye tıklayın. Ortak seçeneğini belirleyin. Yerel makine ' yi seçin. Bir ad girin. *. Cer* dosyanıza gözatıp seçin. Karşıya yükle ' yi seçin. 
4. Parmak izini kopyalayın.
5. Uygulama ayarları ' na gidin. Değer olarak parmak izine sahip WEBSITE_LOAD_ROOT_CERTIFICATES bir uygulama ayarı oluşturun. Birden çok sertifikanız varsa, bunları virgülle ayırarak aynı ayara ve şu şekilde boşluk olmaksızın koyabilirsiniz 

    84EC242A4EC7957817B8E48913E50953552DAFA6, 6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Sertifika, bu ayarı yapılandıran uygulamayla aynı App Service planındaki tüm uygulamalar tarafından kullanılabilir. Farklı bir App Service planındaki uygulamalar için kullanılabilir olması gerekiyorsa, bu App Service planındaki bir uygulamada uygulama ayarı işlemini tekrarlamanız gerekir. Sertifikanın ayarlandığını denetlemek için, kudu konsoluna gidin ve PowerShell hata ayıklama konsolunda aşağıdaki komutu verin:

    dir cert:\localmachine\root

Testi gerçekleştirmek için, otomatik olarak imzalanan bir sertifika oluşturabilir ve aşağıdaki PowerShell ile bir *. cer* dosyası oluşturabilirsiniz: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

