---
title: Azure portal kullanarak Azure Event Grid iş ortağı olarak ekleme
description: Bir Azure Event Grid iş ortağı eklemek için Azure portal kullanın.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050961"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Azure portal kullanarak Azure Event Grid iş ortağı olarak ekleme
Bu makalede, olay yayımcıları veya iş ortakları olarak da bilinen üçüncü taraf SaaS sağlayıcılarının, hizmetlerinden olayları yayımlayabilmesi ve bu olayların son kullanıcılar tarafından nasıl tüketildiği Event Grid açıklanmaktadır.

> [!IMPORTANT]
> Iş ortağı olayları hakkında bilginiz yoksa, anlaşılması gereken önemli kavramların ayrıntılı bir özeti için bkz. [Iş ortağı olaylarına genel bakış](partner-events-overview.md) ve bu makaledeki adımları izleyin.

## <a name="onboarding-process-for-event-publishers-partners"></a>Olay yayımcıları (iş ortakları) için ekleme işlemi
Bir Nutshell 'de, hizmet olaylarının kullanıcılar tarafından tüketilebilmesi için genellikle aşağıdaki işlemi içerir:

1. Sonraki adımlara geçmeden önce, Event Grid hizmet ekibine bir iş ortağı olarak **Ilgilendiğinizi Iletişim kurun** .
1. **Kayıt** oluşturarak iş ortağı konu türü oluşturun. 
1. **Ad alanı** oluşturun.
1. Bir **olay kanalı** ve **iş ortağı konusu** oluşturun (tek adım).
1. Iş ortağı olayları işlevselliğini uçtan uca test edin.

Adım #4 için ne tür bir kullanıcı deneyimi sağlamak istediğinize karar vermeniz gerekir. Aşağıdaki seçenekler mevcuttur:
- Bir olay kanalı ve ilgili iş ortağı konusu oluşturmak için SDK ve/veya REST API kullanarak etki alanınız altında barındırılan, genellikle bir Web grafik kullanıcı arabirimi (GUI) deneyimi sağlar. Bu seçenekle, kullanıcıdan bir iş ortağı konusu oluşturacağınız abonelik ve kaynak grubunu isteyebilirsiniz.
- Olay kanalını ve ilişkili iş ortağı konusunu oluşturmak için Azure portal veya CLı kullanın. Bu seçenekle, kullanıcının Azure aboneliğine bir iş ortağı konusu oluşturacağınız bir şekilde ve kaynak grubunda Get olmanız gerekir. 

Bu makalede Azure portal kullanarak Azure Event Grid iş ortağı olarak nasıl ekleneceği gösterilmektedir. 

