---
title: Öğretici-Azure maliyet yönetimi 'nden aktarılmış verileri oluşturma ve yönetme
description: Bu makalede, dış sistemlerde kullanabilmeniz için, dışarıya aktarılmış Azure maliyet yönetimi verilerini nasıl oluşturabileceğiniz ve yönetebileceğiniz gösterilmektedir.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 070844cbf8f6a550b92d764ddb8a31afec12f437
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374605"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Öğretici: Dışarı aktarılan verileri oluşturma ve yönetme

Maliyet Analizi öğreticisini okuduysanız, Maliyet Yönetimi verilerinizi el ile indirme konusunda bilgi sahibisiniz. Ancak, maliyet yönetimi verilerinizi günlük, haftalık veya aylık olarak Azure depolama 'ya otomatik olarak dışarı aktaran yinelenen bir görev oluşturabilirsiniz. Dışarı aktarılan veriler CSV biçimindedir ve Maliyet Yönetimi tarafından toplanan tüm bilgileri içerir. Daha sonra Azure depolama alanındaki dışarı aktarılan verileri dış sistemlerle kullanabilir ve kendi özel verilerinizle birleştirebilirsiniz. Ayrıca dışarı aktarılan verilerinizi pano gibi bir dış sistemde veya diğer mali sistemde kullanabilirsiniz.

