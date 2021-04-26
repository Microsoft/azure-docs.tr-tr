---
title: Etkin bağlayıcı deneyimleri için yönergeler
description: Azure uzamsal bağlayıcıları kullanarak bağlantıları etkili bir şekilde oluşturup bulmaya yönelik yönergeler ve önemli noktalar.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95487512"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Azure uzamsal bağlayıcıları kullanarak etkin bir bağlantı deneyimi oluşturma

Bu makalede, Azure uzamsal bağlayıcıları kullanarak bağlantıları etkili bir şekilde oluşturmanıza ve bulmanıza yardımcı olacak yönergeler ve konular sunulmaktadır.

## <a name="anchor-improvement-over-time"></a>Zamana göre geçiş geliştirme

Azure uzamsal bağlayıcılarla, her bir tutamacı bulduğunuzda, gelecekteki bulma işlemlerinin kalitesini artırmaya çalışıyoruz. Bunu, aradığımız bağlayıcıların görsel bilgilerini artırmak için toplanan ortam verilerini kullanarak yaptık. Bu işlem, çalışan kapsamında çalışır ve ortamınız için optimize etmek üzere Azure uzamsal bağlayıcı hizmeti tarafından bir çevrimdışı iyileştirme çalıştırmıştır. Her işlem sırasında toplanan ek veriler, ortamı daha güçlü bir şekilde öğrenmeyi oluşturur. Bu, kaliteyi artırır ve ortam değişikliği, zaman geçme ve farklı açılardan ve perspektiflerden çıpası arayan kullanıcılar için bağlantıları daha iyi bulmanıza olanak sağlar.

## <a name="good-anchors"></a>İyi bağlantılar

Azure uzamsal bağlantıları zaman içinde bağlayıcı kalitesini artırmaya çalışırken, iyi bir çıpası oluşturmak için Kullanıcı deneyiminizdeki (UX) kullanıcıları eğitmekte veya buna göre yatırım yapmak da önemlidir. Önde gelen bağlantılar oluşturmaya yatırım yaparak, son kullanıcıların bağlantıları güvenilir bir şekilde bulmasına yardımcı olursunuz:

- Farklı cihazlarda.
- Çeşitli zamanlarda.
- Farklı aydınlatma koşullarında.
- Alan içindeki istenen perspektiflerden.

## <a name="static-and-dynamic-locations"></a>Statik ve dinamik konumlar

Bağlantı deneyimini tasarlamanın bir parçası konumları seçmektir. Konumlar statik olacak ve alanın yöneticisi tarafından tanımlanmalıdır mi? Ya da bunlar dinamik ve Kullanıcı tarafından tanımlanacaktır mi?

Retail Store Manager, kullanıcıları ziyaret etmek için bir statik mağaza deneyimi isteyebilir. Ancak, karma gerçeklik panosu oyununun geliştiricisi, kullanıcıların nerede oynamasını seçebilmesine izin vermek istiyor.

Statik konumlar için, yöneticilere, alanı uygun bağlayıcılarla bir süre boyunca harcamayı öğretebilir.

Dinamik konumlar için, UX 'inizdeki kullanıcılara iyi bağlantılar oluşturma konusunda nasıl eğitim veya rehberlik ettiğiniz hakkında düşünmeniz gerekir.

## <a name="stable-visual-features"></a>Kararlı görsel özellikler

Karma Gerçeklik ve genişletilmiş gerçeklik cihazlarında kullanılan görsel izleme sistemleri, ortamın görsel özelliklerini kullanır. En güvenilir deneyimi almak için:

- Kararlı görsel özellikleri olan konumlarda (yani, genellikle değiştirolmayan Özellikler) *bağlayıcı oluşturun.*

- Ayrım özelliği olmayan büyük boş yüzeyler üzerinde *bağlayıcı oluşturmayın.*

- Yüksek oranda yansıtmalı malzemelerde *bağlayıcı oluşturmayın.*

- Kuevcil hayvan veya duvar kağıdı gibi, düzenin tekrarlandığı yüzeyler üzerinde *bağlayıcı oluşturmayın.*

