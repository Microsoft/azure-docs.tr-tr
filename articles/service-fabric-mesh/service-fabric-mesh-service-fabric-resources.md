---
title: Azure Service Fabric kaynak modeline giriş
description: Service Fabric kafes uygulamalarını tanımlamaya yönelik basitleştirilmiş bir yaklaşım olan Service Fabric kaynak modeli hakkında bilgi edinin.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626918"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric kaynak modeline giriş

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Service Fabric kaynak modeli, bir Service Fabric kafes uygulamasını oluşturan kaynakları tanımlamaya yönelik basit bir yaklaşım açıklar. Tek tek kaynaklar herhangi bir Service Fabric ortamına dağıtılabilir.  Service Fabric kaynak modeli ayrıca Azure Resource Manager modeliyle uyumludur. Aşağıdaki kaynak türleri şu anda bu modelde destekleniyor:

- Uygulamalar ve hizmetler
- Ağlar
- Ağ geçitleri
- Gizlilikler ve gizlilikler/değerler
- Birimler

Her kaynak, bir kaynak dosyasında bildirimli olarak tanımlanır. Bu, kafes uygulamasını açıklayan basit bir YAML veya JSON belgesi, ve Service Fabric platformu tarafından sağlanır.

## <a name="applications-and-services"></a>Uygulamalar ve hizmetler

Uygulama kaynağı, bir kafes uygulamasının dağıtım, sürüm oluşturma ve yaşam süresi birimidir. Mikro hizmeti temsil eden bir veya daha fazla hizmet kaynaklarından oluşur. Her hizmet kaynağı, sırasıyla kod paketiyle ilişkili kapsayıcı görüntüsünü çalıştırmak için gereken her şeyi tanımlayan bir veya daha fazla kod paketinden oluşur.

![Uygulamalar ve hizmetler][Image1]

Bir hizmet kaynağı aşağıdakileri bildirir:

- Kapsayıcı adı, sürümü ve kayıt defteri
- Her kapsayıcı için gereken CPU ve bellek kaynakları
- Ağ uç noktaları
- Ağlar, birimler ve parolalar gibi diğer kaynaklara başvurular 

Hizmet kaynağının bir parçası olarak tanımlanan tüm kod paketleri, bir grup olarak birlikte dağıtılır ve etkinleştirilir. Hizmet kaynağı ayrıca hizmetin kaç örneğinin çalıştırılacağını ve ayrıca diğer kaynaklara (örneğin, ağ kaynağı) bağlı olduğunu da açıklar.

Bir kafes uygulaması birden fazla hizmetten oluşuyorsa, bunların aynı düğümde birlikte çalıştırılması garanti edilmez. Ayrıca, uygulamanın yükseltilmesi sırasında tek bir hizmeti yükseltmenin başarısız olması, tüm hizmetlerin önceki sürümlerine geri alınmasına neden olur.

Daha önce olduğu gibi, her bir uygulama örneğinin yaşam döngüsü bağımsız olarak yönetilebilir. Örneğin, bir uygulama örneği diğer uygulama örneklerinden bağımsız olarak yükseltilebilir. Genellikle, bir uygulamadaki hizmet sayısını, bir uygulamaya yerleştirdiğiniz daha fazla hizmet olarak, her hizmeti bağımsız olarak yönetmek için daha zor hale gelir.

## <a name="networks"></a>Ağlar

Ağ kaynağı, bağımlılığı olarak başvurabilen bir uygulama veya hizmet kaynağıyla bağımsız olarak tek tek dağıtılabilir kaynaktır. Uygulamalarınız için bir ağ oluşturmak üzere kullanılır. Farklı uygulamalardan gelen birden çok hizmet aynı ağın parçası olabilir.  Daha fazla bilgi için [Service Fabric kafes uygulamalarında ağ iletişimi](service-fabric-mesh-networks-and-gateways.md)konusunu okuyun.

> [!NOTE]
> Geçerli Önizleme yalnızca, uygulamalar ve ağlar arasındaki tek eşlemeyi destekler

![Ağ ve ağ geçidi][Image2]

## <a name="gateways"></a>Ağ geçitleri
Ağ Geçidi kaynağı iki ağı birbirine bağlar ve trafiği yönlendirir.  Ağ Geçidi, hizmetlerinizin dış istemcilerle iletişim kurmasını sağlar ve hizmetlerinize giriş sağlar.  Ağ Geçidi, aynı zamanda, var olan sanal ağınızla ağ uygulamanızı bağlamak için de kullanılabilir. Daha fazla bilgi için [Service Fabric kafes uygulamalarında ağ iletişimi](service-fabric-mesh-networks-and-gateways.md)konusunu okuyun.