Azure ücretlerinizi Azure depolama 'ya zamanlanan bir dışarı aktarma hakkında bilgi almak için [Azure maliyet yönetimi ile depolama 'ya dışarı aktarma zamanlamasını](https://www.youtube.com/watch?v=rWa_xI1aRzo) izleyin.

Bu öğreticideki örnek, maliyet yönetimi verilerinizi dışarı aktarmada ve ardından verilerin başarılı bir şekilde dışarı aktarıldığını doğrulamada size yol gösterir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Günlük bir dışarı aktarma oluşturma
> * Verilerin toplandığını doğrulama

## <a name="prerequisites"></a>Önkoşullar
Veri dışa aktarma, [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) müşterileri de dahil olmak üzere çeşitli Azure hesap türleri için kullanılabilir. Desteklenen hesap türlerinin tam listesini görüntülemek için bkz. [maliyet yönetimi verilerini anlama](understand-cost-mgt-data.md). Aşağıdaki Azure izinleri veya kapsamları, Kullanıcı ve grup tarafından verilerin dışarı aktarılması için abonelik başına desteklenir. Kapsamlar hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

- Sahip – Bir abonelik için zamanlanan dışarı aktarmaları oluşturabilir, değiştirebilir veya silebilir.
- Katkıda bulunan – Kendi zamanlanan dışarı aktarmalarını oluşturabilir, değiştirebilir veya silebilir. Başkaları tarafından oluşturulan zamanlanmış dışarı aktarmaların adını değiştirebilir.
- Okuyucu – İzni oldukları dışarı aktarmaları zamanlayabilir.

Azure Depolama hesapları için:
- Yazma izinlerinin, dışarı aktarma üzerindeki izinlerden bağımsız olarak yapılandırılmış depolama hesabını değiştirmesi gerekir.
- Azure depolama hesabınızın blob veya dosya depolama için yapılandırılmış olması gerekir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın
[https://portal.azure.com](https://portal.azure.com/) adresinden Azure portalında oturum açın.

## <a name="create-a-daily-export"></a>Günlük bir dışarı aktarma oluşturma

Veri dışa aktarma oluşturmak veya görüntülemek veya bir dışarı aktarma zamanlamak için, Azure portal istenen kapsamı açın ve menüden **Maliyet Analizi** ' ni seçin. Örneğin, **abonelikler**' e gidin, listeden bir abonelik seçin ve ardından menüde **Maliyet Analizi** ' ni seçin. Maliyet Analizi sayfasının en üstünde **dışarı aktar** ' a tıklayın ve ardından bir dışarı aktarma seçeneği belirleyin. Örneğin, **dışarı aktarmayı zamanla**' yı tıklatın.  

> [!NOTE]
> - Aboneliklerin yanı sıra, kaynak grupları, hesaplar, departmanlar ve kayıtlar üzerinde dışarı aktarmalar oluşturabilirsiniz. Kapsamlar hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).
>- Faturalandırma hesabı kapsamında veya bir müşterinin kiracısında bir iş ortağı olarak oturum açtığınızda, iş ortağı depolama hesabınıza bağlı bir Azure depolama hesabına veri aktarabilirsiniz. Ancak, CSP kiracınızda etkin bir aboneliğiniz olması gerekir.
>


**Ekle**' ye tıklayın, dışarı aktarma için bir ad yazın ve ardından **ayda, aylık maliyetler seçeneğinin günlük dışa aktarılmasını** seçin. **İleri**’ye tıklayın.

![Dışarı aktarma türünü gösteren yeni dışarı aktarma örneği](./media/tutorial-export-acm-data/basics_exports.png)

Azure depolama hesabınız için aboneliği belirtin ve ardından depolama hesabınızı seçin.  Dışa aktarma dosyasının gitmesini istediğiniz depolama kapsayıcısını ve dizin yolunu belirtin.  **İleri**’ye tıklayın.

![Depolama hesabı ayrıntılarını gösteren yeni dışarı aktarma örneği](./media/tutorial-export-acm-data/storage_exports.png)

Dışarı aktarma ayrıntılarınızı gözden geçirin ve **Oluştur**' a tıklayın.

Yeni dışarı aktarmanız, dışarı aktarma listesinde görünür. Varsayılan olarak, yeni dışarı aktarmalar etkindir. Zamanlanmış bir dışarı aktarmayı devre dışı bırakmak veya silmek istiyorsanız, listedeki herhangi bir öğeye ve ardından **Devre dışı bırak** veya **Sil** seçeneklerinden birine tıklayın.

Başlangıçta, dışarı aktarmanın çalışmaya başlaması bir ila iki saat arası sürebilir. Ancak, verilerin dışarı aktarılan dosyalarda gösterilmesi en fazla dört saat sürebilir.

### <a name="export-schedule"></a>Dışarı aktarma zamanlaması

Zamanlanan dışarı aktarmalar, ilk olarak dışa aktarma işlemini oluşturduğunuz zaman ve haftanın günüyle etkilenir. Zamanlanan bir dışarı aktarma oluşturduğunuzda dışarı aktarma işlemi her bir sonraki dışarı aktarma oluşumu için aynı günde çalışır. Örneğin, 1:00 PM 'de günlük bir dışarı aktarma oluşturursunuz. Sonraki dışa aktarma işlemi, aşağıdaki gün 1:00 PM 'de çalışır. Geçerli zaman, diğer tüm dışarı aktarma türlerini aynı şekilde etkiler — her zaman dışa aktarma işlemini ilk oluşturduğunuz zaman aynı günde çalışır. Farklı bir örnekte, Pazartesi günü 4:00 PM 'de haftalık bir dışarı aktarma oluşturursunuz. Sonraki rapor 4:00 PM 'de aşağıdaki Pazartesi günü çalışır. *İçe aktarılmış veriler, çalışma zamanının dört saat içinde kullanılabilir.*

Her dışarı aktarma yeni bir dosya oluşturur, bu nedenle eski dışarı aktarmalar üzerine yazılmaz.

Üç tür dışa aktarma seçeneği vardır:

**Aylık maliyetler Için günlük dışa aktarma** : ilk dışa aktarma hemen çalışır. Sonraki dışarı aktarmalar, ilk dışa aktarma ile aynı anda bir sonraki günü çalışır. En son veriler, önceki günlük dışarı aktarımlardan toplanır.

**Son 7 güne ait maliyetlerin haftalık dışa aktarılması** : ilk dışa aktarma hemen çalışır. Sonraki dışarı aktarmalar, haftanın gününde ve ilk dışa aktarma ile aynı zamanda çalışır. Maliyetler son yedi güne yöneliktir.

**Özel** – haftalık ve aylık dışarı aktarmaları, hafta-tarih ve ay-tarih seçenekleriyle zamanlamanıza izin verir. *İlk dışarı aktarma işlemi hemen çalışacaktır.*

Kullandıkça öde, MSDN veya Visual Studio aboneliğiniz varsa faturanızda fatura dönemi takvim ayına hizalanmayabilir. Bu tür abonelikler ve kaynak grupları için, fatura döneminize veya takvim aylarınıza hizalanmış bir dışarı aktarma işlemi oluşturabilirsiniz. Faturanızda bir dışa aktarma oluşturmak için, **özel**' e gidin ve **faturalandırma dönemi**' ni seçin.  Takvim aya hizalanmış bir dışarı aktarma oluşturmak için, **ay-tarih**' i seçin.
>
>

![Yeni dışarı aktarma-özel haftalık haftalık seçimi gösteren temel bilgiler sekmesi](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verilerin toplandığını doğrulama

Maliyet Yönetimi verilerinizin toplandığını kolaylıkla doğrulayabilir ve Azure Depolama Gezgini’ni kullanarak dışarı aktarılan CSV dosyasını görüntüleyebilirsiniz.

Dışarı aktarma listesinde depolama hesabı adına tıklayın. Depolama hesabı sayfasında Gezgin'de Aç seçeneğine tıklayın. Bir onay kutusu görürseniz, dosyayı Azure Depolama Gezgini’nde açmak için **Evet**’e tıklayın.

![Gezgin 'de açılacak örnek bilgileri ve bağlantıyı gösteren depolama hesabı sayfası](./media/tutorial-export-acm-data/storage-account-page.png)

Depolama Gezgini'nde, açmak istediğiniz kapsayıcıya gidin ve bulunduğunuz aya karşılık gelen klasörü seçin. CSV dosyaları listesi gösterilir. Birini seçin ve **Aç**’a tıklayın.

![Depolama Gezgini gösterilen örnek bilgiler](./media/tutorial-export-acm-data/storage-explorer.png)

Dosya, CSV dosyası uzantılarını açmak üzere ayarlanmış program veya uygulama ile açılır. Bir örneği Excel’de verilmiştir.

![Excel 'de gösterilen örnek CSV verileri örneği](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Dışarı aktarılan verilere diğer sistemlerden erişme

Maliyet Yönetimi verilerinizi dışarı aktarma amaçlarından biri, verilere dış sistemlerden erişmektir. Bir pano sistemi veya diğer finansal sistemi kullanabilirsiniz. Bu sistemler büyük ölçüde farklılık gösterdiğinden bir örnek vermek uygun olmaz.  Bununla birlikte, [Azure Depolama'ya giriş](../storage/common/storage-introduction.md) bölümünde uygulamalarınızdan verilerinize erişmeye başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Günlük bir dışarı aktarma oluşturma
> * Verilerin toplandığını doğrulama

Boşta olan ve az kullanılan kaynakları belirleyerek verimliliği iyileştirmek ve geliştirmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Önerileri gözden geçirme ve gerekli eylemleri gerçekleştirme](tutorial-acm-opt-recommendations.md)
