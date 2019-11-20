---
title: Kimlik doğrulaması ve yetkilendirme Azure Service Bus | Microsoft Docs
description: Paylaşılan erişim Imzası (SAS) kimlik doğrulamasıyla Service Bus için uygulamaların kimliğini doğrulayın.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013237"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus kimlik doğrulaması ve yetkilendirme

Uygulamalar, paylaşılan erişim Imzası (SAS) belirteci kimlik doğrulamasını kullanarak Azure Service Bus kaynaklarına erişim kazanır. SAS ile uygulamalar, hem belirteç veren hem de Service Bus (Bu nedenle "paylaşılan") olarak bilinen bir simetrik anahtarla imzalanmış Service Bus için bir belirteç sunar ve bu anahtar, izin verme iletileri alın/dinleyin veya gönderin. SAS kuralları, ad alanı üzerinde veya doğrudan kuyruk veya konu gibi varlıklarda yapılandırılır ve bu da hassas erişim denetimi sağlar.

SAS belirteçleri doğrudan bir Service Bus istemcisi tarafından oluşturulabilir veya istemcinin etkileşime girdiği ara belirteç veren bir uç nokta tarafından oluşturulabilir. Örneğin, bir sistem istemcinin kimliğini ve sistem erişim haklarını kanıtlamak için Active Directory yetkilendirmesi korumalı bir Web hizmeti uç noktası aramasını gerektirebilir ve Web hizmeti, uygun Service Bus belirtecini döndürür. Bu SAS belirteci, Azure SDK 'sına dahil olan Service Bus belirteç sağlayıcısı kullanılarak kolayca oluşturulabilir. 

> [!IMPORTANT]
> Service Bus ile Azure Active Directory Access Control (Access Control Service veya ACS olarak da bilinir) kullanıyorsanız, bu yöntemin desteğinin artık sınırlı olduğunu ve uygulamanızı SAS kullanacak şekilde geçirmeniz gerektiğini unutmayın. Daha fazla bilgi için [Bu blog gönderisine](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) ve [Bu makaleye](service-bus-migrate-acs-sas.md)bakın.

## <a name="azure-active-directory"></a>Azure Active Directory
Service Bus kaynakları için Azure Active Directory (Azure AD) tümleştirmesi, bir istemcinin kaynaklara erişimi üzerinde ayrıntılı denetim için rol tabanlı erişim denetimi (RBAC) sağlar. Rol tabanlı erişim denetimi 'ni (RBAC), bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek güvenlik sorumlusu için izin vermek üzere kullanabilirsiniz. Güvenlik sorumlusunun bir OAuth 2,0 belirteci döndürmesi için Azure AD tarafından kimliği doğrulanır. Belirteç, bir Service Bus kaynağına (kuyruk, konu vb.) erişim isteğine yetki vermek için kullanılabilir.

Azure AD ile kimlik doğrulama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Yönetilen kimliklerle kimlik doğrulama](service-bus-managed-service-identity.md)
- [Uygulamadan kimlik doğrulama](authenticate-application.md)

> [!IMPORTANT]
> Azure AD tarafından döndürülen OAuth 2,0 belirtecini kullanarak kullanıcıları veya uygulamaları yetkilendirmek, paylaşılan erişim imzaları (SAS) üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Azure AD ile, belirteçlerin kodunuzda depolanması ve olası güvenlik açıklarına karşı risk altında olması gerekmez. Mümkün olduğunda Azure AD 'yi Azure Service Bus uygulamalarınızla kullanmanızı öneririz. 


## <a name="shared-access-signature"></a>Paylaşılan erişim imzası
[SAS kimlik doğrulaması](service-bus-sas.md) , bir kullanıcıya belirli haklara sahip Service Bus kaynaklarına erişim izni vermenizi sağlar. Service Bus SAS kimlik doğrulaması, bir Service Bus kaynağında ilişkili haklara sahip bir şifreleme anahtarı yapılandırmasını içerir. İstemciler, erişilmekte olan kaynak URI 'sinden ve yapılandırılan anahtarla imzalanmış bir süre sonu olan bir SAS belirteci sunarak bu kaynağa erişim elde edebilir.

SAS için anahtarları bir Service Bus ad alanında yapılandırabilirsiniz. Anahtar, bu ad alanı içindeki tüm mesajlaşma varlıkları için geçerlidir. Ayrıca, Service Bus kuyrukları ve konularında anahtarlar da yapılandırabilirsiniz. SAS de [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md)desteklenir.

SAS 'yi kullanmak için bir ad alanı, kuyruk veya konu üzerinde bir [Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nesnesi yapılandırabilirsiniz. Bu kural aşağıdaki öğelerden oluşur:

* *KeyName*: kuralı tanımlar.
* *PrimaryKey*: SAS belirteçlerini imzalamak/doğrulamak için kullanılan bir şifreleme anahtarı.
* *Secondarykey*: SAS belirteçlerini imzalamak/doğrulamak için kullanılan bir şifreleme anahtarı.
* *Haklar*: sağlanan **dinleme**, **gönderme**veya **yönetme** haklarını temsil eder.

Ad alanı düzeyinde yapılandırılan yetkilendirme kuralları, ilgili anahtar kullanılarak imzalanmış belirteçleri olan istemciler için bir ad alanındaki tüm varlıklara erişim verebilir. Bir Service Bus ad alanı, kuyruk veya konu başlığı altında en fazla 12 ' ye kadar yetkilendirme kuralı yapılandırabilirsiniz. Varsayılan olarak, tüm haklara sahip bir [Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) , ilk sağlandığı sırada her ad alanı için yapılandırılır.

Bir varlığa erişmek için, istemci belirli bir [Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)kullanılarak oluşturulan bir SAS belirteci gerektirir. SAS belirteci, erişimi talep edilen kaynak URI 'sinden ve yetkilendirme kuralıyla ilişkili bir şifreleme anahtarıyla bir süre sonu olan bir kaynak dizesinin HMAC-SHA256 kullanılarak oluşturulur.

Service Bus için SAS kimlik doğrulama desteği, Azure .NET SDK 2,0 ve üzeri sürümlerine dahildir. SAS, [Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)için destek içerir. Parametre olarak bir bağlantı dizesini kabul eden tüm API 'Ler SAS bağlantı dizeleri için destek içerir.

## <a name="next-steps"></a>Sonraki adımlar

- SAS hakkında daha fazla bilgi için [paylaşılan erişim imzalarıyla Service Bus kimlik doğrulaması](service-bus-sas.md) okumaya devam edin.
- [Azure Active Directory Access Control (ACS) Ile paylaşılan erişim imzası yetkilendirmesi arasında geçiş](service-bus-migrate-acs-sas.md)yapma.
- [ACS etkin ad alanlarında yapılan değişiklikler](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Azure Relay kimlik doğrulaması ve yetkilendirme hakkında ilgili bilgiler için bkz. [Azure Relay kimlik doğrulaması ve yetkilendirme](../service-bus-relay/relay-authentication-and-authorization.md). 

