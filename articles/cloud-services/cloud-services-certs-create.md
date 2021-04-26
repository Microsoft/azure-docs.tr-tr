---
title: Cloud Services (klasik) ve yönetim sertifikaları | Microsoft Docs
description: Bulut hizmetleri ve Azure 'da yönetim API 'SI ile kimlik doğrulaması için sertifika oluşturma ve dağıtma hakkında bilgi edinin.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c73f9812f344eecf4e51f43405b48693ddfa191b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739743"
---
# <a name="certificates-overview-for-azure-cloud-services-classic"></a>Azure Cloud Services sertifikalara genel bakış (klasik)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Sertifikalar, Azure 'da bulut Hizmetleri ([hizmet sertifikaları](#what-are-service-certificates)) ve yönetim API 'siyle ([yönetim sertifikaları](#what-are-management-certificates)) kimlik doğrulaması için kullanılır. Bu konu, her iki sertifika türü için genel bir bakış sağlar ve bunları Azure 'a [oluşturma](#create) ve dağıtma.

Azure 'da kullanılan sertifikalar x. 509.440 v3 sertifikalardır ve başka bir güvenilen sertifika tarafından imzalanabilir veya otomatik olarak imzalanabilir. Kendinden imzalı bir sertifika kendi oluşturucusu tarafından imzalanır, bu nedenle varsayılan olarak güvenilir değildir. Tarayıcıların çoğu bu sorunu yok sayabilir. Bulut hizmetlerinizi geliştirirken ve sınarken yalnızca otomatik olarak imzalanan sertifikalar kullanmanız gerekir. 

Azure tarafından kullanılan sertifikalar bir ortak anahtar içerebilir. Sertifikaların, bunları belirsiz bir şekilde tanımlamak için bir yol sağlayan bir parmak izi vardır. Bu parmak izi, bir bulut hizmetinin hangi sertifikayı kullanması gerektiğini belirlemek için Azure [yapılandırma dosyasında](cloud-services-configure-ssl-certificate-portal.md) kullanılır. 

>[!Note]
>Azure Cloud Services, AES256-SHA256 şifreli sertifikasını kabul etmez.

## <a name="what-are-service-certificates"></a>Hizmet sertifikaları nelerdir?
Hizmet sertifikaları, bulut hizmetlerine iliştirilir ve hizmete ve hizmetten güvenli iletişimi etkinleştirir. Örneğin, bir Web rolü dağıttıysanız, sunulan bir HTTPS uç noktasının kimliğini doğrulayabilecek bir sertifika sağlamak istersiniz. Hizmet tanımınızda tanımlanan hizmet sertifikaları, rolünüzün bir örneğini çalıştıran sanal makineye otomatik olarak dağıtılır. 

Hizmet sertifikalarını Azure portalı veya klasik dağıtım modelini kullanarak Azure'a yükleyebilirsiniz. Hizmet sertifikaları belirli bir bulut hizmeti ile ilişkilendirilir. Bunlar, hizmet tanımı dosyasındaki bir dağıtıma atanır.

Hizmet sertifikaları hizmetinizden ayrı olarak yönetilebilir ve farklı bireyler tarafından yönetilebilir. Örneğin, bir geliştirici, bir BT yöneticisinin daha önce Azure 'a yüklediği bir sertifikaya başvuran bir hizmet paketini karşıya yükleyebilir. BT yöneticisi, yeni bir hizmet paketini karşıya yüklemeye gerek kalmadan bu sertifikayı yönetebilir ve yenileyebilir (hizmetin yapılandırmasını değiştirerek). Yeni bir hizmet paketi olmadan güncelleştirme, sertifikanın mantıksal adı, mağaza adı ve konumu hizmet tanım dosyasında olduğundan ve sertifika parmak izi hizmet yapılandırma dosyasında belirtildiği için mümkün değildir. Sertifikayı güncelleştirmek için yalnızca yeni bir sertifika yüklemeniz ve hizmet yapılandırma dosyasındaki parmak izi değerini değiştirmeniz gerekir.