![Bağlantılar için iyi bir ortam örnekleri ve bağlayıcı için hatalı ortam](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Çeşitli görüntüleme perspektifi

Bir tutturucu oluştururken, daha sonra bağlayıcıyı bulmaya çalışacak kişileri düşünün.

Örneğin, iki kapıya sahip olan bir odanın ortasında yer alan bir yer işareti düşünün. Büyük olasılıkla kullanıcıların her iki kapıdan Oda girmesine izin vermek istersiniz. Bağlayıcıyı oluştururken, konumunu her iki doorways de taramalısınız. Kullanıcıların, her iki kapıdan bağlantı bulabilmesi için ortam verilerini bağlayıcının etrafında yakalamak üzere perspektifleri değiştirirsiniz.

Genel olarak, bir tutturucu oluştururken onu bulmaya çalışacak kişilerin perspektiflerinden tarayın. Bu nedenle, sanal içeriği bir mekan sulpture üzerine yerleştiriyorsanız, bağlayıcıyı oluştururken, tarama sırasında Sculpture 'in etrafında dolaşmayı mantıklı hale getirir. Yer işareti, bir odanın köşesindedir, kendisine yaklaşımda yalnızca bir yön vardır. Bu bağlayıcıyı oluştururken, bu perspektiften yalnızca tarama yapabilirsiniz.

## <a name="multiple-anchors"></a>Birden çok bağlayıcı

Aydınlatma, bir uygulamanın algıladığı görsel özelliklerde bir farklılık yapabilir. Güçlü doğal hafif bir şekilde oluşturulan çıpaların yapay ışık halinde bulması zor olabilir ve bunun tam tersi de geçerlidir.

Bu sorunla karşılaşırsanız, iki bağlayıcı oluşturmaya yardımcı olabilir. Aynı noktada, gün içinde ve yapay hafif bir bağlantı oluşturun. Uygulamanız daha sonra her iki bağlayıcı için sorgu oluşturabilir. Bağlantı noktası bulunduğunda, uygulamanın bağlantı noktası için bir poz olur.

Benzer şekilde, çoğu nesne hareket ettiğinden görsel özelliklerin değişmediği ortamlarda birden çok bağlayıcı yardımcı olabilir. Bir bağlayıcı, ortamdaki önemli değişiklikler nedeniyle bulmak için çok zor hale geldiğinde, bağlayıcıyı yeni bir ile değiştirebilirsiniz. Bu işlemi Örneğin, düzenin her birkaç ayda bir yenilenen Retail Store 'da yapabilirsiniz.

## <a name="targets-and-rooms"></a>Hedefler ve Odalar

Birçok durumda, bağlayıcı uygulamanızın deneyimine yönelik bir giriş noktasıdır. Bu adımı hızla ve güvenilir bir şekilde almak isteyeceksiniz, böylece kullanıcılar deneyiminizi girebilir. Kullanıcıların bağlayıcılarınızı nasıl bulacağız önemli bir tasarım adımındaki harcama süresi. İki geniş senaryo açısından çıpası bulmayı düşünmek yararlı olur: *hedefler* ve *Odalar*.

### <a name="targets"></a>Targets

Hedef senaryoda, bir bağlayıcının konumu iyi bilinmektedir. Örneğin, kurgusal bir karma gerçeklik boyama uygulamasında, bir Kullanıcı duvara sanal bir tuval koyar. Odadaki diğer kullanıcılara, bağlayıcıyı bulmak ve deneyime başlamak için, cihazlarını duvardaki aynı yere işaret etmelerini söyler.

Bir hedef senaryoya başka bir örnek, "anlaşmalar için tarama" okuyan bir kafeterde oturum açma olabilir. Kahve dükkanı buraya bir bağlantı yerleştirdi. Kullanıcılar işareti taradıklarında, çıpası bulur ve kafeli ile ilgili anlaşmalar bulmak için genişletilmiş gerçeklik deneyimini girer.

Hedef senaryoda fotoğraflar yardımcı olabilir. Kullanıcıları cihazındaki hedeflenen hedefin fotoğrafını göstermezseniz, gerçek dünyada nelerin taranacağını hızlıca tanımlayabilirler. Örneğin, kullanıcıların, GPS kullanarak amaçlanan bir hedefin genel alanı içine ulaşmasını sağlayabilirsiniz. Kullanıcı geldiğinde, uygulamanız hedefin fotoğrafını gösterir. Kullanıcı alana bakar, hedefi bulur ve bağlayıcıyı tarar.

![Kullanıcının mobil cihazında hedefin fotoğrafını gösteren bir bağlayıcının çizimi](./media/start-here-edit.png)

### <a name="rooms"></a>Odaları

Oda senaryosunda, kullanıcılar burada bir yer işareti olduğunu bilmeden bir boşluk girer. Kullanıcılar, alanı cihazlarıyla tarar ve bağlayıcıyı hızlı bir şekilde bulur.

Bu deneyim genellikle çeşitli görüntüleme perspektifleriyle anlatıldığı gibi iyi şekilde yapılan bağlantılar oluşturmanızı gerektirir. Bağlayıcıyı oluştururken birçok perspektiften odayı taradıysanız, kullanıcılar bulmayı denediğinde neredeyse her yerde tarama yapabilir.

![Kullanıcının bir bağlantı bulmak için bir odayı nasıl tarayamayacağını gösteren resim](./media/scan-room.png)

Temelde, daha sonra, bağlayıcıyı daha sonra tarayabilmesi ve bulabilmesi için bağlayıcıyı oluştururken alanı taramanın daha fazla zaman harcamanız gerekir. Deneyiminizi oluştururken bu önemli ticareti göz önünde bulundurmanız gerekir.

Daha önce tartışdığımız karma gerçeklik boyama uygulamasının örneği Oda senaryosu olarak iyi çalışmaz. Burada, bağlayıcıyı yerleştiren Kullanıcı başka bir deneyime hızla katılabilmenizi istiyor. Kullanıcılar, odanın iyi taranmasına kadar deneyimi başlatmak istemelidir. Tüm kullanıcılar, bağlayıcıları bulmak için cihazlarını tam olarak nereye işaret ettiğinden haberdar olduğu için, bu örnek bir hedef senaryo olarak daha iyi çalışmaktadır.

## <a name="anchor-location"></a>Yer işareti konumu

Görsel izleme sistemleri, bir ortamdaki görsel özellikleri kullanır. Taramanın içerdiği daha fazla görsel özelliği, bir tutturucu bulma olasılığının daha yüksektir.

Ortamda yararlı bir tarama teşvik eden bir UX oluşturmak için bu bölümdeki genel yönergeleri izleyin.

İlk olarak, Kullanıcı birkaç saniye içinde bir tutturucu bulamazsa, uygulama, kullanıcıların cihazı daha fazla perspektifle yakalamaya taşımasını teşvik etmelidir. Bu uygulama, kullanıcıların, bağlayıcıyı daha fazla perspektifden tarayabilmesi için ortama taşımasını de teşvik edebilir. Cihazın gördüğü daha fazla özellik perspektifi, bir bağlayıcının bulunduğu olasılığını artırdıkça daha iyi hale gelir ve ayrıca bağlayıcının kalitesini artırmak için kullanılacak daha fazla ortam verisi toplar.

Hedef senaryolar için kullanıcıdan farklı perspektiflerden görüntülemek üzere hedefin etrafında hareket etmesini isteyin. Diğer bir deyişle, bağlayıcı bulunana kadar kullanıcıdan hedefi yeni perspektiflerden yakalamasını isteyin.

Oda senaryolarında, kullanıcıdan odayı yavaş taramasını isteyin. Örneğin, kullanıcıdan 180 derece veya hatta 360 derece yakalamayı aramasını isteyin. Ya da kullanıcıdan yeni bir perspektiften odayı görüntülemesini isteyin.

En anlamlı Yöntem, odanın üzerinde tarama yapmak için kullanılır. Odadaki bir tarama, ortamın daha fazla görsel özelliklerini, örneğin yakın bir duvar taramasından daha fazla yakalar. Yakın bir duvar taraması, ortamın birçok yararlı görsel özelliğini yakalayamaz.

Bir tutturucu aranırken cihazı arka tarafa dışarıdan yana taşımak yararlı değildir. Bu yalnızca aynı perspektiften aynı noktaları yakalar.

## <a name="experience-tests"></a>Deneyim testleri

Bu makalede, genel yönergeleri tartıştık. Uzamsal bağlayıcılarla, gerçek dünya ile etkileşime geçen uygulamalar yazıyoruz. Bu nedenle, gerçek ortamlarda uygulamanızın bağlantı senaryolarını test etmek için zaman ayırabilmeniz gerekir. Bu, özellikle kullanıcılarınızın uygulamayı kullanmasını istediğiniz yeri temsil eden ortamlar için geçerlidir.
