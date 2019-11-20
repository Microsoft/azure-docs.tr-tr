---
title: Kaynak sağlayıcısı işlemlerini Azure Resource Manager | Microsoft Docs
description: Microsoft Azure Kaynak Yöneticisi kaynak sağlayıcıları için kullanılabilir işlemleri listeler
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ca9b0289fbc8dd01427de38e0e7b30eec266bb12
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818600"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager kaynak sağlayıcısı işlemleri

Bu makalede, her bir Azure Resource Manager kaynak sağlayıcısı için kullanılabilen işlemler listelenir. Bu işlemler, Azure 'daki kaynaklara ayrıntılı [rol tabanlı erişim denetimi (RBAC)](overview.md) sağlamak için [özel rollerde](custom-roles.md) kullanılabilir. İşlem dizeleri şu biçimdedir: `{Company}.{ProviderName}/{resourceType}/{action}`

Kaynak sağlayıcısı işlemleri her zaman gelişmektedir. En son işlemleri almak için [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) veya [az Provider Operation List](/cli/azure/provider/operation#az-provider-operation-list)komutunu kullanın.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. AAD/domainServices/Delete | Etki alanı hizmetini Sil |
> | Eylem | Microsoft. AAD/domainServices/oucontainer/Delete | OU kapsayıcısını Sil |
> | Eylem | Microsoft. AAD/domainServices/oucontainer/Read | OU kapsayıcılarını oku |
> | Eylem | Microsoft. AAD/domainServices/oucontainer/Write | OU kapsayıcısı yaz |
> | Eylem | Microsoft. AAD/domainServices/Read | Etki alanı hizmetlerini oku |
> | Eylem | Microsoft. AAD/domainServices/replicaSets/Delete | Küme sitesini sil |
> | Eylem | Microsoft. AAD/domainServices/replicaSets/Read | Küme sitesini oku |
> | Eylem | Microsoft. AAD/domainServices/replicaSets/Write | Küme sitesi yaz |
> | Eylem | Microsoft. AAD/domainServices/Write | Etki alanı hizmeti yaz |
> | Eylem | Microsoft. AAD/konumlar/operationresults/Read |  |
> | Eylem | Microsoft. AAD/Işlemler/okuma |  |
> | Eylem | Microsoft. AAD/Register/ACTION | Etki alanı hizmetini Kaydet |
> | Eylem | Microsoft. AAD/Unregister/eylem | Etki alanı hizmetinin kaydını sil |

## <a name="microsoftaadiam"></a>Microsoft. aadihar

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. aadihar/diagnosticsettings/Delete | Bir tanılama ayarını silme |
> | Eylem | Microsoft. aadihar/diagnosticsettings/Read | Tanılama ayarını okuma |
> | Eylem | Microsoft. aadihar/diagnosticsettings/Write | Tanılama ayarı yazma |
> | Eylem | Microsoft. aadihar/diagnosticsettingscategories/Read | Tanılama ayarı kategorilerini okuma |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. addons/işlemler/okuma | Desteklenen RP işlemlerini alır. |
> | Eylem | Microsoft. addons/Register/ACTION | Belirtilen aboneliği Microsoft. addons ile kaydedin |
> | Eylem | Microsoft. addons/supportProviders/listsupportplanınfo/Action | Belirtilen abonelik için geçerli destek planı bilgilerini listeler. |
> | Eylem | Microsoft. addons/supportProviders/supportPlanTypes/Delete | Belirtilen kurallı destek planını kaldırır |
> | Eylem | Microsoft. addons/supportProviders/supportPlanTypes/Read | Belirtilen kurallı destek planı durumunu alır. |
> | Eylem | Microsoft. addons/supportProviders/supportPlanTypes/Write | Belirtilen kurallı destek planı türünü ekler. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Action | Kiracı için yeni bir orman oluşturun. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/addomainservicemembers/Read | Belirtilen hizmet adı için tüm sunucuları alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/uyarılar/Read | Ormanın AlertId, uyarı verdi tarihi, uyarı son algılanan, uyarı açıklaması, son güncelleme, uyarı düzeyi, uyarı durumu, uyarı sorun giderme bağlantıları vb. gibi uyarı ayrıntılarını alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Configuration/Read | Ormanın hizmet yapılandırmasını alır. Örnek-Orman adı, Işlev düzeyi, etki alanı adlandırma yöneticisi FSMO rolü, şema yöneticisi FSMO rolü vb. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Delete | Hizmet ve sunucularını sistem durumu verileriyle birlikte siler. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Dimensions/Read | Ormanın etki alanlarını ve site ayrıntılarını alır. Örnek-sistem durumu, etkin uyarılar, çözümlenen uyarılar, etki alanı Işlev düzeyi, orman, altyapı yöneticisi, PDC, RID Yöneticisi vb. gibi özellikler  |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Özellikler/kullanıcıtercihi/okuma | Ormanın Kullanıcı tercihi ayarını alır.<br>Ldapbaşarılı fulbind, ntlmaıd 'ler, kerberosauthentications, addsinsıısesagentprivatebytes, ldaparamaları gibi örnek-MetricCounterName.<br>UI grafikleri vs. ayarları. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/forestsummary/Read | Belirtilen orman için orman adı, bu ormandaki etki alanı sayısı, site ve site ayrıntıları gibi orman özetini alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/metricmetadata/Read | Belirli bir hizmet için desteklenen ölçümlerin listesini alır.<br>Örneğin, extranet hesabı kilitleme Işlemleri, toplam başarısız Istek sayısı, bekleyen belirteç Istekleri (proxy), ADFS hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomainService için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, ADSync hizmeti için Azure AD 'ye Istatistikleri dışarı aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/ölçümler/gruplar/okuma | Hizmet verildiğinde, bu API ölçüm bilgilerini alır.<br>Örneğin, bu API ile ilgili bilgi almak için bu API kullanılabilir: extranet hesabı kilitleme Işlemleri, toplam başarısız Istek, bekleyen belirteç Istekleri (proxy) ve ADFederation hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomain hizmeti için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, Istatistikleri eşitleme hizmeti için Azure AD 'ye aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/premiumcheck/Read | Bu API, Premium kiracı için tüm eklendi ADDomainServices listesini alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Read | Belirtilen hizmet adı için hizmet ayrıntılarını alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/replicationdetails/Read | Belirtilen hizmet adı için tüm sunucular için çoğaltma ayrıntılarını alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/ReplicationStatus/Read | Varsa, etki alanı denetleyicilerinin sayısını ve bunların çoğaltma hatalarını alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/replicationsummary/Read | Tüm etki alanı denetleyicisi listesini, belirtilen ormanın çoğaltma ayrıntılarının birlikte alır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/servicemembers/Action | Hizmete bir sunucu örneği ekleyin. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/servicemembers/Credentials/Read | ADDomainService 'in sunucu kaydı sırasında, yeni sunucuları ekleme kimlik bilgilerini almak için bu API çağırılır. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/servicemembers/Delete | Belirli bir hizmet ve kiracı için bir sunucuyu siler. |
> | Eylem | Microsoft. ADHybridHealthService/addsservices/Write | Kiracı için ADDomainService örneğini oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ADHybridHealthService/yapılandırma/eylem | Kiracı yapılandırmasını güncelleştirir. |
> | Eylem | Microsoft. ADHybridHealthService/yapılandırma/okuma | Kiracı yapılandırmasını okur. |
> | Eylem | Microsoft. ADHybridHealthService/yapılandırma/yazma | Kiracı yapılandırması oluşturur. |
> | Eylem | Microsoft. ADHybridHealthService/logs/Contents/Read | Blob 'da depolanan aracı yükleme ve kayıt günlüklerinin içeriğini alır. |
> | Eylem | Microsoft. ADHybridHealthService/logs/Read | Kiracının aracı yüklemesini ve kayıt günlüklerini alır. |
> | Eylem | Microsoft. ADHybridHealthService/işlemler/okuma | Sistem tarafından desteklenen işlemlerin listesini alır. |
> | Eylem | Microsoft. ADHybridHealthService/Register/Action | Adhibrit Sistem Sağlığı Hizmeti kaynak sağlayıcısını kaydeder ve Adhibrit Sistem Sağlığı Hizmeti kaynağının oluşturulmasına izin vermez. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/availabledağıtımlar/okuma | DevOps tarafından müşteri olaylarını desteklemek için kullanılan kullanılabilir bölgelerin listesini alır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/badpassword/Read | Active Directory Federasyon Hizmeti tüm kullanıcılar için hatalı parola denemelerinin listesini alır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/badpassworduseridıservıce frekansı/okuma | Belirli bir kiracı için her gün için IP başına Kullanıcı başına Hatalı Kullanıcı adı/parola denemesi sıklığı ve yeni sıraya alınmış rapor işinin nihai sonucunu içeren blob SAS URI 'sini alır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/consentedtodevopstenants/Read | DevOps onaylı kiracılar listesini alır. Genellikle müşteri desteği için kullanılır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/ıdevops/Read | Kiracının DevOps consented olup olmadığını gösteren bir değer alır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Seçili dev Ops kiracısı için UserID 'yi (ObjectID) güncelleştirir. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/selecteddeployment/Read | Belirtilen kiracı için seçili dağıtımı alır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/tenantassigneddeployment/Read | Verilen Kiracı kimliği kiracı depolama konumunu alır. |
> | Eylem | Microsoft. ADHybridHealthService/Reports/updateselecteddeployment/Read | Verilerin erişilecek coğrafi konumunu alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/Action | Kiracıdaki bir hizmet örneğini güncelleştirir. |
> | Eylem | Microsoft. ADHybridHealthService/Services/uyarılar/okuma | Bir hizmet için uyarıları okur. |
> | Eylem | Microsoft. ADHybridHealthService/Services/uyarılar/okuma | Bir hizmet için uyarıları okur. |
> | Eylem | Microsoft. ADHybridHealthService/Services/checkservicefeatureavability/Read | Hizmet adı verildiğinde, bir hizmetin bu özelliği kullanmak için gereken her şey olup olmadığını doğrular. |
> | Eylem | Microsoft. ADHybridHealthService/Services/Delete | Kiracıdaki bir hizmet örneğini siler. |
> | Eylem | Microsoft. ADHybridHealthService/Services/exporterrors/Read | Belirli bir eşitleme hizmeti için dışarı aktarma hatalarını alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/exportstatus/Read | Belirli bir hizmet için dışarı aktarma durumunu alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/feedbacktype/görüşler/Read | Belirli bir hizmet ve sunucu için uyarı geri bildirimi alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/metricmetadata/Read | Belirli bir hizmet için desteklenen ölçümlerin listesini alır.<br>Örneğin, extranet hesabı kilitleme Işlemleri, toplam başarısız Istek sayısı, bekleyen belirteç Istekleri (proxy), ADFS hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomainService için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, ADSync hizmeti için Azure AD 'ye Istatistikleri dışarı aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/Services/ölçümler/gruplar/ortalama/okuma | Hizmet verildiğinde, bu API belirli bir hizmet için ölçümlerin ortalamasını alır.<br>Örneğin, bu API ile ilgili bilgi almak için bu API kullanılabilir: extranet hesabı kilitleme Işlemleri, toplam başarısız Istek, bekleyen belirteç Istekleri (proxy) ve ADFederation hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomain hizmeti için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, Istatistikleri eşitleme hizmeti için Azure AD 'ye aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/Services/ölçümler/gruplar/okuma | Hizmet verildiğinde, bu API ölçüm bilgilerini alır.<br>Örneğin, bu API ile ilgili bilgi almak için bu API kullanılabilir: extranet hesabı kilitleme Işlemleri, toplam başarısız Istek, bekleyen belirteç Istekleri (proxy) ve ADFederation hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomain hizmeti için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, Istatistikleri eşitleme hizmeti için Azure AD 'ye aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/Services/ölçümler/gruplar/Sum/Read | Bu API, bir hizmet verildiğinde, belirli bir hizmet için ölçümlerin toplu görünümünü alır.<br>Örneğin, bu API ile ilgili bilgi almak için bu API kullanılabilir: extranet hesabı kilitleme Işlemleri, toplam başarısız Istek, bekleyen belirteç Istekleri (proxy) ve ADFederation hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomain hizmeti için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, Istatistikleri eşitleme hizmeti için Azure AD 'ye aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/Services/monitoringconfiguration/Write | Bir hizmet için izleme yapılandırması ekleme veya güncelleştirme. |
> | Eylem | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Read | Belirli bir hizmet için izleme yapılandırmasını alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Write | Bir hizmet için izleme yapılandırması ekleme veya güncelleştirme. |
> | Eylem | Microsoft. ADHybridHealthService/Services/premiumcheck/Read | Bu API, bir Premium kiracının tüm eklendi hizmetlerinin listesini alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/Read | Kiracıdaki hizmet örneklerini okur. |
> | Eylem | Microsoft. ADHybridHealthService/Services/Reports/Blobursıs/Read | Son 7 güne ait tüm riskli IP rapor URI 'Lerini alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/Reports/details/Read | Son 7 günde hatalı parola hatası olan ilk 50 kullanıcının raporunu alır |
> | Eylem | Microsoft. ADHybridHealthService/Services/Reports/Generatebloburı/Action | Riskli IP raporu oluşturur ve ona işaret eden bir URI döndürür. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/Action | Hizmette bir sunucu örneği oluşturur. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/uyarılar/Read | Bir sunucu için uyarıları okur. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/Credentials/Read | Sunucu kaydı sırasında, yeni sunucuları ekleme kimlik bilgilerini almak için bu API çağırılır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/datayeniliği/okuma | Belirli bir sunucu için bu API, sunucular tarafından karşıya yüklenen veri türlerinin bir listesini ve her yükleme için en son saati alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/Delete | Hizmette bir sunucu örneğini siler. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/exportstatus/Read | Belirli bir eşitleme hizmeti için eşitleme dışa aktarma hata ayrıntılarını alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/ölçümler/gruplar/okuma | Hizmet verildiğinde, bu API ölçüm bilgilerini alır.<br>Örneğin, bu API ile ilgili bilgi almak için bu API kullanılabilir: extranet hesabı kilitleme Işlemleri, toplam başarısız Istek, bekleyen belirteç Istekleri (proxy) ve ADFederation hizmeti için belirteç Istekleri/sn.<br>NTLM kimlik doğrulamaları/sn, LDAP başarılı bağlamalar/sn, LDAP bağlama süresi, LDAP etkin Iş parçacıkları, Kerberos kimlik doğrulamaları/sn, ADDomain hizmeti için ATQ Iş parçacığı toplamı.<br>Profil gecikmesini çalıştırın, TCP bağlantıları oluşturulmuştur, Insights Aracısı özel baytları, Istatistikleri eşitleme hizmeti için Azure AD 'ye aktarın. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/ölçümler/okuma | Bağlayıcılar listesini alır ve belirtilen hizmet ve hizmet üyesinin profil adlarını çalıştırır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/Read | Hizmet içindeki sunucu örneğini okur. |
> | Eylem | Microsoft. ADHybridHealthService/Services/servicemembers/ServiceConfiguration/Read | Belirli bir kiracının hizmet yapılandırmasını alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/tenantwhitelisteleme/okuma | Belirli bir kiracının özellik beyaz listeleme durumunu alır. |
> | Eylem | Microsoft. ADHybridHealthService/Services/Write | Kiracıda bir hizmet örneği oluşturur. |
> | Eylem | Microsoft. ADHybridHealthService/Unregister/Action | Adhibrit Sistem Sağlığı Hizmeti kaynak sağlayıcısı için aboneliğin kaydını siler. |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. danışman/yapılandırma/okuma | Yapılandırma al |
> | Eylem | Microsoft. Advisor/Configurations/Write | Yapılandırma oluşturur/güncelleştirir |
> | Eylem | Microsoft. Advisor/Generatereyorumları/eylem | Öneriler oluşturur |
> | Eylem | Microsoft. Advisor/Generatereyorumları/okuma | Öneri durumunu al |
> | Eylem | Microsoft. Advisor/Metadata/Read | Meta verileri al |
> | Eylem | Microsoft. Advisor/işlemler/okuma | Microsoft Advisor 'ın işlemlerini alır |
> | Eylem | Microsoft. Advisor/öneriler/kullanılabilir/eylem | Yeni öneri Microsoft Advisor 'da kullanılabilir |
> | Eylem | Microsoft. Advisor/öneriler/okuma | Önerileri okur |
> | Eylem | Microsoft. Advisor/öneriler/gizlemeleri/silme | Gizlemeleri siler |
> | Eylem | Microsoft. Advisor/öneriler/suppressions/okuma | Gizlemeleri alır |
> | Eylem | Microsoft. Advisor/öneriler/suppressions/Write | Gizlemeleri oluşturur/güncelleştirir |
> | Eylem | Microsoft. Advisor/Register/ACTION | Microsoft Advisor için aboneliği kaydeder |
> | Eylem | Microsoft. Advisor/suppressions/Delete | Gizlemeleri siler |
> | Eylem | Microsoft. Advisor/suppressions/okuma | Gizlemeleri alır |
> | Eylem | Microsoft. Advisor/suppressions/Write | Gizlemeleri oluşturur/güncelleştirir |
> | Eylem | Microsoft. Advisor/kaydı sil/eylem | Microsoft Advisor aboneliğinin kaydını siler |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. AlertsManagement/actionRules/Delete | Belirli bir abonelikteki eylem kuralını silin. |
> | Eylem | Microsoft. AlertsManagement/actionRules/Read | Giriş filtreleri için tüm eylem kurallarını alın. |
> | Eylem | Microsoft. AlertsManagement/actionRules/Write | Belirli bir abonelikte eylem kuralı oluştur veya güncelleştir |
> | Eylem | Microsoft. AlertsManagement/uyarılar/ChangeState/Action | Uyarının durumunu değiştirin. |
> | Eylem | Microsoft. AlertsManagement/Alerts/Diagnostic/Read | Uyarı için tüm tanılamayı al |
> | Eylem | Microsoft. AlertsManagement/uyarılar/History/Read | Uyarının geçmişini al |
> | Eylem | Microsoft. AlertsManagement/uyarılar/okuma | Giriş filtreleri için tüm uyarıları alın. |
> | Eylem | Microsoft. AlertsManagement/alertsList/Read | Abonelikler genelinde giriş filtreleri için tüm uyarıları al |
> | Eylem | Microsoft. AlertsManagement/alertsMetaData/okuma | Giriş parametresi için uyarılar meta verilerini alın. |
> | Eylem | Microsoft. AlertsManagement/alertsSummary/okuma | Uyarıların özetini al |
> | Eylem | Microsoft. AlertsManagement/alertsSummaryList/Read | Abonelikler arasında uyarıların özetini alın |
> | Eylem | Microsoft. AlertsManagement/Işlemler/okuma | Belirtilen işlemleri okur |
> | Eylem | Microsoft. AlertsManagement/Register/Action | Microsoft Uyarıları yönetimi için aboneliği kaydeder |
> | Eylem | Microsoft. AlertsManagement/smartDetectorAlertRules/Delete | Belirli bir abonelikte akıllı algılayıcı uyarı kuralını Sil |
> | Eylem | Microsoft. AlertsManagement/smartDetectorAlertRules/Read | Giriş filtreleri için tüm akıllı algılayıcı uyarısı kurallarını al |
> | Eylem | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Belirli bir abonelikte akıllı algılayıcı uyarı kuralı oluştur veya güncelleştir |
> | Eylem | Microsoft. AlertsManagement/smartGroups/ChangeState/Action | Akıllı grubun durumunu değiştirme |
> | Eylem | Microsoft. AlertsManagement/smartGroups/History/Read | Akıllı grup geçmişini al |
> | Eylem | Microsoft. AlertsManagement/smartGroups/Read | Giriş filtreleri için tüm akıllı grupları Al |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. AnalysisServices/Locations/Checknameavaılabılıty/Action | Verilen Analiz Sunucusu adının geçerli ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. AnalysisServices/konumlar/operationresults/Read | Belirtilen işlem sonucu bilgilerini alır. |
> | Eylem | Microsoft. AnalysisServices/Locations/operationdurumlarının/Read | Belirtilen işlem durumu bilgilerini alır. |
> | Eylem | Microsoft. AnalysisServices/işlemler/okuma | İşlem bilgilerini alır |
> | Eylem | Microsoft. AnalysisServices/Register/ACTION | Analysis Services kaynak sağlayıcısını kaydeder. |
> | Eylem | Microsoft. AnalysisServices/Servers/Delete | Analiz Sunucusu siler. |
> | Eylem | Microsoft. AnalysisServices/Servers/listGatewayStatus/Action | Sunucuyla ilişkili ağ geçidinin durumunu listeleyin. |
> | Eylem | Microsoft. AnalysisServices/Servers/Read | Belirtilen Analiz Sunucusu bilgilerini alır. |
> | Eylem | Microsoft. AnalysisServices/Servers/özgeçmişi/Action | Analiz Sunucusu devam ettirir. |
> | Eylem | Microsoft. AnalysisServices/Servers/SKU 'ları/okuma | Sunucu için kullanılabilir SKU bilgilerini alın |
> | Eylem | Microsoft. AnalysisServices/Servers/beklet/Action | Analiz Sunucusu askıya alır. |
> | Eylem | Microsoft. AnalysisServices/Servers/Write | Belirtilen Analiz Sunucusu oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. AnalysisServices/SKU 'lar/okuma | SKU 'ların bilgilerini alır |

## <a name="microsoftapimanagement"></a>Microsoft. Apimanane

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Apimanamı/Checknameavaılabılıty/Read | Sağlanan hizmet adının kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. Apimanayönetimi/işlemler/okuma | Microsoft. Apimana, kaynağı için kullanılabilen tüm API işlemlerini okuyun |
> | Eylem | Microsoft. Apimanayönetimi/kaydet/eylem | Microsoft. Apimanayönetimi kaynak sağlayıcısı için aboneliği kaydedin |
> | Eylem | Microsoft. Apimanayönetimi/raporlar/okuma | Zaman dönemleri, coğrafi bölge, geliştiriciler, ürünler, API 'Ler, işlemler, abonelik ve byRequest tarafından toplanan raporları alın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/silme | API Management hizmet örneğinin belirtilen API 'sini siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/tanılama/silme | Belirtilen tanılamayı bir API 'den siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/tanılama/okuma | Bir API 'nin tüm tanılamalarını listeler. ya da kendi tanımlayıcısı tarafından belirtilen bir API için tanı ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/tanılama/yazma | Bir API için yeni bir tanılama oluşturur veya var olan bir API 'yi güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen bir API için tanı ayrıntılarını günceller. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/sorunlar/ekler/Sil | Bir sorundan belirtilen yorumu siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/sorunlar/ekler/okuma | Belirtilen API ile ilişkili sorun için tüm ekleri listeler. ya da kendi tanımlayıcısına göre belirtilen bir API için sorun ekinin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/sorunlar/ekler/yazma | Bir API 'de sorun için yeni bir ek oluşturur veya var olanı güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/sorunlar/açıklamalar/silme | Bir sorundan belirtilen yorumu siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/sorunlar/açıklamalar/okuma | Belirtilen API ile ilişkili sorun için tüm açıklamaları listeler. ya da kendi tanımlayıcısına göre belirtilen bir API için sorun açıklamasının ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/sorunlar/açıklamalar/yazma | Bir API 'de sorun için yeni bir açıklama oluşturur veya var olan bir güncelleştirmeyi güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/sorunlar/silme | Belirtilen sorunu bir API 'den siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/sorunlar/okuma | Belirtilen API ile ilişkili tüm sorunları listeler. ya da kendi tanımlayıcısına göre belirtilen bir API için sorunun ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/sorunlar/yazma | Bir API için yeni bir sorun oluşturur veya var olan bir sorunu güncelleştirir. ya da bir API için mevcut bir sorunu güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/işlemler/silme | API 'de belirtilen işlemi siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/ilkeler/silme | API Işlemindeki ilke yapılandırmasını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/ilkeler/okuma | API Işlem düzeyindeki ilke yapılandırması listesini alın. veya API Işlem düzeyinde ilke yapılandırmasını alın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/ilkeler/yazma | API Işlem düzeyi için ilke yapılandırması oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/ilke/silme | Işlem düzeyinde ilke yapılandırmasını silme |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/ilke/okuma | Işlem düzeyinde ilke yapılandırmasını al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/ilke/yazma | Işlem düzeyinde ilke yapılandırması oluştur |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/işlemler/okuma | Belirtilen API için işlemlerin koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen API Işleminin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/Etiketler/silme | Etiketi Işlemden ayırın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/Etiketler/okuma | Işlemle ilişkili tüm etiketleri listeler. veya Işlemle ilişkili Get etiketi. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/işlemler/Etiketler/yazma | Işleme etiket atayın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/işlemler/yazma | API 'de yeni bir işlem oluşturur veya var olan bir işlemi güncelleştirir. veya işlemin ayrıntılarını, tanımlayıcısı tarafından belirtilen API 'de güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/operationsByTags/okuma | Etiketlerle ilişkili işlemler koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/ilkeler/silme | API 'de ilke yapılandırmasını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/ilkeler/okuma | API düzeyinde ilke yapılandırmasını alın. veya API düzeyinde ilke yapılandırmasını alın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/ilkeler/yazma | API için ilke yapılandırması oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/ilke/silme | API düzeyinde ilke yapılandırmasını silme |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/ilke/okuma | API düzeyinde ilke yapılandırmasını al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/ilke/yazma | API düzeyinde ilke yapılandırması oluştur |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/ürünler/okuma | API 'nin parçası olduğu tüm ürünleri listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/okuma | API Management hizmet örneğinin tüm API 'Lerini listeler. ya da kendi tanımlayıcısı tarafından belirtilen API 'nin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/yayınlar/silme | API 'nin tüm sürümlerini kaldırır veya API 'de belirtilen yayını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/yayınlar/okuma | Bir API 'nin tüm sürümlerini listeler.<br>API 'nin düzeltilmesi geçerli olduğunda bir API yayını oluşturulur.<br>Sürümler, önceki düzeltmelere geri dönmek için de kullanılır.<br>Sonuçlar disk belleğine alınır ve $top ve $skip parametreleriyle kısıtlanabilir.<br>ya da bir API sürümünün ayrıntılarını döndürür. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/yayınlar/yazma | API için yeni bir yayın oluşturur. ya da kendi tanımlayıcısı tarafından belirtilen API sürümünün ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/düzeltme/silme | Bir API 'nin tüm düzeltmelerini kaldırır |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/düzeltmeleri/okuma | Bir API 'nin tüm düzeltmelerini listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/şema/silme | API 'de şema yapılandırmasını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/şemalar/okuma | API düzeyinde şema yapılandırmasını alın. veya API düzeyinde şema yapılandırmasını alın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/şemalar/yazma | API için şema yapılandırması oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/tagDescriptions/Delete | API için etiket açıklamasını silin. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/tagDescriptions/Read | API kapsamındaki tüm etiket açıklamalarını listeler. Swagger-tagDescription benzerine benzer bir model API düzeyinde tanımlandı, ancak API kapsamındaki bir etiketi Işlemlere veya Get etiketi açıklamasına atanabilir |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/tagDescriptions/Write | API 'nin kapsamında etiket açıklaması oluştur/güncelleştir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/etiketleri/silme | Etiketi API 'den ayırın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/Etiketler/okuma | API ile ilişkili tüm etiketleri listeler. veya API ile ilişkili Get etiketi. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/API/Etiketler/yazma | API 'ye etiket atayın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/API/yazma | API Management hizmet örneğinin yeni bir API 'sini oluşturur veya güncelleştirir. veya API Management hizmet örneğinin belirtilen API 'sini güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/apisByTags/okuma | Etiketlerle ilişkili API 'lerin bir koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/apiVersionSets/Delete | Belirli API sürüm kümesini siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/apiVersionSets/Read | Belirtilen hizmet örneğindeki API sürüm kümelerinin bir koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen API sürümü kümesinin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/apiVersionSets/sürümler/okuma | Sürüm varlıklarının listesini al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/apiVersionSets/Write | Bir API sürüm kümesi oluşturur veya güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen API VersionSet 'in ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/Service/applynetworkconfigurationupdates/Action | Güncelleştirilmiş ağ ayarlarını seçmek için sanal ağda çalışan Microsoft. Apimana, kaynaklarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/authorizationServers/Sil | Belirli yetkilendirme sunucusu örneğini siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/authorizationServers/okuma | Bir hizmet örneği içinde tanımlanan yetkilendirme sunucularının bir koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen yetkilendirme sunucusunun ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/authorizationServers/yazma | Yeni yetkilendirme sunucusu oluşturur veya var olan bir yetkilendirme sunucusunu güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen yetkilendirme sunucusunun ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/arka uçlar/silme | Belirtilen arka ucu siler. |
> | Eylem | Microsoft. Apimanamı/hizmeti/backends/Read | Belirtilen hizmet örneğindeki arka uçların koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen arka ucun ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanaveya hizmet/arka uçlar/geri bağlanma/eylem | Belirtilen zaman aşımından sonra, APıM proxy 'sine, arka uca yeni bir bağlantı oluşturmasını bildirir. Zaman aşımı belirtilmemişse 2 dakikalık zaman aşımı kullanılır. |
> | Eylem | Microsoft. Apimanamı/hizmeti/backends/Write | Bir arka uç oluşturur veya güncelleştirir. veya var olan bir arka ucu güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/yedekleme/eylem | API Management hizmetini Kullanıcı tarafından sağlanmış bir depolama hesabında belirtilen kapsayıcıya Yedekle |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/önbellekleri/silme | Belirli bir önbelleği siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/önbellekler/okuma | Belirtilen hizmet örneğindeki tüm dış önbellekler için bir koleksiyon listeler. ya da kendi tanımlayıcısı tarafından belirtilen önbelleğin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/önbellekler/yazma | API Management örneğinde kullanılmak üzere bir dış önbellek oluşturur veya güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen önbelleğin ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/sertifikalar/silme | Belirli bir sertifikayı siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/sertifikaları/okuma | Belirtilen hizmet örneğindeki tüm sertifikaların koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen sertifikanın ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/sertifikaları/yazma | Arka uçta kimlik doğrulama için kullanılan sertifikayı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/contentTypes/ContentItems/Delete | Belirtilen içerik öğesini kaldırır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/contentTypes/ContentItems/Read | İçerik öğelerinin listesini döndürür veya içerik öğesi ayrıntılarını döndürür |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/contentTypes/ContentItems/Write | Yeni içerik öğesi oluşturur veya belirtilen içerik öğesini güncelleştirir |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/contentTypes/Read | İçerik türleri listesini döndürür |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/silme | API Management hizmeti örneğini Sil |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/Tanılama/silme | Belirtilen tanılamayı siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/Tanılama/okuma | API Management hizmet örneğinin tüm tanılamalarını listeler. ya da kendi tanımlayıcısı tarafından belirtilen Tanılamanın ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/tanılama/yazma | Yeni bir tanılama oluşturur veya var olan bir tanılamayı güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen Tanılamanın ayrıntılarını günceller. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ağ geçitleri/eylem | Ağ Geçidi yapılandırmasını alır. veya ağ geçidi sinyalini güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ağ geçitleri/silme | Belirli ağ geçidini siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ağ geçitleri/anahtarlar/eylem | Ağ Geçidi anahtarlarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ağ geçitleri/okuma | Hizmet örneğine kayıtlı ağ geçitlerinin bir koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen ağ geçidinin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ağ geçitleri/regeneratePrimaryKey/Action | Birincil ağ geçidi anahtarını doğrulama ile oluşturulan belirteçleri yeniden oluşturur. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ağ geçitleri/regenerateSecondaryKey/Action | İkincil ağ geçidi anahtarı geçersiz hale gelir, bununla oluşturulan belirteçleri oluşturur. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ağ geçitleri/belirteç/eylem | Ağ geçidinin paylaşılan erişim yetkilendirme belirtecini alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ağ geçitleri/yazma | API Management örneğinde kullanılmak üzere bir ağ geçidi oluşturur veya güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen ağ geçidinin ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/getssotoken/Action | API Management hizmeti eski portalda yönetici olarak oturum açmak için kullanılabilecek SSO belirtecini alır |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/gruplar/Sil | API Management hizmet örneğinin belirli bir grubunu siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/gruplar/okuma | Bir hizmet örneği içinde tanımlı grupların koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen grubun ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/gruplar/kullanıcılar/Sil | Mevcut gruptan mevcut kullanıcıyı kaldırın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/gruplar/kullanıcılar/okuma | Grupla ilişkili Kullanıcı varlıklarının koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/gruplar/kullanıcılar/yazma | Mevcut kullanıcıyı mevcut gruba ekle |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/gruplar/yazma | Bir grup oluşturur veya güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen grubun ayrıntılarını günceller. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/IdentityProviders/Delete | Belirtilen kimlik sağlayıcısı yapılandırmasını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/IdentityProviders/Read | Belirtilen hizmet örneğinde yapılandırılan bir kimlik sağlayıcısı koleksiyonunu listeler. veya belirtilen hizmet örneğinde yapılandırılan kimlik sağlayıcısının yapılandırma ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/IdentityProviders/Write | IdentityProvider yapılandırmasını oluşturur veya güncelleştirir. veya var olan bir IdentityProvider yapılandırmasını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/sorunlar/okuma | Belirtilen hizmet örneğindeki sorunların koleksiyonunu listeler. veya API Management sorun ayrıntılarını alır |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/konumlar/networkstatus/Read | Hizmetin konuma bağlı olduğu kaynakların ağ erişim durumunu alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/Günlükçüler/Sil | Belirtilen günlükçü 'yi siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/Günlükçüler/okuma | Belirtilen hizmet örneğindeki günlükçülerin bir koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen günlükçü ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/Günlükçüler/yazma | Bir günlükçü oluşturur veya güncelleştirir. veya var olan bir günlükçü 'yi güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/managedağıtımlar/eylem | SKU/birimleri değiştirme, API Management hizmetinin bölgesel dağıtımlarını ekleme/kaldırma |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/networkstatus/Read | Hizmetin bağımlı olduğu kaynakların ağ erişim durumunu alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/bildirimleri/eylemi | Belirtilen kullanıcıya bildirim gönderir |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/bildirimleri/okuma | Bir hizmet örneği içinde tanımlanan özelliklerin bir koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen bildirimin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/bildirimleri/recipientEmails/Delete | Bildirim listesinden e-postayı kaldırır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/bildirimleri/recipientEmails/Read | Bir bildirime abone olan bildirim alıcısı e-postalarının listesini alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/bildirimleri/recipientEmails/Write | Bildirim için alıcı listesine e-posta adresini ekler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/bildirimler/alıcının kullanıcıları/silme | API Management kullanıcıyı bildirim listesinden kaldırır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/bildirimler/alıcının kullanıcıları/okuma | Bildirime abone olan bildirim alıcısı kullanıcısının listesini alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/bildirimler/alıcının kullanıcıları/yazma | Bildirim için API Management kullanıcıyı alıcı listesine ekler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/bildirimleri/yazma | API Management yayımcı bildirimi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Apimanayönetimi/Service/Openıdconnectproviders/Delete | API Management hizmet örneğinin belirli OpenID Connect sağlayıcısını siler. |
> | Eylem | Microsoft. Apimanayönetimi/Service/Openıdconnectproviders/Read | Tüm OpenID Connect sağlayıcılarının listeleri. veya belirli bir OpenID Connect sağlayıcısı alır. |
> | Eylem | Microsoft. Apimanayönetimi/Service/Openıdconnectproviders/Write | OpenID Connect sağlayıcısı oluşturur veya güncelleştirir. veya belirli OpenID Connect sağlayıcısı 'nı güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/operationresults/Read | Uzun süre çalışan işlemin geçerli durumunu alır |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ilkeleri/silme | API Management hizmetinin genel ilke yapılandırmasını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ilkeleri/okuma | API Management hizmetinin tüm genel Ilke tanımlarını listeler. veya API Management hizmetinin genel ilke tanımını alın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ilkeleri/yazma | API Management hizmetinin genel ilke yapılandırmasını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ilke/silme | Kiracı düzeyinde ilke yapılandırmasını silme |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ilke/okuma | İlke yapılandırmasını kiracı düzeyinde al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ilke/yazma | Kiracı düzeyinde ilke yapılandırması oluştur |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/policyDescriptions/Read | Tüm ilke açıklamalarını listeler. |
> | Eylem | Microsoft. Apimanaya/Service/policySnippets/Read | Tüm ilke parçacıklarını listeler. |
> | Eylem | Microsoft. Apimanaveya hizmet/portalsettings/Read | Portalın oturum açma ayarlarını alın veya portalın kaydolma ayarlarını alın veya portalın yetkilendirme ayarlarını alın. |
> | Eylem | Microsoft. Apimanaveya hizmet/portalsettings/Write | Oturum açma ayarlarını güncelleştirin. ya da oturum açma ayarlarını oluşturun veya güncelleştirin. veya kaydolma ayarlarını güncelleştirebilir veya kaydolma ayarlarını güncelleştirebilir veya yetkilendirme ayarlarını güncelleştirebilirsiniz. veya, yetkilendirme ayarlarını oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/API/silme | Belirtilen üründen belirtilen API 'yi siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/API/okuma | Bir ürünle ilişkili API 'lerin bir koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/API/yazma | Belirtilen ürüne bir API ekler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/silme | Ürünü silin. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/gruplar/Sil | Belirtilen grup ve ürün arasındaki ilişkiyi siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/gruplar/okuma | Belirtilen ürünle ilişkili geliştirici gruplarının koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/gruplar/yazma | Belirtilen geliştirici grubu arasındaki ilişkiyi belirtilen ürüne ekler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/ilkeler/silme | Üründe ilke yapılandırmasını siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/ilkeler/okuma | Ürün düzeyinde ilke yapılandırmasını alın. veya ürün düzeyinde ilke yapılandırmasını alın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/ilkeler/yazma | Ürün için ilke yapılandırması oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/ilke/silme | Ürün düzeyinde ilke yapılandırmasını silme |
> | Eylem | Microsoft. Apimanaize/hizmet/ürünler/ilke/okuma | Ürün düzeyinde ilke yapılandırmasını al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/ilke/yazma | Ürün düzeyinde ilke yapılandırması oluştur |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/okuma | Belirtilen hizmet örneğindeki ürünlerin koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen ürünün ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/abonelikler/okuma | Belirtilen ürüne yönelik aboneliklerin koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ürünleri/etiketleri/silme | Etiketi üründen ayırın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ürünleri/etiketleri/okuma | Ürünle ilişkili tüm etiketleri listeler. veya ürünle ilişkili Get etiketi. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/ürünler/Etiketler/yazma | Ürüne etiket atayın. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/ürünleri/yazma | Bir ürünü oluşturur veya güncelleştirir. veya mevcut ürün ayrıntılarını güncelleştirin. |
> | Eylem | Microsoft. Apimanaya/Service/productsByTags/Read | Etiketlerle ilişkili ürünlerin koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/özellikleri/silme | API Management hizmet örneğinden belirli bir özelliği siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/özellikleri/okuma | Bir hizmet örneği içinde tanımlanan özelliklerin bir koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen özelliğin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/özellikleri/yazma | Bir özelliği oluşturur veya güncelleştirir. veya belirli bir özelliği güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kotalar/dönemler/okuma | Süre için kota sayacı değerini Al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kotalar/dönemler/yazma | Kota sayacı geçerli değeri ayarla |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kotalar/okuma | Kota değerlerini al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kotalar/yazma | Kota sayacı geçerli değeri ayarla |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/okuma | API Management hizmet örneği için meta verileri oku |
> | Eylem | Microsoft. Apimanamı/hizmeti/bölgeleri/okuma | Hizmetin bulunduğu tüm Azure bölgelerini listeler. |
> | Eylem | Microsoft. Apimanaya/hizmet/rapor/okuma | Zaman dilimlerine göre toplanan raporu alın veya coğrafi bölgeye göre toplanan raporu alın veya geliştiriciler tarafından toplanan raporları alın.<br>ya da ürünlere göre toplanan raporları alın.<br>ya da API 'Ler tarafından toplanan veya işleme göre toplanan ya da bir abonelik tarafından toplanan rapor almak için rapor alın.<br>ya da istekleri bildiren verileri al |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/geri yükleme/eylem | API Management hizmetini Kullanıcı tarafından sağlanmış bir depolama hesabındaki belirtilen kapsayıcıdan geri yükle |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/abonelik/silme | Belirtilen aboneliği siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/abonelikler/okuma | API Management hizmeti örneğinin tüm aboneliklerini listeler. veya belirtilen abonelik varlığını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/abonelikler/regeneratePrimaryKey/Action | API Management hizmet örneğinin mevcut aboneliğinin birincil anahtarını yeniden oluşturur. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/abonelikler/regenerateSecondaryKey/Action | API Management hizmet örneğinin mevcut aboneliğinin ikincil anahtarını yeniden oluşturur. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/abonelikleri/yazma | Belirtilen kullanıcının aboneliğini belirtilen ürüne oluşturur veya güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen aboneliğin ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/tagResources/Read | Etiketlerle ilişkili kaynakların koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/etiketleri/silme | API Management hizmet örneğinin belirli bir etiketini siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/etiketleri/okuma | Bir hizmet örneği içinde tanımlanan etiketlerin koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen etiketin ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/etiketleri/yazma | Bir etiket oluşturur. ya da kendi tanımlayıcısı tarafından belirtilen etiketin ayrıntılarını güncelleştirir. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/şablonlar/silme | Varsayılan API Management e-posta şablonunu sıfırla |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/şablonları/okuma | Tüm e-posta şablonlarını alır veya API Management e-posta şablonu ayrıntılarını alır |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/şablonları/yazma | E-posta şablonu API Management API Management e-posta şablonu veya güncelleştirmeleri oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kiracı/silme | Kiracı için ilke yapılandırmasını kaldırma |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kiracı/dağıtım/eylem | Belirtilen git dalından veritabanındaki yapılandırmaya yapılan değişiklikleri uygulamak için bir dağıtım görevi çalıştırır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/operationResults/Read | İşlem sonuçlarının listesini al veya belirli bir işlemin sonucunu al |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/okuma | API Management hizmetinin genel ilke tanımını alın. ya da kiracı erişim bilgileri al ayrıntıları |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/regeneratePrimaryKey/eylem | Birincil erişim anahtarını yeniden oluştur |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/regenerateSecondaryKey/Action | İkincil erişim anahtarını yeniden oluştur |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/kaydet/eylem | Depodaki belirtilen dala yapılandırma anlık görüntüsüne sahip COMMIT oluşturur |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/syncState/Read | Son git eşitlemesinin durumunu al |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kiracı/doğrulama/eylem | Belirtilen git dalından değişiklikleri doğrular |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kiracı/yazma | Kiracı için ilke yapılandırması ayarlama veya kiracı erişim bilgilerini güncelleştirme ayrıntıları |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/updatecertificate/Action | API Management hizmeti için SSL sertifikası yükleme |
> | Eylem | Microsoft. Apimanaya/Service/updatehostname/Action | API Management hizmeti için özel etki alanı adlarını kurma, güncelleştirme veya kaldırma |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/eylemi | Yeni bir Kullanıcı Kaydet |
> | Eylem | Microsoft. Apimanaveya hizmet/Kullanıcı/teyitler/gönderme/eylem | Onay gönderir |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/silme | Belirli kullanıcıyı siler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmet/kullanıcılar/generateSsoUrl/eylem | Belirli bir kullanıcıyı geliştirici portalına imzalamak için bir kimlik doğrulama belirteci içeren bir yeniden yönlendirme URL 'SI alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/grupları/okuma | Tüm Kullanıcı gruplarını listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/kimlikleri/okuma | Tüm kullanıcı kimliklerinin listesi. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/anahtarları/okuma | Kullanıcıyla ilişkili anahtarları al |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/okuma | Belirtilen hizmet örneğindeki kayıtlı kullanıcıların koleksiyonunu listeler. ya da kendi tanımlayıcısı tarafından belirtilen kullanıcının ayrıntılarını alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/abonelikleri/okuma | Belirtilen kullanıcının aboneliklerinin koleksiyonunu listeler. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/belirteci/eylemi | Kullanıcı için paylaşılan erişim yetkilendirme belirtecini alır. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/yazma | Bir kullanıcı oluşturur veya güncelleştirir. ya da kendi tanımlayıcısı tarafından belirtilen kullanıcının ayrıntılarını günceller. |
> | Eylem | Microsoft. Apimanayönetimi/hizmeti/yazma | API Management hizmeti 'nin yeni bir örneğini oluşturma |
> | Eylem | Microsoft. Apimanayönetimi/kaydını kaldırma/eylem | Microsoft. Apimana, kaynak sağlayıcısı için aboneliği kayıt kaldırmayı kaldır |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Authorization/classicAdministrators/Delete | Yöneticiyi abonelikten kaldırır. |
> | Eylem | Microsoft. Authorization/classicAdministrators/operationdurumlarının/Read | Aboneliğin yönetici işlem durumlarını alır. |
> | Eylem | Microsoft. Authorization/classicAdministrators/Read | Aboneliğin yöneticilerini okur. |
> | Eylem | Microsoft. Authorization/classicAdministrators/Write | Bir aboneliğe yönetici ekleyin veya bir aboneliği değiştirin. |
> | Eylem | Microsoft. Authorization/Denyasatamaları/silme | Belirtilen kapsamdaki reddetme atamasını silin. |
> | Eylem | Microsoft. Authorization/Denyasatamaları/okuma | Reddetme atama hakkında bilgi alın. |
> | Eylem | Microsoft. Authorization/Denyasatamaları/yazma | Belirtilen kapsamda bir reddetme ataması oluşturun. |
> | Eylem | Microsoft. Authorization/yükseltir Teaccess/Action | Arayan kullanıcının kiracı kapsamındaki Kullanıcı erişimi yöneticisi erişimine izin verir |
> | Eylem | Microsoft. Authorization/kilitleri/silme | Belirtilen kapsamdaki kilitleri silin. |
> | Eylem | Microsoft. Authorization/kilitleri/okuma | Belirtilen kapsamdaki kilitleri alır. |
> | Eylem | Microsoft. Authorization/kilitleri/yazma | Belirtilen kapsamdaki kilitleri ekleyin. |
> | Eylem | Microsoft. Authorization/işlemler/okuma | İşlem listesini alır |
> | Eylem | Microsoft. Authorization/Permissions/Read | Arayanın verilen kapsamda sahip olduğu tüm izinleri listeler. |
> | Eylem | Microsoft. Authorization/ilkeleri/denetim/eylem | ' Audit ' efektiyle Azure Ilkesi değerlendirmesinin sonucu olarak gerçekleştirilecek eylem |
> | Eylem | Microsoft. Authorization/policies/Auditınotexists/Action | ' Auditınotexists ' efektiyle Azure Ilkesi değerlendirmesinin sonucu olarak gerçekleştirilecek eylem |
> | Eylem | Microsoft. Authorization/ilkeleri/reddetme/eylem | ' Reddet ' efektiyle Azure Ilkesi değerlendirmesinin sonucu olarak gerçekleştirilen eylem |
> | Eylem | Microsoft. Authorization/policies/deployIfNotExists/Action | ' DeployIfNotExists ' efektiyle Azure Ilkesi değerlendirmesinin sonucu olarak gerçekleştirilecek eylem |
> | Eylem | Microsoft. Authorization/Poliyasatamaları/silme | Belirtilen kapsamda bir ilke atamasını silin. |
> | Eylem | Microsoft. Authorization/Poliyasatamaları/okuma | İlke atama hakkında bilgi alın. |
> | Eylem | Microsoft. Authorization/Poliyasatamaları/yazma | Belirtilen kapsamda bir ilke ataması oluşturun. |
> | Eylem | Microsoft. Authorization/policyDefinitions/Delete | İlke tanımını silin. |
> | Eylem | Microsoft. Authorization/policyDefinitions/Read | Bir ilke tanımı hakkında bilgi alın. |
> | Eylem | Microsoft. Authorization/policyDefinitions/Write | Özel bir ilke tanımı oluşturun. |
> | Eylem | Microsoft. Authorization/policySetDefinitions/Delete | İlke kümesi tanımını silin. |
> | Eylem | Microsoft. Authorization/policySetDefinitions/Read | İlke kümesi tanımı hakkında bilgi alın. |
> | Eylem | Microsoft. Authorization/policySetDefinitions/Write | Özel bir ilke kümesi tanımı oluşturun. |
> | Eylem | Microsoft. Authorization/providerOperations/Read | Rol tanımlarında kullanılabilen tüm kaynak sağlayıcıları için işlemleri alın. |
> | Eylem | Microsoft. Authorization/Roleatamaları/silme | Belirtilen kapsamda bir rol atamasını silin. |
> | Eylem | Microsoft. Authorization/Roleatamalar/okuma | Rol ataması hakkında bilgi alın. |
> | Eylem | Microsoft.Authorization/roleAssignments/write | Belirtilen kapsamda bir rol ataması oluşturun. |
> | Eylem | Microsoft. Authorization/roleDefinitions/Delete | Belirtilen özel rol tanımını silin. |
> | Eylem | Microsoft. Authorization/roleDefinitions/Read | Rol tanımı hakkında bilgi alın. |
> | Eylem | Microsoft. Authorization/roleDefinitions/Write | Belirtilen izinlerle ve atanabilir kapsamlarla özel bir rol tanımı oluşturun veya güncelleştirin. |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Automation/automationAccounts/Agentregistrationınformation/Read | Azure Automation DSC kayıt bilgilerini okuyun |
> | Eylem | Microsoft. Automation/automationAccounts/Agentregistrationınformation/regenerateKey/Action | Azure Automation DSC anahtarlarını yeniden üretme isteği yazar |
> | Eylem | Microsoft. Automation/automationAccounts/sertifikalar/Sil | Bir Azure Otomasyonu sertifika varlığını siler |
> | Eylem | Microsoft. Automation/automationAccounts/sertifikalar/getCount/Action | Sertifika sayısını okur |
> | Eylem | Microsoft. Automation/automationAccounts/sertifikalar/okuma | Bir Azure Otomasyonu sertifika varlığı alır |
> | Eylem | Microsoft. Automation/automationAccounts/sertifikalar/yazma | Bir Azure Otomasyonu sertifika varlığı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/compilationjobs/Read | Azure Automation DSC derlemesini okur |
> | Eylem | Microsoft. Automation/automationAccounts/compilationjobs/Read | Azure Automation DSC derlemesini okur |
> | Eylem | Microsoft. Automation/automationAccounts/compilationjobs/Write | Azure Automation DSC derlemesini yazar |
> | Eylem | Microsoft. Automation/automationAccounts/compilationjobs/Write | Azure Automation DSC derlemesini yazar |
> | Eylem | Microsoft. Automation/automationAccounts/Configurations/içerik/okuma | Yapılandırma medyası içeriğini okur |
> | Eylem | Microsoft. Automation/automationAccounts/Configurations/Delete | Azure Automation DSC içeriğini siler |
> | Eylem | Microsoft. Automation/automationAccounts/Configurations/getCount/Action | Azure Automation DSC içeriğinin sayısını okur |
> | Eylem | Microsoft. Automation/automationAccounts/Configurations/Read | Azure Automation DSC içeriğini alır |
> | Eylem | Microsoft. Automation/automationAccounts/Configurations/Write | Azure Automation DSC içeriğini Yazar |
> | Eylem | Microsoft. Automation/automationAccounts/bağlantılar/Sil | Bir Azure Otomasyonu bağlantı varlığını siler |
> | Eylem | Microsoft. Automation/automationAccounts/Connections/getCount/Action | Bağlantı sayısını okur |
> | Eylem | Microsoft. Automation/automationAccounts/Connections/Read | Bir Azure Otomasyonu bağlantı varlığı alır |
> | Eylem | Microsoft. Automation/automationAccounts/bağlantılar/yazma | Bir Azure Otomasyonu bağlantı varlığı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/connectionTypes/Delete | Bir Azure Otomasyonu bağlantı türü varlığını siler |
> | Eylem | Microsoft. Automation/automationAccounts/connectionTypes/Read | Bir Azure Otomasyonu bağlantı türü varlığını alır |
> | Eylem | Microsoft. Automation/automationAccounts/connectionTypes/Write | Bir Azure Otomasyonu bağlantı türü varlığı oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/kimlik bilgileri/sil | Bir Azure Otomasyonu kimlik bilgisi varlığını siler |
> | Eylem | Microsoft. Automation/automationAccounts/kimlik bilgileri/getCount/Action | Kimlik bilgilerinin sayısını okur |
> | Eylem | Microsoft. Automation/automationAccounts/kimlik bilgileri/okuma | Bir Azure Otomasyonu kimlik bilgisi varlığını alır |
> | Eylem | Microsoft. Automation/automationAccounts/kimlik bilgileri/yazma | Bir Azure Otomasyonu kimlik bilgisi varlığı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/Delete | Bir Azure Otomasyonu hesabını siler |
> | Eylem | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Delete | Karma runbook çalışanı kaynaklarını siler |
> | Eylem | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Karma runbook çalışanı kaynaklarını okur |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/output/Read | Bir işin çıkışını alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/okuma | Bir Azure Otomasyonu işini alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/sürdürülecek/Action | Bir Azure Otomasyonu işini sürdürür |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/runbookContent/Action | Azure Otomasyonu runbook 'un içeriğini iş yürütme sırasında alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/durdur/eylem | Bir Azure Otomasyonu işini durduruyor |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/akışlar/okuma | Bir Azure Otomasyonu iş akışı alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/akışlar/okuma | Bir Azure Otomasyonu iş akışı alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/beklet/eylem | Bir Azure Otomasyonu işini askıya alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobs/Write | Azure Otomasyonu işi oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/Jobzamanlamalar/silme | Bir Azure Otomasyonu iş zamanlamasını siler |
> | Eylem | Microsoft. Automation/automationAccounts/Jobzamanlamalar/okuma | Bir Azure Otomasyonu iş zamanlaması alır |
> | Eylem | Microsoft. Automation/automationAccounts/Jobzamanlamalar/yazma | Bir Azure Otomasyonu iş zamanlaması oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Otomasyon hesabına bağlı çalışma alanını alır |
> | Eylem | Microsoft. Automation/automationAccounts/listKeys/Action | Otomasyon hesabının anahtarlarını okur |
> | Eylem | Microsoft. Automation/automationAccounts/modüller/etkinlikler/okuma | Azure Otomasyonu etkinliklerini alır |
> | Eylem | Microsoft. Automation/automationAccounts/modüller/Sil | Bir Azure Otomasyonu PowerShell modülünü siler |
> | Eylem | Microsoft. Automation/automationAccounts/modüller/getCount/Action | Otomasyon hesabı içindeki PowerShell modüllerinin sayısını alır |
> | Eylem | Microsoft. Automation/automationAccounts/modüller/okuma | Bir Azure Otomasyonu PowerShell modülünü alır |
> | Eylem | Microsoft. Automation/automationAccounts/modüller/yazma | Bir Azure Otomasyonu PowerShell modülü oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/nodeConfigurations/Delete | Azure Automation DSC 'nin düğüm yapılandırmasını siler |
> | Eylem | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Azure Automation DSC 'nin düğüm yapılandırma içeriğini okur |
> | Eylem | Microsoft. Automation/automationAccounts/nodeConfigurations/Read | Azure Automation DSC 'in düğüm yapılandırmasını okur |
> | Eylem | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Azure Automation DSC 'ın düğüm yapılandırmasını yazar |
> | Eylem | Microsoft. Automation/automationAccounts/nodecounts/okuma | Belirtilen tür için düğüm sayısı özetini okur |
> | Eylem | Microsoft. Automation/automationAccounts/Nodes/Delete | Azure Automation DSC düğümlerini siler |
> | Eylem | Microsoft. Automation/automationAccounts/Nodes/Read | Azure Automation DSC düğümlerini okur |
> | Eylem | Microsoft. Automation/automationAccounts/Nodes/raporlar/içerik/okuma | Azure Automation DSC rapor içeriğini okur |
> | Eylem | Microsoft. Automation/automationAccounts/Nodes/Reports/Read | Azure Automation DSC raporlarını okur |
> | Eylem | Microsoft. Automation/automationAccounts/Nodes/Write | Azure Automation DSC düğümlerini oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/Objectveri türleri/alanlar/okuma | Azure Otomasyonu TypeFields 'i alır |
> | Eylem | Microsoft. Automation/automationAccounts/python2Packages/Delete | Bir Azure Otomasyonu Python 2 paketini siler |
> | Eylem | Microsoft. Automation/automationAccounts/python2Packages/Read | Bir Azure Otomasyonu Python 2 paketini alır |
> | Eylem | Microsoft. Automation/automationAccounts/python2Packages/Write | Bir Azure Otomasyonu Python 2 paketi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/python3Packages/Delete | Bir Azure Otomasyonu Python 3 paketini siler |
> | Eylem | Microsoft. Automation/automationAccounts/python3Packages/Read | Bir Azure Otomasyonu Python 3 paketini alır |
> | Eylem | Microsoft. Automation/automationAccounts/python3Packages/Write | Bir Azure Otomasyonu Python 3 paketi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/Read | Bir Azure Otomasyonu hesabını alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/içerik/okuma | Bir Azure Otomasyonu runbook 'unun içeriğini alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/Sil | Bir Azure Otomasyonu runbook 'unu siler |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/içerik/yazma | Bir Azure Otomasyonu runbook taslağının içeriğini oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/operationResults/Read | Azure Otomasyonu runbook taslağı işlem sonuçlarını alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/okuma | Bir Azure Otomasyonu runbook taslağı alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/test Işi/okuma | Bir Azure Otomasyonu runbook taslağı test işini alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/test Işi/özgeçmişi/eylemi | Bir Azure Otomasyonu runbook taslağı test işini sürdürür |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/testJob/durdur/eylem | Bir Azure Otomasyonu runbook taslağı test işini durduruyor |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/test Işi/askıya alma/eylem | Bir Azure Otomasyonu runbook taslağı test işini askıya alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/test Işi/yazma | Bir Azure Otomasyonu runbook taslağı test işi oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/taslak/undoEdit/Action | Azure Otomasyonu runbook taslağında yapılan düzenlemeleri geri alma |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/getCount/Action | Azure Otomasyonu runbook 'larının sayısını alır |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/Yayımla/eylem | Bir Azure Otomasyonu runbook taslağı yayımlar |
> | Eylem | Microsoft. Automation/automationAccounts/runbook 'lar/okuma | Bir Azure Otomasyonu runbook 'unu alır |
> | Eylem | Microsoft. Automation/automationAccounts/Runbook/yazma | Bir Azure Otomasyonu runbook 'u oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/zamanlamalar/silme | Bir Azure Otomasyonu zamanlama varlığını siler |
> | Eylem | Microsoft. Automation/automationAccounts/zamanlamalar/getCount/Action | Azure Otomasyonu zamanlamalarının sayısını alır |
> | Eylem | Microsoft. Automation/automationAccounts/zamanlamalar/okuma | Bir Azure Otomasyonu zamanlama varlığını alır |
> | Eylem | Microsoft. Automation/automationAccounts/zamanlamalar/yazma | Bir Azure Otomasyonu zamanlama varlığı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/Softwareupdateconfigurationmachinerbir/Read | Bir Azure Otomasyonu yazılım güncelleştirme yapılandırması makinesi çalıştırmasını alır |
> | Eylem | Microsoft. Automation/automationAccounts/Softwareupdateconfigurationçalıştırmaları/okuma | Bir Azure Otomasyonu yazılım güncelleştirme yapılandırması çalıştırmasını alır |
> | Eylem | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Bir Azure Otomasyonu yazılım güncelleştirme yapılandırmasını siler |
> | Eylem | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Bir Azure Otomasyonu yazılım güncelleştirme yapılandırmasını siler |
> | Eylem | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Read | Bir Azure Otomasyonu yazılım güncelleştirme yapılandırmasını alır |
> | Eylem | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Read | Bir Azure Otomasyonu yazılım güncelleştirme yapılandırmasını alır |
> | Eylem | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Azure Otomasyonu yazılım güncelleştirme yapılandırması oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Azure Otomasyonu yazılım güncelleştirme yapılandırması oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/STATISTICS/Read | Azure Otomasyonu Istatistiklerini alır |
> | Eylem | Microsoft. Automation/automationAccounts/Updatedeploymentmachinerbir/Read | Azure Otomasyonu güncelleştirme dağıtım makinesi edinme |
> | Eylem | Microsoft. Automation/automationAccounts/updateManagementPatchJob/Read | Bir Azure Otomasyonu güncelleştirme yönetimi düzeltme eki işini alır |
> | Eylem | Microsoft. Automation/automationAccounts/kullanımlar/okuma | Azure Otomasyonu kullanımını alır |
> | Eylem | Microsoft. Automation/automationAccounts/değişkenler/Sil | Bir Azure Otomasyonu değişken varlığını siler |
> | Eylem | Microsoft. Automation/automationAccounts/değişkenler/okuma | Bir Azure Otomasyonu değişken varlığını okur |
> | Eylem | Microsoft. Automation/automationAccounts/değişkenler/yazma | Bir Azure Otomasyonu değişken varlığı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/Delete | Azure Otomasyonu İzleyicisi işini silme |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/Read | Bir Azure Otomasyonu İzleyicisi işini alır |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/start/Action | Azure Otomasyonu izleyici işi başlatma |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/durdur/eylem | Azure Otomasyonu İzleyicisi işini durdurma |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/Streams/okuma | Bir Azure Otomasyonu İzleyicisi iş akışı alır |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/watcherActions/Delete | Azure Otomasyonu İzleyicisi iş eylemlerini silme |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/watcherActions/Read | Bir Azure Otomasyonu İzleyicisi iş eylemlerini alır |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/watcherActions/Write | Azure Otomasyonu İzleyicisi iş eylemleri oluşturma |
> | Eylem | Microsoft. Automation/automationAccounts/izleyicileri/Write | Bir Azure Otomasyonu izleyici işi oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/Web kancaları/eylem | Azure Otomasyonu Web kancası için URI oluşturur |
> | Eylem | Microsoft. Automation/automationAccounts/Web kancaları/silme | Bir Azure Otomasyonu web kancasını siler  |
> | Eylem | Microsoft. Automation/automationAccounts/Web kancaları/okuma | Bir Azure Otomasyonu web kancasını okur |
> | Eylem | Microsoft. Automation/automationAccounts/Web kancaları/yazma | Bir Azure Otomasyonu Web kancası oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/automationAccounts/Write | Bir Azure Otomasyonu hesabı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Automation/işlemler/okuma | Azure Otomasyonu kaynakları için kullanılabilir Işlemleri alır |
> | Eylem | Microsoft. Automation/Register/ACTION | Aboneliği Azure Otomasyonu 'na kaydeder |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. AzureActiveDirectory/b2cDirectories/Delete | B2C Dizin kaynağını Sil |
> | Eylem | Microsoft. AzureActiveDirectory/b2cDirectories/Read | B2C Dizin kaynağını görüntüle |
> | Eylem | Microsoft. AzureActiveDirectory/b2cDirectories/Write | B2C dizin kaynağı oluştur veya güncelleştir |
> | Eylem | Microsoft. AzureActiveDirectory/b2ctenants/Read | Kullanıcının üye olduğu tüm B2C kiracılarını listeler |
> | Eylem | Microsoft. AzureActiveDirectory/işlemler/okuma | Microsoft. AzureActiveDirectory kaynak sağlayıcısı için kullanılabilen tüm API işlemlerini okuyun |
> | Eylem | Microsoft. AzureActiveDirectory/Register/ACTION | Microsoft. AzureActiveDirectory kaynak sağlayıcısı için aboneliği kaydetme |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. AzureStack/Işlemler/okuma | Bir kaynak sağlayıcısı işleminin özelliklerini alır |
> | Eylem | Microsoft. AzureStack/Register/Action | Aboneliği Microsoft. AzureStack kaynak sağlayıcısına kaydeder |
> | Eylem | Microsoft. AzureStack/kayıt/müşteri abonelikleri/silme | Bir Azure Stack müşteri aboneliğini siler |
> | Eylem | Microsoft. AzureStack/kayıt/müşteri abonelikleri/okuma | Azure Stack müşteri aboneliğinin özelliklerini alır |
> | Eylem | Microsoft. AzureStack/kayıt/müşteri abonelikleri/yazma | Bir Azure Stack müşteri aboneliği oluşturur veya güncelleştirir |
> | Eylem | Microsoft. AzureStack/kayıt/silme | Azure Stack kaydını siler |
> | Eylem | Microsoft. AzureStack/kayıtlar/getActivationKey/eylem | En son Azure Stack etkinleştirme anahtarını alır |
> | Eylem | Microsoft. AzureStack/kayıtlar/ürünler/listDetails/Action | Azure Stack Market ürünü için genişletilmiş ayrıntıları alır |
> | Eylem | Microsoft. AzureStack/kayıtlar/ürünler/okuma | Azure Stack Market ürününün özelliklerini alır |
> | Eylem | Microsoft. AzureStack/kayıtlar/ürünler/uploadProductLog/Action | Market ürün işlemi durumunu ve zaman damgasını Azure Stack Kaydet |
> | Eylem | Microsoft. AzureStack/kayıtlar/okuma | Azure Stack kaydın özelliklerini alır |
> | Eylem | Microsoft. AzureStack/kayıtlar/yazma | Azure Stack kaydı oluşturur veya güncelleştirir |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/Sil | Bir uygulamayı siler |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/okuma | Uygulamaları listeler veya bir uygulamanın özelliklerini alır |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/sürümler/etkinleştir/eylem | Bir uygulama paketini etkinleştirir |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/sürümler/Sil | Uygulama paketini siler |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/sürümler/okuma | Uygulama paketinin özelliklerini alır |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/sürümler/yazma | Yeni bir uygulama paketi oluşturur veya var olan bir uygulama paketini güncelleştirir |
> | Eylem | Microsoft. Batch/batchAccounts/uygulamalar/yazma | Yeni bir uygulama oluşturur veya var olan bir uygulamayı güncelleştirir |
> | Eylem | Microsoft. Batch/batchAccounts/certificateOperationResults/Read | Bir Batch hesabında uzun süre çalışan bir sertifika işleminin sonuçlarını alır |
> | Eylem | Microsoft. Batch/batchAccounts/Certificates/cancelDelete/Action | Batch hesabındaki bir sertifikanın başarısız silme işlemini iptal eder |
> | Eylem | Microsoft. Batch/batchAccounts/sertifikalar/Sil | Bir toplu Iş hesabındaki bir sertifikayı siler |
> | Eylem | Microsoft. Batch/batchAccounts/sertifikalar/okuma | Bir Batch hesabındaki sertifikaları listeler veya bir sertifikanın özelliklerini alır |
> | Eylem | Microsoft. Batch/batchAccounts/sertifikalar/yazma | Batch hesabında yeni bir sertifika oluşturur veya mevcut bir sertifikayı güncelleştirir |
> | Eylem | Microsoft. Batch/batchAccounts/Delete | Batch hesabını siler |
> | DataAction | Microsoft. Batch/batchAccounts/Jobs/Delete | Batch hesabından bir işi siler |
> | DataAction | Microsoft. Batch/batchAccounts/Jobs/okuma | Bir Batch hesabındaki işleri listeler veya bir işin özelliklerini alır |
> | DataAction | Microsoft. Batch/batchAccounts/Jobs/Write | Batch hesabında yeni bir iş oluşturur veya var olan bir işi güncelleştirir |
> | DataAction | Microsoft. Batch/batchAccounts/Jobzamanlamalar/silme | Bir Batch hesabından iş zamanlaması siler |
> | DataAction | Microsoft. Batch/batchAccounts/Jobzamanlamalar/okuma | Bir Batch hesabındaki iş zamanlamalarını listeler veya iş zamanlamasının özelliklerini alır |
> | DataAction | Microsoft. Batch/batchAccounts/Jobzamanlamalar/yazma | Batch hesabında yeni bir iş zamanlaması oluşturur veya var olan bir iş zamanlamasını güncelleştirir |
> | Eylem | Microsoft. Batch/batchAccounts/ListKeys/ACTION | Batch hesabı için erişim anahtarlarını listeler |
> | Eylem | Microsoft. Batch/batchAccounts/operationResults/Read | Uzun süre çalışan bir Batch hesabı işleminin sonuçlarını alır |
> | Eylem | Microsoft. Batch/batchAccounts/poolOperationResults/Read | Bir Batch hesabında uzun süre çalışan havuz işleminin sonuçlarını alır |
> | Eylem | Microsoft. Batch/batchAccounts/havuzlar/Delete | Bir toplu Iş hesabındaki havuzu siler |
> | Eylem | Microsoft. Batch/batchAccounts/havuzlar/Disableotomatik ölçeklendirme/eylem | Batch hesap havuzu için otomatik ölçeklendirmeyi devre dışı bırakır |
> | Eylem | Microsoft. Batch/batchAccounts/havuzlar/Read | Bir Batch hesabındaki havuzları listeler veya havuzun özelliklerini alır |
> | Eylem | Microsoft. Batch/batchAccounts/havuzlar/stopResize/eylem | Batch hesap havuzunda devam eden bir yeniden boyutlandırma işlemini durduruyor |
> | Eylem | Microsoft. Batch/batchAccounts/havuzlar/Write | Batch hesabında yeni bir havuz oluşturur veya var olan bir havuzu güncelleştirir |
> | Eylem | Microsoft. Batch/batchAccounts/okuma | Batch hesaplarını listeler veya Batch hesabının özelliklerini alır |
> | Eylem | Microsoft. Batch/batchAccounts/regeneratekeys/ACTION | Batch hesabı için erişim anahtarlarını yeniden oluşturur |
> | Eylem | Microsoft. Batch/batchAccounts/syncAutoStorageKeys/Action | Bir Batch hesabı için yapılandırılan otomatik depolama hesabı için erişim anahtarlarını eşitler |
> | Eylem | Microsoft. Batch/batchAccounts/Write | Yeni bir Batch hesabı oluşturur veya var olan bir Batch hesabını güncelleştirir |
> | Eylem | Microsoft. Batch/konumlar/accountOperationResults/Read | Uzun süre çalışan bir Batch hesabı işleminin sonuçlarını alır |
> | Eylem | Microsoft. Batch/Locations/Checknameavaılabılıty/Action | Hesap adının geçerli ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. Batch/konumlar/kotalar/okuma | Belirtilen Azure bölgesindeki belirtilen aboneliğin toplu kotalarını alır |
> | Eylem | Microsoft. Batch/işlemler/okuma | Microsoft. Batch kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | Eylem | Microsoft. Batch/Register/ACTION | Batch kaynak sağlayıcısı için aboneliği kaydeder ve Batch hesaplarının oluşturulmasını etkinleştirilir |
> | Eylem | Microsoft. Batch/Unregister/eylem | Batch kaynak sağlayıcısı için aboneliğin kaydını siler Batch hesaplarının oluşturulmasını önler |

## <a name="microsoftbilling"></a>Microsoft. Faturalandırma

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. faturalandırma/billingAccounts/sözleşmeleri/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingPermissions/Read |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Billingprofiller/billingPermissions/Read |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/müşteriler/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/faturalar/fiyat listesi/download/ACTION |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/billingPermissions/Read |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/Billingabonelikabonelikleri/taşıma/eylem |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/Billingabonelikabonelikleri/aktarım/eylem |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/Billingabonelikleri/Validatemoveuygunluğu/eylemi |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/ürünler/Move/Action |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/ürünler/aktarım/eylem |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/Products/Validatemoveuygunluk/eylem |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/Read |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/ınvoicesections/Write |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/fiyat listesi/download/Action |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/billingProfiles/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Billingprofiller/Write |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Billingprofiller/Write |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Billingabonelikleri/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/müşteriler/billingPermissions/Read |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/müşteriler/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/departmanlar/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Kayıthesapsayısı/billingPermissions/Read |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/KayıtSayısı/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/kayıt/veya tüm Izinler/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Listınvoicesectionswithcreatesubscriptionpermission/Action |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/ürünler/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/okuma |  |
> | Eylem | Microsoft. faturalandırma/billingAccounts/Write |  |
> | Eylem | Microsoft. faturalandırma/departmanlar/okuma |  |
> | Eylem | Microsoft. faturalandırma/Register/ACTION |  |
> | Eylem | Microsoft. faturalandırma/validateAddress/ACTION |  |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. BingMaps/Mapapsıs/Delete | Silme Işlemi |
> | Eylem | Microsoft. BingMaps/Mapapsıs/Listgizlilikler/Action | Gizli dizileri listeleyin |
> | Eylem | Microsoft. BingMaps/Mapapsıs/listSingleSignOnToken/Action | Kaynak Için çoklu oturum açma yetkilendirme belirtecini oku |
> | Eylem | Microsoft. BingMaps/Mapapsıs/okuma | Okuma Işlemi |
> | Eylem | Microsoft. BingMaps/Mapapsıs/regenerateKey/Action | Anahtarı yeniden oluşturur |
> | Eylem | Microsoft. BingMaps/Mapapsıs/Write | Yazma Işlemi |
> | Eylem | Microsoft. BingMaps/Işlemler/okuma | İşlemin açıklaması. |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Blockzincirine/blockchainMembers/Delete | Varolan bir blok zinciri üyesini siler. |
> | Eylem | Microsoft. Blockzincirine/blockchainMembers/listApiKeys/eylem | Varolan blok zinciri üye API anahtarlarını alır veya listeler. |
> | Eylem | Microsoft. Blockzincirine/blockchainMembers/Read | Varolan blok zinciri üyelerini alır veya listeler. |
> | DataAction | Microsoft. Blockzincirini/blockchainMembers/transactionNodes/Connect/ACTION | Bir blok zinciri üye Işlem düğümüne bağlanır. |
> | Eylem | Microsoft. Blockzincirini/blockchainMembers/transactionNodes/Delete | Varolan bir blok zinciri üye Işlem düğümünü siler. |
> | Eylem | Microsoft. Blockzincirine/blockchainMembers/transactionNodes/listApiKeys/eylem | Varolan blok zinciri üye Işlem düğümü API anahtarlarını alır veya listeler. |
> | Eylem | Microsoft. Blockzincirini/blockchainMembers/transactionNodes/Read | Varolan blok zinciri üye Işlem düğümlerini alır veya listeler. |
> | Eylem | Microsoft. Blockzincirine/blockchainMembers/transactionNodes/Write | Bir blok zinciri üye Işlem düğümü oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Blockzincirine/blockchainMembers/Write | Bir blok zinciri üyesi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Blockzinciri/konumlar/blockchainMemberOperationResults/Read | Blok zinciri üyelerinin Işlem sonuçlarını alır. |
> | Eylem | Microsoft. Blockzincirine/konumlarına/Checknameavaılabılıty/Action | Kaynak adının geçerli olduğunu ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. blok zinciri/işlemler/okuma | Microsoft blok zinciri kaynak sağlayıcısındaki tüm Işlemleri listeleyin. |
> | Eylem | Microsoft. Blockzincirine/Register/ACTION | Blok zinciri kaynak sağlayıcısı için aboneliği kaydeder. |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Blueprint/Blueprintasbir/atama/Read | Tüm şema yapılarını okuyun |
> | Eylem | Microsoft. Blueprint/Blueprintasbir/Delete | Tüm şema yapıtları silme |
> | Eylem | Microsoft. Blueprint/Blueprintasbir/Read | Tüm şema yapılarını okuyun |
> | Eylem | Microsoft. Blueprint/Blueprintasbir/whoisblueprınt/Action | Azure şemaları hizmet sorumlusu nesne kimliğini alın. |
> | Eylem | Microsoft. Blueprint/Blueprintasbir/Write | Herhangi bir şema yapıtı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Blueprint/planlar/yapıtlar/silme | Tüm şema yapıtları silme |
> | Eylem | Microsoft. Blueprint/planlar/yapıtlar/okuma | Tüm şema yapılarını okuyun |
> | Eylem | Microsoft. Blueprint/planlar/yapıtlar/yazma | Herhangi bir şema yapıtı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. BLUEPRINT/planlar/silme | Tüm şemaları silme |
> | Eylem | Microsoft. Blueprint/planlar/okuma | Tüm şemaları okuyun |
> | Eylem | Microsoft. Blueprint/planlar/sürümler/yapıtlar/okuma | Tüm şema yapılarını okuyun |
> | Eylem | Microsoft. Blueprint/planlar/sürümler/Sil | Tüm şemaları silme |
> | Eylem | Microsoft. Blueprint/planlar/sürümler/okuma | Tüm şemaları okuyun |
> | Eylem | Microsoft. Blueprint/planlar/sürümler/Write | Tüm şemaları oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Blueprint/planlar/yazma | Tüm şemaları oluşturun veya güncelleştirin |
> | Eylem | Microsoft. BLUEPRINT/Register/Action | Azure şemaları kaynak sağlayıcısını kaydeder |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. BotService/botServices/kanallar/Delete | Bir bot hizmetini silme |
> | Eylem | Microsoft. BotService/botServices/kanallar/Read | Bir bot hizmetini okuyun |
> | Eylem | Microsoft. BotService/botServices/kanallar/Write | Bir bot hizmeti yazın |
> | Eylem | Microsoft. BotService/botServices/Connections/Delete | Bir bot hizmetini silme |
> | Eylem | Microsoft. BotService/botServices/Connections/Read | Bir bot hizmetini okuyun |
> | Eylem | Microsoft. BotService/botServices/Connections/Write | Bir bot hizmeti yazın |
> | Eylem | Microsoft. BotService/botServices/Delete | Bir bot hizmetini silme |
> | Eylem | Microsoft. BotService/botServices/Read | Bir bot hizmetini okuyun |
> | Eylem | Microsoft. BotService/botServices/Write | Bir bot hizmeti yazın |
> | Eylem | Microsoft. BotService/konumlar/operationresults/Read | Zaman uyumsuz bir işlemin durumunu okuyun |
> | Eylem | Microsoft. BotService/Işlemler/okuma | Tüm kaynak türleri için işlemleri okuyun |

## <a name="microsoftcache"></a>Microsoft. Cache

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Cache/checknameavaılabılıty/Action | Bir adın yeni bir Redis Cache kullanılmak üzere kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. Cache/Locations/operationresults/Read | ' Location ' üstbilgisinin istemciye daha önce döndürüldüğü uzun süren bir işlemin sonucunu alır |
> | Eylem | Microsoft. Cache/işlemler/okuma | ' Microsoft. cache ' sağlayıcısının desteklediği işlemleri listeler. |
> | Eylem | Microsoft. Cache/redsıs/Delete | Tüm Redis Cache Sil |
> | Eylem | Microsoft. Cache/redsıs/Export/ACTION | Redsıs verilerini belirtilen biçimde ön ek depolama bloblarına aktar |
> | Eylem | Microsoft. Cache/redsıs/firewallRules/Delete | Redis Cache IP güvenlik duvarı kurallarını silme |
> | Eylem | Microsoft. Cache/redsıs/firewallRules/Read | Redis Cache IP güvenlik duvarı kurallarını al |
> | Eylem | Microsoft. Cache/redsıs/firewallRules/Write | Redis Cache IP güvenlik duvarı kurallarını düzenleme |
> | Eylem | Microsoft. Cache/redsıs/forceReboot/Action | Bir önbellek örneğini yeniden başlatmaya zorla, büyük olasılıkla veri kaybı olabilir. |
> | Eylem | Microsoft. Cache/redsıs/Import/Action | Birden çok blobdan belirtilen biçimdeki verileri Redsıs 'e aktar |
> | Eylem | Microsoft. Cache/redsıs/LinkedServers/Delete | Redis Cache bağlı sunucuyu silme |
> | Eylem | Microsoft. Cache/redsıs/LinkedServers/okuma | Bir redsıs önbelleğiyle ilişkili bağlı sunucuları alın. |
> | Eylem | Microsoft. Cache/redsıs/LinkedServers/Write | Redis Cache bağlı sunucu ekleme |
> | Eylem | Microsoft. Cache/redsıs/listKeys/ACTION | Yönetim portalında Redis Cache erişim anahtarlarının değerini görüntüleme |
> | Eylem | Microsoft. Cache/redsıs/listUpgradeNotifications/Read | Önbellek kiracısı için en son yükseltme bildirimlerini listeleyin. |
> | Eylem | Microsoft. Cache/redsıs/metricDefinitions/okuma | Redis Cache için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. Cache/Reda/Patchzamanlamalar/silme | Redis Cache Patch zamanlamasını silme |
> | Eylem | Microsoft. Cache/redsıs/Patchzamanlamalar/okuma | Redis Cache düzeltme eki uygulama zamanlamasını alır |
> | Eylem | Microsoft. Cache/redsıs/Patchzamanlamalar/yazma | Redis Cache düzeltme eki uygulama zamanlamasını değiştirme |
> | Eylem | Microsoft. Cache/redsıs/Read | Yönetim portalında Redis Cache ayarlarını ve yapılandırmasını görüntüleme |
> | Eylem | Microsoft. Cache/redsıs/regenerateKey/Action | Yönetim portalında Redis Cache erişim anahtarlarının değerini değiştirme |
> | Eylem | Microsoft. Cache/redsıs/start/Action | Bir önbellek örneği başlatın. |
> | Eylem | Microsoft. Cache/reddir/durdur/eylem | Bir önbellek örneğini durdurun. |
> | Eylem | Microsoft. Cache/redsıs/Write | Yönetim portalında Redis Cache ayarlarını ve yapılandırmasını değiştirme |
> | Eylem | Microsoft. Cache/Register/ACTION | ' Microsoft. cache ' kaynak sağlayıcısını bir abonelikle kaydeder |
> | Eylem | Microsoft. Cache/Unregister/eylem | ' Microsoft. cache ' kaynak sağlayıcısının kaydını abonelikle siler |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Capacity/appliedreservations/Read | Tüm rezervasyonları oku |
> | Eylem | Microsoft. Capacity/calculateexchange/Action | Exchange tutarını ve yeni satın alma fiyatını hesaplar ve ilke hatalarını döndürür. |
> | Eylem | Microsoft. Capacity/calculateprice/Action | Herhangi bir rezervasyon fiyatını hesapla |
> | Eylem | Microsoft. Capacity/kataloglar/okuma | Ayırma kataloğunu okuyun |
> | Eylem | Microsoft. Capacity/checktekliflere/eyleme | Tüm abonelik tekliflerini denetleyin |
> | Eylem | Microsoft. Capacity/checkscopes/Action | Herhangi bir aboneliği denetleyin |
> | Eylem | Microsoft. Capacity/ticari cıalrezervationorders/Read | Herhangi bir kiracıda oluşturulan rezervasyon emirlerini al |
> | Eylem | Microsoft. Capacity/Exchange/eylem | Herhangi bir ayırmayı Exchange |
> | Eylem | Microsoft. Capacity/işlemler/okuma | Herhangi bir Işlemi okuyun |
> | Eylem | Microsoft. Capacity/Register/ACTION | Kapasite kaynak sağlayıcısını kaydeder ve kapasite kaynaklarının oluşturulmasına izin vermez. |
> | Eylem | Microsoft. Capacity/rezervationorders/ACTION | Tüm ayırmaları güncelleştirme |
> | Eylem | Microsoft. Capacity/rezervationorders/availablescopes/eylem | Kullanılabilir herhangi bir kapsamı bulun |
> | Eylem | Microsoft. Capacity/rezervationorders/hesaplaizterefund/eylem | Para iadesi tutarını ve yeni satın alma fiyatını hesaplar ve ilke hatalarını döndürür. |
> | Eylem | Microsoft. Capacity/rezervationorders/Delete | Tüm ayırmaları Sil |
> | Eylem | Microsoft. Capacity/rezervationorders/Merge/Action | Tüm ayırmaları birleştirme |
> | Eylem | Microsoft. Capacity/rezervationorders/Read | Tüm rezervasyonları oku |
> | Eylem | Microsoft. Capacity/rezervationorders/rezervasyonlar/eylem | Tüm ayırmaları güncelleştirme |
> | Eylem | Microsoft. Capacity/rezervationorders/rezervasyonlar/availablescopes/eylem | Kullanılabilir herhangi bir kapsamı bulun |
> | Eylem | Microsoft. Capacity/rezervationorders/rezervasyonlar/Delete | Tüm ayırmaları Sil |
> | Eylem | Microsoft. Capacity/rezervationorders/rezervasyonlar/okundu | Tüm rezervasyonları oku |
> | Eylem | Microsoft. Capacity/rezervationorders/rezervasyonlar/düzeltmeler/okuma | Tüm rezervasyonları oku |
> | Eylem | Microsoft. Capacity/rezervationorders/rezervasyonlar/Write | Herhangi bir ayırma oluşturun |
> | Eylem | Microsoft. Capacity/rezervationorders/Return/ACTION | Herhangi bir ayırmayı döndürme |
> | Eylem | Microsoft. Capacity/rezervationorders/Split/ACTION | Tüm ayırmaları Böl |
> | Eylem | Microsoft. Capacity/rezervationorders/Swap/ACTION | Tüm ayırmaları değiştirme |
> | Eylem | Microsoft. Capacity/rezervationorders/Write | Herhangi bir ayırma oluşturun |
> | Eylem | Microsoft. Capacity/kiracılar/Register/Action | Tüm kiracıyı Kaydet |
> | Eylem | Microsoft. Capacity/Unregister/eylem | Tüm kiracının kaydını sil |
> | Eylem | Microsoft. Capacity/validatereservationorder/eylem | Tüm ayırmaları doğrulama |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. CDN/Checknameavaılabılıty/Action |  |
> | Eylem | Microsoft. CDN/CheckResourceUsage/eylem |  |
> | Eylem | Microsoft. CDN/edgenodes/Delete |  |
> | Eylem | Microsoft. CDN/edgenodes/okuma |  |
> | Eylem | Microsoft. CDN/edgenodes/yazma |  |
> | Eylem | Microsoft. CDN/operationresults/Delete |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/silme |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Delete |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Read |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Write |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/Delete |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/Load/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Delete |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Read |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Write |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/Temizleme/eylem |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/Read |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/start/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/durdur/Action |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/ValidateCustomDomain/ACTION |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/endpointresults/Write |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/GenerateSsoUri/eylem |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/eylem |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/okuma |  |
> | Eylem | Microsoft. CDN/operationresults/profileresults/yazma |  |
> | Eylem | Microsoft. CDN/operationresults/Read |  |
> | Eylem | Microsoft. CDN/operationresults/Write |  |
> | Eylem | Microsoft. CDN/işlemler/okuma |  |
> | Eylem | Microsoft. CDN/Profiles/CheckResourceUsage/Action |  |
> | Eylem | Microsoft. CDN/profiller/silme |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/CheckResourceUsage/ACTION |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/customdomains/Delete |  |
> | Eylem | Microsoft. CDN/Profiles/uç noktaları/customdomains/DisableCustomHttps/eylem |  |
> | Eylem | Microsoft. CDN/Profiles/uç noktaları/customdomains/EnableCustomHttps/eylem |  |
> | Eylem | Microsoft. CDN/Profiles/uç noktaları/customdomains/Read |  |
> | Eylem | Microsoft. CDN/Profiles/uç noktaları/customdomains/Write |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/silme |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/yükleme/eylem |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/kaynakları/Sil |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/kaynaklar/okuma |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/çıkış/yazma |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/Temizleme/eylem |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/okuma |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/Başlat/eylem |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/durdur/eylem |  |
> | Eylem | Microsoft. CDN/Profiles/uç noktaları/ValidateCustomDomain/ACTION |  |
> | Eylem | Microsoft. CDN/profiller/uç noktaları/yazma |  |
> | Eylem | Microsoft. CDN/Profiles/GenerateSsoUri/eylem |  |
> | Eylem | Microsoft. CDN/Profiles/GetSupportedOptimizationTypes/Action |  |
> | Eylem | Microsoft. CDN/profiller/okuma |  |
> | Eylem | Microsoft. CDN/profiller/yazma |  |
> | Eylem | Microsoft. CDN/Register/ACTION | CDN kaynak sağlayıcısı için aboneliği kaydeder ve CDN profillerinin oluşturulmasına izin vermez. |
> | Eylem | Microsoft. CDN/Validatearaştırma/eylem |  |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/sertifikalar/Sil | Mevcut bir sertifikayı silme |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/sertifikalar/okuma | Sertifika listesini al |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Certificates/Write | Yeni bir sertifika ekleme veya var olan bir sertifikayı güncelleştirme |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Delete | Var olan bir AppServiceCertificate silme |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Read | CertificateOrders listesini alın |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/restart/Action | Mevcut bir sertifika siparişi 'ı yeniden gönderin |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Yenile/eylem | Mevcut bir sertifika siparişini Yenile |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/resendEmail/Action | Sertifika e-postasını yeniden gönder |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Resendrequestemaıls/eylem | İstek e-postalarını başka bir e-posta adresine yeniden |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Resendrequestemaıls/eylem | Verilen App Service Sertifikası için site mühürlemek alın |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Sertifika eylemlerinin listesini al |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Sertifika e-posta geçmişini al |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Verifydomainsahiplik/eylem | Etki alanı sahipliğini doğrulama |
> | Eylem | Microsoft. CertificateRegistration/certificateOrders/Write | Yeni bir sertifika siparişi ekleme veya var olanı güncelleştirme |
> | Eylem | Microsoft. CertificateRegistration/işlemler/okuma | App Service sertifika kaydından tüm işlemleri Listele |
> | Eylem | Microsoft. CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Hizmet uygulaması sorumlusu için hizmet sorumlusu sağlama |
> | Eylem | Microsoft. CertificateRegistration/Register/ACTION | Abonelik için Microsoft sertifikaları kaynak sağlayıcısını kaydetme |
> | Eylem | Microsoft. CertificateRegistration/Validatecertificateregistrationınformation/Action | Sertifika satın alma nesnesini göndermeden doğrula |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ClassicCompute/Capabilities/Read | Özellikleri gösterir |
> | Eylem | Microsoft. ClassicCompute/Checkdomainnameavaılabılıty/Action | Belirli bir etki alanı adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. ClassicCompute/Checkdomainnameavaılabılıty/Read | Belirli bir etki alanı adının kullanılabilirliğini alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/etkin/Write | Etkin etki alanı adını ayarlar. |
> | Eylem | Microsoft. ClassicCompute/domainNames/kullanılabilirliği \ kümeler/okundu | Kaynak için kullanılabilirlik kümesini görüntüleyin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yetenekler/okuma | Etki alanı adı yeteneklerini gösterir |
> | Eylem | Microsoft. ClassicCompute/domainNames/Delete | Kaynaklar için etki alanı adlarını kaldırın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/okuma | Dağıtım yuvalarını gösterir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/roller/okuma | Etki alanı adının dağıtım yuvasında rol al |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/roller/roleınstances/Read | Etki alanı adının dağıtım yuvasındaki rolün rol örneğini al |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/durumu/okuma | Dağıtım yuvası durumunu alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/durum/yazma | Dağıtım yuvası durumunu ekleyin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/upgradedomain/Read | Etki alanı adındaki dağıtım yuvası için yükseltme etki alanını al |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/upgradedomain/Write | Etki alanı adında dağıtım yuvası için yükseltme etki alanını güncelleştir |
> | Eylem | Microsoft. ClassicCompute/domainNames/deploymentyuvaları/yazma | Dağıtımı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Extensions/Sil | Etki alanı adı uzantılarını kaldırın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Extensions/Operationdurumlarının/Read | Etki alanı adları uzantılarının işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Extensions/Read | Etki alanı adı uzantılarını döndürür. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Extensions/Write | Etki alanı adı uzantılarını ekleyin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Delete | Yeni bir iç yük dengesini kaldırın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Operationdurumlarının/Read | Etki alanı adları iç yük dengeleyiciler için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Read | İç yük dengeleyicileri alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/internalLoadBalancers/Write | Yeni bir iç Yük Dengeleme oluşturur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Operationdurumlarının/Read | Etki alanı adları yük dengeli uç nokta kümeleri için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Read | Yük dengeli uç nokta kümelerini alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/loadBalancedEndpointSets/Write | Yük dengeli uç nokta kümesini ekleyin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/operationdurumlarının/Read | Etki alanı adının işlem durumunu alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Operationdurumlarının/Read | Etki alanı adları uzantılarının işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Read | Kaynaklar için etki alanı adlarını döndürün. |
> | Eylem | Microsoft. ClassicCompute/domainNames/serviceCertificates/Delete | Kullanılan hizmet sertifikalarını silin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/serviceCertificates/Operationdurumlarının/Read | Etki alanı adları hizmet sertifikaları için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/serviceCertificates/Read | Kullanılan hizmet sertifikalarını döndürür. |
> | Eylem | Microsoft. ClassicCompute/domainNames/serviceCertificates/Write | Kullanılan hizmet sertifikalarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/abortMigration/Action | Dağıtım yuvasının geçişini durdurur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/commitMigration/Action | Bir dağıtım yuvasının geçişini kaydeder. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/Delete | Verilen bir dağıtım yuvasını siler. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/Operationdurumlarının/Read | Etki alanı adı yuvaları için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/prepareMigration/Action | Bir dağıtım yuvasının geçişini hazırlar. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/okuma | Dağıtım yuvalarını gösterir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/extensionReferences/Delete | Dağıtım yuvası rolü için uzantı başvurusunu kaldırın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/extensionReferences/Operationdurumlarının/Read | Etki alanı adı yuvaları rol uzantısı başvuruları için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/extensionReferences/Read | Dağıtım yuvası rolü için uzantı başvurusunu döndürür. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/extensionReferences/Write | Dağıtım yuvası rolü için uzantı başvurusunu ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/MetricDefinitions/okuma | Etki alanı adı için rol ölçümü tanımını alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/ölçümler/okuma | Etki alanı adı için rol ölçümünü al. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/operationdurumlarının/Read | Etki alanı adları yuva rolü için işlem durumunu alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/sağlayıcılar/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/okuma | Dağıtım yuvasının rolünü alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Roleınstances/downloadremotedesktopconnectionfile/Action | Etki alanı adı yuva rolündeki rol örneği için Uzak Masaüstü bağlantı dosyasını indirir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Roleınstances/Operationdurumlarının/Read | Etki alanı adları yuva rolündeki rol örneği için işlem durumunu alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Roleınstances/Read | Rol örneğini alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Roleınstances/Rebuild/Action | Rol örneğini yeniden oluşturur. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Roleınstances/ReImage/Action | Rol örneğini yeniden görüntüler. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Roleınstances/restart/Action | Rol örneklerini yeniden başlatır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/SKU/okuma | Dağıtım yuvası için rol SKU 'su alın. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/roller/Write | Dağıtım yuvası için rol ekleyin. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/start/Action | Bir dağıtım yuvası başlatır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/eyalet/Başlat/yaz | Dağıtım yuvası durumunu Durduruldu olarak değiştirir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/eyalet/durdur/yaz | Dağıtım yuvası durumunu başlatıldı olarak değiştirir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/durdur/eylem | Dağıtım yuvasını askıya alır. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/upgradeDomain/Write | Etki alanını yükseltme. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/validateMigration/Action | Dağıtım yuvasının geçişini doğrular. |
> | Eylem | Microsoft. ClassicCompute/domainNames/yuvalar/Write | Dağıtımı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Swap/Action | Hazırlama yuvasını üretim yuvasına değiştirir. |
> | Eylem | Microsoft. ClassicCompute/domainNames/Write | Kaynaklar için etki alanı adlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Tüm klasik kaynakları farklı bir aboneliğe taşıyın. |
> | Eylem | Microsoft. ClassicCompute/operatingSystemFamilies/Read | Microsoft Azure ' de bulunan konuk işletim sistemi ailelerini listeler ve ayrıca her bir aile için kullanılabilir işletim sistemi sürümlerini listeler. |
> | Eylem | Microsoft. ClassicCompute/operatingSystems/Read | Microsoft Azure ' de şu anda kullanılabilir olan konuk işletim sisteminin sürümlerini listeler. |
> | Eylem | Microsoft. ClassicCompute/işlemler/okuma | İşlem listesini alır. |
> | Eylem | Microsoft. ClassicCompute/Operationkıdurumlar/okuma | Kaynak için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/kotalar/okundu | Aboneliğin kotasını alın. |
> | Eylem | Microsoft. ClassicCompute/Register/Action | Klasik Işlem 'a kaydol |
> | Eylem | Microsoft. ClassicCompute/resourceTypes/SKU/okuma | Desteklenen kaynak türleri için SKU listesini alır. |
> | Eylem | Microsoft. ClassicCompute/Validatesubscriptionmoveavaılabılıty/Action | Klasik taşıma işlemi için aboneliğin kullanılabilirliğini doğrulayın. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Ilişkili Networksecuritygroups/Delete | Sanal makineyle ilişkilendirilmiş ağ güvenlik grubunu siler. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Ilişkili Networksecuritygroups/Operationdurumlarının/Read | Sanal makineler ile ilişkili ağ güvenlik grupları için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Ilişkili Networksecuritygroups/Read | Sanal makineyle ilişkili ağ güvenlik grubunu alır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Ilişkili Networksecuritygroups/Write | Sanal makineyle ilişkilendirilmiş bir ağ güvenlik grubu ekler. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Olası zaman uyumsuz işlemleri alır |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/attachDisk/eylem | Bir sanal makineye bir veri diski ekler. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Capture/Action | Bir sanal makineyi yakala. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Delete | Sanal makineleri kaldırır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/çıkarılabilir disk/eylem | Bir veri diskini sanal makineden ayırır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/diagnosticsettings/Read | Sanal makine tanılama ayarlarını al. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/diskler/okuma | Veri disklerinin listesini alır |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/Action | Sanal makine için RDP dosyasını indirir. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Extensions/Operationdurumlarının/Read | Sanal makine uzantılarının işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/uzantılar/okuma | Sanal makine uzantısını alır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/uzantılar/Write | Sanal makine uzantısını koyar. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/MetricDefinitions/Read | Sanal makine ölçüm tanımını alın. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/ölçümler/okuma | Ölçümleri alır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/NetworkInterfaces/ile Ilişkili Networksecuritygroups/Delete | Ağ arabirimiyle ilişkili ağ güvenlik grubunu siler. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/NetworkInterfaces/ile Ilişkili Networksecuritygroups/Operationdurumlarının/Read | Sanal makineler ile ilişkili ağ güvenlik grupları için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/NetworkInterfaces/ile Ilişkili Networksecuritygroups/Read | Ağ arabirimiyle ilişkili ağ güvenlik grubunu alır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/NetworkInterfaces/ile Ilişkili Networksecuritygroups/Write | Ağ arabirimiyle ilişkili bir ağ güvenlik grubu ekler. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Operationdurumlarının/Read | Sanal makineler için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/performMaintenance/Action | Sanal makinede bakım gerçekleştirir. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/okuma | Sanal makinelerin listesini alır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/yeniden dağıtma/eylem | Sanal makineyi yeniden dağıtır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/restart/Action | Sanal makineleri yeniden başlatır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/kapatması/eylemi | Sanal makineyi kapatır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Başlat/eylem | Sanal makineyi başlatın. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/durdur/eylem | Sanal makineyi sonlandırır. |
> | Eylem | Microsoft. ClassicCompute/virtualMachines/Write | Sanal makineler ekleyin veya değiştirin. |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/operationdurumlarının/Read | Bir Express Route çapraz bağlantı işlem durumunu alın. |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/peerler/Sil | Express Route çapraz bağlantı eşlemesini silin. |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/peerler/operationdurumlarının/Read | Bir Express Route çapraz bağlantı eşleme işlem durumunu alır. |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/peerler/okundu | Hızlı rota çapraz bağlantı eşlemesini alın. |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/peerler/yaz | Express Route çapraz bağlantı eşlemesi ekleyin. |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/Read | Express Route çapraz bağlantılarını alın. |
> | Eylem | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Express Route çapraz bağlantıları ekleyin. |
> | Eylem | Microsoft. ClassicNetwork/gatewaySupportedDevices/Read | Desteklenen cihazların listesini alır. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/Delete | Ağ güvenlik grubunu siler. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/Operationdurumlarının/Read | Ağ güvenlik grubu için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Ağ güvenlik grupları tanılama ayarlarını alır |
> | Eylem | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Ağ güvenlik grupları tanılama ayarlarını oluşturur veya güncelleştirir, bu işlem Öngörüler kaynak sağlayıcısı tarafından belirlenir. |
> | Eylem | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. Insights/logDefinitions/Read | Ağ güvenlik grubu olaylarını alır |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/Read | Ağ güvenlik grubunu alır. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete | Güvenlik kuralını siler. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Operationdurumlarının/Read | Ağ güvenlik grubu güvenlik kuralları için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Read | Güvenlik kuralını alır. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Write | Bir güvenlik kuralı ekler veya güncelleştirir. |
> | Eylem | Microsoft. ClassicNetwork/networkSecurityGroups/Write | Yeni bir ağ güvenlik grubu ekler. |
> | Eylem | Microsoft. ClassicNetwork/Operations/Read | Klasik ağ işlemlerini alın. |
> | Eylem | Microsoft. ClassicNetwork/Quotas/Read | Aboneliğin kotasını alın. |
> | Eylem | Microsoft. ClassicNetwork/Register/Action | Klasik ağa Kaydet |
> | Eylem | Microsoft. ClassicNetwork/Rezervedips/Delete | Ayrılmış bir IP 'yi silin. |
> | Eylem | Microsoft. ClassicNetwork/Rezervedips/JOIN/Action | Ayrılmış bir IP 'ye katılır |
> | Eylem | Microsoft. ClassicNetwork/Rezervedips/bağlantı/eylem | Ayrılmış bir IP 'yi bağlama |
> | Eylem | Microsoft. ClassicNetwork/Rezervedips/Operationdurumlarının/Read | Ayrılmış IP 'ler için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicNetwork/Rezervedips/Read | Ayrılmış IP 'Leri alır |
> | Eylem | Microsoft. ClassicNetwork/Rezervedips/Write | Yeni bir ayrılmış IP ekleyin |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/Action | Bir sanal ağın geçişini durdurur |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Capabilities/Read | Özellikleri gösterir |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Checkıpaddressavailability/eylem | Bir sanal ağdaki belirli bir IP adresinin kullanılabilirliğini denetler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/Action | Bir sanal ağın geçişini kaydeder |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Delete | Sanal ağı siler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clientiptal edilmiş sertifikalar/Sil | Bir istemci sertifikasını iptal eder. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clientiptal edilmiş sertifikalar/okuma | İptal edilen istemci sertifikalarını okuyun. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clientiptal edilmiş sertifikalar/Write | Bir istemci sertifikasını iptal eder. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clienentrootcertificates/Delete | Sanal ağ geçidi istemci sertifikasını siler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clienentrootcertificates/Download/Action | Sertifikayı parmak izine göre indirir. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clienentrootcertificates/listPackage/Action | Sanal ağ geçidi sertifika paketini listeler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clienentrootcertificates/Read | İstemci kök sertifikalarını bulun. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Clienentrootcertificates/Write | Yeni bir istemci kök sertifikasını karşıya yükler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Connections/Connect/Action | Bir siteyi site ağ geçidi bağlantısına bağlar. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Connections/Disconnect/Action | Bir siteden siteye ağ geçidi bağlantısı bağlantısını keser. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Connections/Read | Bağlantıların listesini alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Connections/test/Action | Bir siteyi site ağ geçidi bağlantısına sınar. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Delete | Sanal ağ geçidini siler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Downloaddeviceconfigurationscrıpt/Action | Cihaz yapılandırma betiğini indirir. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Downloadagnostiği/Action | Ağ Geçidi tanılamayı indirir. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Listdevresi ServiceKey/Action | Devre hizmet anahtarını alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/listPackage/Action | Sanal ağ geçidi paketini listeler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Operationdurumlarının/Read | Sanal ağ geçitleri için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Packages/Read | Sanal ağ geçidi paketini alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Read | Sanal ağ geçitlerini alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/startDiagnostics/Action | Sanal ağ geçidi için tanılamayı başlatır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/stopDiagnostics/Action | Sanal ağ geçidi için tanılamayı sonlandırır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Write | Bir sanal ağ geçidi ekler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action | Sanal ağı birleştirir. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Operationdurumlarının/Read | Sanal ağların işlem durumunu okur. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Bir sanal ağı başka bir sanal ağla eşler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/Action | Bir sanal ağın geçişini hazırlar |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Read | Sanal ağı alın. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Remotevirtualnetworkpeeringproxy/Delete | Uzak sanal ağ eşleme ara sunucusunu siler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Remotevirtualnetworkpeeringproxy/Read | Uzak sanal ağ eşleme ara sunucusunu alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Remotevirtualnetworkpeeringproxy/Write | Uzak sanal ağ eşleme proxy 'sini ekler veya güncelleştirir. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/altağ/Ilişkili Networksecuritygroups/Delete | Alt ağla ilişkili ağ güvenlik grubunu siler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/altağ/Ilişkili Networksecuritygroups/Operationdurumlar/Read | Sanal ağ alt ağı ile ilişkili ağ güvenlik grubu için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/alt ağlar/Ilişkili olan Networksecuritygroups/Read | Alt ağla ilişkili ağ güvenlik grubunu alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/altağ/Ilişkili Networksecuritygroups/Write | Alt ağla ilişkili bir ağ güvenlik grubu ekler. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/Action | Bir sanal ağın geçişini doğrular |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Virtualnetworkpeerler/okuma | Sanal Ağ eşlemesini alır. |
> | Eylem | Microsoft. ClassicNetwork/virtualNetworks/Write | Yeni bir sanal ağ ekleyin. |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ClassicStorage/Capabilities/Read | Özellikleri gösterir |
> | Eylem | Microsoft. ClassicStorage/checkStorageAccountAvailability/eylem | Bir depolama hesabının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. ClassicStorage/checkStorageAccountAvailability/okuma | Bir depolama hesabının kullanılabilirliğini alın. |
> | Eylem | Microsoft. ClassicStorage/diskleri/okuma | Depolama hesabı diskini döndürür. |
> | Eylem | Microsoft. ClassicStorage/Images/operationdurumlarının/Read | Görüntü Işlemi durumunu alır. |
> | Eylem | Microsoft. ClassicStorage/Images/Read | Görüntüyü döndürür. |
> | Eylem | Microsoft. ClassicStorage/işlemler/okuma | Klasik depolama işlemlerini alır |
> | Eylem | Microsoft. ClassicStorage/Osımages/Read | İşletim sistemi görüntüsünü döndürür. |
> | Eylem | Microsoft. ClassicStorage/Osplatformımages/Read | İşletim sistemi platformu görüntüsünü alır. |
> | Eylem | Microsoft. ClassicStorage/Publicımages/Read | Ortak sanal makine görüntüsünü alır. |
> | Eylem | Microsoft. ClassicStorage/kotaları/Read | Aboneliğin kotasını alın. |
> | Eylem | Microsoft. ClassicStorage/Register/Action | Klasik depolamaya kaydolun |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/abortMigration/Action | Bir depolama hesabının geçişini durdurur. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/commitMigration/Action | Bir depolama hesabının geçişini kaydeder. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Delete | Depolama hesabını silin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Disks/Delete | Belirli bir depolama hesabı diskini siler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Disks/Operationdurumlarının/Read | Kaynak için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Disks/Read | Depolama hesabı diskini döndürür. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/disk/Write | Bir depolama hesabı diski ekler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Images/Delete | Verilen bir depolama hesabı görüntüsünü siler. Kullanım dışı. ' Microsoft. ClassicStorage/storageAccounts/Vmımages ' kullanın) |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Images/operationdurumlarının/Read | Depolama hesabı görüntü işlemi durumunu döndürür. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Images/Read | Depolama hesabı görüntüsünü döndürür. Kullanım dışı. ' Microsoft. ClassicStorage/storageAccounts/Vmımages ' kullanın) |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Operationdurumlarının/Read | Kaynak için işlem durumunu okur. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Osımages/Delete | Belirli bir depolama hesabı işletim sistemi görüntüsünü siler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Osımages/Read | Depolama hesabı işletim sistemi görüntüsünü döndürür. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Osımages/Write | Belirli bir depolama hesabı işletim sistemi görüntüsünü ekler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/prepareMigration/Action | Bir depolama hesabının geçişini hazırlar. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Read | Verilen hesaba sahip depolama hesabını döndürün. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Depolama hesabı için mevcut erişim anahtarlarını yeniden oluşturur. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Services/ölçümler/okuma | Ölçümleri alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Services/Read | Kullanılabilir hizmetleri alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarlarını alın. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ölçüm tanımlarını alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/validateMigration/Action | Bir depolama hesabının geçişini doğrular. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Vmımages/Delete | Belirli bir sanal makine görüntüsünü siler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Vmımages/operationdurumlarının/Read | Belirli bir sanal makine görüntüsü işlem durumunu alır. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Vmımages/Read | Sanal makine görüntüsünü döndürür. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Vmımages/Write | Belirli bir sanal makine görüntüsünü ekler. |
> | Eylem | Microsoft. ClassicStorage/storageAccounts/Write | Yeni bir depolama hesabı ekler. |
> | Eylem | Microsoft. ClassicStorage/Vmımages/Read | Sanal makine görüntülerini listeler. |

## <a name="microsoftcognitiveservices"></a>Microsoft. Biliveservices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | DataAction | Microsoft. Biliveservices/hesaplar/otomatik öneri/arama/eylem | Bu işlem, belirli bir sorgu veya kısmi sorgu için öneriler sağlar. |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/çözümle/eylem | Bu işlem, görüntü içeriğine göre zengin bir görsel özellikler kümesini ayıklar.  |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/areaofınterest/Action | Bu işlem görüntünün en önemli alanının etrafında bir sınırlayıcı kutu döndürür. |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/açıkla/eylem | Bu işlem, bir görüntünün bir açıklamasını tüm cümleler olan okunabilir dilde oluşturur.<br> Açıklama, işlem tarafından da döndürülen bir içerik etiketleri koleksiyonunu temel alır.<br>Her görüntü için birden fazla açıklama oluşturulabilir.<br> Açıklamalar, güven puanlarına göre sıralanır.<br>Tüm açıklamalar Ingilizce 'Dir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/Algıla/eylem | Bu işlem, belirtilen görüntüde nesne algılamayı gerçekleştirir.  |
> | DataAction | Microsoft. Biliveservices/accounts/ComputerVision/generatethumbnail/Action | Bu işlem, Kullanıcı tarafından belirtilen genişlik ve yükseklik ile bir küçük resim oluşturur.<br> Varsayılan olarak, hizmet görüntüyü analiz eder, ilgilendiğiniz bölgeyi (ROı) tanımlar ve yatırım getirisi temelinde akıllı kırpma koordinatları oluşturur.<br> Akıllı kırpma, giriş görüntüsünden farklı bir boyut oranı belirttiğinizde yardımcı olur |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/modeller/çözümle/eylem | Bu işlem, bir görüntü içindeki içeriği, etki alanına özgü bir model uygulayarak tanır.<br> Görüntü İşleme API'si tarafından desteklenen alana özgü modellerin listesi/modeller GET isteği kullanılarak alınabilir.<br> Şu anda, API aşağıdaki alana özgü modeller sağlar: Ünlüler, yer işaretleri. |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/modeller/okuma | Bu işlem, Görüntü İşleme API'si tarafından desteklenen alana özgü modellerin listesini döndürür.  Şu anda API, etki alanına özgü şu modelleri destekliyor: ünlül tanıyıcı, yer işareti tanıyıcı. |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/OCR/eylem | Optik karakter tanıma (OCR), görüntüdeki metni algılar ve tanınan karakterleri makine tarafından kullanılabilen bir karakter akışına ayıklar.    |
> | DataAction | Microsoft. Biliveservices/accounts/ComputerVision/recognizetext/Action | Metin Tanıma işleminin sonucunu almak için bu arabirimi kullanın. Metin Tanıma arabirimini kullandığınızda, yanıt "Işlem-konum" adlı bir alan içerir. "Işlem-konum" alanı, Get Metin Tanıma Işlem sonucu işlemi için kullanmanız gereken URL 'YI içerir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/ComputerVision/Tag/Action | Bu işlem, sağlanan görüntünün içeriğiyle ilgili olan sözcüklerin veya etiketlerin bir listesini oluşturur.<br>Görüntü İşleme API'si, resimlerde nesneler, canlı kullanımlar, manzara veya eylemler temelinde bulunan Etiketler döndürebilir.<br>Kategorilerin aksine, etiketler hiyerarşik bir sınıflandırma sistemine göre düzenlenmemiştir, ancak resim içeriğine karşılık gelir.<br>Etiketler belirsizliği önlemek veya bağlam sağlamak için ipuçları içerebilir, örneğin "cello" etiketine "müzik gereci" ipucu gelebilir.<br>Tüm Etiketler Ingilizce 'Dir. |
> | DataAction | Microsoft. Biliveservices/accounts/ComputerVision/textoperations/Read | Bu arabirim, metin işlemi sonucunu tanımak için kullanılır. Bu arabirimin URL 'SI, Metin Tanıma arabiriminden döndürülen <b>"işlem-konum"</b> alanından alınmalıdır. |
> | Eylem | Microsoft. Biliveservices/hesaplar/Sil | API hesaplarını siler |
> | DataAction | Microsoft. Bilivehizmetleri/hesapları/EntitySearch/arama/eylem | Varlıkları alın ve belirli bir sorgu için sonuçları yerleştir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/algılama/eylem | Bir görüntüdeki insan yüzlerini algılayın, yüz dikdörtgenlerini ve isteğe bağlı olarak Faceid, yer işaretleri ve öznitelikleri yapın. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/çok yönlü listeler/Sil | Belirtilen yüz listesini silin. Yüz listesindeki ilgili yüz görüntüleri de silinecek. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/çok yönlü listeler/persistedfaces/Sil | Belirtilen Facelid ve Persisitedçok yönlü kimliği ile bir yüz listesinden yüz silme. İlgili yüz görüntüsü de silinecek. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/çok yönlü listeler/persistedfaces/Write | Belirli bir yüz listesine, en fazla 1.000 yüze kadar bir yüz ekleyin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/çok yönlü listeler/okundu | Yüz listesinin bir yüz listesinin Facelitıd, ad, userData ve yüzlerini alın. Yüz listeleri listesi ' Facelitıd, ad ve userData. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/çok yönlü listeler/yazma | Kullanıcı tarafından belirtilen Facelitıd, ad ve isteğe bağlı bir userData ile boş bir yüz listesi oluşturun. Ad ve userData gibi yüz listesinin güncelleştirme bilgilerine kadar en fazla 64 yüz listesi izin verilir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/findsimilars/eylem | Çok yönlü bir diziden, yüz listesinden veya büyük bir yüz listesinden benzer görünümlü yüzeyleri aramak için bir sorgu yüzünün çok yönlü kimliği. FaceID |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/Grup/eylem | Aday yüzleri bir yüz benzerliğine göre gruplara bölün. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/tanımla/eylem | bir kişi grubundan veya büyük kişi grubundan belirli bir sorgu kişisinin en yakın eşleşmelerini bulmak için 1-çok tanımlama. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/Sil | Belirtilen büyük bir yüz listesini silin. Büyük yüz listesindeki ilgili yüz görüntüleri de silinecek. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/persistedfaces/Sil | Belirtilen Largefacelitıd ve Persisitedceıd kimliğine göre büyük bir yüz listesinden yüz silme. İlgili yüz görüntüsü de silinecek. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/persistedfaces/okuma | Largefacelitıd ve persistedFaceId tarafından büyük yüz listesindeki kalıcı yüz listesini alın. Belirtilen büyük bir yüz listesinde yüzeyleri ' persistedFaceId ve userData listeleyin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/persistedfaces/Write | Belirli bir büyük yüz listesine en fazla 1.000.000 yüz ekleyerek bir yüz ekleyin. Büyük bir yüz listesindeki belirtilen yüzün userData alanını persistedFaceId göre güncelleştirin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/okundu | Büyük bir yüz listesinin Largefacelitıd, Name, userData alma. Büyük yüz listeleri, Largefacelitıd, Name ve userData bilgilerini listeleyin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/eğitme/eylem | Büyük bir yüz listesi eğitim görevi gönderme. Eğitim, yalnızca eğitilen büyük bir yüz listesinin kullanabileceği önemli bir adımdır. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/eğitim/okuma | Büyük yüz listesi eğitim durumunu tamamlandı veya hala devam eden denetlemek için. Largecelist eğitimi zaman uyumsuz bir işlemdir |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largeçok yönlü listeler/yaz | Kullanıcı tarafından belirtilen Largefacelitıd, adı ve isteğe bağlı bir userData ile boş bir büyük yüz listesi oluşturun. Ad ve userData dahil olmak üzere büyük bir yüz listesinin güncelleştirme bilgileri. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/Delete | Belirtilen Persongroupıd ile mevcut bir büyük kişi grubunu silin. Bu büyük kişi grubundaki kalıcı veriler silinecek. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/kişiler/eylem | Belirtilen büyük kişi grubunda yeni bir kişi oluşturun. Bu kişiye yüz eklemek için lütfen çağırın |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/kişiler/Sil | Büyük bir kişi grubundan mevcut bir kişiyi silin. Kişi girişinde tüm depolanan kişi verileri ve yüz görüntüleri silinir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/kişiler/persistedfaces/Delete | Büyük kişi grubundaki bir kişiden yüz silme. Bu yüz girişiyle ilgili yüz verileri ve görüntü de silinir. |
> | DataAction | Microsoft. Bilivehizmetleri/hesapları/yüz/largepersongroups/kişiler/persistedfaces/Read | Kişi yüz bilgilerini alın. Kalıcı kişi, Largepersongroupıd, PersonID ve persistedFaceId tarafından belirtilir. |
> | DataAction | Microsoft. Bilivehizmetleri/hesapları/yüz/largepersongroups/kişiler/persistedfaces/Write | Yüz tanıma veya doğrulama için bir kişiye yüz görüntüsünü büyük bir kişi grubuna ekleyin. Bir kişinin kalıcı yüz Kullanıcı adını güncelleştirme görüntüsü ile uğraşmak için. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/kişiler/okuma | Kişinin adını ve userData ve kayıtlı kişinin yüz görüntüsünü temsil eden kalıcı Factıd 'leri alın. Tüm kişilerin bilgilerini belirtilen büyük kişi grubunda PersonID, Name, userData ve persistedFaceIds dahil olmak üzere listeleyin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/kişiler/yaz | Bir kişinin adını veya Kullanıcı adını güncelleştirin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/Read | Adı ve userData dahil olmak üzere büyük bir kişi grubunun bilgilerini alın. Bu API, tüm mevcut büyük kişi gruplarının Largepeşinatlı, Name ve userData olan büyük kişi grubu bilgilerini döndürür. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/tren/eylem | Büyük bir kişi grubu eğitim görevi gönderme. Eğitim, yalnızca eğitilen büyük kişi grubunun kullanabileceği önemli bir adımdır. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/eğitim/okuma | Büyük kişi grubu eğitim durumunu tamamlandı veya hala devam eden denetlemek için. LargePersonGroup eğitimi zaman uyumsuz bir işlemdir |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/largepersongroups/Write | Kullanıcı tarafından belirtilen Largepersongroupıd, Name ve optional userData ile yeni bir büyük kişi grubu oluşturun. Mevcut bir büyük kişi grubunun adını ve userData öğesini güncelleştirin. Özellikler istek gövdesinde değilse değişmeden kalır. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişilik grupları/silme | Belirtilen Persongroupıd ile mevcut bir kişi grubunu silin. Bu kişi grubundaki kalıcı veriler silinecek. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişi/Grup/eylem | Belirtilen kişi grubunda yeni bir kişi oluşturun. Bu kişiye yüz eklemek için lütfen çağırın |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişi/kişi/silme | Mevcut bir kişiyi bir kişi grubundan silin. Kişi girişinde tüm depolanan kişi verileri ve yüz görüntüleri silinir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişiler/kişiler/persistedfaces/Sil | Kişi grubundaki bir kişiden yüz silme. Bu yüz girişiyle ilgili yüz verileri ve görüntü de silinir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/persongroups/kişiler/persistedfaces/Read | Kişi yüz bilgilerini alın. Kalıcı kişi, kişisgroupıd, PersonID ve persistedFaceId tarafından belirtilir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişiler/kişiler/persistedfaces/Write | Yüz tanıma veya doğrulama için kişi grubuna bir yüz görüntüsü ekleyin. Birden çok güncelleştirme görüntüsü ile uğraşmak için kişi tarafından kalıcı bir yüz Kullanıcı bilgisi alanı. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişi/kişi/okuma | Kişinin adını ve userData ve kayıtlı kişinin yüz görüntüsünü temsil eden kalıcı Factıd 'leri alın. Belirtilen kişi grubundaki tüm kişilerin bilgilerini, tescilli ID, ad, userData ve persistedFaceIds dahil olmak üzere listeleyin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişi/kişi/yazma | Bir kişinin adını veya Kullanıcı adını güncelleştirin. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişi grupları/okuma | Kişi grubu adı ve userData alma. Bu kişiye ait kişi bilgilerini almak için kişi gruplarının peşinatlı, adının ve userData listesini kullanın. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişilik grupları/eğitme/eylem | Bir kişi grubu eğitim görevi gönder. Eğitim, yalnızca eğitilen kişi grubunun kullanabileceği önemli bir adımdır. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişilik grupları/eğitim/okuma | Kişi grubu eğitim durumunu tamamlandı veya hala devam ediyor olarak denetlemek için. PersonGroup eğitimi, tetiklenen zaman uyumsuz bir işlemdir |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/kişi grupları/yazma | Belirtilen Persongroupıd, Name ve Kullanıcı tarafından belirtilen userData ile yeni bir kişi grubu oluşturun. Mevcut bir kişi grubunun adını ve userData öğesini güncelleştirin. Özellikler istek gövdesinde değilse değişmeden kalır. |
> | DataAction | Microsoft. Biliveservices/hesaplar/yüz/doğrulama/eylem | İki yüzün aynı kişiye ait olup olmadığını veya bir yüzün bir kişiye ait olup olmadığını doğrulayın. |
> | DataAction | Microsoft. Bilivehizmetleri/hesapları/ımagesearch/Ayrıntılar/eylem | Görüntüyü içeren Web sayfaları gibi bir görüntüyle ilgili öngörüleri döndürür. |
> | DataAction | Microsoft. Biliveservices/accounts/ımagesearch/Search/Action | Belirli bir sorgu için ilgili görüntüleri alın. |
> | DataAction | Microsoft. Biliveservices/accounts/ımagesearch/trbitiriliyor/Action | Şu anda popüler resimleri al. |
> | DataAction | Microsoft. Biliveservices/accounts/ImmersiveReader/getcontentmodelforreader/eylem | Bir tam ekran okuyucusu oturumu oluşturur |
> | DataAction | Microsoft. Biliveservices/accounts/ınktanıyıcı/Recognize/eylem | Bir dizi vuruş verisi, içeriği analiz eder ve tanınan metin dahil tanınan varlıkların bir listesini oluşturur. |
> | Eylem | Microsoft. Biliveservices/accounts/listKeys/Action | Anahtarları Listele |
> | DataAction | Microsoft. Biliveservices/accounts/Lua/tahmin/eylem | Verilen sorgu için yayımlanmış uç nokta tahminini alır. |
> | DataAction | Microsoft. Biliveservices/hesaplar/haber arama/categorysearch/Action | Bir belirtilen kategori için haberleri döndürür. |
> | DataAction | Microsoft. Biliveservices/hesaplar/haber arama/arama/eylem | Belirli bir sorgu için ilgili haber makalelerini alın. |
> | DataAction | Microsoft. Biliveservices/hesaplar/NewsSearch/trendingkonular/eylem | Bing tarafından tanımlanan popüler konuları alın. Bunlar, Bing giriş sayfasının alt kısmındaki başlık bölümünde gösterilen konulardır. |
> | Eylem | Microsoft. Biliveservices/hesaplar/okuma | API hesaplarını okur. |
> | Eylem | Microsoft. Biliveservices/accounts/regenerateKey/Action | Anahtarı yeniden oluştur |
> | Eylem | Microsoft. Biliveservices/hesaplar/SKU 'lar/okuma | Mevcut bir kaynak için kullanılabilir SKU 'Ları okur. |
> | DataAction | Microsoft. Biliveservices/accounts/SpellCheck/SpellCheck/Action | GET veya POST aracılığıyla bir yazım denetimi sorgusunun sonucunu elde edin. |
> | DataAction | Microsoft. Biliveservices/accounts/TextAnalytics/Entities/Action | API, belirli bir belgedeki bilinen varlıkların ve genel adlandırılmış varlıkların (\"kişi\", \"konum\", \"kuruluş\" vb.) listesini döndürür. |
> | DataAction | Microsoft. Biliveservices/accounts/TextAnalytics/keyphrases/Action | API, giriş metnindeki başlıca konuşma noktalarını gösteren bir dize listesi döndürür. |
> | DataAction | Microsoft. Biliveservices/accounts/TextAnalytics/Languages/eylem | API, algılanan dile ek olarak 0 ile 1 arasında bir sayısal puan döndürür. Puanın 1’e yakın olması, tanımlanan dilin %100 olasılıkla doğru olduğunu gösterir. Toplamda 120 dil desteklenir. |
> | DataAction | Microsoft. Biliveservices/hesaplar/TextAnalytics/yaklaşım/eylem | API, 0 ile 1 arasında bir sayısal puan döndürür.<br>1 ' e yakın puan pozitif yaklaşımı, 0 ' a yakın puan negatif yaklaşımı gösterir.<br>0,5 puanı, yaklaşım eksikliğinden (ör.<br>bir FACTOID Bildirisi). |
> | Eylem | Microsoft. Biliveservices/hesaplar/kullanımlar/okuma | Mevcut bir kaynak için kota kullanımını alın. |
> | DataAction | Microsoft. Biliveservices/hesaplar/VideoSearch/Ayrıntılar/eylem | İlgili videolar gibi bir video hakkında Öngörüler edinin. |
> | DataAction | Microsoft. Biliveservices/accounts/VideoSearch/Search/ACTION | Belirli bir sorguyla ilgili videoları alın. |
> | DataAction | Microsoft. Biliveservices/hesaplar/VideoSearch/trbitiriliyor/eylem | Şu anda popüler videoları alın. |
> | DataAction | Microsoft. Biliveservices/accounts/VisualSearch/Search/ACTION | Belirtilen görüntüyle ilgili etiketlerin listesini döndürür |
> | DataAction | Microsoft. Bilivehizmetleri/hesapları/Web araması/arama/eylem | Belirli bir sorgu için Web, resim, haber & Videolar sonuçları alın. |
> | Eylem | Microsoft. Biliveservices/hesaplar/yazma | API hesaplarını yazar. |
> | Eylem | Microsoft. Biliveservices/Checkdomainavaılabılıty/Action | Bir abonelik için kullanılabilir SKU 'Ları okur. |
> | Eylem | Microsoft. Biliveservices/Locations/Checkskuavaılabılıty/Action | Bir abonelik için kullanılabilir SKU 'Ları okur. |
> | Eylem | Microsoft. Biliveservices/Locations/Checkskuavaılabılıty/Action | Bir abonelik için kullanılabilir SKU 'Ları okur. |
> | Eylem | Microsoft. Biliveservices/Locations/Deletevirtualnetworkoralt ağları/eylemi | VirtualNetworks veya alt ağları silmenin Microsoft. Network 'e yönelik bildirim. |
> | Eylem | Microsoft. Biliveservices/Işlemler/okuma | Tüm kullanılabilir işlemleri Listele |
> | Eylem | Microsoft. Biliveservices/Register/Action | Bilişsel hizmetler için aboneliği kaydeder |
> | Eylem | Microsoft. Biliveservices/Register/Action | Bilişsel hizmetler için aboneliği kaydeder |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Commerce/RateCard/okuma | Verilen abonelik için teklif verileri, kaynak/ölçüm meta verileri ve ücretler döndürür. |
> | Eylem | Microsoft. Commerce/Usagetoplamalar/okuma | Microsoft Azure bir aboneliğe göre tüketimini alır. Sonuç, belirli bir zaman aralığında kullanım verilerini, aboneliği ve kaynakla ilgili bilgileri toplar. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/Delete | Kullanılabilirlik kümesini siler |
> | Eylem | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/Read | Bir kullanılabilirlik kümesinin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/vmSizes/Read | Kullanılabilirlik kümesinde bir sanal makine oluşturmak veya güncelleştirmek için kullanılabilir boyutları listeleyin |
> | Eylem | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/Write | Yeni bir kullanılabilirlik kümesi oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/diskEncryptionSets/Delete | Disk şifreleme kümesini silme |
> | Eylem | Microsoft. COMPUTE/diskEncryptionSets/Read | Disk şifreleme kümesinin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/diskEncryptionSets/Write | Yeni bir disk şifrelemesi kümesi oluşturma veya var olanı güncelleştirme |
> | Eylem | Microsoft. COMPUTE/Disks/beginGetAccess/Action | Blob erişimi için diskin SAS URI 'sini al |
> | Eylem | Microsoft. COMPUTE/diskler/Sil | Diski siler |
> | Eylem | Microsoft. COMPUTE/Disks/endGetAccess/Action | Diskin SAS URI 'sini iptal etme |
> | Eylem | Microsoft. COMPUTE/Disks/Read | Bir diskin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Disks/Write | Yeni bir disk oluşturur veya var olan bir diski güncelleştirir |
> | Eylem | Microsoft. COMPUTE/galeriler/silme | Galeriyi siler |
> | Eylem | Microsoft. COMPUTE/galeriler/resimler/silme | Galeri görüntüsünü siler |
> | Eylem | Microsoft. COMPUTE/galeriler/resimler/okuma | Galeri resminin özelliklerini alır |
> | Eylem | Microsoft. COMPUTE/galeriler/resimler/sürümler/Sil | Galeri görüntüsü sürümünü siler |
> | Eylem | Microsoft. COMPUTE/galeriler/resimler/sürümler/okuma | Galeri görüntüsü sürümünün özelliklerini alır |
> | Eylem | Microsoft. COMPUTE/galeriler/resimler/sürümler/yazma | Yeni bir galeri görüntüsü sürümü oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/galeriler/resimler/yazma | Yeni bir galeri görüntüsü oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/galeriler/okuma | Galerinin özelliklerini alır |
> | Eylem | Microsoft. COMPUTE/galeriler/yazma | Yeni bir galeri oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/hostGroups/Delete | Konak grubunu siler |
> | Eylem | Microsoft. COMPUTE/hostGroups/konaklar/Delete | Konağı siler |
> | Eylem | Microsoft. COMPUTE/hostGroups/konaklar/Read | Bir konağın özelliklerini al |
> | Eylem | Microsoft. COMPUTE/hostGroups/konaklar/Write | Yeni bir konak oluşturur veya var olan bir konağı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/hostGroups/Read | Bir konak grubunun özelliklerini al |
> | Eylem | Microsoft. COMPUTE/hostGroups/Write | Yeni bir konak grubu oluşturur veya var olan bir konak grubunu güncelleştirir |
> | Eylem | Microsoft. COMPUTE/Images/Delete | Görüntüyü siler |
> | Eylem | Microsoft. COMPUTE/Images/Read | Görüntünün özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Images/Write | Yeni bir görüntü oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/konumlar/capsOperations/Read | Zaman uyumsuz bir Caps işleminin durumunu alır |
> | Eylem | Microsoft. COMPUTE/Locations/diskOperations/Read | Zaman uyumsuz disk işleminin durumunu alır |
> | Eylem | Microsoft. COMPUTE/Locations/logAnalytics/getRequestRateByInterval/Action | Kısıtlama tanılamayı azaltmaya yardımcı olmak için zaman aralığına göre toplam istekleri göstermek üzere Günlükler oluşturun. |
> | Eylem | Microsoft. COMPUTE/Locations/logAnalytics/Getkısıtledrequests/Action | ResourceName, OperationName veya uygulanan kısıtlama Ilkesi tarafından gruplanmış kısıtlanmış isteklerin toplamlarını göstermek için Günlükler oluşturun. |
> | Eylem | Microsoft. COMPUTE/konumlar/işlemler/okuma | Zaman uyumsuz bir işlemin durumunu alır |
> | Eylem | Microsoft. COMPUTE/Locations/yayımcılar/ArtifactTypes/teklifler/Read | Platform görüntüsü teklifinin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Locations/yayımcılar/ArtifactTypes/teklifler/SKU/okuma | Platform görüntüsü SKU 'sunun özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Locations/yayımcılar/ArtifactTypes/teklifler/SKU 'lar/sürümler/okuma | Platform görüntüsü sürümünün özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Locations/yayımcılar/ArtifactTypes/türler/okuma | Vmexgeri türünün özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Locations/yayımcılar/ArtifactTypes/türler/sürümler/okuma | Vmexgeri sürümünün özelliklerini al |
> | Eylem | Microsoft. COMPUTE/konumlar/yayımcılar/okuma | Yayımcının özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Locations/runCommands/Read | Konumdaki kullanılabilir çalıştırma komutlarını listeler |
> | Eylem | Microsoft. COMPUTE/Locations/kullanımlar/Read | Bir konumdaki aboneliğin işlem kaynakları için hizmet sınırlarını ve geçerli kullanım miktarlarını alır |
> | Eylem | Microsoft. COMPUTE/konumlar/vmSizes/Read | Bir konumdaki kullanılabilir sanal makine boyutlarını listeler |
> | Eylem | Microsoft. COMPUTE/işlemler/okuma | Microsoft. COMPUTE kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | Eylem | Microsoft. COMPUTE/proximityPlacementGroups/Delete | Yakınlık yerleşimi grubunu siler |
> | Eylem | Microsoft. COMPUTE/proximityPlacementGroups/Read | Bir yakınlık yerleşimi grubunun özelliklerini al |
> | Eylem | Microsoft. COMPUTE/proximityPlacementGroups/Write | Yeni bir yakınlık yerleşimi grubu oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/Register/ACTION | Aboneliği Microsoft. COMPUTE kaynak sağlayıcısına kaydeder |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/Delete | Geri yükleme noktası koleksiyonunu ve içerilen geri yükleme noktalarını siler |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/Read | Geri yükleme noktası koleksiyonunun özelliklerini al |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/restorePoints/Delete | Geri yükleme noktasını siler |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/restorePoints/Read | Geri yükleme noktasının özelliklerini al |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/restorePoints/Retrievesasurin/Action | Blob SAS URI 'leriyle birlikte geri yükleme noktasının özelliklerini al |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/restorePoints/Write | Yeni bir geri yükleme noktası oluşturur |
> | Eylem | Microsoft. COMPUTE/restorePointCollections/Write | Yeni bir geri yükleme noktası koleksiyonu oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/Delete | Sharedvmımage 'ı siler |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/Read | Sharedvmımage 'ın özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/sürümler/Delete | Sharedvmımageversion 'ı silme |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/sürümler/okuma | Sharedvmımageversion özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/sürümler/Çoğalt/eylem | Sharedvmımageversion 'ı hedef bölgelere çoğaltma |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/sürümler/Write | Yeni bir Sharedvmımageversion oluşturun veya var olan bir sürümü güncelleştirin |
> | Eylem | Microsoft. COMPUTE/Sharedvmımages/Write | Yeni bir Sharedvmımage oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/SKU/okuma | Aboneliğiniz için kullanılabilen Microsoft. COMPUTE SKU 'Larının listesini alır |
> | Eylem | Microsoft. COMPUTE/Snapshot/beginGetAccess/Action | Blob erişimi için anlık görüntünün SAS URI 'sini alma |
> | Eylem | Microsoft. COMPUTE/Snapshot/Delete | Anlık görüntüyü silme |
> | Eylem | Microsoft. COMPUTE/Snapshot/endGetAccess/Action | Anlık görüntünün SAS URI 'sini iptal et |
> | Eylem | Microsoft. COMPUTE/Snapshot/Read | Anlık görüntünün özelliklerini al |
> | Eylem | Microsoft. COMPUTE/Snapshot/Write | Yeni bir anlık görüntü oluşturma veya var olanı güncelleştirme |
> | Eylem | Microsoft. COMPUTE/kaydını kaldırma/eylem | Microsoft. COMPUTE kaynak sağlayıcısı ile aboneliğin kaydını siler |
> | Eylem | Microsoft. COMPUTE/virtualMachines/yakala/eylem | Sanal sabit diskleri kopyalayarak sanal makineyi yakalar ve benzer sanal makineler oluşturmak için kullanılabilecek bir şablon oluşturur |
> | Eylem | Microsoft. COMPUTE/virtualMachines/convertToManagedDisks/Action | Sanal makinenin blob tabanlı disklerini yönetilen disklere dönüştürür |
> | Eylem | Microsoft. COMPUTE/virtualMachines/serbest bırakma/eylem | Sanal makineyi güçlendirir ve işlem kaynaklarını serbest bırakır |
> | Eylem | Microsoft. COMPUTE/virtualMachines/Delete | Sanal makineyi siler |
> | Eylem | Microsoft. COMPUTE/virtualMachines/Extensions/Sil | Sanal makine uzantısını siler |
> | Eylem | Microsoft. COMPUTE/virtualMachines/Extensions/okuma | Bir sanal makine uzantısının özelliklerini al |
> | Eylem | Microsoft. COMPUTE/virtualMachines/uzantılar/Write | Yeni bir sanal makine uzantısı oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachines/Genelleştir/eylem | Sanal makinenin durumunu Genelleştirilmiş olarak ayarlar ve sanal makineyi yakalamaya hazırlar |
> | Eylem | Microsoft. COMPUTE/virtualMachines/InstanceView/Read | Sanal makinenin ve kaynaklarının ayrıntılı çalışma zamanı durumunu alır |
> | DataAction | Microsoft. COMPUTE/virtualMachines/LOGIN/Action | Bir sanal makinede normal kullanıcı olarak oturum açın |
> | DataAction | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/Action | Windows yönetici veya Linux kök kullanıcı ayrıcalıklarına sahip bir sanal makinede oturum açma |
> | Eylem | Microsoft. COMPUTE/virtualMachines/performMaintenance/Action | VM üzerinde bakım Işlemini gerçekleştirir. |
> | Eylem | Microsoft. COMPUTE/virtualMachines/powerOff/ACTION | Sanal makineyi kapatır. Sanal makinenin faturalandırılmaya devam edip etmediğini unutmayın. |
> | Eylem | Microsoft. COMPUTE/virtualMachines/okuma | Bir sanal makinenin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/virtualMachines/yeniden dağıtma/eylem | Sanal makineyi yeniden dağıtır |
> | Eylem | Microsoft. COMPUTE/virtualMachines/ReImage/Action | Fark kayıt diski kullanan sanal makineyi yeniden görüntüler. |
> | Eylem | Microsoft. COMPUTE/virtualMachines/yeniden Başlat/eylem | Sanal makineyi yeniden başlatır |
> | Eylem | Microsoft. COMPUTE/virtualMachines/runCommand/Action | Sanal makinede önceden tanımlanmış bir betiği yürütür |
> | Eylem | Microsoft. COMPUTE/virtualMachines/Başlat/eylem | Sanal makineyi başlatır |
> | Eylem | Microsoft. COMPUTE/virtualMachines/vmSizes/Read | Sanal makinenin güncelleştirileceği kullanılabilir boyutları listeler |
> | Eylem | Microsoft. COMPUTE/virtualMachines/Write | Yeni bir sanal makine oluşturur veya var olan bir sanal makineyi güncelleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/ayırmayı kaldırma/eylem | Sanal makine ölçek kümesinin örnekleri için işlem kaynaklarını güçlendirir ve serbest bırakır  |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Delete | Sanal makine ölçek kümesini siler |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Delete/Action | Sanal makine ölçek kümesinin örneklerini siler |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Delete | Sanal makine ölçek kümesi uzantısını siler |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read | Bir sanal makine ölçek kümesi uzantısının özelliklerini alır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write | Yeni bir sanal makine ölçek kümesi uzantısı oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/Action | Bir Service Fabric sanal makine ölçek kümesinin platform güncelleştirme etki alanlarına, takılı bekleyen bir güncelleştirmeyi tamamlayacak şekilde el ile kılavuzluk edin |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/InstanceView/Read | Sanal makine ölçek kümesinin örnek görünümünü alır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/manualUpgrade/Action | Örnekleri, sanal makine ölçek kümesinin en son modeline el ile güncelleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/NetworkInterfaces/Read | Bir sanal makine ölçek kümesinin tüm ağ arabirimlerinin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/osRollingUpgrade/Action | Tüm sanal makine ölçek kümesi örneklerini kullanılabilir en son Platform görüntüsü işletim sistemi sürümüne taşımak için bir sıralı yükseltme başlatır. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/osUpgradeHistory/Read | Bir sanal makine ölçek kümesi için işletim sistemi yükseltmelerinden oluşan geçmişi alır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/performMaintenance/Action | Sanal makine ölçek kümesinin örneklerinde planlı bakım gerçekleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/powerOff/ACTION | Sanal makine ölçek kümesi örneklerinin kuvvetlerini kapatır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/publicIPAddresses/Read | Bir sanal makine ölçek kümesinin tüm genel IP adreslerinin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Read | Bir sanal makine ölçek kümesinin özelliklerini al |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/yeniden dağıtma/eylem | Sanal makine ölçek kümesinin örneklerini yeniden dağıtın |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/ReImage/Action | Sanal makine ölçek kümesinin örneklerini yeniden görüntüler |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Reımageall/ACTION | Bir sanal makine ölçek kümesi örnekleri için tüm diskleri (işletim sistemi diski ve veri diskleri) yeniden görüntüler  |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/restart/Action | Sanal makine ölçek kümesinin örneklerini yeniden başlatır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Rollingyükseltmelerinde/iptal/eylem | Bir sanal makine ölçek kümesinin sıralı yükseltmesini iptal eder |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Rollingyükseltmelerinde/okunan | Bir sanal makine ölçek kümesi için son sıralı yükseltme durumunu al |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Scale/Action | Mevcut bir sanal makine ölçek kümesinin, belirtilen örnek sayısına göre ölçeklendirebildiğini/ölçeklendirdiğini doğrulayın |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/SKU 'lar/okuma | Mevcut bir sanal makine ölçek kümesi için geçerli SKU 'Ları listeler |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/start/Action | Sanal makine ölçek kümesinin örneklerini başlatır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/serbest bırakma/eylem | VM Ölçek kümesindeki bir sanal makinenin işlem kaynaklarını güçlendirir ve serbest bırakır. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Delete | Bir VM Ölçek kümesindeki belirli bir sanal makineyi silin. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/InstanceView/Read | Bir VM Ölçek kümesindeki bir sanal makinenin örnek görünümünü alır. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/NetworkInterfaces/ipConfigurations/Publicıpaddresses/Read | Sanal makine ölçek kümesi kullanılarak oluşturulan genel IP adresinin özelliklerini al. Sanal makine ölçek kümesi, IP yapılandırması başına en çok bir genel IP (özel IP) oluşturabilir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/NetworkInterfaces/ipConfigurations/Read | Sanal makine ölçek kümesi kullanılarak oluşturulan bir ağ arabiriminin bir veya tüm IP yapılandırmalarının özelliklerini alın. IP yapılandırması özel IP 'Leri temsil eder |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/NetworkInterfaces/Read | Sanal makine ölçek kümesi kullanılarak oluşturulan bir sanal makinenin ağ arabirimlerinin bir veya tüm özelliklerini al |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Sanal makine ölçek kümesindeki bir sanal makine örneğinde planlanmış bakım gerçekleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/powerOff/ACTION | Bir VM Ölçek kümesindeki bir sanal makine örneğini güçlendirir. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Read | VM Ölçek kümesindeki bir sanal makinenin özelliklerini alır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/yeniden dağıtma/eylem | Sanal makine ölçek kümesindeki bir sanal makine örneğini yeniden dağıtır |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/ReImage/Action | Sanal makine ölçek kümesindeki bir sanal makine örneğini yeniden görüntüler. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Reımageall/ACTION | Sanal makine ölçek kümesindeki sanal makine örneği için tüm diskleri (işletim sistemi diski ve veri diskleri) yeniden görüntüler. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/restart/Action | Bir VM Ölçek kümesindeki bir sanal makine örneğini yeniden başlatır. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/runCommand/Action | Bir sanal makine ölçek kümesindeki bir sanal makine örneğinde önceden tanımlanmış bir betiği yürütür. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/start/Action | VM Ölçek kümesindeki bir sanal makine örneğini başlatır. |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/Write | Bir VM Ölçek kümesindeki bir sanal makinenin özelliklerini güncelleştirir |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/vmSizes/Read | Sanal makine ölçek kümesinde bir sanal makine oluşturmak veya güncelleştirmek için kullanılabilir boyutları listeleyin |
> | Eylem | Microsoft. COMPUTE/virtualMachineScaleSets/Write | Yeni bir sanal makine ölçek kümesi oluşturur veya mevcut olanı güncelleştirir |

## <a name="microsoftconsumption"></a>Microsoft. tüketim

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. tüketim/bakiye/okuma | Bir yönetim grubu için bir faturalandırma dönemi için kullanım özetini listeleyin. |
> | Eylem | Microsoft. tüketim/bütçe/silme | Bütçeleri bir aboneliğe veya bir yönetim grubuna göre silin. |
> | Eylem | Microsoft. tüketim/bütçeler/okuma | Bütçeleri bir aboneliğe veya bir yönetim grubuna göre listeleyin. |
> | Eylem | Microsoft. tüketim/bütçe/yazma | Bütçeleri bir aboneliğe veya bir yönetim grubuna göre oluşturur ve güncelleştirir. |
> | Eylem | Microsoft. tüketim/ücretler/okuma | Ücretleri listeleyin |
> | Eylem | Microsoft. tüketim/krediler/okuma | Kredileri Listele |
> | Eylem | Microsoft. tüketim/olaylar/okuma | Olayları listeleme |
> | Eylem | Microsoft. tüketim/tahminler/okuma | Tahminleri listeleyin |
> | Eylem | Microsoft. tüketim/lotlar/okuma | Lotlar Listele |
> | Eylem | Microsoft. tüketim/pazar yerleri/okuma | EA ve WebDirect abonelikleri için bir kapsamın Market kaynak kullanımı ayrıntılarını listeleyin. |
> | Eylem | Microsoft. tüketim/operationresults/Read | Operationresults listeleme |
> | Eylem | Microsoft. tüketim/işlemler/okuma | Microsoft. tüketim kaynak sağlayıcısı tarafından desteklenen tüm işlemleri listeleyin. |
> | Eylem | Microsoft. tüketim/OperationStatus/Read | OperationStatus listeleme |
> | Eylem | Microsoft. tüketim/pricesheets/Read | Bir abonelik veya bir yönetim grubu için Pricesheets verilerini listeleyin. |
> | Eylem | Microsoft. tüketim/Register/ACTION | Tüketim RP 'ye kaydol |
> | Eylem | Microsoft. tüketim/Rezervationdetails/okuma | Ayrılmış örnekler için ayırma sırasına veya yönetim gruplarına göre kullanım ayrıntılarını listeleyin. Ayrıntı verileri günlük düzeyi başına örnek başına. |
> | Eylem | Microsoft. tüketim/Rezervationönerilere/okuma | Bir abonelik için ayrılmış örnekler için tek veya paylaşılan öneriler listeleyin. |
> | Eylem | Microsoft. tüketim/Rezervationözetler/okuma | Ayrılmış örnekler için kullanım özetini rezervasyon siparişi veya yönetim gruplarına göre listeleyin. Özet verileri aylık ya da günlük düzeyindedir. |
> | Eylem | Microsoft. tüketim/Rezervationtransactions/Read | Ayrılmış örnekler için yönetim gruplarına göre işlem geçmişini listeleyin. |
> | Eylem | Microsoft. tüketim/Etiketler/okuma | EA ve abonelikler için Etiketler listeleyin. |
> | Eylem | Microsoft. tüketim/kiracılar/okuma | Kiracılar Listele |
> | Eylem | Microsoft. tüketim/kiracılar/yazmaç/eylem | Bir kiracının Microsoft. tüketim kapsamı için kaydetme eylemi. |
> | Eylem | Microsoft. tüketim/hüküm/okuma | Bir abonelik veya bir yönetim grubu için koşulları listeleyin. |
> | Eylem | Microsoft. tüketim/usageDetails/okuma | EA ve WebDirect abonelikleri için bir kapsamın kullanım ayrıntılarını listeleyin. |

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Containerınstance/containerGroups/kapsayıcılar/exec/Action | Belirli bir kapsayıcıya çalıştırılabilir. |
> | Eylem | Microsoft. Containerınstance/containerGroups/kapsayıcılar/Günlükler/okuma | Belirli bir kapsayıcı için günlükleri al. |
> | Eylem | Microsoft. Containerınstance/containerGroups/Delete | Belirli kapsayıcı grubunu silin. |
> | Eylem | Microsoft. Containerınstance/containerGroups/operationResults/Read | Zaman uyumsuz işlem sonucunu al |
> | Eylem | Microsoft. Containerınstance/containerGroups/Providers/Microsoft. Insights/diagnosticSettings/Read | Kapsayıcı grubu için tanılama ayarını alır. |
> | Eylem | Microsoft. Containerınstance/containerGroups/Providers/Microsoft. Insights/diagnosticSettings/Write | Kapsayıcı grubu için tanılama ayarını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Containerınstance/containerGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Kapsayıcı grubu için kullanılabilir ölçümleri alır. |
> | Eylem | Microsoft. Containerınstance/containerGroups/Read | Tüm kapsayıcı gruplarını al. |
> | Eylem | Microsoft. Containerınstance/containerGroups/restart/Action | Belirli bir kapsayıcı grubunu yeniden başlatır. |
> | Eylem | Microsoft. Containerınstance/containerGroups/start/Action | Belirli bir kapsayıcı grubunu başlatır. |
> | Eylem | Microsoft. Containerınstance/containerGroups/durdur/eylem | Belirli bir kapsayıcı grubunu sonlandırır. İşlem kaynakları serbest bırakılır ve faturalandırma durdurulur. |
> | Eylem | Microsoft. Containerınstance/containerGroups/Write | Belirli bir kapsayıcı grubunu oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Containerınstance/Locations/Önbellekedimages/Read | Bir bölgedeki aboneliğin önbelleğe alınmış görüntülerini alır. |
> | Eylem | Microsoft. Containerınstance/Locations/Capabilities/Read | Bir bölgenin yeteneklerini alın. |
> | Eylem | Microsoft. Containerınstance/Locations/Deletevirtualnetworkoralt ağları/eylem | , Sanal ağın veya alt ağın silinmekte olduğunu Microsoft. Containerınstance 'a bildirir. |
> | Eylem | Microsoft. Containerınstance/Locations/işlemler/okuma | Azure Container Instance hizmeti için işlemleri listeleyin. |
> | Eylem | Microsoft. Containerınstance/Locations/kullanımlar/Read | Belirli bir bölgenin kullanımını alın. |
> | Eylem | Microsoft. Containerınstance/işlemler/okuma | Azure Container Instance hizmeti için işlemleri listeleyin. |
> | Eylem | Microsoft. Containerınstance/Register/ACTION | Kapsayıcı örneği kaynak sağlayıcısı için aboneliği kaydeder ve kapsayıcı gruplarının oluşturulmasına izin vermez. |
> | Eylem | Microsoft. Containerınstance/serviceassociationlinks/Delete | Azure Container Instance kaynak sağlayıcısı tarafından bir alt ağda oluşturulan hizmet ilişkisi bağlantısını silin. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ContainerRegistry/Checknameavaılabılıty/Read | Kapsayıcı kayıt defteri adının kullanıma hazır olup olmadığını denetler. |
> | Eylem | Microsoft. ContainerRegistry/Locations/Deletevirtualnetworkoralt ağları/eylem | Sanal ağ veya alt ağın silindiği Microsoft. ContainerRegistry öğesine bildirir |
> | Eylem | Microsoft. ContainerRegistry/konumlar/operationResults/Read | Zaman uyumsuz işlem sonucunu alır |
> | Eylem | Microsoft. ContainerRegistry/işlemler/okuma | Tüm kullanılabilir Azure Container Registry REST API işlemlerini listeler |
> | Eylem | Microsoft. ContainerRegistry/Register/ACTION | Kapsayıcı kayıt defteri kaynak sağlayıcısı için aboneliği kaydeder ve kapsayıcı kayıt defterlerinin oluşturulmasını sunar. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/yapıtlar/silme | Bir kapsayıcı kayıt defterinde yapıtı silin. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/derlemeler/iptal/eylem | Var olan bir derlemeyi iptal eder. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/derlemeler/getLogLink/Action | Derleme günlüklerini indirmek için bir bağlantı alır. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/derlemeler/okuma | Belirtilen derleme özelliklerini alır veya belirtilen kapsayıcı kayıt defteri için tüm derlemeleri listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/derlemeler/yazma | Bir kapsayıcı kayıt defteri için belirtilen parametrelerle bir derlemeyi güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Delete | Bir kapsayıcı kayıt defterinden derleme görevini siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/listSourceRepositoryProperties/Action | Yapı görevinin kaynak denetim özelliklerini listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Read | Belirtilen derleme görevinin özelliklerini alır veya belirtilen kapsayıcı kayıt defteri için tüm derleme görevlerini listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Steps/Delete | Derleme görevinin yapı adımını siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Steps/listBuildArguments/ACTION | Gizli bağımsız değişkenler de dahil olmak üzere derleme adımının derleme bağımsız değişkenlerini listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Steps/Read | Belirtilen derleme adımının özelliklerini alır veya belirtilen derleme görevinin tüm derleme adımlarını listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Steps/Write | Belirtilen parametrelerle derleme görevi için derleme adımı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/buildTasks/Write | Belirtilen parametrelerle bir kapsayıcı kayıt defteri için derleme görevi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/silme | Bir kapsayıcı kayıt defterini siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/eventGridFilters/Delete | Bir kapsayıcı kayıt defterinden bir olay Kılavuzu filtresini siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/eventGridFilters/Read | Belirtilen olay Kılavuzu filtresinin özelliklerini alır veya belirtilen kapsayıcı kayıt defteri için tüm olay Kılavuzu filtrelerini listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/eventGridFilters/Write | Belirtilen parametrelerle bir kapsayıcı kayıt defteri için bir olay kılavuz filtresi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/getBuildSourceUploadUrl/Action | Kullanıcının kaynağı yükleyebilmesi için karşıya yükleme konumunu alır. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/ımportımage/eylem | Görüntüyü, belirtilen parametrelerle kapsayıcı kayıt defterine aktarın. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/listBuildSourceUploadUrl/Action | Bir kapsayıcı kayıt defteri için kaynak yükleme URL 'si konumunu al. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/listCredentials/Action | Belirtilen kapsayıcı kayıt defteri için oturum açma kimlik bilgilerini listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/listPolicies/Read | Belirtilen kapsayıcı kayıt defteri için ilkeleri listeler |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Listkullanımlar/okuma | Belirtilen kapsayıcı kayıt defteri için kota kullanımlarını listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/meta veriler/okuma | Bir kapsayıcı kayıt defteri için belirli bir deponun meta verilerini alır |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/meta veri/yazma | Bir kapsayıcı kayıt defteri için bir deponun meta verilerini güncelleştirir |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Operationdurumlarının/Read | Kayıt defteri zaman uyumsuz işlem durumunu alır |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çekme/okuma | Bir kapsayıcı kayıt defterinden görüntüleri çekin veya alın. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/gönderme/yazma | Bir kapsayıcı kayıt defterine görüntü gönderin veya yazın. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Quarantıneread/okuma | Kapsayıcı kayıt defterinden karantinaya alınmış görüntüleri çekme veya alma |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/quarantineWrite/Write | Karantinaya alınan görüntülerin karantina durumunu yazma/değiştirme |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/queueBuild/Action | İstek parametrelerine göre yeni bir yapı oluşturur ve yapı kuyruğuna ekler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/okuma | Belirtilen kapsayıcı kayıt defterinin özelliklerini alır veya belirtilen kaynak grubu veya abonelik altındaki tüm kapsayıcı kayıt defterlerinin listesini görüntüler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/regenerateCredential/ACTION | Belirtilen kapsayıcı kayıt defteri için oturum açma kimlik bilgilerinden birini yeniden oluşturur. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çoğaltmalar/silme | Bir kapsayıcı kayıt defterinden bir çoğaltmayı siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çoğaltmalar/Operationdurumlarının/Read | Çoğaltma zaman uyumsuz işlem durumunu alır |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çoğaltmalar/okuma | Belirtilen çoğaltmanın özelliklerini alır veya belirtilen kapsayıcı kayıt defteri için tüm çoğaltmaları listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çoğaltmalar/yazma | Belirtilen parametrelerle bir kapsayıcı kayıt defteri için çoğaltma oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çalıştırmalar/iptal/eylem | Mevcut bir çalıştırmayı iptal edin. |
> | Eylem | Microsoft. ContainerRegistry/Registry/çalıştırmaları/listLogSasUrl/Action | Bir çalıştırmanın günlük SAS URL 'sini alır. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çalıştırmalar/Read | Bir kapsayıcı kayıt defterinde veya liste çalıştırmalarından bir çalıştırmanın özelliklerini alır. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/çalıştırmalar/Write | Bir çalıştırmayı güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/scheduleRun/Action | Bir kapsayıcı kayıt defterine karşı bir çalıştırma zamanlayın. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/imzala/yaz | Kapsayıcı kayıt defteri için gönderme/çekme içeriği güven meta verileri. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/görevler/silme | Bir kapsayıcı kayıt defteri için bir görevi siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/görevler/listDetails/Action | Bir kapsayıcı kayıt defteri için bir görevin tüm ayrıntılarını listeleyin. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/görevler/okuma | Bir kapsayıcı kayıt defteri için bir görev alır veya tüm görevleri listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/görevler/yazma | Bir kapsayıcı kayıt defteri için bir görev oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/updatePolicies/Write | Belirtilen kapsayıcı kayıt defteri için ilkeleri güncelleştirir |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/silme | Bir Web kancasını kapsayıcı kayıt defterinden siler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/getCallbackConfig/Action | Web kancası için hizmet URI 'SI ve özel üst bilgilerin yapılandırmasını alır. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/listEvents/eylem | Belirtilen Web kancası için son olayları listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/Operationdurumlarının/Read | Web kancası zaman uyumsuz işlem durumunu alır |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/ping/eylem | Web kancasına gönderilecek bir ping olayını tetikler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/okuma | Belirtilen kapsayıcı kayıt defteri için belirtilen Web kancasının özelliklerini alır veya tüm Web kancalarını listeler. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları/yazma | Belirtilen parametrelerle bir kapsayıcı kayıt defteri için Web kancası oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ContainerRegistry/kayıt defterleri/yazma | Belirtilen parametrelerle bir kapsayıcı kayıt defteri oluşturur veya güncelleştirir. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ContainerService/containerServices/Delete | Bir kapsayıcı hizmetini siler |
> | Eylem | Microsoft. ContainerService/containerServices/Read | Kapsayıcı hizmeti al |
> | Eylem | Microsoft. ContainerService/containerServices/Write | Yeni bir kapsayıcı hizmeti oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. ContainerService/konumlar/operationresults/Read | Zaman uyumsuz bir işlem sonucunun durumunu alır |
> | Eylem | Microsoft. ContainerService/konumlar/işlemler/okuma | Zaman uyumsuz bir işlemin durumunu alır |
> | Eylem | Microsoft. ContainerService/konumlar/orchestrators/okunan | Desteklenen düzenleyiciler listeler |
> | Eylem | Microsoft. ContainerService/Managedkümeler/accessProfiles/listCredential/Action | Liste kimlik bilgisini kullanarak rol adına göre yönetilen küme erişim profili al |
> | Eylem | Microsoft. ContainerService/Managedkümeler/accessProfiles/okuma | Rol adına göre yönetilen küme erişim profili al |
> | Eylem | Microsoft. ContainerService/Managedkümeler/agentPools/Delete | Bir aracı havuzunu siler |
> | Eylem | Microsoft. ContainerService/Managedkümeler/agentPools/okuma | Bir aracı havuzu alır |
> | Eylem | Microsoft. ContainerService/Managedkümeler/agentPools/upgradeProfiles/okuma | Aracı havuzunun yükseltme profilini alır |
> | Eylem | Microsoft. ContainerService/Managedkümeler/agentPools/Write | Yeni bir aracı havuzu oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. ContainerService/Managedkümeler/Sil | Yönetilen bir kümeyi siler |
> | Eylem | Microsoft. ContainerService/Managedkümeler/detektorları/okuma | Yönetilen küme algılayıcısı 'nı al |
> | Eylem | Microsoft. ContainerService/Managedkümeler/diagnosticsState/Read | Kümenin tanılama durumunu alır |
> | Eylem | Microsoft. ContainerService/Managedkümeler/listClusterAdminCredential/ACTION | Yönetilen kümenin clusterAdmin kimlik bilgisini listeleyin |
> | Eylem | Microsoft. ContainerService/Managedkümeler/listClusterUserCredential/Action | Yönetilen kümenin clusterUser kimlik bilgisini listeleyin |
> | Eylem | Microsoft. ContainerService/Managedkümeler/privateEndpointConnectionsApproval/eylem | Kullanıcının özel bir uç nokta bağlantısını onaylamasını izin verilip verilmeyeceğini belirler |
> | Eylem | Microsoft. ContainerService/Managedkümeler/okuma | Yönetilen bir küme al |
> | Eylem | Microsoft. ContainerService/Managedkümeler/resetAADProfile/Action | Yönetilen kümenin AAD profilini sıfırlayın |
> | Eylem | Microsoft. ContainerService/Managedkümeler/resetServicePrincipalProfile/Action | Yönetilen kümenin hizmet sorumlusu profilini sıfırlayın |
> | Eylem | Microsoft. ContainerService/Managedkümeler/upgradeProfiles/okuma | Kümenin yükseltme profilini alır |
> | Eylem | Microsoft. ContainerService/Managedkümeler/Write | Yeni bir yönetilen küme oluşturur veya var olan bir kümeyi güncelleştirir |
> | Eylem | Microsoft. ContainerService/openShiftClusters/Delete | Açık bir kaydırma kümesini silme |
> | Eylem | Microsoft. ContainerService/openShiftClusters/Read | Açık bir Shift kümesi al |
> | Eylem | Microsoft. ContainerService/openShiftClusters/Write | Yeni bir açık vardiya kümesi oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. ContainerService/openShiftManagedClusters/Delete | Açık bir vardiya yönetilen kümesini silme |
> | Eylem | Microsoft. ContainerService/openShiftManagedClusters/Read | Açık bir vardiya yönetilen kümesi al |
> | Eylem | Microsoft. ContainerService/openShiftManagedClusters/Write | Yeni bir açık vardiya yönetilen kümesi oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. ContainerService/işlemler/okuma | Microsoft. ContainerService kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | Eylem | Microsoft. ContainerService/Register/Action | Aboneliği Microsoft. ContainerService kaynak sağlayıcısına kaydeder |
> | Eylem | Microsoft. ContainerService/Unregister/eylem | Microsoft. ContainerService kaynak sağlayıcısı ile aboneliğin kaydını siler |

## <a name="microsoftcontentmoderator"></a>Microsoft. Contentmoderatör

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Contentmoderatör/uygulamalar/Sil | Silme Işlemi |
> | Eylem | Microsoft. Contentmoderatör/uygulamalar/Listgizlilikler/Action | Gizli dizileri Listele |
> | Eylem | Microsoft. Contentmoderatör/uygulamalar/listSingleSignOnToken/Action | Çoklu oturum açma belirteçlerini oku |
> | Eylem | Microsoft. Contentmoderatör/uygulamalar/okuma | Okuma Işlemi |
> | Eylem | Microsoft. Contentmoderatör/uygulamalar/yazma | Yazma Işlemi |
> | Eylem | Microsoft. Contentmoderatör/uygulamalar/yazma | Yazma Işlemi |
> | Eylem | Microsoft. Contentmoderatör/listCommunicationPreference/eylem | İletişim tercihini Listele |
> | Eylem | Microsoft. Contentmoderatör/işlemler/okuma | okuma işlemleri |
> | Eylem | Microsoft. Contentmoderatör/updateCommunicationPreference/eylem | İletişim tercihini Güncelleştir |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. CostManagement/Cloudbağlayıcılar/Delete | Belirtilen cloudConnector 'ı silin. |
> | Eylem | Microsoft. CostManagement/Cloudkonnektörleri/okuma | Kimliği doğrulanmış kullanıcı için Cloudbağlayıcıları listeleyin. |
> | Eylem | Microsoft. CostManagement/Cloudbağlayıcılar/Write | Belirtilen cloudConnector 'ı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. CostManagement/Dimensions/Read | Desteklenen tüm boyutları bir kapsama göre listeleyin. |
> | Eylem | Microsoft. CostManagement/dışarı aktarmalar/eylem | Belirtilen dışarı aktarmayı çalıştırın. |
> | Eylem | Microsoft. CostManagement/dışarı aktarmalar/Sil | Belirtilen dışarı aktarmayı silin. |
> | Eylem | Microsoft. CostManagement/dışarı aktarmalar/okundu | Dışarı aktarmaları kapsama göre listeleyin. |
> | Eylem | Microsoft. CostManagement/dışarı aktarmalar/Çalıştır/eylem | Dışarı aktarmaları Çalıştır. |
> | Eylem | Microsoft. CostManagement/dışarı aktarmalar/yaz | Belirtilen dışarı aktarmayı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. CostManagement/externalBillingAccounts/Externalabonelikleri/okuma | Kimliği doğrulanmış kullanıcı için externalBillingAccount içindeki Externalaboneliklerinizi listeleyin. |
> | Eylem | Microsoft. CostManagement/externalBillingAccounts/Read | Kimliği doğrulanmış kullanıcı için externalBillingAccounts listesini listeleyin. |
> | Eylem | Microsoft. CostManagement/Externalabonelikleri/okuma | Kimliği doğrulanmış kullanıcı için Externalaboneliklerinizi listeleyin. |
> | Eylem | Microsoft. CostManagement/Externalabonelikleri/yazma | ExternalSubscription ilişkili yönetim grubunu Güncelleştir |
> | Eylem | Microsoft. CostManagement/Query/Action | Kullanım verilerini bir kapsama göre sorgulama. |
> | Eylem | Microsoft. CostManagement/Query/Read | Kullanım verilerini bir kapsama göre sorgulama. |
> | Eylem | Microsoft. CostManagement/Register/ACTION | Abonelik ile Microsoft. CostManagement kapsamı için kaydetme eylemi. |
> | Eylem | Microsoft. CostManagement/Reports/Action | Kullanım verileri hakkında raporları bir kapsama göre zamanlayın. |
> | Eylem | Microsoft. CostManagement/Reports/Read | Kullanım verileri hakkında raporları bir kapsama göre zamanlayın. |
> | Eylem | Microsoft. CostManagement/kiracılar/Register/Action | Bir kiracının Microsoft. CostManagement kapsamı için kaydetme eylemi. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataBox/Jobs/bookShipmentPickUp/ACTION | İade sevk irsaliyeleri için bir çekme defterine izin verir. |
> | Eylem | Microsoft. DataBox/Jobs/iptal/eylem | Devam eden bir siparişi iptal eder. |
> | Eylem | Microsoft. DataBox/işler/Sil | Siparişleri silme |
> | Eylem | Microsoft. DataBox/Jobs/listCredentials/Action | Siparişle ilgili şifrelenmemiş kimlik bilgilerini listeler. |
> | Eylem | Microsoft. DataBox/Jobs/Read | Siparişleri listeleyin veya alın |
> | Eylem | Microsoft. DataBox/işler/yazma | Siparişleri oluşturma veya güncelleştirme |
> | Eylem | Microsoft. DataBox/Locations/Availablesku 'Lar/eylem | Bu yöntem, kullanılabilir SKU 'ların listesini döndürür. |
> | Eylem | Microsoft. DataBox/Locations/Availablesku 'Lar/okuma | Kullanılabilir SKU 'Ları listeleme veya edinme |
> | Eylem | Microsoft. DataBox/Locations/operationResults/Read | Işlem sonuçlarını listeleme veya edinme |
> | Eylem | Microsoft. DataBox/Locations/regionConfiguration/Action | Bu yöntem, bölgenin yapılandırmasını döndürür. |
> | Eylem | Microsoft. DataBox/Locations/validateAddress/Action | Sevkiyat adresini doğrular ve varsa alternatif adresler sağlar. |
> | Eylem | Microsoft. DataBox/Locations/Validategirişlerinde/Action | Bu yöntem, tüm doğrulama türlerini yapar. |
> | Eylem | Microsoft. DataBox/işlemler/okuma | Işlemleri listeleme veya edinme |
> | Eylem | Microsoft. DataBox/Register/Action | Sağlayıcıyı kaydet Microsoft. databox |
> | Eylem | Microsoft. DataBox/abonelikler/resourceGroups/Moveresonak/Action | Bu yöntem, kaynak taşımayı gerçekleştirir. |
> | Eylem | Microsoft. DataBox/abonelikler/resourceGroups/Validatemoveresonak/Action | Bu yöntem, kaynak taşımaya izin verilip verilmeyeceğini doğrular. |
> | Eylem | Microsoft. DataBox/kaydını kaldırma/eylem | Kaydettirme sağlayıcısını Kaldır Microsoft. databox |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uyarılar/okuma | Uyarıları listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uyarılar/okuma | Uyarıları listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Bant genişliği zamanlamalarını siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Bant genişliği zamanlamalarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Bant genişliği zamanlamalarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | Bant genişliği zamanlamalarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Delete | Data Box Edge cihazlarını siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/Action | Güncelleştirmeleri cihaza indir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Gebir Dedınformation/Action | Kaynak genişletilmiş bilgilerini alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/ınstallupdates/Action | Güncelleştirmeleri cihaza yükler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/işler/okuma | İşleri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/networkSettings/Read | Cihaz ağ ayarlarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Nodes/Read | Düğümleri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | İşlem durumunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/siparişler/Sil | Siparişleri siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/siparişler/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/siparişler/okuma | Siparişleri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/siparişler/okuma | Siparişleri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/siparişler/yazma | Siparişleri oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Data Box Edge cihazlarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Data Box Edge cihazlarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Data Box Edge cihazlarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roller/Delete | Rolleri siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roller/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roller/okuma | Rolleri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roller/okuma | Rolleri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roller/Write | Rolleri oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/Action | Güncelleştirmeleri Tara |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/Update/Action | Güvenlik ayarlarını güncelleştirme |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/paylaşımlar/silme | Paylaşımları siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/paylaşımlar/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/paylaşımlar/okuma | Paylaşımları listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/paylaşımlar/okuma | Paylaşımları listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/paylaşımlar/Yenile/eylem | Bulutta bulunan verilerle paylaşma meta verilerini yenileyin |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/paylaşımlar/yazma | Paylaşımları oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Delete | Depolama hesabı kimlik bilgilerini siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Depolama hesabı kimlik bilgilerini listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Depolama hesabı kimlik bilgilerini listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | Depolama hesabı kimlik bilgilerini oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Tetikleyiciler/silme | Tetikleyicileri siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Tetikleyiciler/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Tetikleyiciler/okuma | Tetikleyicileri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Tetikleyiciler/okuma | Tetikleyicileri listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Tetikleyiciler/yazma | Tetikleyicileri oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary/Read | Güncelleştirme özetini listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/Action | Cihaz kaydı için sertifikayı karşıya yükle |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/kullanıcılar/Sil | Paylaşma kullanıcılarını siler |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/kullanıcılar/operationResults/Read | İşlem sonucunu listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/kullanıcılar/okuma | Paylaşma kullanıcılarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/kullanıcılar/okuma | Paylaşma kullanıcılarını listeler veya alır |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/kullanıcılar/yazma | Paylaşma kullanıcıları oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Data Box Edge cihazlarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Data Box Edge cihazlarını oluşturur veya güncelleştirir |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Databricks/Locations/getNetworkPolicies/Action | NRP tarafından kullanılan konuma göre bir alt ağ için ağ hedefi Ilkelerini al |
> | Eylem | Microsoft. Databricks/Register/ACTION | Databricks 'e kaydolun. |
> | Eylem | Microsoft. Databricks/Workspaces/Delete | Databricks çalışma alanını kaldırır. |
> | Eylem | Microsoft. Databricks/Workspaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Databricks çalışma alanı için kullanılabilir tanılama ayarlarını ayarlar |
> | Eylem | Microsoft. Databricks/Workspaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. Databricks/Workspaces/Providers/Microsoft. Insights/logDefinitions/Read | Databricks çalışma alanı için kullanılabilir günlük tanımlarını alır |
> | Eylem | Microsoft. Databricks/Workspaces/Read | Databricks çalışma alanlarının bir listesini alır. |
> | Eylem | Microsoft. Databricks/Workspaces/Write | Bir Databricks çalışma alanı oluşturur. |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataCatalog/Catalogs/Delete | Veri Kataloğu kaynak sağlayıcısı için Katalog kaynağını silin. |
> | Eylem | Microsoft. DataCatalog/Catalogs/Read | Veri Kataloğu kaynak sağlayıcısı için Katalog kaynağını okuyun. |
> | Eylem | Microsoft. DataCatalog/kataloglar/Write | Veri Kataloğu kaynak sağlayıcısı için Katalog kaynağı yazın. |
> | Eylem | Microsoft. DataCatalog/Checknameavaılabılıty/Read | Veri Kataloğu kaynak sağlayıcısı için katalog adı kullanılabilirliğini denetleyin. |
> | Eylem | Microsoft. DataCatalog/datacatalogs/Delete | Veri Kataloğu kaynak sağlayıcısı için DataCatalog kaynağını silin. |
> | Eylem | Microsoft. DataCatalog/datacatalogs/Read | Veri Kataloğu kaynak sağlayıcısı için DataCatalog kaynağını okuyun. |
> | Eylem | Microsoft. DataCatalog/datacatalogs/Write | Veri Kataloğu kaynak sağlayıcısı için DataCatalog kaynağını yazın. |
> | Eylem | Microsoft. DataCatalog/Operations/Read | Veri Kataloğu kaynak sağlayıcısındaki tüm kullanılabilir işlemleri okur. |
> | Eylem | Microsoft. DataCatalog/Register/ACTION | Veri Kataloğu kaynak sağlayıcısı için aboneliği kaydetme |
> | Eylem | Microsoft. DataCatalog/Unregister/ACTION | Veri Kataloğu kaynak sağlayıcısı aboneliğini silme |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataFactory/checkazuredatafactorynameavailability/Read | Data Factory adının kullanılabilir olup olmadığını denetler. |
> | Eylem | Microsoft. DataFactory/DataFactory/activitywindows/Read | Belirtilen parametrelerle Data Factory etkinlik pencerelerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/Activities/activitywindows/Read | Belirtilen parametrelerle işlem hattı etkinliği için etkinlik pencerelerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/activitywindows/Read | Belirtilen parametrelere sahip işlem hattı için etkinlik pencerelerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/Delete | Herhangi bir işlem hattını siler. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarını al/Duraklat/eylem | Tüm işlem hatlarını duraklatır. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/Read | Herhangi bir işlem hattını okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/CV/Action | Tüm işlem hatlarını sürdürür. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/Update/Action | Tüm işlem hatlarını güncelleştirir. |
> | Eylem | Microsoft. DataFactory/DataFactory/datahatlarının/Write | Herhangi bir işlem hattı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/DataFactory/DataSet/activitywindows/Read | Belirtilen parametrelere sahip veri kümesi için etkinlik pencerelerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/veri kümeleri/silme | Herhangi bir veri kümesini siler. |
> | Eylem | Microsoft. DataFactory/DataFactory/veri kümeleri/okuma | Tüm veri kümelerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/DataSet/dilimleyicerbir/Read | Verilen veri kümesi için verilen başlangıç zamanına sahip veri dilimi çalıştırmasını okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/veri kümeleri/dilimler/okuma | Verilen dönemdeki veri dilimlerini alır. |
> | Eylem | Microsoft. DataFactory/DataFactory/veri kümeleri/dilimler/Write | Veri diliminin durumunu güncelleştirin. |
> | Eylem | Microsoft. DataFactory/DataFactory/veri kümeleri/yazma | Herhangi bir veri kümesini oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/DataFactory/Delete | Data Factory siler. |
> | Eylem | Microsoft. DataFactory/DataFactory/Gateway/ConnectionInfo/Action | Herhangi bir ağ geçidi için bağlantı bilgilerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/Gateway/Delete | Tüm ağ geçidini siler. |
> | Eylem | Microsoft. DataFactory/DataFactory/Gateway/listauthkeys/Action | Herhangi bir ağ geçidinin kimlik doğrulama anahtarlarını listeler. |
> | Eylem | Microsoft. DataFactory/DataFactory/Gateway/Read | Herhangi bir ağ geçidini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/Gateway/regenerateauthkey/Action | Herhangi bir ağ geçidinin kimlik doğrulama anahtarlarını yeniden oluşturur. |
> | Eylem | Microsoft. DataFactory/DataFactory/Gateway/Write | Herhangi bir ağ geçidini oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/DataFactory/linkedServices/Delete | Bağlı hizmeti siler. |
> | Eylem | Microsoft. DataFactory/DataFactory/linkedServices/Read | Herhangi bir bağlı hizmeti okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/linkedServices/Write | Herhangi bir bağlı hizmeti oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/DataFactory/okuma | Data Factory okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/çalıştırmalar/LogInfo/Read | Günlükleri içeren bir blob kapsayıcısına SAS URI 'sini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/tablolar/Sil | Herhangi bir veri kümesini siler. |
> | Eylem | Microsoft. DataFactory/DataFactory/tablolar/okuma | Tüm veri kümelerini okur. |
> | Eylem | Microsoft. DataFactory/DataFactory/Tables/Write | Herhangi bir veri kümesini oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/DataFactory/Write | Data Factory oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/Factory/cancelardışık düzen eylemsizlik/eylem | Çalıştırma KIMLIĞI tarafından belirtilen işlem hattı çalıştırmasını iptal eder. |
> | Eylem | Microsoft. DataFactory/Factory/Cancelsandboxardışık düzen eylemsizlik/eylem | İşlem hattı için bir hata ayıklama çalıştırmasını iptal eder. |
> | Eylem | Microsoft. DataFactory/Factory/createdataflowdebugsession/Action | Bir veri akışı hata ayıklama oturumu oluşturur. |
> | Eylem | Microsoft. DataFactory/Factory/dataakışlar/Delete | Veri akışını siler. |
> | Eylem | Microsoft. DataFactory/Factory/dataakışlar/okuma | Veri akışını okur. |
> | Eylem | Microsoft. DataFactory/Factory/dataakışlar/Write | Veri akışı oluştur veya güncelleştir |
> | Eylem | Microsoft. DataFactory/Factory/veri kümeleri/silme | Herhangi bir veri kümesini siler. |
> | Eylem | Microsoft. DataFactory/Factory/veri kümeleri/okuma | Tüm veri kümelerini okur. |
> | Eylem | Microsoft. DataFactory/Factory/veri kümeleri/yazma | Herhangi bir veri kümesini oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/Factory/debugardışık düzen eylemsizlik/iptal/eylem | İşlem hattı için bir hata ayıklama çalıştırmasını iptal eder. |
> | Eylem | Microsoft. DataFactory/Factory/Delete | Data Factory siler. |
> | Eylem | Microsoft. DataFactory/Factory/deletedataflowdebugsession/Action | Bir veri akışı hata ayıklama oturumunu siler. |
> | Eylem | Microsoft. DataFactory/Factory/getDataPlaneAccess/Action | ADF Datadüzlemi hizmetine erişimi alır. |
> | Eylem | Microsoft. DataFactory/Factory/getDataPlaneAccess/Read | ADF Datadüzlemi hizmetine erişimi okur. |
> | Eylem | Microsoft. DataFactory/Factory/getFeatureValue/Action | Belirli bir konum için pozlama denetim özelliği değeri alın. |
> | Eylem | Microsoft. DataFactory/Factory/getFeatureValue/Read | Belirli bir konum için pozlama denetimi özellik değerini okur. |
> | Eylem | Microsoft. DataFactory/Factory/getGitHubAccessToken/eylem | GitHub erişim belirtecini alır. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/silme | Tüm Integration Runtime siler. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationsms/getconnectionınfo/Read | Integration Runtime bağlantı bilgilerini okur. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/getObjectMetadata/eylem | Belirtilen Integration Runtime için SSIS Integration Runtime meta verilerini alın. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/GetStatus/Read | Integration Runtime durumunu okur. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/linkedIntegrationRuntime/Action | Belirtilen paylaşılan Integration Runtime bağlı Integration Runtime başvurusu oluştur. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/listauthkeys/Read | Herhangi bir Integration Runtime için kimlik doğrulama anahtarlarını listeler. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/monitoringdata/Read | Integration Runtime için Izleme verilerini alır. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/düğümleri/silme | Belirtilen Integration Runtime düğümünü siler. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/düğümler/IPAddress/Action | Integration Runtime belirtilen düğümü için IP adresini döndürür. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/düğümleri/okuma | Belirtilen Integration Runtime için düğümü okur. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/düğümleri/yazma | Şirket içinde barındırılan Integration Runtime düğümünü güncelleştirir. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/okuma | Integration Runtime okur. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/refreshObjectMetadata/Action | Belirtilen Integration Runtime için SSIS Integration Runtime meta verilerini yenile. |
> | Eylem | Microsoft. DataFactory/Factory/Integration, çalışma zamanları/regenerateauthkey/Action | Belirtilen Integration Runtime için kimlik doğrulama anahtarlarını yeniden oluşturur. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/removelmürekkepler/eylem | Belirtilen Integration Runtime bağlı Integration Runtime başvurularını kaldırır. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/Başlat/eylem | Integration Runtime başlatır. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/durdur/eylem | Integration Runtime sonlandırır. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/synccredentials/Action | Belirtilen Integration Runtime ait kimlik bilgilerini eşitler. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/yükseltme/eylem | Belirtilen Integration Runtime yükseltir. |
> | Eylem | Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları/yazma | Integration Runtime oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/Factory/linkedServices/Delete | Bağlı hizmeti siler. |
> | Eylem | Microsoft. DataFactory/Factory/linkedServices/Read | Bağlı hizmeti okur. |
> | Eylem | Microsoft. DataFactory/Factory/linkedServices/Write | Bağlı hizmet oluştur veya güncelleştir |
> | Eylem | Microsoft. DataFactory/Factory/ardışık düzen eylemsizlik/activityçalıştırmaları/okuma | Belirtilen işlem hattı çalıştırma KIMLIĞI için etkinlik çalıştırmalarını okur. |
> | Eylem | Microsoft. DataFactory/Factory/ardışık düzen eylemsizlik/iptal/eylem | Çalıştırma KIMLIĞI tarafından belirtilen işlem hattı çalıştırmasını iptal eder. |
> | Eylem | Microsoft. DataFactory/Factory/ardışık düzen eylemsizlik/queryactivityçalıştırmaları/eylemi | Belirtilen işlem hattı çalıştırma KIMLIĞI için etkinliğin çalıştığını sorgular. |
> | Eylem | Microsoft. DataFactory/Factory/ardışık düzen eylemsizlik/queryactivityçalıştırmaları/okumak | Belirtilen işlem hattı çalıştırma KIMLIĞI için sorgu etkinliği çalışmalarının sonucunu okur. |
> | Eylem | Microsoft. DataFactory/Factory/ardışık düzen eylemsizlik/okunan | İşlem hattı çalıştırmalarını okur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/createrun/eylem | İşlem hattı için bir çalıştırma oluşturur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/silme | Ardışık düzeni siler. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/ardışık düzen eylemsizlik/activityçalıştırmaları/ilerlemesi/okuma | Etkinlik çalıştırmalarının Ilerlemesini alır. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/ardışık düzen eylemsizlik/Read | İşlem hattı çalıştırmasını okur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/okuma | İşlem hattını okur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/Sandbox/eylem | İşlem hattı için bir hata ayıklama çalıştırması ortamı oluşturur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/Sandbox/oluşturma/eylem | İşlem hattı için bir hata ayıklama çalıştırması ortamı oluşturur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/Sandbox/Çalıştır/eylem | İşlem hattı için bir hata ayıklama çalıştırması oluşturur. |
> | Eylem | Microsoft. DataFactory/Factory/işlem hatları/yazma | İşlem hattı oluştur veya güncelleştir |
> | Eylem | Microsoft. DataFactory/Factory/querydebugardışık düzen eylemsizlik/Action | Hata ayıklama işlem hattının çalıştığını sorgular. |
> | Eylem | Microsoft. DataFactory/Factory/querypipeleylemsizlik/Action | İşlem hattının çalıştırmalarını sorgular. |
> | Eylem | Microsoft. DataFactory/Factory/querypipeleylemsizlik/Read | Sorgu işlem hattı çalıştırmalarının sonucunu okur. |
> | Eylem | Microsoft. DataFactory/Factory/querytriggerçalıştırmaları/eylemi | Tetikleyici çalıştırmalarını sorgular. |
> | Eylem | Microsoft. DataFactory/Factory/querytriggerçalıştırmaları/okuma | Tetikleyici çalıştırmalarının sonucunu okur. |
> | Eylem | Microsoft. DataFactory/Factory/okuma | Data Factory okur. |
> | Eylem | Microsoft. DataFactory/Factory/sandboxardışık düzen eylemsizlik/Sandboxactivityçalıştırmaları/Read | Etkinliğin hata ayıklama çalıştırma bilgisini alır. |
> | Eylem | Microsoft. DataFactory/Factory/startdataflowdebugsession/Action | Bir veri akışı hata ayıklama oturumu başlatır. |
> | Eylem | Microsoft. DataFactory/Factory/submitDataFlowForPreview/Action | Bir hata ayıklama oturumu kullanarak veri önizlemesi almak için veri akışı gönderme. |
> | Eylem | Microsoft. DataFactory/Factory/triggerçalıştırmaları/okuma | Tetikleyici çalıştırmalarını okur. |
> | Eylem | Microsoft. DataFactory/Factory/Tetikleyiciler/silme | Tüm tetikleyiciyi siler. |
> | Eylem | Microsoft. DataFactory/Factory/Tetikleyiciler/okuma | Herhangi bir tetikleyiciyi okur. |
> | Eylem | Microsoft. DataFactory/Factory/Tetikleyiciler/Başlat/eylem | Herhangi bir tetikleyiciyi başlatır. |
> | Eylem | Microsoft. DataFactory/Factory/Tetikleyiciler/durdur/eylem | Tüm tetikleyiciyi sonlandırır. |
> | Eylem | Microsoft. DataFactory/Factory/Triggers/triggerçalıştırmaları/Read | Tetikleyici çalıştırmalarını okur. |
> | Eylem | Microsoft. DataFactory/Factory/Tetikleyiciler/Write | Herhangi bir tetikleyiciyi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DataFactory/Factory/Write | Data Factory oluşturun veya güncelleştirin |
> | Eylem | Microsoft. DataFactory/Locations/configureFactoryRepo/Action | Fabrika için depoyu yapılandırır. |
> | Eylem | Microsoft. DataFactory/Locations/getFeatureValue/Action | Belirli bir konum için pozlama denetim özelliği değeri alın. |
> | Eylem | Microsoft. DataFactory/Locations/getFeatureValue/Read | Belirli bir konum için pozlama denetimi özellik değerini okur. |
> | Eylem | Microsoft. DataFactory/işlemler/okuma | Microsoft Data Factory Provider 'daki tüm Işlemleri okur. |
> | Eylem | Microsoft. DataFactory/Register/ACTION | Data Factory kaynak sağlayıcısı için aboneliği kaydeder. |
> | Eylem | Microsoft. DataFactory/Unregister/eylem | Data Factory kaynak sağlayıcısı için aboneliğin kaydını siler. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | İşlem ilkesini silin. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/computePolicies/Read | Bir işlem ilkesi hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Bir işlem ilkesi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Bir DataLakeStore hesabının bir DataLakeAnalytics hesabıyla bağlantısını kaldırın. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Read | Bir DataLakeAnalytics hesabının bağlı DataLakeStore hesabı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Bir DataLakeAnalytics hesabının bağlı DataLakeStore hesabını oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/Sil | Bir DataLakeAnalytics hesabını silin. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Bir güvenlik duvarı kuralını silin. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/firewallRules/Read | Bir güvenlik duvarı kuralı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Bir güvenlik duvarı kuralı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/operationResults/Read | Bir DataLakeAnalytics hesap işleminin sonucunu elde edin. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/okuma | Mevcut bir DataLakeAnalytics hesabı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/kapsayıcılar/listSasTokens/Action | Bir DataLakeAnalytics hesabının bağlı depolama hesabının depolama kapsayıcıları için SAS belirteçlerini listeleyin. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/kapsayıcılar/okuma | Bir DataLakeAnalytics hesabının bağlı depolama hesabının kapsayıcılarını alın. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/Delete | Bir depolama hesabının bir DataLakeAnalytics hesabından bağlantısını kaldırın. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/Read | Bir DataLakeAnalytics hesabının bağlı depolama hesabı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/Write | Bir DataLakeAnalytics hesabı için bağlı bir depolama hesabı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/Takesahiplik/eylem | Diğer kullanıcılar tarafından gönderilen işleri iptal etmek için izin verin. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/Transferanalizticsunonun/eylemi | Systemmaxanalizticsunkendisini DataLakeAnalytics hesapları arasında aktarın. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Delete | Bir sanal ağ kuralını silin. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Read | Bir sanal ağ kuralı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Write | Bir sanal ağ kuralı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeAnalytics/hesaplar/yazma | Bir DataLakeAnalytics hesabı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeAnalytics/konumlar/yetenek/okuma | DataLakeAnalytics kullanımıyla ilgili bir aboneliğin yetenek bilgilerini alın. |
> | Eylem | Microsoft. DataLakeAnalytics/konumlar/Checknameavaılabılıty/Action | Bir DataLakeAnalytics hesap adının kullanılabilirliğini denetleyin. |
> | Eylem | Microsoft. DataLakeAnalytics/konumlar/operationResults/Read | Bir DataLakeAnalytics hesap işleminin sonucunu elde edin. |
> | Eylem | Microsoft. DataLakeAnalytics/konumlar/kullanımlar/okuma | DataLakeAnalytics kullanımıyla ilgili bir aboneliğin kota kullanımları bilgilerini alın. |
> | Eylem | Microsoft. DataLakeAnalytics/işlemler/okuma | DataLakeAnalytics 'in kullanılabilir işlemlerini alın. |
> | Eylem | Microsoft. DataLakeAnalytics/Register/Action | Aboneliği DataLakeAnalytics 'e kaydedin. |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataLakeStore/hesaplar/Sil | Bir DataLakeStore hesabını silin. |
> | Eylem | Microsoft. DataLakeStore/accounts/Enablekeykasası/eylem | DataLakeStore hesabı için Anahtar Kasası 'nı etkinleştirin. |
> | Eylem | Microsoft. DataLakeStore/accounts/eventGridFilters/Delete | Bir EventGrid filtresini silin. |
> | Eylem | Microsoft. DataLakeStore/accounts/Eventgridfilter/Read | Bir EventGrid filtresi alın. |
> | Eylem | Microsoft. DataLakeStore/accounts/Eventgridfilter/Write | Bir EventGrid filtresi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeStore/accounts/firewallRules/Delete | Bir güvenlik duvarı kuralını silin. |
> | Eylem | Microsoft. DataLakeStore/accounts/firewallRules/Read | Bir güvenlik duvarı kuralı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeStore/accounts/firewallRules/Write | Bir güvenlik duvarı kuralı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeStore/accounts/operationResults/Read | DataLakeStore hesabı işleminin sonucunu elde edin. |
> | Eylem | Microsoft. DataLakeStore/hesaplar/okuma | Mevcut bir DataLakeStore hesabı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeStore/hesaplar/paylaşımlar/Sil | Bir paylaşma silin. |
> | Eylem | Microsoft. DataLakeStore/hesaplar/paylaşımlar/okuma | Bir paylaşma hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeStore/hesaplar/paylaşımlar/yaz | Bir paylaşma oluşturun veya güncelleştirin. |
> | Eylem | Microsoft.DataLakeStore/accounts/Superuser/action | Microsoft. Authorization/Roleatamalar/Write ile verildiğinde Süper Kullanıcı Data Lake Store verin. |
> | Eylem | Microsoft. DataLakeStore/accounts/trustedIdProviders/Delete | Güvenilen bir kimlik sağlayıcısını silin. |
> | Eylem | Microsoft. DataLakeStore/accounts/trustedIdProviders/Read | Güvenilen bir kimlik sağlayıcısı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeStore/accounts/trustedIdProviders/Write | Güvenilen bir kimlik sağlayıcısı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeStore/accounts/virtualNetworkRules/Delete | Bir sanal ağ kuralını silin. |
> | Eylem | Microsoft. DataLakeStore/accounts/virtualNetworkRules/Read | Bir sanal ağ kuralı hakkında bilgi alın. |
> | Eylem | Microsoft. DataLakeStore/accounts/virtualNetworkRules/Write | Bir sanal ağ kuralı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeStore/hesaplar/yazma | Bir DataLakeStore hesabı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. DataLakeStore/konumlar/yetenek/okuma | DataLakeStore kullanımı ile ilgili bir aboneliğin yetenek bilgilerini alın. |
> | Eylem | Microsoft. DataLakeStore/Locations/Checknameavaılabılıty/Action | DataLakeStore hesap adının kullanılabilirliğini kontrol edin. |
> | Eylem | Microsoft. DataLakeStore/konumlar/operationResults/Read | DataLakeStore hesabı işleminin sonucunu elde edin. |
> | Eylem | Microsoft. DataLakeStore/konumlar/kullanımlar/okuma | DataLakeStore kullanımıyla ilgili bir aboneliğin kota kullanımları bilgilerini alın. |
> | Eylem | Microsoft. DataLakeStore/işlemler/okuma | DataLakeStore 'in kullanılabilir işlemlerini alın. |
> | Eylem | Microsoft. DataLakeStore/Register/ACTION | Aboneliği DataLakeStore 'ye kaydedin. |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DataMigration/konumlar/operationResults/Read | 202 kabul edilen bir Yanıtla ilgili uzun süreli bir işlemin durumunu alın |
> | Eylem | Microsoft. DataMigration/Locations/Operationdurumlarının/Read | 202 kabul edilen bir Yanıtla ilgili uzun süreli bir işlemin durumunu alın |
> | Eylem | Microsoft. DataMigration/Register/Action | Aboneliği Azure veritabanı geçiş hizmeti sağlayıcısına kaydeder |
> | Eylem | Microsoft. DataMigration/Services/addWorker/Action | Hizmetin kullanılabilir çalışanlarına bir DMS çalışanı ekler |
> | Eylem | Microsoft. DataMigration/hizmetler/checkStatus/Action | Hizmetin dağıtılıp dağıtılmadığını ve çalışıp çalışmadığını denetleyin |
> | Eylem | Microsoft. DataMigration/Services/configureWorker/Action | Bir DMS çalışanını hizmetin kullanılabilir çalışanları için yapılandırır |
> | Eylem | Microsoft. DataMigration/hizmetler/Sil | Bir kaynağı ve tüm alt öğelerini siler |
> | Eylem | Microsoft. DataMigration/Services/Projects/accessArtifacts/Action | Proje yapıtları almak veya koymak için kullanılabilecek bir URL Oluştur |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/Sil | Bir kaynağı ve tüm alt öğelerini siler |
> | Eylem | Microsoft. DataMigration/Services/Projects/Files/Delete | Bir kaynağı ve tüm alt öğelerini siler |
> | Eylem | Microsoft. DataMigration/Services/Projects/Files/Read | Kaynaklarla ilgili bilgileri okuyun |
> | Eylem | Microsoft. DataMigration/Services/Projects/Files/Read/action | Dosyanın içeriğini okumak için kullanılabilecek bir URL alın |
> | Eylem | Microsoft. DataMigration/Services/Projects/Files/readWrite/Action | Dosyanın içeriğini okumak veya yazmak için kullanılabilecek bir URL alın |
> | Eylem | Microsoft. DataMigration/Services/Projects/Files/Write | Kaynakları ve bunların özelliklerini oluşturun veya güncelleştirin |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/okuma | Kaynaklarla ilgili bilgileri okuyun |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/görevler/iptal/eylem | Şu anda çalışıyorsa, görevi iptal et |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/görevler/Sil | Bir kaynağı ve tüm alt öğelerini siler |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/görevler/okuma | Kaynaklarla ilgili bilgileri okuyun |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/görevler/yazma | Görevleri çalıştırma Azure veritabanı geçiş hizmeti görevleri |
> | Eylem | Microsoft. DataMigration/hizmetler/projeler/yazma | Görevleri çalıştırma Azure veritabanı geçiş hizmeti görevleri |
> | Eylem | Microsoft. DataMigration/Services/Read | Kaynaklarla ilgili bilgileri okuyun |
> | Eylem | Microsoft. DataMigration/Services/removeWorker/Action | Bir DMS çalışanını hizmetin kullanılabilir çalışanlarına kaldırır |
> | Eylem | Microsoft. DataMigration/Services/serviceTasks/Cancel/Action | Şu anda çalışıyorsa, görevi iptal et |
> | Eylem | Microsoft. DataMigration/Services/serviceTasks/Delete | Bir kaynağı ve tüm alt öğelerini siler |
> | Eylem | Microsoft. DataMigration/Services/serviceTasks/Read | Kaynaklarla ilgili bilgileri okuyun |
> | Eylem | Microsoft. DataMigration/Services/serviceTasks/Write | Görevleri çalıştırma Azure veritabanı geçiş hizmeti görevleri |
> | Eylem | Microsoft. DataMigration/hizmetler/yuvalar/silme | Bir kaynağı ve tüm alt öğelerini siler |
> | Eylem | Microsoft. DataMigration/hizmetler/yuvalar/okuma | Kaynaklarla ilgili bilgileri okuyun |
> | Eylem | Microsoft. DataMigration/hizmetler/yuvalar/yazma | Kaynakları ve bunların özelliklerini oluşturun veya güncelleştirin |
> | Eylem | Microsoft. DataMigration/hizmetler/Başlat/eylem | Bu hizmetin geçişleri yeniden işlemesini sağlamak için DMS hizmetini başlatın |
> | Eylem | Microsoft. DataMigration/hizmetler/durdur/eylem | Kendi maliyetini en aza indirmek için DMS hizmetini durdurun |
> | Eylem | Microsoft. DataMigration/Services/updateAgentConfig/Action | DMS Aracısı yapılandırmasını belirtilen değerlerle güncelleştirir. |
> | Eylem | Microsoft. DataMigration/hizmetler/Write | Kaynakları ve bunların özelliklerini oluşturun veya güncelleştirin |
> | Eylem | Microsoft. DataMigration/SKU 'lar/okuma | DMS kaynakları tarafından desteklenen SKU 'ların bir listesini alın. |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Dbformarıdb/Checknameavaılabılıty/Action | Belirli bir abonelik için verilen sunucu adının dünya genelinde sağlanması için kullanılabilir olup olmadığını doğrulayın. |
> | Eylem | Microsoft. Dbformarıdb/konumlar/azureAsyncOperation/Read | MariaDB sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. Dbformarıdb/konumlar/operationResults/Read | ResourceGroup tabanlı MariaDB sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. Dbformarıdb/konumlar/operationResults/Read | MariaDB sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. Dbformarıdb/konumlar/Performancekatmanları/okuma | Kullanılabilir performans katmanları listesini döndürür. |
> | Eylem | Microsoft. Dbformarıdb/konumlar/securityAlertPoliciesAzureAsyncOperation/Read | Sunucu tehdit algılama işlemi sonucunun listesini döndürür. |
> | Eylem | Microsoft. Dbformarıdb/konumlar/Securityalcertpoliciesoperationresults/Read | Sunucu tehdit algılama işlemi sonucunun listesini döndürür. |
> | Eylem | Microsoft. Dbformarıdb/işlemler/okuma | MariaDB Işlemlerinin listesini döndürün. |
> | Eylem | Microsoft. Dbformarıdb/Performancekatmanları/okuma | Kullanılabilir performans katmanları listesini döndürür. |
> | Eylem | Microsoft. Dbformarıdb/Register/ACTION | MariaDB kaynak sağlayıcısını Kaydet |
> | Eylem | Microsoft. Dbformarıdb/sunucular/Yöneticiler/Sil | MariaDB sunucusunun var olan bir yöneticisini siler. |
> | Eylem | Microsoft. Dbformarıdb/sunucular/Yöneticiler/okuma | MariaDB sunucu yöneticilerinin bir listesini alır. |
> | Eylem | Microsoft. Dbformarıdb/sunucular/Yöneticiler/yazma | MariaDB sunucu yöneticisini belirtilen parametrelerle oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Dbformarıdb/Servers/danışmanörler/Createreyorumdedadctionsession/ACTION | Yeni bir öneri eylem oturumu oluştur |
> | Eylem | Microsoft. Dbformarıdb/sunucular/danışmanları/okuma | Danışmanlarının listesini döndürün |
> | Eylem | Microsoft. Dbformarıdb/sunucular/danışmanları/okuma | Danışman döndürme |
> | Eylem | Microsoft. Dbformarıdb/sunucular/danışmanları/recommendedActions/okuma | Önerilen eylemlerin listesini döndür |
> | Eylem | Microsoft. Dbformarıdb/sunucular/danışmanları/recommendedActions/okuma | Önerilen eylemlerin listesini döndür |
> | Eylem | Microsoft. Dbformarıdb/sunucular/danışmanları/recommendedActions/okuma | Önerilen eylemi döndürür |
> | Eylem | Microsoft. Dbformarıdb/sunucular/yapılandırma/okuma | Bir sunucu için yapılandırmaların listesini döndürün veya belirtilen yapılandırma için özellikleri alır. |
> | Eylem | Microsoft. Dbformarıdb/sunucu/yapılandırma/yazma | Belirtilen yapılandırma için değeri güncelleştir |
> | Eylem | Microsoft. Dbformarıdb/sunucular/veritabanları/silme | Var olan bir MariaDB veritabanını siler. |
> | Eylem | Microsoft. Dbformarıdb/sunucular/veritabanları/okuma | MariaDB veritabanlarının listesini döndürün veya belirtilen veritabanının özelliklerini alır. |
> | Eylem | Microsoft. Dbformarıdb/sunucular/veritabanları/yazma | Belirtilen parametrelerle bir MariaDB veritabanı oluşturur veya belirtilen veritabanı için özellikleri güncelleştirir. |
> | Eylem | Microsoft. Dbformarıdb/sunucu/silme | Var olan bir sunucuyu siler. |
> | Eylem | Microsoft. Dbformarıdb/Servers/firewallRules/Delete | Mevcut bir güvenlik duvarı kuralını siler. |
> | Eylem | Microsoft. Dbformarıdb/Servers/firewallRules/Read | Bir sunucu için güvenlik duvarı kuralları listesini döndürün veya belirtilen güvenlik duvarı kuralının özelliklerini alır. |
> | Eylem | Microsoft. Dbformarıdb/Servers/firewallRules/Write | Belirtilen parametrelerle bir güvenlik duvarı kuralı oluşturur veya var olan bir kuralı güncelleştirir. |
> | Eylem | Microsoft. Dbformarıdb/Servers/logFiles/Read | MariaDB LogFiles listesini döndürün. |
> | Eylem | Microsoft. Dbformarıdb/Servers/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak için ayrılan ayarı alır |
> | Eylem | Microsoft. Dbformarıdb/Servers/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Dbformarıdb/Servers/Providers/Microsoft. Insights/logDefinitions/Read | MariaDB sunucuları için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. Dbformarıdb/Servers/Providers/Microsoft. Insights/metricDefinitions/Read | Veritabanları için kullanılabilir ölçümlerin dönüş türleri |
> | Eylem | Microsoft. Dbformarıdb/Servers/Querymetinler/eylem | Sorgu listesi için metin döndürme |
> | Eylem | Microsoft. Dbformarıdb/Servers/Querymetinler/eylem | Bir sorgunun metnini döndürme |
> | Eylem | Microsoft. Dbformarıdb/sunucular/okuma | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Eylem | Microsoft. Dbformarıdb/Servers/recoverableServers/Read | Kurtarılabilir MariaDB sunucu bilgilerini döndürün |
> | Eylem | Microsoft. Dbformarıdb/sunucular/çoğaltmalar/okuma | MariaDB sunucusunun okuma çoğaltmalarını al |
> | Eylem | Microsoft. Dbformarıdb/sunucular/yeniden Başlat/eylem | Belirli bir sunucuyu yeniden başlatır. |
> | Eylem | Microsoft. Dbformarıdb/Servers/Securityalcertpolicies/Read | Belirli bir sunucuda yapılandırılmış sunucu tehdit algılama ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. Dbformarıdb/Servers/Securityalcertpolicies/Write | Belirli bir sunucu için sunucu tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. Dbformarıdb/Servers/Topquerystatıstıcs/Read | En üstteki sorgular için sorgu Istatistikleri listesini döndürür. |
> | Eylem | Microsoft. Dbformarıdb/Servers/Topquerystatıstıcs/Read | Sorgu Istatistiği döndürme |
> | Eylem | Microsoft. Dbformarıdb/Servers/updateConfigurations/Action | Belirtilen sunucu için Güncelleştirme yapılandırması |
> | Eylem | Microsoft. Dbformarıdb/Servers/virtualNetworkRules/Delete | Var olan bir sanal ağ kuralını siler |
> | Eylem | Microsoft. Dbformarıdb/Servers/virtualNetworkRules/Read | Sanal ağ kurallarının listesini döndürün veya belirtilen sanal ağ kuralı için özellikleri alır. |
> | Eylem | Microsoft. Dbformarıdb/Servers/virtualNetworkRules/Write | Belirtilen parametrelere sahip bir sanal ağ kuralı oluşturur veya belirtilen sanal ağ kuralı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. Dbformarıdb/sunucular/waitStatistics/okuma | Örnek için bekleme istatistiklerini döndür |
> | Eylem | Microsoft. Dbformarıdb/sunucular/waitStatistics/okuma | Bekleme istatistiği döndürme |
> | Eylem | Microsoft. Dbformarıdb/sunucu/yazma | Belirtilen parametrelere sahip bir sunucu oluşturur veya belirtilen sunucu için özellikleri veya etiketleri güncelleştirir. |

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Dbformyısql/Checknameavaılabılıty/Action | Belirli bir abonelik için verilen sunucu adının dünya genelinde sağlanması için kullanılabilir olup olmadığını doğrulayın. |
> | Eylem | Microsoft. Dbformyısql/konumlar/azureAsyncOperation/Read | MySQL sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. Dbformyısql/konumlar/operationResults/Read | ResourceGroup tabanlı MySQL Server Işlem sonuçlarını döndür |
> | Eylem | Microsoft. Dbformyısql/konumlar/operationResults/Read | MySQL sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. Dbformyısql/konumlar/Performancekatmanları/okuma | Kullanılabilir performans katmanları listesini döndürür. |
> | Eylem | Microsoft. Dbformyısql/konumlar/securityAlertPoliciesAzureAsyncOperation/Read | Sunucu tehdit algılama işlemi sonucunun listesini döndürür. |
> | Eylem | Microsoft. Dbformyısql/konumlar/Securityalcertpoliciesoperationresults/Read | Sunucu tehdit algılama işlemi sonucunun listesini döndürür. |
> | Eylem | Microsoft. Dbformyısql/işlemler/okuma | MySQL Işlemleri listesini döndürün. |
> | Eylem | Microsoft. Dbformyısql/Performancekatmanları/okuma | Kullanılabilir performans katmanları listesini döndürür. |
> | Eylem | Microsoft. Dbformyısql/Register/Action | MySQL kaynak sağlayıcısını Kaydet |
> | Eylem | Microsoft. Dbformyısql/Servers/Administrators/Delete | MySQL Server 'ın var olan bir yöneticisini siler. |
> | Eylem | Microsoft. Dbformyısql/Servers/Administrators/Read | MySQL Server yöneticilerinin bir listesini alır. |
> | Eylem | Microsoft. Dbformyısql/Servers/Administrators/Write | Belirtilen parametrelerle MySQL Server Yöneticisi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Dbformyısql/Servers/danışmanları/Createreyorumdedadctionsession/ACTION | Yeni bir öneri eylem oturumu oluştur |
> | Eylem | Microsoft. Dbformyısql/Servers/danışmanları/okuma | Danışmanlarının listesini döndürün |
> | Eylem | Microsoft. Dbformyısql/Servers/danışmanları/okuma | Danışman döndürme |
> | Eylem | Microsoft. Dbformyısql/Servers/danışmanları/recommendedActions/okuma | Önerilen eylemlerin listesini döndür |
> | Eylem | Microsoft. Dbformyısql/Servers/danışmanları/recommendedActions/okuma | Önerilen eylemlerin listesini döndür |
> | Eylem | Microsoft. Dbformyısql/Servers/danışmanları/recommendedActions/okuma | Önerilen eylemi döndürür |
> | Eylem | Microsoft. Dbformyısql/sunucular/yapılandırma/okuma | Bir sunucu için yapılandırmaların listesini döndürün veya belirtilen yapılandırma için özellikleri alır. |
> | Eylem | Microsoft. Dbformyısql/sunucular/yapılandırma/yazma | Belirtilen yapılandırma için değeri güncelleştir |
> | Eylem | Microsoft. Dbformyısql/Servers/veritabanları/Delete | Mevcut bir MySQL veritabanını siler. |
> | Eylem | Microsoft. Dbformyısql/Servers/veritabanları/okuma | MySQL veritabanlarının listesini döndürün veya belirtilen veritabanının özelliklerini alır. |
> | Eylem | Microsoft. Dbformyısql/Servers/veritabanları/Write | Belirtilen parametrelerle bir MySQL veritabanı oluşturur veya belirtilen veritabanı için özellikleri güncelleştirir. |
> | Eylem | Microsoft. Dbformyısql/Servers/Delete | Var olan bir sunucuyu siler. |
> | Eylem | Microsoft. Dbformyısql/Servers/firewallRules/Delete | Mevcut bir güvenlik duvarı kuralını siler. |
> | Eylem | Microsoft. Dbformyısql/Servers/firewallRules/Read | Bir sunucu için güvenlik duvarı kuralları listesini döndürün veya belirtilen güvenlik duvarı kuralının özelliklerini alır. |
> | Eylem | Microsoft. Dbformyısql/Servers/firewallRules/Write | Belirtilen parametrelerle bir güvenlik duvarı kuralı oluşturur veya var olan bir kuralı güncelleştirir. |
> | Eylem | Microsoft. Dbformyısql/Servers/logFiles/Read | PostgreSQL LogFiles listesini döndürün. |
> | Eylem | Microsoft. Dbformyısql/Servers/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak için ayrılan ayarı alır |
> | Eylem | Microsoft. Dbformyısql/Servers/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Dbformyısql/Servers/Providers/Microsoft. Insights/logDefinitions/Read | MySQL sunucuları için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. Dbformyısql/Servers/Providers/Microsoft. Insights/metricDefinitions/Read | Veritabanları için kullanılabilir ölçümlerin dönüş türleri |
> | Eylem | Microsoft. Dbformyısql/Servers/Querymetin'ler/eylem | Sorgu listesi için metin döndürme |
> | Eylem | Microsoft. Dbformyısql/Servers/Querymetin'ler/eylem | Bir sorgunun metnini döndürme |
> | Eylem | Microsoft. Dbformyısql/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Eylem | Microsoft. Dbformyısql/Servers/recoverableServers/Read | Kurtarılabilir MySQL sunucusu bilgilerini döndür |
> | Eylem | Microsoft. Dbformyısql/sunucular/çoğaltmalar/okuma | MySQL sunucusunun okuma çoğaltmalarını al |
> | Eylem | Microsoft. Dbformyısql/sunucular/yeniden Başlat/eylem | Belirli bir sunucuyu yeniden başlatır. |
> | Eylem | Microsoft. Dbformyısql/Servers/Securityalcertpolicies/Read | Belirli bir sunucuda yapılandırılmış sunucu tehdit algılama ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. Dbformyısql/Servers/Securityalcertpolicies/Write | Belirli bir sunucu için sunucu tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. Dbformyısql/Servers/Topquerystatıstıcs/Read | En üstteki sorgular için sorgu Istatistikleri listesini döndürür. |
> | Eylem | Microsoft. Dbformyısql/Servers/Topquerystatıstıcs/Read | Sorgu Istatistiği döndürme |
> | Eylem | Microsoft. Dbformyısql/Servers/updateConfigurations/Action | Belirtilen sunucu için Güncelleştirme yapılandırması |
> | Eylem | Microsoft. Dbformyısql/Servers/virtualNetworkRules/Delete | Var olan bir sanal ağ kuralını siler |
> | Eylem | Microsoft. Dbformyısql/Servers/virtualNetworkRules/Read | Sanal ağ kurallarının listesini döndürün veya belirtilen sanal ağ kuralı için özellikleri alır. |
> | Eylem | Microsoft. Dbformyısql/Servers/virtualNetworkRules/Write | Belirtilen parametrelere sahip bir sanal ağ kuralı oluşturur veya belirtilen sanal ağ kuralı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. Dbformyısql/Servers/Waitstatıstıcs/Read | Örnek için bekleme istatistiklerini döndür |
> | Eylem | Microsoft. Dbformyısql/Servers/Waitstatıstıcs/Read | Bekleme istatistiği döndürme |
> | Eylem | Microsoft. Dbformyısql/Servers/Write | Belirtilen parametrelere sahip bir sunucu oluşturur veya belirtilen sunucu için özellikleri veya etiketleri güncelleştirir. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DBforPostgreSQL/Checknameavaılabılıty/Action | Belirli bir abonelik için verilen sunucu adının dünya genelinde sağlanması için kullanılabilir olup olmadığını doğrulayın. |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/azureAsyncOperation/Read | PostgreSQL sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/operationResults/Read | ResourceGroup tabanlı PostgreSQL sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/operationResults/Read | PostgreSQL sunucusu Işlem sonuçlarını döndür |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/Performancekatmanları/okuma | Kullanılabilir performans katmanları listesini döndürür. |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/privateEndpointConnectionAzureAsyncOperation/Read | Özel bir uç nokta bağlantısı işleminin sonucunu alır |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/privateEndpointConnectionOperationResults/Read | Özel bir uç nokta bağlantısı işleminin sonucunu alır |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/privateEndpointConnectionProxyAzureAsyncOperation/Read | Özel bir uç noktası bağlantı proxy 'si işleminin sonucunu alır |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/privateEndpointConnectionProxyOperationResults/Read | Özel bir uç noktası bağlantı proxy 'si işleminin sonucunu alır |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/securityAlertPoliciesAzureAsyncOperation/Read | Sunucu tehdit algılama işlemi sonucunun listesini döndürür. |
> | Eylem | Microsoft. DBforPostgreSQL/konumlar/Securityalcertpoliciesoperationresults/Read | Sunucu tehdit algılama işlemi sonucunun listesini döndürür. |
> | Eylem | Microsoft. DBforPostgreSQL/işlemler/okuma | PostgreSQL Işlemleri listesini döndürün. |
> | Eylem | Microsoft. DBforPostgreSQL/Performancekatmanlarında/Read | Kullanılabilir performans katmanları listesini döndürür. |
> | Eylem | Microsoft. DBforPostgreSQL/Register/Action | PostgreSQL kaynak sağlayıcısını Kaydet |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/Yöneticiler/Sil | PostgreSQL sunucusunun var olan bir yöneticisini siler. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Administrators/Read | PostgreSQL sunucu yöneticilerinin bir listesini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Administrators/Write | Belirtilen parametrelerle PostgreSQL Sunucu Yöneticisi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/danışmanlar/okundu | Danışmanlarının listesini döndürün |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/danışmanlar/recommendedActions/Read | Önerilen eylemlerin listesini döndür |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/danışmanlar/recommendedActionSessions/Action | Öneri yapın |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/konfigürasyonlar/okuma | Bir sunucu için yapılandırmaların listesini döndürün veya belirtilen yapılandırma için özellikleri alır. |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/konfigürasyonlar/yazma | Belirtilen yapılandırma için değeri güncelleştir |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/veritabanları/Delete | Var olan bir PostgreSQL veritabanını siler. |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/veritabanları/okuma | PostgreSQL veritabanlarının listesini döndürün veya belirtilen veritabanının özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/veritabanları/Write | Belirtilen parametrelerle bir PostgreSQL veritabanı oluşturur veya belirtilen veritabanı için özellikleri güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Delete | Var olan bir sunucuyu siler. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/firewallRules/Delete | Mevcut bir güvenlik duvarı kuralını siler. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/firewallRules/Read | Bir sunucu için güvenlik duvarı kuralları listesini döndürün veya belirtilen güvenlik duvarı kuralının özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/firewallRules/Write | Belirtilen parametrelerle bir güvenlik duvarı kuralı oluşturur veya var olan bir kuralı güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/logFiles/Read | PostgreSQL LogFiles listesini döndürün. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Privateendpointconnectionproxy 'Leri/silme | Var olan bir özel uç nokta bağlantı proxy 'sini siler |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Privateendpointconnectionproxy/okuma | Özel uç nokta bağlantı proxy 'leri listesini döndürür veya belirtilen özel uç nokta bağlantı proxy 'sinin özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Privateendpointconnectionproxy/doğrulama/eylem | Özel bir uç nokta bağlantısını doğrular NRP tarafında çağrı oluştur |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Privateendpointconnectionproxy/yazma | Belirtilen parametrelerle özel bir uç nokta bağlantı proxy 'si oluşturur veya belirtilen özel uç nokta bağlantısı proxy 'si için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Delete | Var olan bir özel uç nokta bağlantısını siler |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Read | Özel uç nokta bağlantılarının listesini döndürür veya belirtilen özel uç nokta bağlantısının özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Var olan bir özel uç nokta bağlantısını onaylar veya reddeder |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/Read | Karşılık gelen PostgreSQL sunucusu için özel bağlantı kaynakları alın |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak için ayrılan ayarı alır |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. Insights/logDefinitions/Read | Postgres sunucuları için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. Insights/metricDefinitions/Read | Veritabanları için kullanılabilir ölçümlerin dönüş türleri |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Querymetinler/eylem | Bir sorgunun metnini döndürme |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Querymetin/Read | Sorgu listesi için metin döndürme |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/recoverableServers/Read | Kurtarılabilir PostgreSQL sunucu bilgilerini döndürür |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/çoğaltmalar/okuma | Bir PostgreSQL sunucusunun okuma çoğaltmalarını al |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/yeniden Başlat/eylem | Belirli bir sunucuyu yeniden başlatır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Securityalcertpolicies/Read | Belirli bir sunucuda yapılandırılmış sunucu tehdit algılama ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Securityalcertpolicies/Write | Belirli bir sunucu için sunucu tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Topquerystatıstıcs/Read | En üstteki sorgular için sorgu Istatistikleri listesini döndürür. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/updateConfigurations/Action | Belirtilen sunucu için Güncelleştirme yapılandırması |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Delete | Var olan bir sanal ağ kuralını siler |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Read | Sanal ağ kurallarının listesini döndürün veya belirtilen sanal ağ kuralı için özellikleri alır. |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Write | Belirtilen parametrelere sahip bir sanal ağ kuralı oluşturur veya belirtilen sanal ağ kuralı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/sunucular/waitStatistics/okuma | Örnek için bekleme istatistiklerini döndür |
> | Eylem | Microsoft. DBforPostgreSQL/Servers/Write | Belirtilen parametrelere sahip bir sunucu oluşturur veya belirtilen sunucu için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Configurations/Read | Bir sunucu için yapılandırmaların listesini döndürün veya belirtilen yapılandırma için özellikleri alır. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Configurations/Write | Belirtilen yapılandırma için değeri güncelleştir |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Delete | Var olan bir sunucuyu siler. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Delete | Mevcut bir güvenlik duvarı kuralını siler. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Read | Bir sunucu için güvenlik duvarı kuralları listesini döndürün veya belirtilen güvenlik duvarı kuralının özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Belirtilen parametrelerle bir güvenlik duvarı kuralı oluşturur veya var olan bir kuralı güncelleştirir. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak için ayrılan ayarı alır |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/logDefinitions/Read | Postgres sunucuları için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. Insights/metricDefinitions/Read | Veritabanları için kullanılabilir ölçümlerin dönüş türleri |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/Action | Belirtilen sunucu için Güncelleştirme yapılandırması |
> | Eylem | Microsoft. DBforPostgreSQL/serversv2/Write | Belirtilen parametrelere sahip bir sunucu oluşturur veya belirtilen sunucu için özellikleri veya etiketleri güncelleştirir. |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Devices/Account/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. Devices/Account/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Devices/Account/logDefinitions/Read | Iothub hizmeti için kullanılabilir günlük tanımlarını alır |
> | Eylem | Microsoft. Devices/Account/metricDefinitions/Read | Iothub hizmeti için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. Devices/Checknameavaılabılıty/Action | Iothub adının kullanılabilir olup olmadığını denetle |
> | Eylem | Microsoft. Devices/Checknameavaılabılıty/Action | Iothub adının kullanılabilir olup olmadığını denetle |
> | Eylem | Microsoft. Devices/Checkprovisioningservicenameavaılabılıty/Action | Sağlama hizmeti adının kullanılabilir olup olmadığını denetleyin |
> | Eylem | Microsoft. Devices/Checkprovisioningservicenameavaılabılıty/Action | Sağlama hizmeti adının kullanılabilir olup olmadığını denetleyin |
> | Eylem | Microsoft. Devices/Digitaltwıns/Delete | Var olan bir dijital TWINS hesabını ve tüm alt öğelerini Sil |
> | Eylem | Microsoft. Devices/Digitaltwıns/operationresults/Read | Bir işlemin durumunu dijital bir TWINS hesabına göre alma |
> | Eylem | Microsoft. Devices/Digitaltwıns/Read | Abonelikle ilişkili dijital TWINS hesaplarının bir listesini alır |
> | Eylem | Microsoft. Devices/Digitaltwıns/SKU/okuma | Dijital TWINS hesapları için geçerli SKU 'ların bir listesini alın |
> | Eylem | Microsoft. Devices/Digitaltwıns/Write | Yeni bir digitial Twins hesabı oluşturun |
> | Eylem | Microsoft. Devices/Elaun havuzları/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/sertifikalar/Sil | Sertifikayı siler |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/Certificates/Generatedoğrulamaları Icationcode/Action | Doğrulama kodu oluştur |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/sertifikalar/okuma | Sertifikayı alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/sertifikalar/doğrula/eylem | Sertifika kaynağını doğrula |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/sertifikalar/yazma | Sertifika Oluştur veya güncelleştir |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/Delete | Iothub kiracı kaynağını silme |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/eventHubEndpoints/consumerGroups/Delete | EventHub tüketici grubunu sil |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/eventHubEndpoints/consumerGroups/Read | EventHub tüketici gruplarını al |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/eventHubEndpoints/consumerGroups/Write | EventHub Tüketici grubu oluştur |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/exportDevices/Action | Cihazları dışarı aktar |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/getStats/Read | Iothub kiracı istatistikleri kaynağını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/ımportdevices/Action | Cihazları içeri aktar |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/iotHubKeys/ListKeys/Action | Iothub kiracı anahtarını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/Iothubkiracılar/işler/okuma | Verilen ıothub üzerinde gönderilen Iş ayrıntılarını al |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/listKeys/Action | Iothub kiracı anahtarlarını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/logDefinitions/Read | Iothub hizmeti için kullanılabilir günlük tanımlarını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/metricDefinitions/okuma | Iothub hizmeti için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/Quotaölçümlerini/Read | Kota ölçümlerini al |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/Read | Iothub kiracı kaynağını alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/yönlendirme/rotalar/$testall/Action | Tüm mevcut yollara karşı bir iletiyi test etme |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/yönlendirme/rotalar/$testnew/Action | Bir iletiyi belirtilen test rotasına karşı test etme |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/routingEndpointsHealth/Read | Bir ıothub için tüm yönlendirme uç noktalarının sistem durumunu alır |
> | Eylem | Microsoft. Devices/Elaun havuzları/ıothubkiracılar/Write | Iothub kiracı kaynağı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Devices/Elaun havuzları/metricDefinitions/okuma | Iothub hizmeti için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. Devices/iotHubs/sertifikalar/Sil | Sertifikayı siler |
> | Eylem | Microsoft. Devices/ıothubs/Certificates/Generatedoğrulamaları Icationcode/Action | Doğrulama kodu oluştur |
> | Eylem | Microsoft. Devices/iotHubs/sertifikalar/okuma | Sertifikayı alır |
> | Eylem | Microsoft. Devices/iotHubs/sertifikalar/doğrula/eylem | Sertifika kaynağını doğrula |
> | Eylem | Microsoft. Devices/iotHubs/sertifikalar/yazma | Sertifika Oluştur veya güncelleştir |
> | Eylem | Microsoft. Devices/ıothubs/Delete | Iothub kaynağını Sil |
> | Eylem | Microsoft. Devices/ıothubs/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. Devices/ıothubs/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Devices/ıothubs/eventGridFilters/Delete | Event Grid filtresini siler |
> | Eylem | Microsoft. Devices/ıothubs/eventGridFilters/Read | Event Grid filtresini alır |
> | Eylem | Microsoft. Devices/ıothubs/eventGridFilters/Write | Yeni oluştur veya var olan Event Grid filtresini Güncelleştir |
> | Eylem | Microsoft. Devices/ıothubs/eventHubEndpoints/consumerGroups/Delete | EventHub tüketici grubunu sil |
> | Eylem | Microsoft. Devices/ıothubs/eventHubEndpoints/consumerGroups/Read | EventHub tüketici gruplarını al |
> | Eylem | Microsoft. Devices/ıothubs/eventHubEndpoints/consumerGroups/Write | EventHub Tüketici grubu oluştur |
> | Eylem | Microsoft. Devices/ıothubs/exportDevices/Action | Cihazları dışarı aktar |
> | Eylem | Microsoft. Devices/ıothubs/ımportdevices/Action | Cihazları içeri aktar |
> | Eylem | Microsoft. Devices/ıothubs/iotHubKeys/ListKeys/Action | Verilen ad için ıothub anahtarını al |
> | Eylem | Microsoft. Devices/ıothubs/iotHubStats/Read | Iothub Istatistiklerini al |
> | Eylem | Microsoft. Devices/ıothubs/Jobs/Read | Verilen ıothub üzerinde gönderilen Iş ayrıntılarını al |
> | Eylem | Microsoft. Devices/ıothubs/ListKeys/Action | Tüm ıothub anahtarlarını al |
> | Eylem | Microsoft. Devices/ıothubs/logDefinitions/Read | Iothub hizmeti için kullanılabilir günlük tanımlarını alır |
> | Eylem | Microsoft. Devices/ıothubs/metricDefinitions/Read | Iothub hizmeti için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. Devices/ıothubs/operationresults/Read | Işlem sonucunu al (eski API) |
> | Eylem | Microsoft. Devices/ıothubs/Quotaölçümlerini/Read | Kota ölçümlerini al |
> | Eylem | Microsoft. Devices/ıothubs/Read | Iothub kaynak (ler) i alır |
> | Eylem | Microsoft. Devices/ıothubs/Routing/$testall/Action | Tüm mevcut yollara karşı bir iletiyi test etme |
> | Eylem | Microsoft. Devices/ıothubs/Routing/$testnew/Action | Bir iletiyi belirtilen test rotasına karşı test etme |
> | Eylem | Microsoft. Devices/ıothubs/routingEndpointsHealth/Read | Bir ıothub için tüm yönlendirme uç noktalarının sistem durumunu alır |
> | Eylem | Microsoft. Devices/ıothubs/SKU/okuma | Geçerli ıothub SKU 'Larını al |
> | Eylem | Microsoft. Devices/ıothubs/Write | Iothub kaynağı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. cihazlar/konumlar/operationresults/Read | Konum tabanlı Işlem sonucunu al |
> | Eylem | Microsoft. Devices/operationresults/Read | Işlem sonucunu al |
> | Eylem | Microsoft. Devices/işlemler/okuma | Tüm ResourceProvider Işlemlerini al |
> | Eylem | Microsoft. Devices/provisioningServices/sertifikalar/Sil | Sertifikayı siler |
> | Eylem | Microsoft. Devices/provisioningServices/Certificates/Generatedoğrulamaları Icationcode/Action | Doğrulama kodu oluştur |
> | Eylem | Microsoft. Devices/provisioningServices/sertifikalar/okuma | Sertifikayı alır |
> | Eylem | Microsoft. Devices/provisioningServices/sertifikalar/doğrula/eylem | Sertifika kaynağını doğrula |
> | Eylem | Microsoft. Devices/provisioningServices/sertifikalar/yazma | Sertifika Oluştur veya güncelleştir |
> | Eylem | Microsoft. Devices/provisioningServices/Delete | Iotdps kaynağını Sil |
> | Eylem | Microsoft. Devices/provisioningServices/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. Devices/provisioningServices/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Devices/provisioningServices/anahtarlar/ListKeys/Action | Anahtar adı için ıotdps anahtarlarını al |
> | Eylem | Microsoft. Devices/provisioningServices/ListKeys/Action | Tüm ıotdps anahtarlarını al |
> | Eylem | Microsoft. Devices/provisioningServices/logDefinitions/Read | Sağlama hizmeti için kullanılabilir günlük tanımlarını alır |
> | Eylem | Microsoft. Devices/provisioningServices/metricDefinitions/Read | Sağlama hizmeti için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. Devices/provisioningServices/operationresults/Read | DPS Işlem sonucunu al |
> | Eylem | Microsoft. Devices/provisioningServices/okuma | Iotdps kaynağını al |
> | Eylem | Microsoft. Devices/provisioningServices/SKU 'lar/okuma | Geçerli ıotdps SKU 'Larını al |
> | Eylem | Microsoft. Devices/provisioningServices/Write | Iotdps kaynağı oluşturma |
> | Eylem | Microsoft. Devices/Register/Action | Iothub kaynak sağlayıcısı için aboneliği kaydedin ve ıothub kaynaklarının oluşturulmasını etkinleştirilir |
> | Eylem | Microsoft. Devices/kullanımlar/Read | Bu sağlayıcının abonelik kullanım ayrıntılarını al. |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DevSpaces/denetleyiciler/silme | Azure Dev Spaces denetleyicisi ve veri düzlemi hizmetlerini silme |
> | Eylem | Microsoft. DevSpaces/denetleyiciler/listConnectionDetails/Action | Azure Dev Spaces denetleyicisinin altyapısının bağlantı ayrıntılarını listeleyin |
> | Eylem | Microsoft. DevSpaces/denetleyiciler/okuma | Azure Dev Spaces denetleyicisi özelliklerini okuyun |
> | Eylem | Microsoft. DevSpaces/denetleyiciler/yazma | Azure Dev Spaces denetleyicisi özellikleri oluştur veya güncelleştir |
> | Eylem | Microsoft. DevSpaces/konumlar/checkContainerHostMapping/ACTION | Bir kapsayıcı konağı için var olan denetleyici eşlemesini denetleyin |
> | Eylem | Microsoft. DevSpaces/konumlar/operationresults/Read | Zaman uyumsuz bir işlemin durumunu oku |
> | Eylem | Microsoft. DevSpaces/Register/ACTION | Microsoft dev Spaces kaynak sağlayıcısını abonelikle kaydetme |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DevTestLab/labCenters/Delete | Laboratuvar merkezlerini silin. |
> | Eylem | Microsoft. DevTestLab/labCenters/okuma | Laboratuvar merkezlerini okuyun. |
> | Eylem | Microsoft. DevTestLab/labCenters/Write | Laboratuvar merkezleri ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/artifactSources/armTemplates/Read | Azure Resource Manager şablonlarını okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/artifactSources/yapıtlar/GenerateArmTemplate/Action | Belirtilen yapıt için ARM şablonu oluşturur, gerekli dosyaları bir depolama hesabına yükler ve oluşturulan yapıtı doğrular. |
> | Eylem | Microsoft. DevTestLab/Labs/artifactSources/yapıtlar/okuma | Yapıtları okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/artifactSources/Sil | Yapıt kaynaklarını silin. |
> | Eylem | Microsoft. DevTestLab/Labs/artifactSources/okuma | Yapıt kaynaklarını okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/artifactSources/yazma | Yapıt kaynakları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/ClaimAnyVm/eylem | Laboratuvarda rastgele bir çakışan sanal makine talep edin. |
> | Eylem | Microsoft. DevTestLab/Labs/maliyetler/okuma | Maliyetleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/maliyetler/yazma | Maliyetleri ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/CreateEnvironment/eylem | Laboratuvarda sanal makineler oluşturun. |
> | Eylem | Microsoft. DevTestLab/Labs/CustomImages/Delete | Özel görüntüleri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/CustomImages/Read | Özel görüntüleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/CustomImages/Write | Özel görüntüler ekleme veya değiştirme. |
> | Eylem | Microsoft. DevTestLab/Labs/Delete | Laboratuvarları silin. |
> | Eylem | Microsoft. DevTestLab/Labs/EnsureCurrentUserProfile/Action | Geçerli kullanıcının laboratuvarda geçerli bir profile sahip olduğundan emin olun. |
> | Eylem | Microsoft. DevTestLab/Labs/ExportResourceUsage/Action | Laboratuvar kaynak kullanımını bir depolama hesabına dışarı aktarır |
> | Eylem | Microsoft. DevTestLab/Labs/formüller/Sil | Formülleri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/formüller/okuma | Formülleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/formüller/yazma | Formüller ekleme veya değiştirme. |
> | Eylem | Microsoft. DevTestLab/Labs/Gallerımages/okuma | Galeri görüntülerini okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/GenerateUploadUri/Action | Özel disk görüntülerini laboratuvara yüklemek için bir URI oluşturun. |
> | Eylem | Microsoft. DevTestLab/Labs/ımportvirtualmachine/Action | Bir sanal makineyi farklı bir laboratuvara aktarın. |
> | Eylem | Microsoft. DevTestLab/Labs/Listvhd/eylem | Özel görüntü oluşturmak için kullanılabilir disk görüntülerini listeleyin. |
> | Eylem | Microsoft. DevTestLab/Labs/notificationChannels/Delete | Bildirim kanallarını silin. |
> | Eylem | Microsoft. DevTestLab/Labs/notificationChannels/Notify/ACTION | Belirtilen kanala bildirim gönderin. |
> | Eylem | Microsoft. DevTestLab/Labs/notificationChannels/Read | Bildirim kanallarını okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/notificationChannels/Write | Bildirim kanalları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/policySets/EvaluatePolicies/Action | Laboratuvar ilkesini değerlendirir. |
> | Eylem | Microsoft. DevTestLab/Labs/policySets/policies/Delete | İlkeleri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/policySets/policies/Read | İlkeleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/policySets/policies/Write | İlke ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/policySets/Read | İlke kümelerini okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/Read | Labs 'i okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/zamanlamalar/silme | Zamanlamaları silin. |
> | Eylem | Microsoft. DevTestLab/Labs/zamanlamalar/yürütme/eylem | Bir zamanlama yürütün. |
> | Eylem | Microsoft. DevTestLab/Labs/zamanlamalar/ListApplicable/Action | Tüm geçerli zamanlamaları listeler |
> | Eylem | Microsoft. DevTestLab/Labs/zamanlamalar/okuma | Zamanlamaları okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/zamanlamalar/yazma | Zamanlamaları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/Servicerunanlar/Delete | Hizmet çalıştıranlar silme. |
> | Eylem | Microsoft. DevTestLab/Labs/Servicerunanlar/okuma | Hizmet çalıştıranlar bölümünü okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/Servicerunanlar/Write | Hizmet çalıştıranlar ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/Sharedgaleriler/silme | Paylaşılan galerileri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/Sharedgaleriler/okuma | Paylaşılan galerileri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/Sharedgaleriler/sharedImages/Delete | Paylaşılan görüntüleri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/Sharedgaleriler/sharedImages/Read | Paylaşılan görüntüleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/Sharedgaleriler/sharedImages/Write | Paylaşılan görüntüleri ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/Sharedgaleriler/yazma | Paylaşılan galeriler ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/Sil | Kullanıcı profillerini silin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/diskler/Iliştirme/eylem | Diskin sanal makineye kiralanmasını ekleyin ve oluşturun. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/diskler/Sil | Diskleri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/diskler/ayırma/eylem | Sanal makineye bağlı disk kiralanmasını ayırın ve bölün. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/diskler/okuma | Diskleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/diskler/yazma | Diskleri ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/ortamlar/Sil | Ortamları silin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/ortamlar/okuma | Ortamları okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/ortamlar/Write | Ortamları ekleme veya değiştirme. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/okuma | Kullanıcı profillerini okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/gizlilikler/Delete | Gizli dizileri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/gizlilikler/Read | Gizli dizileri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/gizlilikler/Write | Gizli dizileri ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/Sil | Hizmet yapılarını silin. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/Listapperepblezamanlamalar/eylem | Varsa, geçerli başlatma/durdurma zamanlamalarını listeler. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/okuma | Hizmet yapılarını okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/zamanlamalar/silme | Zamanlamaları silin. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/zamanlamalar/yürütme/eylem | Bir zamanlama yürütün. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/zamanlamalar/okuma | Zamanlamaları okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/zamanlamalar/yazma | Zamanlamaları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/Başlat/eylem | Bir Service Fabric başlatın. |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/durdur/eylem | Service Fabric 'i durdur |
> | Eylem | Microsoft. DevTestLab/Labs/Users/Serviceyapılar/yazma | Hizmet yapıları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/kullanıcılar/yaz | Kullanıcı profilleri ekleme veya değiştirme. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/AddDataDisk/eylem | Sanal makineye yeni veya mevcut bir veri diski ekleyin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/Applyartifdavranır/eylemi | Yapıtları sanal makineye uygulayın. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/talep/eylem | Mevcut bir sanal makinenin sahipliğini al |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/Delete | Sanal makineleri silin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/çıkarılabilir veri diski/eylem | Belirtilen diski sanal makineden ayırın. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/GetRdpFileContents/Action | Sanal makine için RDP dosyasının içeriğini temsil eden bir dize alır |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/Listapperepblezamanlamalar/eylem | Varsa, geçerli başlatma/durdurma zamanlamalarını listeler. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/okuma | Sanal makineleri okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/yeniden dağıtma/eylem | Bir sanal makineyi yeniden dağıtın |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/yeniden boyutlandır/eylem | Sanal makineyi yeniden boyutlandırın. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/yeniden Başlat/eylem | Bir sanal makineyi yeniden başlatın. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/zamanlamalar/silme | Zamanlamaları silin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/zamanlamalar/yürütme/eylem | Bir zamanlama yürütün. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/zamanlamalar/okuma | Zamanlamaları okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/zamanlamalar/yazma | Zamanlamaları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/Başlat/eylem | Bir sanal makine başlatın. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/durdur/eylem | Bir sanal makineyi durdur |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/TransferDisks/Action | Sanal makineye bağlı tüm veri disklerini geçerli kullanıcıya ait olacak şekilde aktarır. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/talep/işlem | Mevcut bir sanal makinenin sahipliğini serbest bırakma |
> | Eylem | Microsoft. DevTestLab/Labs/virtualMachines/Write | Sanal makineler ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualNetworks/savunma/silme | Savunma ana bilgisayarlarını silin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualNetworks/Bastionkonakları/okuma | Savunma ana bilgisayarlarını okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualNetworks/Bastionkonakları/yazma | Savunma Konakları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualNetworks/Delete | Sanal ağları silin. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualNetworks/Read | Sanal ağları okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/virtualNetworks/Write | Sanal ağları ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/vmPools/Delete | Sanal makine havuzlarını silin. |
> | Eylem | Microsoft. DevTestLab/Labs/vmPools/Read | Sanal makine havuzlarını okuyun. |
> | Eylem | Microsoft. DevTestLab/Labs/vmPools/Write | Sanal makine havuzlarını ekleyin veya değiştirin. |
> | Eylem | Microsoft. DevTestLab/Labs/Write | Labs ekleme veya değiştirme. |
> | Eylem | Microsoft. DevTestLab/konumlar/işlemler/okuma | Okuma işlemleri. |
> | Eylem | Microsoft. DevTestLab/Register/Action | Aboneliği kaydeder |
> | Eylem | Microsoft. DevTestLab/zamanlamalar/silme | Zamanlamaları silin. |
> | Eylem | Microsoft. DevTestLab/zamanlamalar/yürütme/eylem | Bir zamanlama yürütün. |
> | Eylem | Microsoft. DevTestLab/zamanlamalar/okuma | Zamanlamaları okuyun. |
> | Eylem | Microsoft. DevTestLab/zamanlamalar/yeniden hedefle/eylem | Bir zamanlamanın hedef kaynak kimliğini güncelleştirir. |
> | Eylem | Microsoft. DevTestLab/zamanlamalar/yazma | Zamanlamaları ekleyin veya değiştirin. |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DocumentDB/databaseAccountNames/Read | Ad kullanılabilirliğini denetler. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/Sil | Bir koleksiyonu silin. Yalnızca API türleri için geçerlidir: ' MongoDB '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' MongoDB '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/okuma | Bir koleksiyonu okuyun veya tüm koleksiyonları listeleyin. Yalnızca API türleri için geçerlidir: ' MongoDB '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/ayarlar/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' MongoDB '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/ayarlar/okuma | Koleksiyon verimini okuyun. Yalnızca API türleri için geçerlidir: ' MongoDB '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/ayarlar/yazma | Koleksiyon verimini güncelleştirin. Yalnızca API türleri için geçerlidir: ' MongoDB '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/koleksiyonlar/yazma | Koleksiyon oluşturun veya güncelleştirin. Yalnızca API türleri için geçerlidir: ' MongoDB '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/silme | Kapsayıcıyı silin. Yalnızca API türleri için geçerlidir: ' SQL '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' SQL '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/okuma | Bir kapsayıcıyı okuyun veya tüm kapsayıcıları listeleyin. Yalnızca API türleri için geçerlidir: ' SQL '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/ayarlar/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' SQL '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/ayarlar/okuma | Kapsayıcı verimini okuyun. Yalnızca API türleri için geçerlidir: ' SQL '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/ayarlar/yazma | Kapsayıcı verimini güncelleştirme. Yalnızca API türleri için geçerlidir: ' SQL '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/kapsayıcılar/yazma | Bir kapsayıcı oluşturun veya güncelleştirin. Yalnızca API türleri için geçerlidir: ' SQL '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/Delete | Bir veritabanını silin. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/silme | Bir grafiği silin. Yalnızca API türleri için geçerlidir: ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/okuma | Bir grafiği okuyun veya tüm grafikleri listeleyin. Yalnızca API türleri için geçerlidir: ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/ayarlar/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' gremlin'. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/ayarlar/okuma | Grafik aktarım hızını okuyun. Yalnızca API türleri için geçerlidir: ' gremlin'. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/ayarlar/yazma | Grafik aktarım hızını güncelleştirin. Yalnızca API türleri için geçerlidir: ' gremlin'. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/grafikler/yazma | Grafik oluşturun veya güncelleştirin. Yalnızca API türleri için geçerlidir: ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/okuma | Bir veritabanını okuyun veya tüm veritabanlarını listeleyin. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/Settings/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/ayarlar/okuma | Veritabanı verimini okuyun. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/ayarlar/yazma | Veritabanı verimini güncelleştirme. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/veritabanları/yazma | Bir veritabanı oluşturun. Yalnızca API türleri için geçerlidir: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Delete | Anahtar alanını silin. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Read | Bir anahtar alanı okuyun veya tüm anahtar alanlarını listeleyin. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Settings/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' Cassandra '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Settings/Read | Anahtar alanı verimini okuyun. Yalnızca API türleri için geçerlidir: ' Cassandra '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Settings/Write | Anahtar alanı aktarım hızını güncelleştirin. Yalnızca API türleri için geçerlidir: ' Cassandra '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Tables/Delete | Bir tabloyu silin. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Tables/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Tables/Read | Tablo okuyun veya tüm tabloları listeleyin. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Tables/Settings/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' Cassandra '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/api/keyspaces/Tables/Settings/Read | Tablo verimini okuyun. Yalnızca API türleri için geçerlidir: ' Cassandra '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API 'ler/keyspaces/tablolar/ayarlar/yazma | Tablo verimini güncelleştirme. Yalnızca API türleri için geçerlidir: ' Cassandra '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API 'ler/keyspaces/tablolar/Write | Tablo oluşturun veya güncelleştirin. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/keyspaces/Write | Anahtar alanı oluşturun. Yalnızca API türleri için geçerlidir: ' Cassandra '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/Delete | Bir tabloyu silin. Yalnızca API türleri için geçerlidir: ' Table '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' Table '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/Read | Tablo okuyun veya tüm tabloları listeleyin. Yalnızca API türleri için geçerlidir: ' Table '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/Settings/operationResults/Read | Zaman uyumsuz işlemin durumunu okuyun. Yalnızca API türleri için geçerlidir: ' Table '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/Settings/Read | Tablo verimini okuyun. Yalnızca API türleri için geçerlidir: ' Table '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/Settings/Write | Tablo verimini güncelleştirme. Yalnızca API türleri için geçerlidir: ' Table '. Yalnızca ayar türleri için geçerlidir: ' aktarım hızı '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/API/Tables/Write | Tablo oluşturun veya güncelleştirin. Yalnızca API türleri için geçerlidir: ' Table '. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Backup/Action | Yedeklemeyi yapılandırmak için bir istek gönder |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/changeResourceGroup/Action | Veritabanı hesabının kaynak grubunu değiştir |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/metricDefinitions/okuma | Koleksiyon ölçüm tanımlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/ölçümler/okuma | Koleksiyon ölçümlerini okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/Partitionkeyrangeıd/ölçümler/okuma | Veritabanı hesabı bölüm anahtarı düzeyi ölçümlerini oku |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/bölümler/ölçümler/okuma | Veritabanı hesabı bölüm düzeyi ölçümlerini oku |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/bölümler/okuma | Bir koleksiyondaki veritabanı hesabı bölümlerini okuma |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/bölümler/kullanımlar/okuma | Veritabanı hesabı bölüm düzeyi kullanımlarını oku |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/koleksiyonlar/kullanımlar/okuma | Koleksiyon kullanımlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/metricDefinitions/okuma | Veritabanı ölçüm tanımlarını okur |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/ölçümler/okuma | Veritabanı ölçümlerini okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/veritabanları/kullanımlar/okuma | Veritabanı kullanımlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Delete | Veritabanı hesaplarını siler. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/Action | Bir veritabanı hesabının bölgelerinin yük devretme önceliklerini değiştirme. Bu, el ile yük devretme işlemi gerçekleştirmek için kullanılır |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Veritabanı hesabının yedekleme ilkesini al |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/Action | Bir veritabanı hesabı için bağlantı dizelerini al |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/listKeys/Action | Bir veritabanı hesabının anahtarlarını listeleme |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/metricDefinitions/Read | Veritabanı hesabı ölçüm tanımlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/ölçümler/okuma | Veritabanı hesabı ölçümlerini okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/offlineRegion/eylem | Veritabanı hesabının bir bölgesi çevrimdışı.  |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/onlineRegion/ACTION | Veritabanı hesabının bir bölgesi çevrimiçi. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/operationResults/Read | Zaman uyumsuz işlemin durumunu oku |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/yüzdebirlik/ölçümler/okuma | Gecikme süresi ölçümlerini okuyun |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/yüzdebirlik/Read | Çoğaltma gecikmeleri yüzdebirlik değeri okuyun |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/yüzdebirlik/sourceRegion/targetRegion/ölçümler/okuma | Belirli bir kaynak ve hedef bölge için gecikme süresi ölçümlerini okuyun |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/yüzdebirlik/targetRegion/ölçümler/okuma | Belirli bir hedef bölge için gecikme süresi ölçümlerini okuyun |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Privateendpointconnectionproxy/Delete | Veritabanı hesabının özel bir uç noktası bağlantı proxy 'sini Sil |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Privateendpointconnectionproxy/okuma | Veritabanı hesabının özel bir uç noktası bağlantı proxy 'sini okuma |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Privateendpointconnectionproxy/doğrulama/eylem | Veritabanı hesabının özel bir uç noktası bağlantı proxy 'sini doğrulama |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Privateendpointconnectionproxy/yazma | Veritabanı hesabının özel uç nokta bağlantısı proxy 'sini oluşturma veya güncelleştirme |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Read | Bir veritabanı hesabını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Veritabanı hesabı salt okunur anahtarlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Read | Veritabanı hesabı salt okunur anahtarlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/regenerateKey/ACTION | Bir veritabanı hesabının anahtarlarını döndürme |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Region/veritabanları/koleksiyonlar/ölçümler/okuma | Bölgesel koleksiyon ölçümlerini okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Region/veritabanları/koleksiyonlar/Partitionkeyrangeıd/ölçümler/okuma | Bölgesel veritabanı hesabı bölüm anahtarı düzeyi ölçümlerini oku |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Region/veritabanları/koleksiyonlar/bölümler/ölçümler/okuma | Bölgesel veritabanı hesabı bölüm düzeyi ölçümlerini oku |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Region/veritabanları/koleksiyonlar/bölümler/okuma | Bir koleksiyondaki bölgesel veritabanı hesabı bölümlerini okuma |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Region/ölçümler/okuma | Bölge ve veritabanı hesabı ölçümlerini okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/restore/Action | Geri yükleme isteği gönder |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/kullanımlar/Read | Veritabanı hesabı kullanımlarını okur. |
> | Eylem | Microsoft. DocumentDB/databaseAccounts/Write | Bir veritabanı hesabını güncelleştirin. |
> | Eylem | Microsoft. DocumentDB/konumlar/Deletevirtualnetworkoralt ağları/eylem | VirtualNetwork veya subnet 'in silinmekte olduğunu Microsoft. DocumentDB 'ye bildirir |
> | Eylem | Microsoft. DocumentDB/konumlar/Deletevirtualnetworkoralt ağları/operationResults/Read | Deletevirtualnetworkoraltağlar zaman uyumsuz işleminin durumunu okuyun |
> | Eylem | Microsoft. DocumentDB/konumlar/operationsStatus/Read | Zaman uyumsuz Işlemlerin durumunu okur |
> | Eylem | Microsoft. DocumentDB/operationResults/Read | Zaman uyumsuz işlemin durumunu oku |
> | Eylem | Microsoft. DocumentDB/işlemler/okuma | Microsoft DocumentDB için kullanılabilen okuma işlemleri  |
> | Eylem | Microsoft. DocumentDB/Register/ACTION |  Abonelik için Microsoft DocumentDB kaynak sağlayıcısını kaydedin |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. DomainRegistration/Checkdomainavaılabılıty/Action | Bir etki alanının satın alma için kullanılabilir olup olmadığını denetleyin |
> | Eylem | Microsoft. DomainRegistration/Domains/Delete | Var olan bir etki alanını silin. |
> | Eylem | Microsoft. DomainRegistration/Domains/domainownershiptanýmlayýcýlarý/Delete | Sahiplik tanımlayıcısını sil |
> | Eylem | Microsoft. DomainRegistration/Domains/domainownershiptanýmlayýcýlarý/Read | Sahiplik tanımlayıcılarını listeleme |
> | Eylem | Microsoft. DomainRegistration/Domains/domainownershiptanýmlayýcýlarý/Read | Sahiplik tanımlayıcıyı al |
> | Eylem | Microsoft. DomainRegistration/Domains/domainownershiptanýmlayýcýlarý/Write | Tanımlayıcı oluştur veya güncelleştir |
> | Eylem | Microsoft. DomainRegistration/Domains/operationresults/Read | Etki alanı işlemi al |
> | Eylem | Microsoft. DomainRegistration/Domains/Read | Etki alanlarının listesini al |
> | Eylem | Microsoft. DomainRegistration/Domains/Read | Etki alanını al |
> | Eylem | Microsoft. DomainRegistration/etki alanları/Yenile/eylem | Var olan bir etki alanını yenileyin. |
> | Eylem | Microsoft. DomainRegistration/Domains/Write | Yeni bir etki alanı ekleme veya var olanı güncelleştirme |
> | Eylem | Microsoft. DomainRegistration/generateSsoRequest/ACTION | Etki alanı Denetim Merkezi 'nde oturum açmak için bir istek oluşturun. |
> | Eylem | Microsoft. DomainRegistration/Listdomainönerilere/eylem | Anahtar sözcüklere göre liste etki alanı önerilerini al |
> | Eylem | Microsoft. DomainRegistration/işlemler/okuma | App Service etki alanı kaydından tüm işlemleri Listele |
> | Eylem | Microsoft. DomainRegistration/Register/Action | Abonelik için Microsoft etki alanları kaynak sağlayıcısını kaydetme |
> | Eylem | Microsoft. DomainRegistration/topLevelDomains/Listagreleştir/eylem | Sözleşme eylemini listeleme |
> | Eylem | Microsoft. DomainRegistration/topLevelDomains/Read | TopLevel etki alanlarını al |
> | Eylem | Microsoft. DomainRegistration/topLevelDomains/Read | TopLevel etki alanını al |
> | Eylem | Microsoft. DomainRegistration/Validatedomainregistrationınformation/Action | Etki alanı satın alma nesnesini göndermeden doğrula |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. EventGrid/Domains/Delete | Etki alanını silme |
> | Eylem | Microsoft. EventGrid/Domains/listKeys/Action | Bir etki alanı için liste anahtarları |
> | Eylem | Microsoft. EventGrid/Domains/Providers/Microsoft. Insights/metricDefinitions/Read | Etki alanları için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. EventGrid/Domains/Read | Etki alanını okuma |
> | Eylem | Microsoft. EventGrid/Domains/regenerateKey/ACTION | Bir etki alanı için anahtarı yeniden oluştur |
> | Eylem | Microsoft. EventGrid/Domains/konular/Sil | Etki alanı konusunu silme |
> | Eylem | Microsoft. EventGrid/Domains/konular/okuma | Bir etki alanı konusunu okuyun |
> | Eylem | Microsoft. EventGrid/Domains/konular/yazma | Etki alanı konusu oluşturma veya güncelleştirme |
> | Eylem | Microsoft. EventGrid/Domains/Write | Etki alanı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. EventGrid/Eventabonelikleri/silme | Bir eventSubscription silme |
> | Eylem | Microsoft. EventGrid/Eventabonelikler/getFullUrl/eylem | Olay aboneliği için tam URL al |
> | Eylem | Microsoft. EventGrid/Eventabonelikler/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Olay abonelikleri için tanılama ayarını alır |
> | Eylem | Microsoft. EventGrid/Eventabonelikler/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Olay abonelikleri için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. EventGrid/Eventabonelikleri/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Eventabonelikler için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. EventGrid/Eventaboneliklerde/okunan | Bir eventSubscription okuma |
> | Eylem | Microsoft. EventGrid/Eventaboneliklerin/Write | EventSubscription oluşturma veya güncelleştirme |
> | Eylem | Microsoft. EventGrid/Extensionkonular/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Konular için tanılama ayarını alır |
> | Eylem | Microsoft. EventGrid/Extensionkonular/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Konular için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. EventGrid/Extensionkonular/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Konular için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. EventGrid/Extensionkonuları/okuma | Bir extensionTopic okuyun. |
> | Eylem | Microsoft. EventGrid/Locations/Eventaboneliklerde/okunan | Bölgesel olay aboneliklerini listeleme |
> | Eylem | Microsoft. EventGrid/Locations/operationResults/Read | Bölgesel bir işlemin sonucunu oku |
> | Eylem | Microsoft. EventGrid/Locations/operationsStatus/Read | Bölgesel bir işlemin durumunu okuyun |
> | Eylem | Microsoft. EventGrid/Locations/topictypes/Eventabonelikleri/okuma | TopicType 'a göre bölgesel olay aboneliklerini listeleyin |
> | Eylem | Microsoft. EventGrid/operationResults/Read | İşlemin sonucunu oku |
> | Eylem | Microsoft. EventGrid/işlemler/okuma | EventGrid işlemlerini listeleyin. |
> | Eylem | Microsoft. EventGrid/operationsStatus/Read | İşlemin durumunu okuyun |
> | Eylem | Microsoft. EventGrid/Register/Action | EventGrid kaynak sağlayıcısı için aboneliği kaydeder. |
> | Eylem | Microsoft. EventGrid/konular/Sil | Konuyu silme |
> | Eylem | Microsoft. EventGrid/konular/listKeys/ACTION | Konu başlığı anahtarlarını listeleyin |
> | Eylem | Microsoft. EventGrid/konular/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Konular için tanılama ayarını alır |
> | Eylem | Microsoft. EventGrid/konular/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Konular için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. EventGrid/konular/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Konular için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. EventGrid/konular/okuma | Konu başlığını okuyun |
> | Eylem | Microsoft. EventGrid/konular/regenerateKey/Action | Konu için anahtarı yeniden oluştur |
> | Eylem | Microsoft. EventGrid/konular/yaz | Konu oluşturma veya güncelleştirme |
> | Eylem | Microsoft. EventGrid/topictypes/Eventaboneliklerde/okunan | Küresel olay aboneliklerini konu türüne göre Listele |
> | Eylem | Microsoft. EventGrid/topictypes/eventTypes/Read | Bir topıtype tarafından desteklenen eventTypes 'ı okuyun |
> | Eylem | Microsoft. EventGrid/topictypes/Read | Bir topıtype oku |
> | Eylem | Microsoft. EventGrid/Unregister/eylem | EventGrid kaynak sağlayıcısı için aboneliğin kaydını siler. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. EventHub/Availableclusterregion/okuma | Azure bölgesi tarafından sağlanan önceden sağlanmış kümeleri listelemek için okuma işlemi. |
> | Eylem | Microsoft. EventHub/Checknameavaılabılıty/Action | Belirtilen abonelikte ad alanının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. EventHub/checkNamespaceAvailability/Action | Belirtilen abonelikte ad alanının kullanılabilirliğini denetler. Bu API kullanım dışı, lütfen bunun yerine Checknameavaılabılıty kullanın. |
> | Eylem | Microsoft. EventHub/kümeler/Sil | Var olan bir küme kaynağını siler. |
> | Eylem | Microsoft. EventHub/kümeler/ad alanları/okuma | Bir küme içindeki ad alanları için ad alanı ARM kimliklerini listeleyin. |
> | Eylem | Microsoft. EventHub/kümeler/operationresults/Read | Zaman uyumsuz küme işleminin durumunu alın. |
> | Eylem | Microsoft. EventHub/kümeler/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Küme ölçümleri kaynak açıklamaları listesini al |
> | Eylem | Microsoft. EventHub/kümeler/okuma | Küme kaynağı açıklamasını alır |
> | Eylem | Microsoft. EventHub/kümeler/yazma | Var olan bir küme kaynağını oluşturur veya değiştirir. |
> | Eylem | Microsoft. EventHub/konumlar/Deletevirtualnetworkoralt ağları/eylem | Belirtilen VNet için EventHub kaynak sağlayıcısında VNet kurallarını siler |
> | Eylem | Microsoft. EventHub/ad alanları/authorizationRules/eylem | Güncelleştirme ad alanı yetkilendirme kuralı. Bu API kullanım dışıdır. Lütfen bunun yerine ad alanı yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. EventHub/ad alanları/authorizationRules/Sil | Ad alanı yetkilendirme kuralını silin. Varsayılan ad alanı yetkilendirme kuralı silinemez.  |
> | Eylem | Microsoft. EventHub/namespaces/authorizationRules/ListKeys/Action | Ad alanına bağlantı dizesi al |
> | Eylem | Microsoft. EventHub/ad alanları/authorizationRules/okuma | Ad alanı yetkilendirme kuralları açıklaması listesini alın. |
> | Eylem | Microsoft. EventHub/namespaces/authorizationRules/regenerateKeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. EventHub/ad alanları/authorizationRules/Write | Ad alanı düzeyinde yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları, birincil ve Ikincil anahtarlar güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. EventHub/ad alanları/silme | Ad alanı kaynağını Sil |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/authorizationRules/ListKeys/Action | Olağanüstü durum kurtarma birincil ad alanı için yetkilendirme kuralları anahtarlarını alır |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/authorizationRules/Read | Olağanüstü durum kurtarma birincil ad alanının yetkilendirme kurallarını al |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/Breakeşleştirmeyi/eylemi | Olağanüstü durum kurtarmayı devre dışı bırakır ve birincil olan değişiklikleri ikincil ad alanlarına çoğaltmayı durduruyor. |
> | Eylem | Microsoft. EventHub/namespaces/disyıldız recoveryconfigs/Checknameavaılabılıty/Action | Belirtilen abonelik altında ad alanı diğer adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/Delete | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını siler. Bu işlem yalnızca birincil ad alanı aracılığıyla çağrılabilir. |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/yük devretme/eylem | COĞRAFI bir DR yük devretmesini çağırır ve ad alanı diğer adını ikincil ad alanına işaret edecek şekilde yeniden yapılandırır. |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/Read | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını alır. |
> | Eylem | Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/Write | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/authorizationRules/Action | EventHub güncelleştirme işlemi. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. Yetkilendirme kuralları. Lütfen yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/authorizationRules/Delete | EventHub yetkilendirme kurallarını silme işlemi |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/authorizationRules/ListKeys/Action | EventHub bağlantı dizesini al |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/authorizationRules/Read |  EventHub yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/authorizationRules/Write | EventHub yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup kaynağını silme işlemi |
> | Eylem | Microsoft. EventHub/namespaces/eventHubs/consumergroups/Read | ConsumerGroup kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/namespaces/eventHubs/consumergroups/Write | ConsumerGroup özellikleri oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/Delete | EventHub kaynağını silme işlemi |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/Read | EventHub kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/namespaces/eventhubs/Write | EventHub özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. EventHub/namespaces/ıpfilterrules/Delete | IP filtresi kaynağını Sil |
> | Eylem | Microsoft. EventHub/namespaces/ıpfilterrules/Read | IP filtre kaynağı al |
> | Eylem | Microsoft. EventHub/namespaces/ıpfilterrules/Write | IP filtre kaynağı oluştur |
> | DataAction | Microsoft. EventHub/ad alanları/iletiler/alma/eylem | İleti alma |
> | DataAction | Microsoft. EventHub/ad alanları/iletiler/gönderme/eylem | İleti gönderme |
> | Eylem | Microsoft. EventHub/namespaces/messagingPlan/okuma | Bir ad alanı için mesajlaşma planını alır.<br>Bu API kullanım dışıdır.<br>MessagingPlan kaynağı aracılığıyla sunulan özellikler sonraki API sürümlerindeki (üst) ad alanı kaynağına taşınır.<br>Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. EventHub/namespaces/messagingPlan/yazma | Bir ad alanı için mesajlaşma planını güncelleştirir.<br>Bu API kullanım dışıdır.<br>MessagingPlan kaynağı aracılığıyla sunulan özellikler sonraki API sürümlerindeki (üst) ad alanı kaynağına taşınır.<br>Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. EventHub/namespaces/networkruleset/Delete | VNET kural kaynağını Sil |
> | Eylem | Microsoft. EventHub/namespaces/networkruleset/Read | NetworkRuleSet kaynağını alır |
> | Eylem | Microsoft. EventHub/namespaces/networkruleset/Write | VNET kural kaynağı oluşturma |
> | Eylem | Microsoft. EventHub/namespaces/networkrulesets/Delete | VNET kural kaynağını Sil |
> | Eylem | Microsoft. EventHub/namespaces/networkrulesets/Read | NetworkRuleSet kaynağını alır |
> | Eylem | Microsoft. EventHub/namespaces/networkrulesets/Write | VNET kural kaynağı oluşturma |
> | Eylem | Microsoft. EventHub/namespaces/operationresults/Read | Ad alanı işleminin durumunu al |
> | Eylem | Microsoft. EventHub/namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Ad alanı Tanılama ayarları kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Ad alanı Tanılama ayarları kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Ad alanı günlükleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Ad alanı ölçümleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/ad alanları/okuma | Ad alanı kaynağı açıklaması listesini al |
> | Eylem | Microsoft. EventHub/namespaces/removeAcsNamepsace/eylem | ACS ad alanını kaldır |
> | Eylem | Microsoft. EventHub/namespaces/virtualNetworkRules/Delete | VNET kural kaynağını Sil |
> | Eylem | Microsoft. EventHub/namespaces/virtualNetworkRules/Read | VNET kural kaynağını alır |
> | Eylem | Microsoft. EventHub/namespaces/virtualNetworkRules/Write | VNET kural kaynağı oluşturma |
> | Eylem | Microsoft. EventHub/ad alanları/yazma | Bir ad alanı kaynağı oluşturun ve özelliklerini güncelleştirin. Ad alanının etiketleri ve kapasitesi, güncelleştirilebilen özelliklerdir. |
> | Eylem | Microsoft. EventHub/işlemler/okuma | Işlemleri al |
> | Eylem | Microsoft. EventHub/Register/ACTION | EventHub kaynak sağlayıcısı için aboneliği kaydeder ve EventHub kaynaklarının oluşturulmasını sunar |
> | Eylem | Microsoft. EventHub/SKU/okuma | SKU kaynak açıklamaları listesini al |
> | Eylem | Microsoft. EventHub/SKU/bölge/okuma | Skuregion kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. EventHub/kaydını kaldırma/eylem | EventHub kaynak sağlayıcısını kaydeder |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Features/Features/Read | Bir aboneliğin özelliklerini alır. |
> | Eylem | Microsoft. Features/işlemler/okuma | İşlem listesini alır. |
> | Eylem | Microsoft. Özellikler/sağlayıcılar/Özellikler/okuma | Belirli bir kaynak sağlayıcısındaki bir aboneliğin özelliğini alır. |
> | Eylem | Microsoft. Özellikler/sağlayıcılar/Özellikler/kaydet/eylem | Bir abonelik için özelliği belirli bir kaynak sağlayıcısına kaydeder. |
> | Eylem | Microsoft. Özellikler/sağlayıcılar/Özellikler/kayıt kaldırma/eylem | Belirli bir kaynak sağlayıcısındaki bir aboneliğin özelliğinin kaydını siler. |
> | Eylem | Microsoft. Features/Register/Action | Bir aboneliğin özelliğini kaydeder. |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. GuestConfiguration/Guestconfigurationatamaları/silme | Konuk yapılandırma atamasını silin. |
> | Eylem | Microsoft. GuestConfiguration/Guestconfigurationatamaları/okuma | Konuk yapılandırma atamasını al. |
> | Eylem | Microsoft. GuestConfiguration/Guestconfigurationatamaları/raporlar/okuma | Konuk yapılandırma atama raporunu al. |
> | Eylem | Microsoft. GuestConfiguration/Guestconfigurationatamaları/yazma | Yeni Konuk yapılandırma ataması oluşturun. |
> | Eylem | Microsoft. GuestConfiguration/işlemler/okuma | Microsoft. GuestConfiguration kaynak sağlayıcısı için işlemleri alır |
> | Eylem | Microsoft. GuestConfiguration/Register/Action | Microsoft. GuestConfiguration kaynak sağlayıcısı için aboneliği kaydeder. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. HDInsight/kümeler/uygulamalar/Sil | HDInsight kümesi için uygulamayı silme |
> | Eylem | Microsoft. HDInsight/kümeler/uygulamalar/okuma | HDInsight kümesi için uygulama al |
> | Eylem | Microsoft. HDInsight/kümeler/uygulamalar/yazma | HDInsight kümesi için uygulama oluşturma veya güncelleştirme |
> | Eylem | Microsoft. HDInsight/kümeler/changerdpsetting/Action | HDInsight kümesi için RDP ayarını değiştir |
> | Eylem | Microsoft. HDInsight/kümeler/konfigürasyonlar/eylem | HDInsight küme yapılandırmasını güncelleştirme |
> | Eylem | Microsoft. HDInsight/kümeler/konfigürasyonlar/eylem | HDInsight küme yapılandırmasını al |
> | Eylem | Microsoft. HDInsight/kümeler/yapılandırma/okuma | HDInsight küme yapılandırmasını al |
> | Eylem | Microsoft. HDInsight/kümeler/Sil | HDInsight kümesini silme |
> | Eylem | Microsoft. HDInsight/kümeler/uzantılar/Sil | HDInsight kümesi için küme uzantısını Sil |
> | Eylem | Microsoft. HDInsight/kümeler/uzantılar/okuma | HDInsight kümesi için küme uzantısı Al |
> | Eylem | Microsoft. HDInsight/kümeler/uzantılar/yazma | HDInsight kümesi için küme uzantısı oluştur |
> | Eylem | Microsoft. HDInsight/kümeler/getGatewaySettings/Action | HDInsight kümesi için ağ geçidi ayarlarını al |
> | Eylem | Microsoft. HDInsight/kümeler/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Kaynak HDInsight kümesi için tanılama ayarını alır |
> | Eylem | Microsoft. HDInsight/kümeler/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Kaynak HDInsight kümesi için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. HDInsight/kümeler/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | HDInsight kümesi için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. HDInsight/kümeler/okuma | HDInsight kümesiyle ilgili ayrıntıları al |
> | Eylem | Microsoft. HDInsight/kümeler/roller/yeniden boyutlandır/eylem | HDInsight kümesini yeniden boyutlandırma |
> | Eylem | Microsoft. HDInsight/kümeler/updateGatewaySettings/Action | HDInsight kümesi için ağ geçidi ayarlarını güncelleştirme |
> | Eylem | Microsoft. HDInsight/kümeler/yazma | HDInsight kümesi oluşturma veya güncelleştirme |
> | Eylem | Microsoft. HDInsight/konumlar/yetenekler/okuma | Abonelik yeteneklerini al |
> | Eylem | Microsoft. HDInsight/Locations/Checknameavaılabılıty/Read | Ad kullanılabilirliğini denetle |
> | Eylem | Microsoft. HDInsight/Register/ACTION | Abonelik için HDInsight kaynak sağlayıcısını Kaydet |
> | Eylem | Microsoft. HDInsight/Unregister/eylem | Abonelik için HDInsight kaynak sağlayıcısı kaydını sil |

## <a name="microsoftimportexport"></a>Microsoft. ımportexport

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ımportexport/işler/Sil | Var olan bir işi siler. |
> | Eylem | Microsoft. ımportexport/Jobs/listBitLockerKeys/Action | Belirtilen iş için BitLocker anahtarlarını alır. |
> | Eylem | Microsoft. ımportexport/Jobs/Read | Belirtilen işin özelliklerini alır veya işlerin listesini döndürür. |
> | Eylem | Microsoft. ımportexport/işler/yazma | Belirtilen parametrelere sahip bir iş oluşturur veya belirtilen iş için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. ımportexport/konumlar/okuma | Belirtilen konum için özellikleri alır veya konumların listesini döndürür. |
> | Eylem | Microsoft. ımportexport/işlemler/okuma | Kaynak sağlayıcısı tarafından desteklenen işlemleri alır. |
> | Eylem | Microsoft. ımportexport/Register/Action | İçeri/dışarı aktarma kaynak sağlayıcısı için aboneliği kaydeder ve içeri/dışarı aktarma işlerinin oluşturulmasına izin verir. |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Insights/ActionGroups/Delete | Bir eylem grubunu silme |
> | Eylem | Microsoft. Insights/ActionGroups/Read | Bir eylem grubunu okuyun |
> | Eylem | Microsoft. Insights/ActionGroups/Write | Bir eylem grubu oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Insights/ActivityLogAlerts/etkin/eylem | Etkinlik günlüğü uyarısı etkinleştirildi |
> | Eylem | Microsoft. Insights/ActivityLogAlerts/Delete | Etkinlik günlüğü uyarısını silme |
> | Eylem | Microsoft. Insights/ActivityLogAlerts/Read | Etkinlik günlüğü uyarısını okuma |
> | Eylem | Microsoft. Insights/ActivityLogAlerts/Write | Etkinlik günlüğü uyarısı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Insights/AlertRules/Activated/eylem | Klasik ölçüm uyarısı etkinleştirildi |
> | Eylem | Microsoft. Insights/AlertRules/Delete | Klasik ölçüm uyarısını silme |
> | Eylem | Microsoft. Insights/AlertRules/olaylar/okuma | Klasik ölçüm uyarısı olayını okuyun |
> | Eylem | Microsoft. Insights/AlertRules/Read | Klasik ölçüm uyarısını okuyun |
> | Eylem | Microsoft. Insights/AlertRules/çözüldü/Action | Klasik ölçüm uyarısı çözümlendi |
> | Eylem | Microsoft. Insights/AlertRules/kısıt/eylem | Klasik ölçüm uyarı kuralı kısıtlandı |
> | Eylem | Microsoft. Insights/AlertRules/Write | Klasik ölçüm uyarısı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Insights/oto Scalesettings/Delete | Otomatik ölçeklendirme ayarını silme |
> | Eylem | Microsoft. Insights/oto Scalesettings/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak tanılama ayarını oku |
> | Eylem | Microsoft. Insights/oto Scalesettings/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak tanılama ayarı oluştur veya güncelleştir |
> | Eylem | Microsoft. Insights/oto Scalesettings/Providers/Microsoft. Insights/logDefinitions/Read | Günlük tanımlarını oku |
> | Eylem | Microsoft. Insights/oto Scalesettings/Providers/Microsoft. Insights/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | Eylem | Microsoft. Insights/oto Scalesettings/Read | Otomatik ölçeklendirme ayarını oku |
> | Eylem | Microsoft. Insights/oto Scalesettings/Scaleaşağı/Action | Otomatik ölçeklendirme ölçeği azaltma başlatıldı |
> | Eylem | Microsoft. Insights/oto Scalesettings/ScaledownResult/Action | Otomatik ölçeklendirme ölçeği azaltma tamamlandı |
> | Eylem | Microsoft. Insights/oto Scalesettings/Scaleup/Action | Otomatik ölçeklendirme ölçeği artırma başlatıldı |
> | Eylem | Microsoft. Insights/oto Scalesettings/ScaleupResult/Action | Otomatik ölçeklendirme ölçeği tamamlandı |
> | Eylem | Microsoft. Insights/oto Scalesettings/Write | Otomatik ölçeklendirme ayarı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Öngörüler/taban çizgisi/okuma | Ölçüm temelini okuma (Önizleme) |
> | Eylem | Microsoft. Insights/CalculateBaseline/Read | Ölçüm değerleri için taban çizgisini hesapla (Önizleme) |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticsitems/Delete | Application Insights Analytics öğesini silme |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticsitems/okuma | Application Insights Analytics öğesi okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticsitems/yazma | Application Insights Analytics öğesi yazma |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticstables/Action | Application Insights Analytics tablo eylemi |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticstables/Delete | Application Insights Analytics tablo şemasını silme |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticstables/Read | Application Insights Analytics tablo şemasını okuma |
> | Eylem | Microsoft. Insights/bileşenler/analiz Ticstables/Write | Application Insights Analytics tablo şeması yazma |
> | Eylem | Microsoft. Insights/bileşenler/ek açıklamalar/Sil | Application Insights ek açıklaması silme |
> | Eylem | Microsoft. Insights/bileşenler/ek açıklamalar/okuma | Application Insights ek açıklaması okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/ek açıklamalar/yazma | Application Insights ek açıklaması yazma |
> | Eylem | Microsoft. Insights/bileşenler/API/okuma | Application Insights bileşen verileri API 'SI okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/ApiKeys/ACTION | Application Insights API anahtarı oluşturma |
> | Eylem | Microsoft. Insights/bileşenler/ApiKeys/Delete | Application Insights API anahtarını silme |
> | Eylem | Microsoft. Insights/bileşenler/ApiKeys/Read | Application Insights API anahtarı okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/BillingPlanForComponent/Read | Application Insights bileşeni için faturalandırma planı okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/CurrentBillingFeatures/Read | Application Insights bileşeni için geçerli faturalandırma özellikleri okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/CurrentBillingFeatures/Write | Application Insights bileşeni için geçerli faturalandırma özellikleri yazılıyor |
> | Eylem | Microsoft. Insights/bileşenler/Günlükcapulaşıldı/Action | Application Insights bileşeni için günlük Cap 'e ulaşıldı |
> | Eylem | Microsoft. Insights/bileşenler/Günlükcapwarningthresholdulaşıldı/Action | Application Insights bileşeni için günlük uç uyarısı eşiğine ulaşıldı |
> | Eylem | Microsoft. Insights/Components/Defaultworkıtemconfig/Read | Application Insights varsayılan bir ALM tümleştirme yapılandırması okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/Sil | Bir Application Insights bileşen yapılandırmasını silme |
> | Eylem | Microsoft. Insights/bileşenler/olaylar/okuma | OData sorgu biçimini kullanarak Application Insights günlükleri al |
> | Eylem | Microsoft. Insights/bileşenler/ExportConfiguration/eylem | Ayarları dışarı aktarma eylemi Application Insights |
> | Eylem | Microsoft. Insights/bileşenler/ExportConfiguration/Delete | Application Insights dışarı aktarma ayarları siliniyor |
> | Eylem | Microsoft. Insights/bileşenler/ExportConfiguration/Read | Application Insights dışarı aktarma ayarları okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/ExportConfiguration/Write | Application Insights dışarı aktarma ayarları yazılıyor |
> | Eylem | Microsoft. Insights/bileşenler/ExtendQueries/Read | Application Insights bileşeni genişletilmiş sorgu sonuçları okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/sık kullanılanlar/Sil | Application Insights sık kullanılanı silme |
> | Eylem | Microsoft. Insights/bileşenler/sık kullanılanlar/okuma | Sık kullanılan Application Insights okuma |
> | Eylem | Microsoft. Insights/bileşenler/sık kullanılanlar/yaz | Sık kullanılan Application Insights yazma |
> | Eylem | Microsoft. Insights/bileşenler/FeatureCapabilities/okuma | Application Insights bileşen özelliği özelliklerini okuma |
> | Eylem | Microsoft. Insights/bileşenler/GetAvailableBillingFeatures/Read | Application Insights bileşeni kullanılabilir faturalama özelliklerini okuma |
> | Eylem | Microsoft. Insights/bileşenler/GetToken/Read | Application Insights bileşen belirtecini okuma |
> | Eylem | Microsoft. Insights/bileşenler/MetricDefinitions/okuma | Application Insights bileşeni ölçüm tanımlarını okuma |
> | Eylem | Microsoft. Insights/bileşenler/ölçümler/okuma | Application Insights bileşen ölçümlerini okuma |
> | Eylem | Microsoft. Öngörüler/bileşenler/taşıma/eylem | Application Insights bileşenini başka bir kaynak grubuna veya aboneliğe taşıma |
> | Eylem | Microsoft. Insights/bileşenler/MyAnalyticsItems/Delete | Application Insights kişisel analiz öğesini silme |
> | Eylem | Microsoft. Insights/bileşenler/MyAnalyticsItems/Read | Application Insights kişisel analitik öğesini okuma |
> | Eylem | Microsoft. Insights/bileşenler/MyAnalyticsItems/Write | Application Insights kişisel analiz öğesi yazma |
> | Eylem | Microsoft. Insights/bileşenler/MyFavorites/Read | Application Insights kişisel sık kullanılanı okuma |
> | Eylem | Microsoft. Insights/bileşenler/Işlemler/okuma | Application Insights uzun süren işlemlerin durumunu al |
> | Eylem | Microsoft. Insights/bileşenler/Pricingplanlar/okuma | Application Insights bileşen fiyatlandırma planı okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/Pricingplanlar/yazma | Application Insights bileşen fiyatlandırma planı yazma |
> | Eylem | Microsoft. Insights/bileşenler/ProactiveDetectionConfigs/Read | Proaktif algılama yapılandırmasını okuma Application Insights |
> | Eylem | Microsoft. Insights/bileşenler/ProactiveDetectionConfigs/Write | Proaktif algılama yapılandırması yazma Application Insights |
> | Eylem | Microsoft. Insights/bileşenler/sağlayıcılar/Microsoft. Insights/MetricDefinitions/okuma | Ölçüm tanımlarını oku |
> | Eylem | Microsoft. Insights/bileşenler/Temizleme/eylem | Application Insights verileri temizleme |
> | Eylem | Microsoft. Insights/bileşenler/sorgu/okuma | Sorguları Application Insights günlüklere göre Çalıştır |
> | Eylem | Microsoft. Insights/bileşenler/QuotaStatus/Read | Application Insights bileşen kota durumu okunuyor |
> | Eylem | Microsoft. Insights/bileşenler/okuma | Bir Application Insights bileşen yapılandırmasını okuma |
> | Eylem | Microsoft. Insights/bileşenler/SyntheticMonitorLocations/Read | Application Insights WebTest konumlarını okuma |
> | Eylem | Microsoft. Insights/bileşenler/Web testleri/okuma | Bir WebTest yapılandırmasını okuma |
> | Eylem | Microsoft. Insights/bileşenler/Workıtemconfigs/Delete | Application Insights ALM tümleştirme yapılandırmasını silme |
> | Eylem | Microsoft. Insights/bileşenler/Workıtemconfigs/Read | Application Insights ALM tümleştirme yapılandırmasını okuma |
> | Eylem | Microsoft. Insights/bileşenler/Workıtemconfigs/Write | Application Insights ALM tümleştirme yapılandırması yazma |
> | Eylem | Microsoft. Insights/bileşenler/yazma | Bir Application Insights bileşen yapılandırmasına yazma |
> | Eylem | Microsoft. Insights/DiagnosticSettings/Delete | Kaynak tanılama ayarını silme |
> | Eylem | Microsoft. Insights/DiagnosticSettings/Read | Kaynak tanılama ayarını oku |
> | Eylem | Microsoft. Insights/DiagnosticSettings/Write | Kaynak tanılama ayarı oluştur veya güncelleştir |
> | Eylem | Microsoft. Insights/EventCategories/okuma | Kullanılabilir etkinlik günlüğü olay kategorilerini oku |
> | Eylem | Microsoft. Insights/eventTypes/digestevents/Read | Yönetim olayı türü özetini oku |
> | Eylem | Microsoft. Insights/eventTypes/Values/Read | Etkinlik günlüğü olaylarını oku |
> | Eylem | Microsoft. Insights/ExtendedDiagnosticSettings/Delete | Ağ akışı günlük tanılama ayarını silme |
> | Eylem | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Ağ akışı günlük tanılama ayarını oku |
> | Eylem | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Ağ akışı günlük tanılama ayarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Insights/ListMigrationDate/Action | Abonelik geçiş tarihini geri al |
> | Eylem | Microsoft. Insights/ListMigrationDate/Read | Abonelik geçiş tarihini geri al |
> | Eylem | Microsoft. Insights/LogDefinitions/okuma | Günlük tanımlarını oku |
> | Eylem | Microsoft. Insights/günlüğe profiller/Sil | Etkinlik günlüğü günlük profilini silme |
> | Eylem | Microsoft. Insights/günlüğe profiller/okuma | Etkinlik günlüğü günlük profilini okuyun |
> | Eylem | Microsoft. Insights/günlüğe profiller/yazma | Etkinlik günlüğü günlük profili oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Insights/logs/Adassessmentönerisi/okuma | Adassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ADReplicationResult/Read | ADReplicationResult tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/Adsecurityassessmentönerisi/okuma | Adsecurityassessmentönerisi tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/uyarı/okuma | Uyarı tablosundan verileri oku |
> | Eylem | Microsoft. Öngörüler/Günlükler/AlertHistory/Read | AlertHistory tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ApplicationInsights/Read | ApplicationInsights tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/AzureActivity/Read | AzureActivity tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/AzureMetrics/Read | AzureMetrics tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/BoundPort/Read | BoundPort tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/CommonSecurityLog/Read | CommonSecurityLog tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ComputerGroup/Read | ComputerGroup tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/ConfigurationChange/Read | ConfigurationChange tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ConfigurationData/Read | ConfigurationData tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Containerımageınventory/Read | Containerımageınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Containerınventory/Read | Containerınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ContainerLog/Read | ContainerLog tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ContainerServiceLog/Read | ContainerServiceLog tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Deviceappkilitleniyor/Read | DeviceAppCrash tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Deviceapplayeniden Başlat/oku | Deviceapplayeniden Başlat tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/DeviceCalendar/okuma | DeviceCalendar tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/Devicectaanup/okuma | Devicectaanup tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/DeviceConnectSession/Read | DeviceConnectSession tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/DeviceEtw/okuma | DeviceEtw tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/DeviceHardwareHealth/Read | DeviceHardwareHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/DeviceHealth/okuma | DeviceHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Devicesinyal/okuma | Devicesinyal tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/DeviceSkypeHeartbeat/Read | DeviceSkypeHeartbeat tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/DeviceSkypeSignIn/Read | DeviceSkypeSignIn tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Devicestaepstate/Read | Devicestaepstate tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/Dnne hatası/okuma | Dılure tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/Dakppresorumluluğun/okunan | Dappreyükümlülük tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/Dhdrivergüvenilirlik/okuma | Dhdrivergüvenirlik tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Dhlogonbaşarısızlıkları/okuma | Dhlogonarızaları tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Dhlogonölçümler/okuma | Dhlogonölçümlerini tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Dhoatık Kıdata/Read | Dhoatık verileri tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Dhosgüvenilirliği/okuma | Dhosgüvenirlik tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/DHWipAppLearning/Read | DHWipAppLearning tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/DnsEvents/okuma | DnsEvents tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Dnsınventory/Read | DnsInventory tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Etwewevent/Read | ETWEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/olay/okuma | Olay tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ExchangeAssessmentRecommendation/Read | ExchangeAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ExchangeOnlineAssessmentRecommendation/Read | ExchangeOnlineAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/sinyal/okuma | Sinyal tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/IISAssessmentRecommendation/Read | IISAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ınboundconnection/Read | Inboundconnection tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Kubenodeınventory/Read | Kubenodeınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Kubepodinvenfer/Read | KubePodInventory tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/LinuxAuditLog/Read | LinuxAuditLog tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAApplication/Read | MAApplication tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/MAApplicationHealth/Read | MAApplicationHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAApplicationHealthAlternativeVersions/Read | MAApplicationHealthAlternativeVersions tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maapplicationhealthsorunlar/okuma | Maapplicationhealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maapplicationınstance/Read | Maapplicationınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maapplicationınstancereadsürekliliği/okuma | Maapplicationınstancereadılmi tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/MAApplicationReadiness/Read | MAApplicationReadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MADeploymentPlan/okuma | MADeploymentPlan tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/Günlükler/MADevice/okuma | MADevice tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MADevicePnPHealth/Read | MADevicePnPHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MADevicePnPHealthAlternativeVersions/Read | MADevicePnPHealthAlternativeVersions tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Madevicepnphealthsorunlar/okuma | Madevicepnphealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/Madevicereadsürekliliği/okuma | Madevicereadtıo tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Madriverınstancereadsürekliliği/okuma | Madriverınstancereadılmi tablosundan verileri oku |
> | Eylem | Microsoft. Öngörüler/Günlükler/MADriverReadiness/Read | MADriverReadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeeklentisi/okuma | Maofficeeklenti tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeaddınhealth/Read | Maofficeaddınhealth tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeaddınhealthsorunlar/okuma | Maofficeaddınhealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeaddınınstance/Read | Maofficeaddınınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeaddınınstancereadsürekliliği/okuma | Maofficeaddınınstancereadılmi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeaddınreadiness/Read | Maofficeaddınreadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeuygulaması/okuma | Maofficeuygulama tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAOfficeAppHealth/Read | MAOfficeAppHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficeappınstance/Read | Maofficeappınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAOfficeAppReadiness/Read | MAOfficeAppReadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficebuilınfo/Read | Maofficebuilınfo tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAOfficeCurrencyAssessment/Read | MAOfficeCurrencyAssessment tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAOfficeCurrencyAssessmentDailyCounts/Read | MAOfficeCurrencyAssessmentDailyCounts tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAOfficeDeploymentStatus/Read | MAOfficeDeploymentStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficemakrosağlığı/okuma | Maofficemakrohealth tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficemakrohealthsorunlar/okuma | Maofficemacrohealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficemakroıssueınstancereadsürekliliği/okuma | Maofficemacroıssueınstancectablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficemakroıssuereadsürekliliği/okuma | Maofficemacroıssuereadılmi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficemakroözeti/okuma | MAOfficeMacroSummary tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAOfficeSuite/Read | MAOfficeSuite tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Maofficesuiteınstance/Read | Maofficesuiteınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/MAProposedPilotDevices/Read | MAProposedPilotDevices tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Mawindowsbuilınfo/Read | Mawindowsbuilınfo tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Mawindowscurrencki/Read | Mawindowscurrencyaskment tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Mawindowscurrencyassessmentgünlükcounts/okuma | MAWindowsCurrencyAssessmentDailyCounts tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/MAWindowsDeploymentStatus/Read | MAWindowsDeploymentStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Mawindowssysreqınstancereadsürekliliği/okuma | Mawindowssysreqınstancereadtıı tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/NetworkMonitoring/Read | NetworkMonitoring tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Officeetkinliği/okuma | OfficeActivity tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/Işlem/okuma | Işlem tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/OutboundConnection/Read | OutboundConnection tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/perf/okuma | Perf tablosundan verileri oku |
> | Eylem | Microsoft. Öngörüler/Günlükler/ProtectionStatus/Read | ProtectionStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/okuma | Tüm günlüklerinizin verilerini okuma |
> | Eylem | Microsoft. Insights/logs/ReservedAzureCommonFields/Read | ReservedAzureCommonFields tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ReservedCommonFields/Read | ReservedCommonFields tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Sccmassessmentönerisi/okuma | Sccmassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/Scomassessmentönerisi/okuma | Scomassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SecurityAlert/Read | SecurityAlert tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SecurityBaseline/okuma | SecurityBaseline tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SecurityBaselineSummary/okuma | SecurityBaselineSummary tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SecurityDetection/okuma | SecurityDetection tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SecurityEvent/Read | SecurityEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ServiceFabricOperationalEvent/Read | ServiceFabricOperationalEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ServiceFabricReliableActorEvent/Read | ServiceFabricReliableActorEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/ServiceFabricReliableServiceEvent/Read | ServiceFabricReliableServiceEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Sfbassessmentönerisi/okuma | Sfbassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SfBOnlineAssessmentRecommendation/Read | SfBOnlineAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SharePointOnlineAssessmentRecommendation/Read | SharePointOnlineAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Spassessmentönerisi/okuma | Spassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Sqlassessmentönerisi/okuma | Sqlassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/SQLQueryPerformance/Read | SQLQueryPerformance tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Syslog/Read | Syslog tablosundan veri okuma |
> | Eylem | Microsoft. Insights/logs/SysmonEvent/Read | SysmonEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Tables. Custom/Read | Herhangi bir özel günlükteki verileri okuma |
> | Eylem | Microsoft. Insights/logs/UAApp/Read | UAApp tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/Uabilgisayar/okuma | UAComputer tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/UAComputerRank/Read | Uıacomputerrank tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/UADriver/okuma | Uıadriver tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/UADriverProblemCodes/Read | Uıadriverproblemcodes tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/UAFeedback/Read | UAFeedback tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/UAHardwareSecurity/Read | Uıahardwaresecurity tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Uaıesitediscovery/Read | Uıaııesitediscovery tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Uaofficeeklentisi/okuma | Uıaofficeaddın tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/UAProposedActionPlan/Read | UAProposedActionPlan tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Uasysreqıssue/Read | Uıasysreqıssue tablosundaki verileri oku |
> | Eylem | Microsoft. Insights/logs/Uayükseltilebilir Dedcomputer/Read | Uayükseltilebilir Dedcomputer tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/güncelleştirme/okuma | Güncelleştirme tablosundan verileri oku |
> | Eylem | Microsoft. Öngörüler/Günlükler/UpdateRunProgress/Read | UpdateRunProgress tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/UpdateSummary/Read | UpdateSummary tablosundan verileri oku |
> | Eylem | Microsoft. Öngörüler/Günlükler/kullanım/okuma | Kullanım tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/W3CIISLog/Read | W3CIISLog tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/WaaSDeploymentStatus/Read | WaaSDeploymentStatus tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/Waasınsiderstatus/Read | Waasınsiderstatus tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/WaaSUpdateStatus/Read | WaaSUpdateStatus tablosundan verileri oku |
> | Eylem | Microsoft. Insights/logs/WDAVStatus/Read | WDAVStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/WDAVThreat/Read | WDAVThreat Table 'dan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Windowsclientassessmentönerisi/okuma | Windowsclientassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/WindowsFirewall/Read | WindowsFirewall tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/WindowsServerAssessmentRecommendation/Read | WindowsServerAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/kablolu veri/okuma | Kablolu veri tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/logs/Wudoaggreg, Status/Read | Wudoaggreg, Status tablosundan verileri okuma |
> | Eylem | Microsoft. Öngörüler/Günlükler/WUDOStatus/okuma | WUDOStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Insights/MetricAlerts/Delete | Ölçüm uyarısını silme |
> | Eylem | Microsoft. Insights/MetricAlerts/okuma | Ölçüm uyarısını okuyun |
> | Eylem | Microsoft. Insights/MetricAlerts/durum/okuma | Ölçüm uyarısı durumunu oku |
> | Eylem | Microsoft. Insights/MetricAlerts/Write | Ölçüm uyarısı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Insights/Metrictemeller/okuma | Ölçüm temellerini oku |
> | Eylem | Microsoft. Insights/MetricDefinitions/Microsoft. Insights/oku | Ölçüm tanımlarını oku |
> | Eylem | Microsoft. Insights/MetricDefinitions/sağlayıcılar/Microsoft. Insights/Read | Ölçüm tanımlarını oku |
> | Eylem | Microsoft. Insights/MetricDefinitions/okuma | Ölçüm tanımlarını oku |
> | Eylem | Microsoft. Insights/Metricnamespaces/Read | Ölçüm ad alanlarını oku |
> | Eylem | Microsoft. Insights/ölçümler/eylem | Ölçüm eylemi |
> | Eylem | Microsoft. Insights/ölçümler/Microsoft. Öngörüler/okuma | Ölçümleri oku |
> | Eylem | Microsoft. Insights/ölçümler/sağlayıcılar/ölçümler/okuma | Ölçümleri oku |
> | Eylem | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | DataAction | Microsoft. Insights/ölçümler/yazma | Ölçümleri yaz |
> | Eylem | Microsoft. Insights/MigrateToNewpricingModel/Action | Aboneliği yeni fiyatlandırma modeline geçir |
> | Eylem | Microsoft. Insights/Işlemler/okuma | Okuma İşlemleri |
> | Eylem | Microsoft. Insights/Register/ACTION | Microsoft Insights sağlayıcısını kaydedin |
> | Eylem | Microsoft. Insights/RollbackToLegacyPricingModel/Action | Aboneliği eski fiyatlandırma modeline geri al |
> | Eylem | Microsoft. Insights/ScheduledQueryRules/Delete | Zamanlanmış sorgu kuralını silme |
> | Eylem | Microsoft. Insights/ScheduledQueryRules/Read | Zamanlanmış bir sorgu kuralı okunuyor |
> | Eylem | Microsoft. Insights/ScheduledQueryRules/Write | Zamanlanmış bir sorgu kuralı yazma |
> | Eylem | Microsoft. Insights/kiracılar/Register/Action | Microsoft Insights sağlayıcısını başlatır |
> | Eylem | Microsoft. Öngörüler/kaydını kaldırma/eylem | Microsoft Insights sağlayıcısını kaydedin |
> | Eylem | Microsoft. Insights/Web testleri/silme | Bir WebTest yapılandırmasını silme |
> | Eylem | Microsoft. Insights/webtests/GetToken/Read | Bir WebTest belirtecini okuma |
> | Eylem | Microsoft. Insights/Web testleri/MetricDefinitions/okuma | Bir WebTest ölçüm tanımlarını okuma |
> | Eylem | Microsoft. Insights/webtests/ölçümler/okuma | Bir WebTest ölçümlerini okuma |
> | Eylem | Microsoft. Insights/Web testleri/okuma | Bir WebTest yapılandırmasını okuma |
> | Eylem | Microsoft. Insights/Web testleri/yazma | Bir WebTest yapılandırmasına yazma |
> | Eylem | Microsoft. Öngörüler/çalışma kitapları/silme | Çalışma kitabını silme |
> | Eylem | Microsoft. Insights/çalışma kitapları/okuma | Çalışma kitabını okuma |
> | Eylem | Microsoft. Insights/çalışma kitapları/yazma | Çalışma kitabı oluşturma veya güncelleştirme |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Intune/diagnosticsettings/Delete | Bir tanılama ayarını silme |
> | Eylem | Microsoft. Intune/diagnosticsettings/Read | Tanılama ayarını okuma |
> | Eylem | Microsoft. Intune/diagnosticsettings/Write | Tanılama ayarı yazma |
> | Eylem | Microsoft. Intune/diagnosticsettingscategories/Read | Tanılama ayarı kategorilerini okuma |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ıotcentral/appTemplates/Action | Azure IoT Central tüm kullanılabilir uygulama şablonlarını alır |
> | Eylem | Microsoft. ıotcentral/Checknameavaılabılıty/Action | IoT Central bir uygulama adının kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. ıotcentral/Checksubdomainavaılabılıty/Action | IoT Central uygulama alt etki alanının kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. ıotcentral/ıotapps/Delete | IoT Central uygulamalarını siler |
> | Eylem | Microsoft. ıotcentral/ıotapps/Read | Tek bir IoT Central uygulaması alır |
> | Eylem | Microsoft. ıotcentral/ıotapps/Write | IoT Central uygulamalar oluşturur veya güncelleştirir |
> | Eylem | Microsoft. ıotcentral/işlemler/okuma | IoT Central uygulamalarda tüm kullanılabilir işlemleri alır |
> | Eylem | Microsoft. ıotcentral/Register/Action | Azure IoT Central kaynak sağlayıcısı için aboneliği kaydetme |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ıotspaces/Graf/Delete | Microsoft. ıotspaces Graph kaynağını siler |
> | Eylem | Microsoft. ıotspaces/Graf/Read | Microsoft. ıotspaces grafik kaynakları alır |
> | Eylem | Microsoft. ıotspaces/Graf/Write | Microsoft. ıotspaces grafik kaynağı oluştur |
> | Eylem | Microsoft. ıotspaces/yazmaç/Action | Kaynak oluşturmayı etkinleştirmek için Microsoft. ıotspaces Graph kaynak sağlayıcısı için aboneliği kaydedin |

## <a name="microsoftkeyvault"></a>Microsoft. Keykasası

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Keykasası/Checknameavaılabılıty/Read | Anahtar Kasası adının geçerli olduğunu ve kullanımda olup olmadığını denetler |
> | Eylem | Microsoft. Keykasası/Silinleults/okuma | Geçici olarak silinen anahtar kasalarının özelliklerini görüntüleyin |
> | Eylem | Microsoft. Keykasası/hsmPools/Delete | Bir HSM havuzunu silme |
> | Eylem | Microsoft. Keykasası/hsmPools/Joinkasa/eylem | Bir HSM havuzuna Anahtar Kasası ekleme |
> | Eylem | Microsoft. Keykasası/hsmPools/Read | Bir HSM havuzunun özelliklerini görüntüleme |
> | Eylem | Microsoft. Keykasası/hsmPools/Write | Yeni bir HSM havuzu oluşturun var olan bir HSM havuzunun özelliklerini güncelleştir |
> | Eylem | Microsoft. Keykasası/konumlar/Silinkaults/Temizleme/eylem | Geçici olarak silinen bir anahtar kasasını temizle |
> | Eylem | Microsoft. Keykasası/konumlar/Silinkaults/okuma | Geçici olarak silinen bir anahtar kasasının özelliklerini görüntüleyin |
> | Eylem | Microsoft. Keykasası/konumlar/Deletevirtualnetworkoralt ağları/eylem | Bir sanal ağ veya alt ağın silinmekte olduğunu Microsoft. Keykasasına bildirir |
> | Eylem | Microsoft. Keykasası/konumlar/operationResults/Read | Uzun bir çalıştırma işleminin sonucunu denetleyin |
> | Eylem | Microsoft. Keykasası/işlemler/okuma | Microsoft. Keykasası kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | Eylem | Microsoft. Keykasası/Register/ACTION | Bir aboneliği kaydeder |
> | Eylem | Microsoft. Keykasası/kaydını kaldırma/eylem | Aboneliğin kaydını siler |
> | Eylem | Microsoft. Keykasası/Vaults/accessPolicies/Write | Mevcut bir erişim ilkesini birleştirerek veya değiştirerek veya bir kasaya yeni bir erişim ilkesi ekleyerek güncelleştirin. |
> | Eylem | Microsoft. Keykasası/kasa/silme | Bir anahtar kasasını silme |
> | Eylem | Microsoft. Keykasası/Vaults/dağıtım/eylem | Azure kaynaklarını dağıttığınızda bir anahtar kasasındaki gizli dizileri erişimine izin sağlar |
> | Eylem | Microsoft. Keykasası/Vaults/eventGridFilters/Delete | Microsoft. Keykasasına Key Vault için bir EventGrid aboneliğinin silinmekte olduğunu bildirir |
> | Eylem | Microsoft. Keykasası/Vaults/eventGridFilters/Read | Microsoft. Keykasasına Key Vault için bir EventGrid aboneliğinin görüntülendiğini bildirir |
> | Eylem | Microsoft. Keykasası/Vaults/eventGridFilters/Write | Microsoft. Keykasasında Key Vault yeni bir EventGrid aboneliğinin oluşturulduğunu bildirir |
> | Eylem | Microsoft. Keykasası/Kasaults/okuma | Anahtar kasasının özelliklerini görüntüleme |
> | Eylem | Microsoft. Keykasası/Kasaults/gizlilikler/Read | Gizli anahtar özelliklerini görüntüleyin, ancak değerini değil. |
> | Eylem | Microsoft. Keykasası/kasa/gizli dizi/yazma | Yeni bir gizli dizi oluşturun veya var olan bir parolanın değerini güncelleştirin. |
> | Eylem | Microsoft. Keykasası/Vaults/yazma | Yeni bir Anahtar Kasası oluşturun veya var olan bir anahtar kasasının özelliklerini güncelleştirin |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. kusto/kümeler/etkinleştir/eylem | Kümeyi başlatır. |
> | Eylem | Microsoft. kusto/kümeler/AttachedDatabaseConfigurations/Delete | Eklenmiş bir veritabanı yapılandırması resourceCopy öğesini siler. |
> | Eylem | Microsoft. kusto/kümeler/AttachedDatabaseConfigurations/Read | Eklenmiş bir veritabanı yapılandırması resourceCopy öğesini okur. |
> | Eylem | Microsoft. kusto/kümeler/AttachedDatabaseConfigurations/Write | Ekli bir veritabanı yapılandırması resourceCopy yazar. |
> | Eylem | Microsoft. kusto/kümeler/Checknameavaılabılıty/Action | Küme adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/Addsorumlularını/eylemi | Veritabanı sorumlularını ekler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/Checknameavaılabılıty/Action | Verilen bir tür için ad kullanılabilirliğini denetler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/veri bağlantıları/silme | Bir veri bağlantıları resourceCopy öğesini siler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/veri bağlantıları/okuma | Bir veri bağlantıları resourceCopy okur. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/veri bağlantıları/yazma | Bir veri bağlantıları resourceCopy yazar. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/DataConnectionValidation/Action | Veritabanı veri bağlantısını doğrular. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/Sil | Bir veritabanı resourceCopy öğesini siler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/EventHubConnections/Delete | Bir olay hub 'ı bağlantıları resourceCopy öğesini siler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/EventHubConnections/Read | Bir olay hub 'ı bağlantıları resourceCopy okur. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/EventHubConnections/Write | Bir olay hub 'ı bağlantıları resourceCopy yazar. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/EventHubConnectionValidation/Action | Veritabanı Olay Hub 'ı bağlantısını doğrular. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/Listsorumlularını/eylemi | Veritabanı sorumlularını listeler. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/okuma | Bir veritabanı resourceCopy öğesini okur. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/Removesorumlular/eylem | Veritabanı sorumlularını kaldırır. |
> | Eylem | Microsoft. kusto/kümeler/veritabanları/yazma | Bir veritabanı resourceCopy yazar. |
> | Eylem | Microsoft. kusto/kümeler/devre dışı bırakma/eylem | Kümeyi sonlandırır. |
> | Eylem | Microsoft. kusto/kümeler/Sil | Bir küme resourceCopy öğesini siler. |
> | Eylem | Microsoft. kusto/kümeler/okuma | Bir küme resourceCopy 'i okur. |
> | Eylem | Microsoft. kusto/kümeler/SKU 'Lar/okuma | Bir küme SKU resourceCopy öğesini okur. |
> | Eylem | Microsoft. kusto/kümeler/Başlat/eylem | Kümeyi başlatır. |
> | Eylem | Microsoft. kusto/kümeler/durdur/eylem | Kümeyi sonlandırır. |
> | Eylem | Microsoft. kusto/kümeler/yazma | Bir küme resourceCopy yazar. |
> | Eylem | Microsoft. kusto/çıkarılabilir Izleme \ veritabanları/eylem | İzleyici veritabanlarını ayırır. |
> | Eylem | Microsoft. kusto/Listsuerdatabases/Action | İzleyici veritabanlarını listeler. |
> | Eylem | Microsoft. kusto/Locations/Checknameavaılabılıty/Action | ResourceCopy adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. kusto/konumlar/operationresults/Read | Operations resourceCopys okur |
> | Eylem | Microsoft. kusto/Işlemler/okuma | Operations resourceCopys okur |
> | Eylem | Microsoft. kusto/Register/ACTION | Abonelik kaydı eylemi |
> | Eylem | Microsoft. kusto/Register/ACTION | Aboneliği kusto kaynak sağlayıcısına kaydeder. |
> | Eylem | Microsoft. kusto/SKU/okuma | Bir SKU resourceCopy öğesini okur. |
> | Eylem | Microsoft. kusto/Unregister/eylem | Kusto kaynak sağlayıcısına aboneliğin kaydını siler. |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. LabServices/labAccounts/CreateLab/eylem | Laboratuvar hesabında laboratuvar oluşturun. |
> | Eylem | Microsoft. LabServices/labAccounts/Delete | Laboratuvar hesaplarını silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Gallerımages/Delete | Galeri görüntülerini silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Gallerımages/Read | Galeri görüntülerini okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Gallerımages/Write | Galeri görüntülerini ekleyin veya değiştirin. |
> | Eylem | Microsoft. LabServices/labAccounts/GetRegionalAvailability/eylem | Laboratuvar hesabı altında yapılandırılan her boyut kategorisi için bölgesel kullanılabilirlik bilgilerini al |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/AddUsers/Action | Laboratuvara Kullanıcı ekleme |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/Delete | Laboratuvarları silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Delete | Ortam ayarını silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ortamlar/Sil | Ortamları silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ortamlar/okuma | Ortamları okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ortamlar/ResetPassword/Action | Bir ortamdaki Kullanıcı parolasını sıfırlar |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ortamlar/start/Action | Ortamın içindeki tüm kaynakları başlatarak bir ortamı başlatır. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ortamlar/durdur/eylem | Ortamın içindeki tüm kaynakları durdurarak bir ortamı durduruyor |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ortamlar/Write | Ortamları ekleme veya değiştirme. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Yayımla/eylem | Laboratuvar/ortam ayarının geçerli durumuna bağlı olarak bir ortam ayarı için gerekli kaynakları sağlar/hazırlar. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Read | Ortam ayarını okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/Action | Şablon sanal makinesindeki parolayı sıfırlar. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/SaveImage/Action | Geçerli şablon görüntüsünü laboratuvar hesabındaki paylaşılan galeriye kaydeder |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/zamanlamalar/silme | Zamanlamaları silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/zamanlamalar/okuma | Zamanlamaları okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/zamanlamalar/yazma | Zamanlamaları ekleyin veya değiştirin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/start/Action | Şablon içindeki tüm kaynakları başlatarak bir şablonu başlatır. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/durdur/eylem | Şablon içindeki tüm kaynakları durdurarak bir şablonu sonlandırır. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Write | Ortam ayarı ekleyin veya değiştirin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/Read | Labs 'i okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/Sendeposta/eylem | Laboratuvara kayıt bağlantısı ile e-posta gönderin |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/kullanıcılar/Sil | Kullanıcıları silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/kullanıcılar/okuma | Kullanıcıları okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/kullanıcılar/yazma | Kullanıcıları ekleyin veya değiştirin. |
> | Eylem | Microsoft. LabServices/labAccounts/Labs/Write | Labs ekleme veya değiştirme. |
> | Eylem | Microsoft. LabServices/labAccounts/okuma | Laboratuvar hesaplarını okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Sharedgalerileri/silme | Sharedgalerileri silin. |
> | Eylem | Microsoft. LabServices/labAccounts/Sharedgaleriler/okuma | Sharedgaleriler ' i okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/Sharedgaleriler/yazma | Sharedgaleriler ekleyin veya değiştirin. |
> | Eylem | Microsoft. LabServices/labAccounts/sharedImages/Delete | Parça silme. |
> | Eylem | Microsoft. LabServices/labAccounts/sharedImages/Read | Köpkmages 'ları okuyun. |
> | Eylem | Microsoft. LabServices/labAccounts/sharedImages/Write | Parça ekleme veya değiştirme. |
> | Eylem | Microsoft. LabServices/labAccounts/Write | Laboratuvar hesapları ekleyin veya değiştirin. |
> | Eylem | Microsoft. LabServices/konumlar/işlemler/okuma | Okuma işlemleri. |
> | Eylem | Microsoft. LabServices/Register/ACTION | Aboneliği kaydeder |
> | Eylem | Microsoft. LabServices/Users/GetOperationBatchStatus/Action | Toplu işlem durumunu al |
> | Eylem | Microsoft. LabServices/kullanıcılar/GetOperationStatus/Action | Uzun süre çalışan işlemin durumunu alır |
> | Eylem | Microsoft. LabServices/Users/Getpersonal tercihleri/eylemi | Bir kullanıcı için kişisel tercihlerini al |
> | Eylem | Microsoft. LabServices/Users/ListAllEnvironments/ACTION | Kullanıcının tüm ortamlarını listeleyin |
> | Eylem | Microsoft. LabServices/kullanıcılar/Register/Action | Bir kullanıcıyı yönetilen laboratuvara kaydetme |
> | Eylem | Microsoft. LabServices/kullanıcılar/ResetPassword/Action | Bir ortamdaki Kullanıcı parolasını sıfırlar |
> | Eylem | Microsoft. LabServices/Users/StartEnvironment/eylem | Ortamın içindeki tüm kaynakları başlatarak bir ortamı başlatır. |
> | Eylem | Microsoft. LabServices/kullanıcılar/StopEnvironment/Action | Ortamın içindeki tüm kaynakları durdurarak bir ortamı durduruyor |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/sözleşmeleri/silme | Tümleştirme hesabındaki sözleşmeyi siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/sözleşmeleri/listContentCallbackUrl/eylem | Tümleştirme hesabındaki anlaşma içeriği için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/sözleşmeleri/okuma | Tümleştirme hesabındaki sözleşmeyi okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/sözleşmeleri/yazma | Tümleştirme hesabındaki sözleşmeyi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/derlemeler/Sil | Tümleştirme hesabındaki derlemeyi siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/derlemeler/listContentCallbackUrl/eylem | Tümleştirme hesabındaki derleme içeriği için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/derlemeler/okuma | Tümleştirme hesabındaki derlemeyi okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/derlemeler/Write | Tümleştirme hesabındaki derlemeyi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/batchConfigurations/Delete | Tümleştirme hesabındaki toplu yapılandırmayı siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/batchConfigurations/Read | Tümleştirme hesabındaki Batch yapılandırmasını okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/batchConfigurations/Write | Tümleştirme hesabındaki toplu yapılandırmayı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Certificates/Delete | Tümleştirme hesabındaki sertifikayı siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Certificates/Read | Tümleştirme hesabındaki sertifikayı okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Certificates/Write | Tümleştirme hesabındaki sertifikayı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Delete | Tümleştirme hesabını siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/JOIN/Action | Tümleştirme hesabını birleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/listCallbackUrl/eylem | Tümleştirme hesabı için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/listKeyVaultKeys/Action | Anahtar kasasındaki anahtarları alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/logTrackingEvents/eylem | Tümleştirme hesabındaki izleme olaylarını günlüğe kaydeder. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/haritalar/Sil | Tümleştirme hesabındaki eşlemeyi siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Maps/listContentCallbackUrl/eylem | Tümleştirme hesabındaki eşleme içeriği için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Maps/Read | Tümleştirme hesabındaki eşlemeyi okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Maps/Write | Tümleştirme hesabındaki eşlemeyi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/partner/Delete | Tümleştirme hesabındaki ortağı siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Partners/listContentCallbackUrl/eylem | Tümleştirme hesabındaki iş ortağı içeriği için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/partner/Read | Tümleştirme hesabındaki ortağı okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/partner/Write | Tümleştirme hesabındaki iş ortağını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Providers/Microsoft. Insights/logDefinitions/Read | Tümleştirme hesabı günlük tanımlarını okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Read | Tümleştirme hesabını okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/regenerateAccessKey/Action | Erişim anahtarı gizli dizilerini yeniden oluşturur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/şemalar/Delete | Tümleştirme hesabındaki şemayı siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/schemas/listContentCallbackUrl/eylem | Tümleştirme hesabındaki şema içeriği için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/schemas/Read | Tümleştirme hesabındaki şemayı okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/şemalar/Write | Tümleştirme hesabındaki şemayı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Sessions/Delete | Tümleştirme hesabındaki oturumu siler. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Sessions/Read | Tümleştirme hesabındaki Batch yapılandırmasını okur. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Sessions/Write | Tümleştirme hesabındaki oturumu oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationaccounts/Write | Tümleştirme hesabı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamlarını/silme | Tümleştirme hizmeti ortamını siler. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamları/JOIN/Action | Tümleştirme Hizmeti Ortamı birleştirir. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamlarını/Managedap/apiOperations/Read | Tümleştirme hizmeti ortamı yönetilen API işlemini okur. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamlarını/Managedap/Read | Tümleştirme hizmeti ortamı yönetilen API 'sini okur. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamlarını/Operationdurumlarının/Read | Tümleştirme hizmeti ortamı işlem durumlarını okur. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamları/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Tümleştirme hizmeti ortamı ölçüm tanımlarını okur. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamları/okuma | Tümleştirme hizmeti ortamını okur. |
> | Eylem | Microsoft. Logic/ıntegrationserviceortamlar/Write | Tümleştirme hizmeti ortamını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/Locations/iş akışları/recommendOperationGroups/Action | İş akışı önerilir işlem grupları alır. |
> | Eylem | Microsoft. Logic/Locations/iş akışları/Validate/Action | İş akışını doğrular. |
> | Eylem | Microsoft. Logic/işlemler/okuma | İşlemi alır. |
> | Eylem | Microsoft. Logic/Register/ACTION | Belirli bir abonelik için Microsoft. Logic Resource sağlayıcısını kaydeder. |
> | Eylem | Microsoft. Logic/iş akışları/accessKeys/Delete | Erişim anahtarını siler. |
> | Eylem | Microsoft. Logic/iş akışları/accessKeys/List/Action | Erişim anahtarı gizli dizilerini listeler. |
> | Eylem | Microsoft. Logic/iş akışları/accessKeys/Read | Erişim anahtarını okur. |
> | Eylem | Microsoft. Logic/iş akışları/accessKeys/yeniden oluştur/eylem | Erişim anahtarı gizli dizilerini yeniden oluşturur. |
> | Eylem | Microsoft. Logic/iş akışları/accessKeys/Write | Erişim anahtarını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/iş akışları/silme | İş akışını siler. |
> | Eylem | Microsoft. Logic/iş akışları/devre dışı bırakma/eylem | İş akışını devre dışı bırakır. |
> | Eylem | Microsoft. Logic/iş akışları/etkinleştir/eylem | İş akışını izin vermez. |
> | Eylem | Microsoft. Logic/iş akışları/listCallbackUrl/eylem | İş akışı için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/iş akışları/listSwagger/eylem | İş akışı Swagger tanımlarını alır. |
> | Eylem | Microsoft. Logic/iş akışları/taşıma/eylem | Iş akışını var olan abonelik kimliği, kaynak grubu ve/veya adından farklı bir abonelik kimliğine, kaynak grubuna ve/veya ada taşıdıkça. |
> | Eylem | Microsoft. Logic/iş akışları/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | İş akışı tanılama ayarlarını okur. |
> | Eylem | Microsoft. Logic/iş akışları/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | İş akışı tanılama ayarını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Logic/iş akışları/sağlayıcılar/Microsoft. Insights/logDefinitions/okuma | İş akışı günlük tanımlarını okur. |
> | Eylem | Microsoft. Logic/iş akışları/sağlayıcılar/Microsoft. Insights/metricDefinitions/Read | İş akışı ölçüm tanımlarını okur. |
> | Eylem | Microsoft. Logic/iş akışları/okuma | İş akışını okur. |
> | Eylem | Microsoft. Logic/iş akışları/regenerateAccessKey/Action | Erişim anahtarı gizli dizilerini yeniden oluşturur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırma/eylem | İş akışının çalıştırılmasını başlatır. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Actions/Listexpressionizlemeler/eylem | İş akışı çalıştırma eylemi ifade izlemelerini alır. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/eylemler/okuma | İş akışı çalıştırma eylemini okur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Actions/tekrarları/Listexpressionizlemeler/eylem | İş akışı çalıştırma eylemi yineleme ifadesi izlemelerini alır. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Actions/tekrarları/Read | İş akışı çalıştırma eylemi tekrarlamayı okur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Actions/tekrarları/requestHistories/Read | İş akışı çalıştırma yineleme eylemi istek geçmişini okur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Actions/requestHistories/Read | İş akışı çalıştırma eylemi istek geçmişini okur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/eylemler/scoperepetitions/Read | İş akışı çalıştırma eylemi kapsam tekrarlamayı okur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/iptal/eylem | Bir iş akışının çalıştırılmasını iptal eder. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Sil | Bir iş akışının çalıştırılmasını siler. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/işlemler/okuma | İş akışı çalıştırma işlemi durumunu okur. |
> | Eylem | Microsoft. Logic/iş akışları/çalıştırmalar/Read | İş akışı çalıştırmasını okur. |
> | Eylem | Microsoft. Logic/iş akışları/beklet/eylem | İş akışını askıya alır. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/geçmiş/okuma | Tetikleyici geçmişlerini okur. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/geçmiş/yeniden gönder/eylem | Resubmits iş akışı tetikleyicisi. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/listCallbackUrl/eylem | Tetikleyici için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/okuma | Tetikleyiciyi okur. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/sıfırlama/eylem | Tetikleyiciyi sıfırlar. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/çalıştırma/eylem | Tetikleyiciyi yürütür. |
> | Eylem | Microsoft. Logic/iş akışları/Tetikleyiciler/setState/Action | Tetikleyici durumunu ayarlar. |
> | Eylem | Microsoft. Logic/iş akışları/doğrula/eylem | İş akışını doğrular. |
> | Eylem | Microsoft. Logic/iş akışları/sürümler/okuma | İş akışı sürümünü okur. |
> | Eylem | Microsoft. Logic/iş akışları/sürümler/Tetikleyiciler/listCallbackUrl/eylem | Tetikleyici için geri çağırma URL 'sini alır. |
> | Eylem | Microsoft. Logic/iş akışları/yazma | İş akışını oluşturur veya güncelleştirir. |

## <a name="microsoftmachinelearning"></a>Microsoft. Machinöğrenim

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Machinöğrenim/Commitmentplanlar/commitmentAssociations/Move/Action | Tüm Machine Learning taahhüt planı Ilişkilendirmesini taşıyın |
> | Eylem | Microsoft. Machinöğrenim/Commitmentplanlar/commitmentAssociations/okuma | Machine Learning taahhüt planı Ilişkilendirmesini okuyun |
> | Eylem | Microsoft. Machinöğrenim/Commitmentplanlar/silme | Machine Learning taahhüt planını Sil |
> | Eylem | Microsoft. Machinöğrenim/Commitmentplanlar/Birleştir/eylem | Machine Learning taahhüt planına katılarak |
> | Eylem | Microsoft. Machinöğrenim/Commitmentplanlar/okuma | Machine Learning taahhüt planını okuyun |
> | Eylem | Microsoft. Machinöğrenim/Commitmentplanlar/yazma | Machine Learning taahhüt planı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Machinöğrenim/konumlar/operationresults/Read | Machine Learning bir Işlemin sonucunu alın |
> | Eylem | Microsoft. Machinöğrenim/konumlar/operationsstatus/Read | Devam eden Machine Learning Işleminin durumunu al |
> | Eylem | Microsoft. Machinöğrenim/işlemler/okuma | Machine Learning Işlemleri al |
> | Eylem | Microsoft. Machinöğretim/Register/ACTION | Machine Learning Web hizmeti kaynak sağlayıcısı için aboneliği kaydeder ve Web Hizmetleri oluşturmayı sunar. |
> | Eylem | Microsoft. Machinöğrenim/SKU/okuma | Machine Learning taahhüt planı SKU 'Larını al |
> | Eylem | Microsoft. Machinöğrenim/webServices/eylem | Desteklenen bölgeler için bölgesel Web hizmeti özellikleri oluşturma |
> | Eylem | Microsoft. Machinöğrenim/webServices/Delete | Tüm Machine Learning Web hizmetini silme |
> | Eylem | Microsoft. Machinöğrenim/webServices/ListKeys/Read | Machine Learning Web hizmetine anahtar al |
> | Eylem | Microsoft. Machinöğrenim/webServices/Read | Herhangi bir Machine Learning Web hizmetini okuyun |
> | Eylem | Microsoft. Machinöğrenim/webServices/Write | Herhangi bir Machine Learning Web hizmeti oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Machinöğrenim/çalışma alanları/silme | Machine Learning Çalışma Alanı silme |
> | Eylem | Microsoft. Machinöğrenim/çalışma alanları/listworkspacekeys/eylem | Machine Learning Çalışma Alanı için anahtarları listeleyin |
> | Eylem | Microsoft. Machinöğrenim/çalışma alanları/okuma | Machine Learning Çalışma Alanı okuyun |
> | Eylem | Microsoft. Machinöğrenim/çalışma alanları/resyncstoragekeys/eylem | Machine Learning Çalışma Alanı için yapılandırılmış depolama hesabı anahtarlarını yeniden eşitleme |
> | Eylem | Microsoft. Machinöğrenim/çalışma alanları/yazma | Machine Learning Çalışma Alanı oluşturun veya güncelleştirin |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. MachineLearningServices/konumlar/computeoperationsstatus/Read | Belirli bir işlem işleminin durumunu alır |
> | Eylem | Microsoft. MachineLearningServices/konumlar/kullanımlar/okuma | Bir abonelikteki AML işlem kaynakları için kullanım raporu |
> | Eylem | Microsoft. MachineLearningServices/konumlar/vmsizes/Read | Desteklenen VM boyutlarını al |
> | Eylem | Microsoft. MachineLearningServices/Locations/Workspace Operationsstatus/Read | Belirli bir çalışma alanı işleminin durumunu alır |
> | Eylem | Microsoft. MachineLearningServices/Register/Action | Machine Learning Services kaynak sağlayıcısı için aboneliği kaydeder |
> | Eylem | Microsoft. MachineLearningServices/çalışma alanları/hesaplar/Sil | Machine Learning Services çalışma alanındaki işlem kaynaklarını siler |
> | Eylem | Microsoft. MachineLearningServices/Workspaces/hesaplar/listKeys/Action | Machine Learning Services çalışma alanındaki işlem kaynakları için gizli dizileri listeleyin |
> | Eylem | Microsoft. MachineLearningServices/Workspaces/hesaplar/listNodes/Action | Machine Learning Services çalışma alanında işlem kaynağı için düğümleri listeleyin |
> | Eylem | Microsoft. MachineLearningServices/çalışma alanları/hesaplar/okundu | Machine Learning Services çalışma alanlarında işlem kaynaklarını alır |
> | Eylem | Microsoft. MachineLearningServices/çalışma alanları/hesaplar/yaz | Machine Learning Services çalışma alanlarında işlem kaynaklarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. MachineLearningServices/çalışma alanları/silme | Machine Learning Services çalışma alanlarını siler |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/denemeleri/Delete | Machine Learning Services çalışma alanlarında denemeleri siler |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/denemeleri/Read | Machine Learning Services çalışma alanlarında denemeleri alır |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/denemeleri/Write | Machine Learning Services çalışma alanlarında denemeleri oluşturur veya güncelleştirir |
> | Eylem | Microsoft. MachineLearningServices/Workspaces/listKeys/Action | Machine Learning Services çalışma alanı için gizli dizileri listeleme |
> | Eylem | Microsoft. MachineLearningServices/çalışma alanları/okuma | Machine Learning Services çalışma alanlarını alır |
> | Eylem | Microsoft. MachineLearningServices/çalışma alanları/yazma | Machine Learning Services çalışma alanı oluşturur veya güncelleştirir |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Managedıdentity/kimlikler/okuma | Mevcut bir sistem tarafından atanmış kimliği alır |
> | Eylem | Microsoft. Managedıdentity/Register/Action | Yönetilen kimlik kaynak sağlayıcısı için aboneliği kaydeder |
> | Eylem | Microsoft. Managedıdentity/Useratandıdentities/ata/eylem | Mevcut kullanıcı tarafından atanan kimliği bir kaynağa atamak için RBAC eylemi |
> | Eylem | Microsoft. Managedıdentity/Useratandıdentities/Delete | Mevcut kullanıcı tarafından atanan bir kimliği siler |
> | Eylem | Microsoft. Managedıdentity/Useratandıdentities/Read | Mevcut kullanıcı tarafından atanan kimliği alır |
> | Eylem | Microsoft. Managedıdentity/Useratandıdentities/Write | Yeni Kullanıcı tarafından atanmış bir kimlik oluşturur veya var olan bir kullanıcı tarafından atanan kimlikle ilişkili etiketleri güncelleştirir |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ManagedServices/Pazar Placeryumuristrationdefinitions/okuma | Yönetilen hizmetler kayıt tanımlarının bir listesini alır. |
> | Eylem | Microsoft. ManagedServices/işlemler/okuma | Yönetilen hizmet işlemlerinin bir listesini alır. |
> | Eylem | Microsoft. ManagedServices/Operationdurumlar/okuma | Kaynak için işlem durumunu okur. |
> | Eylem | Microsoft. ManagedServices/Register/ACTION | Yönetilen hizmetlere kaydolun. |
> | Eylem | Microsoft. ManagedServices/Registrationatamaları/silme | Yönetilen hizmetler kayıt atamasını kaldırır. |
> | Eylem | Microsoft. ManagedServices/Registrationatamaları/okuma | Yönetilen hizmetler kayıt atamalarının bir listesini alır. |
> | Eylem | Microsoft. ManagedServices/Registrationatamaları/yazma | Yönetilen hizmetler kayıt atamasını ekleyin veya değiştirin. |
> | Eylem | Microsoft. ManagedServices/registrationDefinitions/Sil | Yönetilen hizmetler kayıt tanımını kaldırır. |
> | Eylem | Microsoft. ManagedServices/registrationDefinitions/okuma | Yönetilen hizmetler kayıt tanımlarının bir listesini alır. |
> | Eylem | Microsoft. ManagedServices/registrationDefinitions/yaz | Yönetilen hizmetler kayıt tanımı ekleyin veya değiştirin. |
> | Eylem | Microsoft. ManagedServices/kaydını kaldırma/eylem | Yönetilen hizmetlerden kayıt silme. |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Management/Checknameavaılabılıty/Action | Belirtilen yönetim grubu adının geçerli ve benzersiz olup olmadığını denetler. |
> | Eylem | Microsoft. Management/getEntities/Action | Kimliği doğrulanmış kullanıcı için tüm varlıkları (Yönetim Grupları, abonelikler vb.) listeleyin. |
> | Eylem | Microsoft. Management/managementGroups/Delete | Yönetim grubunu silin. |
> | Eylem | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | Eylem | Microsoft. Management/managementGroups/abonelikler/Sil | Aboneliği yönetim grubundan kaldır. |
> | Eylem | Microsoft. Management/managementGroups/abonelikler/Write | Mevcut aboneliği yönetim grubuyla ilişkilendirir. |
> | Eylem | Microsoft. Management/managementGroups/Write | Bir yönetim grubu oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Management/Register/ACTION | Belirtilen aboneliği Microsoft. Management ile kaydet |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | DataAction | Microsoft. Maps/hesaplar/veri/okuma | Haritalar hesabına veri okuma erişimi verir. |
> | Eylem | Microsoft. Maps/hesaplar/Sil | Haritalar hesabını silin. |
> | Eylem | Microsoft. Haritalar/hesaplar/eventGridFilters/Delete | Event Grid filtresini silme |
> | Eylem | Microsoft. Maps/accounts/Eventgridfilter/Read | Event Grid filtresi al |
> | Eylem | Microsoft. Maps/accounts/Eventgridfilter/Write | Event Grid filtresi oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Haritalar/hesaplar/listKeys/ACTION | Liste haritaları hesap anahtarları |
> | Eylem | Microsoft. Maps/hesaplar/okuma | Haritalar hesabı alın. |
> | Eylem | Microsoft. Maps/accounts/regenerateKey/ACTION | Yeni haritalar hesabı birincil veya ikincil anahtar oluştur |
> | Eylem | Microsoft. Maps/hesaplar/yazma | Haritalar hesabı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Maps/işlemler/okuma | Sağlayıcı işlemlerini okuyun |
> | Eylem | Microsoft. Maps/Register/ACTION | Sağlayıcıyı Kaydet |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Market/offerTypes/yayımcılar/teklifler/planlar/anlaşmalar/okuma | Bir sözleşme döndürür. |
> | Eylem | Microsoft. Market/offerTypes/yayımcılar/teklifler/planlar/anlaşmalar/yazma | İmzalı bir sözleşmeyi kabul eder. |
> | Eylem | Microsoft. Market/offerTypes/yayımcılar/teklifler/planlar/configs/ımportımage/Action | Son kullanıcının ACR 'ye bir görüntü aktarır. |
> | Eylem | Microsoft. Market/offerTypes/yayımcılar/teklifler/planlar/configs/Read | Bir yapılandırma döndürür. |
> | Eylem | Microsoft. Market/offerTypes/yayımcılar/teklifler/planlar/configs/Write | Bir yapılandırmayı kaydeder. |
> | Eylem | Microsoft. Market/Register/ACTION | Microsoft. Market kaynak sağlayıcısını abonelikte kaydeder. |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. MarketplaceApps/ClassicDevServices/Delete | Klasik bir geliştirme hizmeti kaynağında SILME işlemi yapar. |
> | Eylem | Microsoft. MarketplaceApps/ClassicDevServices/Listgizlilikler/Action | Klasik bir geliştirme hizmeti kaynak yönetimi anahtarları alır. |
> | Eylem | Microsoft. MarketplaceApps/Classıdevservices/listSingleSignOnToken/Action | Klasik bir geliştirme hizmeti için çoklu oturum açma URL 'sini alır. |
> | Eylem | Microsoft. MarketplaceApps/ClassicDevServices/Read | Klasik bir geliştirme hizmetinde bir GET işlemi yapar. |
> | Eylem | Microsoft. MarketplaceApps/ClassicDevServices/regenerateKey/ACTION | Klasik bir geliştirme hizmeti kaynak yönetimi anahtarları oluşturur. |
> | Eylem | Microsoft. MarketplaceApps/Işlemler/okuma | Tüm kaynak türleri için işlemleri okuyun. |

## <a name="microsoftmarketplaceordering"></a>Microsoft. Marketplacesıralaması

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Marketplacesıralaması/sözleşmeleri/teklifler/planlar/iptal/eylem | Belirli bir Market öğesi için bir sözleşmeyi iptal etme |
> | Eylem | Microsoft. Marketplacesıralaması/sözleşmeleri/teklifler/planlar/okuma | Belirli bir Market öğesi için anlaşma döndürme |
> | Eylem | Microsoft. Marketplacesıralaması/sözleşmeleri/teklifler/planlar/imzala/eylem | Belirli bir Market öğesi için sözleşme imzalama |
> | Eylem | Microsoft. Marketplacesıralaması/sözleşmeleri/okumak | Belirtilen abonelik altındaki tüm anlaşmaları döndür |
> | Eylem | Microsoft. Marketplacesıralaması/offertypes/yayımcılar/teklifler/planlar/anlaşmalar/okuma | Belirli bir market sanal makine öğesi için anlaşma alın |
> | Eylem | Microsoft. Marketplacesıralaması/offertypes/yayımcılar/teklifler/planlar/anlaşmalar/yazma | Belirli bir market sanal makine öğesi için sözleşmeyi imzala veya Iptal etme |
> | Eylem | Microsoft. Marketplacesıralaması/işlemler/okuma | API 'de olası tüm işlemleri listeleme |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Media/checknameavaılabılıty/Action | Media Services hesap adının kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. Media/Locations/Checknameavaılabılıty/Action | Media Services hesap adının kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. Media/mediaservices/accountfilters/Delete | Hesap filtrelerini Sil |
> | Eylem | Microsoft. Media/mediaservices/accountfilters/okuma | Tüm hesap filtrelerini okuyun |
> | Eylem | Microsoft. Media/mediaservices/accountfilters/Write | Hesap Filtresi Oluştur veya güncelleştir |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/assetfilters/Delete | Tüm varlık filtrelerini Sil |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/assetfilters/Read | Tüm varlık filtrelerini okuyun |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/assetfilters/Write | Herhangi bir varlık Filtresi Oluştur veya güncelleştir |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/Sil | Herhangi bir varlığı silme |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/getEncryptionKey/Action | Varlık şifreleme anahtarını al |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/listContainerSas/eylem | Varlık kapsayıcısı SAS URL 'Lerini Listele |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/Liststreamingkonumlandırıcı/eylem | Varlık için akış Bulıcıları Listele |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/okuma | Herhangi bir varlığı okuyun |
> | Eylem | Microsoft. Media/mediaservices/varlıklar/yazma | Herhangi bir varlık oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/contentKeyPolicies/Delete | Içerik anahtarı Ilkesini silme |
> | Eylem | Microsoft. Media/mediaservices/contentKeyPolicies/Getpolicypropertieswithgizlilikler/Action | Gizli dizileri olan Ilke özelliklerini al |
> | Eylem | Microsoft. Media/mediaservices/contentKeyPolicies/Read | Herhangi bir Içerik anahtarı Ilkesini okuyun |
> | Eylem | Microsoft. Media/mediaservices/contentKeyPolicies/Write | Içerik anahtar Ilkesi oluştur veya güncelleştir |
> | Eylem | Microsoft. Media/mediaservices/Delete | Media Services hesabı silme |
> | Eylem | Microsoft. Media/mediaservices/eventGridFilters/Delete | Event Grid filtresini silme |
> | Eylem | Microsoft. Media/mediaservices/eventGridFilters/Read | Herhangi bir Event Grid filtresini okuyun |
> | Eylem | Microsoft. Media/mediaservices/eventGridFilters/Write | Herhangi bir Event Grid filtresi oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/liveEventOperations/Read | Canlı olay Işlemlerini okuyun |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Delete | Canlı etkinliği silme |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Liveçıktıları/Delete | Canlı çıktıyı silme |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Liveçıktıları/Read | Canlı çıktıyı okuyun |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Liveçıktıları/Write | Canlı çıkış oluştur veya güncelleştir |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Read | Tüm canlı olayları okuyun |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Reset/Action | Canlı olay Işlemlerini sıfırlayın |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Başlat/eylem | Tüm canlı olay Işlemlerini başlatın |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/durdur/eylem | Canlı olay Işlemlerini durdur |
> | Eylem | Microsoft. Media/mediaservices/liveEvents/Write | Canlı bir olay oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/liveOutputOperations/Read | Tüm canlı çıktı Işlemlerini okuyun |
> | Eylem | Microsoft. Media/mediaservices/okuma | Tüm Media Services hesaplarını okuyun |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpointOperations/okuma | Tüm akış uç noktası Işlemlerini okuyun |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpoints/Delete | Tüm akış uç noktasını sil |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpoints/Read | Tüm akış uç noktasını okuyun |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpoints/ölçek/eylem | Tüm akış uç noktası Işlemlerini ölçeklendirme |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpoints/start/Action | Tüm akış uç noktası Işlemlerini başlatma |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpoints/durdur/eylem | Tüm akış uç noktası Işlemlerini durdur |
> | Eylem | Microsoft. Media/mediaservices/streamingEndpoints/Write | Herhangi bir akış uç noktası oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/Streamingkonumlandırıcı/silme | Tüm akış bulucuyu silme |
> | Eylem | Microsoft. Media/mediaservices/Streamingkonumlandırıcı/listContentKeys/ACTION | Liste Içerik anahtarları |
> | Eylem | Microsoft. Media/mediaservices/Streamingkonumlandırıcı/listPaths/Action | Liste yolları |
> | Eylem | Microsoft. Media/mediaservices/Streamingkonumlandırıcı/okuma | Herhangi bir akış bulucuyu okuyun |
> | Eylem | Microsoft. Media/mediaservices/Streamingkonumlandırıcı/yazma | Herhangi bir akış Bulucu oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Media/mediaservices/streamingPolicies/Delete | Tüm akış Ilkelerini silme |
> | Eylem | Microsoft. Media/mediaservices/streamingPolicies/okuma | Herhangi bir akış Ilkesini okuyun |
> | Eylem | Microsoft. Media/mediaservices/streamingPolicies/Write | Herhangi bir akış Ilkesi oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/syncStorageKeys/Action | Bağlı bir Azure depolama hesabı için depolama anahtarlarını eşitler |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/silme | Herhangi bir dönüşümü Sil |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/işler/cancelJob/Action | Işi iptal et |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/işler/Sil | Herhangi bir Işi silme |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/işler/okuma | Herhangi bir Işi okuyun |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/işler/yazma | Herhangi bir Işi oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/okuma | Herhangi bir dönüşümü okuyun |
> | Eylem | Microsoft. Media/mediaservices/dönüşümler/yazma | Herhangi bir dönüşüm oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/mediaservices/Write | Media Services hesabı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Media/işlemler/okuma | Kullanılabilir Işlemleri al |
> | Eylem | Microsoft. Media/Register/ACTION | Media Services kaynak sağlayıcısı için aboneliği kaydeder ve Media Services hesaplarının oluşturulmasını sunar |
> | Eylem | Microsoft. Media/Unregister/eylem | Media Services kaynak sağlayıcısı için aboneliğin kaydını siler |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Migrate/assessmentprojects/assessmentOptions/Read | Verilen konumda kullanılabilen değerlendirme seçeneklerini alır |
> | Eylem | Microsoft. Migrate/assessmentprojects/değerlendirmeler/Read | Bir proje içindeki değerlendirmeleri listeler |
> | Eylem | Microsoft. Migrate/assessmentprojects/Delete | Değerlendirme projesini siler |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/değerlendirmeler/assessedmachines/Read | Değerlendirilen bir makinenin özelliklerini al |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/değerlendirmeler/silme | Bir değerlendirmeyi siler |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/değerlendirmeler/downloadurl/eylem | Bir değerlendirme raporunun URL 'sini indirir |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/değerlendirmeler/okuma | Bir değerlendirmenin özelliklerini alır |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/değerlendirmeler/yazma | Yeni bir değerlendirme oluşturur veya var olan bir değerlendirmeyi güncelleştirir |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/Sil | Bir grubu siler |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/okuma | Bir grubun özelliklerini al |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/updateMachines/eylem | Makine ekleyerek veya kaldırarak grup güncelleştirme |
> | Eylem | Microsoft. Migrate/assessmentprojects/gruplar/yaz | Yeni bir grup oluşturur veya var olan bir grubu güncelleştirir |
> | Eylem | Microsoft. Migrate/assessmentprojects/hiper sanal toplayıcılar/Sil | HyperV toplayıcısını siler |
> | Eylem | Microsoft. Migrate/assessmentprojects/hypervtoplayıcıları/Read | HyperV toplayıcısının özelliklerini alır |
> | Eylem | Microsoft. Migrate/assessmentprojects/hypervtoplayıcıları/Write | Yeni bir HyperV toplayıcısı oluşturur veya var olan bir HyperV toplayıcıyı güncelleştirir |
> | Eylem | Microsoft. Migrate/assessmentprojects/makineler/okuma | Makinenin özelliklerini alır |
> | Eylem | Microsoft. Migrate/assessmentprojects/Read | Değerlendirme projesinin özelliklerini alır |
> | Eylem | Microsoft. Migrate/assessmentprojects/vmwarecollectors/Delete | VMware toplayıcısını siler |
> | Eylem | Microsoft. Migrate/assessmentprojects/vmwarecollectors/Read | VMware toplayıcısının özelliklerini alır |
> | Eylem | Microsoft. Migrate/assessmentprojects/vmwarecollectors/Write | Yeni bir VMware toplayıcısı oluşturur veya var olan bir VMware toplayıcıyı güncelleştirir |
> | Eylem | Microsoft. Migrate/assessmentprojects/Write | Yeni bir değerlendirme projesi oluşturur veya var olan bir değerlendirme projesini güncelleştirir |
> | Eylem | Microsoft. Migrate/konumlar/assessmentOptions/Read | Verilen konumda kullanılabilen değerlendirme seçeneklerini alır |
> | Eylem | Microsoft. Migrate/Locations/checknameavaılabılıty/Action | Verilen konumdaki belirtilen aboneliğin kaynak adının kullanılabilirliğini denetler |
> | Eylem | Microsoft. Migrate/migrateprojects/Databaseınstances/Read | Bir veritabanı örneğinin özelliklerini alır |
> | Eylem | Microsoft. Migrate/migrateprojects/veritabanları/okuma | Bir veritabanının özelliklerini alır |
> | Eylem | Microsoft. Migrate/migrateprojects/Delete | Bir geçişi projeyi siler |
> | Eylem | Microsoft. Migrate/migrateprojeler/makineler/okuma | Makinenin özelliklerini alır |
> | Eylem | Microsoft. Migrate/migrateprojects/MigrateEvents/Delete | Geçiş olayını siler |
> | Eylem | Microsoft. Migrate/migrateprojeler/MigrateEvents/okuma | Geçiş olaylarının özelliklerini alır. |
> | Eylem | Microsoft. Migrate/migrateprojects/Read | Projeyi geçir özelliklerini alır |
> | Eylem | Microsoft. Migrate/migrateprojects/RefreshSummary/Action | Projeyi geçir özetini yeniler |
> | Eylem | Microsoft. Migrate/migrateprojects/registerTool/Action | Aracı bir geçiş projesine kaydeder |
> | Eylem | Microsoft. Migrate/migrateprojects/Solutions/cleanupData/Action | Projeyi geçir çözüm verilerini temizle |
> | Eylem | Microsoft. Migrate/migrateprojects/Solutions/Delete | Bir geçiş projesi çözümünü siler |
> | Eylem | Microsoft. Migrate/migrateprojects/Solutions/GetConfig/Action | Proje çözümünü geçir yapılandırmasını alır |
> | Eylem | Microsoft. Migrate/migrateprojects/Solutions/Read | Proje çözümünü geçirme özelliklerini alır |
> | Eylem | Microsoft. Migrate/migrateprojects/Solutions/Write | Yeni bir geçiş projesi çözümü oluşturur veya var olan bir geçiş projesi çözümünü güncelleştirir |
> | Eylem | Microsoft. Migrate/migrateprojects/Write | Yeni bir geçiş projesi oluşturur veya var olan bir geçişi projeyi güncelleştirir |
> | Eylem | Microsoft. Migrate/Işlemler/okuma | Microsoft. Migrate kaynak sağlayıcısı 'nda bulunan işlemleri listeler |
> | Eylem | Microsoft. Migrate/projeler/değerlendirmeler/okuma | Bir proje içindeki değerlendirmeleri listeler |
> | Eylem | Microsoft. Migrate/projeler/Sil | Projeyi siler |
> | Eylem | Microsoft. Migrate/projeler/gruplar/değerlendirmeler/assessedmachines/Read | Değerlendirilen bir makinenin özelliklerini al |
> | Eylem | Microsoft. Migrate/projeler/gruplar/değerlendirmeler/silme | Bir değerlendirmeyi siler |
> | Eylem | Microsoft. Migrate/projeler/gruplar/değerlendirmeler/downloadurl/eylem | Bir değerlendirme raporunun URL 'sini indirir |
> | Eylem | Microsoft. Migrate/projeler/gruplar/değerlendirmeler/okuma | Bir değerlendirmenin özelliklerini alır |
> | Eylem | Microsoft. Migrate/projeler/gruplar/değerlendirmeler/yazma | Yeni bir değerlendirme oluşturur veya var olan bir değerlendirmeyi güncelleştirir |
> | Eylem | Microsoft. Migrate/projeler/gruplar/Sil | Bir grubu siler |
> | Eylem | Microsoft. Migrate/projeler/gruplar/okuma | Bir grubun özelliklerini al |
> | Eylem | Microsoft. Migrate/projeler/gruplar/yaz | Yeni bir grup oluşturur veya var olan bir grubu güncelleştirir |
> | Eylem | Microsoft. Migrate/projeler/anahtarlar/eylem | Proje için paylaşılan anahtarları alır |
> | Eylem | Microsoft. Migrate/projeler/makineler/okuma | Makinenin özelliklerini alır |
> | Eylem | Microsoft. Migrate/projeler/okuma | Projenin özelliklerini alır |
> | Eylem | Microsoft. Migrate/projeler/yazma | Yeni bir proje oluşturur veya var olan bir projeyi güncelleştirir |
> | Eylem | Microsoft. Migrate/Register/ACTION | Aboneliği Microsoft. Migrate kaynak sağlayıcısına kaydeder |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. MixedReality/Register/ACTION | Karma Gerçeklik kaynak sağlayıcısı için bir abonelik kaydeder. |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/ACTION | Uzamsal bağlayıcı oluşturma |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Uzamsal bağlayıcıları Sil |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Özellikler/okuma | Uzamsal Tutturucuların özelliklerini al |
> | Eylem | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Microsoft. MixedReality/spatialAnchorsAccounts için tanılama ayarını alır |
> | Eylem | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Microsoft. MixedReality/spatialAnchorsAccounts için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/metricDefinitions/okuma | Microsoft. MixedReality/spatialAnchorsAccounts için kullanılabilir ölçümleri alır |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/sorgu/okuma | Uzamsal bağlayıcıları bul |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uzamsal Tutturucuların özelliklerini güncelleştirme |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. NetApp/Locations/checkfilepathavaılabılıty/Action | Dosya yolunun kullanılabilir olup olmadığını denetleyin |
> | Eylem | Microsoft. NetApp/konumlar/checknameavaılabılıty/Action | Kaynak adının kullanılabilir olup olmadığını denetle |
> | Eylem | Microsoft. NetApp/konumlar/operationresults/Read | Bir işlem sonucu kaynağını okur. |
> | Eylem | Microsoft. NetApp/konumlar/okuma | Bir kullanılabilirlik denetimi kaynağını okur. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/Delete | Havuz kaynağını siler. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/Read | Bir havuz kaynağını okur. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/birimler/Sil | Birim kaynağını siler. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/Volumes/Mount hedefleri/okuma | Bağlama hedef kaynağını okur. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/birimler/okuma | Bir birim kaynağını okur. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler/Sil | Anlık görüntü kaynağını siler. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler/okuma | Anlık görüntü kaynağını okur. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler/yazma | Anlık görüntü kaynağı yazar. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/birimler/yazma | Birim kaynağı yazar. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Capacityhavuzlar/Write | Havuz kaynağı yazar. |
> | Eylem | Microsoft. NetApp/netAppAccounts/Delete | Bir hesap kaynağını siler. |
> | Eylem | Microsoft. NetApp/netAppAccounts/okuma | Bir hesap kaynağını okur. |
> | Eylem | Microsoft. NetApp/netAppAccounts/yazma | Bir hesap kaynağı yazar. |
> | Eylem | Microsoft. NetApp/Işlemler/okuma | İşlem kaynaklarını okur. |
> | Eylem | Microsoft. NetApp/Register/ACTION | Aboneliği Microsoft. NetApp kaynak sağlayıcısına kaydeder |
> | Eylem | Microsoft. NetApp/Unregister/eylem | Microsoft. NetApp kaynak sağlayıcısı ile aboneliğin kaydını siler |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Network/Applicationgatewaykullanılabilirliği Blerequestheaders/Read | Application Gateway kullanılabilir Istek üst bilgilerini al |
> | Eylem | Microsoft. Network/Applicationgatewaykullanılabilirliği Bleresponseheaders/okuma | Application Gateway kullanılabilir yanıt üst bilgisi al |
> | Eylem | Microsoft. Network/applicationGatewayAvailableServerVariables/Read | Application Gateway kullanılabilir sunucu değişkenlerini al |
> | Eylem | Microsoft. Network/applicationGatewayAvailableSslOptions/predefinedPolicies/Read | Application Gateway SSL önceden tanımlı Ilkesi |
> | Eylem | Microsoft. Network/applicationGatewayAvailableSslOptions/Read | Kullanılabilir SSL seçeneklerini Application Gateway |
> | Eylem | Microsoft. Network/applicationGatewayAvailableWafRuleSets/Read | Application Gateway kullanılabilir WAF kural kümelerini alır |
> | Eylem | Microsoft. Network/Applicationgateway/Backendavddresspoir/JOIN/Action | Bir uygulama ağ geçidi arka uç adres havuzunu birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/Applicationgateway/backendhealth/Action | Uygulama ağ geçidi arka uç durumunu alır |
> | Eylem | Microsoft. Network/Applicationgateway/Delete | Bir uygulama ağ geçidini siler |
> | Eylem | Microsoft. Network/Applicationgateway/getBackendHealthOnDemand/Action | Verilen http ayarı ve arka uç havuzu için isteğe bağlı bir uygulama ağ geçidi arka uç durumu alır |
> | Eylem | Microsoft. Network/Applicationgateway/Read | Bir uygulama ağ geçidini alır |
> | Eylem | Microsoft. Network/Applicationgateway/start/Action | Bir uygulama ağ geçidi başlatır |
> | Eylem | Microsoft. Network/Applicationgateway/stop/Action | Bir uygulama ağ geçidini durduruyor |
> | Eylem | Microsoft. Network/Applicationgateway/Write | Uygulama ağ geçidi oluşturur veya bir uygulama ağ geçidini güncelleştirir |
> | Eylem | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Application Gateway WAF ilkesini siler |
> | Eylem | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Read | Application Gateway WAF ilkesi alır |
> | Eylem | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Bir Application Gateway WAF ilkesi oluşturur veya bir Application Gateway WAF ilkesini güncelleştirir |
> | Eylem | Microsoft. Network/applicationSecurityGroups/Delete | Bir uygulama güvenlik grubunu siler |
> | Eylem | Microsoft. Network/applicationSecurityGroups/Joinıp/Action | Bir IP yapılandırmasını uygulama güvenlik gruplarıyla birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Uygulama güvenlik gruplarına bir güvenlik kuralı birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/applicationSecurityGroups/Liztipconfigurations/Action | ApplicationSecurityGroup içindeki IP yapılandırmasını listeler |
> | Eylem | Microsoft. Network/applicationSecurityGroups/Read | Bir uygulama güvenlik grubu KIMLIĞI alır. |
> | Eylem | Microsoft. Network/applicationSecurityGroups/Write | Bir uygulama güvenlik grubu oluşturur veya var olan bir uygulama güvenlik grubunu güncelleştirir. |
> | Eylem | Microsoft. Network/azureFirewallFqdnTags/Read | Azure Güvenlik Duvarı FQDN etiketlerini alır |
> | Eylem | Microsoft. Network/azurefirewalls/Delete | Azure Güvenlik duvarını silme |
> | Eylem | Microsoft. Network/azurefirewalls/Read | Azure Güvenlik Duvarı 'Nı al |
> | Eylem | Microsoft. Network/azurefirewalls/Write | Bir Azure Güvenlik duvarı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Bastionkonakları/silme | Bir savunma konağını siler |
> | Eylem | Microsoft. Network/bastionHosts/okuma | Bir savunma ana bilgisayarı alır |
> | Eylem | Microsoft. Network/Bastionkonakları/yazma | Savunma Konağı oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Network/bgpServiceCommunities/Read | BGP hizmeti topluluklarını al |
> | Eylem | Microsoft. Network/checkFrontDoorNameAvailability/Action | Bir ön kapı adının kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. Network/checkTrafficManagerNameAvailability/Action | Traffic Manager göreli DNS adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. Network/Connections/Delete | VirtualNetworkGatewayConnection 'ı siler |
> | Eylem | Microsoft. Network/Connections/Read | VirtualNetworkGatewayConnection alır |
> | Eylem | Microsoft. Network/Connections/revoke/Action | Bir Express Route bağlantı durumunu Iptal edildi olarak işaretler |
> | Eylem | Microsoft. Network/Connections/sharedkey/Action | VirtualNetworkGatewayConnection SharedKey al |
> | Eylem | Microsoft. Network/Connections/sharedKey/Read | VirtualNetworkGatewayConnection SharedKey değerini alır |
> | Eylem | Microsoft. Network/Connections/sharedKey/Write | Var olan bir VirtualNetworkGatewayConnection SharedKey oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Connections/vpndeviceconfigurationscrıpt/Action | VirtualNetworkGatewayConnection 'un VPN cihaz yapılandırmasını alır |
> | Eylem | Microsoft. Network/Connections/Write | Var olan VirtualNetworkGatewayConnection 'ı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/ddosCustomPolicies/Delete | DDoS özelleştirilmiş ilkeyi siler |
> | Eylem | Microsoft. Network/ddosCustomPolicies/Read | DDoS özelleştirilmiş ilke tanımı tanımını alır |
> | Eylem | Microsoft. Network/ddosCustomPolicies/Write | DDoS özelleştirilmiş bir ilke oluşturur veya var olan bir DDoS özelleştirilmiş ilkesini güncelleştirir |
> | Eylem | Microsoft. Network/Ddosprotectionplanlar/silme | DDoS koruma planını siler |
> | Eylem | Microsoft. Network/Ddosprotectionplanlar/JOIN/Action | DDoS koruma planına katılır. Alertable değil. |
> | Eylem | Microsoft. Network/Ddosprotectionplanlar/okuma | DDoS koruma planını alır |
> | Eylem | Microsoft. Network/Ddosprotectionplanlar/yazma | DDoS koruma planı oluşturur veya DDoS koruma planını güncelleştirir  |
> | Eylem | Microsoft. Network/dnsoperationresults/Read | Bir DNS işleminin sonuçlarını alır |
> | Eylem | Microsoft. Network/dnsoperationdurumlar/okuma | Bir DNS işleminin durumunu alır  |
> | Eylem | Microsoft. Network/dnszones/A/Delete | Verilen bir adın kayıt kümesini ve ' A ' türünü bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/A/Read | ' A ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/dnszones/A/Write | DNS bölgesi içinde ' A ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/AAAA/Delete | Verilen bir adın kayıt kümesini ve ' AAAA ' türünü bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/AAAA/Read | ' AAAA ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/dnszones/AAAA/Write | Bir DNS bölgesi içinde ' AAAA ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/All/Read | Türler genelinde DNS kayıt kümelerini alır |
> | Eylem | Microsoft. Network/dnszones/CAA/Delete | Verilen bir adın kayıt kümesini kaldırın ve bir DNS bölgesinden ' CAA ' yazın. |
> | Eylem | Microsoft. Network/dnszones/CAA/Read | ' CAA ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi TTL, Etiketler ve ETag değerini içerir. |
> | Eylem | Microsoft. Network/dnszones/CAA/Write | Bir DNS bölgesi içinde ' CAA ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/CNAME/Delete | Verilen bir adın kayıt kümesini ve ' CNAME ' türünü bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/CNAME/Read | ' CNAME ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi TTL, Etiketler ve ETag değerini içerir. |
> | Eylem | Microsoft. Network/dnszones/CNAME/Write | Bir DNS bölgesi içinde ' CNAME ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/Delete | DNS bölgesini JSON biçiminde silin. Bölge özellikleri, Etiketler, ETag, Numberofkayıtkümeleri ve Maxnumberofkayıtkümelerdir. |
> | Eylem | Microsoft. Network/dnszones/MX/Delete | Verilen bir adın kayıt kümesini ve ' MX ' türünü bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/MX/Read | ' MX ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/dnszones/MX/Write | Bir DNS bölgesi içinde ' MX ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/NS/Delete | NS türündeki DNS kayıt kümesini siler |
> | Eylem | Microsoft. Network/dnszones/NS/Read | NS türündeki DNS kayıt kümesini alır |
> | Eylem | Microsoft. Network/dnszones/NS/Write | NS türünde DNS kayıt kümesi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/dnszones/PTR/Delete | Verilen bir adın kayıt kümesini ve ' PTR ' türünü bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/PTR/Read | ' PTR ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/dnszones/PTR/Write | Bir DNS bölgesi içinde ' PTR ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/Read | JSON biçiminde DNS bölgesini alın. Bölge özellikleri, Etiketler, ETag, Numberofkayıtkümeleri ve Maxnumberofkayıtkümelerdir. Bu komutun bölge içinde yer alan kayıt kümelerini almayacağını unutmayın. |
> | Eylem | Microsoft. Network/dnszones/kayıt kümeleri/okuma | Türler genelinde DNS kayıt kümelerini alır |
> | Eylem | Microsoft. Network/dnszones/SOA/Read | SOA türündeki DNS kayıt kümesini alır |
> | Eylem | Microsoft. Network/dnszones/SOA/Write | SOA türünde DNS kayıt kümesi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/dnszones/SRV/Delete | Verilen bir adın kayıt kümesini ve ' SRV ' türünü bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/SRV/Read | ' SRV ' türündeki kayıt kümesini JSON biçiminde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/dnszones/SRV/Write | SRV türü kayıt kümesi oluştur veya güncelleştir |
> | Eylem | Microsoft. Network/dnszones/TXT/Delete | Verilen ad ve ' TXT ' türünün kayıt kümesini bir DNS bölgesinden kaldırın. |
> | Eylem | Microsoft. Network/dnszones/TXT/Read | JSON biçiminde ' TXT ' türünün kayıt kümesini alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/dnszones/TXT/Write | Bir DNS bölgesi içinde ' TXT ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/dnszones/Write | Bir kaynak grubu içinde bir DNS bölgesi oluşturun veya güncelleştirin.  Bir DNS bölgesi kaynağındaki etiketleri güncelleştirmek için kullanılır. Bu komutun bölge içindeki kayıt kümelerini oluşturmak veya güncelleştirmek için kullanılamayacağını unutmayın. |
> | Eylem | Microsoft. Network/Expressroutedevreleri/yetkilendirmeler/silme | Bir Expressroutedevyetkilendirmesini siler |
> | Eylem | Microsoft. Network/Expressroutedevreleri/yetkilendirmeler/okuma | Bir Expressroutedevresi yetkilendirmesi alır |
> | Eylem | Microsoft. Network/Expressroutedevreleri/yetkilendirmeler/yazma | Mevcut bir Expressroutedevresi yetkilendirmesi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Expressroutedevreleri/Delete | Bir Expressroutedevresini siler |
> | Eylem | Microsoft. Network/Expressroutedevreleri/JOIN/Action | Bir hızlı rota devresini birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/Expressroutedevreleri/peerler/arpTables/Read | Expressroutedevresi eşlemesi ArpTable alır |
> | Eylem | Microsoft. Network/Expressroutedevreleri/eşlemeleri/bağlantıları/silme | Expressroutedevconnection 'ı siler |
> | Eylem | Microsoft. Network/Expressroutedevreler/eşlemeler/bağlantılar/okuma | Bir Expressroutedevconnection alır |
> | Eylem | Microsoft. Network/Expressroutedevreler/eşlemeler/bağlantılar/yazma | Mevcut bir Expressroutedevconnection kaynağını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Expressroutedevreleri/eşlemeleri/silme | Bir Expressroutedeveşliğini siler |
> | Eylem | Microsoft. Network/Expressroutedevreleri/peerler/peerConnections/Read | Eş Express Route bağlantı hattı bağlantısını alır |
> | Eylem | Microsoft. Network/Expressroutedevreler/peerler/okuma | Expressroutedeveşliğini alır |
> | Eylem | Microsoft. Network/Expressroutedevreleri/peerler/routeTables/Read | Bir Expressroutedevresi eşleme RouteTable alır |
> | Eylem | Microsoft. Network/Expressroutedevreleri/peerler/routeTablesSummary/Read | Bir Expressroutedevresi eşlemesi RouteTable özetini alır |
> | Eylem | Microsoft. Network/Expressroutedevreler/eşlemeler/istatistikler/okuma | Expressroutedevresi eşleme stat alır |
> | Eylem | Microsoft. Network/Expressroutedevreleri/eşlemeleri/yazma | Mevcut bir Expressroutedeveşliğini oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Expressroutedevreler/okuma | Expressroutedevresi al |
> | Eylem | Microsoft. Network/Expressroutedevreleri/stats/Read | Expressroutedevstat alır |
> | Eylem | Microsoft. Network/Expressroutedevreleri/Write | Mevcut bir Expressroutedevresini oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/JOIN/Action | Bir Express Route çapraz bağlantısını birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/peerler/arpTables/Read | Bir Express Route çapraz bağlantı eşleme ARP tablosu alır |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/peerler/Sil | Bir Express Route çapraz bağlantı eşlemesini siler |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/peerler/okundu | Bir Express Route çapraz bağlantı eşlemesi alır |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/peerler/routeTables/Read | Bir Express Route çapraz bağlantı eşleme yolu tablosu alır |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/peerler/routeTableSummary/Read | Bir Express Route çapraz bağlantı eşleme yolu tablosu özetini alır |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/peerler/yaz | Bir Express Route çapraz bağlantı eşlemesi oluşturur veya var olan bir Express Route çapraz bağlantı eşlemesini güncelleştirir |
> | Eylem | Microsoft. Network/expressRouteCrossConnections/Read | Express Route çapraz bağlantı al |
> | Eylem | Microsoft. Network/Expressroutegateway/expressRouteConnections/Delete | Bir Express Route bağlantısını siler |
> | Eylem | Microsoft. Network/Expressroutegateway/expressRouteConnections/Read | Bir Express Route bağlantısı alır |
> | Eylem | Microsoft. Network/Expressroutegateway/expressRouteConnections/Write | Bir Express Route bağlantısı oluşturur veya var olan bir Express Route bağlantısını güncelleştirir |
> | Eylem | Microsoft. Network/Expressroutegateway/JOIN/Action | Bir Express Route ağ geçidini birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/Expressroutegateway/Read | Express Route ağ geçidini al |
> | Eylem | Microsoft. Network/expressRoutePorts/Delete | ExpressRoutePorts siler |
> | Eylem | Microsoft. Network/expressRoutePorts/JOIN/Action | Express Route bağlantı noktalarını birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/expressRoutePorts/bağlantılar/okuma | ExpressRouteLink alır |
> | Eylem | Microsoft. Network/expressRoutePorts/Read | ExpressRoutePorts alır |
> | Eylem | Microsoft. Network/expressRoutePorts/Write | Expressrouteport oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/expressRoutePortsLocations/Read | Hızlı rota bağlantı noktaları konumlarını al |
> | Eylem | Microsoft. Network/expressRouteServiceProviders/Read | Hızlı Yönlendirme Hizmeti sağlayıcılarını alır |
> | Eylem | Microsoft. Network/firewallPolicies/Delete | Bir güvenlik duvarı Ilkesini siler |
> | Eylem | Microsoft. Network/firewallPolicies/JOIN/Action | Bir güvenlik duvarı Ilkesini birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/firewallPolicies/Read | Bir güvenlik duvarı Ilkesi alır |
> | Eylem | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Bir güvenlik duvarı Ilkesi kural grubunu siler |
> | Eylem | Microsoft. Network/firewallPolicies/ruleGroups/Read | Bir güvenlik duvarı Ilke kuralı grubunu alır |
> | Eylem | Microsoft. Network/firewallPolicies/ruleGroups/Write | Bir güvenlik duvarı Ilkesi kural grubu oluşturur veya var olan bir güvenlik duvarı Ilkesi kural grubunu güncelleştirir |
> | Eylem | Microsoft. Network/firewallPolicies/Write | Bir güvenlik duvarı Ilkesi oluşturur veya var olan bir güvenlik duvarı Ilkesini güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/backendPools/Delete | Bir arka uç havuzunu siler |
> | Eylem | Microsoft. Network/Frontkapaklı/backendPools/Read | Bir arka uç havuzu alır |
> | Eylem | Microsoft. Network/Frontkapaklı/backendPools/Write | Bir arka uç havuzu oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/Delete | Ön kapıyı siler |
> | Eylem | Microsoft. Network/Frontkapılar/ön uçlar/silme | Ön uç uç noktasını siler |
> | Eylem | Microsoft. Network/Frontkapaklı/frontendEndpoints/disableHttps/Action | Ön uç uç noktasında HTTPS 'yi devre dışı bırakır |
> | Eylem | Microsoft. Network/Frontkapaklı/frontendEndpoints/enableHttps/Action | Ön uç uç noktasında HTTPS 'yi etkinleştirilir |
> | Eylem | Microsoft. Network/Frontkapaklı/frontendEndpoints/Read | Ön uç uç noktası alır |
> | Eylem | Microsoft. Network/Frontkapıları/ön uçlar/yazındnoktaları/yazma | Ön uç uç noktası oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/healthProbeSettings/Delete | Durum araştırma ayarlarını siler |
> | Eylem | Microsoft. Network/Frontkapaklı/healthProbeSettings/Read | Sistem durumu araştırma ayarlarını alır |
> | Eylem | Microsoft. Network/Frontkapaklı/healthProbeSettings/Write | Durum araştırma ayarlarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/loadBalancingSettings/Delete | Yük Dengeleme ayarlarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/loadBalancingSettings/Read | Yük Dengeleme ayarlarını alır |
> | Eylem | Microsoft. Network/Frontkapaklı/loadBalancingSettings/Write | Yük Dengeleme ayarlarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/Temizleme/eylem | Ön kapıdan önbelleğe alınmış içeriği Temizleme |
> | Eylem | Microsoft. Network/Frontkapaklı/Read | Ön kapıyı alır |
> | Eylem | Microsoft. Network/Frontkapaklı/routingRules/Delete | Bir yönlendirme kuralını siler |
> | Eylem | Microsoft. Network/Frontkapaklı/routingRules/Read | Bir yönlendirme kuralı alır |
> | Eylem | Microsoft. Network/Frontkapaklı/routingRules/Write | Yönlendirme kuralı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Frontkapaklı/validateCustomDomain/Action | Ön kapı için bir ön uç uç noktasını doğrular |
> | Eylem | Microsoft. Network/Frontkapaklı/Write | Ön kapı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/Read | Web uygulaması güvenlik duvarı yönetilen kural kümelerini alır |
> | Eylem | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Delete | Web uygulaması güvenlik duvarı Ilkesini siler |
> | Eylem | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Read | Web uygulaması güvenlik duvarı Ilkesini alır |
> | Eylem | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Bir Web uygulaması güvenlik duvarı Ilkesi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/loadBalancers/Backendadddresspoir/JOIN/Action | Yük dengeleyici arka uç adres havuzunu birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/loadBalancers/Backendadddresspospool/Read | Yük dengeleyici arka uç adres havuzu tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/Delete | Yük dengeleyiciyi siler |
> | Eylem | Microsoft. Network/loadBalancers/Frontendıpconfigurations/JOIN/Action | Load Balancer bir ön uç IP yapılandırmasına katılır. Alertable değil. |
> | Eylem | Microsoft. Network/loadBalancers/Frontendıpconfigurations/Read | Yük dengeleyici ön uç IP yapılandırma tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/ınboundnatpools/JOIN/Action | Yük dengeleyici gelen NAT havuzunu birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/loadBalancers/ınboundnatpools/Read | Yük dengeleyici gelen NAT havuzu tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/ınboundnatrules/Delete | Yük dengeleyici gelen NAT kuralını siler |
> | Eylem | Microsoft. Network/loadBalancers/ınboundnatrules/JOIN/Action | Yük dengeleyici gelen NAT kuralına katılır. Alertable değil. |
> | Eylem | Microsoft. Network/loadBalancers/ınboundnatrules/Read | Yük dengeleyici gelen NAT kuralı tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/ınboundnatrules/Write | Yük dengeleyici gelen NAT kuralı oluşturur veya var olan yük dengeleyici gelen NAT kuralını güncelleştirir |
> | Eylem | Microsoft. Network/loadBalancers/loadBalancingRules/Read | Yük dengeleyici Yük Dengeleme kuralı tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/NetworkInterfaces/Read | Yük dengeleyici altındaki tüm ağ arabirimlerine başvuruları alır |
> | Eylem | Microsoft. Network/loadBalancers/outboundRules/Read | Yük dengeleyici giden kuralı tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/araştırmaları/JOIN/Action | Yük dengeleyicinin araştırmalarını sağlar. Örneğin, bu izinle VM Ölçek kümesinin Healtharaştırma özelliği araştırmasına başvurabilir. Alertable değil. |
> | Eylem | Microsoft. Network/loadBalancers/araştırmaları/Read | Yük dengeleyici araştırması alır |
> | Eylem | Microsoft. Network/loadBalancers/Read | Yük dengeleyici tanımını alır |
> | Eylem | Microsoft. Network/loadBalancers/virtualMachines/Read | Yük dengeleyici kapsamındaki tüm sanal makinelere başvuruları alır |
> | Eylem | Microsoft. Network/loadBalancers/Write | Yük dengeleyici oluşturur veya var olan bir yük dengeleyiciyi güncelleştirir |
> | Eylem | Microsoft. Network/localnetworkgateway/Delete | LocalNetworkGateway 'i siler |
> | Eylem | Microsoft. Network/localnetworkgateway/Read | LocalNetworkGateway alır |
> | Eylem | Microsoft. Network/localnetworkgateway/Write | Mevcut bir LocalNetworkGateway oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/Locations/oto Approlarprivatelinkservices/Read | Otomatik onaylanan özel bağlantı hizmetlerini alır |
> | Eylem | Microsoft. Network/Locations/Availabletemsilciler/okuma | Kullanılabilir temsilcileri alır |
> | Eylem | Microsoft. Network/Locations/availablePrivateEndpointTypes/Read | Kullanılabilir özel uç nokta kaynaklarını alır |
> | Eylem | Microsoft. Network/Locations/Availableservicetakma adları/okuma | Kullanılabilir hizmet diğer adlarını alır |
> | Eylem | Microsoft. Network/Locations/bareMetalTenants/Action | Çıplak kiracıyı ayırır veya doğrular |
> | Eylem | Microsoft. Network/Locations/Checkivme Networkingsupport/Action | Hızlandırılmış ağ desteğini denetler |
> | Eylem | Microsoft. Network/Locations/Checkdnsnameavaılabılıty/Read | Belirtilen konumda DNS etiketinin kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. Network/Locations/checkPrivateLinkServiceVisibility/eylem | Özel bağlantı hizmeti görünürlüğünü denetler |
> | Eylem | Microsoft. Network/Locations/operationResults/Read | Zaman uyumsuz POST veya DELETE işleminin işlem sonucunu alır |
> | Eylem | Microsoft. Network/Locations/Operations/Read | Zaman uyumsuz bir işlemin durumunu temsil eden işlem kaynağını alır |
> | Eylem | Microsoft. Network/Locations/serviceTags/Read | Hizmet etiketlerini al |
> | Eylem | Microsoft. Network/Locations/Supportedvirtualınines/Read | Desteklenen sanal makine boyutlarını alır |
> | Eylem | Microsoft. Network/Locations/kullanımlar/Read | Kaynak kullanım ölçümlerini alır |
> | Eylem | Microsoft. Network/Locations/virtualNetworkAvailableEndpointServices/Read | Kullanılabilir sanal ağ uç noktası hizmetlerinin bir listesini alır |
> | Eylem | Microsoft. Network/Networkyoğunlutpolicies/Delete | Bir ağ hedefi Ilkesini siler |
> | Eylem | Microsoft. Network/Networkyoğunlutpolicies/okuma | Bir ağ hedefi Ilke açıklamasını alır |
> | Eylem | Microsoft. Network/Networkyoğunlutpolicies/Write | Bir ağ hedefi Ilkesi oluşturur veya var olan bir ağ hedefi Ilkesini güncelleştirir |
> | Eylem | Microsoft. Network/NetworkInterfaces/Delete | Bir ağ arabirimini siler |
> | Eylem | Microsoft. Network/NetworkInterfaces/effectiveNetworkSecurityGroups/Action | VM 'Nin ağ arabiriminde yapılandırılan ağ güvenlik gruplarını al |
> | Eylem | Microsoft. Network/NetworkInterfaces/effectiveRouteTable/Action | VM 'Nin ağ arabiriminde yapılandırılan yol tablosunu al |
> | Eylem | Microsoft. Network/NetworkInterfaces/ipconfigurations/JOIN/Action | Bir ağ arabirimi IP yapılandırmasına katılır. Alertable değil. |
> | Eylem | Microsoft. Network/NetworkInterfaces/ipconfigurations/Read | Bir ağ arabirimi IP yapılandırma tanımını alır.  |
> | Eylem | Microsoft. Network/NetworkInterfaces/JOIN/Action | Bir sanal makineyi bir ağ arabirimine birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/NetworkInterfaces/loadBalancers/Read | Ağ arabiriminin parçası olduğu tüm yük dengeleyicileri alır |
> | Eylem | Microsoft. Network/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | Eylem | Microsoft. Network/NetworkInterfaces/tapConfigurations/Delete | Bir ağ arabirimini siler yapılandırma ' ya dokunun. |
> | Eylem | Microsoft. Network/NetworkInterfaces/tapConfigurations/Read | Yapılandırma ' ya bir ağ arabirimi alır. |
> | Eylem | Microsoft. Network/NetworkInterfaces/tapConfigurations/Write | Yapılandırma ' ya bir ağ arabirimi oluşturur veya var olan bir ağ arabirimini güncelleştirir yapılandırma ' ya dokunun. |
> | Eylem | Microsoft. Network/NetworkInterfaces/Write | Ağ arabirimi oluşturur veya var olan bir ağ arabirimini güncelleştirir.  |
> | Eylem | Microsoft. Network/networkProfiles/Delete | Bir ağ profilini siler |
> | Eylem | Microsoft. Network/networkProfiles/Read | Ağ profilini alır |
> | Eylem | Microsoft. Network/networkProfiles/removeContainers/Action | Kapsayıcıları kaldırır |
> | Eylem | Microsoft. Network/networkProfiles/setContainers/Action | Kapsayıcıları ayarlar |
> | Eylem | Microsoft. Network/networkProfiles/Setnetworkınterfaces/Action | Kapsayıcı ağ arabirimlerini ayarlar |
> | Eylem | Microsoft. Network/networkProfiles/Write | Bir ağ profili oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/Read | Varsayılan bir güvenlik kuralı tanımını alır |
> | Eylem | Microsoft. Network/networkSecurityGroups/Delete | Bir ağ güvenlik grubunu siler |
> | Eylem | Microsoft. Network/networkSecurityGroups/JOIN/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | Eylem | Microsoft. Network/networkSecurityGroups/Read | Bir ağ güvenlik grubu tanımını alır |
> | Eylem | Microsoft. Network/networkSecurityGroups/securityRules/Delete | Bir güvenlik kuralını siler |
> | Eylem | Microsoft. Network/networkSecurityGroups/securityRules/Read | Bir güvenlik kuralı tanımını alır |
> | Eylem | Microsoft. Network/networkSecurityGroups/securityRules/Write | Bir güvenlik kuralı oluşturur veya var olan bir güvenlik kuralını güncelleştirir |
> | Eylem | Microsoft. Network/networkSecurityGroups/Write | Bir ağ güvenlik grubu oluşturur veya var olan bir ağ güvenlik grubunu güncelleştirir |
> | Eylem | Microsoft. Network/networkWatchers/availableProvidersList/Action | Belirtilen Azure bölgesi için kullanılabilir tüm internet hizmeti sağlayıcılarını döndürür. |
> | Eylem | Microsoft. Network/networkWatchers/azureReachabilityReport/Action | Belirli bir konumdan Azure bölgelerine Internet hizmet sağlayıcılarının göreli gecikme puanı döndürür. |
> | Eylem | Microsoft. Network/networkWatchers/Configuyeniden Akıtılme günlüğü/eylemi | Bir hedef kaynak için akış günlüğünü yapılandırır. |
> | Eylem | Microsoft. Network/networkWatchers/Connectionizleyicileri/silme | Bir bağlantı Izleyicisini siler |
> | Eylem | Microsoft. Network/networkWatchers/Connectionizleyicileri/sorgu/eylem | Belirtilen uç noktalar arasındaki sorgu izleme bağlantısı |
> | Eylem | Microsoft. Network/networkWatchers/Connectionmonitörleri/okuma | Bağlantı Izleyicisi ayrıntılarını al |
> | Eylem | Microsoft. Network/networkWatchers/Connectionizleyicileri/Başlat/eylem | Belirtilen uç noktalar arasındaki bağlantıyı izlemeye başla |
> | Eylem | Microsoft. Network/networkWatchers/Connectionizleyicileri/durdur/eylem | Belirtilen uç noktalar arasındaki izleme bağlantısını Durdur/Duraklat |
> | Eylem | Microsoft. Network/networkWatchers/Connectionmonitörleri/yazma | Bağlantı Izleyicisi oluşturur |
> | Eylem | Microsoft. Network/networkWatchers/connectivityCheck/Action | Bir sanal makineden, başka bir VM veya rastgele uzak sunucu dahil olmak üzere belirli bir uç noktasına doğrudan TCP bağlantısı kurma olasılığını doğrular. |
> | Eylem | Microsoft. Network/networkWatchers/Delete | Ağ İzleyicisi siler |
> | Eylem | Microsoft. Network/networkWatchers/ipFlowVerify/Action | Paketin belirli bir hedefe mi izin verileceğini yoksa reddetme mi olduğunu döndürür. |
> | Eylem | Microsoft. Network/networkWatchers/lenses/Delete | Bir lens siler |
> | Eylem | Microsoft. Network/networkWatchers/lenses/sorgu/eylem | Belirtilen bir uç noktada ağ trafiğini izleme sorgusu |
> | Eylem | Microsoft. Network/networkWatchers/lenses/okuma | Lens ayrıntılarını al |
> | Eylem | Microsoft. Network/networkWatchers/lenses/başlangıç/eylem | Belirtilen bir uç noktada ağ trafiğini izlemeye başla |
> | Eylem | Microsoft. Network/networkWatchers/lenses/durdur/eylem | Belirtilen bir uç noktada ağ trafiğini izlemeyi Durdur/Duraklat |
> | Eylem | Microsoft. Network/networkWatchers/lenses/yazma | Bir lens oluşturur |
> | Eylem | Microsoft. Network/networkWatchers/networkConfigurationDiagnostic/Action | Ağ yapılandırması tanılaması. |
> | Eylem | Microsoft. Network/networkWatchers/Sonrakii/eylem | Belirtilen hedef ve hedef IP adresi için sonraki atlama türünü ve sonraki Umarım IP adresini döndürün. |
> | Eylem | Microsoft. Network/networkWatchers/Packetyakalamaları/silme | Bir paket yakalamayı siler |
> | Eylem | Microsoft. Network/networkWatchers/Packetyakalamaları/queryStatus/Action | Bir paket yakalama kaynağının özellikleri ve durumu hakkında bilgi alır. |
> | Eylem | Microsoft. Network/networkWatchers/Packetyakalamaları/Read | Paket yakalama tanımını alma |
> | Eylem | Microsoft. Network/networkWatchers/Packetyakalamaları/durdur/eylem | Çalışan paket yakalama oturumunu durdurun. |
> | Eylem | Microsoft. Network/networkWatchers/Packetyakalamaları/yazma | Paket yakalama oluşturur |
> | Eylem | Microsoft. Network/networkWatchers/Pingkafesler/Sil | Pinga kafesi siler |
> | Eylem | Microsoft. Network/networkWatchers/Pingkafesler/okuma | Pingkafes ayrıntılarını al |
> | Eylem | Microsoft. Network/networkWatchers/Pingkafes/start/Action | Belirtilen VM 'Ler arasında Pingkafes'i Başlat |
> | Eylem | Microsoft. Network/networkWatchers/Pingkafesler/durdur/eylem | Belirtilen VM 'Ler arasında Pingkafesi durdur |
> | Eylem | Microsoft. Network/networkWatchers/Pingkafesler/yaz | Pingkafes oluşturur |
> | Eylem | Microsoft. Network/networkWatchers/Queryconnectionizleyicileri/eylemi | Belirtilen uç noktalar arasındaki bağlantıyı izleyen toplu iş sorgusu |
> | Eylem | Microsoft. Network/networkWatchers/queryFlowLogStatus/Action | Bir kaynaktaki akış günlüğünün durumunu alır. |
> | Eylem | Microsoft. Network/networkWatchers/Querybir TResult/Action | Daha önce çalıştırılan veya çalışmakta olan sorun giderme işleminin sorun giderme sonucunu alır. |
> | Eylem | Microsoft. Network/networkWatchers/Read | Ağ İzleyicisi tanımını al |
> | Eylem | Microsoft. Network/networkWatchers/securityGroupView/Action | Bir VM 'ye uygulanan yapılandırılmış ve etkin ağ güvenlik grubu kurallarını görüntüleyin. |
> | Eylem | Microsoft. Network/networkWatchers/topoloji/eylem | Kaynak grubundaki kaynakların ve bunların ilişkilerinin ağ düzeyi görünümünü alır. |
> | Eylem | Microsoft. Network/networkWatchers/sorun giderme/eylem | Azure 'daki bir ağ kaynağında sorun gidermeye başlar. |
> | Eylem | Microsoft. Network/networkWatchers/Write | Bir ağ izleyicisi oluşturur veya var olan bir ağ izleyicisini güncelleştirir |
> | Eylem | Microsoft. Network/Operations/Read | Kullanılabilir Işlemleri al |
> | Eylem | Microsoft. Network/p2sVpnGateways/Delete | Bir P2SVpnGateway siler. |
> | Eylem | Microsoft. Network/p2sVpnGateways/generatevpnprofile/Action | P2SVpnGateway için VPN profili oluşturma |
> | Eylem | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealth/Action | P2SVpnGateway için P2S VPN bağlantısı durumunu alır |
> | Eylem | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | P2SVpnGateway için ayrıntılı bir P2S VPN bağlantısı durumunu alır |
> | Eylem | Microsoft. Network/p2sVpnGateways/Read | Bir P2SVpnGateway alır. |
> | Eylem | Microsoft. Network/p2sVpnGateways/Write | Bir P2SVpnGateway koyar. |
> | Eylem | Microsoft. Network/privateDnsOperationResults/Read | Özel DNS işleminin sonuçlarını alır |
> | Eylem | Microsoft. Network/privateDnsOperationStatuses/Read | Özel DNS işleminin durumunu alır |
> | Eylem | Microsoft. Network/privateDnsZones/A/Delete | Verilen bir ada sahip kayıt kümesini kaldırın ve bir Özel DNS bölgesinden ' A ' yazın. |
> | Eylem | Microsoft. Network/privateDnsZones/A/Read | ' A ' türündeki kayıt kümesini JSON biçiminde bir Özel DNS bölgesi içinde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/privateDnsZones/A/Write | Özel DNS bölgesi içinde ' A ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/privateDnsZones/AAAA/Delete | Verilen bir adın kayıt kümesini ve bir Özel DNS bölgesinden ' AAAA ' türünü kaldırın. |
> | Eylem | Microsoft. Network/privateDnsZones/AAAA/Read | ' AAAA ' türündeki kayıt kümesini JSON biçiminde bir Özel DNS bölgesi içinde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/privateDnsZones/AAAA/Write | Bir Özel DNS bölgesi içinde ' AAAA ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/privateDnsZones/ALL/Read | Türler arasında Özel DNS kayıt kümelerini alır |
> | Eylem | Microsoft. Network/privateDnsZones/CNAME/Delete | Verilen bir adın kayıt kümesini ve bir Özel DNS bölgesinden ' CNAME ' türünü kaldırın. |
> | Eylem | Microsoft. Network/privateDnsZones/CNAME/Read | JSON biçiminde bir Özel DNS bölgesi içindeki ' CNAME ' türündeki kayıt kümesini alın. |
> | Eylem | Microsoft. Network/privateDnsZones/CNAME/Write | Bir Özel DNS bölgesi içinde ' CNAME ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Network/privateDnsZones/Delete | Özel DNS bölgesini silin. |
> | Eylem | Microsoft. Network/privateDnsZones/MX/Delete | Verilen bir adın kayıt kümesini ve bir Özel DNS bölgesinden ' MX ' türünü kaldırın. |
> | Eylem | Microsoft. Network/privateDnsZones/MX/Read | JSON biçiminde bir Özel DNS bölgesi içindeki ' MX ' türündeki kayıt kümesini alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/privateDnsZones/MX/Write | Bir Özel DNS bölgesi içinde ' MX ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/privateDnsZones/PTR/Delete | Verilen bir adın kayıt kümesini ve bir Özel DNS bölgesinden ' PTR ' türünü kaldırın. |
> | Eylem | Microsoft. Network/privateDnsZones/PTR/Read | ' PTR ' türünün kayıt kümesini JSON biçiminde bir Özel DNS bölgesi içinde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/privateDnsZones/PTR/Write | Bir Özel DNS bölgesi içinde ' PTR ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/privateDnsZones/Read | Özel DNS bölgesi özelliklerini JSON biçiminde alın. Bu komutun Özel DNS bölgenin bağlı olduğu sanal ağları veya bölgedeki kayıt kümelerini almadığına unutmayın. |
> | Eylem | Microsoft. Network/privateDnsZones/kayıt kümeleri/okuma | Türler arasında Özel DNS kayıt kümelerini alır |
> | Eylem | Microsoft. Network/privateDnsZones/SOA/Read | ' SOA ' türündeki kayıt kümesini JSON biçiminde bir Özel DNS bölgesi içinde alın. |
> | Eylem | Microsoft. Network/privateDnsZones/SOA/Write | Özel DNS bölgesinde ' SOA ' türündeki bir kayıt kümesini güncelleştirin. |
> | Eylem | Microsoft. Network/privateDnsZones/SRV/Delete | Verilen bir adın kayıt kümesini ve bir Özel DNS bölgesinden ' SRV ' türünü kaldırın. |
> | Eylem | Microsoft. Network/privateDnsZones/SRV/Read | ' SRV ' türündeki kayıt kümesini JSON biçiminde bir Özel DNS bölgesi içinde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/privateDnsZones/SRV/Write | Bir Özel DNS bölgesi içinde ' SRV ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/privateDnsZones/TXT/Delete | Verilen bir adın kayıt kümesini ve bir Özel DNS bölgesinden ' TXT ' türünü kaldırın. |
> | Eylem | Microsoft. Network/privateDnsZones/TXT/Read | ' TXT ' türündeki kayıt kümesini JSON biçiminde bir Özel DNS bölgesi içinde alın. Kayıt kümesi, TTL, Etiketler ve ETag 'in yanı sıra bir kayıt listesi içerir. |
> | Eylem | Microsoft. Network/privateDnsZones/TXT/Write | Bir Özel DNS bölgesi içinde ' TXT ' türünde bir kayıt kümesi oluşturun veya güncelleştirin. Belirtilen kayıtlar, kayıt kümesindeki geçerli kayıtların yerini alır. |
> | Eylem | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Delete | Sanal ağa Özel DNS bölgenin bağlantısını silin. |
> | Eylem | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read | JSON biçiminde sanal ağ özelliklerine Özel DNS bölgenin bağlantısını alın. |
> | Eylem | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write | Sanal ağa bir Özel DNS bölgesi bağlantısı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Network/privateDnsZones/Write | Bir kaynak grubu içinde Özel DNS bölgesi oluşturun veya güncelleştirin. Bu komutun bölge içindeki sanal ağ bağlantılarını veya kayıt kümelerini oluşturmak veya güncelleştirmek için kullanılamayacağını unutmayın. |
> | Eylem | Microsoft. Network/privateEndpointRedirectMaps/Read | Özel bir uç nokta RedirectMap alır |
> | Eylem | Microsoft. Network/privateEndpointRedirectMaps/Write | Özel uç nokta RedirectMap oluşturur veya var olan bir özel uç nokta RedirectMap 'i güncelleştirir |
> | Eylem | Microsoft. Network/privateEndpoints/Delete | Özel bir uç nokta kaynağını siler. |
> | Eylem | Microsoft. Network/privateEndpoints/okuma | Özel bir uç nokta kaynağı alır. |
> | Eylem | Microsoft. Network/privateEndpoints/yazma | Yeni bir özel uç nokta oluşturur veya var olan bir özel uç noktayı güncelleştirir. |
> | Eylem | Microsoft. Network/privateLinkServices/Delete | Özel bir bağlantı hizmeti kaynağını siler. |
> | Eylem | Microsoft. Network/privateLinkServices/privateEndpointConnections/Delete | Özel bir uç nokta bağlantısını siler. |
> | Eylem | Microsoft. Network/privateLinkServices/privateEndpointConnections/Read | Özel bir uç nokta bağlantı tanımını alır. |
> | Eylem | Microsoft. Network/privateLinkServices/privateEndpointConnections/Write | Yeni bir özel uç nokta bağlantısı oluşturur veya var olan bir özel uç nokta bağlantısını güncelleştirir. |
> | Eylem | Microsoft. Network/privateLinkServices/Read | Özel bir bağlantı hizmeti kaynağı alır. |
> | Eylem | Microsoft. Network/privateLinkServices/Write | Yeni bir özel bağlantı hizmeti oluşturur veya var olan bir özel bağlantı hizmetini güncelleştirir. |
> | Eylem | Microsoft. Network/publicIPAddresses/Delete | Genel bir IP adresini siler. |
> | Eylem | Microsoft. Network/publicIPAddresses/JOIN/Action | Genel bir IP adresini birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/publicIPAddresses/Read | Genel IP adresi tanımını alır. |
> | Eylem | Microsoft. Network/Publicıpaddresses/Write | Genel bir IP adresi oluşturur veya var olan bir genel IP adresini güncelleştirir.  |
> | Eylem | Microsoft. Network/Publicipöneklerini/silmeyi | Genel bir IP önekini siler |
> | Eylem | Microsoft. Network/Publicipöneklerini/JOIN/Action | Bir PublicIPPrefix öğesine katılır. Alertable değil. |
> | Eylem | Microsoft. Network/Publicipönekleri/okuma | Genel IP öneki tanımını alır |
> | Eylem | Microsoft. Network/Publicipöneklerini/yazma | Genel bir IP öneki oluşturur veya var olan bir genel IP önekini güncelleştirir |
> | Eylem | Microsoft. Network/Register/ACTION | Aboneliği kaydeder |
> | Eylem | Microsoft. Network/routeFilters/Delete | Bir rota filtresi tanımını siler |
> | Eylem | Microsoft. Network/routeFilters/JOIN/Action | Bir yol filtresi birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/routeFilters/Read | Rota filtresi tanımını alır |
> | Eylem | Microsoft. Network/routeFilters/routeFilterRules/Delete | Yol filtresi kuralı tanımını siler |
> | Eylem | Microsoft. Network/routeFilters/routeFilterRules/Read | Yol filtresi kuralı tanımını alır |
> | Eylem | Microsoft. Network/routeFilters/routeFilterRules/Write | Bir yol filtresi kuralı oluşturur veya var olan bir yol filtre kuralını güncelleştirir |
> | Eylem | Microsoft. Network/routeFilters/Write | Bir yol filtresi oluşturur veya var olan bir yol filtresini güncelleştirir |
> | Eylem | Microsoft. Network/routeTables/Delete | Rota tablosu tanımını siler |
> | Eylem | Microsoft. Network/routeTables/JOIN/Action | Bir yol tablosunu birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/routeTables/Read | Yol tablosu tanımını alır |
> | Eylem | Microsoft. Network/routeTables/rotalar/Delete | Bir yol tanımını siler |
> | Eylem | Microsoft. Network/routeTables/rotalar/Read | Bir yol tanımını alır |
> | Eylem | Microsoft. Network/routeTables/rotalar/Write | Bir rota oluşturur veya var olan bir yolu güncelleştirir |
> | Eylem | Microsoft. Network/routeTables/Write | Bir yol tablosu oluşturur veya var olan bir yol tablosunu güncelleştirir |
> | Eylem | Microsoft. Network/securegateyollarla/silme | Güvenli ağ geçidini Sil |
> | Eylem | Microsoft. Network/securegateyollar/okuma | Güvenli ağ geçidi al |
> | Eylem | Microsoft. Network/securegateyollar/yazma | Güvenli ağ geçidi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/Delete | Hizmet uç noktası Ilkesini siler |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/JOIN/Action | Hizmet uç noktası Ilkesini birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/joinSubnet/Action | Bir alt ağı hizmet uç noktası Ilkelerine birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/okuma | Hizmet uç noktası Ilke açıklamasını alır |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Delete | Hizmet uç noktası Ilke tanımını siler |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Read | Bir hizmet uç noktası Ilke tanımı açıklaması Al |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Bir hizmet uç noktası Ilke tanımı oluşturur veya var olan bir hizmet uç noktası Ilke tanımını güncelleştirir |
> | Eylem | Microsoft. Network/serviceEndpointPolicies/Write | Bir hizmet uç noktası Ilkesi oluşturur veya var olan bir hizmet uç noktası Ilkesini güncelleştirir |
> | Eylem | Microsoft. Network/trafficManagerGeographicHierarchies/Read | Coğrafi trafik yönlendirme yöntemiyle kullanılabilecek bölgeleri içeren Traffic Manager coğrafi hiyerarşisini alır |
> | Eylem | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Delete | Varolan bir Traffic Manager profilinden bir Azure Uç Noktası siler. Traffic Manager, silinen Azure Uç Noktası yönlendirme trafiğini durdurur. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Read | Bu Azure Uç Noktası tüm özellikleri de dahil olmak üzere bir Traffic Manager profiline ait bir Azure Uç Noktası alır. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Write | Var olan bir Traffic Manager profiline yeni bir Azure Uç Noktası ekleyin veya bu Traffic Manager profilinde mevcut bir Azure Uç Noktası özelliklerini güncelleştirin. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/Delete | Traffic Manager profilini silin. Traffic Manager profiliyle ilişkili tüm ayarlar kaybedilir ve bu profil artık trafiği yönlendirmek için kullanılamaz. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Delete | Varolan bir Traffic Manager profilinden bir dış uç noktayı siler. Traffic Manager, silinen dış uç noktaya yönlendirme trafiğini durdurur. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Read | Bu dış uç noktanın tüm özellikleri dahil olmak üzere Traffic Manager profile ait olan bir dış uç noktayı alır. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Write | Mevcut bir Traffic Manager profilinde yeni bir dış uç nokta ekleyin veya bu Traffic Manager profilindeki mevcut bir dış noktanın özelliklerini güncelleştirin. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/heatMaps/Read | Konuma ve kaynak IP 'ye göre sorgu sayılarını ve gecikme verilerini içeren verilen Traffic Manager profili için Traffic Manager ısı haritasını alır. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Delete | Varolan bir Traffic Manager profilinden Iç Içe geçmiş bir uç noktayı siler. Traffic Manager, silinen Iç Içe geçmiş uç noktaya yönlendirme trafiğini durdurur. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Read | Bu Iç Içe geçmiş uç noktanın tüm özellikleri dahil olmak üzere bir Traffic Manager profile ait olan Iç Içe geçmiş bir uç noktası alır. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Write | Mevcut bir Traffic Manager profiline yeni bir Iç Içe geçmiş uç noktası ekleyin veya bu Traffic Manager profilinde mevcut Iç Içe geçmiş bir uç noktanın özelliklerini güncelleştirin. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/Read | Traffic Manager profili yapılandırmasını alın. Buna DNS ayarları, trafik yönlendirme ayarları, uç nokta izleme ayarları ve bu Traffic Manager profili tarafından yönlendirilen uç noktaların listesi dahildir. |
> | Eylem | Microsoft. Network/trafficManagerProfiles/Write | Traffic Manager profili oluşturun veya var olan bir Traffic Manager profilinin yapılandırmasını değiştirin.<br>Bu, bir profilin etkinleştirilmesini veya devre dışı bırakılmasını ve DNS ayarlarını, trafik yönlendirme ayarlarını veya uç nokta izleme ayarlarını değiştirmeyi içerir.<br>Traffic Manager profili tarafından yönlendirilen uç noktalar eklenebilir, kaldırılabilir, etkinleştirilebilir veya devre dışı bırakılabilir. |
> | Eylem | Microsoft. Network/trafficManagerUserMetricsKeys/Delete | Gerçek zamanlı Kullanıcı ölçümleri koleksiyonu için kullanılan abonelik düzeyi anahtarını siler. |
> | Eylem | Microsoft. Network/trafficManagerUserMetricsKeys/Read | Gerçek zamanlı Kullanıcı ölçümleri koleksiyonu için kullanılan abonelik düzeyi anahtarını alır. |
> | Eylem | Microsoft. Network/trafficManagerUserMetricsKeys/Write | Gerçek zamanlı Kullanıcı ölçümleri koleksiyonu için kullanılacak yeni bir abonelik düzeyi anahtar oluşturur. |
> | Eylem | Microsoft. Network/Unregister/ACTION | Aboneliğin kaydını siler |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/silme | Sanal hub 'ı siler |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/hubVirtualNetworkConnections/Delete | Bir HubVirtualNetworkConnection 'ı siler |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/hubVirtualNetworkConnections/Read | HubVirtualNetworkConnection alın |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/hubVirtualNetworkConnections/Write | HubVirtualNetworkConnection oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/okuma | Sanal hub al |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/routeTables/Delete | Bir VirtualHubRouteTableV2 silme |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/routeTables/Read | VirtualHubRouteTableV2 edinme |
> | Eylem | Microsoft. Network/Virtualhub 'Ları/routeTables/Write | VirtualHubRouteTableV2 oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Network/Virtualhub/Write | Sanal hub oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Network/virtualnetworkgateway/Connections/Read | VirtualNetworkGatewayConnection 'ı al |
> | Eylem | Microsoft. Network/Virtualnetworkgateway/Delete | VirtualNetworkGateway siler |
> | Eylem | Microsoft. Network/virtualnetworkgateway/generatevpnclientpackage/Action | VirtualNetworkGateway için VpnClient paketi oluşturma |
> | Eylem | Microsoft. Network/virtualnetworkgateway/generatevpnprofile/Action | VirtualNetworkGateway için VpnProfile paketi oluşturma |
> | Eylem | Microsoft. Network/virtualnetworkgateway/getadvertisedroutes/Action | Tanıtılan virtualNetworkGateway yollarını alır |
> | Eylem | Microsoft. Network/virtualnetworkgateway/getbgppeerstatus/Action | VirtualNetworkGateway BGP eş durumunu alır |
> | Eylem | Microsoft. Network/virtualnetworkgateway/getlearnedroutes/Action | Virtualnetworkgateway öğrenilen yollarını alır |
> | Eylem | Microsoft. Network/virtualnetworkgateway/getvpnclientconnectionhealth/Action | VirtualNetworkGateway için VPN Istemci bağlantısı başına durum Al |
> | Eylem | Microsoft. Network/virtualnetworkgateway/getvpnclientipsecparameters/eylem | VirtualNetworkGateway P2S istemcisi için vpnclient IPSec parametrelerini alın. |
> | Eylem | Microsoft. Network/virtualnetworkgateway/getvpnprofilepackageurl/eylem | Önceden oluşturulmuş bir VPN istemci profili paketinin URL 'sini alır |
> | Eylem | Microsoft. Network/Virtualnetworkgateway/Read | VirtualNetworkGateway alır |
> | Eylem | Microsoft. Network/virtualnetworkgateway/Reset/Action | VirtualNetworkGateway 'i sıfırlar |
> | Eylem | Microsoft. Network/virtualnetworkgateway/resetvpnclientsharedkey/Action | VirtualNetworkGateway P2S istemcisi için vpnclient paylaşılan anahtarını sıfırlayın. |
> | Eylem | Microsoft. Network/virtualnetworkgateway/setvpnclientipsecparameters/eylem | VirtualNetworkGateway P2S istemcisi için vpnclient IPSec parametrelerini ayarlayın. |
> | Eylem | Microsoft. Network/virtualnetworkgateway/supportedvpndevices/Action | Desteklenen VPN cihazlarını listeler |
> | Eylem | Microsoft. Network/Virtualnetworkgateway/Write | VirtualNetworkGateway oluşturur veya güncelleştirir |
> | Eylem | Microsoft. Network/virtualNetworks/Bastionkonakları/eylem | Sanal bir ağdaki savunma ana bilgisayar başvurularını alır. |
> | Eylem | Microsoft. Network/virtualNetworks/Bastionkonakları/varsayılan/eylem | Sanal bir ağdaki savunma ana bilgisayar başvurularını alır. |
> | Eylem | Microsoft. Network/virtualNetworks/Checkıpaddressavailability/Read | IP adresinin belirtilen sanal ağda kullanılabilir olup olmadığını denetle |
> | Eylem | Microsoft. Network/virtualNetworks/Delete | Bir sanal ağı siler |
> | Eylem | Microsoft. Network/virtualNetworks/JOIN/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/virtualNetworks/eş/eylem | Bir sanal ağı başka bir sanal ağla eşler |
> | Eylem | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/contextualServiceEndpointPolicies/Delete | Bağlamsal hizmet uç noktası Ilkesini siler |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/contextualServiceEndpointPolicies/Read | Bağlamsal hizmet uç noktası Ilkelerini alır |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/contextualServiceEndpointPolicies/Write | Bağlamsal hizmet uç noktası Ilkesi oluşturur veya var olan bir bağlamsal hizmet uç noktası Ilkesini güncelleştirir |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/Sil | Bir sanal ağ alt ağını siler |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | Eylem | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/hazırlık Ilkeleri/eylemi | Gerekli ağ Ilkelerini uygulayarak bir alt ağ hazırlar |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/okuma | Bir sanal ağ alt ağ tanımını alır |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/hazırlık Ilkeleri/eylemi | Uygulanan ağ Ilkelerini kaldırarak bir alt ağın hazırlamasını kaldır |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/virtualMachines/Read | Bir sanal ağ alt ağındaki tüm sanal makinelere başvuruları alır |
> | Eylem | Microsoft. Network/virtualNetworks/alt ağlar/yazma | Bir sanal ağ alt ağı oluşturur veya var olan bir sanal ağ alt ağını güncelleştirir |
> | Eylem | Microsoft. Network/virtualNetworks/kullanımlar/Read | Sanal ağın her bir alt ağı için IP kullanımlarını al |
> | Eylem | Microsoft. Network/virtualNetworks/virtualMachines/Read | Bir sanal ağdaki tüm sanal makinelere başvuruları alır |
> | Eylem | Microsoft. Network/virtualNetworks/Virtualnetworkpeerler/Sil | Sanal Ağ eşlemesini siler |
> | Eylem | Microsoft. Network/virtualNetworks/Virtualnetworkpeerler/okuma | Sanal ağ eşleme tanımını alır |
> | Eylem | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Bir sanal ağ eşlemesi oluşturur veya var olan bir sanal ağ eşlemesini güncelleştirir |
> | Eylem | Microsoft. Network/virtualNetworks/Write | Bir sanal ağ oluşturur veya var olan bir sanal ağı güncelleştirir |
> | Eylem | Microsoft. Network/virtualNetworkTaps/Delete | Sanal ağ dokunmayı Sil |
> | Eylem | Microsoft. Network/virtualNetworkTaps/JOIN/Action | Bir sanal ağa dokunmasına katılır. Alertable değil. |
> | Eylem | Microsoft. Network/virtualNetworkTaps/Read | Sanal ağ dokunmayı al |
> | Eylem | Microsoft. Network/virtualNetworkTaps/Write | Sanal ağ dokunma oluştur veya güncelleştir |
> | Eylem | Microsoft. Network/Virtualyönlendiriciler/Delete | VirtualRouter siler |
> | Eylem | Microsoft. Network/Virtualyönlendiriciler/JOIN/Action | Bir VirtualRouter birleştirir. Alertable değil. |
> | Eylem | Microsoft. Network/Virtualyönlendiricilerle/okunan | Bir VirtualRouter alır |
> | Eylem | Microsoft. Network/Virtualyönlendiriciler/Virtualrouterpeerler/Sil | Virtualroutereşlemesindeki eşlemeyi siler |
> | Eylem | Microsoft. Network/Virtualyönlendiriciler/Virtualrouterpeerler/okundu | Virtualroutereşleme alır |
> | Eylem | Microsoft. Network/Virtualyönlendiriciler/Virtualrouterpeerler/yaz | Virtualroutereşleme oluşturur veya var olan Virtualroutereşlemesindeki bir eşlemeyi güncelleştirir |
> | Eylem | Microsoft. Network/Virtualyönlendiriciler/Write | Bir Virtualyönlendirici oluşturur veya var olan bir VirtualRouter 'ı güncelleştirir |
> | Eylem | Microsoft. Network/Virtualwan/Delete | Bir sanal WAN siler |
> | Eylem | Microsoft. Network/virtualwan/generateVpnProfile/Action | VirtualWanVpnServerConfiguration VpnProfile oluştur |
> | Eylem | Microsoft. Network/Virtualwan/Read | Sanal WAN alın |
> | Eylem | Microsoft. Network/virtualwan/supportedSecurityProviders/Read | Desteklenen VirtualWan güvenlik sağlayıcılarını alır. |
> | Eylem | Microsoft. Network/Virtualwan/Virtualhub/Read | Bir sanal WAN 'a başvuran tüm sanal hub 'Ları alır. |
> | Eylem | Microsoft. Network/virtualwan/vpnconfiguration/Action | VPN yapılandırmasını alır |
> | Eylem | Microsoft. Network/virtualwan/vpnServerConfigurations/Action | VirtualWanVpnServerConfigurations al |
> | Eylem | Microsoft. Network/Virtualwan/vpnSites/Read | Bir sanal WAN 'a başvuran tüm VPN sitelerini alır. |
> | Eylem | Microsoft. Network/Virtualwan/Write | Sanal WAN oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Network/Vpngateway/Delete | Bir VpnGateway siler. |
> | Eylem | Microsoft. Network/vpngateway/listvpnconnectionshegizli/Action | Bir VpnGateway 'de bir bağlantı kümesinin tümü veya bir alt kümesi için bağlantı durumunu alır |
> | Eylem | Microsoft. Network/Vpngateway/Read | Bir VpnGateway alır. |
> | Eylem | Microsoft. Network/vpngateway/Reset/Action | VpnGateway 'i sıfırlar |
> | Eylem | Microsoft. Network/Vpngateway/vpnConnections/Delete | Bir VpnConnection 'ı siler. |
> | Eylem | Microsoft. Network/Vpngateway/vpnConnections/Read | Bir VpnConnection alır. |
> | Eylem | Microsoft. Network/Vpngateway/vpnConnections/vpnLinkConnections/Read | VPN bağlantısı bağlantısını alır |
> | Eylem | Microsoft. Network/Vpngateway/vpnConnections/Write | Bir VpnConnection koyar. |
> | Eylem | Microsoft. Network/Vpngateway/Write | Bir VpnGateway koyar. |
> | Eylem | Microsoft. Network/vpnServerConfigurations/Delete | VpnServerConfiguration silme |
> | Eylem | Microsoft. Network/vpnServerConfigurations/Read | VpnServerConfiguration al |
> | Eylem | Microsoft. Network/vpnServerConfigurations/Write | VpnServerConfiguration oluştur veya güncelleştir |
> | Eylem | Microsoft. Network/vpnsites/Delete | Bir VPN sitesi kaynağını siler. |
> | Eylem | Microsoft. Network/vpnsites/Read | Bir VPN sitesi kaynağı alır. |
> | Eylem | Microsoft. Network/vpnSites/vpnSiteLinks/Read | VPN sitesi bağlantısını alır |
> | Eylem | Microsoft. Network/vpnsites/Write | Bir VPN sitesi kaynağı oluşturur veya güncelleştirir. |

## <a name="microsoftnotificationhubs"></a>Microsoft. Notificationhub 'Lar

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Notificationhub 'Lar/CheckNamespaceAvailability/Action | Belirtilen bir ad alanı kaynak adının NotificationHub hizmeti içinde kullanılabilir olup olmadığını denetler. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules/eylem | Ad alanı yetkilendirme kuralları açıklaması listesini alın. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules/silme | Ad alanı yetkilendirme kuralını silin. Varsayılan ad alanı yetkilendirme kuralı silinemez.  |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules/ListKeys/Action | Ad alanına bağlantı dizesi al |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules/okuma | Ad alanı yetkilendirme kuralları açıklaması listesini alın. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules/regenerateKeys/ACTION | Ad alanı yetkilendirme kuralı birincil/Ikincil anahtarı yeniden oluştur, yeniden oluşturulması gereken anahtarı belirtin |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules/yazma | Ad alanı düzeyinde yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları, birincil ve Ikincil anahtarlar güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Checbcertificate sertifikası/eylem | Bir ad alanı içinde belirli bir NotificationHub adının kullanılabilir olup olmadığını denetler. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/silme | Ad alanı kaynağını Sil |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules/Action | Bildirim Hub 'ı yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules/Delete | Bildirim Hub 'ı yetkilendirme kurallarını Sil |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules/ListKeys/Action | Bağlantı dizesini Bildirim Hub 'ına al |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules/Read | Bildirim Hub 'ı yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules/regenerateKeys/ACTION | Bildirim Hub 'ı yetkilendirme kuralı birincil/Ikincil anahtarı yeniden oluşturma, yeniden oluşturulması gereken anahtarı belirtin |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules/Write | Bildirim Hub 'ı yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları, birincil ve Ikincil anahtarlar güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/debugSend/Action | Test anında iletme bildirimi gönderin. |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub 'Lar/silme | Bildirim Hub 'ı kaynağını Sil |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub 'Lar/metricDefinitions/okuma | Ad alanı ölçümleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/pnsCredentials/Action | Tüm Notification Hub 'ı kimlik bilgilerini alın. Bu, WNS, MPNS, APNS, GCM ve Baidu kimlik bilgilerini içerir |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/okuma | Bildirim Hub 'ı kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/yazma | Bir Bildirim Hub 'ı oluşturun ve özelliklerini güncelleştirin. Özellikleri genellikle PNS kimlik bilgilerini içerir. Yetkilendirme kuralları ve TTL |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/okuma | Ad alanı kaynağı açıklaması listesini al |
> | Eylem | Microsoft. Notificationhub 'Lar/ad alanları/yazma | Bir ad alanı kaynağı oluşturun ve özelliklerini güncelleştirin. Ad alanının etiketleri ve kapasitesi, güncelleştirilebilen özelliklerdir. |
> | Eylem | Microsoft. Notificationhub 'Lar/operationResults/Read | Notification Hubs sağlayıcısı için işlem sonuçlarını döndürür |
> | Eylem | Microsoft. Notificationhub 'Lar/işlemler/okuma | Notification Hubs sağlayıcısı için desteklenen işlemlerin listesini döndürür |
> | Eylem | Microsoft. Notificationhub/Register/ACTION | Notificationhub 'Lar kaynak sağlayıcısı için aboneliği kaydeder ve ad alanları ile Notificationhub 'ların oluşturulmasını sunar |
> | Eylem | Microsoft. Notificationhub 'Lar/kaydını kaldırma/eylem | Notificationhub 'Lar kaynak sağlayıcısı için aboneliğin kaydını siler ve ad alanları ile Notificationhub 'ların oluşturulmasını etkinleştirilir |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. OffAzure/HyperVSites/kümeler/okuma | Hyper-V kümesinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/kümeler/Write | Hyper-V kümesi oluşturur veya güncelleştirir |
> | Eylem | Microsoft. OffAzure/HyperVSites/Delete | Hyper-V sitesini siler |
> | Eylem | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Hyper-V kaynağı için sistem durumu özetini alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/konaklar/Read | Hyper-V konağının özelliklerini alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/konaklar/Write | Hyper-V konağını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. OffAzure/HyperVSites/Jobs/Read | Hyper-V işlerinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/makineler/okuma | Hyper-V makinelerinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/operationsstatus/Read | Hyper-V işlem durumunun özelliklerini alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/Read | Hyper-V sitesinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/hiper sanal siteler/Yenile/eylem | Hyper-V sitesi içindeki nesneleri yeniler |
> | Eylem | Microsoft. OffAzure/HyperVSites/runasaccounts/Read | Hyper-V Farklı Çalıştır hesaplarının özelliklerini alır |
> | Eylem | Microsoft. OffAzure/hiper sanal siteler/kullanım/okuma | Hyper-V sitesinin kullanımlarını alır |
> | Eylem | Microsoft. OffAzure/HyperVSites/Write | Hyper-V sitesini oluşturur veya güncelleştirir |
> | Eylem | Microsoft. OffAzure/Işlemler/okuma | Gösterilen işlemleri okur |
> | Eylem | Microsoft. OffAzure/Register/ACTION | Aboneliği Microsoft. OffAzure kaynak sağlayıcısına kaydeder |
> | Eylem | Microsoft. OffAzure/Sunucusites/Jobs/Read | Sunucu işlerinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/Sunucusites/makineler/Read | Bir sunucu makinenin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/Sunucusites/makineler/Write | Sunucu makinelerinin özelliklerini yazma |
> | Eylem | Microsoft. OffAzure/Sunucusites/operationsstatus/Read | Sunucu işlemi durumunun özelliklerini alır |
> | Eylem | Microsoft. OffAzure/Sunucusites/Read | Sunucu sitesinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/Sunucusites/Refresh/ACTION | Sunucu sitesi içindeki nesneleri yeniler |
> | Eylem | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Sunucu farklı Çalıştır hesaplarının özelliklerini alır |
> | Eylem | Microsoft. OffAzure/Sunucusites/kullanım/okuma | Sunucu sitesinin kullanımlarını alır |
> | Eylem | Microsoft. OffAzure/Sunucusites/Write | Sunucu sitesini oluşturur veya güncelleştirir |
> | Eylem | Microsoft. OffAzure/VMwareSites/Delete | VMware sitesini siler |
> | Eylem | Microsoft. OffAzure/VMwareSites/healthsummary/Read | VMware kaynağı için sistem durumu özetini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/Jobs/Read | Bir VMware işlerinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/makineler/okuma | VMware makinelerinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/makineler/Başlat/eylem | VMware makinelerini başlatma |
> | Eylem | Microsoft. OffAzure/VMwareSites/makineler/durdur/eylem | VMware makinelerini durduruyor |
> | Eylem | Microsoft. OffAzure/VMwareSites/operationsstatus/Read | Bir VMware işlem durumunun özelliklerini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/Read | Bir VMware sitesinin özelliklerini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/Refresh/Action | VMware sitesi içindeki nesneleri yeniler |
> | Eylem | Microsoft. OffAzure/VMwareSites/runasaccounts/Read | VMware Farklı Çalıştır hesaplarının özelliklerini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/kullanım/okuma | Bir VMware sitesinin kullanımlarını alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/vcenters/Read | VMware vCenter özelliklerini alır |
> | Eylem | Microsoft. OffAzure/VMwareSites/vcenters/Write | VMware vCenter 'ı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. OffAzure/VMwareSites/Write | VMware sitesini oluşturur veya güncelleştirir |

## <a name="microsoftoperationalinsights"></a>Microsoft. Operationalınsights

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Operationalınsights/linkTargets/okuma | Bir Azure aboneliği ile ilişkilendirilmemiş mevcut hesapları listeler. Bu Azure aboneliğini bir çalışma alanına bağlamak için, çalışma alanı oluştur işleminin müşteri kimliği özelliği içinde bu işlem tarafından döndürülen bir müşteri kimliği kullanın. |
> | Eylem | Microsoft. operationalınsights/işlemler/okuma | Tüm kullanılabilir Operationalınsights REST API işlemlerini listeler. |
> | Eylem | Microsoft. operationalınsights/Register/ACTION | Aboneliği yeniden yönlendirme. |
> | Eylem | Microsoft. Operationalınsights/Register/ACTION | Bir kaynak sağlayıcısına abonelik kaydedin. |
> | Eylem | Microsoft. operationalınsights/kaydını kaldırma/eylem | Aboneliğin kaydını siler. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/eylem | Yeni altyapıyı kullanarak arama yapın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/şema/okuma | Arama şeması v2 'yi alın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/API/sorgu/eylem | Yeni altyapıyı kullanarak arama yapın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/API/sorgu/şema/okuma | Arama şeması v2 'yi alın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/configurationScopes/Sil | Yapılandırma kapsamını Sil |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/configurationScopes/okuma | Yapılandırma kapsamını al |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/configurationScopes/yaz | Yapılandırma kapsamını ayarla |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/Sil | Bir çalışma alanındaki veri kaynaklarını silin. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/okuma | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/yazma | Bir çalışma alanında veri kaynakları oluşturun/güncelleştirin. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/Sil | Bir çalışma alanını siler. Çalışma alanı, oluşturma sırasında var olan bir çalışma alanına bağlanmışsa, bağlantılı olduğu çalışma alanı silinmez. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/ağ geçitleri/silme | Çalışma alanı için yapılandırılmış bir ağ geçidini kaldırır. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/generateregistrationcertificate/Action | Çalışma alanı için kayıt sertifikası oluşturur. Bu sertifika, Microsoft System Center Operation Manager 'ı çalışma alanına bağlamak için kullanılır. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/Disable/Action | Belirli bir çalışma alanı için bir zeka paketini devre dışı bırakır. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/Enable/Action | Belirli bir çalışma alanı için bir zeka paketi sunar. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/okuma | Belirli bir çalışma alanı için görünür olan tüm akıllı zeka paketlerini listeler ve ayrıca paketin bu çalışma alanı için etkin veya devre dışı olduğunu listeler. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/linkedServices/Delete | Belirtilen çalışma alanı altındaki bağlı hizmetleri silin. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/linkedServices/okuma | Belirtilen çalışma alanı altındaki bağlı hizmetleri alın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/linkedServices/Write | Belirtilen çalışma alanı altındaki bağlı hizmetleri oluşturun/güncelleştirin. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/listKeys/ACTION | Çalışma alanının liste anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/listKeys/Read | Çalışma alanının liste anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/managementGroups/okuma | Bu çalışma alanına bağlı System Center Operations Manager yönetim gruplarının adlarını ve meta verilerini alır. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/metricDefinitions/okuma | Çalışma alanı altında ölçüm tanımlarını al |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/notificationSettings/Delete | Çalışma alanının kullanıcı bildirim ayarlarını silin. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/notificationSettings/Read | Çalışma alanının kullanıcı bildirim ayarlarını alın. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/notificationSettings/Write | Çalışma alanının kullanıcı bildirim ayarlarını belirleyin. |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/işlemler/okuma | Bir Operationalınsights çalışma alanı işleminin durumunu alır. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/temizleme/eylem | Belirtilen verileri çalışma alanından Sil |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/AADDomainServicesAccountLogon/Read | AADDomainServicesAccountLogon tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AADDomainServicesAccountManagement/Read | AADDomainServicesAccountManagement tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AADDomainServicesDirectoryServiceAccess/Read | AADDomainServicesDirectoryServiceAccess tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AADDomainServicesLogonLogoff/Read | AADDomainServicesLogonLogoff tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AADDomainServicesPolicyChange/Read | AADDomainServicesPolicyChange tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AADDomainServicesPrivilegeUse/Read | AADDomainServicesPrivilegeUse tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AADDomainServicesSystemSecurity/Read | AADDomainServicesSystemSecurity tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Adassessmentönerisi/okuma | Adassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AddonAzureBackupAlerts/okuma | AddonAzureBackupAlerts tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AddonAzureBackupJobs/okuma | AddonAzureBackupJobs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AddonAzureBackupPolicy/okuma | AddonAzureBackupPolicy tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AddonAzureBackupProtectedInstance/okuma | AddonAzureBackupProtectedInstance tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AddonAzureBackupStorage/okuma | AddonAzureBackupStorage tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ADFActivityRun/Read | ADFActivityRun tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Adfardışık düzen eylemsizlik/Read | Adfardışık düzen eylemsizlik tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ADFTriggerRun/Read | ADFTriggerRun tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ADReplicationResult/okundu | ADReplicationResult tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Adsecurityassessmentönerisi/okuma | Adsecurityassessmentönerisi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/uyarı/okuma | Uyarı tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AlertHistory/Read | AlertHistory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AmlComputeClusterEvent/okuma | AmlComputeClusterEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AmlComputeClusterNodeEvent/okuma | AmlComputeClusterNodeEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AmlComputeJobEvent/okuma | AmlComputeJobEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ApiManagementGatewayLogs/Read | ApiManagementGatewayLogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Appcenterhata/okuma | AppCenterError tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ApplicationInsights/okuma | ApplicationInsights tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Appplatformlogsforyay/okuma | AppPlatformLogsforSpring tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | AppServiceEnvironmentPlatformLogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AuditLogs/okuma | AuditLogs tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AutoscaleEvaluationsLog/okuma | AutoscaleEvaluationsLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/oto Scalescaleactionslog/Read | Oto Scalescaleactionslog tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Awscses Izi/okuma | Awscses Iziz tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AzureActivity/okuma | AzureActivity tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/Azureassessmentönerisi/okuma | Azureassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/AzureMetrics/okuma | AzureMetrics tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/BaiClusterEvent/Read | BaiClusterEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/BaiClusterNodeEvent/Read | BaiClusterNodeEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/BaiJobEvent/okuma | BaiJobEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/BlockchainApplicationLog/Read | BlockchainApplicationLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/BlockchainProxyLog/Read | BlockchainProxyLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/BoundPort/Read | BoundPort tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/CommonSecurityLog/Read | CommonSecurityLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/bilgisayar grubu/okuma | ComputerGroup tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ConfigurationChange/Read | ConfigurationChange tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ConfigurationData/Read | ConfigurationData tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Containerımageınventory/Read | Containerımageınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Containerınventory/Read | Containerınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ContainerLog/Read | ContainerLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Containernodeınventory/Read | Containernodeınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/ContainerRegistryLoginEvents/Read | ContainerRegistryLoginEvents tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Containerregistrydepotoryevents/Read | Containerregistrydepotoryevents tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ContainerServiceLog/okuma | ContainerServiceLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/CoreAzureBackup/Read | CoreAzureBackup tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DatabricksAccounts/okuma | DatabricksAccounts tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/DatabricksClusters/okuma | DatabricksClusters tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/DatabricksDBFS/Read | DatabricksDBFS tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Databricksınstancepools/Read | Databricksınstancepools tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Databricksk Işleri/okuma | DatabricksJobs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Databricksnot/okuma | DatabricksNotebook tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Databricksgizlilikler/Read | Databricksgizlilikler tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Databricksssınpermissions/Read | Databricksstopermissions tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DatabricksSSH/Read | DatabricksSSH tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/DatabricksTables/Read | DatabricksTables tablosundan veri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/DatabricksWorkspace/Read | DatabricksWorkspace tablosundan veri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceAppCrash/okuma | DeviceAppCrash tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Deviceapplayeniden Başlat/oku | Deviceapplayeniden Başlat tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceCalendar/Read | DeviceCalendar tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Devicectaanup/okuma | Devicectaanup tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/DeviceConnectSession/Read | DeviceConnectSession tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceEtw/okuma | DeviceEtw tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceHardwareHealth/Read | DeviceHardwareHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceHealth/Read | DeviceHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Devicesinyal/okuma | Devicesinyal tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceSkypeHeartbeat/okuma | DeviceSkypeHeartbeat tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DeviceSkypeSignIn/okuma | DeviceSkypeSignIn tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Devicesliepstate/Read | Devicestaepstate tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Dnne hatası/okuma | Dılure tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Dakppresorumluluğun/okunan | Dappreyükümlülük tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Dhdrivergüvenirlik/okuma | Dhdrivergüvenirlik tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Dhlogonhatalarıyla/okuma | Dhlogonarızaları tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Dhlogonölçümlerini/Read | Dhlogonölçümlerini tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Dhoatık Kıdata/Read | Dhoatık verileri tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Dhosgüvenilirliği/okuma | Dhosgüvenirlik tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DHWipAppLearning/okuma | DHWipAppLearning tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/DnsEvents/okuma | DnsEvents tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Dnsınventory/okuma | DnsInventory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ETWEvent/okuma | ETWEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/olay/okuma | Olay tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ExchangeAssessmentRecommendation/okuma | ExchangeAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ExchangeOnlineAssessmentRecommendation/okuma | ExchangeOnlineAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/FailedIngestion/Read | FailedIngestion tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/sinyal/okuma | Sinyal tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/HuntingBookmark/Read | HuntingBookmark tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/IISAssessmentRecommendation/okuma | IISAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ınboundconnection/Read | Inboundconnection tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ınsightsölçümlerini/Read | Insightsölçümlerini tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ıntuneauditlogs/Read | Intuneauditlogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ıntuneoperationallogs/Read | Intuneoperationallogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/KubeEvents/okuma | KubeEvents tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/KubeHealth/Read | KubeHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Kubenodeınventory/Read | Kubenodeınventory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/KubePodInventory/okuma | KubePodInventory tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/KubeServices/Read | KubeServices tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/LinuxAuditLog/Read | LinuxAuditLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/MAApplication/Read | MAApplication tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAApplicationHealth/Read | MAApplicationHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/MAApplicationHealthAlternativeVersions/Read | MAApplicationHealthAlternativeVersions tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Maapplicationhealthsorunlar/okuma | Maapplicationhealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maapplicationınstance/Read | Maapplicationınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maapplicationınstancereadsürekliliği/okuma | Maapplicationınstancereadılmi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAApplicationReadiness/Read | MAApplicationReadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MADeploymentPlan/okuma | MADeploymentPlan tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MADevice/okuma | MADevice tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Madevicenotentopla/Read | Madevicenotentoplatable 'dan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MADeviceNRT/okuma | MADeviceNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/MADevicePnPHealth/Read | MADevicePnPHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MADevicePnPHealthAlternativeVersions/Read | MADevicePnPHealthAlternativeVersions tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Madevicepnphealthsorunlar/okuma | Madevicepnphealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Madevicereadsürekliliği/okuma | Madevicereadtıo tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Madriverınstancereadsürekliliği/okuma | Madriverınstancereadılmi tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MADriverReadiness/Read | MADriverReadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddın/Read | Maofficeeklenti tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddınentityhealth/Read | Maofficeaddınentityhealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddınhealth/Read | Maofficeaddınhealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAOfficeAddinHealthEventNRT/okuma | MAOfficeAddinHealthEventNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddınhealthsorunlar/okuma | Maofficeaddınhealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddınınstance/Read | Maofficeaddınınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddınınstancereadsürekliliği/okuma | Maofficeaddınınstancereadılmi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeaddınreadiness/Read | Maofficeaddınreadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeuygulaması/okuma | Maofficeuygulama tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MAOfficeAppCrashesNRT/okuma | MAOfficeAppCrashesNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/MAOfficeAppHealth/Read | MAOfficeAppHealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeappınstance/Read | Maofficeappınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficeappınstancehealth/Read | Maofficeappınstancehealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAOfficeAppReadiness/Read | MAOfficeAppReadiness tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MAOfficeAppSessionsNRT/okuma | MAOfficeAppSessionsNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficebuilınfo/Read | Maofficebuilınfo tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAOfficeCurrencyAssessment/okuma | MAOfficeCurrencyAssessment tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAOfficeCurrencyAssessmentDailyCounts/okuma | MAOfficeCurrencyAssessmentDailyCounts tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/MAOfficeDeploymentStatus/Read | MAOfficeDeploymentStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MAOfficeDeploymentStatusNRT/Read | MAOfficeDeploymentStatusNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakroerrornrt/okuma | MAOfficeMacroErrorNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakroglobalhealth/Read | Maofficemakroglobalhealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakrosağlığı/okuma | Maofficemakrohealth tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakrohealthsorunlar/okuma | Maofficemacrohealthsorunlar tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakroıssueınstancec/Read | Maofficemacroıssueınstancectablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakroıssuereadsürekliliği/okuma | Maofficemacroıssuereadılmi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficemakroözeti/okuma | MAOfficeMacroSummary tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAOfficeSuite/okuma | MAOfficeSuite tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Maofficesuiteınstance/Read | Maofficesuiteınstance tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MAProposedPilotDevices/okuma | MAProposedPilotDevices tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Mawindowsbuilınfo/Read | Mawindowsbuilınfo tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Mawindowscurrencment/Read | Mawindowscurrencyaskment tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Mawindowscurrencyassessmentgünlükcounts/okuma | MAWindowsCurrencyAssessmentDailyCounts tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sorgu/MAWindowsDeploymentStatus/Read | MAWindowsDeploymentStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MAWindowsDeploymentStatusNRT/okuma | MAWindowsDeploymentStatusNRT tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Mawindowssysreqınstancereadsürekliliği/okuma | Mawindowssysreqınstancereadtıı tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/McasShadowItReporting/okuma | McasShadowItReporting tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MicrosoftDataShareShareLog/okuma | MicrosoftDataShareShareLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Microsofthealthumapisauditlogs/Read | Microsofthealthınumtaauditlogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Microsoftinsıizsazureactivitylog/Read | Microsoftınsı, Sazureactivitylog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MicrosoftWebApplicationLog/Read | MicrosoftWebApplicationLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/MicrosoftWebFunctionExecutionLogs/Read | MicrosoftWebFunctionExecutionLogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MicrosoftWebStdOutStdErrLog/okuma | MicrosoftWebStdOutStdErrLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/MicrosoftWebW3CLog/okuma | MicrosoftWebW3CLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/NetworkMonitoring/Read | NetworkMonitoring tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Officeetkinliği/okuma | OfficeActivity tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Işlem/okuma | Işlem tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/OutboundConnection/Read | OutboundConnection tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/perf/okuma | Perf tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ProtectionStatus/Read | ProtectionStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/okuma | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ReservedAzureCommonFields/okuma | ReservedAzureCommonFields tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/ReservedCommonFields/Read | ReservedCommonFields tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Sccmassessmentönerisi/okuma | Sccmassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Scomassessmentönerisi/okuma | Scomassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/SecurityAlert/Read | SecurityAlert tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/SecurityBaseline/okuma | SecurityBaseline tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/SecurityBaselineSummary/Read | SecurityBaselineSummary tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/SecurityDetection/okuma | SecurityDetection tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/SecurityEvent/Read | SecurityEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Securityıotrawevent/Read | Securityıotrawevent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Securityönerisi/okuma | Securityöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ServiceFabricOperationalEvent/Read | ServiceFabricOperationalEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ServiceFabricReliableActorEvent/Read | ServiceFabricReliableActorEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/ServiceFabricReliableServiceEvent/Read | ServiceFabricReliableServiceEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Sfbassessmentönerisi/okuma | Sfbassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/SfBOnlineAssessmentRecommendation/okuma | SfBOnlineAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/SharePointOnlineAssessmentRecommendation/okuma | SharePointOnlineAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Signınlogs/Read | Signınlogs tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Spassessmentönerisi/okuma | Spassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Sqlassessmentönerisi/okuma | Sqlassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/SQLQueryPerformance/Read | SQLQueryPerformance tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Sqlthreatprotectionlogindenetimleri/okuma | Sqlthreatprotectionlogindenetimleri tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/SQL,, ıyassessmentresult/Read | SQLbir bu sonuç tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/SucceededIngestion/okuma | SucceededIngestion tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Syslog/okuma | Syslog tablosundan veri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/SysmonEvent/Read | SysmonEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/tablolar. Custom/Read | Herhangi bir özel günlükteki verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Threatıntelligenceındicator/Read | Threatıntelligenceındicator tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Uıaapp/Read | UAApp tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Uıacomputer/Read | UAComputer tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/UAComputerRank/Read | Uıacomputerrank tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/UADriver/okuma | Uıadriver tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Uıadriverproblemcodes/Read | Uıadriverproblemcodes tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/UAFeedback/Read | UAFeedback tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Uıahardwaresecurity/Read | Uıahardwaresecurity tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Uaıesitediscovery/Read | Uıaııesitediscovery tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Uaofficeaddın/Read | Uıaofficeaddın tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/UAProposedActionPlan/okuma | UAProposedActionPlan tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Uasysreqıssue/Read | Uıasysreqıssue tablosundaki verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Uayükseltilebilir Dedcomputer/Read | Uayükseltilebilir Dedcomputer tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/güncelleştirme/okuma | Güncelleştirme tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/UpdateRunProgress/Read | UpdateRunProgress tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/UpdateSummary/Read | UpdateSummary tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/kullanım/okuma | Kullanım tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/VMBoundPort/Read | VMBoundPort tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/VMConnection/Read | VMConnection tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/W3CIISLog/okuma | W3CIISLog tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/WaaSDeploymentStatus/Read | WaaSDeploymentStatus tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Waasınsiderstatus/Read | Waasınsiderstatus tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/WaaSUpdateStatus/Read | WaaSUpdateStatus tablosundan verileri oku |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/WDAVStatus/Read | WDAVStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/WDAVThreat/Read | WDAVThreat Table 'dan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/Windowsclientassessmentönerisi/okuma | Windowsclientassessmentöneriyi tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/WindowsEvent/okuma | WindowsEvent tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/WindowsFirewall/Read | WindowsFirewall tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/WindowsServerAssessmentRecommendation/okuma | WindowsServerAssessmentRecommendation tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/kablolu veri/okuma | Kablolu veri tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/Workspaces/Query/WorkloadMonitoringPerf/Read | WorkloadMonitoringPerf tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/Wudoaggregişlendiği durumu/okuma | Wudoaggreg, Status tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sorgu/WUDOStatus/okuma | WUDOStatus tablosundan verileri okuma |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/okuma | Mevcut bir çalışma alanını alır |
> | Eylem | Microsoft. Operationalınsights/Workspaces/regeneratesharedkey/Action | Belirtilen çalışma alanı paylaşılan anahtarını yeniden oluşturur |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/kurallar/okuma | Tüm uyarı kurallarını al. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/silme | Kaydedilmiş bir arama sorgusunu siler |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/okuma | Kayıtlı bir arama sorgusu alır |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/sonuçlar/okuma | Kaydedilmiş aramaları al sonuçları. Kullanım Dışı |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/zamanlamalar/eylemler/silme | Zamanlanmış arama eylemlerini silin. |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/zamanlamalar/eylemler/okuma | Zamanlanmış arama eylemleri alın. |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/zamanlamalar/eylemler/yazma | Zamanlanmış arama eylemleri oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/zamanlamalar/silme | Zamanlanan aramaları silin. |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/zamanlamalar/okuma | Zamanlanmış aramalar alın. |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/savedaramalar/zamanlamalar/yazma | Zamanlanmış aramalar oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/yazma | Kaydedilmiş bir arama sorgusu oluşturur |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/şema/okuma | Çalışma alanının arama şemasını alır.  Arama şeması, sunulan alanları ve bunların türlerini içerir. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/arama/eylem | Arama sorgusu yürütür |
> | Eylem | Microsoft. operationalınsights/çalışma alanları/arama/okuma | Arama sonuçlarını al. Kullanım dışı. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/sharedKeys/Action | Çalışma alanının paylaşılan anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/sharedKeys/Read | Çalışma alanının paylaşılan anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/storageınsiizconfigs/Delete | Depolama yapılandırmasını siler. Bu, Microsoft operasyonel içgörüler 'in depolama hesabından veri okumasını durdurur. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/storageınsiizconfigs/Read | Bir depolama yapılandırması alır. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/storageınsiizconfigs/Write | Yeni bir depolama yapılandırması oluşturur. Bu yapılandırma, mevcut bir depolama hesabındaki bir konumdan veri çekmek için kullanılır. |
> | Eylem | Microsoft. Operationalınsights/Workspaces/upgradetranslationhatalarıyla/okuma | Çalışma alanı için arama yükseltme çevirisi hata günlüğünü al |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/kullanımlar/okuma | Çalışma alanı tarafından okunan veri miktarı dahil olmak üzere çalışma alanı için kullanım verilerini alır. |
> | Eylem | Microsoft. Operationalınsights/çalışma alanları/yazma | Mevcut çalışma alanından müşteri kimliğini sağlayarak yeni bir çalışma alanı veya mevcut bir çalışma alanına bağlantılar oluşturur. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. OperationsManagement/managementAssociations/Delete | Varolan yönetim Ilişkilendirmesini Sil |
> | Eylem | Microsoft. OperationsManagement/managementAssociations/Read | Varolan yönetim Ilişkilendirmesini al |
> | Eylem | Microsoft. OperationsManagement/managementAssociations/Write | Yeni bir yönetim Ilişkilendirmesi oluştur |
> | Eylem | Microsoft. OperationsManagement/managementConfigurations/Delete | Varolan yönetim yapılandırmasını sil |
> | Eylem | Microsoft. OperationsManagement/managementConfigurations/okuma | Varolan yönetim yapılandırmasını al |
> | Eylem | Microsoft. OperationsManagement/managementConfigurations/Write | Yeni bir yönetim yapılandırması oluştur |
> | Eylem | Microsoft. OperationsManagement/Register/Action | Bir kaynak sağlayıcısına abonelik kaydedin. |
> | Eylem | Microsoft. OperationsManagement/Solutions/Delete | Mevcut OMS çözümünü Sil |
> | Eylem | Microsoft. OperationsManagement/Solutions/Read | OMS çözümünü çıkmadan al |
> | Eylem | Microsoft. OperationsManagement/Solutions/Write | Yeni OMS çözümü oluştur |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Poliyeleghts/asyncOperationResults/Read | Zaman uyumsuz işlem sonucunu alır. |
> | DataAction | Microsoft. Policınghts/Checkdatapolicyuyumluluk/eylem | Veri ilkelerine karşı belirli bir bileşenin uyumluluk durumunu kontrol edin. |
> | Eylem | Microsoft. Poliyeleghts/işlemler/okuma | Microsoft. Policınsıghts ad alanı üzerinde desteklenen işlemleri alır |
> | DataAction | Microsoft. Poliyeleghts/policyEvents/logDataEvents/eylem | Kaynak bileşen ilkesi olaylarını günlüğe kaydedin. |
> | Eylem | Microsoft. Poliyeleghts/policyEvents/queryResults/Action | İlke olayları hakkında sorgu bilgileri. |
> | Eylem | Microsoft. Poliyeleghts/policyEvents/queryResults/Read | İlke olayları hakkında sorgu bilgileri. |
> | Eylem | Microsoft. Poliyeleghts/policyStates/queryResults/Action | İlke durumlarıyla ilgili bilgileri sorgulayın. |
> | Eylem | Microsoft. Poliyeleghts/policyStates/queryResults/Read | İlke durumlarıyla ilgili bilgileri sorgulayın. |
> | Eylem | Microsoft. Poliyeleghts/policyStates/özetleme/eylem | İlke en son durumlarıyla ilgili özet bilgileri sorgulayın. |
> | Eylem | Microsoft. Poliyeleghts/policyStates/özetleme/okuma | İlke en son durumlarıyla ilgili özet bilgileri sorgulayın. |
> | Eylem | Microsoft. Poliyeleghts/policyStates/triggerEvaluation/Action | Seçili kapsam için yeni bir uyumluluk değerlendirmesi tetikler. |
> | Eylem | Microsoft. Poliyeleghts/policyTrackedResources/queryResults/Read | DeployIfNotExists ilkeleri için gereken kaynaklarla ilgili bilgileri sorgulayın. |
> | Eylem | Microsoft. Poliyeleghts/Register/Action | Microsoft Policy Insights kaynak sağlayıcısını kaydeder ve üzerinde eylemler sunar. |
> | Eylem | Microsoft. Poliyeleghts/düzeltme/iptal/eylem | Devam eden Microsoft Ilke düzeltmelerinin iptali. |
> | Eylem | Microsoft. Poliyeleghts/düzeltme/silme | İlke düzeltmelerinin silme. |
> | Eylem | Microsoft. Poliyeleghts/düzeltmelere/Listdağıtımlar/okuma | Bir ilke düzeltmesi için gereken dağıtımları listeler. |
> | Eylem | Microsoft. Poliyeleghts/düzeltme/okuma | İlke düzeltmeleri alın. |
> | Eylem | Microsoft. Poliyeleghts/düzeltme/yazma | Microsoft Policy düzeltmelerinin oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Policınghts/Unregister/eylem | Microsoft Policy Insights kaynak sağlayıcısının kaydını siler. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Portal/konsollar/silme | Cloud Shell örneğini kaldırır. |
> | Eylem | Microsoft. Portal/konsollar/okuma | Cloud Shell örneğini okur. |
> | Eylem | Microsoft. Portal/konsollar/yazma | Cloud Shell örneği oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Portal/panolar/silme | Panoyu abonelikten kaldırır. |
> | Eylem | Microsoft. Portal/panolar/okuma | Abonelik için panoları okur. |
> | Eylem | Microsoft. Portal/panolar/yazma | Bir aboneliğe Pano ekleme veya bu panoyu değiştirme. |
> | Eylem | Microsoft. Portal/kaydet/eylem | Portala kaydolun |
> | Eylem | Microsoft. Portal/UserSettings/Delete | Cloud Shell Kullanıcı ayarlarını kaldırır. |
> | Eylem | Microsoft. Portal/UserSettings/Read | Cloud Shell Kullanıcı ayarlarını okur. |
> | Eylem | Microsoft. Portal/UserSettings/Write | Cloud Shell Kullanıcı ayarı oluşturun veya güncelleştirin. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Powerbiadanmış/kapasiteler/silme | Adanmış Power BI kapasitesini siler. |
> | Eylem | Microsoft. Powerbiadanmış/kapasiteler/okuma | Belirtilen Power BI adanmış kapasitenin bilgilerini alır. |
> | Eylem | Microsoft. Powerbiadanmış/kapasiteler/özgeçmişi/eylem | Kapasiteyi sürdürür. |
> | Eylem | Microsoft. Powerbiadanmış/kapasiteler/SKU/okuma | Kapasite için kullanılabilir SKU bilgilerini alın |
> | Eylem | Microsoft. Powerbiadanmış/kapasiteler/beklet/eylem | Kapasiteyi askıya alır. |
> | Eylem | Microsoft. Powerbiadanmış/kapasiteler/Write | Belirtilen Power BI adanmış kapasiteyi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Powerbiadanmış/konumlar/Checknameavaılabılıty/Action | Verilen Power BI adanmış kapasite adının geçerli olduğunu ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. Powerbiadanmış/konumlar/operationresults/Read | Belirtilen işlem sonucu bilgilerini alır. |
> | Eylem | Microsoft. Powerbiadanmış/konumlar/operationdurumlar/okuma | Belirtilen işlem durumu bilgilerini alır. |
> | Eylem | Microsoft. Powerbiadanmış/işlemler/okuma | İşlem bilgilerini alır |
> | Eylem | Microsoft. Powerbiadanmış/yazmaç/eylem | Adanmış Power BI kaynak sağlayıcısını kaydeder. |
> | Eylem | Microsoft. Powerbiadanmış/SKU/okuma | SKU 'ların bilgilerini alır |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. RecoveryServices/konumlar/allocatedStamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | Eylem | Microsoft. RecoveryServices/Locations/Allocatedamga/eylem | AllocateStamp, hizmet tarafından kullanılan iç işlemdir |
> | Eylem | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Eylem | Microsoft. RecoveryServices/Locations/Backupkorunabilir/Write | Yedekleme korumalı öğesi oluştur |
> | Eylem | Microsoft. RecoveryServices/Locations/Backupkorunabilir/Read | Tüm korumalı öğelerin listesini döndürür. |
> | Eylem | Microsoft. RecoveryServices/konumlar/backupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | Eylem | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Özellikleri doğrulama |
> | Eylem | Microsoft. RecoveryServices/Locations/Checknameavaılabılıty/Action | Kaynak adının kullanılabilirliğini denetle, kaynak adının kullanılabilir olup olmadığını denetlemek için bir API 'dir |
> | Eylem | Microsoft. RecoveryServices/konumlar/operationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | Eylem | Microsoft. RecoveryServices/işlemler/okuma | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | Eylem | Microsoft. RecoveryServices/Register/Action | Belirtilen kaynak sağlayıcısı için aboneliği kaydeder |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupconfig/Read | Kurtarma Hizmetleri Kasası için yapılandırmayı döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupconfig/Write | Kurtarma Hizmetleri Kasası için yapılandırmayı güncelleştirir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupEngines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/Backupprotectionhedefini/silme | Yedekleme koruma hedefini silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuların/Backupprotectionamacını/Read | Yedekleme koruması hedefi al |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/Backupprotectionamacını/Write | Yedekleme koruma hedefi oluşturma |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/operationResults/Read | İşlemin durumunu döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/operationsStatus/Read | İşlemin durumunu döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/korunabilir | Tüm korunabilir kapsayıcıları al |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/Delete | Kayıtlı kapsayıcıyı siler |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/sorgu/eylem | Bir kapsayıcı içindeki iş yükleri için sorgulama yap |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/öğeler/okuma | Bir kapsayıcıdaki tüm öğeleri Al |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/operationResults/Read | Koruma kapsayıcısında gerçekleştirilen Işlemin sonucunu alır. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/operationsStatus/Read | Koruma kapsayıcısında gerçekleştirilen Işlemin durumunu alır. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/korunabilir/yedekleme/eylem | Korumalı öğe için yedekleme gerçekleştirir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/Sil | Korumalı öğeyi siler |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/operationResults/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin sonucunu alır. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/operationsStatus/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin durumunu döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/provisionInstantItemRecovery/eylem | Korumalı öğe için anında öğe kurtarma sağla |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/kurtarma noktaları/okuma | Korumalı öğeler için kurtarma noktalarını alın. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/kurtarma noktaları/geri yükleme/eylem | Korumalı öğeler için kurtarma noktalarını geri yükleyin. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/revokeInstantItemRecovery/eylem | Korumalı öğe için anında öğe kurtarmayı iptal et |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/yazma | Yedekleme korumalı öğesi oluştur |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/okuma | Tüm kayıtlı kapsayıcıları döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/Write | Kayıtlı bir kapsayıcı oluşturur |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupdokuları/refreshContainers/eylem | Kapsayıcı listesini yeniler |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupJobs/iptal/eylem | Işi iptal et |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupJobs/operationResults/Read | Iş Işleminin sonucunu döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupJobs/operationsStatus/Read | Iş Işleminin durumunu döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupJobs/Read | Tüm Iş nesnelerini döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupJobsExport/eylem | Işleri dışarı aktar |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupOperationResults/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi sonucunu döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupOperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupPolicies/Delete | Koruma Ilkesini silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupPolicies/operationResults/Read | Ilke Işleminin sonuçlarını alın. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupPolicies/işlemler/okuma | Ilke Işleminin durumunu alın. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupPolicies/Read | Tüm koruma Ilkelerini döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupPolicies/Write | Koruma Ilkesi oluşturur |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/Read | Tüm korunabilir öğelerin listesini döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/Read | Tüm korumalı öğelerin listesini döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupProtectionContainers/Read | Aboneliğe ait tüm kapsayıcıları döndürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/Backupprotectionhedefleri/okuma | Tüm yedekleme koruma amaçlarını Listele |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupSecurityPIN/Action | Kurtarma Hizmetleri Kasası için güvenlik PIN bilgilerini döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupstorageconfig/Read | Kurtarma Hizmetleri Kasası için depolama yapılandırmasını döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupstorageconfig/Write | Kurtarma Hizmetleri Kasası için depolama yapılandırmasını güncelleştirir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupUsageSummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/backupValidateOperation/Action | Korumalı öğe üzerinde Işlemi doğrula |
> | Eylem | Microsoft. RecoveryServices/Vaults/sertifikalar/yazma | Kaynak sertifikası güncelleştirme işlemi kaynak/kasa kimlik bilgisi sertifikasını güncelleştirir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Delete | Kasayı Sil işlemi, ' kasa ' türündeki belirtilen Azure kaynağını siler |
> | Eylem | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Delete | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Eylem | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Eylem | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Write | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Eylem | Microsoft. RecoveryServices/Kasaults/monitoringAlerts/okuma | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | Eylem | Microsoft. RecoveryServices/Vaults/monitoringAlerts/Write | Uyarıyı çözer. |
> | Eylem | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/okuma | Kurtarma Hizmetleri Kasası bildirim yapılandırmasını alır. |
> | Eylem | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/Write | Kurtarma Hizmetleri kasasına e-posta bildirimlerini yapılandırır. |
> | Eylem | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Eylem | Microsoft. RecoveryServices/Kasaults/registeredIdentities/Delete | Kapsayıcı kaydını silme işlemi, bir kapsayıcının kaydını silmek için kullanılabilir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/registeredIdentities/operationResults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | Eylem | Microsoft. RecoveryServices/Vaults/registeredIdentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/registeredIdentities/Write | Hizmet kapsayıcısını Kaydet işlemi, bir kapsayıcıyı kurtarma hizmeti 'ne kaydetmek için kullanılabilir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationAlertSettings/Read | Tüm uyarı ayarlarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationAlertSettings/Write | Tüm uyarı ayarlarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. RecoveryServices/kasaults/replicationEvents/okuma | Tüm olayları okuyun |
> | Eylem | Microsoft. RecoveryServices/kasaults/Replicationdokuların/Checktutarlılığı/eylemi | Dokunun tutarlılığını denetler |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/silme | Tüm dokuları Sil |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/deployProcessServerImage/eylem | Işlem sunucusu görüntüsünü dağıt |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/migratetoaad/eylem | Dokuyu AAD 'ye geçir |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/operationresults/Read | Kaynak dokularındaki zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/okuma | Tüm yapıları okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/yeniden Ilişkilendirilmesi/eylem | Ağ geçidini yeniden ilişkilendir |
> | Eylem | Microsoft. RecoveryServices/kasaults/Replicationyapılar/kaldırma/eylem | Dokuyu kaldır |
> | Eylem | Microsoft. RecoveryServices/kasaults/Replicationyapılar/yenilenebilir sertifika/eylem | Doku için Sertifikayı Yenile |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationLogicalNetworks/okuma | Tüm mantıksal ağları okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokular/replicationNetworks/Read | Tüm ağları okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationNetworks/replicationNetworkMappings/Delete | Tüm ağ eşlemelerini silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationNetworks/replicationNetworkMappings/Read | Tüm ağ eşlemelerini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationNetworks/replicationNetworkMappings/Write | Herhangi bir ağ eşlemesi oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Discoverlıtableıtem/Action | Korunabilir öğeyi bul |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/operationresults/Read | Kaynak koruma kapsayıcılarındaki zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Read | Tüm koruma kapsayıcılarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Remove/Action | Koruma kapsayıcısını kaldır |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/Delete | Tüm geçiş öğelerini silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/Migrate/Action | Öğeyi geçir |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/migrationRecoveryPoints/Read | Tüm geçiş kurtarma noktalarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/operationresults/Read | Kaynak geçiş öğelerinde zaman uyumsuz bir işlemin sonuçlarını izleyin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/Read | Herhangi bir geçiş öğesini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/testMigrate/Action | Test geçişi |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/testMigrateCleanup/Action | Test geçişini Temizleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems/Write | Herhangi bir geçiş öğesi oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir | Korunabilir öğeleri oku |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/addDisks/Action | Disk ekleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/applyRecoveryPoint/Action | Kurtarma noktası Uygula |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/Delete | Tüm korumalı öğeleri sil |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/işlem | Yük devretme yürütmesi |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/operationresults/Read | Kaynak korumalı öğelerde zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Plannedyük devretme/eylem | Planlı Yük devretme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Read | Tüm korumalı öğeleri okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/recoveryPoints/Read | Tüm çoğaltma kurtarma noktalarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/kaldırma/eylem | Korumalı öğeyi kaldır |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/removeDisks/Action | Diskleri kaldır |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/\ çoğaltma/eylem | Çoğaltmayı Onar |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorudıtems/reProtect/ACTION | Korumalı öğeyi yeniden koru |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/ResolveHealthErrors/Action |  |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/geri bildirim/eylem | Geri bildirim gönder |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/targetComputeSizes/Read | Herhangi bir hedef Işlem boyutunu okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/TEMS/testFailover/ACTION | Test Yük Devretmesi |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/test Failovercleanup/Action | Yük devretme sınamasını Temizleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/unplannedFailover/ACTION | Yük Devretme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Updatebir Dıtems/Update, Ityservice/Action | Mobility hizmetini Güncelleştir |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Write | Korunan öğeleri oluşturma veya güncelleştirme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/Delete | Tüm koruma kapsayıcısı eşlemelerini silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/Read | Kaynak koruma kapsayıcısı eşlemelerinde zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Tüm koruma kapsayıcısı eşlemelerini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/Remove/Action | Koruma kapsayıcısı eşlemesini Kaldır |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/Write | Herhangi bir koruma kapsayıcısı eşlemesi oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/switchprotection/eylem | Koruma kapsayıcısını Değiştir |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Write | Herhangi bir koruma kapsayıcısı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationRecoveryServicesProviders/Delete | Tüm kurtarma hizmetleri sağlayıcılarını silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationRecoveryServicesProviders/operationresults/Read | Kaynak kurtarma hizmetleri sağlayıcıları üzerinde zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationRecoveryServicesProviders/Read | Tüm kurtarma hizmetleri sağlayıcılarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationRecoveryServicesProviders/refreshProvider/Action | Sağlayıcıyı Yenile |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationRecoveryServicesProviders/Remove/Action | Kurtarma Hizmetleri sağlayıcısını kaldır |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationRecoveryServicesProviders/Write | Herhangi bir kurtarma hizmetleri sağlayıcısı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/Replicationstorageclassıfler/okuma | Tüm depolama sınıflandırmalarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/Replicationstorageclassıflıya/Replicationstorageclassıficationmappings/Delete | Tüm depolama sınıflandırması eşlemelerini silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/Replicationstorageclassıflıya/Replicationstorageclassıficationmappings/operationresults/Read | Kaynak depolama sınıflandırması eşlemelerinde zaman uyumsuz bir işlemin sonuçlarını izleyin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/Replicationstorageclassıflıya/Replicationstorageclassıficationmappings/Read | Tüm depolama sınıflandırması eşlemelerini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationdokuların/Replicationstorageclassıfmalar/Replicationstorageclassıficationmappings/Write | Tüm depolama sınıflandırması eşlemelerini oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationvCenters/Delete | Tüm sanal merkezleri silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationvCenters/operationresults/Read | Kaynak sanal merkezlerindeki zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationvCenters/Read | Tüm sanal merkezleri okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationvCenters/Write | Tüm sanal merkezleri oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationyapılar/yazma | Tüm yapılar oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationJobs/iptal/eylem | Işi iptal et |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationJobs/operationresults/Read | Kaynak Işlerinde zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/kasaults/replicationJobs/okuma | Tüm Işleri okuyun |
> | Eylem | Microsoft. RecoveryServices/kasaults/replicationJobs/yeniden Başlat/eylem | İşi yeniden Başlat |
> | Eylem | Microsoft. RecoveryServices/kasaults/replicationJobs/özgeçmişi/eylem | Işi sürdürür |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationmigrationıtems/Read | Herhangi bir geçiş öğesini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationNetworkMappings/Read | Tüm ağ eşlemelerini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationNetworks/Read | Tüm ağları okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationPolicies/Delete | Tüm Ilkeleri silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationPolicies/operationresults/Read | Kaynak Ilkelerinde zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/kasaults/replicationPolicies/okuma | Tüm Ilkeleri okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationPolicies/Write | Herhangi bir Ilke oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationkorunabilir/Read | Tüm korumalı öğeleri okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationProtectionContainerMappings/Read | Tüm koruma kapsayıcısı eşlemelerini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationProtectionContainers/Read | Tüm koruma kapsayıcılarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/Delete | Tüm kurtarma planlarını silme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/Failovercommıt/Action | Yük devretme yürütmesi kurtarma planı |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/operationresults/Read | Kaynak kurtarma planlarında zaman uyumsuz bir işlemin sonuçlarını izleme |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/plannedFailover/ACTION | Planlı Yük devretme kurtarma planı |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/okuma | Tüm kurtarma planlarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/yeniden koruma/eylem | Kurtarma planını yeniden koru |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/testFailover/Action | Yük devretme kurtarma planını sına |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/testFailoverCleanup/Action | Sınama yük devretmesi Temizleme kurtarma planı |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/unplannedFailover/ACTION | Yük devretme kurtarma planı |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/Write | Herhangi bir kurtarma planı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationRecoveryServicesProviders/Read | Tüm kurtarma hizmetleri sağlayıcılarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationstorageclassıficationmappings/Read | Tüm depolama sınıflandırması eşlemelerini okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationstorageclassıfler/okuma | Tüm depolama sınıflandırmalarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationSupportedOperatingSystems/okuma | Her birini okuyun  |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationkullanımlar/okuma | Tüm kasa çoğaltma kullanımlarını okuyun |
> | Eylem | Microsoft. RecoveryServices/kasaults/Replicationerthegizli/operationresults/Read | Kaynak Kasası çoğaltma durumu üzerinde zaman uyumsuz bir işlemin sonuçlarını izleyin |
> | Eylem | Microsoft. RecoveryServices/Vaults/Replicationvaulthegizli/Read | Tüm kasa çoğaltma sistem durumunu okuyun |
> | Eylem | Microsoft. RecoveryServices/kasaults/Replicationvaulthegizli/Yenile/eylem | Kasa durumunu yenile |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationVaultSettings/Read | Her birini okuyun  |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationVaultSettings/Write | Oluşturma veya güncelleştirme  |
> | Eylem | Microsoft. RecoveryServices/Vaults/replicationvCenters/Read | Tüm sanal merkezleri okuyun |
> | Eylem | Microsoft. RecoveryServices/kasaults/kullanımlar/okuma | Tüm kasa kullanımlarını okuyun |
> | Eylem | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | Eylem | Microsoft. RecoveryServices/Vaults/vaultTokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | Eylem | Microsoft. RecoveryServices/Vaults/Write | Kasa oluşturma işlemi, ' kasa ' türünde bir Azure kaynağı oluşturur |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Relay/Checknameavaılabılıty/Action | Belirtilen abonelikte ad alanının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. Relay/checkNamespaceAvailability/Action | Belirtilen abonelikte ad alanının kullanılabilirliğini denetler. Bu API kullanım dışı, lütfen bunun yerine Checknameavaılabılıty kullanın. |
> | Eylem | Microsoft. Relay/Namespace/authorizationRules/Action | Güncelleştirme ad alanı yetkilendirme kuralı. Bu API kullanım dışıdır. Lütfen bunun yerine ad alanı yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. Relay/Namespace/authorizationRules/Delete | Ad alanı yetkilendirme kuralını silin. Varsayılan ad alanı yetkilendirme kuralı silinemez.  |
> | Eylem | Microsoft. Relay/Namespace/authorizationRules/ListKeys/Action | Ad alanına bağlantı dizesi al |
> | Eylem | Microsoft. Relay/Namespace/authorizationRules/Read | Ad alanı yetkilendirme kuralları açıklaması listesini alın. |
> | Eylem | Microsoft. Relay/Namespace/authorizationRules/regenerateKeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. Relay/Namespace/authorizationRules/Write | Ad alanı düzeyinde yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları, birincil ve Ikincil anahtarlar güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. Relay/Namespace/Delete | Ad alanı kaynağını Sil |
> | Eylem | Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/authorizationRules/ListKeys/Action | Olağanüstü durum kurtarma birincil ad alanı için yetkilendirme kuralları anahtarlarını alır |
> | Eylem | Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/authorizationRules/Read | Olağanüstü durum kurtarma birincil ad alanının yetkilendirme kurallarını al |
> | Eylem | Microsoft. Relay/namespaces/Diskalrecoveryconfigs/Breakeşleştirmeyi/eylemi | Olağanüstü durum kurtarmayı devre dışı bırakır ve birincil olan değişiklikleri ikincil ad alanlarına çoğaltmayı durduruyor. |
> | Eylem | Microsoft. Relay/namespaces/disyıldız recoveryconfigs/Checknameavaılabılıty/Action | Belirtilen abonelik altında ad alanı diğer adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/Delete | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını siler. Bu işlem yalnızca birincil ad alanı aracılığıyla çağrılabilir. |
> | Eylem | Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/yük devretme/eylem | COĞRAFI bir DR yük devretmesini çağırır ve ad alanı diğer adını ikincil ad alanına işaret edecek şekilde yeniden yapılandırır. |
> | Eylem | Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/Read | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını alır. |
> | Eylem | Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/Write | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/authorizationRules/Action | HybridConnection 'ı güncelleştirme işlemi. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. Yetkilendirme kuralları. Lütfen yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/authorizationRules/Delete | HybridConnection yetkilendirme kurallarını silme işlemi |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/authorizationRules/ListKeys/Action | Bağlantı dizesini HybridConnection 'a al |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/authorizationRules/Read |  HybridConnection yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/authorizationRules/regeneratekeys/Action | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/authorizationRules/Write | HybridConnection yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/Delete | HybridConnection kaynağını silme işlemi |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/Read | HybridConnection kaynak açıklamaları listesini al |
> | Eylem | Microsoft. Relay/Namespace/HybridConnections/Write | HybridConnection özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Relay/namespaces/messagingPlan/okuma | Bir ad alanı için mesajlaşma planını alır.<br>Bu API kullanım dışıdır.<br>MessagingPlan kaynağı aracılığıyla sunulan özellikler sonraki API sürümlerindeki (üst) ad alanı kaynağına taşınır.<br>Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. Relay/namespaces/messagingPlan/yazma | Bir ad alanı için mesajlaşma planını güncelleştirir.<br>Bu API kullanım dışıdır.<br>MessagingPlan kaynağı aracılığıyla sunulan özellikler sonraki API sürümlerindeki (üst) ad alanı kaynağına taşınır.<br>Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. Relay/Namespace/operationresults/Read | Ad alanı işleminin durumunu al |
> | Eylem | Microsoft. Relay/namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Ad alanı Tanılama ayarları kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. Relay/namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Ad alanı Tanılama ayarları kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. Relay/namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Ad alanı günlükleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. Relay/namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Ad alanı ölçümleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. Relay/Namespace/Read | Ad alanı kaynağı açıklaması listesini al |
> | Eylem | Microsoft. Relay/namespaces/removeAcsNamepsace/eylem | ACS ad alanını kaldır |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/authorizationRules/Action | WcfRelay 'i güncelleştirme işlemi. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. Yetkilendirme kuralları. Lütfen yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/authorizationRules/Delete | WcfRelay yetkilendirme kurallarını silme işlemi |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/authorizationRules/ListKeys/Action | Bağlantı dizesini WcfRelay 'e al |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/authorizationRules/Read |  WcfRelay yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/authorizationRules/regeneratekeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. Relay/namespaces/Wcfrehera/authorizationRules/Write | WcfRelay yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/Delete | WcfRelay kaynağını silme işlemi |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/Read | WcfRelay kaynak açıklamaları listesini al |
> | Eylem | Microsoft. Relay/namespaces/Wcfreyerleştir/Write | WcfRelay özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Relay/Namespace/Write | Bir ad alanı kaynağı oluşturun ve özelliklerini güncelleştirin. Ad alanının etiketleri ve kapasitesi, güncelleştirilebilen özelliklerdir. |
> | Eylem | Microsoft. Relay/işlemler/okuma | Işlemleri al |
> | Eylem | Microsoft. Relay/Register/ACTION | Geçiş kaynak sağlayıcısı için aboneliği kaydeder ve geçiş kaynaklarının oluşturulmasını sunar |
> | Eylem | Microsoft. Relay/Unregister/eylem | Geçiş kaynak sağlayıcısı için aboneliği kaydeder ve geçiş kaynaklarının oluşturulmasını sunar |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ResourceHealth/kullanılabilirliği Bilitydurumlar/geçerli/okundu | Belirtilen kaynak için kullanılabilirlik durumunu alır |
> | Eylem | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Eylem | Microsoft. ResourceHealth/olayları/okuma | Belirtilen abonelik için hizmet durumu olaylarını al |
> | Eylem | Microsoft. Resourcehealth/healthevent/Action | Belirtilen kaynak için sistem durumunda değişiklik gösterir |
> | Eylem | Microsoft. Resourcehealth/healthevent/Activated/Action | Belirtilen kaynak için sistem durumunda değişiklik gösterir |
> | Eylem | Microsoft. Resourcehealth/healthevent/InProgress/Action | Belirtilen kaynak için sistem durumunda değişiklik gösterir |
> | Eylem | Microsoft. Resourcehealth/healthevent/bekliyor/Action | Belirtilen kaynak için sistem durumunda değişiklik gösterir |
> | Eylem | Microsoft. Resourcehealth/healthevent/çözüldü/Action | Belirtilen kaynak için sistem durumunda değişiklik gösterir |
> | Eylem | Microsoft. Resourcehealth/healthevent/Updated/Action | Belirtilen kaynak için sistem durumunda değişiklik gösterir |
> | Eylem | Microsoft. ResourceHealth/ımpactedresources/Read | Verilen abonelik için etkilenen kaynakları al |
> | Eylem | Microsoft. ResourceHealth/Metadata/Read | Meta verileri alır |
> | Eylem | Microsoft. ResourceHealth/Işlemler/okuma | Microsoft ResourceHealth için kullanılabilir işlemleri al |
> | Eylem | Microsoft. ResourceHealth/Register/ACTION | Microsoft ResourceHealth için aboneliği kaydeder |
> | Eylem | Microsoft. ResourceHealth/Unregister/ACTION | Microsoft ResourceHealth için aboneliğin kaydını siler |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. resources/calculateTemplateHash/Action | Belirtilen şablonun karmasını hesaplayın. |
> | Eylem | Microsoft. resources/Checkpolicyuyumluluk/eylem | Kaynak ilkelerine karşı belirli bir kaynağın uyumluluk durumunu kontrol edin. |
> | Eylem | Microsoft. resources/checkResourceName/Action | Kaynak adını geçerlilik için denetleyin. |
> | Eylem | Microsoft. resources/dağıtımlar/iptal/eylem | Bir dağıtımı iptal eder. |
> | Eylem | Microsoft. resources/dağıtımlar/Sil | Bir dağıtımı siler. |
> | Eylem | Microsoft. resources/dağıtımlar/exportTemplate/Action | Dağıtım için şablonu dışarı aktarma |
> | Eylem | Microsoft. resources/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Eylem | Microsoft. resources/dağıtımlar/operationdurumlarının/Read | Dağıtım işlemi durumlarını alır veya listeler. |
> | Eylem | Microsoft. resources/dağıtımlar/okuma | Dağıtımları alır veya listeler. |
> | Eylem | Microsoft. resources/dağıtımlar/doğrulama/eylem | Bir dağıtımı doğrular. |
> | Eylem | Microsoft. resources/dağıtımlar/whatIf/Action | Şablon dağıtım değişikliklerini tahmin eder. |
> | Eylem | Microsoft. resources/dağıtımlar/yazma | Bir dağıtımı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. resources/bağlantılar/Sil | Bir kaynak bağlantısını siler. |
> | Eylem | Microsoft. resources/bağlantılar/okuma | Kaynak bağlantılarını alır veya listeler. |
> | Eylem | Microsoft. resources/bağlantılar/yazma | Kaynak bağlantısı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. resources/Market/satın alma/eylem | Market 'ten bir kaynak satın alır. |
> | Eylem | Microsoft. resources/sağlayıcılar/okuma | Sağlayıcıların listesini alın. |
> | Eylem | Microsoft. resources/kaynakları/okuma | Filtreleri temel alarak kaynak listesini alın. |
> | Eylem | Microsoft. resources/abonelikler/konumlar/okuma | Desteklenen konumların listesini alır. |
> | Eylem | Microsoft. resources/abonelikler/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | Eylem | Microsoft. resources/abonelikler/sağlayıcılar/okuma | Kaynak sağlayıcılarını alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/okuma | Aboneliklerin listesini alır. |
> | Eylem | Microsoft. resources/abonelikler/resourceGroups/Delete | Bir kaynak grubunu ve tüm kaynaklarını siler. |
> | Eylem | Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/operationdurumlarının/Read | Dağıtım işlemi durumlarını alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/okuma | Dağıtımları alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. resources/abonelikler/resourceGroups/Moveresonak/Action | Kaynakları bir kaynak grubundan diğerine taşıın. |
> | Eylem | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/ResourceGroups/kaynaklar/okuma | Kaynak grubunun kaynaklarını alır. |
> | Eylem | Microsoft. resources/abonelikler/resourceGroups/Validatemoveresonak/Action | Kaynakları bir kaynak grubundan diğerine taşımayı doğrulayın. |
> | Eylem | Microsoft. resources/abonelikler/resourceGroups/Write | Bir kaynak grubu oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. resources/abonelikler/kaynaklar/okuma | Bir aboneliğin kaynaklarını alır. |
> | Eylem | Microsoft. resources/abonelikler/tagNames/Delete | Bir abonelik etiketini siler. |
> | Eylem | Microsoft. resources/abonelikler/tagNames/Read | Abonelik etiketlerini alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/tagNames/tagValues/Delete | Abonelik etiketi değerini siler. |
> | Eylem | Microsoft. resources/abonelikler/tagNames/tagValues/Read | Abonelik etiketi değerlerini alır veya listeler. |
> | Eylem | Microsoft. resources/abonelikler/tagNames/tagValues/Write | Abonelik etiketi değeri ekler. |
> | Eylem | Microsoft. resources/abonelikler/tagNames/Write | Abonelik etiketi ekler. |
> | Eylem | Microsoft. resources/etiketleri/silme | Bir kaynaktaki tüm etiketleri kaldırır. |
> | Eylem | Microsoft. resources/Tags/okuma | Bir kaynaktaki tüm etiketleri alır. |
> | Eylem | Microsoft. resources/Tags/Write | Varolan etiketleri yeni bir etiket kümesiyle değiştirerek veya birleştirerek ya da varolan etiketleri kaldırarak bir kaynaktaki etiketleri güncelleştirir. |
> | Eylem | Microsoft. resources/kiracılar/Read | Kiracılar listesini alır. |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Scheduler/jobcollections/Delete | İş koleksiyonunu siler. |
> | Eylem | Microsoft. Scheduler/jobcollections/Disable/Action | İş koleksiyonunu devre dışı bırakır. |
> | Eylem | Microsoft. Scheduler/jobcollections/Enable/Action | İş toplamayı etkinleştirilir. |
> | Eylem | Microsoft. Scheduler/jobcollections/Jobs/Delete | İşi siler. |
> | Eylem | Microsoft. Scheduler/jobcollections/Jobs/generateLogicAppDefinition/Action | Bir Zamanlayıcı Işini temel alarak mantıksal uygulama tanımı oluşturur. |
> | Eylem | Microsoft. Scheduler/jobcollections/Jobs/jobgeçmiş/Read | İş geçmişini alır. |
> | Eylem | Microsoft. Scheduler/jobcollections/Jobs/Read | İşi alır. |
> | Eylem | Microsoft. Scheduler/jobcollections/Jobs/Run/Action | İşi çalıştırır. |
> | Eylem | Microsoft. Scheduler/jobcollections/Jobs/Write | İş oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Scheduler/jobcollections/Read | Iş toplamayı al |
> | Eylem | Microsoft. Scheduler/jobcollections/Write | İş koleksiyonu oluşturur veya güncelleştirir. |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Search/Checknameavaılabılıty/Action | Hizmet adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. Search/işlemler/okuma | Microsoft. Search sağlayıcısı 'nın tüm kullanılabilir işlemlerini listeler. |
> | Eylem | Microsoft. Search/Register/ACTION | Arama kaynak sağlayıcısı için aboneliği kaydeder ve arama hizmetleri oluşturmayı sunar. |
> | Eylem | Microsoft. Search/searchServices/createQueryKey/Action | Sorgu anahtarını oluşturur. |
> | Eylem | Microsoft. Search/searchServices/Delete | Arama hizmetini siler. |
> | Eylem | Microsoft. Search/searchServices/deleteQueryKey/Delete | Sorgu anahtarını siler. |
> | Eylem | Microsoft. Search/searchServices/listAdminKeys/Action | Yönetici anahtarlarını okur. |
> | Eylem | Microsoft. Search/searchServices/listQueryKeys/Action | Verilen Azure Bilişsel Arama hizmeti için sorgu API 'SI anahtarlarının listesini döndürür. |
> | Eylem | Microsoft. Search/searchServices/listQueryKeys/Read | Verilen Azure Bilişsel Arama hizmeti için sorgu API 'SI anahtarlarının listesini döndürür. |
> | Eylem | Microsoft. Search/searchServices/Read | Arama hizmetini okur. |
> | Eylem | Microsoft. Search/searchServices/regenerateAdminKey/Action | Yönetici anahtarını yeniden oluşturur. |
> | Eylem | Microsoft. Search/searchServices/start/Action | Arama hizmetini başlatır. |
> | Eylem | Microsoft. Search/searchServices/durdur/eylem | Arama hizmetini sonlandırır. |
> | Eylem | Microsoft. Search/searchServices/Write | Arama hizmetini oluşturur veya güncelleştirir. |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Security/adaptiveNetworkHardenings/enforce/Action | Verilen ağ güvenlik grupları üzerinde eşleşen güvenlik kuralları oluşturarak verilen trafik sağlamlaştırma kurallarını zorlar |
> | Eylem | Microsoft. Security/adaptiveNetworkHardenings/Read | Azure korumalı bir kaynağın Uyarlamalı ağ sağlamlaştırma önerilerini alır |
> | Eylem | Microsoft. Security/advancedThreatProtectionSettings/Read | Kaynak için Gelişmiş tehdit koruması ayarlarını alır |
> | Eylem | Microsoft. Security/advancedThreatProtectionSettings/Write | Kaynak için Gelişmiş tehdit koruması ayarlarını güncelleştirir |
> | Eylem | Microsoft. Security/alerts/Read | Tüm kullanılabilir güvenlik uyarılarını alır |
> | Eylem | Microsoft. Security/Applicationwhitedökümler/okuma | Uygulama beyaz listelerini alır |
> | Eylem | Microsoft. Security/Applicationwhitedökümler/Write | Yeni bir uygulama beyaz listeye oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. Security/assessmentMetadata/Read | Aboneliğinizde kullanılabilir güvenlik değerlendirmesi meta verilerini alın |
> | Eylem | Microsoft. Security/assessmentMetadata/Write | Güvenlik değerlendirmesi meta verileri oluşturma veya güncelleştirme |
> | Eylem | Microsoft. Security/değerlendirmeleri/okuma | Aboneliğinizde güvenlik değerlendirmeleri alın |
> | Eylem | Microsoft. Security/değerlendirmeleri/yazma | Aboneliğinizde güvenlik değerlendirmeleri oluşturun veya güncelleştirin |
> | Eylem | Microsoft. Security/Karmaşıkanceresults/okuma | Kaynak için uyumluluk sonuçlarını alır |
> | Eylem | Microsoft. Security/ınformationprotectionpolicies/Read | Kaynak için bilgi koruma ilkelerini alır |
> | Eylem | Microsoft. Security/ınformationprotectionpolicies/Write | Kaynak için bilgi koruma ilkelerini güncelleştirir |
> | Eylem | Microsoft. güvenlik/konumlar/uyarılar/etkinleştir/eylem | Güvenlik uyarısını etkinleştirme |
> | Eylem | Microsoft. güvenlik/konumlar/uyarılar/kapat/eylem | Güvenlik uyarısını kapat |
> | Eylem | Microsoft. güvenlik/konumlar/uyarılar/okuma | Tüm kullanılabilir güvenlik uyarılarını alır |
> | Eylem | Microsoft. Security/Locations/Jağaccesspolicies/Delete | Tam zamanında ağ erişim ilkesini siler |
> | Eylem | Microsoft. Security/Locations/Jağaccesspolicies/Initiate/Action | Tam zamanında ağ erişim ilkesi isteği başlatır |
> | Eylem | Microsoft. Security/Locations/Jağaccesspolicies/Read | Tam zamanında ağ erişim ilkelerini alır |
> | Eylem | Microsoft. Security/Locations/Jağaccesspolicies/Write | Yeni bir tam zamanında ağ erişim ilkesi oluşturur veya var olan bir ağı güncelleştirir |
> | Eylem | Microsoft. Security/Locations/Read | Güvenlik veri konumunu alır |
> | Eylem | Microsoft. güvenlik/konumlar/görevler/etkinleştir/eylem | Güvenlik önerisi etkinleştirme |
> | Eylem | Microsoft. güvenlik/konumlar/görevler/kapat/eylem | Güvenlik önerisini kapat |
> | Eylem | Microsoft. güvenlik/konumlar/görevler/okuma | Tüm kullanılabilir güvenlik önerilerini alır |
> | Eylem | Microsoft. güvenlik/konumlar/görevler/çözümle/eylem | Güvenlik önerisini çözümleyin |
> | Eylem | Microsoft. güvenlik/konumlar/görevler/Başlat/eylem | Güvenlik önerisi başlatma |
> | Eylem | Microsoft. Security/Policies/Read | Güvenlik ilkesini alır |
> | Eylem | Microsoft. Security/Policies/Write | Güvenlik ilkesini güncelleştirir |
> | Eylem | Microsoft. Security/pricler/Delete | Kapsamın fiyatlandırma ayarlarını siler |
> | Eylem | Microsoft. Security/pricler/okuma | Kapsamın fiyatlandırma ayarlarını alır |
> | Eylem | Microsoft. Security/pricler/Write | Kapsamın fiyatlandırma ayarlarını güncelleştirir |
> | Eylem | Microsoft. Security/Register/ACTION | Azure Güvenlik Merkezi için aboneliği kaydeder |
> | Eylem | Microsoft. Security/securityContacts/Delete | Güvenlik ilgili kişisini siler |
> | Eylem | Microsoft. Security/securityContacts/Read | Güvenlik ilgili kişisini alır |
> | Eylem | Microsoft. Security/securityContacts/Write | Güvenlik ilgili kişisini güncelleştirir |
> | Eylem | Microsoft. Security/securitySolutions/Delete | Bir güvenlik çözümünü siler |
> | Eylem | Microsoft. Security/securitySolutions/Read | Güvenlik çözümlerini alır |
> | Eylem | Microsoft. Security/securitySolutions/Write | Yeni bir güvenlik çözümü oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. Security/securitySolutionsReferenceData/Read | Güvenlik çözümleri başvuru verilerini alır |
> | Eylem | Microsoft. Security/Securitydurumlar/okuma | Azure kaynakları için güvenlik durumu durumlarını alır |
> | Eylem | Microsoft. Security/securityStatusesSummaries/Read | Kapsam için güvenlik durumları özetlerini alır |
> | Eylem | Microsoft. Security/Settings/Read | Kapsamın ayarlarını alır |
> | Eylem | Microsoft. Security/Settings/Write | Kapsamın ayarlarını güncelleştirir |
> | Eylem | Microsoft. Security/Tasks/Read | Tüm kullanılabilir güvenlik önerilerini alır |
> | Eylem | Microsoft. Security/Unregister/ACTION | Azure Güvenlik Merkezi ' nden aboneliğin kaydını siler |
> | Eylem | Microsoft. Security/Webapplicationgüvenlik duvarları/silme | Web uygulaması güvenlik duvarını siler |
> | Eylem | Microsoft. Security/Webapplicationgüvenlik duvarları/okuma | Web uygulaması güvenlik duvarlarını alır |
> | Eylem | Microsoft. Security/Webapplicationgüvenlik duvarları/yazma | Yeni bir Web uygulaması güvenlik duvarı oluşturur veya mevcut olanı güncelleştirir |
> | Eylem | Microsoft. Security/Workspace ayarları/Connect/Action | Çalışma alanı ayarlarının yeniden bağlanma ayarlarını değiştir |
> | Eylem | Microsoft. Security/Workspace ayarları/silme | Çalışma alanı ayarlarını siler |
> | Eylem | Microsoft. Security/Workspace ayarları/okuma | Çalışma alanı ayarlarını alır |
> | Eylem | Microsoft. Security/Workspace ayarları/yazma | Çalışma alanı ayarlarını güncelleştirir |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. SecurityGraph/diagnosticsettings/Delete | Bir tanılama ayarını silme |
> | Eylem | Microsoft. SecurityGraph/diagnosticsettings/Read | Tanılama ayarını okuma |
> | Eylem | Microsoft. SecurityGraph/diagnosticsettings/Write | Tanılama ayarı yazma |
> | Eylem | Microsoft. SecurityGraph/diagnosticsettingscategories/Read | Tanılama ayarı kategorilerini okuma |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ServiceBus/Checknameavaılabılıty/Action | Belirtilen abonelikte ad alanının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Belirtilen abonelikte ad alanının kullanılabilirliğini denetler. Bu API kullanım dışı, lütfen bunun yerine Checknameavaılabılıty kullanın. |
> | Eylem | Microsoft. ServiceBus/Locations/Deletevirtualnetworkoralt ağları/eylem | Belirtilen VNet için ServiceBus kaynak sağlayıcısında VNet kurallarını siler |
> | Eylem | Microsoft. ServiceBus/namespaces/authorizationRules/Action | Güncelleştirme ad alanı yetkilendirme kuralı. Bu API kullanım dışıdır. Lütfen bunun yerine ad alanı yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. ServiceBus/Namespace/authorizationRules/Delete | Ad alanı yetkilendirme kuralını silin. Varsayılan ad alanı yetkilendirme kuralı silinemez.  |
> | Eylem | Microsoft. ServiceBus/Namespace/authorizationRules/ListKeys/Action | Ad alanına bağlantı dizesi al |
> | Eylem | Microsoft. ServiceBus/Namespace/authorizationRules/Read | Ad alanı yetkilendirme kuralları açıklaması listesini alın. |
> | Eylem | Microsoft. ServiceBus/Namespace/authorizationRules/regenerateKeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. ServiceBus/Namespace/authorizationRules/Write | Ad alanı düzeyinde yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları, birincil ve Ikincil anahtarlar güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. ServiceBus/Namespace/Delete | Ad alanı kaynağını Sil |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/authorizationRules/ListKeys/Action | Olağanüstü durum kurtarma birincil ad alanı için yetkilendirme kuralları anahtarlarını alır |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/authorizationRules/Read | Olağanüstü durum kurtarma birincil ad alanının yetkilendirme kurallarını al |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/Breakeşleştirmeyi/eylemini | Olağanüstü durum kurtarmayı devre dışı bırakır ve birincil olan değişiklikleri ikincil ad alanlarına çoğaltmayı durduruyor. |
> | Eylem | Microsoft. ServiceBus/namespaces/diskalrecoveryconfigs/Checknameavaılabılıty/Action | Belirtilen abonelik altında ad alanı diğer adının kullanılabilirliğini denetler. |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/Delete | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını siler. Bu işlem yalnızca birincil ad alanı aracılığıyla çağrılabilir. |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/yük devretme/eylem | COĞRAFI bir DR yük devretmesini çağırır ve ad alanı diğer adını ikincil ad alanına işaret edecek şekilde yeniden yapılandırır. |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/Read | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını alır. |
> | Eylem | Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/Write | Ad alanıyla ilişkili olağanüstü durum kurtarma yapılandırmasını oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ServiceBus/namespaces/Eventgridfilter/Delete | Ad alanıyla ilişkili Event Grid filtresini siler. |
> | Eylem | Microsoft. ServiceBus/namespaces/Eventgridfilter/Read | Ad alanıyla ilişkili Event Grid filtresini alır. |
> | Eylem | Microsoft. ServiceBus/namespaces/Eventgridfilter/Write | Ad alanıyla ilişkili Event Grid filtresini oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. ServiceBus/Namespace/eventhubs/Read | EventHub kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/ıpfilterrules/Delete | IP filtresi kaynağını Sil |
> | Eylem | Microsoft. ServiceBus/namespaces/ıpfilterrules/Read | IP filtre kaynağı al |
> | Eylem | Microsoft. ServiceBus/namespaces/ıpfilterrules/Write | IP filtre kaynağı oluştur |
> | DataAction | Microsoft. ServiceBus/namespaces/iletiler/Receive/Action | İleti alma |
> | DataAction | Microsoft. ServiceBus/ad alanları/iletiler/gönderme/eylem | İleti gönderme |
> | Eylem | Microsoft. ServiceBus/namespaces/messagingPlan/okuma | Bir ad alanı için mesajlaşma planını alır.<br>Bu API kullanım dışıdır.<br>MessagingPlan kaynağı aracılığıyla sunulan özellikler sonraki API sürümlerindeki (üst) ad alanı kaynağına taşınır.<br>Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. ServiceBus/namespaces/messagingPlan/yazma | Bir ad alanı için mesajlaşma planını güncelleştirir.<br>Bu API kullanım dışıdır.<br>MessagingPlan kaynağı aracılığıyla sunulan özellikler sonraki API sürümlerindeki (üst) ad alanı kaynağına taşınır.<br>Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. |
> | Eylem | Microsoft. ServiceBus/namespaces/Migrate/Action | Ad alanı geçirme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/migrationConfigurations/Delete | Geçiş yapılandırmasını siler. |
> | Eylem | Microsoft. ServiceBus/ad alanları/migrationConfigurations/okuma | Geçişin durumunu ve bekleyen çoğaltma işlemlerini gösteren geçiş yapılandırmasını alır |
> | Eylem | Microsoft. ServiceBus/namespaces/migrationConfigurations/döndürülüyor/Action | Standart, Premium ad alanı geçişine geri döndürür |
> | Eylem | Microsoft. ServiceBus/namespaces/migrationConfigurations/Upgrade/Action | Standart ad alanı ile ilişkili DNS 'yi, geçişi tamamlayan Premium ad alanına ve standart olan kaynakları Standart ve Premium ad alanına eşitlemeyi durduran şekilde atar |
> | Eylem | Microsoft. ServiceBus/namespaces/migrationConfigurations/Write | Geçiş yapılandırması oluşturur veya güncelleştirir. Bu işlem, kaynakları standart olan Premium ad alanına eşitlemeye başlar |
> | Eylem | Microsoft. ServiceBus/namespaces/networkruleset/Delete | VNET kural kaynağını Sil |
> | Eylem | Microsoft. ServiceBus/namespaces/networkruleset/Read | NetworkRuleSet kaynağını alır |
> | Eylem | Microsoft. ServiceBus/namespaces/networkruleset/Write | VNET kural kaynağı oluşturma |
> | Eylem | Microsoft. ServiceBus/namespaces/networkrulesets/Delete | VNET kural kaynağını Sil |
> | Eylem | Microsoft. ServiceBus/namespaces/networkrulesets/Read | NetworkRuleSet kaynağını alır |
> | Eylem | Microsoft. ServiceBus/namespaces/networkrulesets/Write | VNET kural kaynağı oluşturma |
> | Eylem | Microsoft. ServiceBus/Namespace/operationresults/Read | Ad alanı işleminin durumunu al |
> | Eylem | Microsoft. ServiceBus/namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Ad alanı Tanılama ayarları kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Ad alanı Tanılama ayarları kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Ad alanı günlükleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/Providers/Microsoft. Insights/metricDefinitions/Read | Ad alanı ölçümleri kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules/Action | Kuyruğu güncelleştirme işlemi. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. Yetkilendirme kuralları. Lütfen yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules/Delete | Kuyruk yetkilendirme kurallarını silme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules/ListKeys/Action | Sıraya yönelik bağlantı dizesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules/Read |  Kuyruk yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules/regenerateKeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules/Write | Sıra yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/Sil | Kuyruk kaynağını silme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/okuma | Kuyruk kaynağı açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/kuyruklar/Write | Sıra özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. ServiceBus/Namespace/Read | Ad alanı kaynağı açıklaması listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/removeAcsNamepsace/eylem | ACS ad alanını kaldır |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/authorizationRules/Action | Konuyu güncelleştirmek için işlem. Bu işlem API sürüm 2017-04-01 ' de desteklenmiyor. Yetkilendirme kuralları. Lütfen yetkilendirme kuralını güncelleştirmek için bir PUT çağrısı kullanın. |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/authorizationRules/Delete | Konu yetkilendirme kurallarını silme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/authorizationRules/ListKeys/Action | Bağlantı dizesini konuya al |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/authorizationRules/Read |  Konu yetkilendirme kurallarının listesini alın |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/authorizationRules/regenerateKeys/ACTION | Kaynağa birincil veya Ikincil anahtarı yeniden oluştur |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/authorizationRules/Write | Konu yetkilendirme kuralları oluşturun ve özelliklerini güncelleştirin. Yetkilendirme kuralları erişim hakları güncelleştirilebilen olabilir. |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/Sil | Konu kaynağını silme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/okuma | Konu kaynağı açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/abonelikler/Sil | TopicSubscription kaynağını silme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/abonelikler/okuma | TopicSubscription kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/abonelikler/kurallar/Sil | Kural kaynağını silme işlemi |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/abonelikler/kurallar/okuma | Kural kaynağı açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/abonelikler/kurallar/yazma | Kural özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/abonelikler/yazma | TopicSubscription özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. ServiceBus/namespaces/konular/yaz | Konu özelliklerini oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. ServiceBus/Namespace/virtualNetworkRules/Delete | VNET kural kaynağını Sil |
> | Eylem | Microsoft. ServiceBus/Namespace/virtualNetworkRules/Read | VNET kural kaynağını alır |
> | Eylem | Microsoft. ServiceBus/Namespace/virtualNetworkRules/Write | VNET kural kaynağı oluşturma |
> | Eylem | Microsoft. ServiceBus/Namespace/Write | Bir ad alanı kaynağı oluşturun ve özelliklerini güncelleştirin. Ad alanının etiketleri ve kapasitesi, güncelleştirilebilen özelliklerdir. |
> | Eylem | Microsoft. ServiceBus/işlemler/okuma | Işlemleri al |
> | Eylem | Microsoft. ServiceBus/Register/ACTION | ServiceBus kaynak sağlayıcısı için aboneliği kaydeder ve ServiceBus kaynaklarının oluşturulmasını sunar |
> | Eylem | Microsoft. ServiceBus/SKU/okuma | SKU kaynak açıklamaları listesini al |
> | Eylem | Microsoft. ServiceBus/SKU/bölge/okuma | Skuregion kaynak açıklamalarının listesini al |
> | Eylem | Microsoft. ServiceBus/Unregister/ACTION | ServiceBus kaynak sağlayıcısı için aboneliği kaydeder ve ServiceBus kaynaklarının oluşturulmasını sunar |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/Sil | Herhangi bir uygulamayı silme |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/okuma | Herhangi bir uygulamayı okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/hizmetler/Sil | Tüm Hizmetleri Sil |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/hizmetler/bölümler/okuma | Herhangi bir bölümü okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/hizmetler/bölümler/çoğaltmalar/okuma | Herhangi bir çoğaltmayı okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/hizmetler/okuma | Tüm hizmetleri okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/hizmetler/durumlar/okuma | Tüm hizmet durumlarını okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/hizmetler/yazma | Herhangi bir hizmeti oluşturun veya güncelleştirin |
> | Eylem | Microsoft. ServiceFabric/kümeler/uygulamalar/yazma | Herhangi bir uygulamayı oluşturun veya güncelleştirin |
> | Eylem | Microsoft. ServiceFabric/kümeler/applicationTypes/Delete | Herhangi bir uygulama türünü sil |
> | Eylem | Microsoft. ServiceFabric/kümeler/applicationTypes/Read | Herhangi bir uygulama türünü okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/applicationTypes/sürümler/Delete | Herhangi bir uygulama türü sürümünü Sil |
> | Eylem | Microsoft. ServiceFabric/kümeler/applicationTypes/sürümler/Read | Herhangi bir uygulama türü sürümünü okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/applicationTypes/sürümler/Write | Herhangi bir uygulama türü sürümü oluşturun veya güncelleştirin |
> | Eylem | Microsoft. ServiceFabric/kümeler/applicationTypes/Write | Herhangi bir uygulama türü oluşturun veya güncelleştirin |
> | Eylem | Microsoft. ServiceFabric/kümeler/Sil | Herhangi bir kümeyi silme |
> | Eylem | Microsoft. ServiceFabric/kümeler/düğümler/okuma | Herhangi bir düğümü okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/okuma | Herhangi bir kümeyi okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/durumlar/okuma | Tüm küme durumlarını okuyun |
> | Eylem | Microsoft. ServiceFabric/kümeler/yazma | Herhangi bir kümeyi oluşturma veya güncelleştirme |
> | Eylem | Microsoft. ServiceFabric/konumlar/Kümesürümleri/okuma | Tüm küme sürümlerini okuyun |
> | Eylem | Microsoft. ServiceFabric/konumlar/ortamlar/Kümesürümleri/okuma | Belirli bir ortam için herhangi bir küme sürümünü okuyun |
> | Eylem | Microsoft. ServiceFabric/konumlar/operationresults/Read | Işlem sonuçlarını okuma |
> | Eylem | Microsoft. ServiceFabric/konumlar/işlemler/okuma | Konuma göre tüm Işlemleri oku |
> | Eylem | Microsoft. ServiceFabric/işlemler/okuma | Tüm kullanılabilir Işlemleri okuyun |
> | Eylem | Microsoft. ServiceFabric/Register/ACTION | Tüm eylemleri kaydetme |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. SignalRService/Locations/checknameavaılabılıty/Action | Bir adın yeni bir SignalR hizmeti ile kullanım için kullanılabilir olup olmadığını denetler |
> | Eylem | Microsoft. SignalRService/Locations/operationresults/SignalR/Read | Zaman uyumsuz bir işlemin durumunu sorgulama |
> | Eylem | Microsoft. SignalRService/Locations/Operationdurumlarının/operationId/Read | Zaman uyumsuz bir işlemin durumunu sorgulama |
> | Eylem | Microsoft. SignalRService/konumlar/kullanımlar/okuma | Azure SignalR hizmeti için kota kullanımlarını edinme |
> | Eylem | Microsoft. SignalRService/operationresults/Read | Zaman uyumsuz bir işlemin durumunu sorgulama |
> | Eylem | Microsoft. SignalRService/işlemler/okuma | Azure SignalR hizmeti için işlemleri listeleyin. |
> | Eylem | Microsoft. SignalRService/OperationStatus/Read | Zaman uyumsuz bir işlemin durumunu sorgulama |
> | Eylem | Microsoft. SignalRService/Register/Action | ' Microsoft. SignalRService ' kaynak sağlayıcısını bir abonelikle kaydeder |
> | Eylem | Microsoft. SignalRService/SignalR/Delete | SignalR hizmeti 'nin tamamını silme |
> | Eylem | Microsoft. SignalRService/SignalR/eventGridFilters/Delete | Bir SignalR 'den Event Grid filtresini silin. |
> | Eylem | Microsoft. SignalRService/SignalR/eventGridFilters/Read | Belirtilen bir olay Kılavuzu filtresinin özelliklerini alın veya belirtilen SignalR için tüm olay Kılavuzu filtrelerini listeleyin. |
> | Eylem | Microsoft. SignalRService/SignalR/eventGridFilters/Write | Bir SignalR için belirtilen parametrelerle bir olay Kılavuzu filtresi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. SignalRService/SignalR/ListKeys/Action | Yönetim portalında veya API aracılığıyla SignalR erişim anahtarlarının değerini görüntüleme |
> | Eylem | Microsoft. SignalRService/SignalR/Read | SignalR 'nin ayarlarını ve yapılandırmasını yönetim portalında veya API aracılığıyla görüntüleme |
> | Eylem | Microsoft. SignalRService/SignalR/RegenerateKey/Action | Yönetim portalında veya API aracılığıyla SignalR erişim anahtarlarının değerini değiştirme |
> | Eylem | Microsoft. SignalRService/SignalR/restart/Action | Yönetim portalında veya API aracılığıyla bir Azure SignalR hizmetini yeniden başlatmak için. Belirli kapalı kalma süresi olacaktır. |
> | Eylem | Microsoft. SignalRService/SignalR/Write | SignalR 'nin ayarlarını ve yapılandırmasını yönetim portalında veya API aracılığıyla değiştirme |
> | Eylem | Microsoft. SignalRService/Unregister/eylem | ' Microsoft. SignalRService ' kaynak sağlayıcısının aboneliğini bir abonelikle siler |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Solutions/applicationDefinitions/Applicationyapay/Read | Uygulama tanımının uygulama yapılarını listeler. |
> | Eylem | Microsoft. Solutions/applicationDefinitions/Delete | Bir uygulama tanımını kaldırır. |
> | Eylem | Microsoft. Solutions/applicationDefinitions/Read | Uygulama tanımlarının bir listesini alır. |
> | Eylem | Microsoft. Solutions/applicationDefinitions/Write | Uygulama tanımı ekleme veya değiştirme. |
> | Eylem | Microsoft. Solutions/Applications/applicationArtifacts/Read | Uygulama yapılarını listeler. |
> | Eylem | Microsoft. Solutions/Applications/Delete | Bir uygulamayı kaldırır. |
> | Eylem | Microsoft. Solutions/Applications/Read | Uygulamaların bir listesini alır. |
> | Eylem | Microsoft. Solutions/Applications/refreshPermissions/Action | Uygulama izinlerini yeniler. |
> | Eylem | Microsoft. Solutions/Applications/updateAccess/Action | Uygulama erişimini güncelleştirir. |
> | Eylem | Microsoft. Solutions/Applications/Write | Uygulama oluşturur. |
> | Eylem | Microsoft. Solutions/Jrequests/Delete | JRequest 'i kaldırma |
> | Eylem | Microsoft. Solutions/Jrequests/Read | Jisteklerinin bir listesini alır |
> | Eylem | Microsoft. Solutions/Jrequests/Write | Jbir Istek oluşturur |
> | Eylem | Microsoft. Solutions/Locations/Operationdurumlarının/Read | Kaynak için işlem durumunu okur. |
> | Eylem | Microsoft. Solutions/Register/ACTION | Çözümlere kaydolun. |
> | Eylem | Microsoft. Solutions/Unregister/Action | Çözümlerdeki kaydını siler. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. SQL/Checknameavaılabılıty/Action | Belirli bir abonelik için verilen sunucu adının dünya genelinde sağlanması için kullanılabilir olup olmadığını doğrulayın. |
> | Eylem | Microsoft. SQL/ınstancehavuzlar/Delete | Bir örnek havuzunu siler |
> | Eylem | Microsoft. SQL/ınstancepools/okuma | Bir örnek havuzu alır |
> | Eylem | Microsoft. SQL/ınstancepools/kullanımlar/okuma | Örnek havuzunun kullanım bilgilerini alır |
> | Eylem | Microsoft. SQL/ınstancehavuzlar/Write | Örnek havuzu oluşturur veya güncelleştirir |
> | Eylem | Microsoft. SQL/Locations/auditingSettingsAzureAsyncOperation/Read | Genişletilmiş sunucu blobu denetim ilkesi ayarlama işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Locations/auditingSettingsOperationResults/Read | Sunucu blobu denetim ilkesi ayarlama işleminin sonucunu al |
> | Eylem | Microsoft. SQL/konumlar/yetenekler/okuma | Belirli bir konumdaki bu aboneliğin yeteneklerini alır |
> | Eylem | Microsoft. SQL/konumlar/databaseAzureAsyncOperation/Read | Bir veritabanı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/konumlar/databaseOperationResults/Read | Bir veritabanı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/konumlar/deletedServerAsyncOperation/Read | Silinen sunucuda devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/Locations/Silinserveroperationresults/Read | Silinen sunucuda devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/deletedServers/okuma | Silinen sunucuların listesini döndürün veya belirtilen silinen sunucu için özellikleri alır. |
> | Eylem | Microsoft. SQL/konumlar/silinmeler/kurtarma/eylem | Silinen bir sunucuyu kurtarma |
> | Eylem | Microsoft. SQL/konumlar/elasticPoolAzureAsyncOperation/Read | Elastik havuz zaman uyumsuz işlemi için Azure zaman uyumsuz işlemini alır |
> | Eylem | Microsoft. SQL/Locations/Elayapışpooloperationresults/Read | Elastik havuz işleminin sonucunu alır. |
> | Eylem | Microsoft. SQL/Locations/Encryptionkorunabilir/Read | Saydam veri şifrelemesi şifreleme koruyucusuyla devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/Locations/Encryptionkorunabilir sonuçları/okuma | Saydam veri şifrelemesi şifreleme koruyucusuyla devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/extendedAuditingSettingsAzureAsyncOperation/Read | Genişletilmiş sunucu blobu denetim ilkesi ayarlama işleminin sonucunu al |
> | Eylem | Microsoft. SQL/konumlar/extendedAuditingSettingsOperationResults/Read | Genişletilmiş sunucu blobu denetim ilkesi ayarlama işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/Read | Bir güvenlik duvarı kuralı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/Locations/firewallRulesOperationResults/Read | Bir güvenlik duvarı kuralı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/Locations/ınstancefailovergroups/Delete | Var olan bir örnek yük devretme grubunu siler. |
> | Eylem | Microsoft. SQL/Locations/ınstancefailovergroups/yük devretme/eylem | Mevcut bir örnek yük devretme grubunda planlı yük devretmeyi yürütür. |
> | Eylem | Microsoft. SQL/Locations/ınstancefailovergroups/forceFailoverAllowDataLoss/eylem | Var olan bir örnek yük devretme grubunda zorla yük devretmeyi yürütür. |
> | Eylem | Microsoft. SQL/Locations/ınstancefailovergroups/Read | Örnek yük devretme grupları listesini döndürür veya belirtilen örnek yük devretme grubu için özellikleri alır. |
> | Eylem | Microsoft. SQL/Locations/ınstancefailovergroups/Write | Belirtilen parametrelerle bir örnek yük devretme grubu oluşturur veya belirtilen örnek yük devretme grubu için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/konumlar/instancePoolAzureAsyncOperation/Read | Örnek havuzu işleminin durumunu alır |
> | Eylem | Microsoft. SQL/Locations/ınstancepooloperationresults/Read | Örnek havuzu işleminin sonucunu alır |
> | Eylem | Microsoft. SQL/Locations/ınterfaceendpointprofileazureasyncoperation/Read | Belirli bir arabirim uç noktası için Azure zaman uyumsuz işleminin ayrıntılarını döndürür |
> | Eylem | Microsoft. SQL/Locations/ınterfaceendpointprofileoperationresults/Read | Belirtilen arabirim uç noktası profili işleminin ayrıntılarını döndürür |
> | Eylem | Microsoft. SQL/konumlar/jobAgentAzureAsyncOperation/Read | Bir iş Aracısı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/konumlar/jobAgentOperationResults/Read | Bir iş Aracısı işleminin sonucunu alır. |
> | Eylem | Microsoft. SQL/konumlar/longTermRetentionBackups/Read | Bir konumdaki her sunucu için, her veritabanı için uzun süreli bekletme yedeklemelerini listeler |
> | Eylem | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read | Bir sunucudaki her veritabanı için uzun süreli bekletme yedeklemelerini listeler |
> | Eylem | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete | Uzun süreli bekletme yedeklemesini siler |
> | Eylem | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read | Bir veritabanı için uzun süreli bekletme yedeklemelerini listeler |
> | Eylem | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/tamamlana/eylem | Yönetilen veritabanı geri yükleme işlemini tamamlar |
> | Eylem | Microsoft. SQL/Locations/Managedınstanceencryptionkorunabilir Tokızureasyncoperation/Read | Saydam veri şifrelemesi yönetilen örnek şifreleme koruyucusu üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/Locations/Managedınstanceencryptionkorunabilir/Read | Saydam veri şifrelemesi yönetilen örnek şifreleme koruyucusu üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/managedInstanceKeyAzureAsyncOperation/Read | Saydam veri şifrelemesi yönetilen örnek anahtarları üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/managedInstanceKeyOperationResults/Read | Saydam veri şifrelemesi yönetilen örnek anahtarları üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/managedTransparentDataEncryptionAzureAsyncOperation/Read | Yönetilen veritabanı saydam veri şifrelemesi üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/managedTransparentDataEncryptionOperationResults/Read | Yönetilen veritabanı saydam veri şifrelemesi üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/privateEndpointConnectionAzureAsyncOperation/Read | Özel bir uç nokta bağlantısı işleminin sonucunu alır |
> | Eylem | Microsoft. SQL/konumlar/privateEndpointConnectionOperationResults/Read | Özel bir uç nokta bağlantısı işleminin sonucunu alır |
> | Eylem | Microsoft. SQL/konumlar/privateEndpointConnectionProxyAzureAsyncOperation/Read | Özel bir uç noktası bağlantı proxy 'si işleminin sonucunu alır |
> | Eylem | Microsoft. SQL/konumlar/privateEndpointConnectionProxyOperationResults/Read | Özel bir uç noktası bağlantı proxy 'si işleminin sonucunu alır |
> | Eylem | Microsoft. SQL/konumlar/okuma | Belirli bir abonelik için kullanılabilir konumları alır |
> | Eylem | Microsoft. SQL/konumlar/serverKeyAzureAsyncOperation/Read | Saydam veri şifreleme sunucusu anahtarlarında devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/serverKeyOperationResults/Read | Saydam veri şifreleme sunucusu anahtarlarında devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/konumlar/syncAgentOperationResults/Read | Eşitleme Aracısı kaynak işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Locations/Syncdatabaseıds/Read | Belirli bir bölge ve abonelik için eşitleme veritabanı kimliklerini alma |
> | Eylem | Microsoft. SQL/Locations/syncGroupOperationResults/Read | Eşitleme grubu kaynağı işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Locations/syncMemberOperationResults/Read | Eşitleme üyesi kaynağı işleminin sonucunu al |
> | Eylem | Microsoft. SQL/konumlar/kullanımlar/okuma | Bir konumdaki bu abonelik için kullanım ölçümlerinin bir koleksiyonunu alır |
> | Eylem | Microsoft. SQL/Locations/virtualNetworkRulesAzureAsyncOperation/Read | Belirtilen sanal ağ kurallarının ayrıntılarını döndürür Azure zaman uyumsuz işlemi  |
> | Eylem | Microsoft. SQL/konumlar/virtualNetworkRulesOperationResults/Read | Belirtilen sanal ağ kuralları işleminin ayrıntılarını döndürür  |
> | Eylem | Microsoft. SQL/ManagedInstances/Administrators/Delete | Yönetilen örneğin mevcut bir yöneticisini siler. |
> | Eylem | Microsoft. SQL/ManagedInstances/Administrators/Read | Yönetilen örnek yöneticilerinin bir listesini alır. |
> | Eylem | Microsoft. SQL/ManagedInstances/Administrators/Write | Yönetilen örnek yöneticisini belirtilen parametrelerle oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/backupShortTermRetentionPolicies/Read | Yönetilen bir veritabanı için kısa süreli bekletme ilkesi alır |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/backupShortTermRetentionPolicies/Write | Yönetilen bir veritabanı için kısa süreli bekletme ilkesini güncelleştirir |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/sütunlar/okuma | Yönetilen veritabanı için sütunların bir listesini döndürün |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/currentSensitivityLabels/Read | Belirli bir veritabanının duyarlılık etiketlerini listeleyin |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/currentSensitivityLabels/Write | Batch güncelleştirmesi duyarlılık etiketleri |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/Sil | Varolan bir yönetilen veritabanını siler |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/sağlayıcılar/Microsoft. Insights/logDefinitions/Read | Yönetilen örnek veritabanları için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/okuma | Mevcut yönetilen veritabanını alır |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/recommendedSensitivityLabels/Read | Belirli bir veritabanının duyarlılık etiketlerini listeleyin |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/recommendedSensitivityLabels/Write | Toplu güncelleştirme önerilen duyarlılık etiketleri |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/restoreDetails/Read | Geri yükleme devam ederken yönetilen veritabanı geri yükleme ayrıntılarını döndürür. |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/okuma | Yönetilen bir veritabanı şeması alın. |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/okuma | Yönetilen veritabanı sütunu al |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Delete | Belirli bir sütunun duyarlılık etiketini Sil |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Disable/Action | Belirli bir sütunda duyarlılık önerilerini devre dışı bırak |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Enable/Action | Belirli bir sütunda duyarlılık önerilerini etkinleştir |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Read | Belirli bir sütunun duyarlılık etiketini al |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Write | Belirli bir sütunun duyarlılık etiketini oluştur veya güncelleştir |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/okuma | Yönetilen veritabanı tablosu al |
> | Eylem | Microsoft. SQL/ManagedInstances/Databases/Securityalcertpolicies/Read | Belirli bir sunucu için yapılandırılmış yönetilen veritabanı tehdit algılama ilkelerinin listesini alma |
> | Eylem | Microsoft. SQL/ManagedInstances/Databases/Securityalcertpolicies/Write | Belirli bir yönetilen veritabanı için veritabanı tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/securityEvents/Read | Yönetilen veritabanı güvenlik olaylarını alır |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/sensitivityLabels/Read | Belirli bir veritabanının duyarlılık etiketlerini listeleyin |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/transparentDataEncryption/Read | Belirli bir yönetilen veritabanında veritabanı Saydam Veri Şifrelemesi ayrıntılarını alma |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/transparentDataEncryption/Write | Belirli bir yönetilen veritabanı için veritabanı Saydam Veri Şifrelemesi değiştirme |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ | Belirli bir veritabanı için güvenlik açığı değerlendirmesini kaldırma |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ | Bir givendatabase üzerinde güvenlik açığı değerlendirme ilkelerini alma |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ek olarak, bu, kural/taban çizgileri/silme | Belirli bir veritabanı için güvenlik açığı değerlendirme kuralı temelini kaldır |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ek olarak, bu, kurallar/temeller/okunan | Belirli bir veritabanı için güvenlik açığı değerlendirme kuralı temelini al |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ek olarak, bu, kural/taban çizgileri/yazma | Belirli bir veritabanı için güvenlik açığı değerlendirme kuralı temelini değiştirme |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/bu işlem,, taramalar/taramalar/dışarı aktarma/eylem | Var olan bir tarama sonucunu okunabilir bir biçime dönüştürün. Zaten varsa, hiçbir şey olmaz |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/bu işlem, bir veya taramalar/ınitiatescan/ACTION | Güvenlik açığı değerlendirmesi veritabanı taramasını yürütün. |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ | Veritabanı güvenlik açığı değerlendirmesi tarama kayıtlarının listesini döndürün veya belirtilen tarama KIMLIĞI için tarama kaydını alın. |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/ | Belirli bir veritabanı için güvenlik açığı değerlendirmesini değiştirme |
> | Eylem | Microsoft. SQL/ManagedInstances/veritabanları/yazma | Yeni bir veritabanı oluşturur veya var olan bir veritabanını güncelleştirir. |
> | Eylem | Microsoft. SQL/ManagedInstances/Delete | Varolan bir yönetilen örneği siler. |
> | Eylem | Microsoft. SQL/ManagedInstances/encryptionProtector/Read | Sunucu şifreleme koruyucuları listesini döndürür veya belirtilen sunucu şifreleme koruyucusu için özellikleri alır. |
> | Eylem | Microsoft. SQL/ManagedInstances/encryptionProtector/yeniden doğrula/eylem | Belirtilen sunucu şifreleme koruyucusu için özellikleri güncelleştirin. |
> | Eylem | Microsoft. SQL/ManagedInstances/encryptionProtector/Write | Belirtilen sunucu şifreleme koruyucusu için özellikleri güncelleştirin. |
> | Eylem | Microsoft. SQL/ManagedInstances/anahtarlar/Sil | Mevcut bir Azure SQL yönetilen örnek anahtarını siler. |
> | Eylem | Microsoft. SQL/ManagedInstances/anahtarlar/okuma | Yönetilen örnek anahtarlarının listesini döndürün veya belirtilen yönetilen örnek anahtarı için özellikleri alır. |
> | Eylem | Microsoft. SQL/ManagedInstances/anahtarlar/Write | Belirtilen parametrelerle bir anahtar oluşturur veya belirtilen yönetilen örnek anahtarı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/ManagedInstances/metricDefinitions/oku | Yönetilen örnek ölçüm tanımlarını al |
> | Eylem | Microsoft. SQL/ManagedInstances/ölçümler/okuma | Yönetilen örnek ölçümlerini al |
> | Eylem | Microsoft. SQL/ManagedInstances/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. SQL/ManagedInstances/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. SQL/ManagedInstances/Providers/Microsoft. Insights/logDefinitions/Read | Yönetilen örnekler için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. SQL/ManagedInstances/Providers/Microsoft. Insights/metricDefinitions/Read | Yönetilen örnekler için kullanılabilen ölçümlerin dönüş türleri |
> | Eylem | Microsoft. SQL/ManagedInstances/Read | Yönetilen örneklerin listesini döndürün veya belirtilen yönetilen örnek için özellikleri alır. |
> | Eylem | Microsoft. SQL/ManagedInstances/recoverableDatabases/Read | Kurtarılabilir yönetilen veritabanlarının listesini döndürür |
> | Eylem | Microsoft. SQL/ManagedInstances/Restokbledroppeddatabases/backupShortTermRetentionPolicies/Read | Bırakılmış bir yönetilen veritabanı için kısa süreli bekletme ilkesi alır |
> | Eylem | Microsoft. SQL/ManagedInstances/Restokbledroppeddatabases/backupShortTermRetentionPolicies/Write | Bırakılmış bir yönetilen veritabanı için kısa süreli bekletme ilkesini güncelleştirir |
> | Eylem | Microsoft. SQL/ManagedInstances/Restokbledroppeddatabases/Read | Yeniden yüklenebilen, bırakılmış yönetilen veritabanlarının listesini döndürür. |
> | Eylem | Microsoft. SQL/ManagedInstances/Securityalcertpolicies/okuma | Belirli bir sunucu için yapılandırılmış yönetilen sunucu tehdit algılama ilkelerinin listesini alma |
> | Eylem | Microsoft. SQL/ManagedInstances/Securityalcertpolicies/Write | Belirli bir yönetilen sunucu için yönetilen sunucu tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. SQL/ManagedInstances/tdeCertificates/ACTION | TDE sertifikası oluştur/güncelleştir |
> | Eylem | Microsoft. SQL/ManagedInstances/ | Belirli bir yönetilen örnek için güvenlik açığı değerlendirmesini kaldırma |
> | Eylem | Microsoft. SQL/ManagedInstances/ | Belirli bir yönetilen örnekteki güvenlik açığı değerlendirme ilkelerini alma |
> | Eylem | Microsoft. SQL/ManagedInstances/ | Belirli bir yönetilen örnek için güvenlik açığı değerlendirmesini değiştirme |
> | Eylem | Microsoft. SQL/ManagedInstances/Write | Belirtilen parametrelerle yönetilen bir örnek oluşturur veya belirtilen yönetilen örnek için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/işlemler/okuma | Kullanılabilir REST işlemlerini alır |
> | Eylem | Microsoft. SQL/privateEndpointConnectionsApproval/eylem | Kullanıcının özel bir uç nokta bağlantısını onaylamasını izin verilip verilmeyeceğini belirler |
> | Eylem | Microsoft. SQL/Register/ACTION | Microsoft SQL veritabanı kaynak sağlayıcısı için aboneliği kaydeder ve Microsoft SQL veritabanlarının oluşturulmasına izin vermez. |
> | Eylem | Microsoft. SQL/Servers/Administrators/Delete | Belirli bir Azure Active Directory yönetici nesnesini siler |
> | Eylem | Microsoft. SQL/Servers/Administrators/Read | Belirli bir Azure Active Directory yönetici nesnesini alır |
> | Eylem | Microsoft. SQL/Servers/Administrators/Write | Belirli bir Azure Active Directory yönetici nesnesi ekler veya güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/danışmanları/okuma | Sunucu için kullanılabilen danışmanlarının listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/danışmanları/recommendedActions/Read | Sunucu için belirtilen danışman için önerilen eylemlerin listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/danışmanları/recommendedActions/Write | Sunucuda önerilen eylemi Uygula |
> | Eylem | Microsoft. SQL/Servers/danışmanları/yazma | Güncelleştirme, sunucu düzeyinde bir danışman 'ın durumunu otomatik olarak yürütür. |
> | Eylem | Microsoft. SQL/Servers/auditingPolicies/Read | Belirli bir sunucuda yapılandırılan varsayılan sunucu tablosu denetim ilkesinin ayrıntılarını alın |
> | Eylem | Microsoft. SQL/Servers/auditingPolicies/Write | Belirli bir sunucu için varsayılan sunucu tablosu denetimini değiştirme |
> | Eylem | Microsoft. SQL/Servers/auditingSettings/operationResults/Read | Sunucu blobu denetim ilkesi ayarlama işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Servers/auditingSettings/Read | Belirli bir sunucuda yapılandırılan sunucu blobu denetim ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/auditingSettings/Write | Belirli bir sunucu için sunucu blobu denetimini değiştirme |
> | Eylem | Microsoft. SQL/Servers/otomatik olarak ayarlama/okuma | Sunucu için otomatik ayarlama ayarlarını döndürür |
> | Eylem | Microsoft. SQL/Servers/otomatik olarak ayarlama/yazma | Sunucu için otomatik ayarlama ayarlarını güncelleştirir ve güncelleştirilmiş ayarları döndürür |
> | Eylem | Microsoft. SQL/Servers/backupLongTermRetentionVaults/Delete | Var olan bir yedek arşiv kasasını siler. |
> | Eylem | Microsoft. SQL/Servers/backupLongTermRetentionVaults/Read | Bu işlem, yedekleme uzun süreli saklama Kasası elde etmek için kullanılır. Bu sunucuya kayıtlı kasayla ilgili bilgileri döndürür |
> | Eylem | Microsoft. SQL/Servers/backupLongTermRetentionVaults/Write | Bu işlem, yedekleme uzun süreli saklama kasasını bir sunucuya kaydetmek için kullanılır |
> | Eylem | Microsoft. SQL/Servers/communicationLinks/Delete | Var olan bir sunucu iletişim bağlantısını siler. |
> | Eylem | Microsoft. SQL/Servers/communicationLinks/Read | Belirtilen bir sunucunun iletişim bağlantılarının listesini döndürür. |
> | Eylem | Microsoft. SQL/Servers/communicationLinks/Write | Sunucu iletişim bağlantısı oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. SQL/Servers/connectionPolicies/Read | Belirtilen bir sunucunun sunucu bağlantı ilkelerinin listesini döndürür. |
> | Eylem | Microsoft. SQL/Servers/connectionPolicies/Write | Sunucu bağlantı ilkesi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/danışmanları/okuma | Veritabanı için kullanılabilen danışmanlarının listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/danışmanları/recommendedActions/okuma | Veritabanı için belirtilen danışman için önerilen eylemlerin listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/danışmanları/recommendedActions/yazma | Önerilen eylemi veritabanına Uygula |
> | Eylem | Microsoft. SQL/Servers/veritabanları/danışmanları/yazma | Bir danışman 'nin veritabanı düzeyinde otomatik yürütme durumunu güncelleştir. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/auditingPolicies/Read | Belirli bir veritabanında yapılandırılan tablo denetim ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/auditingPolicies/Write | Belirli bir veritabanı için tablo Denetim ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/auditingSettings/Read | Belirli bir veritabanında yapılandırılan blob denetim ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/auditingSettings/Write | Belirli bir veritabanı için blob denetim ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/auditRecords/okuma | Veritabanı blobu denetim kayıtlarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/otomatik olarak ayarlama/okuma | Bir veritabanı için otomatik ayarlama ayarlarını döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/otomatik olarak ayarlama/yazma | Bir veritabanı için otomatik ayarlama ayarlarını güncelleştirir ve güncelleştirilmiş ayarları döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/azureAsyncOperation/Read | Bir veritabanı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/backupLongTermRetentionPolicies/Read | Belirtilen bir veritabanının yedekleme Arşivi ilkelerinin listesini döndürür. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/backupLongTermRetentionPolicies/Write | Veritabanı yedekleme arşivi ilkesi oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/backupShortTermRetentionPolicies/Read | Bir veritabanı için kısa süreli bekletme ilkesi alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/backupShortTermRetentionPolicies/Write | Bir veritabanı için kısa süreli saklama ilkesini güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/veritabanları/sütunlar/okuma | Bir veritabanı için sütun listesi döndürme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/connectionPolicies/okuma | Belirli bir veritabanında yapılandırılan bağlantı ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/connectionPolicies/Write | Belirli bir veritabanı için bağlantı ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/currentSensitivityLabels/Read | Belirli bir veritabanının duyarlılık etiketlerini listeleyin |
> | Eylem | Microsoft. SQL/Servers/veritabanları/currentSensitivityLabels/Write | Batch güncelleştirmesi duyarlılık etiketleri |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/Read | Veritabanı veri maskeleme ilkelerinin listesini döndürün. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/Rules/Delete | Belirli bir veritabanı için veri maskeleme ilkesi kuralını Sil |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/kurallar/okuma | Belirli bir veritabanında yapılandırılan veri maskeleme ilkesi kuralının ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/Rules/Write | Belirli bir veritabanı için veri maskeleme ilke kuralını değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/Write | Belirli bir veritabanı için veri maskeleme ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataWarehouseQueries/dataWarehouseQuerySteps/Read | Seçilen adım KIMLIĞI için veri ambarı sorgusunun dağıtılmış sorgu adımı bilgilerini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataWarehouseQueries/Read | Seçili sorgu KIMLIĞI için veri ambarı dağıtım sorgu bilgilerini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/dataWarehouseUserActivities/okundu | Çalışan ve askıya alınmış sorguları içeren bir SQL veri ambarı örneğinin Kullanıcı etkinliklerini alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Delete | Var olan bir veritabanını siler. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Export/Action | Azure SQL veritabanını dışarı aktarma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/extendedAuditingSettings/Read | Belirli bir veritabanında yapılandırılan genişletilmiş blob denetim ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/extendedAuditingSettings/Write | Belirli bir veritabanı için genişletilmiş blob denetim ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/uzantılar/okuma | Veritabanı için uzantıların bir koleksiyonunu alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/uzantılar/yazma | Belirli bir veritabanı için uzantıyı değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/yük devretme/eylem | Müşterinin başlattığı veritabanını yük devretme. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/geoBackupPolicies/okuma | Belirli bir veritabanı için coğrafi yedekleme ilkeleri alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/geoBackupPolicies/Write | Veritabanı coğrafi yedekleme ilkesi oluşturma veya güncelleştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ımportexportoperationresults/Read | Devam eden içeri/dışarı aktarma işlemlerini alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/maintenanceWindowOptions/Read | Seçili veritabanı için kullanılabilir bakım pencerelerinin bir listesini alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/maintenanceWindows/Read | Seçilen bir veritabanı için bakım Windows ayarlarını alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/maintenanceWindows/Write | Seçili veritabanı için bakım Windows ayarlarını ayarlar. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/metricDefinitions/Read | Veritabanları için kullanılabilir ölçümlerin dönüş türleri |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ölçümler/okuma | Veritabanları için ölçümleri döndürme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Move/Action | Varolan bir veritabanının adını değiştirin. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/operationResults/Read | Bir veritabanı işleminin durumunu alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/işlemler/iptal/eylem | Henüz bitmemiş Azure SQL veritabanı bekleyen zaman uyumsuz işlemi iptal eder. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/işlemler/okuma | Veritabanında gerçekleştirilen işlemlerin listesini döndürün |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Duraklat/eylem | Azure SQL veri ambarı veritabanını Duraklat |
> | Eylem | Microsoft. SQL/Servers/veritabanları/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/veritabanları/sağlayıcılar/Microsoft. Insights/logDefinitions/Read | Veritabanları için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Veritabanları için kullanılabilir ölçümlerin dönüş türleri |
> | Eylem | Microsoft. SQL/Servers/veritabanları/queryStore/Querymetin/Read | Belirtilen parametrelere karşılık gelen sorgu metinleri koleksiyonunu döndürür. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/queryStore/Read | Veritabanı için sorgu deposu ayarlarının geçerli değerlerini döndürür. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/queryStore/Write | Veritabanı için sorgu deposu ayarını güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/veritabanları/okuma | Veritabanlarının listesini döndürün veya belirtilen veritabanının özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/recommendedSensitivityLabels/Read | Belirli bir veritabanının duyarlılık etiketlerini listeleyin |
> | Eylem | Microsoft. SQL/Servers/veritabanları/recommendedSensitivityLabels/Write | Toplu güncelleştirme önerilen duyarlılık etiketleri |
> | Eylem | Microsoft. SQL/Servers/veritabanları/replicationLinks/Delete | Çoğaltma ilişkisini zorla ve olası veri kaybı ile sonlandır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/replicationLinks/yük devretme/eylem | Tüm değişiklikleri birinceden eşitlemeden sonra, bu veritabanını çoğaltma relationship\u0027s birincil öğesine ve uzak birincili ikincil haline getirmek için yük devretme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/replicationLinks/forceFailoverAllowDataLoss/eylem | Olası veri kaybından hemen yük devretme, bu veritabanını çoğaltmaya yönelik olan relationship\u0027s birincili ve uzak birincil |
> | Eylem | Microsoft. SQL/Servers/veritabanları/replicationLinks/Read | Çoğaltma bağlantıları listesini döndürün veya belirtilen çoğaltma bağlantıları için özellikleri alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/replicationLinks/link/Action | Çoğaltma ilişkisini zorla veya iş ortağıyla eşitlemeden sonra sonlandırın |
> | Eylem | Microsoft. SQL/Servers/veritabanları/replicationLinks/updateReplicationMode/Action | Zaman uyumlu veya zaman uyumsuz mod bağlantısı için çoğaltma modunu Güncelleştir |
> | Eylem | Microsoft. SQL/Servers/veritabanları/restorePoints/ACTION | Yeni bir geri yükleme noktası oluşturur |
> | Eylem | Microsoft. SQL/Servers/veritabanları/restorePoints/Delete | Veritabanı için bir geri yükleme noktası siler. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/restorePoints/Read | Veritabanı için geri yükleme noktalarını döndürür. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/özgeçmişi/Action | Azure SQL veri ambarı veritabanını sürdürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/okuma | Veritabanı şeması alın. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/okuma | Veritabanı sütunu al. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Delete | Belirli bir sütunun duyarlılık etiketini Sil |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Disable/Action | Belirli bir sütunda duyarlılık önerilerini devre dışı bırak |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Enable/Action | Belirli bir sütunda duyarlılık önerilerini etkinleştir |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Read | Belirli bir sütunun duyarlılık etiketini al |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/Write | Belirli bir sütunun duyarlılık etiketini oluştur veya güncelleştir |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/okuma | Veritabanı tablosu al. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/recommendedIndexes/Read | Bir veritabanındaki dizin önerilerinin listesini alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/recommendedIndexes/Write | Dizin önerisi uygulama |
> | Eylem | Microsoft. SQL/Servers/Databases/Securityalcertpolicies/Read | Belirli bir sunucu için yapılandırılmış veritabanı tehdit algılama ilkelerinin listesini alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Securityalcertpolicies/Write | Belirli bir veritabanı için veritabanı tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/Databases/Securityölçümlerini/Read | Veritabanı güvenlik ölçümlerinin bir koleksiyonunu alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/sensitivityLabels/Read | Belirli bir veritabanının duyarlılık etiketlerini listeleyin |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Servicetierdanışmanors/Read | Performansı artırmak veya maliyeti azaltmak için sorgu yürütme istatistiklerine bağlı olarak veritabanını ölçeklendirmeye veya azaltmasına ilişkin öneriyi döndürün |
> | Eylem | Microsoft. SQL/Servers/veritabanları/SKU/okuma | Bir veritabanı için kullanılabilen SKU 'ların bir koleksiyonunu alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/cancelSync/Action | Eşitleme grubu eşitlemesini iptal et |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/Delete | Varolan bir eşitleme grubunu siler. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/hubSchemas/Read | Eşitleme Merkezi veritabanı şemaları listesini döndürün |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/logs/Read | Eşitleme grubu günlüklerinin listesini döndürün |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/Read | Eşitleme gruplarının listesini döndürün veya belirtilen eşitleme grubunun özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/refreshHubSchema/Action | Eşitleme Merkezi veritabanı şemasını Yenile |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/refreshHubSchemaOperationResults/Read | Eşitleme Merkezi şema yenileme işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/syncMembers/Delete | Varolan bir eşitleme üyesini siler. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/syncMembers/Read | Eşitleme üyelerinin listesini döndürün veya belirtilen eşitleme üyesinin özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/syncMembers/refreshSchema/Action | Eşitleme üye şemasını Yenile |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/syncMembers/refreshSchemaOperationResults/Read | Eşitleme üye şeması yenileme işleminin sonucunu al |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/syncMembers/schemas/Read | Eşitleme üyesi veritabanı şemaları listesini döndür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/syncMembers/Write | Belirtilen parametrelerle bir eşitleme üyesi oluşturur veya belirtilen eşitleme üyesinin özelliklerini güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/triggerSync/Action | Eşitleme grubu eşitlemesini tetikleme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/syncGroups/Write | Belirtilen parametrelerle bir eşitleme grubu oluşturur veya belirtilen eşitleme grubunun özelliklerini güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/topQueries/queryText/Action | Seçili sorgu KIMLIĞI için Transact-SQL metnini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/topQueries/Read | Seçili zaman döneminde seçili sorgu için toplu çalışma zamanı istatistiklerini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/topQueries/STATISTICS/Read | Seçili zaman döneminde seçili sorgu için toplu çalışma zamanı istatistiklerini döndürür |
> | Eylem | Microsoft. SQL/Servers/veritabanları/transparentDataEncryption/operationResults/Read | Saydam veri şifrelemesi üzerinde devam eden işlemleri alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/transparentDataEncryption/Read | Belirli bir veritabanı için saydam veri şifreleme güvenlik özelliğinin durumunu ve ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/transparentDataEncryption/Write | Saydam veri şifreleme durumunu değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/upgradeDataWarehouse/eylem | Azure SQL veri ambarı veritabanını yükselt |
> | Eylem | Microsoft. SQL/Servers/veritabanları/kullanımlar/okuma | Azure SQL veritabanı kullanımları bilgilerini alır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ | Belirli bir veritabanı için güvenlik açığı değerlendirmesini kaldırma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ | Bir givendatabase üzerinde güvenlik açığı değerlendirme ilkelerini alma |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ek | Belirli bir veritabanı için güvenlik açığı değerlendirme kuralı temelini kaldır |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ek | Belirli bir veritabanı için güvenlik açığı değerlendirme kuralı temelini al |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ek | Belirli bir veritabanı için güvenlik açığı değerlendirme kuralı temelini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ek bir işlem/tarama/dışarı aktarma/eylem | Var olan bir tarama sonucunu okunabilir bir biçime dönüştürün. Zaten varsa, hiçbir şey olmaz |
> | Eylem | Microsoft. SQL/Servers/veritabanları/bu işlem, ve taramalar/ınitiatescan/ACTION | Güvenlik açığı değerlendirmesi veritabanı taramasını yürütün. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ | Veritabanı güvenlik açığı değerlendirmesi tarama kayıtlarının listesini döndürün veya belirtilen tarama KIMLIĞI için tarama kaydını alın. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/ | Belirli bir veritabanı için güvenlik açığı değerlendirmesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/bu işlem | Güvenlik açığı değerlendirmesi veritabanı taramasını yürütün. |
> | Eylem | Microsoft. SQL/Servers/veritabanları/bu işlem, beklenen taramalar/operationResults/Read | Veritabanı güvenlik açığı değerlendirme taraması yürütme işleminin sonucunu alma |
> | Eylem | Microsoft. SQL/Servers/databases/, bir ıactive Settings/Read | Belirli bir veritabanında yapılandırılan güvenlik açığı değerlendirmesinin ayrıntılarını alın |
> | Eylem | Microsoft. SQL/Servers/databases/, bir ıactive Settings/Write | Belirli bir veritabanı için güvenlik açığı değerlendirmesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/veritabanları/Write | Belirtilen parametrelerle bir veritabanı oluşturur veya belirtilen veritabanı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/Delete | Var olan bir sunucuyu siler. |
> | Eylem | Microsoft. SQL/Servers/Diskalrecoveryconfiguration/Delete | Belirli bir sunucu için mevcut bir olağanüstü durum kurtarma yapılandırmasını siler |
> | Eylem | Microsoft. SQL/Servers/Diskalrecoveryconfiguration/yük devretme/eylem | Diskalrecoveryconfiguration yük devretme |
> | Eylem | Microsoft. SQL/Servers/Diskalrecoveryconfiguration/forceFailoverAllowDataLoss/eylem | Diskalrecoveryconfiguration yük devretmeyi zorla |
> | Eylem | Microsoft. SQL/Servers/Diskalrecoveryconfiguration/Read | Bu sunucuyu içeren olağanüstü durum kurtarma yapılandırmalarının bir koleksiyonunu alır |
> | Eylem | Microsoft. SQL/Servers/Diskalrecoveryconfiguration/Write | Sunucu olağanüstü durum kurtarma yapılandırmasını değiştirme |
> | Eylem | Microsoft. SQL/Servers/Elakpoolelismates/Read | Bu sunucu için önceden oluşturulmuş elastik havuz tahminlerinin listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/Elakpoolemates/Write | Belirtilen veritabanlarının listesi için yeni elastik havuz tahmini oluşturur |
> | Eylem | Microsoft. SQL/Servers/Elaor havuzları/danışmanları/okuma | Elastik havuz için kullanılabilen danışmanlarının listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/Elaor havuzları/danışmanları/recommendedActions/okuma | Elastik havuz için belirtilen danışman önerilen eylemlerin listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/Elaor havuzları/danışmanları/recommendedActions/yazma | Elastik havuzda önerilen eylemi uygulayın |
> | Eylem | Microsoft. SQL/Servers/Elaor havuzları/danışmanları/yazma | Elastik havuz düzeyinde bir danışman 'ın otomatik yürütülme durumunu güncelleştirin. |
> | Eylem | Microsoft. SQL/Servers/Elaun havuzları/veritabanları/okuma | Elastik havuz için veritabanlarının listesini alır |
> | Eylem | Microsoft. SQL/Servers/Elaun havuzları/Delete | Mevcut elastik havuzu Sil |
> | Eylem | Microsoft. SQL/Servers/Elaun havuzları/elasticPoolActivity/Read | Belirli bir elastik veritabanı havuzundaki etkinlikleri ve ayrıntıları alma |
> | Eylem | Microsoft. SQL/Servers/Elaetiketler/Elakpooldatabaseactivity/Read | Elastik veritabanı havuzunun bir parçası olan belirli bir veritabanında etkinlikleri ve ayrıntıları alma |
> | Eylem | Microsoft. SQL/Servers/Elaetiketler/yük devretme/eylem | Müşteri tarafından başlatılan elastik havuz yük devretme. |
> | Eylem | Microsoft. SQL/Servers/Elakıhavuzhavuzları/metricDefinitions/okuma | Elastik veritabanı havuzları için kullanılabilen ölçümlerin dönüş türleri |
> | Eylem | Microsoft. SQL/Servers/ela/Pools/ölçümler/okuma | Elastik veritabanı havuzları için dönüş ölçümleri |
> | Eylem | Microsoft. SQL/Servers/Elaetiketler/işlemler/iptal/eylem | Henüz bitmemiş Azure SQL elastik havuzu bekleyen zaman uyumsuz işlemini iptal eder. |
> | Eylem | Microsoft. SQL/Servers/ela/Pools/işlemler/okuma | Elastik havuzda gerçekleştirilen işlemlerin listesini döndürün |
> | Eylem | Microsoft. SQL/Servers/ela/Pools/Providers/Microsoft. Insights/diagnosticSettings/Read | Kaynak için tanılama ayarını alır |
> | Eylem | Microsoft. SQL/Servers/ela/Pools/Providers/Microsoft. Insights/diagnosticSettings/Write | Kaynak için tanılama ayarını oluşturur veya güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/Elakıpools/Providers/Microsoft. Insights/metricDefinitions/Read | Elastik veritabanı havuzları için kullanılabilen ölçümlerin dönüş türleri |
> | Eylem | Microsoft. SQL/Servers/Elaun havuzları/okuma | Belirli bir sunucudaki elastik havuzun ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/ela/Pools/SKU 'lar/okuma | Elastik havuz için kullanılabilen SKU 'ların bir koleksiyonunu alır |
> | Eylem | Microsoft. SQL/Servers/Elaun havuzları/yazma | Mevcut elastik havuzun yeni veya değişiklik özelliklerini oluştur |
> | Eylem | Microsoft. SQL/Servers/encryptionProtector/Read | Sunucu şifreleme koruyucuları listesini döndürür veya belirtilen sunucu şifreleme koruyucusu için özellikleri alır. |
> | Eylem | Microsoft. SQL/Servers/encryptionProtector/yeniden doğrula/eylem | Belirtilen sunucu şifreleme koruyucusu için özellikleri güncelleştirin. |
> | Eylem | Microsoft. SQL/Servers/encryptionProtector/Write | Belirtilen sunucu şifreleme koruyucusu için özellikleri güncelleştirin. |
> | Eylem | Microsoft. SQL/Servers/extendedAuditingSettings/Read | Belirli bir sunucuda yapılandırılmış genişletilmiş sunucu blobu denetim ilkesinin ayrıntılarını alma |
> | Eylem | Microsoft. SQL/Servers/extendedAuditingSettings/Write | Belirli bir sunucu için genişletilmiş sunucu blobu denetimini değiştirme |
> | Eylem | Microsoft. SQL/Servers/failoverGroups/Delete | Var olan bir yük devretme grubunu siler. |
> | Eylem | Microsoft. SQL/Servers/failoverGroups/yük devretme/eylem | Var olan bir yük devretme grubunda planlı yük devretmeyi yürütür. |
> | Eylem | Microsoft. SQL/Servers/failoverGroups/forceFailoverAllowDataLoss/eylem | Var olan bir yük devretme grubunda zorla yük devretmeyi yürütür. |
> | Eylem | Microsoft. SQL/Servers/failoverGroups/Read | Yük devretme grupları listesini döndürür veya belirtilen yük devretme grubu için özellikleri alır. |
> | Eylem | Microsoft. SQL/Servers/failoverGroups/Write | Belirtilen parametrelerle bir yük devretme grubu oluşturur veya belirtilen yük devretme grubu için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/firewallRules/Delete | Var olan bir sunucu güvenlik duvarı kuralını siler. |
> | Eylem | Microsoft. SQL/Servers/firewallRules/Read | Sunucu Güvenlik Duvarı kurallarının listesini döndürün veya belirtilen sunucu güvenlik duvarı kuralının özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/firewallRules/Write | Belirtilen parametrelere sahip bir sunucu güvenlik duvarı kuralı oluşturur, belirtilen kural için özellikleri güncelleştirin veya yeni sunucu güvenlik duvarı kurallarıyla tüm var olan kuralların üzerine yazın. |
> | Eylem | Microsoft. SQL/Servers/Import/Action | Sunucuda yeni bir veritabanı oluşturun ve bir DacPac paketinden şemayı ve verileri dağıtın |
> | Eylem | Microsoft. SQL/Servers/ımportexportoperationresults/Read | Devam eden içeri/dışarı aktarma işlemlerini alır |
> | Eylem | Microsoft. SQL/Servers/ınterfaceendpointprofiles/Delete | Belirtilen arabirim uç noktası profilini siler |
> | Eylem | Microsoft. SQL/Servers/ınterfaceendpointprofiles/okuma | Belirtilen arabirim uç noktası profilinin özelliklerini döndürür |
> | Eylem | Microsoft. SQL/Servers/ınterfaceendpointprofiles/Write | Belirtilen parametrelerle bir arabirim uç noktası profili oluşturur veya belirtilen arabirim uç noktası için özellikleri veya etiketleri güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/jobAgents/Delete | Azure SQL DB iş aracısını siler |
> | Eylem | Microsoft. SQL/Servers/jobAgents/Read | Azure SQL DB iş aracısını alır |
> | Eylem | Microsoft. SQL/Servers/jobAgents/Write | Bir Azure SQL DB iş Aracısı oluşturur veya güncelleştirir |
> | Eylem | Microsoft. SQL/Servers/anahtarlar/Delete | Var olan bir sunucu anahtarını siler. |
> | Eylem | Microsoft. SQL/sunucular/anahtarlar/okuma | Sunucu anahtarlarının listesini döndürün veya belirtilen sunucu anahtarı için özellikleri alır. |
> | Eylem | Microsoft. SQL/Servers/anahtarlar/Write | Belirtilen parametrelere sahip bir anahtar oluşturur veya belirtilen sunucu anahtarı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/operationResults/Read | Devam eden sunucu işlemlerini alır |
> | Eylem | Microsoft. SQL/Servers/Privateendpointconnectionproxy/Delete | Var olan bir özel uç nokta bağlantı proxy 'sini siler |
> | Eylem | Microsoft. SQL/Servers/Privateendpointconnectionproxy/okuma | Özel uç nokta bağlantı proxy 'leri listesini döndürür veya belirtilen özel uç nokta bağlantı proxy 'sinin özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/Privateendpointconnectionproxy/Validate/Action | Özel bir uç nokta bağlantısını doğrular NRP tarafında çağrı oluştur |
> | Eylem | Microsoft. SQL/Servers/Privateendpointconnectionproxy/yazma | Belirtilen parametrelerle özel bir uç nokta bağlantı proxy 'si oluşturur veya belirtilen özel uç nokta bağlantısı proxy 'si için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/privateEndpointConnections/Delete | Var olan bir özel uç nokta bağlantısını siler |
> | Eylem | Microsoft. SQL/Servers/privateEndpointConnections/Read | Özel uç nokta bağlantılarının listesini döndürür veya belirtilen özel uç nokta bağlantısının özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/privateEndpointConnections/Write | Var olan bir özel uç nokta bağlantısını onaylar veya reddeder |
> | Eylem | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/eylem | Kullanıcının özel bir uç nokta bağlantısını onaylamasını izin verilip verilmeyeceğini belirler |
> | Eylem | Microsoft. SQL/Servers/privateLinkResources/okuma | Karşılık gelen SQL Server için özel bağlantı kaynaklarını alın |
> | Eylem | Microsoft. SQL/Servers/Providers/Microsoft. Insights/metricDefinitions/Read | Sunucular için kullanılabilen ölçümlerin dönüş türleri |
> | Eylem | Microsoft. SQL/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/recommendedElasticPools/Databases/Read | Belirli bir sunucu için önerilen elastik veritabanı havuzları için ölçümleri alma |
> | Eylem | Microsoft. SQL/Servers/recommendedElasticPools/Read | Maliyeti azaltmak veya geçmiş kaynak kullanımına göre performansı artırmak için esnek veritabanı havuzları için öneri alın |
> | Eylem | Microsoft. SQL/Servers/recoverableDatabases/Read | Bu işlem, veritabanını son bilinen iyi yedekleme noktasına geri yüklemek için canlı veritabanının olağanüstü durum kurtarması için kullanılır. Son iyi yedekleme hakkında bilgi döndürür ancak veritabanını gerçekten geri yüklemek mümkün değildir. |
> | Eylem | Microsoft. SQL/Servers/replicationLinks/Read | Çoğaltma bağlantıları listesini döndürün veya belirtilen çoğaltma bağlantıları için özellikleri alır. |
> | Eylem | Microsoft. SQL/Servers/Restokbledroppeddatabases/Read | Hala bekletme ilkesi içinde olan belirli bir sunucuda bırakılan veritabanlarının listesini alın. |
> | Eylem | Microsoft. SQL/Servers/Securityalcertpolicies/operationResults/Read | Sunucu tehdit algılama ilkesi yazma işleminin sonuçlarını al |
> | Eylem | Microsoft. SQL/Servers/Securityalcertpolicies/Read | Belirli bir sunucu için yapılandırılmış sunucu tehdit algılama ilkelerinin listesini alma |
> | Eylem | Microsoft. SQL/Servers/Securityalcertpolicies/Write | Belirli bir sunucu için sunucu tehdit algılama ilkesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/Serviceamaçlar/okuma | Belirli bir sunucuda kullanılabilen hizmet düzeyi hedeflerinin (performans katmanları olarak da bilinir) listesini alma |
> | Eylem | Microsoft. SQL/Servers/syncAgents/Delete | Var olan bir eşitleme aracısını siler. |
> | Eylem | Microsoft. SQL/Servers/syncAgents/generateKey/Action | Eşitleme Aracısı kayıt anahtarı oluştur |
> | Eylem | Microsoft. SQL/Servers/syncAgents/Linkedveritabanları/okuma | Eşitleme Aracısı bağlantılı veritabanlarının listesini döndürür |
> | Eylem | Microsoft. SQL/Servers/syncAgents/Read | Eşitleme aracılarının listesini döndürün veya belirtilen eşitleme aracısının özelliklerini alır. |
> | Eylem | Microsoft. SQL/Servers/syncAgents/Write | Belirtilen parametrelerle bir eşitleme Aracısı oluşturur veya belirtilen eşitleme aracısının özelliklerini güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/tdeCertificates/ACTION | TDE sertifikası oluştur/güncelleştir |
> | Eylem | Microsoft. SQL/Servers/kullanımlar/Read | Sunucu DTU kotasını ve geçerli DTU tüketimini sunucu içindeki tüm veritabanlarına göre döndür |
> | Eylem | Microsoft. SQL/Servers/virtualNetworkRules/Delete | Var olan bir sanal ağ kuralını siler |
> | Eylem | Microsoft. SQL/Servers/virtualNetworkRules/Read | Sanal ağ kurallarının listesini döndürün veya belirtilen sanal ağ kuralı için özellikleri alır. |
> | Eylem | Microsoft. SQL/Servers/virtualNetworkRules/Write | Belirtilen parametrelere sahip bir sanal ağ kuralı oluşturur veya belirtilen sanal ağ kuralı için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/Servers/ | Belirli bir sunucu için güvenlik açığı değerlendirmesini kaldırma |
> | Eylem | Microsoft. SQL/Servers/ | Belirli bir sunucuda güvenlik açığı değerlendirme ilkelerini alma |
> | Eylem | Microsoft. SQL/Servers/ | Belirli bir sunucu için güvenlik açığı değerlendirmesini değiştirme |
> | Eylem | Microsoft. SQL/Servers/Write | Belirtilen parametrelere sahip bir sunucu oluşturur veya belirtilen sunucu için özellikleri veya etiketleri güncelleştirir. |
> | Eylem | Microsoft. SQL/Unregister/eylem | Microsoft SQL veritabanı kaynak sağlayıcısı için aboneliğin kaydını siler ve Microsoft SQL veritabanlarının oluşturulmasına izin vermez. |
> | Eylem | Microsoft. SQL/Virtualkümeler/Delete | Var olan bir sanal kümeyi siler. |
> | Eylem | Microsoft. SQL/Virtualkümeler/okuma | Sanal kümelerin listesini döndürün veya belirtilen sanal kümenin özelliklerini alır. |
> | Eylem | Microsoft. SQL/Virtualkümeler/Write | Sanal küme etiketlerini güncelleştirir. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Storage/checknameavaılabılıty/Read | Hesap adının geçerli olduğunu ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. Storage/Locations/checknameavaılabılıty/Read | Hesap adının geçerli olduğunu ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. Storage/Locations/Deletevirtualnetworkoralt ağları/eylem | Microsoft. Storage 'a sanal ağ veya alt ağın silindiğini bildirir |
> | Eylem | Microsoft. Storage/konumlar/kullanımlar/okuma | Belirtilen abonelikteki kaynaklar için sınırı ve geçerli kullanım sayısını döndürür |
> | Eylem | Microsoft. Storage/işlemler/okuma | Zaman uyumsuz bir işlemin durumunu yoklar. |
> | Eylem | Microsoft. Storage/Register/ACTION | Depolama kaynak sağlayıcısı için aboneliği kaydeder ve depolama hesaplarının oluşturulmasına izin vermez. |
> | Eylem | Microsoft. Storage/SKU/okuma | Microsoft. Storage tarafından desteklenen SKU 'Ları listeler. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/ekleme/eylem | Blob içeriği ekleme sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/Sil | Bir blobu silmenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/deleteAutomaticSnapshot/Action | Otomatik anlık görüntü silmenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/filtre/eylem | Eşleşen Etiketler filtresi olan bir hesap altındaki Blobların listesini döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/okuma | Blob veya Blobların listesini döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/runAsSuperUser/Action | Blob komutunun sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/Etiketler/okuma | Blob etiketlerinin okunması sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/Etiketler/yazma | Blob etiketlerinin yazılması sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/yazma | Blob yazma sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/clearLegalHold/Action | Blob kapsayıcısı yasal tutmayı temizle |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Delete | Bir kapsayıcıyı silmenin sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Imdeğiştirici Bilitypolicies/Delete | Blob kapsayıcısı kullanılabilirlik ilkesini Sil |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Imdeğiştirici Bilitypolicies/Genişlet/eylem | Blob kapsayıcısı dengeskaya bilirlik ilkesini Genişlet |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Imdeğiştirici Bilitypolicies/Lock/Action | Blob kapsayıcısı dengeskaya bilirlik ilkesini kilitle |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Imdeğiştirici Bilitypolicies/Read | Blob kapsayıcısı dengeskaya bilirlik ilkesini al |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Imdeğiştirici Bilitypolicies/Write | Blob kapsayıcısı dengeskaya bilirlik ilkesini koy |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/kira/eylem | Kira blob kapsayıcısının sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/okuma | Bir kapsayıcı döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/okuma | Kapsayıcıların listesini döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/setLegalHold/Action | Blob kapsayıcısı yasal tutmayı ayarla |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Write | Patch blob kapsayıcısının sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Write | Put blob kapsayıcısının sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Blob hizmeti için bir Kullanıcı temsili anahtarı döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/okuma |  |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/okuma | Blob hizmeti özelliklerini veya istatistiklerini döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/blobServices/Write | Put blob hizmeti özelliklerinin sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/Delete | Var olan bir depolama hesabını siler. |
> | Eylem | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Eylem | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Eylem | Microsoft. Storage/storageAccounts/yük devretme/eylem | Müşteri, kullanılabilirlik sorunları durumunda yük devretmeyi denetleyebilir |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/actassuperuser/Action | Dosya Yöneticisi ayrıcalıklarını al |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Delete | Dosya/klasör silmenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/modifypermissions/Action | Bir dosya/klasör üzerinde izin değiştirme sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesi döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Write | Bir dosya yazmanın veya bir klasör oluşturmanın sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/Read |  |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/Read | Dosya hizmeti özelliklerini al |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/paylaşımlar/Sil |  |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/paylaşımlar/okuma |  |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/paylaşımlar/okuma |  |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/paylaşımlar/yazma |  |
> | Eylem | Microsoft. Storage/storageAccounts/fileServices/Write |  |
> | Eylem | Microsoft. Storage/storageAccounts/listAccountSas/eylem | Belirtilen depolama hesabı için hesap SAS belirtecini döndürür. |
> | Eylem | Microsoft. Storage/storageAccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | Eylem | Microsoft. Storage/storageAccounts/listServiceSas/eylem | Belirtilen depolama hesabı için hizmet SAS belirtecini döndürür. |
> | Eylem | Microsoft. Storage/storageAccounts/managementPolicies/Delete | Depolama hesabı yönetim ilkelerini Sil |
> | Eylem | Microsoft. Storage/storageAccounts/managementPolicies/okuma | Depolama yönetimi hesabı ilkelerini al |
> | Eylem | Microsoft. Storage/storageAccounts/managementPolicies/Write | Depolama hesabı yönetim ilkelerini yerleştirme |
> | Eylem | Microsoft. Storage/storageAccounts/Objectreplicationilkeleri/silme |  |
> | Eylem | Microsoft. Storage/storageAccounts/Objectreplicationilkeleri/okuma |  |
> | Eylem | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Eylem | Microsoft. Storage/storageAccounts/Privateendpointconnectionproxy/Delete | Özel uç nokta bağlantı proxy 'Lerini Sil |
> | Eylem | Microsoft. Storage/storageAccounts/Privateendpointconnectionproxy/okuma | Özel uç nokta bağlantısı proxy 'Si al |
> | Eylem | Microsoft. Storage/storageAccounts/Privateendpointconnectionproxy/yazma | Özel uç nokta bağlantı proxy 'Leri koy |
> | Eylem | Microsoft. Storage/storageAccounts/privateEndpointConnections/Delete | Özel uç nokta bağlantısını sil |
> | Eylem | Microsoft. Storage/storageAccounts/privateEndpointConnections/Read | Özel uç nokta bağlantısı al |
> | Eylem | Microsoft. Storage/storageAccounts/privateEndpointConnections/Write | Özel uç nokta bağlantısı koy |
> | Eylem | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/eylem | Özel uç nokta bağlantılarını Onayla |
> | Eylem | Microsoft. Storage/storageAccounts/privateLinkResources/Read | StorageAccount grup kimliklerini al |
> | Eylem | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/Sil | Kuyruğu silmenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/ekleme/eylem | İleti eklemenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/Sil | İletiyi silmenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/işlem/eylem | İleti işlemenin sonucunu döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/okundu | Bir ileti döndürür |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/yazma | İleti yazmanın sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/queueServices/kuyrukları/okuma | Kuyruğu veya kuyruk listesini döndürür. |
> | Eylem | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/Write | Sıra yazmanın sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/queueServices/Read | Kuyruk hizmeti özellikleri al |
> | Eylem | Microsoft. Storage/storageAccounts/queueServices/Read | Sıra hizmeti özelliklerini veya istatistiklerini döndürür. |
> | Eylem | Microsoft. Storage/storageAccounts/queueServices/Write | Sıra hizmeti özelliklerini ayarlamanın sonucunu döndürür |
> | Eylem | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Eylem | Microsoft. Storage/storageAccounts/RegenerateKey/ACTION | Belirtilen depolama hesabı için erişim anahtarlarını yeniden oluşturur. |
> | Eylem | Microsoft. Storage/storageAccounts/restoreBlobRanges/eylem | Blob aralıklarını belirtilen sürenin durumuna geri yükle |
> | Eylem | Microsoft. Storage/storageAccounts/Iptal Keuserdelegationkeys/Action | Belirtilen depolama hesabı için tüm Kullanıcı temsili anahtarlarını iptal eder. |
> | Eylem | Microsoft. Storage/storageAccounts/Services/diagnosticSettings/Write | Depolama hesabı tanılama ayarlarını oluşturun/güncelleştirin. |
> | Eylem | Microsoft. Storage/storageAccounts/tableServices/Read | Tablo hizmeti özelliklerini al |
> | Eylem | Microsoft. Storage/storageAccounts/Write | Belirtilen parametrelerle bir depolama hesabı oluşturur veya özellikleri ya da etiketleri güncelleştirir veya belirtilen depolama hesabı için özel etki alanı ekler. |
> | Eylem | Microsoft. Storage/kullanımlar/okuma | Belirtilen abonelikteki kaynaklar için sınırı ve geçerli kullanım sayısını döndürür |

## <a name="microsoftstoragesync"></a>Microsoft. storagessync

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. storagessync/Locations/Checknameavaılabılıty/Action | Depolama eşitleme hizmeti adının geçerli olduğunu ve kullanımda olup olmadığını denetler. |
> | Eylem | Microsoft. storagessync/Locations/iş akışları/işlemler/okuma | Zaman uyumsuz bir işlemin durumunu alır |
> | Eylem | Microsoft. storagessync/işlemler/okuma | Desteklenen Işlemlerin bir listesini alır |
> | Eylem | Microsoft. storagessync/Register/ACTION | Depolama eşitleme sağlayıcısı için aboneliği kaydeder |
> | Eylem | Microsoft. storagessync/storageSyncServices/Delete | Tüm depolama eşitleme hizmetlerini silme |
> | Eylem | Microsoft. storagessync/storageSyncServices/Providers/Microsoft. Insights/metricDefinitions/Read | Depolama Eşitleme Hizmetleri için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/Read | Tüm depolama eşitleme hizmetlerini okuyun |
> | Eylem | Microsoft. storagessync/storageSyncServices/registeredServers/Delete | Kayıtlı tüm sunucuyu Sil |
> | Eylem | Microsoft. storagessync/storageSyncServices/registeredServers/Providers/Microsoft. Insights/metricDefinitions/Read | Kayıtlı sunucu için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/registeredServers/Read | Kayıtlı tüm sunucuları okuyun |
> | Eylem | Microsoft. storagessync/storageSyncServices/registeredServers/Write | Kayıtlı sunucu oluştur veya güncelleştir |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/Delete | Tüm bulut uç noktalarını silme |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/Read | Zaman uyumsuz bir yedekleme/geri yükleme işleminin durumunu alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/Action | Yedeklemeden sonra bu eylemi çağır |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/PostRestore/Action | Geri yüklemeden sonra bu eylemi çağır |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/Action | Yedeklemeden önce bu eylemi çağırın |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/preresıte/Action | Geri yüklemeden önce bu eylemi çağırın |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/Read | Tüm bulut uç noktalarını okuyun |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/restoresinyal/eylem | Sinyal geri yükleme |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/cloudEndpoints/Write | Herhangi bir bulut uç noktası oluşturun veya güncelleştirin |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/Delete | Tüm eşitleme gruplarını silme |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/Providers/Microsoft. Insights/metricDefinitions/Read | Eşitleme grupları için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/Read | Tüm eşitleme gruplarını okuyun |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/serverEndpoints/Delete | Tüm sunucu uç noktalarını silme |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. Insights/metricDefinitions/Read | Sunucu uç noktaları için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/Sunucuendpoints/Read | Tüm sunucu uç noktalarını okuyun |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/Sunucuendpoints/yeniden hesaplama/eylem | Dosyaları bir sunucuya geri çekmek için bu eylemi çağırın |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/serverEndpoints/Write | Sunucu uç noktalarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. storagessync/storageSyncServices/syncGroups/Write | Tüm eşitleme gruplarını oluşturun veya güncelleştirin |
> | Eylem | Microsoft. storagessync/storageSyncServices/iş akışları/operationresults/Read | Zaman uyumsuz bir işlemin durumunu alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/iş akışları/işlemler/okuma | Zaman uyumsuz bir işlemin durumunu alır |
> | Eylem | Microsoft. storagessync/storageSyncServices/iş akışları/okuma | Iş akışlarını oku |
> | Eylem | Microsoft. storagessync/storageSyncServices/Write | Depolama Eşitleme Hizmetleri oluşturun veya güncelleştirin |
> | Eylem | Microsoft. storagessync/Unregister/Action | Depolama eşitleme sağlayıcısı için aboneliğin kaydını siler |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. StorSimple/yöneticileri/accessControlRecords/Delete | Access Control kayıtlarını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/accessControlRecords/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/accessControlRecords/okuma | Access Control kayıtları listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/accessControlRecords/Write | Access Control kayıtlarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/uyarıları/okuma | Uyarıları listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/yedeklemeleri/okuma | Yedekleme kümesini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/bandwidthSettings/Delete | Var olan bir bant genişliği ayarlarını siler (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/bandwidthSettings/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/bandwidthSettings/Read | Bant genişliği ayarlarını listeleme (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/bandwidthSettings/Write | Yeni bir veya güncelleştirme bant genişliği ayarları oluşturur (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/sertifikaları/yazma | Sertifikaları oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/sertifikaları/yazma | Kaynak sertifikası güncelleştirme işlemi kaynak/kasa kimlik bilgisi sertifikasını güncelleştirir. |
> | Eylem | Microsoft. StorSimple/yöneticileri/clearAlerts/Action | Cihaz yöneticisiyle ilişkili tüm uyarıları temizleyin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/Cloudapplıanceconfigurations/Read | Bulut gereci desteklenen yapılandırmalarının listesini listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/configureDevice/Action | Bir cihazı yapılandırır |
> | Eylem | Microsoft. StorSimple/yöneticileri/silme | Cihaz yöneticilerini siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/silme | Kasayı Sil işlemi, ' kasa ' türündeki belirtilen Azure kaynağını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/alertSettings/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/alertSettings/Read | Uyarı ayarlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/alertSettings/Write | Uyarı ayarlarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/authorizeForServiceEncryptionKeyRollover/Action | Cihazların hizmet şifreleme anahtarı geçişi için yetki verme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/Backup/Action | İlke tarafından korunan tüm birimlerin isteğe bağlı yedeklemesini oluşturmak için el ile yedekleme yapın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/Delete | Var olan bir yedekleme Ilkelerini siler (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/Read | Yedekleme Ilkelerini listeleyin (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/zamanlamalar/silme | Var olan zamanlamaları siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/zamanlamalar/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/zamanlamalar/okuma | Zamanlamaları listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/zamanlamalar/yazma | Yeni bir veya güncelleştirmeler zamanlaması oluşturur |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupPolicies/Write | Yeni veya güncelleştirme yedekleme Ilkeleri oluşturur (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yedeklemeleri/silme | Yedekleme kümesini siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yedeklemeleri/öğeleri/kopya/eylem | Bir yedekleme öğesi kullanarak bir paylaşma veya birimi klonlayın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yedeklemeleri/öğeleri/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yedeklemeleri/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yedeklemeleri/okuma | Yedekleme kümesini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yedeklemeleri/geri yükleme/eylem | Tüm birimleri bir yedekleme kümesinden geri yükleyin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupScheduleGroups/Delete | Yedekleme zamanlama gruplarını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupScheduleGroups/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupScheduleGroups/Read | Yedekleme zamanlaması gruplarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/backupScheduleGroups/Write | Yedekleme zamanlama gruplarını oluşturun veya güncelleştirin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Bölümayarları/silme | CHAP ayarlarını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Bölüsettings/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Bölüsettings/Read | CHAP ayarlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Bölüsettings/Write | CHAP ayarlarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/devre dışı bırakma/eylem | Bir cihazı devre dışı bırakır. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/silme | Cihazları siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/diskleri/okuma | Diskleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/indirme/eylem | Bir cihaz için yükleme güncelleştirmelerini yükleyin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yük devretme/eylem | Cihazın yük devretmesi. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yük devretme/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/failoverTargets/okuma | Cihazların yük devretme hedeflerini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/dosya sunucuları/yedekleme/eylem | Bir dosya sunucusunun yedeğini alın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/dosya sunucuları/silme | Dosya sunucularını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/ölçümler/okuma | Ölçümleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/dosya sunucuları/metricsDefinitions/okuma | Ölçüm tanımlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/dosya sunucuları/okuma | Dosya sunucularını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/paylaşımlar/Sil | Paylaşımları siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/paylaşımlar/ölçümler/okuma | Ölçümleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/fileservers/paylaşımlar/metricsDefinitions/okuma | Ölçüm tanımlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/paylaşımlar/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/paylaşımlar/okuma | Paylaşımları listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/paylaşımlar/yazma | Paylaşımlar oluşturun veya güncelleştirin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/fileservers/Write | Dosya sunucularını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/hardwareComponentGroups/changeControllerPowerState/Action | Donanım bileşen gruplarının denetleyici güç durumunu değiştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/hardwareComponentGroups/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/hardwareComponentGroups/Read | Donanım bileşen gruplarını listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Install/Action | Güncelleştirmeleri bir cihaza yükler. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/ınstallupdates/Action | Güncelleştirmeleri cihazlara (yalnızca 8000 seriler) yüklenir. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/yedekleme/eylem | Bir Iscsı sunucusunun yedeğini alın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/Sil | Iscsı sunucularını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/diskler/Sil | Diskleri siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/Ise/diskler/ölçümler/okuma | Ölçümleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/Ise/diskler/metricsDefinitions/okuma | Ölçüm tanımlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/Ise/diskler/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/diskler/okunan | Diskleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/diskler/yazma | Diskleri oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/Ise/ölçümler/okuma | Ölçümleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/nSite/metricsDefinitions/okuma | Ölçüm tanımlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/Ise/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/okuma | Iscsı sunucularını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/SCC/yazma | Iscsı sunucularını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/işleri/iptal/eylem | Çalışan bir işi iptal etme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/işleri/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/işleri/okuma | Işleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/listFailoverSets/Action | Mevcut bir cihaz için yük devretme kümelerini listeleyin (yalnızca 8000 serisi). |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/listFailoverTargets/eylem | Cihazların yük devretme hedeflerini listeleyin (yalnızca 8000 serisi). |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/ölçümleri/okuma | Ölçümleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/metricsDefinitions/okuma | Ölçüm tanımlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/confirmMigration/Action | Başarılı bir geçiş onaylar ve bunu işleyin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/migrationSourceConfigurations/confirmMigrationStatus/Read | Geçiş durumunu onaylayın listesini listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/fetchConfirmMigrationStatus/Action | Geçişin onaylama durumunu getirir. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/Fetchmigrationtahmine/eylem | Geçiş tahmini işinin durumunu getirin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/fetchMigrationStatus/Action | Geçişin durumunu getirin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/Import/Action | Geçiş için kaynak konfigürasyonları içeri aktarma |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/migrationSourceConfigurations/Migrationtahmin/okuma | Geçiş tahminini listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/migrationSourceConfigurations/migrationStatus/Read | Geçiş durumunu listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/migrationSourceConfigurations/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/Başlangıçgeçişi/eylem | Kaynak yapılandırmasını kullanarak geçişi Başlat |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/migrationSourceConfigurations/Startmigrationtahmine/eylem | Geçiş işleminin süresini tahmin etmek için bir iş başlatın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/networkSettings/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/networkSettings/Read | Ağ ayarlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/networkSettings/Write | Yeni bir ağ ayarları oluşturur veya güncelleştirir |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/publicEncryptionKey/Action | Cihaz yöneticisinin genel şifreleme anahtarını Listele |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazlar/publishSupportPackage/Action | Mevcut bir cihaz için destek paketini yayımlayın. StorSimple destek paketi, tüm StorSimple cihaz sorunlarını gidermeye yönelik Microsoft Desteği yardımcı olacak tüm ilgili günlükleri toplayan kullanımı kolay bir mekanizmadır. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/okuma | Cihazları listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/scanForUpdates/Action | Bir cihazdaki güncelleştirmeleri tarayın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/securitySettings/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/securitySettings/Read | Güvenlik ayarlarını listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/securitySettings/syncRemoteManagementCertificate/Action | Bir cihaz için uzaktan yönetim sertifikasını eşitler. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/securitySettings/Update/Action | Güvenlik ayarlarını güncelleştirin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/securitySettings/Write | Yeni bir güvenlik ayarları oluşturur veya güncelleştirir |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/sendTestAlertEmail/eylem | Yapılandırılmış e-posta alıcılarına test uyarı e-postası gönderin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/paylaşımları/okuma | Paylaşımları listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/timeSettings/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/timeSettings/Read | Zaman ayarlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/timeSettings/Write | Yeni veya güncelleştirme zaman ayarlarını oluşturur |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/güncelleştirmeler/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/updateSummary/Read | Güncelleştirme özetini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/silme | Var olan bir birim kapsayıcılarını siler (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/ölçümleri/okuma | Ölçümleri listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/metricsDefinitions/okuma | Ölçüm tanımlarını listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Volumecontainer/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/okuma | Birim kapsayıcılarını listeleme (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/birimleri/silme | Varolan birimleri siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/birimleri/ölçümleri/okuma | Ölçümleri listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/birimleri/metricsDefinitions/okuma | Ölçüm tanımlarını listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/birimleri/operationResults/Read | Işlem sonuçlarını listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/birimleri/okuma | Birimleri listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/birimleri/yazma | Yeni veya güncelleştirme birimleri oluşturur |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/Birimkapsayıcıları/yazma | Yeni veya güncelleştirme birimi kapsayıcıları oluşturur (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/birimleri/okuma | Birimleri listeleme |
> | Eylem | Microsoft. StorSimple/yöneticileri/cihazları/yazma | Cihazları oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/encryptionSettings/Read | Şifreleme ayarlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/Extendedınformation/Delete | Genişletilmiş kasa bilgilerini siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/Extendedınformation/Delete | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Eylem | Microsoft. StorSimple/yöneticileri/Extendedınformation/Read | Genişletilmiş kasa bilgilerini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/Extendedınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Eylem | Microsoft. StorSimple/yöneticileri/Extendedınformation/Write | Genişletilmiş kasa bilgilerini oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/Extendedınformation/Write | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Eylem | Microsoft. StorSimple/yöneticileri/Özellikler/okuma | Özellikleri listeleyin |
> | Eylem | Microsoft. StorSimple/yöneticileri/fileservers/okuma | Dosya sunucularını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/getEncryptionKey/Action | Cihaz Yöneticisi için şifreleme anahtarı alın. |
> | Eylem | Microsoft. StorSimple/yöneticileri/SCC/okunan | Iscsı sunucularını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/işleri/okuma | Işleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/listActivationKey/Action | StorSimple Aygıt Yöneticisi etkinleştirme anahtarını alır. |
> | Eylem | Microsoft. StorSimple/yöneticileri/listPublicEncryptionKey/Action | StorSimple Aygıt Yöneticisi ortak şifreleme anahtarlarını listeleyin. |
> | Eylem | Microsoft. StorSimple/yöneticileri/ölçümleri/okuma | Ölçümleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/metricsDefinitions/okuma | Ölçüm tanımlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/migrateClassicToResourceManager/Action | Klasik Kaynak Yöneticisi cihazları |
> | Eylem | Microsoft. StorSimple/yöneticileri/migrationSourceConfigurations/okuma | Geçiş kaynağı yapılandırmasını listeleme (yalnızca 8000 serisi) |
> | Eylem | Microsoft. StorSimple/yöneticileri/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/provisionCloudAppliance/eylem | Yeni bir bulut gereci oluşturun. |
> | Eylem | Microsoft. StorSimple/yöneticileri/okuma | Cihaz yöneticilerini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/regenerateActivationKey/Action | Mevcut bir StorSimple Aygıt Yöneticisi için etkinleştirme anahtarını yeniden oluşturun. |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageAccountCredentials/Delete | Depolama hesabı kimlik bilgilerini siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageAccountCredentials/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageAccountCredentials/Read | Depolama hesabı kimlik bilgilerini listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageAccountCredentials/Write | Depolama hesabı kimlik bilgilerini oluşturun veya güncelleştirin |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageDomains/Delete | Depolama etki alanlarını siler |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageDomains/operationResults/Read | Işlem sonuçlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageDomains/Read | Depolama alanlarını listeler veya alır |
> | Eylem | Microsoft. StorSimple/yöneticileri/storageDomains/Write | Depolama alanlarını oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/yazma | Cihaz yöneticilerini oluşturma veya güncelleştirme |
> | Eylem | Microsoft. StorSimple/yöneticileri/yazma | Kasa oluşturma işlemi, ' kasa ' türünde bir Azure kaynağı oluşturur |
> | Eylem | Microsoft. StorSimple/işlemler/okuma | Işlemleri listeler veya alır |
> | Eylem | Microsoft. StorSimple/Register/ACTION | Sağlayıcıyı kaydet Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. StreamAnalytics/konumlar/kotalar/okundu | Stream Analytics abonelik kotasını okuyun |
> | Eylem | Microsoft. StreamAnalytics/işlemler/okuma | Stream Analytics Işlemleri oku |
> | Eylem | Microsoft. StreamAnalytics/Register/ACTION | Aboneliği Stream Analytics kaynak sağlayıcısına Kaydet |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/Sil | Stream Analytics Işini Sil |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/işlevler/Sil | Stream Analytics Işi Işlevini Sil |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/işlevler/operationresults/Read | Stream Analytics Job Işlevi için işlem sonuçlarını oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/işlevler/okuma | Stream Analytics Işi Işlevini oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/işlevler/RetrieveDefaultDefinition/eylem | Stream Analytics Işi Işlevinin varsayılan tanımını alma |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/işlevler/test/eylem | Test Stream Analytics Işi Işlevi |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/işlevler/yazma | Stream Analytics Iş Işlevini yaz |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/giriş/silme | Stream Analytics Iş girişini sil |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/giriş/operationresults/Read | Stream Analytics Iş girişi için işlem sonuçlarını oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/giriş/okuma | Stream Analytics Iş girişini oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/girişler/örnek/eylem | Örnek Stream Analytics Işi girişi |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/girişler/test/eylem | Test Stream Analytics Işi girişi |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/giriş/yazma | Stream Analytics Işi girişi yazma |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/MetricDefinitions/okuma | Ölçüm tanımlarını oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics Iş için işlem sonuçlarını oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/çıkışlar/silme | Stream Analytics Iş çıkışını Sil |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/çıkışlar/operationresults/Read | Stream Analytics Iş çıktısı için işlem sonuçlarını oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/çıktılar/okuma | Stream Analytics Iş çıktısını oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/çıktılar/test/eylem | Test Stream Analytics Iş çıktısı |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/çıktılar/yazma | Stream Analytics Iş çıkışı yaz |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Read | Tanılama ayarını oku. |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/sağlayıcılar/Microsoft. Insights/diagnosticSettings/Write | Tanılama ayarı yaz. |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/sağlayıcılar/Microsoft. Insights/logDefinitions/Read | Streamingjobs için kullanılabilir günlükleri alır |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/sağlayıcılar/Microsoft. Insights/metricDefinitions/okuma | Streamingjobs için kullanılabilir ölçümleri alır |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/okuma | Stream Analytics Işi oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/Başlat/eylem | Stream Analytics Işi Başlat |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/durdur/eylem | Stream Analytics Işini durdur |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/dönüşümler/silme | Stream Analytics Işi dönüşümünü Sil |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/dönüşümler/okuma | Stream Analytics Işi dönüşümünü oku |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/dönüşümler/yazma | Stream Analytics Işi dönüşümünü yaz |
> | Eylem | Microsoft. StreamAnalytics/streamingjobs/yazma | Stream Analytics Işi yaz |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Subscription/Cancel/ACTION | Aboneliği iptal eder |
> | Eylem | Microsoft. Subscription/CreateSubscription/eylem | Azure aboneliği oluşturma |
> | Eylem | Microsoft. Subscription/Register/ACTION | Aboneliği Microsoft. Subscription kaynak sağlayıcısına kaydeder |
> | Eylem | Microsoft. abonelik/yeniden adlandırma/eylem | Aboneliği yeniden adlandırır |
> | Eylem | Microsoft. Subscription/SubscriptionDefinitions/okuma | Bir yönetim grubu içinde bir Azure abonelik tanımı alın. |
> | Eylem | Microsoft. Subscription/SubscriptionDefinitions/yaz | Azure abonelik tanımı oluşturma |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft.Support/register/action | Destek Kaynağı Sağlayıcısı'na kayıt yapar |
> | Eylem | Microsoft.Support/supportTickets/read | Durum, önem derecesi, kişi ayrıntıları ve iletişimler gibi Destek Biletleri ayrıntılarını alır veya aboneliklerdeki Destek Biletleri listesini alır. |
> | Eylem | Microsoft.Support/supportTickets/write | Destek Bileti oluşturur veya güncelleştirir. Teknik, Faturalama, Kotalar veya Abonelik Yönetimi konusunda bir Destek Bileti oluşturabilirsiniz. Var olan destek biletlerinin önem derecesini, iletişim bilgilerini ve iletişimlerini güncelleştirebilirsiniz. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. Timeseriesınsights

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/accesspolicies/Delete | Erişim ilkesini siler. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/accesspolicies/Read | Erişim ilkesinin özelliklerini alın. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/accesspolicies/Write | Bir ortam için yeni bir erişim ilkesi oluşturur veya var olan bir erişim ilkesini güncelleştirir. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/Sil | Ortamı siler. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/EventSources/Sil | Olay kaynağını siler. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/EventSources/okuma | Bir olay kaynağının özelliklerini alın. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/EventSources/yazma | Bir ortam için yeni bir olay kaynağı oluşturur veya var olan bir olay kaynağını güncelleştirir. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/okuma | Bir ortamın özelliklerini alın. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/referencedataset/Delete | Başvuru veri kümesini siler. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/referencedataset/Read | Başvuru veri kümesinin özelliklerini alın. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/referencedataset/Write | Bir ortam için yeni bir başvuru veri kümesi oluşturur veya var olan bir başvuru veri kümesini güncelleştirir. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/Status/Read | Ortam durumunu, ilişkili işlemlerinin durumunu alma gibi bir duruma alın. |
> | Eylem | Microsoft. Timeseriesınsights/ortamlar/Write | Yeni bir ortam oluşturur veya var olan bir ortamı güncelleştirir. |
> | Eylem | Microsoft. Timeseriesınsights/Register/ACTION | Time Series Insights kaynak sağlayıcısı için aboneliği kaydeder ve Time Series Insights ortamların oluşturulmasını sunar. |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. VisualStudio/Account/Delete | Hesabı Sil |
> | Eylem | Microsoft. VisualStudio/Account/EXTENSION/Read | Hesabı/uzantıyı oku |
> | Eylem | Microsoft. VisualStudio/Account/Project/Read | Hesabı/projeyi oku |
> | Eylem | Microsoft. VisualStudio/Account/Project/Write | Hesabı/projeyi ayarla |
> | Eylem | Microsoft. VisualStudio/Account/Read | Hesabı oku |
> | Eylem | Microsoft. VisualStudio/Account/Write | Hesabı ayarla |
> | Eylem | Microsoft. VisualStudio/Extension/Delete | Uzantıyı Sil |
> | Eylem | Microsoft. VisualStudio/Extension/Read | Uzantıyı oku |
> | Eylem | Microsoft. VisualStudio/Extension/Write | Uzantı ayarla |
> | Eylem | Microsoft. VisualStudio/Project/Delete | Projeyi Sil |
> | Eylem | Microsoft. VisualStudio/Project/Read | Projeyi oku |
> | Eylem | Microsoft. VisualStudio/Project/Write | Projeyi ayarla |
> | Eylem | Microsoft. VisualStudio/Register/ACTION | Azure aboneliğini Microsoft. VisualStudio sağlayıcısıyla kaydetme |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. Web/apimanagementaccounts/apiacl/okuma | API yönetim hesapları Apiacl 'Lerini alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/apiacl/Delete | API Management hesapları API 'Leri silme Apiacl 'leri. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/apiacl/okuma | API Management hesapları API 'Leri al Apiacl 'leri. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/apiacl/Write | API Management hesapları API 'Leri güncelleştirme Apiacl 'Leri. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/connectionacl/okuma | API Management hesapları API 'Leri Connectionacl 'Ler alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API 'ler/bağlantılar/confirmconsentcode/Action | Onay kodu API yönetim hesapları API 'Leri bağlantılarını onaylayın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacl/Delete | API Management hesaplarını silme API 'Leri bağlantı ACL 'leri. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacl/okuma | API yönetim hesapları API 'Leri bağlantı ACL 'lerini alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacl/yazma | API Management hesaplarını güncelleştirme API 'Leri bağlantı ACL 'leri. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/bağlantılar/silme | API Management hesapları API 'Leri bağlantılarını silin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Connections/getconsentlinks/Action | API Management hesapları API 'Leri bağlantıları için onay bağlantıları alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API 'ler/bağlantılar/listconnectionkeys/ACTION | Bağlantı anahtarları API yönetim hesapları API 'Leri bağlantılarını listeleyin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/bağlantılar/listgizlilikler/Action | Gizli API yönetim hesapları API 'Leri bağlantılarını listeleyin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API 'ler/bağlantılar/okuma | API Management hesapları API 'Leri bağlantıları alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API 'ler/bağlantılar/yazma | API Management hesapları API 'Leri bağlantılarını güncelleştirin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Delete | API Management hesapları API 'Lerini silin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/localizeddefinitions/Delete | API yönetim hesapları API 'Leri yerelleştirilmiş tanımlarını silin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/localizeddefinitions/oku | API yönetim hesapları API 'Leri yerelleştirilmiş tanımları alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/localizeddefinitions/yaz | API yönetim hesapları API 'Leri yerelleştirilmiş tanımları güncelleştirme. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Read | API Management hesapları API 'Lerini alın. |
> | Eylem | Microsoft. Web/apimanagementaccounts/API/Write | API Management hesapları API 'Lerini güncelleştirin. |
> | Eylem | Microsoft. Web/apimanagementaccounts/connectionacl/okuma | API Management hesapları Connectionacl 'Leri alın. |
> | Eylem | Microsoft. Web/availableyığınları/okuma | Kullanılabilir yığınları alın. |
> | Eylem | Microsoft. Web/sertifikalar/Sil | Var olan bir sertifikayı silin. |
> | Eylem | Microsoft. Web/sertifikalar/okuma | Sertifikaların listesini alın. |
> | Eylem | Microsoft. Web/sertifikalar/yazma | Yeni bir sertifika ekleyin veya var olan bir sertifikayı güncelleştirin. |
> | Eylem | Microsoft. Web/checknameavaılabılıty/Read | Kaynak adının kullanılabilir olup olmadığını denetleyin. |
> | Eylem | Microsoft. Web/classımobileservices/Read | Klasik Mobile Services alın. |
> | Eylem | Microsoft. Web/Connectiongateway/Delete | Bir bağlantı ağ geçidini siler. |
> | Eylem | Microsoft. Web/Connectiongateway/JOIN/Action | Bir bağlantı ağ geçidini birleştirir. |
> | Eylem | Microsoft. Web/Connectiongateway/ListStatus/Action | Bir bağlantı ağ geçidinin durumunu listeler. |
> | Eylem | Microsoft. Web/Connectiongateway/Move/Action | Bir bağlantı ağ geçidini gider. |
> | Eylem | Microsoft. Web/Connectiongateway/Read | Bağlantı ağ geçitlerinin listesini alın. |
> | Eylem | Microsoft. Web/Connectiongateway/Write | Bir bağlantı ağ geçidi oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Web/Connections/confirmconsentcode/Action | Bağlantı onay kodunu onaylayın. |
> | Eylem | Microsoft. Web/bağlantılar/silme | Bir bağlantıyı siler. |
> | Eylem | Microsoft. Web/Connections/JOIN/Action | Bir bağlantıyı birleştirir. |
> | Eylem | Microsoft. Web/Connections/listconsentlinks/Action | Bağlantılar için Izin bağlantılarını listeleyin. |
> | Eylem | Microsoft. Web/bağlantılar/taşıma/eylem | Bir bağlantıyı gider. |
> | Eylem | Microsoft. Web/Connections/Read | Bağlantıların listesini alın. |
> | Eylem | Microsoft. Web/Connections/Write | Bir bağlantı oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Web/Customapsıs/Delete | Özel bir API 'YI siler. |
> | Eylem | Microsoft. Web/Customapsıs/extractApiDefinitionFromWsdl/Action | Bir WSDL 'den API tanımını ayıklar. |
> | Eylem | Microsoft. Web/Customapsıs/JOIN/Action | Özel bir API 'ye katılır. |
> | Eylem | Microsoft. Web/Customapsıs/Listwsdlınterfaces/eylem | Özel bir API için WSDL arabirimlerini listeler. |
> | Eylem | Microsoft. Web/Customapsıs/taşıma/eylem | Özel bir API 'YI taşıın. |
> | Eylem | Microsoft. Web/Customapsıs/okuma | Özel API 'nin listesini alın. |
> | Eylem | Microsoft. Web/Customapsıs/yazma | Özel bir API oluşturur veya güncelleştirir. |
> | Eylem | Microsoft. Web/deletedSites/Read | Silinen bir Web uygulamasının özelliklerini al |
> | Eylem | Microsoft. Web/deploymentlocations/okuma | Dağıtım konumlarını al. |
> | Eylem | Microsoft. Web/coğrafi bölgeler/okuma | Coğrafi bölgelerin listesini alın. |
> | Eylem | Microsoft. Web/hostingenvironments/kapasiteleri/okuma | Barındırma ortamları kapasitelerine ulaşın. |
> | Eylem | Microsoft. Web/hostingEnvironments/Delete | App Service Ortamı silme |
> | Eylem | Microsoft. Web/hostingenvironments/detektorlar/okuma | Barındırma ortamları algılayıcıları alın. |
> | Eylem | Microsoft. Web/hostingenvironments/Diagnostic/Read | Barındırma ortamları tanılamayı alın. |
> | Eylem | Microsoft. Web/hostingenvironments/ınboundnetworkbağımlıcıesendpoints/okuma | Tüm gelen bağımlılıkların ağ uç noktalarını alın. |
> | Eylem | Microsoft. Web/hostingEnvironments/JOIN/Action | Bir App Service Ortamı birleştirir |
> | Eylem | Microsoft. Web/hostingenvironments/MetricDefinitions/okuma | Barındırma ortamları ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/hostingenvironments/multirolehavuzlar/MetricDefinitions/okuma | Barındırma ortamları çoklu rol havuzları ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/hostingenvironments/multirolepools/ölçümler/okuma | Barındırma ortamları çoklu rol havuzları ölçümlerini alın. |
> | Eylem | Microsoft. Web/hostingEnvironments/multiRolePools/okuma | Bir App Service Ortamı ön uç havuzunun özelliklerini al |
> | Eylem | Microsoft. Web/hostingenvironments/multirolepools/SKU/okuma | Barındırma ortamları çoklu rol havuzları SKU 'Ları alın. |
> | Eylem | Microsoft. Web/hostingenvironments/multirolepools/kullanımlar/okuma | Barındırma ortamları çoklu rol havuzları kullanımları alın. |
> | Eylem | Microsoft. Web/hostingEnvironments/Multirolehavuzlar/Write | App Service Ortamı yeni bir ön uç havuzu oluşturma veya var olanı güncelleştirme |
> | Eylem | Microsoft. Web/hostingenvironments/işlemler/okuma | Barındırma ortamları Işlemlerini alın. |
> | Eylem | Microsoft. Web/hostingenvironments/outboundnetworkbağımlıcıesendpoints/okuma | Tüm giden bağımlılıkların ağ uç noktalarını alın. |
> | Eylem | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/eylem | Özel uç nokta bağlantılarını Onayla |
> | Eylem | Microsoft. Web/hostingEnvironments/Read | App Service Ortamı özelliklerini al |
> | Eylem | Microsoft. Web/hostingEnvironments/reboot/eylem | App Service Ortamı tüm makineleri yeniden Başlat |
> | Eylem | Microsoft. Web/hostingenvironments/sürdürülecek/Action | Barındırma ortamlarını sürdürür. |
> | Eylem | Microsoft. Web/hostingenvironments/sunucugrupları/okuma | App Service planlarda barındırma ortamları alın. |
> | Eylem | Microsoft. Web/hostingenvironments/siteler/okuma | Barındırma ortamlarını Web Apps alın. |
> | Eylem | Microsoft. Web/hostingenvironments/beklet/eylem | Barındırma ortamlarını askıya alın. |
> | Eylem | Microsoft. Web/hostingenvironments/kullanımlar/okuma | Barındırma ortamları kullanımları alın. |
> | Eylem | Microsoft. Web/hostingenvironments/workerpools/MetricDefinitions/Read | Barındırma ortamları Workerpools ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/hostingenvironments/workerpools/ölçümler/okuma | Barındırma ortamları için Workerpools ölçümlerini alın. |
> | Eylem | Microsoft. Web/hostingEnvironments/workerPools/Read | Bir App Service Ortamı çalışan havuzunun özelliklerini al |
> | Eylem | Microsoft. Web/hostingenvironments/workerpools/SKU/okuma | Barındırma ortamları Workerpools SKU 'Larını alın. |
> | Eylem | Microsoft. Web/hostingenvironments/workerpools/kullanımlar/Read | Çalışan ortamlarının kullanım havuzları kullanımlarını alın. |
> | Eylem | Microsoft. Web/hostingEnvironments/workerPools/Write | App Service Ortamı yeni bir çalışan havuzu oluşturma veya var olan bir çalışanı güncelleştirme |
> | Eylem | Microsoft. Web/hostingEnvironments/Write | Yeni bir App Service Ortamı oluşturma veya var olanı güncelleştirme |
> | Eylem | Microsoft. Web/ıhostingenvironmentnameavailable/Read | Barındırma ortamı adının kullanılabilir olup olmadığını öğrenin. |
> | Eylem | Microsoft. Web/ıhostnameavailable/Read | Ana bilgisayar adının kullanılabilir olup olmadığını denetleyin. |
> | Eylem | Microsoft. Web/ıusernameavailable/Read | Kullanıcı adının kullanılabilir olup olmadığını denetleyin. |
> | Eylem | Microsoft. Web/listSitesAssignedToHostName/Read | Ana bilgisayar adına atanan sitelerin adlarını alın. |
> | Eylem | Microsoft. Web/konumlar/apioperations/Read | Konumları al API Işlemleri. |
> | Eylem | Microsoft. Web/konumlar/connectiongatewaykurulumları/okuma | Konumları al bağlantı ağ geçidi yüklemeleri. |
> | Eylem | Microsoft. Web/konumlar/Deletevirtualnetworkoralt ağları/eylem | Konumlar için VNET veya alt ağ silme bildirimi. |
> | Eylem | Microsoft. Web/konumlar/extractapidefinitionfromwsdl/Action | Konumlar için WSDL 'den API tanımını ayıklayın. |
> | Eylem | Microsoft. Web/Locations/listwsdlınterfaces/Action | Konumlar için WSDL arabirimlerini listeleyin. |
> | Eylem | Microsoft. Web/konumlar/managedap/apioperations/Read | Konum yönetilen API Işlemlerini alın. |
> | Eylem | Microsoft. Web/Locations/managedap/JOIN/Action | Yönetilen bir API 'ye katılır. |
> | Eylem | Microsoft. Web/konumlar/managedap/okuma | Konumlara yönetilen API 'Ler alın. |
> | Eylem | Microsoft. Web/konumlar/operationResults/Read | Işlemleri al. |
> | Eylem | Microsoft. Web/konumlar/işlemler/okuma | Işlemleri al. |
> | Eylem | Microsoft. Web/işlemler/okuma | Işlemleri al. |
> | Eylem | Microsoft. Web/publishingusers/Read | Kullanıcıları yayımlamayı öğrenin. |
> | Eylem | Microsoft. Web/publishingusers/Write | Yayımlama kullanıcılarını güncelleştir. |
> | Eylem | Microsoft. Web/öneriler/okuma | Abonelikler için önerilerin listesini alın. |
> | Eylem | Microsoft. Web/Register/ACTION | Abonelik için Microsoft. Web kaynak sağlayıcısını kaydedin. |
> | Eylem | Microsoft. Web/resourcehealthmetadata/Read | Kaynak Durumu meta verilerini alın. |
> | Eylem | Microsoft. Web/sunucugrupları/özellikleri/okuma | App Service planları yeteneklerini alın. |
> | Eylem | Microsoft. Web/sunucugrupları/silme | Mevcut bir App Service planını Sil |
> | Eylem | Microsoft. Web/sunucugrupları/eventGridFilters/Delete | Sunucu grubunda Event Grid filtresini silin. |
> | Eylem | Microsoft. Web/sunucugrupları/eventGridFilters/Read | Sunucu grubunda Event Grid filtresi al. |
> | Eylem | Microsoft. Web/sunucugrupları/eventGridFilters/Write | Event Grid filtresini sunucu grubuna yerleştirin. |
> | Eylem | Microsoft. Web/sunucugrupları/firstpartyapps/Settings/Delete | App Service planları birinci taraf uygulama ayarlarını silin. |
> | Eylem | Microsoft. Web/sunucugrupları/firstpartyapps/Settings/Read | App Service planlarını Ilk taraf uygulama ayarlarına alın. |
> | Eylem | Microsoft. Web/sunucugrupları/firstpartyapps/Settings/Write | Güncelleştirme App Service planları birinci taraf uygulama ayarları. |
> | Eylem | Microsoft. Web/sunucugrupları/hybridconnectionnamespaces/geçişler/Sil | Karma bağlantı ad alanları geçişlerinin App Service planlarını silin. |
> | Eylem | Microsoft. Web/sunucugrupları/hybridconnectionnamespaces/geçişler/okuma | App Service planları karma bağlantı ad alanları geçişleri alın. |
> | Eylem | Microsoft. Web/sunucugrupları/hybridconnectionnamespaces/geçişler/siteler/okuma | App Service planlarını karma bağlantı ad alanları geçişleri Web Apps alın. |
> | Eylem | Microsoft. Web/sunucugrupları/hybridconnectionplanlimit/Read | App Service planlarını karma bağlantı planı sınırlarına alın. |
> | Eylem | Microsoft. Web/sunucugrupları/hybridconnectiongeçişleri/okuma | App Service planları karma bağlantı geçişleri alın. |
> | Eylem | Microsoft. Web/sunucugrupları/MetricDefinitions/okuma | App Service planları ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/sunucugrupları/ölçümleri/okuma | App Service plan ölçümleri alın. |
> | Eylem | Microsoft. Web/sunucugrupları/operationresults/Read | App Service planı Işlem sonuçlarını alın. |
> | Eylem | Microsoft. Web/sunucugrupları/okuma | App Service planında özellikleri al |
> | Eylem | Microsoft. Web/sunucugrupları/restartSites/Action | Bir App Service planındaki tüm Web Apps yeniden Başlat |
> | Eylem | Microsoft. Web/sunucugrupları/siteleri/okuma | App Service planlarını Web Apps alın. |
> | Eylem | Microsoft. Web/sunucugrupları/SKU 'lar/okuma | App Service planları SKU 'Larını alın. |
> | Eylem | Microsoft. Web/sunucugrupları/kullanımlar/okuma | App Service planları kullanımları alın. |
> | Eylem | Microsoft. Web/sunucugrupları/virtualnetworkconnections/Gateway/Write | Sanal ağ bağlantısı ağ geçitlerini güncelleştirme App Service planlar. |
> | Eylem | Microsoft. Web/sunucugrupları/virtualnetworkconnections/Read | Sanal ağ bağlantılarını App Service planları alın. |
> | Eylem | Microsoft. Web/sunucugrupları/virtualnetworkconnections/rotalar/Delete | App Service planları sanal ağ bağlantıları yollarını silin. |
> | Eylem | Microsoft. Web/sunucugrupları/virtualnetworkconnections/rotalar/Read | App Service planları sanal ağ bağlantıları yollarını alın. |
> | Eylem | Microsoft. Web/sunucugrupları/virtualnetworkconnections/rotalar/Write | Sanal ağ bağlantı yollarını güncelleştirme App Service planlar. |
> | Eylem | Microsoft. Web/sunucugrupları/çalışanlar/yeniden başlatma/eylem | App Service planları çalışanları 'nı yeniden başlatın. |
> | Eylem | Microsoft. Web/sunucugrupları/yazma | Yeni bir App Service planı oluşturma veya var olanı güncelleştirme |
> | Eylem | Microsoft. Web/Sites/çözümleyiciler ecustomhostname/Read | Özel ana bilgisayar adını çözümleyin. |
> | Eylem | Microsoft. Web/Sites/applySlotConfig/Action | Hedef yuvadan geçerli Web uygulamasına Web uygulaması yuva yapılandırması Uygula |
> | Eylem | Microsoft. Web/siteler/yedekleme/eylem | Yeni bir Web uygulaması yedeklemesi oluşturun |
> | Eylem | Microsoft. Web/siteler/yedekleme/okuma | Web Apps yedeklemesini alın. |
> | Eylem | Microsoft. Web/siteler/yedekleme/yazma | Web Apps Yedeklemeyi güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yedeklemeler/eylem | Azure Storage 'daki bir bloba geri yüklenebilir var olan bir uygulama yedeklemesini bulur. |
> | Eylem | Microsoft. Web/siteler/yedeklemeler/Sil | Web Apps yedeklemeleri silin. |
> | Eylem | Microsoft. Web/siteler/yedeklemeler/liste/eylem | Web Apps yedeklemeleri listeleyin. |
> | Eylem | Microsoft. Web/siteler/yedeklemeler/okuma | Bir Web uygulamasının yedeğinin özelliklerini al |
> | Eylem | Microsoft. Web/siteler/yedeklemeler/geri yükleme/eylem | Web Apps yedeklemeleri geri yükleyin. |
> | Eylem | Microsoft. Web/siteler/yedeklemeler/yazma | Web Apps yedeklemeleri güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yapılandırma/silme | Web Apps yapılandırmasını silin. |
> | Eylem | Microsoft. Web/siteler/yapılandırma/liste/eylem | Web uygulamasının, yayımlama kimlik bilgileri, uygulama ayarları ve bağlantı dizeleri gibi güvenlikle hassas ayarlarını listeleyin |
> | Eylem | Microsoft. Web/siteler/yapılandırma/okuma | Web uygulaması yapılandırma ayarlarını al |
> | Eylem | Microsoft. Web/siteler/yapılandırma/anlık görüntüler/okuma | Web Apps config anlık görüntülerini alın. |
> | Eylem | Microsoft. Web/siteler/yapılandırma/yazma | Web uygulamasının yapılandırma ayarlarını Güncelleştir |
> | Eylem | Microsoft. Web/Sites/containerlogs/Action | Web uygulaması için daraltılmış kapsayıcı günlüklerini al. |
> | Eylem | Microsoft. Web/Sites/containerlogs/Download/Action | Web Apps kapsayıcı günlüklerini indirin. |
> | Eylem | Microsoft. Web/Sites/continuouswebjobs/Delete | Web Apps sürekli Web Işlerini silin. |
> | Eylem | Microsoft. Web/Sites/continuouswebjobs/Read | Sürekli Web Işleri Web Apps alın. |
> | Eylem | Microsoft. Web/Sites/continuouswebjobs/start/Action | Sürekli Web Işleri Web Apps başlatın. |
> | Eylem | Microsoft. Web/Sites/continuouswebjobs/durdur/Action | Sürekli Web Işlerini Web Apps durdurun. |
> | Eylem | Microsoft. Web/Sites/Delete | Var olan bir Web uygulamasını silme |
> | Eylem | Microsoft. Web/siteler/dağıtımlar/Sil | Web Apps dağıtımlarını silin. |
> | Eylem | Microsoft. Web/siteler/dağıtımlar/günlük/okuma | Web Apps dağıtım günlüğünü alın. |
> | Eylem | Microsoft. Web/siteler/dağıtımlar/okuma | Web Apps dağıtımlarını alın. |
> | Eylem | Microsoft. Web/siteler/dağıtımlar/yazma | Web Apps dağıtımlarını güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/detektorler/okuma | Web Apps detektorları alın. |
> | Eylem | Microsoft. Web/siteler/tanılama/çözümlemeler/yürütme/eylem | Web Apps tanılama analizini çalıştırın. |
> | Eylem | Microsoft. Web/siteler/Tanılamalar/çözümlemeler/okuma | Web Apps tanılama analizini alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/aspnetcore/Read | ASP.NET Core uygulama için Web Apps tanılamayı alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/oto Heal/Read | Web Apps tanılama oto Heal 'ı alın. |
> | Eylem | Microsoft. Web/siteler/tanılama/dağıtım/okuma | Web Apps tanılama dağıtımı alın. |
> | Eylem | Microsoft. Web/siteler/Tanılamalar/dağıtımlar/okuma | Web Apps tanılama dağıtımlarını alın. |
> | Eylem | Microsoft. Web/siteler/tanılama/algılayıcıları/yürütme/eylem | Web Apps tanılama algılayıcısı 'nı çalıştırın. |
> | Eylem | Microsoft. Web/siteler/Tanılamalar/algılayıcıları/okuma | Web Apps tanılama algılayıcısı alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/failedrequestsperuri/Read | Uri başına başarısız Web Apps tanılama Isteği Isteklerini alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/frebanalysis/Read | Web Apps tanılama FREB analizini alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/loganalyzer/Read | Web Apps tanılama günlüğü Çözümleyicisi 'ni alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/Read | Web Apps tanılama kategorilerini alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/runtimeavaılabılıty/Read | Web Apps tanılama çalışma zamanı kullanılabilirliği alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/servicehealth/Read | Web Apps tanılama hizmeti sistem durumunu alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/sitecpuanalysis/Read | Web Apps tanılama sitesi CPU analizini alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/sitecrash/Read | Web Apps tanılama sitesi kilitlenmelerini alın. |
> | Eylem | Microsoft. Web/siteler/tanılama/sitegecikmesi/okuma | Tanılama sitesi gecikmesini Web Apps alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/sitememoryanalysis/Read | Web Apps tanılama sitesi bellek analizini alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/sıterestartsettingupdate/Read | Web Apps tanılama sitesini yeniden başlatma ayarı güncelleştirmesi. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/sıterestartuserbaşlatılmış/Read | Web Apps tanılama sitesini yeniden başlatma Kullanıcı tarafından başlatıldı. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/siteswap/Read | Web Apps tanılama sitesini değiştirme 'yi alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/ThreadCount/Read | Web Apps tanılama Iş parçacığı sayısı alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostics/workeravaılabılıty/Read | Web Apps tanılama Workeravaılabılıty 'i alın. |
> | Eylem | Microsoft. Web/Sites/Diagnostic/workerprocessrecycle/Read | Web Apps tanılama çalışan Işlemi geri dönüşümü alın. |
> | Eylem | Microsoft. Web/Sites/domainownershiptanýmlayýcýlarý/Read | Web Apps etki alanı sahiplik tanımlayıcıları alın. |
> | Eylem | Microsoft. Web/Sites/domainownershiptanýmlayýcýlarý/Write | Web Apps etki alanı sahiplik tanımlayıcılarını güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/eventGridFilters/Delete | Web uygulamasındaki Event Grid filtresini silin. |
> | Eylem | Microsoft. Web/Sites/Eventgridfilter/Read | Web App üzerinde Event Grid filtresi alın. |
> | Eylem | Microsoft. Web/Sites/Eventgridfilter/Write | Event Grid filtresini Web uygulamasına koyun. |
> | Eylem | Microsoft. Web/siteler/uzantılar/silme | Web Apps site uzantılarını silin. |
> | Eylem | Microsoft. Web/Sites/Extensions/Read | Web Apps site uzantıları alın. |
> | Eylem | Microsoft. Web/Sites/Extensions/Write | Web Apps site uzantılarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/işlevler/eylem | İşlevler Web Apps. |
> | Eylem | Microsoft. Web/siteler/işlevler/Sil | Web Apps Işlevlerini silin. |
> | Eylem | Microsoft. Web/siteler/işlevler/anahtarlar/Sil | Işlev anahtarlarını silin. |
> | Eylem | Microsoft. Web/siteler/işlevler/anahtarlar/yazma | Işlev anahtarlarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/işlevler/ListKeys/Action | Işlev anahtarlarını listeleyin. |
> | Eylem | Microsoft. Web/Sites/Functions/listgizlilikler/Action | Işlev gizli dizileri listeleyin. |
> | Eylem | Microsoft. Web/siteler/işlevler/MasterKey/Read | Web Apps Işlevlerini MasterKey alın. |
> | Eylem | Microsoft. Web/siteler/işlevler/Özellikler/okuma | Web Apps Işlevleri özelliklerini okuyun. |
> | Eylem | Microsoft. Web/siteler/işlevler/özellikler/yazma | Web Apps Işlevleri özelliklerini güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/işlevler/okuma | Web Apps Işlevleri alın. |
> | Eylem | Microsoft. Web/siteler/işlevler/belirteç/okuma | Web Apps Işlevleri belirteci alın. |
> | Eylem | Microsoft. Web/siteler/işlevler/yazma | Web Apps Işlevlerini güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/ana bilgisayar/işlev anahtarları/silme | Işlevler ana bilgisayar Işlev anahtarlarını silin. |
> | Eylem | Microsoft. Web/Sites/Host/functionkeys/Write | Güncelleştirme Işlevleri konak Işlev anahtarları. |
> | Eylem | Microsoft. Web/siteler/ana bilgisayar/ListKeys/ACTION | Işlevlerin ana bilgisayar anahtarlarını listeleyin. |
> | Eylem | Microsoft. Web/Sites/Host/listsyncstatus/Action | Eşitleme Işlevinin durum tetiklemesini listele. |
> | Eylem | Microsoft. Web/siteler/konak/Özellikler/okuma | Web Apps Işlevleri konak özelliklerini okuyun. |
> | Eylem | Microsoft. Web/siteler/ana bilgisayar/eşitleme/eylem | Işlev tetikleyicilerini Eşitle. |
> | Eylem | Microsoft. Web/siteler/ana bilgisayar/sistem anahtarları/silme | Işlev sistem anahtarlarını siler. |
> | Eylem | Microsoft. Web/siteler/ana bilgisayar/sistem anahtarları/yazma | Güncelleştirme Işlevleri sistem anahtarlarını barındırır. |
> | Eylem | Microsoft. Web/Sites/hostnamebindings/Delete | Web Apps konak adı bağlamalarını silin. |
> | Eylem | Microsoft. Web/Sites/hostnamebindings/Read | Web Apps konak adı bağlamaları alın. |
> | Eylem | Microsoft. Web/Sites/hostnamebindings/Write | Web Apps konak adı bağlamalarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/hostruntime/işlevler/anahtarlar/okuma | Web Apps Hostruntime Işlevleri anahtarlarını alın. |
> | Eylem | Microsoft. Web/Sites/hostruntime/Host/_master/Read | Yönetici işlemleri için İşlev Uygulaması ana anahtarını al |
> | Eylem | Microsoft. Web/Sites/hostruntime/Host/Action | Eşitleme Tetikleyicileri, işlevler eklemek, işlevleri çağırmak, işlevleri silmek gibi İşlev Uygulaması çalışma zamanı eylemi gerçekleştirin. |
> | Eylem | Microsoft. Web/Sites/hostruntime/Host/Read | Web Apps Hostruntime konağını alın. |
> | Eylem | Microsoft. Web/Sites/hybridconnection/Delete | Karma bağlantıyı Web Apps silin. |
> | Eylem | Microsoft. Web/Sites/hybridconnection/Read | Karma Bağlantı Web Apps alın. |
> | Eylem | Microsoft. Web/Sites/hybridconnection/Write | Karma bağlantıyı Web Apps güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/hybridconnectionnamespaces/geçişler/Sil | Karma bağlantı ad alanları geçişleri Web Apps silin. |
> | Eylem | Microsoft. Web/Sites/hybridconnectionnamespaces/geçişler/ListKeys/Action | Karma bağlantı ad alanları geçişleri Web Apps anahtarları listeleyin. |
> | Eylem | Microsoft. Web/Sites/hybridconnectionnamespaces/geçişler/okundu | Karma bağlantı ad alanları geçişleri Web Apps alın. |
> | Eylem | Microsoft. Web/Sites/hybridconnectionnamespaces/geçişler/yaz | Karma bağlantı ad alanları geçişlerinin güncelleştirme Web Apps. |
> | Eylem | Microsoft. Web/Sites/hybridconnectiongeçişleri/okuma | Karma bağlantı geçişleri Web Apps alın. |
> | Eylem | Microsoft. Web/siteler/örnekler/dağıtımlar/Sil | Web Apps örnekleri dağıtımlarını silin. |
> | Eylem | Microsoft. Web/siteler/örnekler/dağıtımlar/okuma | Web Apps örnekleri dağıtımlarını alın. |
> | Eylem | Microsoft. Web/Sites/Instances/Extensions/log/Read | Web Apps örnekleri uzantıları günlüğünü alın. |
> | Eylem | Microsoft. Web/Sites/Instances/Extensions/süreçler/okuma | Web Apps örnekleri uzantı süreçlerini alın. |
> | Eylem | Microsoft. Web/Sites/Instances/Extensions/Read | Web Apps örnekleri uzantılarını alın. |
> | Eylem | Microsoft. Web/siteler/örnekler/süreçler/Sil | Web Apps örnekleri Işlemini silin. |
> | Eylem | Microsoft. Web/siteler/örnekler/süreçler/modüller/okuma | Web Apps örnekleri, modülleri Işler. |
> | Eylem | Microsoft. Web/siteler/örnekler/süreçler/okuma | Web Apps örnekleri Işlemini alın. |
> | Eylem | Microsoft. Web/siteler/örnekler/süreçler/iş parçacıkları/okuma | Web Apps örnekleri Iş parçacıklarını Işler. |
> | Eylem | Microsoft. Web/Sites/Instances/Read | Web Apps örnekleri alın. |
> | Eylem | Microsoft. Web/Sites/listsyncfunctiontriggerstatus/Action | Eşitleme Işlevi tetikleme durumunu listeleyin. |
> | Eylem | Microsoft. Web/Sites/MetricDefinitions/Read | Web Apps ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/siteler/ölçümler/okuma | Web Apps ölçümleri alın. |
> | Eylem | Microsoft. Web/Sites/metricsdefinitions/okuma | Web Apps ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/Sites/migratemysql/Action | MySql Web Apps geçirin. |
> | Eylem | Microsoft. Web/Sites/migratemysql/Read | MySql Web Apps geçirin. |
> | Eylem | Microsoft. Web/Sites/networktrace/Action | Ağ Izleme Web Apps. |
> | Eylem | Microsoft. Web/Sites/networkizlemeler/operationresults/Read | Web Apps ağ Izleme Işlemi sonuçlarını alın. |
> | Eylem | Microsoft. Web/Sites/YeniParola/Action | YeniParola Web Apps. |
> | Eylem | Microsoft. Web/Sites/operationresults/Read | Web Apps Işlem sonuçlarını alın. |
> | Eylem | Microsoft. Web/Sites/işlemler/okuma | Web Apps Işlemleri alın. |
> | Eylem | Microsoft. Web/Sites/PerfCounters/okuma | Web Apps performans sayaçlarını alın. |
> | Eylem | Microsoft. Web/Sites/premieraddons/Delete | Web Apps Premier Eklentiler öğesini silin. |
> | Eylem | Microsoft. Web/Sites/premieraddons/Read | Web Apps Premier eklentilerden yararlanın. |
> | Eylem | Microsoft. Web/Sites/premieraddons/Write | Web Apps Premier eklentilerini güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/privateaccess/Read | Siteye erişebilen özel site erişimi etkinleştirme ve yetkilendirilmiş sanal ağlarla ilgili veri alın. |
> | Eylem | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/eylem | Özel uç nokta bağlantılarını Onayla |
> | Eylem | Microsoft. Web/siteler/süreçler/modüller/okuma | Web Apps Işleme modüllerini alın. |
> | Eylem | Microsoft. Web/siteler/süreçler/okuma | Web Apps süreçlerini alın. |
> | Eylem | Microsoft. Web/siteler/süreçler/iş parçacıkları/okuma | Web Apps Işleme Iş parçacıklarını alın. |
> | Eylem | Microsoft. Web/Sites/publiccertificates/Delete | Web Apps ortak sertifikaları silme. |
> | Eylem | Microsoft. Web/Sites/publiccertificates/Read | Web Apps ortak sertifikaları alın. |
> | Eylem | Microsoft. Web/Sites/publiccertificates/Write | Ortak sertifikaları güncelleştirme Web Apps. |
> | Eylem | Microsoft. Web/siteler/Yayımla/eylem | Web uygulaması yayımlama |
> | Eylem | Microsoft. Web/Sites/publishxml/Action | Bir Web uygulaması için Yayımlama profili xml 'i al |
> | Eylem | Microsoft. Web/Sites/publishxml/Read | Yayımlama XML 'i Web Apps alın. |
> | Eylem | Microsoft. Web/Sites/Read | Bir Web uygulamasının özelliklerini al |
> | Eylem | Microsoft. Web/Sites/recommendationhistory/Read | Web Apps öneri geçmişini alın. |
> | Eylem | Microsoft. Web/siteler/öneriler/devre dışı bırakma/eylem | Web Apps önerilerini devre dışı bırakın. |
> | Eylem | Microsoft. Web/siteler/öneriler/okuma | Web uygulaması için önerilerin listesini alın. |
> | Eylem | Microsoft. Web/siteler/kurtar/eylem | Web Apps kurtarın. |
> | Eylem | Microsoft. Web/Sites/resetSlotConfig/Action | Web uygulaması yapılandırmasını sıfırlama |
> | Eylem | Microsoft. Web/Sites/resourcehealthmetadata/Read | Web Apps Kaynak Durumu meta verilerini alın. |
> | Eylem | Microsoft. Web/siteler/yeniden Başlat/eylem | Bir Web uygulamasını yeniden başlatma |
> | Eylem | Microsoft. Web/siteler/geri yükleme/okuma | Web Apps geri yüklemeyi alın. |
> | Eylem | Microsoft. Web/siteler/geri yükleme/yazma | Web Apps geri yükleyin. |
> | Eylem | Microsoft. Web/Sites/restorefrombackupblob/Action | Web uygulamasını yedekleme Blobundan geri yükleyin. |
> | Eylem | Microsoft. Web/Sites/restorefromdeletedapp/eylem | Web Apps silinen uygulamadan geri yükleyin. |
> | Eylem | Microsoft. Web/siteler/restoresnapshot/eylem | Web Apps anlık görüntülerini geri yükleyin. |
> | Eylem | Microsoft. Web/Sites/siteextensions/Sil | Web Apps site uzantılarını silin. |
> | Eylem | Microsoft. Web/Sites/siteextensions/okuma | Web Apps site uzantıları alın. |
> | Eylem | Microsoft. Web/Sites/siteextensions/Write | Web Apps site uzantılarını güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/çözümleyiciler ecustomhostname/Read | Web Apps yuvaları özel ana bilgisayar adını analiz edin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/applySlotConfig/Action | Hedef yuvadan geçerli yuvaya Web uygulaması yuva yapılandırması uygulayın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedekleme/eylem | Yeni Web uygulaması yuvası yedeklemesi oluşturun. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedekleme/okuma | Web Apps yuvaları yedeklemesini alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedekleme/yazma | Web Apps yuvaları yedeklemesini güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedeklemeler/eylem | Web Apps yuvaları yedeklemelerini bulun. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedeklemeler/silme | Web Apps yuvaları yedeklemelerini silin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedeklemeler/liste/eylem | Web Apps yuvaları yedeklemelerini listeleyin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedeklemeler/okuma | Web uygulaması yuvaları yedeklemesinin özelliklerini al |
> | Eylem | Microsoft. Web/siteler/yuvalar/yedeklemeler/geri yükleme/eylem | Web Apps yuvaları yedeklemelerini geri yükleyin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yapılandırma/silme | Web Apps yuvaları yapılandırmasını silin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yapılandırma/listeleme/eylem | Web uygulaması yuvasının kimlik bilgileri, uygulama ayarları ve bağlantı dizeleri gibi güvenlik duyarlı ayarlarını listeleyin |
> | Eylem | Microsoft. Web/siteler/yuvalar/yapılandırma/okuma | Web uygulaması yuvasının yapılandırma ayarlarını al |
> | Eylem | Microsoft. Web/siteler/yuvalar/yapılandırma/yazma | Web uygulaması yuvasının yapılandırma ayarlarını Güncelleştir |
> | Eylem | Microsoft. Web/Sites/yuvaları/containerlogs/Action | Web uygulaması yuvası için daraltılmış kapsayıcı günlüklerini al. |
> | Eylem | Microsoft. Web/Sites/yuvaları/containerlogs/Download/Action | Web Apps yuvaları kapsayıcı günlüklerini indirin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/continuouswebjobs/Delete | Web Apps yuvaları sürekli Web Işlerini silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/continuouswebjobs/Read | Web Apps yuvaları sürekli Web Işleri alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/continuouswebjobs/start/Action | Web Apps yuvaları sürekli Web Işleri başlatın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/continuouswebjobs/durdur/eylem | Web Apps yuvaları sürekli Web Işlerini durdurun. |
> | Eylem | Microsoft. Web/siteler/yuvalar/silme | Var olan bir Web uygulaması yuvasını Sil |
> | Eylem | Microsoft. Web/siteler/yuvalar/dağıtımlar/silme | Web Apps yuvaları dağıtımlarını silin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/dağıtımlar/günlük/okuma | Web Apps yuvaları dağıtım günlüğünü alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/dağıtımlar/okuma | Web Apps yuvaları dağıtımlarını alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/dağıtımlar/yazma | Web Apps yuvaları dağıtımlarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/detektorler/okuma | Web Apps yuvaları algılayıcıları alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/Tanılamalar/çözümlemeler/yürütme/eylem | Web Apps yuvaları tanılama analizini çalıştırın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/Tanılamalar/çözümlemeler/okuma | Web Apps yuvaları tanılama analizini alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/aspnetcore/Read | ASP.NET Core uygulama için Web Apps yuvaları tanılamayı alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/oto Heal/okuma | Web Apps yuvaları tanılama, oto Heal al. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/dağıtım/okuma | Web Apps yuvaları tanılama dağıtımı alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/Tanılamalar/dağıtımlar/okuma | Web Apps yuvaları tanılama dağıtımlarını alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/detektorları/yürütme/eylem | Web Apps yuvası tanılama algılayıcısı 'nı çalıştırın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/Tanılamalar/detektorler/okuma | Web Apps yuvaları tanılama algılayıcısı alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/frebanalysis/okuma | Web Apps yuvaları tanılama FREB analizini alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/loganalyzer/Read | Web Apps yuvaları tanılama günlüğü Çözümleyicisi 'ni alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/okuma | Web Apps yuvaları tanılamayı alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/runtimeavaılabılıty/Read | Web Apps yuvaları tanılama çalışma zamanı kullanılabilirliği alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/servicehealth/Read | Web Apps yuvaları tanılama hizmeti durumu alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/sitecpuanalysis/Read | Web Apps yuvaları tanılama sitesi CPU analizini alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/sitekilitleniyor/okuma | Web Apps yuvaları tanılama site kilitlenmelerini alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/sitegecikmesi/okuma | Web Apps yuvaları tanılama sitesi gecikmesi alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/tanılama/sitememoryanalysis/okuma | Web Apps yuvaları tanılama site belleği Analizi alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/Diagnostics/sıterestartsettingupdate/Read | Web Apps yuvaları tanılama sitesini yeniden Başlat ayarı güncelleştirmesi. |
> | Eylem | Microsoft. Web/Sites/yuvaları/Diagnostics/sıterestartuserbaşlatılmış/Read | Web Apps yuvaları tanılama sitesini yeniden başlatma Kullanıcı tarafından başlatıldı. |
> | Eylem | Microsoft. Web/Sites/yuvaları/Tanılamalar/siteswap/Read | Web Apps yuvaları tanılama sitesi değiştirme 'yi alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/ThreadCount/Read | Web Apps yuvaları tanılama Iş parçacığı sayısı alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/workeravaılabılıty/Read | Web Apps yuvaları tanılama Workeravaılabılıty 'i alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/tanılama/workerprocessrecycle/Read | Web Apps yuvaları tanılama çalışan Işlemi geri dönüşümü alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/domainownershiptanýmlayýcýlarý/Read | Web Apps yuvaları etki alanı sahiplik tanımlayıcıları alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/işlevler/listgizlilikler/Action | Gizli dizi Web Apps yuvaları Işlevlerini listeleyin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/işlevleri/okuma | Web Apps yuvası Işlevleri alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hostnamebindings/Delete | Web Apps yuvaları konak adı bağlamalarını silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hostnamebindings/Read | Web Apps yuvası konak adı bağlamaları alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hostnamebindings/Write | Web Apps yuvaları konak adı bağlamalarını güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hybridconnection/Delete | Web Apps yuvaların karma bağlantısını silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hybridconnection/Read | Web Apps yuvası karma bağlantı alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hybridconnection/Write | Web Apps yuvaların karma bağlantısını güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hybridconnectionnamespaces/geçişler/Sil | Web Apps yuvaları karma bağlantı ad alanları geçişleri silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hybridconnectionnamespaces/geçişler/yaz | Web Apps yuvaları karma bağlantı ad alanları geçişleri güncelleştirme. |
> | Eylem | Microsoft. Web/Sites/yuvaları/hybridconnectiongeçişleri/okuma | Web Apps yuvaları karma bağlantı geçişleri alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/örnekler/dağıtımlar/okuma | Web Apps yuva örnekleri dağıtımlarını alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/örnek/işlem/silme | Web Apps yuvaları örnek süreçlerini silin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/örnekler/süreçler/okuma | Web Apps yuvası örnek süreçlerini alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/örnekler/okuma | Web Apps yuvaları örnekleri alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/MetricDefinitions/Read | Web Apps yuvaları ölçüm tanımlarını alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/ölçümler/okuma | Web Apps yuvaları ölçümlerini alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/migratemysql/Read | Web Apps yuvaları MySql geçişini alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/networktrace/Action | Ağ Izleme Web Apps yuvaları. |
> | Eylem | Microsoft. Web/Sites/yuvaları/networkizlemeler/operationresults/Read | Web Apps Yuvaları ağ Izleme Işlemi sonuçları alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/YeniParola/eylem | YeniParola Web Apps yuvaları. |
> | Eylem | Microsoft. Web/Sites/yuvaları/operationresults/Read | Web Apps yuvaları Işlem sonuçlarını alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/işlemler/okuma | Web Apps yuvası Işlemleri alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/PerfCounters/okuma | Web Apps yuvaları performans sayaçlarını alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/phplogging/okuma | Web Apps yuvaları Phplogging alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/premieraddons/Delete | Web Apps yuvaları Premier eklentileri silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/premieraddons/Read | Web Apps yuvaları Premier eklentilerden yararlanın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/premieraddons/Write | Web Apps yuvaları Premier Eklentiler ' i güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/süreçler/okuma | Web Apps yuvası süreçlerini alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/publiccertificates/Delete | Web Apps yuvaları Genel sertifikalarını silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/publiccertificates/Read | Web Apps yuvaları Genel sertifikalarını alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/publiccertificates/Write | Web Apps yuvaları Genel sertifikalarını oluşturun veya güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yayımlama/eylem | Web uygulaması yuvası yayımlama |
> | Eylem | Microsoft. Web/Sites/yuvaları/publishxml/Action | Web uygulaması yuvası için Yayımlama profili xml 'i al |
> | Eylem | Microsoft. Web/siteler/yuvalar/okuma | Web uygulaması dağıtım yuvasının özelliklerini al |
> | Eylem | Microsoft. Web/siteler/yuvalar/kurtarma/eylem | Web Apps yuvaları kurtarın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/resetSlotConfig/Action | Web uygulaması yuva yapılandırmasını Sıfırla |
> | Eylem | Microsoft. Web/Sites/yuvaları/resourcehealthmetadata/Read | Meta verileri Kaynak Durumu Web Apps yuvaları alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yeniden başlatma/eylem | Web uygulaması yuvasını yeniden başlatma |
> | Eylem | Microsoft. Web/siteler/yuvalar/geri yükleme/okuma | Web Apps yuvaları geri yükleme alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/geri yükleme/yazma | Web Apps yuvalarını geri yükleyin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/restorefrombackupblob/eylem | Web Apps yuvasını yedekleme Blobundan geri yükleyin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/restorefromdeletedapp/eylem | Silinen uygulamadan Web uygulaması yuvalarını geri yükleyin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/restoresnapshot/eylem | Web Apps yuvaları anlık görüntülerini geri yükleyin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/siteextensions/Sil | Web Apps yuvaları site uzantılarını silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/siteextensions/okuma | Web Apps yuvaları site uzantıları alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/siteextensions/yaz | Web Apps yuvaları site uzantılarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/slotsdiffs/eylem | Web uygulaması ve yuvaları arasındaki yapılandırmada farklılıklar alın |
> | Eylem | Microsoft. Web/Sites/yuvaları/slotsswap/eylem | Web uygulaması dağıtım yuvalarını değiştirme |
> | Eylem | Microsoft. Web/siteler/yuvalar/anlık görüntüler/okuma | Web Apps yuvaları anlık görüntülerini alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/sourcedenetimleri/silme | Web uygulaması yuvasının kaynak denetimi yapılandırma ayarlarını Sil |
> | Eylem | Microsoft. Web/siteler/yuvalar/sourcedenetimleri/okuma | Web uygulaması yuvasının kaynak denetimi yapılandırma ayarlarını al |
> | Eylem | Microsoft. Web/siteler/yuvalar/sourcedenetimleri/yazma | Web uygulaması yuvasının kaynak denetimi yapılandırma ayarlarını Güncelleştir |
> | Eylem | Microsoft. Web/siteler/yuvalar/Başlat/eylem | Web uygulaması yuvası başlatma |
> | Eylem | Microsoft. Web/siteler/yuvalar/durdur/eylem | Web uygulaması yuvasını durdur |
> | Eylem | Microsoft. Web/siteler/yuvalar/eşitleme/eylem | Web Apps yuvalarını eşitleyin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/triggeredwebjobs/Delete | Web Işleri tetiklediği Web Apps yuvalarını silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/triggeredwebjobs/okuma | Web Işleri tetiklediği Web Apps yuvaları alma. |
> | Eylem | Microsoft. Web/Sites/yuvaları/triggeredwebjobs/Run/ACTION | Web Işleri tetiklediği Web Apps yuvaları çalıştırın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/kullanımlar/okuma | Web Apps yuvası kullanımları alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/virtualnetworkconnections/Delete | Web Apps yuvaları sanal ağ bağlantılarını silin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/virtualnetworkconnections/Gateway/Write | Web Apps yuvaları sanal ağ bağlantısı ağ geçitlerini güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/yuvaları/virtualnetworkconnections/Read | Web Apps yuvaları sanal ağ bağlantıları alın. |
> | Eylem | Microsoft. Web/Sites/yuvaları/virtualnetworkconnections/Write | Web Apps yuvaları sanal ağ bağlantılarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/yuvalar/Web işleri/okuma | Web Apps yuvaları Web Işleri alın. |
> | Eylem | Microsoft. Web/siteler/yuvalar/yazma | Yeni bir Web uygulaması yuvası oluşturun veya mevcut olanı güncelleştirin |
> | Eylem | Microsoft. Web/Sites/slotsdiffs/eylem | Web uygulaması ve yuvaları arasındaki yapılandırmada farklılıklar alın |
> | Eylem | Microsoft. Web/Sites/slotsswap/Action | Web uygulaması dağıtım yuvalarını değiştirme |
> | Eylem | Microsoft. Web/siteler/anlık görüntüler/okuma | Web Apps anlık görüntülerini alın. |
> | Eylem | Microsoft. Web/Sites/sourcedenetimleri/silme | Web uygulamasının kaynak denetimi yapılandırma ayarlarını Sil |
> | Eylem | Microsoft. Web/Sites/sourcedenetimleri/okuma | Web uygulamasının kaynak denetimi yapılandırma ayarlarını al |
> | Eylem | Microsoft. Web/Sites/sourcedenetimleri/yazma | Web uygulamasının kaynak denetimi yapılandırma ayarlarını Güncelleştir |
> | Eylem | Microsoft. Web/siteler/Başlat/eylem | Web uygulaması başlatma |
> | Eylem | Microsoft. Web/siteler/durdur/eylem | Bir Web uygulamasını durdur |
> | Eylem | Microsoft. Web/siteler/eşitleme/eylem | Eşitleme Web Apps. |
> | Eylem | Microsoft. Web/Sites/syncfunctiontrigger/Action | Işlev tetikleyicilerini Eşitle. |
> | Eylem | Microsoft. Web/Sites/triggeredwebjobs/Delete | Web Apps tetiklenen Web Işlerini silin. |
> | Eylem | Microsoft. Web/Sites/triggeredwebjobs/geçmiş/okuma | Web Apps harekete geçirilmiş web Işleri geçmişi alın. |
> | Eylem | Microsoft. Web/Sites/triggeredwebjobs/okuma | Web Apps tetiklenen Web Işleri alın. |
> | Eylem | Microsoft. Web/Sites/triggeredwebjobs/Run/ACTION | Web Apps tetiklenen Web Işleri çalıştırın. |
> | Eylem | Microsoft. Web/Sites/kullanımlar/Read | Web Apps kullanımları alın. |
> | Eylem | Microsoft. Web/Sites/virtualnetworkconnections/Delete | Web Apps sanal ağ bağlantılarını silin. |
> | Eylem | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/Read | Web Apps sanal ağ bağlantısı ağ geçitlerini alın. |
> | Eylem | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/Write | Web Apps sanal ağ bağlantısı ağ geçitlerini güncelleştirin. |
> | Eylem | Microsoft. Web/Sites/virtualnetworkconnections/Read | Sanal ağ bağlantılarını Web Apps alın. |
> | Eylem | Microsoft. Web/Sites/virtualnetworkconnections/Write | Web Apps sanal ağ bağlantılarını güncelleştirin. |
> | Eylem | Microsoft. Web/siteler/Web işleri/okuma | Web Apps WebJobs alın. |
> | Eylem | Microsoft. Web/Sites/Write | Yeni bir Web uygulaması oluşturma veya var olanı güncelleştirme |
> | Eylem | Microsoft. Web/SKU/okuma | SKU 'Ları al. |
> | Eylem | Microsoft. Web/sourcecontrols/Read | Kaynak denetimlerini al. |
> | Eylem | Microsoft. Web/sourcecontrols/Write | Kaynak denetimlerini güncelleştir. |
> | Eylem | Microsoft. Web/Unregister/eylem | Abonelik için Microsoft. Web kaynak sağlayıcısı kaydını silin. |
> | Eylem | Microsoft. Web/Validate/ACTION | Doğrulamalısınız. |
> | Eylem | Microsoft. Web/verifyhostingenvironmentvnet/eylem | Barındırma ortamı VNET 'i doğrulayın. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Eylem türü | İşlem | Açıklama |
> | --- | --- | --- |
> | Eylem | Microsoft. WorkloadMonitor/Components/Read | Kaynak için bileşenleri alır |
> | Eylem | Microsoft. WorkloadMonitor/componentsSummary/okuma | Bileşenlerin özetini alır |
> | Eylem | Microsoft. WorkloadMonitor/Monitorınstances/Read | Kaynak için izleyici örneklerini alır |
> | Eylem | Microsoft. WorkloadMonitor/Monitorınstancessummary/Read | İzleme örneklerinin özetini alır |
> | Eylem | Microsoft. WorkloadMonitor/izleyicileri/okundu | Kaynak için izleyicileri alır |
> | Eylem | Microsoft. WorkloadMonitor/izleyicileri/Write | Kaynak için izleyiciyi yapılandırın |
> | Eylem | Microsoft. WorkloadMonitor/notificationSettings/Read | Kaynak için bildirim ayarlarını alır |
> | Eylem | Microsoft. WorkloadMonitor/notificationSettings/Write | Kaynak için bildirim ayarlarını yapılandırın |
> | Eylem | Microsoft. WorkloadMonitor/işlemler/okuma | Desteklenen işlemleri alır |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için özel roller](custom-roles.md)
- [Azure kaynakları için yerleşik roller](built-in-roles.md)
