---
title: Azure AD Access Control Service 'ten SAS 'ye geçiş
description: Azure Active Directory Access Control Service, paylaşılan erişim Imzası yetkilendirmesini geçirme hakkında bilgi edinin.
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: ae0dd3827e17cc63b4b698eb8d88a08799c7278f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790344"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Active Directory Access Control Service, paylaşılan erişim Imzası yetkilendirmesi 'ne geçirme

Service Bus uygulamalar, daha önce iki farklı yetkilendirme modeli kullanma seçeneği içeriyordu: doğrudan Service Bus tarafından sunulan [paylaşılan erişim imzası (SAS)](service-bus-sas.md) belirteç modeli ve yetkilendirme kurallarının yönetilebileceği bir Federasyon modeli [Azure Active Directory](/azure/active-directory/) Access Control SERVICE (ACS) tarafından YÖNETILEN ve ACS 'den edinilen belirteçler, istenen özelliklere erişimi yetkilendirmek için Service Bus geçirilir.

ACS yetkilendirme modelinin süresi, tercih edilen model olarak [SAS yetkilendirmesi](service-bus-authentication-and-authorization.md) ile değiştirilmiştir ve tüm belgeler, kılavuzluk ve örnekler günümüzde SAS kullanır. Üstelik, artık ACS ile eşleştirilmiş yeni Service Bus ad alanları oluşturmak mümkün değildir.

SAS kural adı ve kural anahtarına istemci erişimi vererek, SAS, başka bir hizmete hemen bağımlı olmasa da, hiçbir aracı olmadan doğrudan bir istemciden doğrudan kullanılabilir. SAS ayrıca, bir istemcinin ilk olarak başka bir hizmetle bir yetkilendirme denetimi geçirmesi gereken bir yaklaşım ile kolayca tümleştirilebilir ve ardından bir belirteç verilir. İkinci yaklaşım ACS kullanım düzenine benzerdir, ancak ACS 'de hızlı bir şekilde ifade edilen uygulamaya özgü koşullara göre erişim belirteçleri verilmesine olanak sağlar.

ACS 'ye bağlı olan tüm mevcut uygulamalar için, müşterileri uygulamalarını SAS 'ye göre geçirmeye geçiririz.

## <a name="migration-scenarios"></a>Geçiş senaryoları

ACS ve Service Bus, bir *İmzalama anahtarının*paylaşılan bilgisi aracılığıyla tümleştirilir. İmzalama anahtarı, bir ACS ad alanı tarafından, yetkilendirme belirteçlerini imzalamak için kullanılır ve belirtecin eşleştirilmiş ACS ad alanı tarafından verildiğini doğrulamak için Service Bus tarafından kullanılır. ACS ad alanı hizmet kimliklerini ve yetkilendirme kurallarını barındırır. Yetkilendirme kuralları, hangi hizmet kimliğinin veya bir dış kimlik sağlayıcısı tarafından verilen belirtecin, en uzun ön ek eşleşme biçiminde Service Bus ad alanı grafiğinin bir bölümüne hangi tür erişimi olduğunu tanımlar.

Örneğin, bir ACS kuralı, bir hizmet kimliğine `/` yol ön ekine **gönderilen** isteği verebilir, bu da bu KURALA göre ACS tarafından verilen belirtecin, istemci haklarının ad alanındaki tüm varlıklara gönderilmesini sağlar. Yol ön eki `/abc`ise, kimlik, bu ön ek altında `abc` olarak adlandırılan varlıklara gönderilmeye kısıtlıdır. Bu geçiş kılavuzunun okuyucularının bu kavramlarla zaten tanıdık olduğu varsayılır.

Geçiş senaryoları üç geniş kategoriye ayrılır:

1.  **Değiştirilmemiş varsayılanlar**. Bazı müşteriler bir [Sharedsecrettokenprovider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) nesnesi kullanarak, ACS ad alanı için otomatik olarak oluşturulan **sahip** hizmeti kimliğini ve gizli anahtarını geçirerek Service Bus ad alanıyla eşleştirilir ve yeni kurallar eklemez.

