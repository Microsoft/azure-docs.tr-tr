---
title: Microsoft Azure Stack hub hazırlık Denetleyicisi aracını kullanarak sertifika oluşturma | Microsoft Docs
description: Sertifika isteklerinin nasıl oluşturulduğunu ve Azure Stack hub hazırlık Denetleyicisi Aracı kullanılarak Azure Stack Edge Pro GPU cihazınızdan sertifika alma ve yüklemeyi açıklar.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 8316dd0abfa437d4bf88e8268dfe034344c6614c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389341"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack hub hazırlık Denetleyicisi aracını kullanarak Azure Stack Edge Pro için sertifikalar oluşturma 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack hub hazırlık Denetleyicisi Aracı kullanılarak Azure Stack Edge Pro için nasıl sertifika oluşturacağınız açıklanır. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack hub hazırlık Denetleyicisi aracını kullanma

Bir Azure Stack Edge Pro cihaz dağıtımı için sertifika Imzalama Istekleri (CSR) oluşturmak üzere Azure Stack hub hazırlık Denetleyicisi aracını kullanın. Bu istekleri, Azure Stack Edge Pro cihazı için bir sipariş ettikten sonra cihazın gelmesini bekleyip sonra oluşturabilirsiniz.

> [!NOTE]
> Bu aracı yalnızca, üretim cihazları için değil, test veya geliştirme amacıyla kullanın. 

Aşağıdaki sertifikaları istemek için Azure Stack hub hazırlık Denetleyicisi aracı 'nı (AzsReadinessChecker) kullanabilirsiniz:

- Azure Resource Manager sertifikası
- Yerel UI sertifikası
- Düğüm sertifikası
- Blob sertifikası
- VPN sertifikası


## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihaz dağıtımı için CSR 'Ler oluşturmak üzere şunları yaptığınızdan emin olun: 

- Windows 10 veya Windows Server 2016 veya sonraki bir sürümünü çalıştıran bir istemcdiniz. 
- Microsoft Azure Stack hub hazırlık Denetleyicisi aracını bu sistemdeki [PowerShell Galerisi](https://aka.ms/AzsReadinessChecker) indirdiniz.
- Sertifikalar için aşağıdaki bilgilere sahipsiniz:
  - Cihaz adı
  - Düğüm seri numarası
  - Dış tam etki alanı adı (FQDN)

## <a name="generate-certificate-signing-requests"></a>Sertifika imzalama istekleri oluşturma

Azure Stack Edge Pro cihaz sertifikalarını hazırlamak için aşağıdaki adımları kullanın:

1. PowerShell 'i yönetici olarak çalıştırın (5,1 veya üzeri).
2. Azure Stack hub hazırlık Denetleyicisi aracını yükler. PowerShell isteminde şunu yazın: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Yüklü sürümü almak için şunu yazın:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Henüz bir tane yoksa, tüm sertifikalar için bir dizin oluşturun. Şunu yazın: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Bir sertifika isteği oluşturmak için aşağıdaki bilgileri sağlayın. VPN sertifikası oluşturuyorsanız, bu girişlerin bazıları uygulanmaz.
    
    |Giriş |Açıklama  |
    |---------|---------|
    |`OutputRequestPath`|Sertifika isteklerinin oluşturulmasını istediğiniz yerel istemcinizdeki dosya yolu.        |
    |`DeviceName`|Cihazınızın yerel Web Kullanıcı arabirimindeki **cihazlar** sayfasında Cihazınızın adı. <br> Bu alan, bir VPN sertifikası için gerekli değildir.         |
    |`NodeSerialNumber`|Cihazınızın yerel Web Kullanıcı arabirimindeki **ağ** sayfasında bulunan cihaz düğümünün seri numarası. <br> Bu alan, bir VPN sertifikası için gerekli değildir.       |
    |`ExternalFQDN`|Cihazınızın yerel Web Kullanıcı arabirimindeki **cihazlar** sayfasındaki DNSDomain değeri.         |
    |`RequestType`|İstek türü `MultipleCSR` , çeşitli uç noktalar için farklı sertifikalar veya `SingleCSR` tüm uç noktalar için tek bir sertifika olabilir. <br> Bu alan, bir VPN sertifikası için gerekli değildir.     |

    VPN sertifikası dışındaki tüm sertifikalar için şunu yazın: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    VPN sertifikası oluşturuyorsanız şunu yazın: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Sertifika istek dosyalarını yukarıdaki OutputRequestPath parametresinde belirttiğiniz dizinde bulabilirsiniz. `MultipleCSR`Parametresini kullanırken, aşağıdaki dört dosyayı `.req` uzantısıyla görürsünüz:

    
    |Dosya adları  |Sertifika isteği türü  |
    |---------|---------|
    |İle başlangıç `DeviceName`     |Yerel Web Kullanıcı arabirimi sertifika isteği      |
    |İle başlangıç `NodeSerialNumber`     |Düğüm sertifikası isteği         |
    |Başlangıç `login`     |Azure Resource Manager uç noktası sertifika isteği       |
    |Başlangıç `wildcard`     |BLOB depolama sertifikası isteği. Cihazda oluşturabileceğiniz tüm depolama hesaplarını kapsadığından bir joker karakter içeriyor.          |
    |Başlangıç `AzureStackEdgeVPNCertificate`     |VPN istemci sertifikası isteği.         |

    Ayrıca bir INF klasörü görürsünüz. Bu bir Management. <Edge-aygı> Information dosyası, sertifika ayrıntılarını açıklayan şifresiz metin olarak içerir.  


6. Bu dosyaları sertifika yetkilinize (iç veya ortak) gönderebilirsiniz. Sertifika YETKILISININ, [düğüm sertifikaları](azure-stack-edge-gpu-manage-certificates.md#node-certificates), [uç nokta SERTIFIKALARı](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)ve [Yerel UI sertifikalarına](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)yönelik Azure Stack Edge Pro sertifikası gereksinimlerini karşılayan, oluşturulan isteğinizi kullanarak sertifika üretdiğinizden emin olun.

## <a name="prepare-certificates-for-deployment"></a>Dağıtım için sertifikaları hazırlama

Sertifika yetkilinizden (CA) aldığınız sertifika dosyaları içeri aktarılmalıdır ve Azure Stack Edge Pro cihazının sertifika gereksinimleriyle eşleşen özelliklerle birlikte verilmelidir. Aşağıdaki adımları, sertifika imzalama isteklerini oluşturduğunuz sistemde doldurun.

- Sertifikaları içeri aktarmak için [Azure Stack Edge Pro cihazınıza erişen istemcilerde sertifikaları Içeri aktarma](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)' daki adımları izleyin.

- Sertifikaları dışarı aktarmak için [Azure Stack Edge Pro cihazına erişen istemciden sertifikaları dışarı aktarma](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)' daki adımları izleyin.


## <a name="validate-certificates"></a>Sertifikaları doğrula

İlk olarak, uygun bir klasör yapısı oluşturacak ve sertifikaları ilgili klasörlere yerleştirtireceksiniz. Yalnızca aracı kullanarak sertifikaları doğrularsınız.

1. PowerShell 'i yönetici olarak çalıştırın.

2. Uygun klasör yapısını oluşturmak için, komut isteminde şunu yazın:

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. PFX parolasını güvenli bir dizeye dönüştürün. Şunu yazın:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Ardından, sertifikaları doğrulayın. Şunu yazın:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro cihazınızı dağıtma](azure-stack-edge-gpu-deploy-prep.md)
