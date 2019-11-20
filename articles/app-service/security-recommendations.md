---
title: Azure App Service için güvenlik önerileri
description: Azure App Service için güvenlik önerileri. Bu önerilerin uygulanması, paylaşılan sorumluluk modelinizde açıklandığı şekilde güvenlik yükümlülüklerinizi karşılamanız ve Web uygulaması çözümleriniz için genel güvenliği iyileştirmenize yardımcı olacaktır.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 71d564dac43328371e9d34684f2d13a26616a99d
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609779"
---
# <a name="security-recommendations-for-app-service"></a>App Service için güvenlik önerileri

Bu makale Azure App Service için güvenlik önerileri içerir. Bu önerilerin uygulanması, paylaşılan sorumluluk modelinizde açıklandığı şekilde güvenlik yükümlülüklerinizi karşılamanız ve Web uygulaması çözümleriniz için genel güvenliği iyileştirmenize yardımcı olacaktır. Microsoft 'un hizmet sağlayıcısı sorumluluklarını yerine getirmek için neler yaptığı hakkında daha fazla bilgi için [Azure altyapı güvenliği](../security/fundamentals/infrastructure.md)makalesini okuyun.

## <a name="general"></a>Genel

| Öneri | Açıklamalar |
|-|-|----|
| Güncel kalın | Desteklenen platformlar, programlama dilleri, protokoller ve çerçeveler için en son sürümleri kullanın. |

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi

| Öneri | Açıklamalar |
|-|----|
| Anonim erişimi devre dışı bırak | Anonim istekleri desteklemeniz gerekmiyorsa, anonim erişimi devre dışı bırakın. Azure App Service kimlik doğrulama seçenekleri hakkında daha fazla bilgi için bkz. [Azure App Service kimlik doğrulama ve yetkilendirme](overview-authentication-authorization.md).|
| Kimlik doğrulaması gerektir | Mümkün olduğunda kimlik doğrulama ve yetkilendirmeyi işlemek için kod yazmak yerine App Service kimlik doğrulama modülünü kullanın. Bkz. [Azure App Service kimlik doğrulaması ve yetkilendirme](overview-authentication-authorization.md). |
| Kimliği doğrulanmış erişim ile arka uç kaynaklarını koruma | Bir arka uç kaynağında kimlik doğrulaması yapmak için kullanıcının kimliğini kullanabilir veya bir uygulama kimliği kullanabilirsiniz. Uygulama kimliği kullanmayı seçtiğinizde, [yönetilen bir kimlik](overview-managed-identity.md)kullanın.
| İstemci sertifikası kimlik doğrulaması iste | İstemci sertifikası kimlik doğrulaması, yalnızca sağladığınız sertifikaları kullanarak kimlik doğrulayabilecek istemcilerden gelen bağlantılara izin vererek güvenliği geliştirir. |

## <a name="data-protection"></a>Veri koruma

