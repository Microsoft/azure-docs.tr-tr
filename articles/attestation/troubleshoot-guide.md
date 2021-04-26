---
title: Azure Doğrulama sorun giderme kılavuzu
description: Yaygın olarak gözlemlenen sorunlar hakkında sorun giderme kılavuzu
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d3e34bee3d0f1420b379638389e6fad0a2fed60
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831573"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure kanıtlama sorun giderme kılavuzu

Azure kanıtlama 'nda hata işleme, [Microsoft REST API yönergelerinden](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)sonra uygulanır. Azure kanıtlama API 'Leri tarafından döndürülen hata yanıtı, "Code" ve "Message" adlarıyla birlikte HTTP durum kodu ve ad/değer çiftleri içerir. "Code" değeri insan tarafından okunabilir ve hata türünün bir göstergesidir. "Message" değeri kullanıcıya yardımcı olur ve hata ayrıntıları sağlar.

Sorununuz bu makalede giderilmemişse [Azure destek sayfasında](https://azure.microsoft.com/support/options/)de bir Azure destek isteği gönderebilirsiniz.

Aşağıda, Azure kanıtlama tarafından döndürülen hatalara ilişkin bazı örnekler verilmiştir:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: yetkisiz özel durum

### <a name="http-status-code"></a>HTTP durum kodu
401

**Hata kodu** Erişilmesini

**Senaryo örnekleri**
  - Kullanıcı uygun rollerle atanmadığı için kanıtlama ilkeleri yönetimedi
  - Kullanıcı uygun rollerle atanmadığı için kanıtlama ilkesi imzalayanlar yönetimi yapılamıyor

Okuyucu rolü olan Kullanıcı, PowerShell 'de bir kanıtlama ilkesini düzenlemeye çalışıyor 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Sorun giderme adımları**

İlkeleri yönetmek için, bir Azure AD kullanıcısı "eylemler" için aşağıdaki izinleri gerektirir:
- Microsoft. kanıtlama/attestationProviders/kanıtlama/okuma
- Microsoft. kanıtlama/attestationProviders/kanıtlama/yazma
- Microsoft. kanıtlama/attestationProviders/kanıtlama/silme

  Bu işlemleri gerçekleştirmek için, bir Azure AD kullanıcısının kanıtlama sağlayıcısında "kanıtlama katılımcısı" rolü olmalıdır. Bu izinler, abonelik/kaynak grubundaki "sahip" (joker karakter izinleri), "katkıda bulunan" (joker karakter izinleri) gibi rollerle da devralınabilir.  

İlkeleri okumak için, bir Azure AD kullanıcısı "eylemler" için aşağıdaki izinleri gerektirir:
- Microsoft. kanıtlama/attestationProviders/kanıtlama/okuma

  Bu eylemi gerçekleştirmek için, bir Azure AD kullanıcısının kanıtlama sağlayıcısında "kanıtlama okuyucusu" rolü olmalıdır. Okuma izni Ayrıca, abonelik/kaynak grubundaki "okuyucu" (joker karakter izinleri) gibi rollerle devralınabilir.  

PowerShell 'deki rolleri doğrulamak için aşağıdaki adımları çalıştırın:

a. PowerShell 'i başlatın ve "Connect-AzAccount" cmdlet 'i aracılığıyla Azure 'da oturum açın

b. Kanıtlama sağlayıcısında Azure rolü atamasını doğrulamak için lütfen [buradaki](../role-based-access-control/role-assignments-list-powershell.md) kılavuza başvurun

c. Uygun bir rol ataması bulamıyorsanız [buradaki](../role-based-access-control/role-assignments-powershell.md) yönergeleri izleyin

## <a name="2-http--400-errors"></a>2. HTTP – 400 hata

### <a name="http-status-code"></a>HTTP durum kodu
400

Bir isteğin 400 döndürmesine neden olabilecek farklı nedenler vardır. Aşağıda, Azure kanıtlama API 'Leri tarafından döndürülen hatalara ilişkin bazı örnekler verilmiştir:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. İlke değerlendirme hataları nedeniyle kanıtlama hatası

Kanıtlama ilkesi, yetkilendirme kurallarını ve verme kurallarını içerir. Kuşatma kanıtı yetkilendirme kurallarına göre değerlendirilir. Verme kuralları, kanıtlama belirtecine dahil edilecek talepleri tanımlar. Şifreleme kanıtlarındaki talepler yetkilendirme kurallarıyla uyumlu değilse, onaylamasını sağlar çağrıları ilke değerlendirme hatası döndürür. 

**Hata kodu** PolicyEvaluationError

**Senaryo örnekleri** Şifreleme teklifindeki talepler, kanıtlama ilkesinin yetkilendirme kurallarıyla eşleşmezse

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Sorun giderme adımları** Kullanıcılar, aynı yapılandırmayı yapmadan önce bir SGX kanıtlama ilkesiyle şifreleme kanıtlamasını değerlendirebilirler.

"Draftpolicyforkanıtlama" parametresinde ilke metni sağlayarak onaylamasını sağlar API 'sine bir istek gönderin. AttestSgxEnclave API 'si, onaylamasını sağlar çağrısı sırasında bu ilke belgesini kullanır ve bu işlem, tüketilme öncesinde kanıtlama ilkelerini test etmek için kullanılabilir. Bu alan mevcut olduğunda üretilen kanıtlama belirteci güvenli olmayan bir şekilde yapılır.

[Kanıtlama ilkesi örneklerine](./policy-examples.md) bakın

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Geçersiz giriş nedeniyle kanıtlama hatası

**Hata kodu** Geçersiz parametre

**Senaryo örnekleri** Geçersiz giriş nedeniyle SGX kanıtlama hatası. Aşağıda hata iletileri için bazı örnekler verilmiştir:
- Teklif yardımcı sürümündeki bir hata nedeniyle belirtilen teklif geçersizdi 
- Teklifin oluşturulduğu cihaz Azure temel gereksinimlerini karşılamadığından, belirtilen teklif geçersizdi
- PCK önbellek hizmeti tarafından belirtilen Tcınfo veya QıD geçersiz olduğundan belirtilen teklif geçersizdi

**Sorun giderme adımları**

Microsoft Azure kanıtlama, Intel SDK ve açık şifreleme SDK 'Sı tarafından oluşturulan SGX tekliflerinin kanıtlamasını destekler.

Açık Enclave SDK/Intel SDK kullanarak kanıtlama gerçekleştirmek için [kod örneklerine](/samples/browse/?expanded=azure&terms=attestation) bakın

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. İlke/ilke imzalayan karşıya yüklenirken geçersiz sertifika zinciri hatası oluştu

**Hata kodu** Geçersiz parametre

**Senaryo örnekleri** İmzalı ilkeyi yapılandırın veya geçersiz bir sertifika zinciri ile imzalanmış ilke imzalayıcısı ekleyin/silin (örneğin, kök sertifikanın temel kısıtlamalar uzantısı Subject Type = CA olarak ayarlanmamışsa)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Sorun giderme adımları** Kök sertifika, bir CA (X. 509.440 temel kısıtlamaları) tarafından verilmek üzere işaretlenmelidir, aksi takdirde geçerli bir sertifika olarak kabul edilmez. 

Kök sertifikanın temel kısıtlamalar uzantısının, konu türü = CA 'nın olduğunu belirtecek şekilde ayarlandığından emin olun

Aksi takdirde sertifika zinciri geçersiz olarak kabul edilir.

Bkz. [ilke imzalayan](./policy-signer-examples.md) ve [ilke](./policy-examples.md) örnekleri 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. İlke imzalayan ekleme/silme hatası

**Hata kodu** InvalidOperation

**Senaryo örnekleri**

Kullanıcı "Maa-policyCertificate" talebi olmadan JWS 'yi karşıya yüklediğinde

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Kullanıcı JWS biçiminde bir sertifikayı karşıya yüklememez

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Sorun giderme adımları** Yeni bir ilke imzalayan sertifikası eklemek/silmek için, "x-MS-policyCertificate" adlı bir talep ile RFC7519 JSON Web Token (JWT) kullanın. Talebin değeri, eklenecek sertifikayı içeren bir RFC7517 JSON Web anahtarıdır. JWT, sağlayıcı ile ilişkili geçerli ilke imzalayan sertifikalarının herhangi birinin özel anahtarıyla imzalanmalıdır. Bkz. [ilke imzalayan örnekleri](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Kanıtlama ilkesi yapılandırma hatası

**Hata kodu** Policyparsıngerror

**Senaryo örnekleri** İlke hatalı sözdizimi ile sağlanmış (örneğin, noktalı virgül eksik)/Valid JWT ilkesi

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Hata kodu** InvalidOperation

**Senaryo örnekleri** Geçersiz içerik belirtildi (örneğin, ilke imzalama gerektiğinde karşıya yükleme ilkesi/imzasız ilke)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Sorun giderme adımları** Metin biçimindeki ilkenin UTF-8 ile kodlanmış olduğundan emin olun.

İlke imzalama gerekliyse, kanıtlama ilkesinin yalnızca RFC7519 JSON Web Token (JWT) biçiminde yapılandırılması gerekir. İlke imzalama gerekmiyorsa, ilke metin veya JWT biçiminde yapılandırılabilir.

Bir ilkeyi JWT biçiminde yapılandırmak için, "AttestationPolicy" adlı bir talep ile JWT kullanın. Talebin değeri, ilke metninin Base64URL kodlamalı sürümüdür. Kanıtlama sağlayıcısı ilke imzalayan sertifikalarıyla yapılandırıldıysa, JWT ile ilişkili geçerli ilke imzalayıcı sertifikalarının herhangi birinin özel anahtarıyla birlikte oturum açmanız gerekir. 

Bir ilkeyi metin biçiminde yapılandırmak için, ilke metnini doğrudan belirtin.

PowerShell 'de, ilkeyi JWT biçiminde yapılandırmak için PolicyFormat öğesini JWT olarak belirtin. Varsayılan ilke biçimi metindir.

Bkz. kanıtlama [ilkesi örnekleri](./policy-examples.md) ve [bir kanıtlama ilkesi yazma](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. az. PowerShell 'de kanıtlama yükleme sorunları

PowerShell 'e az veya az. kanıtlama modülleri yüklenemedi

### <a name="error"></a>Hata

Uyarı: ' ' paket kaynağı çözümlenemiyor https://www.powershellgallery.com/api/v2 PackageManagement\Install-Package: belirtilen arama ölçütleri ve modül adı için eşleşme bulunamadı

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

PowerShell Galerisi kullanım dışı Aktarım Katmanı Güvenliği (TLS) 1,0 ve 1,1 sürümlerini içerir. 

TLS 1,2 veya sonraki bir sürüm önerilir. 

PowerShell Galerisi etkileşim kurmaya devam etmek için Install-Module komutlarından önce aşağıdaki komutu çalıştırın

**[Net. ServicePointManager]:: SecurityProtocol = [net. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. PowerShell 'de ilke erişimi/yapılandırma sorunları

Kullanıcı uygun rollerle atandı. Ancak PowerShell aracılığıyla kanıtlama ilkelerini yönetirken karşılıklı yetkilendirme sorunları.

### <a name="error"></a>Hata
Nesne KIMLIĞI &lt; nesne kimliğine sahip istemci, &gt;  ' subcrisettings/ &lt; SubscriptionID &gt; ResourceGroups/secure_enclave_poc/Providers/Microsoft.Authorization/roleassignments/ &lt; rol atamaadı &gt; ' veya kapsam geçersiz olarak Microsoft. Authorization/roleatamalar/Write eylemini gerçekleştirmeye yönelik yetkiye sahip değil. Erişim yakın zamanda verildiyse, lütfen kimlik bilgilerinizi yenileyin

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

Kanıtlama işlemlerini desteklemek için gereken az modül en düşük sürümü aşağıda verilmiştir: 

 **Az 4.5.0** 
 
 **Az. Accounts 1.9.2**
 
 **Az. kanıtlama 0.1.8** 

Tüm az modüllerin yüklü sürümünü doğrulamak için aşağıdaki komutu çalıştırın 

```powershell
Get-InstalledModule 
```

Sürümler en düşük gereksinimle eşleşmez Update-Module komutları çalıştırın

Örneğin-Update-Module adı az. kanıtlama
