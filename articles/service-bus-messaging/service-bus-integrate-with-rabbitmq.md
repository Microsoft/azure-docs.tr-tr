---
title: Kbbitmq 'i Azure Service Bus ile tümleştirme
description: Kbbitmq 'ı Azure Service Bus ile tümleştirme hakkında adım adım kılavuz
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 71ea4dfcc164d5b8a8ba8bb411d529ce58a68f4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933747"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Kbbitmq 'i Azure Service Bus ile tümleştirme

Bu kılavuzda, Kbbitmq ' den Azure Service Bus ileti gönderme hakkında bilgi edineceğiz.

Aşağıda, bu özellikleri kullandığımız birkaç senaryo vardır:

- **Edge kurulumları**: Kbbitmq 'e ileti göndermekte olduğumuz, ancak bu iletileri daha fazla işleme için [Azure Service Bus](./service-bus-messaging-overview.md) iletmek istiyoruz, bu sayede birçok [Azure büyük veri özelliği](/azure/architecture/guide/architecture-styles/big-data)kullanabiliriz.
- **Karma bulut**: şirketiniz, yalnızca kendi mesajlaşma ihtiyaçları Için Kbıbitmq kullanan bir üçüncü taraf almış. Bunlar farklı bir bulutlardır. Azure 'a geçiş yaparken, Azure Service Bus ile kbbitmq ' i köprüleyerek daha önce veri paylaşmaya başlayabilirsiniz.
- **Üçüncü taraf tümleştirmesi**: üçüncü taraf, bir aracı olarak Kbbitmq kullanır ve bu verileri bize göndermek ister, ancak kuruluşumuz dışındaysanız. Bunlara, iletilerini iletebilecekleri sınırlı bir Azure Service Bus kuyruğu kümesine erişim sağlayan SAS anahtarı ile birlikte sağlayabiliriz.

Liste açık demektir, ancak bu kullanım durumlarının çoğunu, Kbbitmq 'yi Azure 'a köprüleyerek çözebiliriz.