| Öneri | Açıklamalar |
|-|-|
| HTTP 'yi HTTPs 'ye yönlendir | Varsayılan olarak, istemciler Web uygulamalarına hem HTTP ya da HTTPS kullanarak bağlanabilir. HTTPS SSL/TLS protokolünü kullandığından, hem şifreli hem de kimliği doğrulanmış güvenli bir bağlantı sağlamak için HTTP 'yi HTTPs 'ye yeniden yönlendirmenizi öneririz. |
| Azure kaynaklarıyla iletişimi şifreleyin | Uygulamanız [SQL veritabanı](https://azure.microsoft.com/services/sql-database/) veya [Azure depolama](/azure/storage/)gibi Azure kaynaklarına bağlanıyorsa, bağlantı Azure 'da kalır. Bağlantı, Azure 'daki paylaşılan ağ üzerinden başladığından, her zaman tüm iletişimi şifrelemeniz gerekir. |
| Mümkün olan en son TLS sürümünü gerektir | 2018 yeni Azure App Service uygulamalar TLS 1,2 ' i kullandığından. Daha yeni TLS sürümleri, eski protokol sürümleri üzerinde güvenlik geliştirmeleri içerir. |
| FTPS kullanma | App Service, dosyalarınızı dağıtmak için hem FTP hem de FTPS 'yi destekler. Mümkün olduğunda, FTP yerine FTPS kullanın. Bu protokollerin biri veya her ikisi kullanımda olmadığında, [bunları devre dışı bırakmanız](deploy-ftp.md#enforce-ftps)gerekir. |
| Uygulama verilerinin güvenliğini sağlama | Kodunuzda veya yapılandırma dosyalarınızda veritabanı kimlik bilgileri, API belirteçleri veya özel anahtarlar gibi uygulama gizli dizileri depolamayın. Yaygın olarak kabul edilen yaklaşım, bunlara seçtiğiniz dilde standart bir model kullanılarak [ortam değişkenleri](https://wikipedia.org/wiki/Environment_variable) olarak erişiyor. Azure App Service, [uygulama ayarları](web-sites-configure.md) ve [bağlantı dizeleri](web-sites-configure.md)aracılığıyla ortam değişkenlerini tanımlayabilirsiniz. Uygulama ayarları ve bağlantı dizeleri Azure 'da şifreli olarak depolanır. Uygulama ayarlarının şifresi, uygulama başladığında uygulamanızın işlem belleğine eklenmeden önce çözülür. Şifreleme anahtarları düzenli olarak döndürülür. Alternatif olarak, gelişmiş gizlilikler yönetimi için Azure App Service uygulamanızı [Azure Key Vault](/azure/key-vault/) tümleştirebilirsiniz. [Key Vault yönetilen bir kimlikle](../key-vault/tutorial-web-application-keyvault.md)App Service uygulamanız, ihtiyacınız olan gizli bilgilere güvenli bir şekilde erişebilir. |

## <a name="networking"></a>Ağ

| Öneri | Açıklamalar |
|-|-|
| Statik IP kısıtlamalarını kullanın | Windows üzerinde Azure App Service, uygulamanıza erişmelerine izin verilen IP adreslerinin bir listesini tanımlamanızı sağlar. İzin verilen liste, tek tek IP adreslerini veya bir alt ağ maskesi tarafından tanımlanan bir IP adresi aralığını içerebilir. Daha fazla bilgi için bkz. [Azure App Service STATIK IP kısıtlamaları](app-service-ip-restrictions.md).  |
| Yalıtılmış fiyatlandırma katmanını kullanın | Yalıtılmış fiyatlandırma katmanı hariç tüm katmanlar, uygulamalarınızı Azure App Service paylaşılan ağ altyapısında çalıştırır. Yalıtılmış katman, uygulamalarınızı adanmış bir [App Service ortamı](environment/intro.md)içinde çalıştırarak tamamen ağ yalıtımı sağlar. Bir App Service ortamı, kendi [Azure sanal ağı](/azure/virtual-network/)Örneğinizde çalışır.|
| Şirket içi kaynaklara erişirken güvenli bağlantıları kullanma | Şirket içi kaynaklara bağlanmak için [karma bağlantılar](app-service-hybrid-connections.md), [sanal ağ tümleştirmesi](web-sites-integrate-with-vnet.md)veya [App Service ortamı](environment/intro.md) ' nı kullanabilirsiniz. |
| Gelen ağ trafiği için pozlamayı sınırlayın | Ağ güvenlik grupları ağ erişimini kısıtlayıp açığa çıkarılan uç noktaların sayısını denetlemenizi sağlar. Daha fazla bilgi için bkz. [App Service ortamı gelen trafiği denetleme](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>İzleme

| Öneri | Açıklamalar |
|-|-|
|Azure Güvenlik Merkezi Standart katmanını kullanın | [Azure Güvenlik Merkezi](../security-center/security-center-app-services.md) , Azure App Service ile yerel olarak tümleşiktir. Değerlendirme çalıştırabilir ve güvenlik önerileri sağlayabilir. |

## <a name="next-steps"></a>Sonraki adımlar

Ek güvenlik gereksinimleri olup olmadığını görmek için uygulama sağlayıcınıza danışın. Güvenli uygulamalar geliştirme hakkında daha fazla bilgi için bkz. [Güvenli Geliştirme belgeleri](../security/fundamentals/abstract-develop-secure-apps.md).
