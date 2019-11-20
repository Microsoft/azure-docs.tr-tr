---
title: Azure Veri Kataloğu 'nda aramaları kaydetme ve veri varlıklarını sabitleme
description: Azure Veri Kataloğu 'nda, daha sonra kullanılmak üzere veri kaynaklarını ve veri varlıklarını kaydetmek için nasıl yapılır makalesi.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976813"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda aramaları kaydetme ve veri varlıklarını sabitleme
## <a name="introduction"></a>Giriş
Azure Veri Kataloğu, veri kaynağı bulma için yetenekler sağlar. Veri kaynaklarını bulmak ve amaçlanan amacını anlamak için kataloğu hızla arayıp filtreleyerek işle ilgili doğru verileri daha kolay bir şekilde bulmanızı sağlayabilirsiniz.

Ancak aynı verilerle düzenli olarak çalışmanız gerekiyorsa ne olacak? Ve siz ve diğer kullanıcılarınız, bilgilerinizi katalogdaki aynı veri kaynaklarına düzenli olarak katkıda bulunursa ne olacak? Bu durumlarda, aynı aramaları sürekli olarak vermek, verimsiz olabilir. Bu, kayıtlı aramanın ve sabitlenmiş veri varlıklarının yardımcı olduğu yerdir.

## <a name="saved-searches"></a>Kayıtlı aramalar
Veri kataloğunda kaydedilmiş bir arama, yeniden kullanılabilir, Kullanıcı başına arama tanımıdır. Arama terimleri, Etiketler ve diğer filtreler dahil olmak üzere bir arama tanımlayabilir ve ardından kaydedebilirsiniz. Daha sonra, arama ölçütleriyle eşleşen herhangi bir veri varlığı döndürmek için kaydedilmiş arama tanımını yeniden çalıştırabilirsiniz.

### <a name="create-a-saved-search"></a>Kayıtlı bir arama oluşturun
Kayıtlı bir arama oluşturmak için aşağıdakileri yapın:
1. Azure Veri Kataloğu portalında, **geçerli arama** penceresinde **Kaydet**' e tıklayın. 

    ![Geçerli arama ayarları kaydetme bağlantısı](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Yeniden kullanmak istediğiniz arama ölçütünü girin ve **Kaydet**' e tıklayın.

    ![Geçerli arama ayarları kayıtlı arama adı](./media/data-catalog-how-to-save-pin/02-name.png)

3. İstendiğinde, kayıtlı arama için bir ad girin. Anlamlı olan ve arama tarafından döndürülecek veri varlıklarını açıklayan bir ad seçin.

### <a name="manage-saved-searches"></a>Kayıtlı aramaları yönetme
Bir veya daha fazla aramayı kaydettikten sonra, **geçerli arama** kutusunun altında **kaydedilmiş aramalar** seçeneği görüntülenir. Liste genişletildiğinde, tüm kayıtlı aramalar görüntülenir.

 ![Veri Kataloğu-kayıtlı aramaların listesi](./media/data-catalog-how-to-save-pin/03-list.png)

Aşağıdakilerden birini yapın:

* Bir arama yürütmek için listede bir kayıtlı arama seçin.

* Kayıtlı bir arama için yönetim seçeneklerinin bir listesini görüntülemek için arama adının yanındaki aşağı oka tıklayın.

    ![Kayıtlı aramaları yönetme seçenekleri](./media/data-catalog-how-to-save-pin/04-managing.png)

* Kayıtlı arama için yeni bir ad girmek üzere **Yeniden Adlandır**' ı seçin. Arama tanımı değiştirilmez.

* Kayıtlı aramayı listenizden kaldırmak için **Sil**' i seçin ve sonra silmeyi onaylayın.

* Kayıtlı aramayı varsayılan arama olarak işaretlemek için **Varsayılan olarak kaydet**' i seçin. Azure Veri Kataloğu giriş sayfasından "boş" arama gerçekleştirirseniz, varsayılan aramanız yürütülür. Ayrıca, varsayılan arama olarak işaretlenen arama, **kayıtlı aramalar** listesinin en üstünde görüntülenir.

### <a name="organizational-saved-searches"></a>Kurumsal kayıtlı aramalar
Kuruluşunuzdaki tüm kullanıcılar, kendi kullanımları için aramaları kaydedebilir. Veri Kataloğu yöneticileri Ayrıca, kuruluştaki tüm kullanıcılar için aramaları kaydedebilir. Yöneticiler bir arama kaydederken, **Şirket seçeneği içinde bir paylaşımda** sunulur. Bu seçeneğin belirlenmesi, kuruluştaki tüm kullanıcılar için kayıtlı aramayı paylaşır.

 ![Veri Kataloğu-kurumsal kayıtlı aramalar](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Sabitlenmiş veri varlıkları
Kayıtlı aramalarla arama tanımlarını kaydedebilir ve yeniden kullanabilirsiniz. Arama tarafından döndürülen veri varlıkları, kataloğun içeriği değiştikçe zaman içinde değişebilir. Veri varlıklarını sabitlemeyi yaparken, bir arama kullanmaya gerek kalmadan kolayca erişim sağlamak için belirli veri varlıklarını açıkça tanımlayabilirsiniz.

Veri varlığını sabitleme basittir. Veri varlığını sabitlenmiş listenize eklemek için **sabitle** simgesine tıklamanız yeterlidir. Simge, kutucuk görünümündeki varlık kutucuğunun köşesinde ve Azure Veri Kataloğu portalındaki liste görünümünde bulunan en soldaki sütunda görüntülenir.

![Veri Kataloğu-veri-varlık PIN simgesi](./media/data-catalog-how-to-save-pin/05-pinning.png)

Veri varlığının sabitlenmesi eşit ölçüde basittir. Seçili varlık için ayarı değiştirmek üzere **Ayır** simgesine tıklamanız yeterlidir.

![Veri Kataloğu-veri varlık sabitleme simgesi](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Varlıklarım bölümü
Veri Kataloğu Portalı giriş sayfası, geçerli kullanıcıya ilgilendiğiniz varlıkları görüntüleyen **varlıklarım** bölümünü içerir. Bu bölüm hem sabitlenmiş varlıkları hem de kaydedilmiş aramaları içerir.

![Giriş sayfasındaki varlıklarım bölümü](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Özet
Azure Veri Kataloğu, ihtiyacınız olan veri kaynaklarını bulmayı kolaylaştıran yetenekler sağlar. böylece siz ve diğer kuruluş üyeleri, veri ararken daha az zaman harcayabilir ve bunlarla daha fazla süre çalışabilir. Kayıtlı aramalar ve sabitlenmiş veri varlıkları, kullanıcıların sürekli olarak çalıştıkları veri kaynaklarını kolayca belirleyebilmeleri için bu temel yeteneklere sahiptir.