İlk olarak, [buradan](https://azure.microsoft.com/free/) kaydolarak ücretsiz bir Azure hesabı oluşturmanız gerekir

Hesabınızda oturum açtıktan sonra, [Azure Portal](https://portal.azure.com/) gidin ve yeni bir Azure Service Bus [ad alanı](./service-bus-create-namespace-portal.md)oluşturun. Ad alanları, mesajlaşma bileşenlerinizin canlı olacağı, kuyruklar ve konular gibi kapsam kapsayıcılarıdır.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Yeni bir Azure Service Bus ad alanı ekleniyor

Azure portal ' de, yeni bir kaynak eklemek için büyük artı düğmesine tıklayın

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Kaynak oluşturma":::

Ardından, tümleştirme ' i seçin ve Azure Service Bus ' ye tıklayarak bir mesajlaşma ad alanı oluşturun:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Azure Service Bus 'ı seçin":::

Ad alanı bilgilerini girmeniz istenir. Kullanmak istediğiniz Azure aboneliğini seçin. Bir [kaynak grubunuz](../azure-resource-manager/management/manage-resource-groups-portal.md)yoksa yeni bir tane oluşturabilirsiniz.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Ad alanı oluşturma":::

`rabbitmq`İçin kullanın `Namespace name` , ancak istediğiniz herhangi bir şey olabilir. Ardından `East US` konum için ayarlayın. `Basic`Fiyat katmanı olarak seçin.

Hepsi de varsa, aşağıdaki onay ekranını görmeniz gerekir:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Ad alanı onayı oluştur":::

Sonra Azure portal geri döndüğünüzde, `rabbitmq` burada listelenen yeni ad alanınızı görürsünüz. Kendisine bir kuyruk ekleyebilmek için kaynağa erişmek üzere tıklayın.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Yeni ad alanı olan kaynak listesi":::

## <a name="creating-our-azure-service-bus-queue"></a>Azure Service Bus kuyruğumuzu oluşturma

Azure Service Bus ad alanınız olduğuna göre, sol taraftaki düğmeye tıklayın, bu `Queues` `Entities` nedenle yeni bir kuyruk ekleyebilirsiniz:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Sıra oluştur":::

Kuyruğun adı, `from-rabbitmq` iletilerin nereden geldiği hakkında bir anımsatıcı olarak olacaktır. Diğer tüm seçenekleri varsayılan olarak bırakabilirsiniz, ancak bunları uygulamanızın gereksinimlerine uyacak şekilde değiştirebilirsiniz.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Kbıbitmq Sholevel eklentisini etkinleştirme

Pbbitmq 'dan Azure Service Bus ileti göndermek için, kbbitmq ile paketlenmiş [Showlevel eklentisini](https://www.rabbitmq.com/shovel.html) kullanacağız. Bu komutla birlikte eklentiyi ve görsel arabirimini etkinleştirebilirsiniz:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Bu komutu kök olarak çalıştırmanız gerekebilir.

Şimdi, kbıbitmq 'yi Azure 'a bağlamak için gereken kimlik bilgilerini almak için zaman.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Kbbitmq Azure Service Bus 'a bağlanıyor

Kuyruğunuza yönelik bir [paylaşılan erişim ilkesi](../storage/common/storage-sas-overview.md) (SAS) oluşturmanız gerekir. bu nedenle, Kbbitmq bu iletiye ileti yayımlayabilir. SAS Ilkesi, kaynağınız ile hangi dış tarafın yapılmasına izin verileceğini belirtmenizi sağlar. Bu düşünce, Kbbitmq 'ın ileti gönderebilmesinde veya sıradan yönetmemelidir.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="SAS Ilkesi Ekle":::

Kutuyu işaret `Send` edin ve ardından `Create` SAS ilkenize sahip olmak için tıklayın.

İlke oluşturulduktan sonra, **birincil bağlantı dizesini** görmek için üzerine tıklayın. Bu uygulamayı, kbbitmq ile Azure Service Bus konuşmasına olanak verecek şekilde kullanacağız:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="SAS Ilkesini al":::

Bu bağlantı dizesini kullanabilmeniz için, bunu kbbitmq 'ın AMQP bağlantı biçimine dönüştürmeniz gerekir. [Bağlantı dizesi Dönüştürücüsü aracına](https://red-mushroom-0f7446a0f.azurestaticapps.net/) gidin ve Bağlantı dizenizi forma yapıştırın, Dönüştür ' e tıklayın. Korbbitmq Ready bağlantı dizesi alacaksınız. (Bu web sitesi, tarayıcınızda yerel olarak her şeyi çalıştırır, böylece Verileriniz hat üzerinden gönderilmez). [GitHub](https://github.com/videlalvaro/connstring_to_amqp)'da kaynak koduna erişebilirsiniz.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Bağlantı dizesini Dönüştür":::

Şimdi tarayıcılarımızda Kbbitmq yönetim eklentisini açın `http://localhost:15672/#/dynamic-shovels` ve adresine giderek `Admin -> Shovel Management` , bir kbıbitmq kuyruğundan Azure Service Bus kuyruğuna ileti göndermek için gereken yeni bir showlekinizi ekleyebilirsiniz.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Kbıbitmq Sholevel ekleme":::

Burada, Showsize `azure` 'yı çağırın ve `AMQP 0.9.1` kaynak protokolü olarak öğesini seçin. Ekran görüntüsünde, `amqp://` bize yerel bir kbıı MQ sunucusuna bağlanan varsayılan URI değeri sunuyoruz. Bunu geçerli dağıtımınız ile uyarlayadığınızdan emin olun.

Nesnelerin sıra tarafında, `azure` kuyruğunuzun adı olarak kullanabilirsiniz. Bu kuyruk yoksa, kbbitmq bunu sizin için oluşturur. Zaten var olan bir kuyruğun adını da seçebilirsiniz. Diğer seçenekleri varsayılan olarak bırakabilirsiniz.

Ardından, `destination` nesnelerin yanında protokol olarak öğesini seçin `AMQP 1.0` . `URI`Alanına, önceki adımdan aldığınız bağlantı dizesini girin, Azure Bağlantı dizenizi Kbbitmq biçimine dönüştürdünüz. Şu şekilde görünmelidir:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

`Address`Bu alanda **Azure Service Bus kuyruğunuzun** adını gireceğiz, bu durumda çağırılır `from-rabbitmq` . ' `Add Shovel` A tıklayın ve kurulum 'un ileti almaya başlamaya hazırlanmalıdır.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Kbbitmq ' den Azure Service Bus Iletileri yayımlama

Kbbitmq yönetim arabiriminde gideceğiz `Queues` , `azure` kuyruğu seçebilir ve panel için arama yapabilirsiniz `Publish message` . İletileri doğrudan kuyruğunuza yayımlamanıza olanak sağlayacak bir form görüntülenir. Bizim örneğimizde şu şekilde ekleyeceğiz `fist message` `Payload` `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="İlk iletiyi Yayımla":::

Azure 'a geri dönün ve kuyruğunuzu inceleyin. `Service Bus Explorer`Sol panelde ve ardından _göz at_ düğmesine tıklayın. Hepsi de varsa, kuyruğunuzun şimdi bir ileti olduğunu görürsünüz. Oley, tebrikler!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure Service Bus kuyruğu":::

Ancak, Oybbitmq ' den gönderdiğiniz iletinin olduğundan emin olalım. Bu `Peek` sekmeyi seçin ve `Peek` Sıradaki son iletileri almak için düğmeye tıklayın. İçeriğini incelemek için iletiye tıklayın. Aşağıda listelenen görüntüye benzer bir şey görmeniz gerekir `first message` .

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Kuyruk Özeti":::

## <a name="lets-recap"></a>Redelim

Tebrikler! Çok fazla elde edersiniz! İletilerinizi Kbbitmq 'dan Azure Service Bus 'e almak için yönetilenirsiniz, şimdi adımları geri alalım:

1. Azure Service Bus ad alanı oluşturma
2. Ad alanına kuyruk ekleme
3. Kuyruğunuza SAS Ilkesi ekleme
4. Kuyruk bağlantı dizesini al
5. Yönetim arabirimi & Kbbitmq sholevel eklentisini etkinleştirin
6. Azure Service Bus bağlantı dizesini kbbitmq 'ın AMQP biçimine Dönüştür
7. Kbbitmq ' ye yeni bir Showlevel ekleyin & Azure Service Bus bağlayın
8. İletileri Yayımla

Önceki adımları izleyerek, kuruluşunuzun Azure dışından tümleştirilmiş bölgelerini tümleştirmiş olursunuz. Sholevel eklentisi, Kbbitmq 'dan Azure Service Bus iletileri sevk etmeniz için izin verildi. Bu, artık güvenilen üçüncü tarafların uygulamalarını Azure dağıtımınıza bağlamaya izin verbileceğinizden bu yana büyük ölçüde avantaj sağlar.

Son olarak, mesajlaşma bağlantıları etkinleştirmek ve bu teknikle yeni bir tane açıyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Service Bus](./service-bus-messaging-overview.md) hakkında daha fazla bilgi edinin
- [Service Bus 'Da AMQP 1,0 desteği](./service-bus-amqp-overview.md) hakkında daha fazla bilgi edinin
