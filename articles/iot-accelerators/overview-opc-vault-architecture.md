---
title: OPC kasa mimarisi-Azure | Microsoft Docs
description: OPC Kasası sertifika yönetimi hizmeti mimarisi
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200152"
---
# <a name="opc-vault-architecture"></a>OPC Kasası mimarisi

Bu makale OPC Kasası mikro hizmeti ve OPC Kasası IoT Edge modülüyle ilgili bir genel bakış sunar.

OPC UA uygulamaları uygulama düzeyi güvenliği sağlamak için uygulama örneği sertifikaları kullanır. Güvenli bir bağlantı, asimetrik şifreleme kullanılarak oluşturulur ve bu uygulama sertifikaları ortak ve özel anahtar çiftini sağlar. Sertifikalar otomatik olarak imzalanabilir veya bir sertifika yetkilisi (CA) tarafından imzalanabilir.

OPC UA uygulaması, güvendiği uygulamaları temsil eden güvenilir sertifikaların bir listesini içerir. Bu sertifikalar, bir CA tarafından otomatik olarak imzalanmış veya imzalanabilir ya da bir kök CA ya da bir alt CA olabilir. Güvenilir bir sertifika daha büyük bir Sertifika zincirinin parçasıysa, uygulama, güven listesindeki sertifikaya zincirleme yapan tüm sertifikalara güvenir. Tam sertifika zinciri doğrulanabilir olduğu sürece bu değer doğrudur.

Otomatik olarak imzalanan sertifikalara güvenen ve CA sertifikasına güvenen önemli fark, güven dağıtmak ve korumak için gereken yükleme çabasıdır. Ayrıca, şirkete özgü bir CA barındırmak için de ek çaba vardır. 

Tek bir istemci uygulaması ile birden çok sunucuya yönelik otomatik olarak imzalanan sertifikalara güven dağıtmak için, tüm sunucu uygulama sertifikalarını istemci uygulama güven listesine yüklemelisiniz. Ayrıca, istemci uygulama sertifikasını tüm sunucu uygulaması güven listelerine yüklemelisiniz. Bu yönetim çabasının oldukça bir yükü vardır ve sertifika ömrünü göz önünde bulundurmanız ve sertifikaları yenilemeniz gerektiğinde de artar.

Şirkete özgü CA kullanımı, birden çok sunucu ve istemcilerle güvenin yönetimini büyük ölçüde basitleştirebilir. Bu durumda, yönetici, kullanılan her istemci ve sunucu için bir kez CA imzalı uygulama örneği sertifikası oluşturur. Ayrıca, CA sertifikası tüm sunucu ve istemcilerde her uygulama güven listesine yüklenir. Bu yaklaşımda, etkilenen uygulamalar için yalnızca süre dolma yapılan sertifikaların yenilenmesi ve değiştirilmeleri gerekir.

Azure endüstriyel IoT OPC UA sertifika yönetimi hizmeti, OPC UA uygulamaları için şirkete özgü bir CA 'yı yönetmenize yardımcı olur. Bu hizmet OPC Kasası mikro hizmetini temel alır. OPC Kasası, güvenli bir bulutta şirkete özgü bir CA barındırmak için bir mikro hizmet sağlar. Bu çözüm, Azure Active Directory (Azure AD) tarafından desteklenen hizmetler tarafından desteklenir Azure Key Vault, donanım güvenlik modülleri (HSM 'ler), Azure Cosmos DB ve isteğe bağlı olarak bir uygulama deposu olarak IoT Hub.

OPC Kasası mikro hizmeti, güvenlik yöneticileri ve onaylayanlara, istekleri onaylama veya reddetme Azure Key Vault oturum açma haklarını içeren rol tabanlı iş akışını destekleyecek şekilde tasarlanmıştır.

Mevcut OPC UA çözümleriyle uyumluluk için, hizmetler bir OPC Kasası mikro hizmeti destekli Edge modülü desteği içerir. Bu, **OPC UA genel keşif sunucusunu ve sertifika yönetimi** arabirimini, belirtiminin 12. bölümüne göre sertifikaları ve güven listelerini dağıtmak için uygular. 


## <a name="architecture"></a>Mimari

Mimari, fabrika ağı için OPC Kasası IoT Edge modülü ve iş akışını denetlemek için bir Web örneği UX ile OPC Kasası mikro hizmetini temel alır:

![OPC Kasası mimarisinin diyagramı](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC Kasası mikro hizmeti

OPC Kasası mikro hizmeti, OPC UA uygulamalarına yönelik şirkete özgü bir CA 'yı dağıtmak ve yönetmek üzere iş akışını uygulamak için aşağıdaki arabirimlerden oluşur.

### <a name="application"></a>Uygulama 
- OPC UA uygulaması, bir sunucu veya istemci ya da her ikisi olabilir. OPC Kasası, bu durumda bir uygulama kayıt yetkilisi olarak hizmet verir. 
- Uygulamaları kaydetmek, güncelleştirmek ve silmek için temel işlemlere ek olarak, arama ifadelerine sahip uygulamaları bulmak ve sorgulamak için de arabirimler vardır. 
- Bir isteği işlemek ve OPC UA 'ya özgü uzantılara sahip imzalı bir sertifika vermek için sertifika isteklerinin geçerli bir uygulamaya başvurması gerekir. 
- Uygulama hizmeti Azure Cosmos DB bir veritabanı tarafından desteklenir.

### <a name="certificate-group"></a>Sertifika grubu
- Sertifika grubu, sertifikaları imzalamak için özel anahtar dahil olmak üzere kök CA veya alt CA sertifikası depolayan bir varlıktır. 
- RSA anahtar uzunluğu, SHA-2 karma uzunluğu ve yaşam süreleri hem veren CA hem de imzalı uygulama sertifikaları için yapılandırılabilir. 
- CA sertifikalarını FIPS 140-2 düzey 2 HSM ile desteklenen Azure Key Vault ' de depoladığınızda. İmzalama, Azure AD tarafından güvenliği sağlanmış bir Key Vault işlemi tarafından yapıldığından, özel anahtar hiçbir şekilde güvenli depolamayı terk etmez. 
- CA sertifikalarını zaman içinde yenileyebilir ve Key Vault geçmişi nedeniyle güvenli depolamada kalabilirler. 
- Her CA sertifikası için iptal listesi, gizli olarak Key Vault da depolanır. Bir uygulamanın kaydı silindiğinde, uygulama sertifikası da bir yönetici tarafından sertifika Iptal listesinde (CRL) iptal edilir.
- Tek sertifikaların yanı sıra toplu sertifikaları iptal edebilirsiniz.

### <a name="certificate-request"></a>Sertifika isteği
Bir sertifika isteği, OPC UA uygulaması için bir sertifika Imzalama Isteği (CSR) kullanarak yeni bir anahtar çifti veya imzalı bir sertifika oluşturmak üzere iş akışını uygular. 
- İstek, konu veya CSR gibi bilgileri içeren bir veritabanında depolanır ve OPC UA uygulamasına bir başvurudur. 
- Hizmette iş mantığı, isteği uygulama veritabanında depolanan bilgilere karşı doğrular. Örneğin, veritabanındaki uygulama URI 'Si CSR içindeki uygulama URI 'Siyle eşleşmelidir.
- İmzalama hakları olan bir güvenlik Yöneticisi (yani onaylayan rolü) isteği onaylar veya reddeder. İstek onaylanırsa, yeni bir anahtar çifti veya imzalı bir sertifika (veya her ikisi de) oluşturulur. Yeni özel anahtar Key Vault güvenli bir şekilde depolanır ve yeni imzalanmış ortak sertifika, sertifika isteği veritabanında depolanır.
- İstek sahibi onaylanana veya iptal edilene kadar istek durumunu yoklayabilirler. İstek onaylanmışsa, özel anahtar ve sertifika, OPC UA uygulamasının sertifika deposuna indirilip yüklenebilir.
- İstek sahibi artık istek veritabanından gereksiz bilgileri silme isteğini kabul edebilir. 

İmzalı bir sertifikanın kullanım ömrü boyunca, bir uygulama silinebilir veya bir anahtar tehlikeye çıkabilir. Böyle bir durumda, CA yöneticisi şunları yapabilir:
- Uygulamanın bekleyen ve onaylanmış tüm sertifika isteklerini de silen bir uygulamayı silin. 
- Yalnızca bir anahtar yenilenirse veya güvenliği tehlikeye atılırsa yalnızca tek bir sertifika isteğini silin.

Artık tehlikede onaylanan ve kabul edilen sertifika istekleri silindi olarak işaretlenir.

Bir yönetici, veren CA CRL 'YI düzenli olarak yenileyebilir. Yenileme sırasında, tüm silinen sertifika istekleri iptal edilir ve sertifika seri numaraları CRL iptal listesine eklenir. İptal edilen sertifika istekleri iptal edildi olarak işaretlenir. Acil olaylarda, tek sertifika istekleri de iptal edilebilir.

Son olarak, güncelleştirilmiş CRL 'Ler katılan OPC UA istemcilerine ve sunucularına dağıtım için kullanılabilir.

## <a name="opc-vault-iot-edge-module"></a>OPC Kasası IoT Edge modülü
Bir fabrika ağı küresel bulma sunucusunu desteklemek için OPC Kasası modülünü kenarda dağıtabilirsiniz. Bunu yerel bir .NET Core uygulaması olarak çalıştırın veya bir Docker kapsayıcısında başlatın. Geçerli OPC UA .NET Standard yığınında auth2 kimlik doğrulama desteğinin bulunmaması nedeniyle OPC Kasası Edge modülünün işlevselliği bir okuyucu rolüyle sınırlıdır. OPC UA GDS standart arabirimini kullanarak, bir kullanıcının Edge modülünden mikro hizmete özellikleri alınamaz.

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC Kasası mimarisini öğrendiğinize göre şunları yapabilirsiniz:

> [!div class="nextstepaction"]
> [OPC Kasası oluşturma ve dağıtma](howto-opc-vault-deploy.md)
