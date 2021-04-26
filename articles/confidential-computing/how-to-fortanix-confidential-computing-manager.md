---
title: Azure yönetilen uygulamasında fortanix gizli bilgi Işlem yöneticisi
description: Fortanix gizli bilgi Işlem yöneticisi 'Ni (CCM) Azure portal yönetilen bir uygulamada dağıtmayı öğrenin.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563430"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Azure yönetilen uygulamasında fortanix gizli bilgi Işlem yöneticisi

Bu makalede, Azure portal Fortanix gizli bilgi Işlem yöneticisi tarafından yönetilen bir uygulamanın nasıl dağıtılacağı gösterilir.

Fortanx, Azure altyapısının üzerine kurulmuş ürün ve hizmetlere sahip bir üçüncü taraf yazılım satıcıdır. Azure 'da benzer gizli bilgi işlem hizmetleri sunan başka üçüncü taraf sağlayıcılar vardır.

> [!NOTE]
>Bu belgede başvurulan Ürünler Microsoft denetimi altında değil. Microsoft bu bilgileri size kolaylık sağlaması için sağlıyor ve bu Microsoft dışı ürünlerin başvurusu Microsoft tarafından onay göstermez.

## <a name="prerequisites"></a>Önkoşullar

- Dönüştürülmüş uygulama görüntülerini göndermek için özel bir Docker kayıt defteri.
- Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Azure yönetilen uygulaması aracılığıyla gizli bilgi Işlem Yöneticisi dağıtma

