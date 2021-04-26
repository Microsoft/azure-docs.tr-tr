---
title: Azure destek isteği oluşturma
description: Yardıma ihtiyacı olan müşteriler, self servis çözümlerini bulmak ve destek istekleri oluşturmak ve yönetmek için Azure portal kullanabilir.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 395fc0d5905389ffb8f68d8bbc4c6f8b3a871d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774889"
---
# <a name="create-an-azure-support-request"></a>Azure destek isteği oluşturma

Azure, destek bileti olarak da bilinen destek istekleri oluşturmanıza ve yönetmenize olanak sağlar. [Azure Portal](https://portal.azure.com), bu makalede ele alınan istekleri oluşturabilir ve yönetebilirsiniz. Ayrıca, [Azure destek bileti REST API](/rest/api/support)kullanarak veya [Azure CLI](/cli/azure/azure-cli-support-request)kullanarak, istekleri programlı bir şekilde oluşturabilir ve yönetebilirsiniz.

> [!NOTE]
> Azure portal URL 'SI, kuruluşunuzun dağıtıldığı Azure bulutuna özgüdür.
>
>* Ticari kullanım için Azure portal: [https://portal.azure.com](https://portal.azure.com)
>* Almanya için Azure portal: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Birleşik Devletler kamu Azure portal: [https://portal.azure.us](https://portal.azure.us)

Destek isteği deneyimi üç ana hedefle odaklanır:

* **Kolaylaştırılmış**: destek isteği göndermeyi ve yönetmeyi kolaylaştırmak için destek ve sorun gidermeyi kolaylaştırın.
* **Tümleşik**: bir Azure kaynağıyla ilgili sorun giderirken bağlam değiştirmeden kolayca bir destek isteği açabilirsiniz.
* **Verimli**: sorununuzu verimli bir şekilde çözmek için destek mühendislerinizin ihtiyaç duyacağı anahtar bilgilerini toplayın.

Azure, faturalandırma, kota ayarlamaları ve hesap aktarımları dahil olmak üzere abonelik yönetimi için sınırsız destek sağlar. Teknik destek için bir destek planına ihtiyacınız vardır. Daha fazla bilgi için bkz. [Destek planlarını karşılaştırma](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Kullanmaya başlama

Azure portal **Yardım + Destek** alabilirsiniz. Bu, bir hizmetin Azure portal menüsünde, genel üst bilgisinde veya kaynak menüsünden kullanılabilir. Bir destek isteğini kaydedebilmeniz için, uygun izinlere sahip olmanız gerekir.

### <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Bir destek isteği oluşturmak için, abonelik düzeyindeki [destek Isteği katılımcısı](../../role-based-access-control/built-in-roles.md#support-request-contributor) rolüne bir [sahip](../../role-based-access-control/built-in-roles.md#owner), [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) veya atanmış olmanız gerekir. Aboneliği olmayan bir destek isteği oluşturmak için, örneğin bir Azure Active Directory senaryosunda, [yönetici](../../active-directory/roles/permissions-reference.md)olmanız gerekir.

### <a name="go-to-help--support-from-the-global-header"></a>Genel üst bilgiden yardım + destek 'e gidin

Azure portal her yerden bir destek talebi başlatmak için:

1. Seçin **?** Genel üst bilgisinde **Yardım + Destek**' i seçin.

   ![Yardım ve Destek](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. **Yeni destek isteği**’ni seçin. Sorununuz hakkında bilgi sağlamak için istemleri izleyin. Bazı olası çözümler önereceğiz, sorunla ilgili ayrıntıların toplanması ve Destek isteğini göndermenize ve izlemenize yardımcı olacak.

   ![Yeni Destek İsteği](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Kaynak menüsünden Yardım + Destek 'e gidin

Şu anda çalıştığınız kaynak bağlamında bir destek talebi başlatmak için:

1. Kaynak menüsünde, **destek + sorun giderme** bölümünde **Yeni destek isteği**' ni seçin.

   ![Bağlamda](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Bize karşılaştığınız sorun hakkında bilgi sağlamak için istemleri izleyin. Kaynaktan destek isteği sürecini başlattığınızda, bazı seçenekler sizin için önceden seçilmiştir.

## <a name="create-a-support-request"></a>Destek isteği oluşturma

Sorununuz hakkında bilgi toplamak ve çözmenize yardımcı olmak için bazı adımlarda size kılavuzluk ederiz. Her adım aşağıdaki bölümlerde açıklanmıştır.

### <a name="basics"></a>Temel Bilgiler

Destek isteği işleminin ilk adımı, sorununuz ve Destek planınız hakkında temel bilgileri toplar.

**Yeni destek isteğine** Ilişkin **temel bilgiler** sekmesinde, sorunu bize bildirmek için seçicileri kullanın. İlk olarak, sorun türü için bazı genel kategoriler tanımlayacaksınız ve ilgili aboneliği seçmelisiniz. Hizmeti (örneğin, **Windows çalıştıran sanal makine**) seçin. Sanal makinenizin adı gibi kaynağı seçin. Sorunu kendi sözcüklerinizle anlaın ve daha fazla bilgi almak için **sorun türü** ve **sorun alt türü** ' nü seçin.

![Temel Bilgiler dikey penceresi](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Çözümler

Temel bilgiler toplandıktan sonra, bir sonraki adımda deneyebileceğiniz çözümleri göstereceğiz. Bazı durumlarda, bir hızlı tanılama da çalıştırabiliriz. Çözümler Azure mühendisleri tarafından yazılmıştır ve en sık karşılaşılan sorunları çözmeyecektir.

### <a name="details"></a>Ayrıntılar

Daha sonra, sorunla ilgili ek ayrıntıları topladık. Bu adımda kapsamlı ve ayrıntılı bilgiler sağlamak, destek isteğinizi doğru mühendise yönlendirmemize yardımcı olur.

1. Mümkünse, sorunun ne zaman başlatıldığını ve bunu yeniden oluşturmak için gereken adımları bize söyleyin. Günlük dosyası veya tanılama çıktısı gibi bir dosyayı karşıya yükleyebilirsiniz. Dosya karşıya yüklemeleri hakkında daha fazla bilgi için bkz. [dosya yükleme yönergeleri](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Sorunla ilgili tüm bilgileri aldıktan sonra, nasıl destek alınacağını seçin. **Ayrıntıların** **destek yöntemi** bölümünde, etkinin önem derecesini seçin. En yüksek önem düzeyi, [destek planınıza](https://azure.microsoft.com/support/plans)bağlıdır.

    Varsayılan olarak, **Tanılama bilgilerini paylaşma** seçeneği seçilidir. Bu, Azure desteğinin Azure kaynaklarınızdan [Tanılama bilgileri](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) toplamasına olanak sağlar. Bazı durumlarda, bir sanal makinenin belleğine erişim isteme gibi, varsayılan olarak seçili olmayan ikinci bir soru vardır.

1. Tercih ettiğiniz iletişim yönteminizi, sizinle iletişim kurmak için iyi bir zaman ve destek dilinizi belirtin.

1. Ardından, sizinle nasıl iletişim kurabileceğinizi bilmemiz için **iletişim bilgileri** bölümünü doldurun.

### <a name="review--create"></a>Gözden geçirme ve oluşturma

Her sekme için gerekli tüm bilgileri doldurun ve ardından **gözden geçir + oluştur**' u seçin. Destek için göndereceğiniz ayrıntıları denetleyin. Gerekirse değişiklik yapmak için herhangi bir sekmeye dönün. Destek isteği tamamlandıktan memnun olduğunuzda **Oluştur**' u seçin.

Bir destek mühendisi belirttiğiniz yöntemi kullanarak sizinle iletişim kuracaktır. İlk yanıt süreleri hakkında daha fazla bilgi için bkz. [Destek kapsamı ve yanıt hızı](https://azure.microsoft.com/support/plans/response/).


## <a name="next-steps"></a>Sonraki adımlar

Azure 'da kendi kendine yardım destek seçenekleri hakkında daha fazla bilgi edinmek için şu videoyu izleyin:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [Azure Destek isteğini yönetme](how-to-manage-azure-support-request.md)
* [Azure destek bileti REST API](/rest/api/support)
* [Görüşlerinizi ve önerilerinizi bize gönderin](https://feedback.azure.com/forums/266794-support-feedback)
* [Twitter](https://twitter.com/azuresupport) 'da bizimle iletişime katılın
* [Microsoft Q&soru sayfasında](/answers/products/azure) Meslektaşlarınızdan yardım alın
* [Azure DESTEĞI hakkında](https://azure.microsoft.com/support/faq) daha fazla bilgi