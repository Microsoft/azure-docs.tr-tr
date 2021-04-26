---
title: Azure AD Connect bulut eşitleme hata kodları ve açıklamaları
description: bulut eşitleme hata kodları için başvuru makalesi
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0aeb2e9649a4bbbb2520eac683836ebbf7dfd0a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075058"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect bulut eşitleme hata kodları ve açıklamaları
Aşağıda hata kodlarının listesi ve açıklamaları verilmiştir


## <a name="error-codes"></a>Hata kodları

|Hata kodu|Ayrıntılar|Senaryo|Çözüm|
|-----|-----|-----|-----|
|Aş|Hata Iletisi: şirket içi aracıda iletişim kurulurken ve yapılandırmanızı eşitlerken bir istek zaman aşımı hatası algıladık. Bulut eşitleme aracıızla ilgili ek sorunlar için lütfen sorun giderme kılavuzumuzu inceleyin.|İstek zaman aşımına uğradı. Geçerli zaman aşımı değeri 10 dakikadır.|[Sorun giderme kılavuzumuzu](how-to-troubleshoot.md) inceleyin|
|Hybridsynchronizationactivedirectoryınternalservererror|Hata Iletisi: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse lütfen desteğe başvurun ve şu iş tanımlayıcısını sağlayın: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Ek ayrıntılar: HTTP isteğinin Işlenmesi bir özel durumla sonuçlandı. |SCıM isteğinde alınan parametreler bir arama isteğine işlenemedi.|Ayrıntılar için lütfen bu özel durumun ' Response ' özelliği tarafından döndürülen HTTP yanıtı ' nı inceleyin.|
|HybridIdentityServiceNoAgentsAssigned|Hata Iletisi: eşitlemeye çalıştığınız etki alanı için etkin bir aracı bulamıyoruz. Aracıların kaldırılıp kaldırılmadığını kontrol edin. Bu durumda, aracıyı yeniden tekrar yüklemelisiniz.|Çalışan bir aracı yok. Büyük olasılıkla aracılar kaldırılmıştır. Yeni bir aracı kaydedin.|"Bu durumda, portalda etki alanına atanan bir aracı görmezsiniz.|
|HybridIdentityServiceNoActiveAgents|Hata Iletisi: eşitlemeye çalıştığınız etki alanı için etkin bir aracı bulamıyoruz. Aracının yüklü olduğu sunucuya gidip hizmetin çalışıp çalışmadığını denetleyin ve hizmetler 'in altında "Microsoft Azure AD bulut eşitleme Aracısı" olup olmadığını kontrol edin.|"Aracılar ServiceBus uç noktasını dinlemiyor. [Aracı, Service Bus bağlantılarına izin verilmeyen bir güvenlik duvarının arkasında](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|Hybrididentityserviceınvalidresource|Hata Iletisi: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse lütfen desteğe başvurun ve şu iş tanımlayıcısını sağlayın: AD2AADProvisioning. 3a2a0d8418f34f54a03dad5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Ek ayrıntılar: bulut eşitleme kurulumunuzda bir sorun var gibi görünüyor. Lütfen bulut eşitleme aracınızı şirket içi AD etki alanında yeniden kaydedin ve Azure portalından yapılandırmayı yeniden başlatın.|Kaynak adı, hangi aracının iletişim kuracağını bilmesi için ayarlanmalıdır.|Lütfen bulut eşitleme aracınızı şirket içi AD etki alanında yeniden kaydedin ve Azure portalından yapılandırmayı yeniden başlatın.|
|HybridIdentityServiceAgentSignalingError|Hata Iletisi: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse lütfen desteğe başvurun ve şu iş tanımlayıcısını sağlayın: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9. Ek ayrıntılar: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden).|Service Bus aracıya ileti gönderemiyor. Service Bus 'da bir kesinti olabilir veya aracı yanıt vermiyor olabilir.|Bu sorun devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden).|
|AzureDirectoryServiceServerBusy|Hata Iletisi: bir hata oluştu. Hata kodu: 81. Hata açıklaması: Azure Active Directory şu anda meşgul. Bu işlem otomatik olarak yeniden denenecek. Bu sorun 24 saatten uzun bir sürerse devam ederse teknik desteğe başvurun. İzleme KIMLIĞI: 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f sunucu adı:|Azure Active Directory şu anda meşgul.|Bu sorun 24 saatten uzun bir sürerse devam ederse teknik desteğe başvurun.|
|AzureActiveDirectoryInvalidCredential|Hata Iletisi: bulut eşitlemesini Azure AD Connect çalıştırmak için kullanılan hizmet hesabıyla ilgili bir sorun bulduk. [Buradaki](./how-to-troubleshoot.md)yönergeleri izleyerek bulut hizmeti hesabını onarabilirsiniz. Hata devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden). Ek hata ayrıntıları: Credentialsgeçersiz AADSTS50034: Kullanıcı hesabı {EmailHidden}, skydrive365.onmicrosoft.com dizininde yok. Bu uygulamada oturum açmak için hesabın dizine eklenmesi gerekir. İzleme KIMLIĞI: 14b63033-3bc9-4BD4-b871-5eb4b3500200 bağıntı KIMLIĞI: 57d93ed1-be4d-483c-997c-a3b6f03deb00 zaman damgası: 2021-01-12 21:08:29Z |Bu hata, kiracınızda ADToAADSyncServiceAccount eşitleme hizmeti hesabı olmadığında oluşur. Hesap yanlışlıkla silinmesinin nedeni olabilir.|Hizmet hesabını [onarmak Için Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) komutunu kullanın.|
|AzureActiveDirectoryExpiredCredentials|Hata Iletisi: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden). Ek hata ayrıntıları: CredentialsExpired AADSTS50055: parolanın geçerliliği dolmuştur. İzleme KIMLIĞI: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 bağıntı KIMLIĞI: 1c69b196-1C3A-4381-9187-c84747807155 zaman damgası: 2021-01-12 20:59:31Z | Yanıt durum kodu başarıyı göstermiyor: 401 (yetkisiz).<br> AAD Eşitleme hizmet hesabı kimlik bilgilerinin zaman aşımına uğradı.|' Deki yönergeleri izleyerek bulut hizmeti hesabını onarabilirsiniz https://go.microsoft.com/fwlink/?linkid=2150988 . Hata devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden).  Ek hata ayrıntıları: yönetim Azure Active Directory Kiracı kimlik bilgileriniz, süresi dolduktan sonra gelen bir OAuth belirteci için değiştirildi. "|
|AzureActiveDirectoryAuthenticationFailed|Hata Iletisi: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse lütfen desteğe başvurun ve şu iş tanımlayıcısını sağlayın: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405D-a3b9-de87761dc3ac. Ek ayrıntılar: Bu isteği bu noktada işleyemedik. Bu sorun devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden). Ek hata ayrıntıları: UnexpectedError.|Bilinmeyen hata.|Bu sorun devam ederse, lütfen Iş KIMLIĞI ile desteğe başvurun (yapılandırmanızın durum bölmesinden).|

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
