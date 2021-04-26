---
title: Kimlik doğrulaması ve yetkilendirme Azure Service Bus | Microsoft Docs
description: Paylaşılan erişim Imzası (SAS) kimlik doğrulamasıyla Service Bus için uygulamaların kimliğini doğrulayın.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccb526abd99be50e33c8adb918186944b7af3bd6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516663"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus kimlik doğrulaması ve yetkilendirme
Azure Service Bus kaynaklarına erişimin kimliğini doğrulamak ve bunlara yetki vermek için iki yol vardır: Azure etkinlik dizini (Azure AD) ve paylaşılan erişim Imzaları (SAS). Bu makale, bu iki tür güvenlik mekanizmasını kullanma hakkında ayrıntılı bilgi sağlar. 

## <a name="azure-active-directory"></a>Azure Active Directory
Service Bus kaynakları için Azure AD tümleştirmesi, bir istemcinin kaynaklara erişimi üzerinde ayrıntılı denetim için Azure rol tabanlı erişim denetimi (RBAC) sağlar. Bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izin vermek üzere Azure RBAC kullanabilirsiniz. Güvenlik sorumlusunun bir OAuth 2,0 belirteci döndürmesi için Azure AD tarafından kimliği doğrulanır. Belirteç, bir Service Bus kaynağına (kuyruk, konu vb.) erişim isteğine yetki vermek için kullanılabilir.

Azure AD ile kimlik doğrulama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Yönetilen kimliklerle kimlik doğrulaması](service-bus-managed-service-identity.md)
- [Uygulamadan kimlik doğrulaması yapma](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) , OAuth kimlik DOĞRULAMASıNı Azure AD ile destekler.

> [!IMPORTANT]
> Azure AD tarafından döndürülen OAuth 2,0 belirtecini kullanarak kullanıcıları veya uygulamaları yetkilendirmek, paylaşılan erişim imzaları (SAS) üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Azure AD ile, belirteçlerin kodunuzda depolanması ve olası güvenlik açıklarına karşı risk altında olması gerekmez. Mümkünse Azure Service Bus uygulamalarınızla Azure AD kullanmanızı öneririz. 

## <a name="shared-access-signature"></a>Paylaşılan erişim imzası
[SAS kimlik doğrulaması](service-bus-sas.md) , bir kullanıcıya belirli haklara sahip Service Bus kaynaklarına erişim izni vermenizi sağlar. Service Bus SAS kimlik doğrulaması, bir Service Bus kaynağında ilişkili haklara sahip bir şifreleme anahtarı yapılandırmasını içerir. İstemciler, erişilmekte olan kaynak URI 'sinden ve yapılandırılan anahtarla imzalanmış bir süre sonu olan bir SAS belirteci sunarak bu kaynağa erişim elde edebilir.

SAS için anahtarları bir Service Bus ad alanında yapılandırabilirsiniz. Anahtar, bu ad alanı içindeki tüm mesajlaşma varlıkları için geçerlidir. Ayrıca, Service Bus kuyrukları ve konularında anahtarlar da yapılandırabilirsiniz. SAS de [Azure Relay](../azure-relay/relay-authentication-and-authorization.md)desteklenir.

SAS 'yi kullanmak için, bir ad alanı, kuyruk veya konu üzerinde bir paylaşılan erişim yetkilendirme kuralı yapılandırabilirsiniz. Bu kural aşağıdaki öğelerden oluşur:

* *KeyName*: kuralı tanımlar.
* *PrimaryKey*: SAS belirteçlerini imzalamak/doğrulamak için kullanılan bir şifreleme anahtarı.
* *Secondarykey*: SAS belirteçlerini imzalamak/doğrulamak için kullanılan bir şifreleme anahtarı.
* *Haklar*: sağlanan **dinleme**, **gönderme** veya **yönetme** haklarını temsil eder.

Ad alanı düzeyinde yapılandırılan yetkilendirme kuralları, ilgili anahtar kullanılarak imzalanmış belirteçleri olan istemciler için bir ad alanındaki tüm varlıklara erişim verebilir. Bir Service Bus ad alanı, kuyruk veya konu başlığı altında en fazla 12 ' ye kadar yetkilendirme kuralı yapılandırabilirsiniz. Varsayılan olarak, tüm haklara sahip bir paylaşılan erişim yetkilendirme kuralı, ilk sağlandığı zaman her ad alanı için yapılandırılır.

Bir varlığa erişmek için, istemci belirli bir paylaşılan erişim yetkilendirme kuralı kullanılarak oluşturulan bir SAS belirteci gerektirir. SAS belirteci, erişimi talep edilen kaynak URI 'sinden ve yetkilendirme kuralıyla ilişkili bir şifreleme anahtarıyla bir süre sonu olan bir kaynak dizesinin HMAC-SHA256 kullanılarak oluşturulur.

Service Bus için SAS kimlik doğrulama desteği, Azure .NET SDK 2,0 ve üzeri sürümlerine dahildir. SAS, paylaşılan erişim yetkilendirme kuralı için destek içerir. Parametre olarak bir bağlantı dizesini kabul eden tüm API 'Ler SAS bağlantı dizeleri için destek içerir.

> [!IMPORTANT]
> Service Bus ile Azure Active Directory Access Control (Access Control Service veya ACS olarak da bilinir) kullanıyorsanız, bu yöntemin desteğinin artık sınırlı olduğunu ve [UYGULAMANıZı SAS kullanacak şekilde geçirmeniz](service-bus-migrate-acs-sas.md) ya da Azure AD ile OAuth 2,0 kimlik doğrulaması kullanmanızı (önerilir) unutmayın. ACS 'nin kullanımdan kaldırılması hakkında daha fazla bilgi için [Bu blog gönderisine](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)bakın.

## <a name="next-steps"></a>Sonraki adımlar
Azure AD ile kimlik doğrulama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Yönetilen kimliklerle kimlik doğrulaması](service-bus-managed-service-identity.md)
- [Uygulamadan kimlik doğrulama](authenticate-application.md)

SAS ile kimlik doğrulama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [SAS ile kimlik doğrulama](service-bus-sas.md)