1. [Azure Portal](https://portal.azure.com/) gidin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure portal.":::

2. Arama çubuğunda "Fortanix gizli bilgi Işlem yöneticisi" ni arayın ve Fortanx CCM için market listesini bulabilirsiniz. **Azure 'Da Fortanix gizli bilgi Işlem Yöneticisi '** ni seçin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Market listeleme.":::

3. CCM ile yönetilen uygulamayı oluşturduğunuz sayfa açılır. **Oluştur**' u seçin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Uygulama oluşturun.":::

4. Tüm gerekli alanları doldur.
   1. Yönetilen uygulama ayrıntıları bölümünde, **yönetilen kaynak grubu** alanında, kullanıcının ihtiyacı varsa değiştirebilecekleri bir varsayılan değer olacaktır.
   2. **Bölge** alanında **Avustralya Doğu**, **Avustralya Güneydoğu**, **Doğu ABD**, **Batı ABD 2**, **Batı Avrupa**, **Kuzey Avrupa**, **Kanada Orta**, **Kanada Doğu** veya **Doğu ABD 2 euap** seçeneklerinden birini belirleyin.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Gerekli alanlar":::

   Fortanx CCM tarafından yönetilen uygulamayı oluşturmak için **gözden geçir + oluştur** ' u seçin.

5. Ayrıntıları gözden geçirin ve doğrulama başarılı olduktan sonra, **Yukarıdaki hüküm ve koşulları kabul** ediyorum onay kutusunu işaretleyin ve ardından **Oluştur** ' u seçerek yönetilen uygulamayı oluşturun.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Ayrıntıları gözden geçirin.":::

6. Fortanx CCM dağıtımı başlar ve dağıtımın devam ettiğini bildirir.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Dağıtım Ilerleme durumu.":::

7. Dağıtım tamamlandığında, işlem düğümünü kaydetmek için dağıtılan CCM tarafından yönetilen uygulamanın "genel bakış" sayfasına gitmek için **Kaynağa Git** düğmesini seçin.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Azure portal başarılı bir dağıtım gösteren ekran görüntüsü.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Azure portal gizli bilgi işlem kaynağına genel bakış gösteren ekran görüntüsü.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>İşlem düğümünü Fortanx CCM 'ye kaydetme

1. Sol gezinti menüsünden **Gizli Bilgi Işlem yöneticisi** ' ni seçin. Fortanx CCM 'de oturum açın ve **Şekil 9**' da gördüğünüz şekilde bir hesap oluşturun.

    Kaydolma hakkında daha fazla ayrıntı için bkz. CCM 'de oturum açma ve hesap oluşturma, [CCM Başlarken](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)'e bakın.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Fortanx gizli bilgi Işlem Yöneticisi oturum açma bilgilerini gösteren ekran görüntüsü.":::
    
2. CCM yönetim konsolundan JOIN belirtecini almak için önce **düğüm kaydet** düğmesini seçin. Ardından, düğüm Kaydet penceresinde, Birleştir simgesini kopyalamak için **Kopyala** düğmesini seçin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="JOIN belirtecini alma işlemini gösteren ekran görüntüsü.":::

3. Şimdi bir düğüm Aracısı kaydetmek için **Gizli Bilgi Işlem düğüm Aracısı** sekmesini seçin ve **Ekle** ' yi seçerek bir CCM düğüm aracısı ekleyin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Düğüm aracısının eklenmesini gösteren ekran görüntüsü.":::

4.  CCM düğüm Aracısı formunda, tüm gerekli alanları doldurur. 2. adımda kopyaladığınız JOIN belirtecini, **JOIN belirtecine** yapıştırın. Onaylamak için **gözden geçir + gönder** ' i seçin.

    CCM işlem düğümünü kaydetme hakkında daha fazla bilgi için bkz. [Işlem düğümünü kaydetme](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="İşlem düğümünün kaydedilmesini gösteren ekran görüntüsü.":::
    
5. Doğrulama geçtikten sonra, düğüm Aracısı oluşturmayı gerçekleştirmek için **Gönder** ' i seçin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Düğüm aracısının oluşturulduğunu gösteren ekran görüntüsü.":::

6. Dağıtım durumunu denetlemek için **genel bakış** sekmesine gidin ve **yönetilen kaynak grubu** bağlantısı ' nı seçin.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Düğümün kaydedildiğini gösteren ekran görüntüsü.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Dağıtım durumunun kontrol olduğunu gösteren ekran görüntüsü.":::

7. Artık dağıtım durumunun devam ettiğini ve düğüm aracısının başarıyla kaydedilmesi birkaç dakika sürecek olduğunu fark edeceksiniz.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Sürmekte olan dağıtımı gösteren ekran görüntüsü.":::

8. Düğüm Aracısı kaydı başarılı olduğunda, durum "başarılı" olarak değişir.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Dağıtımın başarılı olduğunu gösteren ekran görüntüsü.":::

9. Şimdi CCM ile yönetilen uygulamada, Işlem düğümleri sayfasına gidin ve düğümün **etkin** durumda olduğunu ve başarıyla kaydedildiğini fark edeceksiniz.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Düğümün başarıyla kaydedildiğini gösteren ekran görüntüsü.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kullanıcı ayrıca gizli bilgi Işlem düğüm Aracısı sayfasından bir CCM düğüm aracısını silebilir. Düğüm aracısını silmek için düğüm aracısını seçin ve üstteki çubukta **Sil** düğmesini seçin.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Düğüm aracısının silinmesini gösteren ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure tarafından yönetilen bir uygulamayı kullanarak bir düğümü, gizli bilgi Işlem yöneticisi 'ne kaydettiniz. Düğüm kaydı, uygulama görüntünüzü gizli bir bilgi işlem sanal makinesinin en üstünde çalışacak şekilde dönüştürmenize olanak sağlar. Azure 'daki sanal makinelerin gizli bilgi işlem hakkında daha fazla bilgi için bkz. [sanal makinelerdeki çözümler](virtual-machine-solutions.md).

Azure 'un gizli bilgi işlem teklifleri hakkında daha fazla bilgi edinmek için bkz. [Azure gizli bilgi işlem](overview.md).

Azure 'da [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) ve [skoni](https://sconedocs.github.io)gibi diğer üçüncü taraf teklifleri kullanarak benzer görevleri tamamlamayı öğrenin.

