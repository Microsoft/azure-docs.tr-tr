---
title: Azure Sertifikalı cihaz programı-öğretici-cihaz ayrıntıları ekleme
description: Azure Sertifikalı cihaz portalında projenize cihaz ayrıntıları eklemek için adım adım kılavuz
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310436"
---
# <a name="tutorial-add-device-details"></a>Öğretici: cihaz ayrıntıları ekleme

Artık projenizi cihazınız için oluşturdunuz ve sertifika sürecini başlatmak için hazırsınız! İlk olarak, cihaz ayrıntılarınızı ekleyelim. Bunlar, müşterilerinizin Azure Sertifikalı cihaz kataloğu 'nda ve bir karar verdiklerinde satın alma için kullandıkları pazarlama ayrıntılarının görüntüleyebilecekleri teknik belirtimleri içerir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bileşenler ve bağımlılıklar özelliklerini kullanarak cihaz ayrıntıları ekleme
> * Cihazınız için bir başlangıç kılavuzu yükleyin
> * Müşterilerinizin cihazınızı satın almasını sağlamak için pazarlama ayrıntılarını belirtin
> * İsteğe bağlı olarak herhangi bir sektör sertifikası tanımla

## <a name="prerequisites"></a>Önkoşullar

* Oturumunuz açık olmalıdır ve [Azure Sertifikalı cihaz portalında](https://certify.azure.com)cihazınızda oluşturulmuş bir projeniz olmalıdır. Daha fazla bilgi için [öğreticiyi](tutorial-01-creating-your-project.md)görüntüleyin.
* Cihazınız için PDF biçiminde bir Başlarken kılavuzunuz olmalıdır. Sertifika programına ve tercih ettiğiniz dile bağlı olarak, kullanmanız için kullanabileceğiniz bir dizi başlangıç şablonu sağlıyoruz. Şablonlar, [Başlarken Şablonlarımızda](https://aka.ms/GSTemplate "Kullanmaya başlama şablonları") GitHub konumunda bulunur.

## <a name="adding-technical-device-details"></a>Teknik cihaz ayrıntıları ekleme

Proje sayfanızın ' giriş cihaz ayrıntıları ' adlı ilk bölümü, cihazınızın çekirdek donanım özellikleri hakkında bilgi sağlamanıza olanak tanır (Cihaz adı, açıklama, işlemci, işletim sistemi, bağlantı seçenekleri, donanım arabirimleri, sektör protokolleri, fiziksel boyutlar vb.). Birçok alan isteğe bağlı olsa da, cihazınızı sertifikalandırdıktan sonra yayımlamayı seçerseniz, bu bilgilerin çoğu Azure Sertifikalı cihaz kataloğunda potansiyel müşteriler için kullanılabilir hale gelir.

1. `Add`Proje özet sayfanızdaki ' cihaz ayrıntıları ' bölümüne tıklayarak cihaz ayrıntıları bölümünü açın. Doldurmanız için beş bölüm görürsünüz.

![Proje Ayrıntıları sayfasının görüntüsü](./media/images/device-details-menu.png)

2. Daha önce, sekmesi altında projeyi oluştururken verdiğiniz bilgileri gözden geçirin `Basics` .
1. Aygıtınızla, için uyguladığınız sertifikaları sekmesi altına gözden geçirin `Certifications` .
1. Sekmesini açın `Product details` ve en az bir işletim sistemi seçin.
1. Cihazınızı tanımlayan **en az** bir ayrı bileşen ekleyin. [Burada](how-to-using-the-components-feature.md)bileşen kullanımıyla ilgili ek rehberlik görüntüleyebilirsiniz.
1. `Save` öğesine tıklayın. Daha sonra, bileşen cihazınızı düzenleyebilir ve daha gelişmiş ayrıntılar ekleyebilirsiniz.
1. Altındaki bileşen ayrıntıları tarafından yakalanmayan ek cihaz ayrıntılarını listeleyin `Additional product details` .
1. `Other`Bileşen alanlarından herhangi birinde işaretlenmiş veya Azure Sertifika ekibine bayrak eklemek istediğiniz özel bir durum varsa, bölümünde bir açıklama yorumu bırakın `Comments for reviewer` .
1. Cihazınız, `Dependencies` verileri Azure 'a göndermek için ek donanım veya hizmet gerektiriyorsa, herhangi bir bağımlılığı listelemek için sekmesini kullanın. Bağımlılıkları listeleme hakkında ek yönergeler [buradan](how-to-indirectly-connected-devices.md)görebilirsiniz.
1. Sağladığınız bilgileri karşıladıktan sonra, `Review` girilen cihaz ayrıntılarının tamamına yönelik salt okunurdur bir genel bakış için sekmesini kullanabilirsiniz.
1. `Project summary`Özet sayfanıza geri dönmek için sayfanın üst kısmına tıklayın.

![Proje ayrıntıları sayfasını gözden geçir](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Başlarken Kılavuzunu karşıya yükleme

Kullanmaya başlama kılavuzu, ürününüzün kurulumunu ve yapılandırmasını ve yönetimini basitleştirecek bir PDF belgesidir. Amacı, müşterilerin cihazınızı kullanarak Azure 'da cihazları bağlanmasını ve desteklememelerini sağlamaktır. Sertifika sürecinin bir parçası olarak iş ortaklarımızın en ilgili sertifika programı için **bir** Başlangıç Kılavuzu sağlaması gerekir.

1. Sağlanan [şablonlara](https://aka.ms/GSTemplate)göre Başlarken Kılavuzu PDF 'nize istenen tüm bilgileri sağladığınıza emin olun. Kullandığınız şablon için uyguladığınız sertifika rozetinin belirlenmesi gerekir. (Örneğin, bir IoT Tak ve Kullan cihazı IoT Tak ve Kullan şablonunu kullanacaktır. *Yalnızca* Azure Sertifikalı cihaz temeli sertifikası için uygulanan cihazlar, Azure Sertifikalı cihaz şablonunu kullanır.)
1. `Add`Proje Özeti sayfasının ' Başlarken ' kılavuzunun bölümüne tıklayın.

![GSG düğmesi görüntüsü](./media/images/gsg-menu.png)

2. PDF 'nizi karşıya yüklemek için ' Dosya Seç 'e tıklayın.
1. Biçimlendirmeyi önizlemek için belgeyi gözden geçirin.
1. ' Kaydet ' düğmesine tıklayarak karşıya yüklemeyi kaydedin.
1. `Project summary`Özet sayfanıza geri dönmek için sayfanın üst kısmına tıklayın.

## <a name="providing-marketing-details"></a>Pazarlama ayrıntıları sağlama

Bu alanda, cihazınız için müşterinin sağladığı pazarlama bilgilerini sağlayacaksınız. Sertifikalı cihazınızı yayımlamayı seçerseniz, bu alanlar Azure Sertifikalı cihaz kataloğunda gösterilir.

1. `Add`Pazarlama ayrıntıları sayfasını açmak için ' pazarlama ayrıntıları Ekle ' bölümüne tıklayın.

![Pazarlama ayrıntıları bölümünün görüntüsü](./media/images/marketing-details.png)

1. Katalogda kullanılacak JPEG veya PNG biçiminde bir ürün fotoğrafı yükleyin.
1. Cihazınızın ürün açıklaması sayfasında görüntülenecek kısa bir açıklama yazın.
1. Cihazınızın coğrafi kullanılabilirliğini belirtin.
1. Bu cihaz için üreticinin pazarlama sayfasına bir bağlantı sağlayın. Bu, cihaz hakkında ek bilgiler sağlayan bir sitenin bağlantısı olmalıdır.
    > [!Note]
    > Lütfen tüm sağlanan URL 'Lerin geçerli olduğundan emin olun veya onay sonrasında yayın sırasında etkin olacak. *)

1. Cihazınızın en iyi duruma getirildiği 3 adede kadar hedef sektörler olduğunu gösterir.
1. Cihazınızın 5 ' e kadar dağıtıcılarının bilgilerini sağlayın. Bu, üreticinin kendi sitesini içerebilir.

    > [!Note]
    > Hiçbir dağıtıcı ürün sayfası URL 'SI sağlanmadığında, `Shop` katalogdaki düğme varsayılan olarak `Distributor page` , cihaza özgü olmayan, için sağlanan bağlantıya varsayılan olur. İdeal olarak, dağıtıcı URL 'SI müşterinin bir cihaz satın aldığı ancak zorunlu olmadığı belirli bir sayfaya yol açabilmelidir. Dağıtıcı üretici ile aynıysa, bu URL üreticinin pazarlama sayfası ile aynı olabilir. *)

1. `Save`Bilgilerinizi onaylamak için tıklayın.
1. `Project summary`Özet sayfanıza geri dönmek için sayfanın üst kısmına tıklayın.

## <a name="declaring-additional-industry-certifications"></a>Ek sektör sertifikaları bildirme

Ayrıca, cihazınız için almış olduğunuz ek sektör sertifikalarını da yükseltebilirsiniz. Bu sertifikalar, cihazınızın kullanım amacına daha fazla açıklık sağlanmasına yardımcı olabilir ve Azure Sertifikalı cihaz kataloğu 'nda aranabilir olacaktır.

1. `Add`' Sektör sertifikaları sağla ' bölümüne tıklayın.
1. `Add a certification`Ortak endüstri sertifikası programlarının listesinden seçim yapmak için tıklayın. Ürününüz listemde olmayan bir sertifika elde ediyorsanız, öğesini seçerek özel bir dize değeri belirtebilirsiniz `Other (please specify)` .
1. İsteğe bağlı olarak gözden geçiren için bir açıklama veya notlar sağlayın. Ancak, bu notlar katalogda görüntülemek için herkese açık olmayacaktır.
1. `Save`Bilgilerinizi onaylamak için tıklayın.
1. `Project summary`Özet sayfanıza geri dönmek için sayfanın üst kısmına tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Artık cihazınızı açıklama işlemini tamamladınız! Bu, Azure Sertifikalı cihaz incelemesi ekibinin ve müşterinizin Ürününüzü daha iyi anlamasına yardımcı olur. Sağladığınız bilgileri karşıladıktan sonra, artık sertifika sürecinin test aşamasına geçiş yapmaya hazırsınız demektir.
> [!div class="nextstepaction"]
> [Öğretici: cihazınızı test etme](tutorial-03-testing-your-device.md)