2.  **Basit kurallara sahip özel hizmet kimlikleri**. Bazı müşteriler yeni hizmet kimlikleri ekler ve her yeni hizmet kimliğini belirli bir varlık için **gönderme**, **dinleme**ve **yönetme** izinleri verir.

3.  **Karmaşık kurallara sahip özel hizmet kimlikleri**. Çok az sayıda müşteri, dışarıdan verilen belirteçlerin geçiş üzerindeki haklara eşlendiği veya tek bir hizmet kimliğinin birden çok kural aracılığıyla çeşitli ad alanı yollarında farklılaştırılan haklar atandığı karmaşık kural kümelerine sahiptir.

Karmaşık kural kümelerinin geçişine ilişkin yardım için [Azure desteğine](https://azure.microsoft.com/support/options/)başvurabilirsiniz. Diğer iki senaryo doğrudan geçişe olanak tanır.

### <a name="unchanged-defaults"></a>Değiştirilmemiş varsayılanlar

Uygulamanız ACS varsayılanlarını değiştirmediyse, tüm [Sharedsecrettokenprovider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) kullanımını [Sharedaccesssignaturetokenprovider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) nesnesiyle değiştirebilir ve bunun yerine önceden yapılandırılmış **RootManageSharedAccessKey** ad alanını kullanabilirsiniz , ACS **sahip** hesabı. Bu hesap/kural ad alanı üzerinde tüm varlıkları silme izni de dahil olmak üzere tam yönetim yetkilisi sağladığından, ACS **sahip** hesabıyla bile bu yapılandırmanın (ve yine de) önerildiğine unutmayın.

### <a name="simple-rules"></a>Basit kurallar

Uygulama basit kurallarla özel hizmet kimlikleri kullanıyorsa, belirli bir kuyrukta erişim denetimi sağlamak için bir ACS hizmet kimliğinin oluşturulduğu durumda doğrudan geçiş yapılır. Bu senaryo genellikle her kuyruğun bir kiracı sitesi veya şube ofisi için bir köprü olarak kullanıldığı ve bu belirli site için hizmet kimliğinin oluşturulduğu SaaS stili çözümlerde bir durumdur. Bu durumda, ilgili hizmet kimliği, doğrudan kuyruktaki bir paylaşılan erişim Imza kuralına geçirilebilir. Hizmet kimliği adı SAS kuralı adı olabilir ve hizmet kimliği anahtarı SAS kural anahtarı olabilir. SAS kuralının hakları daha sonra varlık için sırasıyla geçerli ACS kuralına eşit olarak yapılandırılır.

ACS ile federe olan mevcut bir ad alanı üzerinde bu yeni ve ek SAS yapılandırmalarını yerinde yapabilir ve ACS 'den uzağa geçiş daha sonra, [](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) [yerine Sharedaccesssignaturetokenprovider kullanılarak gerçekleştirilir SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Ad alanının ACS 'den bağlantısının kesilme gereksinimi yoktur.

### <a name="complex-rules"></a>Karmaşık kurallar

SAS kurallarının hesap olmaması gerekmez, ancak haklarıyla ilişkili İmzalama anahtarları adlandırılmaktadır. Bu nedenle, uygulamanın birçok hizmet kimliği oluşturduğu ve bunlara çeşitli varlıklar için erişim hakları veren senaryolar veya tüm ad alanı hala belirteç veren bir aracı gerektirir. [Destek ile iletişime geçerek](https://azure.microsoft.com/support/options/)böyle bir aracı için rehberlik elde edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus kimlik doğrulaması hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kimlik doğrulaması ve yetkilendirme](service-bus-authentication-and-authorization.md)
* [Paylaşılan erişim Imzaları ile kimlik doğrulamasını Service Bus](service-bus-sas.md)