> [!NOTE]
> İş ortağı konu türünü kaydetme isteğe bağlı bir adımdır. Bir iş ortağı konu türü oluşturmanız gerektiğine karar vermenize yardımcı olmak için bkz. [olay yayımcısı tarafından yönetilen kaynaklar](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>İlginizi çeken bir iş ortağı olarak iletişim kurun
[Bu formu](https://aka.ms/gridpartnerform) doldurun ve Event Grid ekibine başvurun [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Iş ortağı olaylarının kullanım örnekleri, kişilas, ekleme işlemi, işlevsellik, fiyatlandırma ve daha fazlası hakkında ayrıntılı bilgi sağlayan bir konuşmamız olacak.

## <a name="prerequisites"></a>Önkoşullar
Kalan adımları tamamlayabilmeniz için, şunları yaptığınızdan emin olun:

- Azure aboneliği. Bir tane yoksa, başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .
- Bir Azure [kiracısı](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="register-a-partner-topic-type-optional"></a>İş ortağı konu türünü kaydetme (isteğe bağlı)
1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Sol gezinti bölmesinden **tüm hizmetler** ' i seçin, sonra arama çubuğuna **Event Grid iş ortağı kayıtları** yazın ve seçin. 
1. **Event Grid Iş ortağı kayıtları** sayfasında, araç çubuğunda **+ Ekle** ' yi seçin. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="İş ortağı kayıt bağlantısı ekle":::
1. **Iş ortağı konu türü kaydı oluştur-temel bilgiler** sayfasında, aşağıdaki bilgileri girin: 
    1. **Proje ayrıntıları** bölümünde, şu adımları izleyin:
        1. Azure **aboneliğinizi** seçin. 
        1. Var olan bir Azure **kaynak grubunu** seçin veya yeni bir kaynak grubu oluşturun. 
    1. **Kayıt ayrıntıları** bölümünde, şu adımları izleyin:
        1. **Kayıt adı** için kayıt için bir ad girin. 
        1. **Kuruluş adı** için kuruluşunuzun adını girin. 
    1. **Iş ortağı kaynak türü** bölümünde, **iş ortağı konu oluştur** sayfasında görüntülenecek kaynak türü hakkındaki ayrıntıları girin: 
        1. **Iş ortağı kaynak türü adı** için kaynak türü adını girin. Bu, Azure aboneliğinizde oluşturulacak iş ortağı konusunun türüdür. 
        2. **Görünen ad** için, iş ortağı konusu (kaynak) türü için Kullanıcı dostu bir görünen ad girin. 
        3. **Kaynak türü için bir açıklama** girin. 
        4. **Senaryo için bir açıklama** girin. Kaynaklarınızın iş ortağı konularının kullanılabileceği yolları veya senaryoları açıklamalıdır.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="İş ortağı kaydı oluştur":::            
1. Ileri ' yi seçin. sayfanın en altındaki **özel hizmet** . **Iş ortağı kaydı oluştur** sayfasının **müşteri hizmeti** sekmesinde, abone kullanıcıların Olay kaynağıyla ilgili bir sorun olması durumunda sizinle iletişim kurmak için kullanacağı bilgileri girin:
    1. **Telefon numarasını** girin.
    1. Telefon numarası için **uzantı** girin.
    1. Bir destek web sitesi **URL 'si** girin. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="İş ortağı kaydı oluşturma-müşteri hizmeti":::        
1. **İleri:** sayfanın en altındaki Etiketler ' i seçin. 
1. **Etiketler** sayfasında, aşağıdaki değerleri yapılandırın. 
    1. Eklemek istediğiniz etiket için bir **ad** ve bir **değer** girin. Bu adım **isteğe bağlıdır**. 
    1. Kayıt (iş ortağı konu türü) oluşturmak için sayfanın altındaki **gözden geçir + oluştur** ' u seçin.

## <a name="create-a-partner-namespace"></a>İş ortağı ad alanı oluşturma

1. Azure portal sol gezinti menüsünde **tüm hizmetler** ' i seçin, sonra arama çubuğuna **Event Grid Iş ortağı ad alanlarını** yazın ve ardından listeden seçin. 
1. **Event Grid Iş ortağı ad alanları** sayfasında, araç çubuğunda **+ Ekle** ' yi seçin. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="İş ortağı ad alanları-bağlantı ekle":::
1. **Iş ortağı ad alanı oluşturma-temel bilgiler** sayfasında aşağıdaki bilgileri belirtin.
    1. **Proje ayrıntıları** bölümünde aşağıdaki adımları uygulayın: 
        1. Bir Azure **aboneliği** seçin.
        1. Var olan bir **kaynak grubunu** seçin veya bir kaynak grubu oluşturun. 
    1. **Ad alanı ayrıntıları** bölümünde aşağıdaki adımları uygulayın:
        1. Ad alanı için bir **ad** girin. 
        1. Ad alanı için bir **konum** seçin. 
    1. **Kayıt ayrıntıları** bölümünde, ad alanını bir iş ortağı kaydıyla ilişkilendirmek için aşağıdaki adımları uygulayın. 
        1. İş ortağı kaydının mevcut olduğu **aboneliği** seçin. 
        1. İş ortağı kaydını içeren **kaynak grubunu** seçin. 
        1. Aşağı açılan listeden **iş ortağı kaydını** seçin.
    1. **İleri:** sayfanın en altındaki Etiketler ' i seçin.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="İş ortağı ad alanı oluşturma-temel bilgiler sayfası":::
1. **Etiketler** sayfasında Etiketler ekleyin (isteğe bağlı).
    1. Eklemek istediğiniz etiket için bir **ad** ve bir **değer** girin. Bu adım **isteğe bağlıdır**.
    1. Sayfanın alt kısmındaki **gözden geçir + oluştur** ' u seçin.         
1. **Gözden geçir + oluştur** sayfasında, ayrıntıları gözden geçirin ve **Oluştur**' u seçin. 

## <a name="create-an-event-channel"></a>Olay kanalı oluşturma
> [!IMPORTANT]
> Kullanıcılarınızın sahip olacağı ve yöneteceği bir iş ortağı konu başlığı oluşturmak için, kullanıcıdan bir Azure aboneliği, kaynak grubu, konum ve iş ortağı konu adı istemeniz gerekir.

1. Oluşturduğunuz ad alanının **genel bakış** sayfasına gidin. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="İş ortağı ad alanı-genel bakış sayfası":::
    partner-namespace-overview.png
1. Araç çubuğunda **+ olay kanalı** ' nı seçin. 
1. **Olay kanalı oluşturma-temel bilgiler** sayfasında, aşağıdaki bilgileri belirtin. 
    1. **Kanal ayrıntıları** bölümünde şu adımları uygulayın:
        1. **Olay kanalı adı** için olay kanalı için bir ad girin. 
        1. **Kaynağı** girin. Kaynak için uygun bir değer hakkında fikir edinmek için bkz. [bulut olayları 1,0 belirtimleri](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) . Ayrıca bkz. [Bu bulut olayları şeması örneği](cloud-event-schema.md#sample-event-using-cloudevents-schema).
    1. **Hedef ayrıntıları** bölümünde, bu olay kanalı için oluşturulacak hedef iş ortağı konusunun ayrıntılarını girin. 
        1. İş ortağı konusunun oluşturulacağı **aboneliğin kimliğini** girin. 
        1. İş ortağı konu kaynağının oluşturulacağı **kaynak grubunun adını** girin. 
        1. **İş ortağı konusu için bir ad** girin. 
    1. **İleri:** sayfanın en altındaki filtreler ' i seçin. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Olay kanalı oluşturma-temel bilgiler sayfası":::
1. **Filtreler** sayfasında filtre ekleyin. aşağıdaki adımları uygulayın:
    1. Her olayın özniteliklerini filtreleyin. Yalnızca tüm filtrelerle eşleşen olaylar alınır. En fazla 25 filtre belirlenebilir. Karşılaştırmalar büyük/küçük harfe duyarlıdır. Filtreler için kullanılan geçerli anahtarlar olay şemasına göre farklılık gösterir. Aşağıdaki örnekte,,, `eventid` `source` `eventtype` ve `eventtypeversioin` anahtarlar için kullanılabilir. Ayrıca, `.` iç içe geçme işleci olarak kullanarak veri yükünün içinde özel özellikler de kullanabilirsiniz. Örneğin: `data` , `data.key` , `data.key1.key2` .
    1. Ileri ' yi seçin: sayfanın en altındaki **ek özellikler** . 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Olay kanalı oluşturma-Filtreler sayfası":::
        create-event-channel-filters-page.png
1. **Ek özellikler** sayfasında, **iş ortağı konusu için** bir **sona erme süresi** ve açıklama ayarlayabilirsiniz. 
    1. **Sona erme zamanı** , konunun ve ilişkili olay kanalının müşteri tarafından etkinleştirilmediğinde otomatik olarak silineceği süredir. Bir zaman sağlanmazsa varsayılan yedi gün kullanılır. Kendi sona erme süresini belirtmek için onay kutusunu seçin. 
    1. Bu konu, Kullanıcı tarafından oluşturulmamış bir kaynak olduğundan, bir **Açıklama** kullanıcının bu konunun yapısını anlamasına yardımcı olabilir. Hiçbiri ayarlanmamışsa genel bir açıklama sağlanacaktır. Kendi iş ortağı konu açıklamanızı ayarlamak için onay kutusunu seçin. 
    1. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Olay kanalı oluşturma-ek özellikler sayfası":::
1. **Gözden geçir + oluştur** sayfasında ayarları gözden geçirin ve **Oluştur** ' u seçerek olay kanalını oluşturun. 

## <a name="next-steps"></a>Sonraki adımlar
- [İş ortağı konularına genel bakış](./partner-events-overview.md)
- [İş ortağı konuları ekleme formu](https://aka.ms/gridpartnerform)
- [Auth0 iş ortağı konusu](auth0-overview.md)
- [Auth0 iş ortağı konusunu kullanma](auth0-how-to.md)