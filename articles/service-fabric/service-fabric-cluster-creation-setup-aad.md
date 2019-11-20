---
title: Service Fabric istemci kimlik doğrulaması için Azure Active Directory ayarlama | Microsoft Docs
description: Service Fabric kümeler için istemcilerin kimliğini doğrulamak üzere Azure Active Directory (Azure AD) ayarlamayı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 77814d04daca0ebb649ffa2e8ff46becddec4f0f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901516"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>İstemci kimlik doğrulaması için Azure Active Directory ayarlama

Azure üzerinde çalışan kümeler için, yönetim uç noktalarına erişimi güvenli hale getirmek için Azure Active Directory (Azure AD) önerilir. Bu makalede, bir Service Fabric kümesi için istemcilerin kimliğini doğrulamak üzere Azure AD 'nin nasıl ayarlanacağı açıklanır.

Bu makalede, "uygulama" terimi, Service Fabric uygulamalar değil [Azure Active Directory uygulamalara](../active-directory/develop/developer-glossary.md#client-application)başvurmak için kullanılacaktır; ayrım gerektiğinde yapılır. Azure AD, kuruluşların (kiracılar olarak bilinir) uygulamalara Kullanıcı erişimini yönetmesine olanak sağlar.

Service Fabric kümesi, Web tabanlı [Service Fabric Explorer][service-fabric-visualizing-your-cluster] ve [Visual Studio][service-fabric-manage-application-in-visual-studio]da dahil olmak üzere yönetim işlevlerine birkaç giriş noktası sunar. Sonuç olarak, kümeye erişimi denetlemek için iki Azure AD uygulaması oluşturacaksınız: bir Web uygulaması ve bir yerel uygulama. Uygulamalar oluşturulduktan sonra kullanıcıları salt okuma ve yönetici rollerine atayacaksınız.

> [!NOTE]
> Linux 'ta, kümeyi oluşturmadan önce aşağıdaki adımları gerçekleştirmeniz gerekir. Windows 'da, [var olan bir küme Için Azure AD kimlik doğrulamasını yapılandırma](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md)seçeneğiniz de vardır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, zaten bir kiracı oluşturmuş olduğunuz varsayılmaktadır. Bunu yapmadıysanız, [Azure Active Directory kiracının nasıl alınacağını][active-directory-howto-tenant]okuyarak başlayın.

Azure AD 'yi Service Fabric bir kümeyle yapılandırma ile ilgili bazı adımları basitleştirmek için bir Windows PowerShell komut dosyası kümesi oluşturduk.

1. Depoyu bilgisayarınıza [kopyalayın](https://github.com/Azure-Samples/service-fabric-aad-helpers) .
2. Yüklü betikler için [Tüm önkoşullara sahip olduğunuzdan emin olun](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) .

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD uygulamaları oluşturma ve rollere kullanıcı atama

Kümeye erişimi denetlemek için iki Azure AD uygulaması oluşturmak üzere betikleri kullanacağız: bir Web uygulaması ve bir yerel uygulama. Kümenizi temsil etmek üzere uygulamalar oluşturduktan sonra, [Service Fabric tarafından desteklenen roller](service-fabric-cluster-security-roles.md)için Kullanıcı oluşturacaksınız: salt okunurdur ve yönetici.

`SetupApplications.ps1`çalıştırın ve kiracı KIMLIĞI, küme adı ve Web uygulaması yanıt URL 'sini parametre olarak sağlayın.  Ayrıca, kullanıcılar için Kullanıcı adları ve parolalar da belirtin. Örnek:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Ulusal bulutlar için (örneğin, Azure Kamu, Azure Çin, Azure Almanya), `-Location` parametresini de belirtmeniz gerekir.

`Get-AzureSubscription`PowerShell komutunu yürüterek *Tenantıd* 'nizi bulabilirsiniz. Bu komutun yürütülmesi, her abonelik için Tenantıd 'yi görüntüler.

*Clustername* , komut dosyası tarafından oluşturulan Azure AD uygulamalarının ön ekini almak için kullanılır. Gerçek küme adıyla tam olarak eşleşmesi gerekmez. Yalnızca Azure AD yapıtları ile kullanıldıkları Service Fabric kümeyle eşlemeyi kolaylaştırmak için tasarlanmıştır.

*Webapplicationreplyurl 'si* , oturum açmayı tamamladıktan sonra Azure AD 'nin kullanıcılarınıza döndürdüğü varsayılan uç noktadır. Bu uç noktayı kümeniz için Service Fabric Explorer uç noktası olarak ayarlayın. Mevcut bir kümeyi temsil etmek için Azure AD uygulamaları oluşturuyorsanız, bu URL 'nin mevcut kümenizin bitiş noktasıyla eşleştiğinden emin olun. Yeni bir küme için uygulama oluşturuyorsanız, kümenizin sahip olacağı uç noktayı planlayın ve var olan bir kümenin uç noktasını kullanmadığından emin olun. Service Fabric Explorer uç noktası varsayılan olarak:

https://&lt;cluster_domain&gt;: 19080/Explorer

Azure AD kiracısı için yönetici ayrıcalıklarına sahip bir hesapta oturum açmanız istenir. Oturum açtıktan sonra betik, Service Fabric kümenizi temsil etmek üzere Web ve yerel uygulamalar oluşturur. [Azure Portal][azure-portal]kiracı uygulamalarına baktığınızda, iki yeni giriş görmeniz gerekir:

   * *ClusterName*\_kümesi
   * *ClusterName*\_istemcisi

Betik, [AAD etkin kümenizi oluştururken](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)Azure Resource Manager şablonu IÇIN gereken JSON 'ı yazdırır, bu nedenle PowerShell penceresini açık tutmak iyi bir fikirdir.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Azure Active Directory kurarken yardım sorunlarını giderme
Azure AD 'yi ayarlama ve bunu kullanma zor olabilir, bu nedenle sorun ayıklamak için yapabilecekleriniz konusunda bazı işaretçiler vardır.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer bir sertifika seçmenizi ister
#### <a name="problem"></a>Sorun
Service Fabric Explorer ' de Azure AD 'de başarıyla oturum açtıktan sonra, tarayıcı giriş sayfasına geri döner, ancak bir ileti sizden bir sertifika seçmenizi ister.

![SFX sertifikası iletişim kutusu][sfx-select-certificate-dialog]

#### <a name="reason"></a>Neden
Kullanıcıya Azure AD küme uygulamasında bir rol atanmaz. Bu nedenle, Service Fabric kümesinde Azure AD kimlik doğrulaması başarısız olur. Service Fabric Explorer sertifika kimlik doğrulamasına geri döner.

#### <a name="solution"></a>Çözüm
Azure AD ayarlama ve Kullanıcı rolleri atama yönergelerini izleyin. Ayrıca, `SetupApplications.ps1` olduğu gibi "uygulamaya erişmek için Kullanıcı Ataması gereklidir" seçeneğini etkinleştirmenizi öneririz.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>PowerShell ile bağlantı, şu hata ile başarısız oluyor: "belirtilen kimlik bilgileri geçersiz"
#### <a name="problem"></a>Sorun
PowerShell kullanarak "AzureActiveDirectory" güvenlik modunu kullanarak kümeye bağlanmak için, Azure AD 'de başarıyla oturum açtıktan sonra bağlantı hata vererek başarısız olur: "belirtilen kimlik bilgileri geçersiz."

#### <a name="solution"></a>Çözüm
Bu çözüm, önceki bir ile aynıdır.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer, oturum açtığınızda bir hata döndürür: "AADSTS50011"
#### <a name="problem"></a>Sorun
Service Fabric Explorer ' de Azure AD 'de oturum açmaya çalıştığınızda, sayfa bir hata döndürür: "AADSTS50011: yanıt adresi &lt;URL 'si&gt; uygulama için yapılandırılan yanıt adresleriyle eşleşmez: &lt;GUID&gt;."

![SFX yanıt adresi eşleşmiyor][sfx-reply-address-not-match]

#### <a name="reason"></a>Neden
Service Fabric Explorer temsil eden küme (Web) uygulaması Azure AD 'de kimlik doğrulaması yapmayı dener ve isteğin bir parçası olarak yeniden yönlendirme dönüş URL 'si sağlar. Ancak URL, Azure AD uygulama **yanıt URL 'si** listesinde listelenmez.

#### <a name="solution"></a>Çözüm
AAD sayfasında "Uygulama kayıtları" seçeneğini belirleyin, küme uygulamanızı seçin ve ardından **yanıt URL 'leri** düğmesini seçin. "Yanıt URL 'Leri" sayfasında, Service Fabric Explorer URL 'sini listeye ekleyin veya listedeki öğelerden birini değiştirin. İşiniz bittiğinde, değişiklerinizi kaydedin.

![Web uygulaması yanıt URL 'si][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell aracılığıyla Azure AD kimlik doğrulaması kullanarak kümeyi bağlama
Service Fabric kümesine bağlanmak için aşağıdaki PowerShell komut örneğini kullanın:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Daha fazla bilgi için bkz. [Connect-ServiceFabricCluster cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)'i.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Aynı Azure AD kiracısını birden çok kümede yeniden kullanabilir miyim?
Evet. Ancak Service Fabric Explorer URL 'sini küme (Web) uygulamanıza eklemeyi unutmayın. Aksi takdirde Service Fabric Explorer çalışmaz.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD etkinken neden hala bir sunucu sertifikasına ihtiyacım var?
FabricClient ve FabricGateway, karşılıklı kimlik doğrulaması gerçekleştirir. Azure AD kimlik doğrulaması sırasında, Azure AD tümleştirmesi sunucuya bir istemci kimliği sağlar ve sunucu sertifikası sunucunun kimliğini doğrulamak için istemci tarafından kullanılır. Service Fabric sertifikaları hakkında daha fazla bilgi için bkz. [X. 509.440 sertifikaları ve Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Sonraki adımlar
Azure Active Directory uygulamalar ayarlayıp, kullanıcılar için rolleri ayarlamadıktan sonra [bir kümeyi yapılandırın ve dağıtın](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
