---
title: Sessiz install Azure AD Uygulaması Proxy Bağlayıcısı | Microsoft Docs
description: Şirket içi uygulamalarınıza güvenli uzaktan erişim sağlamak için Azure AD Uygulama Ara Sunucusu bağlayıcısının katılımsız yüklemesinin nasıl gerçekleştirileceğini ele alır.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258159"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Azure AD Uygulama Ara Sunucusu Bağlayıcısı için katılımsız yükleme betiği oluşturma

Bu konu, Azure AD Uygulama Ara Sunucusu Bağlayıcınız için katılımsız yükleme ve kayıt sağlayan bir Windows PowerShell betiği oluşturmanıza yardımcı olur.

Bu özellik şunları yapmak istediğinizde yararlıdır:

* Bağlayıcıyı, Kullanıcı arabirimi etkinleştirilmemiş olan veya uzak masaüstü ile erişemeyen Windows sunucularına yükleyebilirsiniz.
* Aynı anda birçok bağlayıcıyı yükleyip kaydettirin.
* Bağlayıcı yüklemesini ve kaydını başka bir yordamın parçası olarak tümleştirin.
* Bağlayıcı bitlerini içeren, ancak kayıtlı olmayan standart bir sunucu görüntüsü oluşturun.

[Uygulama proxy bağlayıcısının](application-proxy-connectors.md) çalışması için, uygulama Yöneticisi ve parolası KULLANıLARAK Azure AD dizininizle birlikte kaydedilmelidir. Normalde bu bilgiler, bağlayıcı yüklemesi sırasında açılan bir iletişim kutusunda girilir, ancak bunun yerine bu süreci otomatikleştirmek için PowerShell kullanabilirsiniz.

Katılımsız yükleme için iki adım vardır. İlk olarak bağlayıcıyı yüklemeniz gerekir. İkincisi, bağlayıcıyı Azure AD 'ye kaydedin.

> [!IMPORTANT]
> Bağlayıcıyı Azure Kamu Bulutu için yüklüyorsanız, [ön](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) koşullar ve [yükleme adımlarını](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)gözden geçirin. Bu, yüklemeyi çalıştırmak için farklı bir URL kümesine ve ek parametreye erişim etkinleştirilmesini gerektirir.

## <a name="install-the-connector"></a>Bağlayıcıyı yükler
Bağlayıcıyı kaydetmeden yüklemek için aşağıdaki adımları kullanın:

1. Bir komut istemi açın.
2. Aşağıdaki komutu çalıştırın, burada/q sessiz yükleme anlamına gelir. Sessiz bir yükleme End-User lisans anlaşmasını kabul etmenizi istemez.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Bağlayıcıyı Azure AD 'ye kaydetme
Bağlayıcıyı kaydetmek için kullanabileceğiniz iki yöntem vardır:

* Windows PowerShell kimlik bilgisi nesnesi kullanarak bağlayıcıyı kaydetme
* Çevrimdışı oluşturulan bir belirteci kullanarak bağlayıcıyı kaydetme

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell kimlik bilgisi nesnesi kullanarak bağlayıcıyı kaydetme
1. `$cred`Dizininiz için Yönetici Kullanıcı adı ve parola içeren bir Windows PowerShell kimlik bilgileri nesnesi oluşturun. Ve yerine aşağıdaki komutu çalıştırın *\<username\>* *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. **C:\Program FILES\MICROSOFT AAD uygulama proxy Bağlayıcısı** ' na gidin ve oluşturduğunuz nesneyi kullanarak aşağıdaki betiği çalıştırın `$cred` :

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Çevrimdışı oluşturulan bir belirteci kullanarak bağlayıcıyı kaydetme
1. Aşağıdaki kod parçacığı veya PowerShell cmdlet 'lerinde bulunan değerleri kullanarak AuthenticationContext sınıfını kullanarak bir çevrimdışı belirteç oluşturun:

   **C# kullanarak:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **PowerShell 'i kullanma:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Belirteci aldıktan sonra belirteci kullanarak bir SecureString oluşturun:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Aşağıdaki Windows PowerShell komutunu çalıştırarak \<tenant GUID\> DIZIN Kimliğinizle değiştirin:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Sonraki adımlar
* [Kendi etki alanı adınızı kullanarak uygulama yayımlama](application-proxy-configure-custom-domain.md)
* [Çoklu oturum açmayı etkinleştirme](application-proxy-configure-single-sign-on-with-kcd.md)
* [Uygulama Ara Sunucusu ile ilgili sorunları giderme](application-proxy-troubleshoot.md)