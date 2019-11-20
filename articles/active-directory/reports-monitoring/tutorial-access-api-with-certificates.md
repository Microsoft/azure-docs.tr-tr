---
title: Sertifikalarla AD Raporlama API 'SI için öğretici | Microsoft Docs
description: Bu öğreticide, Kullanıcı müdahalesi olmadan dizinlerden veri almak üzere sertifika kimlik bilgileriyle Azure AD Raporlama API 'sinin nasıl kullanılacağı açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014261"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Öğretici: sertifikalarla Azure Active Directory Raporlama API 'sini kullanarak veri edinme

[Azure Active Directory (Azure AD) raporlama API'leri](concept-reporting-api.md), bir dizi REST tabanlı API aracılığıyla verilere programlı erişim sağlar. Çeşitli programlama dilleri ve araçlarından bu API'leri çağırabilirsiniz. Azure AD Raporlama API 'sine Kullanıcı müdahalesi olmadan erişmek istiyorsanız, erişimi sertifikaları kullanacak şekilde yapılandırmanız gerekir.

Bu öğreticide, raporlama için MS Graph API erişmek üzere bir test sertifikası kullanmayı öğreneceksiniz. Test sertifikalarının bir üretim ortamında kullanılması önerilmez. 

## <a name="prerequisites"></a>Önkoşullar

1. Oturum açma verilerine erişmek için Premium (P1/P2) lisansına sahip bir Azure Active Directory kiracınız olduğundan emin olun. Azure Active Directory sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md) başlama. Yükseltmeden önce herhangi bir etkinlik veriniz yoksa, Premium bir lisansa yükselttikten sonra verilerin raporlarda gösterilmesi birkaç gün sürecek şekilde değişir. 

2. Kiracı için **genel yönetici**, **Güvenlik Yöneticisi**, **güvenlik okuyucusu** veya **rapor okuyucu** rolünde bir kullanıcı hesabı oluşturun veya bu hesaba geçiş yapın. 

3. [Azure Active Directory Raporlama API 'sine erişmek için önkoşulları](howto-configure-prerequisites-for-reporting-api.md)doldurun. 

4. [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)'yi indirin ve yükleyin.

5. [Mscloudidutils](https://www.powershellgallery.com/packages/MSCloudIdUtils/)' i yükler. Bu modül aşağıdaki çeşitli yardımcı program cmdlet'lerini sağlar:
    - Kimlik doğrulaması için gereken ADAL kitaplıkları
    - ADAL kullanarak kullanıcı, uygulama anahtarları ve sertifikalardan erişim belirteçleri
    - Disk belleğine alınmış Graph API işleme sonuçları

6. Modülü ilk kez kullanıyorsanız **Install-MSCloudIdUtilsModule**komutunu çalıştırın, aksi takdirde **Import-Module** PowerShell komutunu kullanarak içeri aktarın. Oturumunuz Şu ekrana benzemelidir: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Bir test sertifikası oluşturmak için **New-SelfSignedCertificate** PowerShell komutunu kullanın.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Sertifika dosyasına dışarı aktarmak için **Export-Certificate** komutunu kullanın.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Sertifikalarla Azure Active Directory raporlama API’sini kullanarak veri alma

1. [Azure Portal](https://portal.azure.com)gidin, **Azure Active Directory**' i seçin ve **uygulama kayıtları** ' yı seçin ve listeden uygulamanızı seçin. 

2. **Ayarlar** > **anahtarlar** ' ı seçin ve **ortak anahtar Yükle ' yi**seçin.

3. Önceki adımdan sertifika dosyasını seçin ve **Kaydet**' i seçin. 

4. Uygulama KIMLIĞINI ve uygulamanıza yeni kaydettiğiniz sertifikanın parmak izini göz önünde bulabilirsiniz. Parmak izini bulmak için portaldaki uygulama sayfanızda **Ayarlar** ' a gidin ve **anahtarlar**' a tıklayın. Parmak izi **ortak anahtarlar** listesinin altında olacaktır.

5. Uygulama bildirimini satır içi bildirim düzenleyicisinde açın ve aşağıdaki şemayı kullanarak *Keycredentials* özelliğini yeni sertifika bilgilerinizle değiştirin. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Bildirimi kaydedin. 
  
7. Şimdi bu sertifikayı kullanarak MS Graph API için bir erişim belirteci alabilirsiniz. MSCloudIdUtils PowerShell modülünden **Get-Mscloudıdmsgraphaccesstokenfromcert** cmdlet 'Ini kullanarak uygulama kimliğini ve önceki adımdan edindiğiniz parmak izini geçirerek kullanın. 

   ![Azure portalında](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Graph API sorgulamak için PowerShell betiğinizdeki erişim belirtecini kullanın. Signins ve directoryAudits uç noktasını numaralandırmak için MSCloudIDUtils öğesinden **Invoke-Mscloudıdmsgraphquery** cmdlet 'ini kullanın. Bu cmdlet çok sayfalı sonuçları işler ve bu sonuçları PowerShell işlem hattına gönderir.

9. Denetim günlüklerini almak için directoryAudits uç noktasını sorgulayın. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Oturum açma günlüklerini almak için signins uç noktasını sorgulayın.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Artık bu verileri bir CSV 'ye vermeyi ve bir SıEM sistemine kaydetmeyi seçebilirsiniz. Ayrıca, betiğinizi zamanlanmış bir göreve kaydırarak, uygulama anahtarlarını kaynak kodda depolamak zorunda kalmadan kiracınızdan Azure AD verilerini düzenli olarak alabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* [Raporlama API'leriyle ilgili ilk izlenim elde edin](concept-reporting-api.md)
* [API başvurusunu denetle](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Oturum açma Etkinliği raporu API başvurusu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