![Ağ ve ağ geçidi][Image2]

## <a name="secrets"></a>Gizli Diziler

Gizli dizi kaynakları, bağımlılığı olarak başvurabilecek bir uygulama veya hizmet kaynağıyla bağımsızdır. Uygulamalarınıza gizli dizileri güvenli bir şekilde sunmak için kullanılır. Farklı uygulamalardan birden çok hizmet aynı gizli dizi değerlerine başvurabilir.

## <a name="volumes"></a>Birimler

Kapsayıcılar genellikle geçici diskleri kullanılabilir hale getirir. Ancak geçici diskler kısa ömürlü olduğundan, yeni bir geçici disk alır ve bir kapsayıcı kilitlenirse bilgileri kaybedersiniz. Ayrıca, diğer kapsayıcılarla geçici disklerde bilgi paylaşmak da zordur. Birimler, durumu kalıcı hale getirmek için kullanabileceğiniz kapsayıcı örneklerinizin içine bağlanmış olan dizinlerdir. Birimler, genel amaçlı dosya depolaması sağlar ve normal disk g/ç dosya API 'Lerini kullanarak dosyaları okumanızı/yazmanızı sağlar. Birim kaynağı, bir dizinin nasıl takıldığını ve BT için depolama (Azure dosyaları birimi veya Service Fabric güvenilir birim) ile ilgili bir dizin oluşturmayı tanımlamaya yönelik bildirime dayalı bir yoldur.  Daha fazla bilgi için [depolama durumunu](service-fabric-mesh-storing-state.md#volumes)okuyun.

![Diyagram, hem Service Fabric güvenilir birime hem de çoğaltılan bir yerel diske ve Azure dosyaları birimine, ardından ağ depolama alanına akan bir disk birimine bir hizmet akışını gösterir.][Image3]

## <a name="programming-models"></a>Programlama modelleri
Hizmet kaynağı yalnızca kaynak ile ilişkili kod paketleri içinde başvurulan bir kapsayıcı görüntüsünün çalıştırılmasını gerektirir. Herhangi bir dilde yazılmış herhangi bir kodu, ağ için belirli API 'Leri bilmeniz veya Service Fabric kullanmak zorunda kalmadan, kapsayıcının içindeki herhangi bir çerçeveyi kullanarak çalıştırabilirsiniz. 

Uygulama kodunuz Service Fabric Ağ dışından bile taşınabilir kalır ve uygulama dağıtımlarınız, hizmetlerinizi uygulamak için kullanılan dil veya çerçeveden bağımsız olarak tutarlı kalır. Uygulamanızın ASP.NET Core, git veya yalnızca bir dizi işlem ve komut dosyası olup olmadığı, Service Fabric kafes kaynak dağıtım modeli aynı kalır. 

## <a name="packaging-and-deployment"></a>Paketleme ve dağıtım

Kaynak modeli temel alan Service Fabric kafes uygulamaları Docker Kapsayıcıları olarak paketlenir.  Service Fabric ağ, paylaşılan, çok kiracılı bir ortam ve kapsayıcılar size yüksek düzeyde yalıtım sağlar.  Bu uygulamalar JSON biçimi veya YAML biçimi (daha sonra JSON 'a dönüştürülür) kullanılarak açıklanır. Azure Service Fabric ağı 'na bir kafes uygulaması dağıtıldığında, uygulamayı anlatmak için kullanılan JSON bir Azure Resource Manager şablonudur. Kaynaklar Azure kaynaklarıyla eşlenir.  Bir Service Fabric kümesine (tek başına veya Azure 'da barındırılan) bir kafes uygulaması dağıtıldığında, uygulamayı anlatmak için kullanılan JSON, Azure Resource Manager şablonuna benzer bir biçimdir.  Dağıtıldıktan sonra, ağ uygulamaları HTTP arabirimleri veya Azure CLı aracılığıyla yönetilebilir. 


## <a name="next-steps"></a>Sonraki adımlar 
Service Fabric ağ hakkında daha fazla bilgi edinmek için genel bakışı okuyun:
- [Service Fabric kafese bakış](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