>[!Note]
>[Cloud SERVICES SSS-yapılandırma ve yönetim](cloud-services-configuration-and-management-faq.md) makalesinde sertifikalar hakkında bazı yararlı bilgiler bulunur.

## <a name="what-are-management-certificates"></a>Yönetim sertifikaları nelerdir?
Yönetim sertifikaları, klasik dağıtım modeliyle kimlik doğrulaması yapmanıza izin verir. Birçok program ve araç (Visual Studio veya Azure SDK gibi), çeşitli Azure hizmetlerinin yapılandırılmasını ve dağıtımını otomatik hale getirmek için bu sertifikaları kullanır. Bunlar gerçekten bulut hizmetleriyle ilgili değildir. 

> [!WARNING]
> Dikkat et! Bu tür sertifikalar, bunlarla ilişkili oldukları aboneliği yönetmek için kimliğini doğrulayan herkese izin verir. 
> 
> 

### <a name="limitations"></a>Sınırlamalar
Abonelik başına 100 yönetim sertifikası sınırı vardır. Ayrıca, belirli bir hizmet yöneticisinin Kullanıcı KIMLIĞI altındaki tüm abonelikler için 100 yönetim sertifikası sınırlaması vardır. Hesap yöneticisinin Kullanıcı KIMLIĞI zaten 100 yönetim sertifikası eklemek için kullanılmışsa ve daha fazla sertifika olması gerekiyorsa, ek sertifikaları eklemek için ortak yönetici ekleyebilirsiniz. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Otomatik olarak imzalanan yeni bir sertifika oluştur
Bu ayarlara bağlı oldukları sürece kendinden imzalı bir sertifika oluşturmak için kullanılabilen herhangi bir aracı kullanabilirsiniz:

* Bir X. 509.440 sertifikası.
* Ortak anahtar içerir.
* Anahtar değişimi (. pfx dosyası) için oluşturulur.
* Konu adı, bulut hizmetine erişmek için kullanılan etki alanıyla aynı olmalıdır.

    > Cloudapp.net (veya Azure ile ilgili herhangi bir) etki alanı için TLS/SSL sertifikası edinemezsiniz; Sertifikanın konu adı, uygulamanıza erişmek için kullanılan özel etki alanı adıyla aynı olmalıdır. Örneğin, **contoso.cloudapp.net** değil, **contoso.net**.

* En az 2048 bit şifreleme.
* **Yalnızca hizmet sertifikası**: istemci tarafı sertifika, *Kişisel* sertifika deposunda bulunmalıdır.

Windows 'da, `makecert.exe` yardımcı programıyla veya IIS 'de sertifika oluşturmanın iki kolay yolu vardır.

### <a name="makecertexe"></a>Makecert.exe
Bu yardımcı program kullanımdan kaldırılmıştır ve artık burada açıklanmamıştır. Daha fazla bilgi için [Bu MSDN makalesine](/windows/desktop/SecCrypto/makecert)bakın.

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Sertifikayı bir etki alanı yerine bir IP adresiyle kullanmak istiyorsanız,-DnsName parametresindeki IP adresini kullanın.


Bu [sertifikayı yönetim portalı ile birlikte](/previous-versions/azure/azure-api-management-certs)kullanmak istiyorsanız, bir **. cer** dosyasına dışarı aktarın:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Internet 'te bunun IIS ile nasıl yapılacağını kapsayan çok sayıda sayfa vardır. [İşte](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) bu kadar iyi bir fikir buldum. 

### <a name="linux"></a>Linux
[Bu](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) makalede SSH ile sertifikaların nasıl oluşturulacağı açıklanır.

## <a name="next-steps"></a>Sonraki adımlar
[Hizmet sertifikanızı Azure Portal yükleyin](cloud-services-configure-ssl-certificate-portal.md).

Azure portal bir [Yönetim API 'si sertifikasını](/previous-versions/azure/azure-api-management-certs) karşıya yükleyin.