---
title: Azure Veri Kataloğu oluşturma
description: Bu hızlı başlangıçta Azure portal kullanarak bir Azure Veri Kataloğu oluşturmayı açıklanmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976871"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Hızlı Başlangıç: Azure Veri Kataloğu oluşturma

Azure Veri Kataloğu kurumsal veri varlıkları için bir kayıt sistemi ve bulma sistemi olarak görev yapan tam yönetilen bir bulut hizmetidir. Ayrıntılı bir genel bakış için bkz. [Azure Veri Kataloğu nedir](overview.md).

Bu hızlı başlangıç, Azure Veri Kataloğu oluşturmaya başlamanıza yardımcı olur.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunları yapmanız gerekir:

* [Microsoft Azure](https://azure.microsoft.com/) aboneliği.
* Kendi [Azure Active Directory kiracınız](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)olması gerekir.

Veri Kataloğu 'Nu ayarlamak için bir Azure aboneliğinin sahibi veya ortak sahibi olmanız gerekir.

## <a name="create-a-data-catalog"></a>Veri Kataloğu oluşturma

Bir kuruluş (Azure Active Directory etki alanı) için yalnızca bir tane veri kataloğu hazırlayabilirsiniz. Bu nedenle, bu Azure Active Directory etki alanına ait olan bir Azure aboneliğinin sahibi veya ortak sahibi zaten bir katalog oluşturduysanız, birden fazla Azure aboneliğiniz olsa bile bir katalog oluşturamazsınız. Azure Active Directory etki alanınızda bir kullanıcı tarafından veri kataloğu oluşturulup oluşturulmadığını test etmek için [Azure Veri Kataloğu giriş sayfasına](http://azuredatacatalog.com) gidin ve kataloğu görüp görmediğinizi doğrulayın. Sizin için katalog zaten oluşturulmuşsa aşağıdaki yordamı atlayın ve sonraki bölümüne gidin.

1. **Kaynak oluşturmak** [Azure Portal](https://portal.azure.com) > gidin ve **Veri Kataloğu**' nu seçin.

    ![Azure Veri Kataloğu oluştur düğmesi](media/data-catalog-get-started/data-catalog-create.png)

2. Veri Kataloğu, kullanmak istediğiniz **abonelik** , Katalog **konumu** ve **fiyatlandırma katmanı**için bir **ad** belirtin. Ardından **Oluştur**’u seçin.

3. [Azure Veri Kataloğu giriş sayfasına](http://azuredatacatalog.com) gidin ve **Verileri Yayımla**’ya tıklayın.

   ![Azure Veri Kataloğu--Verileri Yayımla düğmesi](media/data-catalog-get-started/data-catalog-publish-data.png)

   Ayrıca, [Veri Kataloğu hizmeti sayfasında](https://azure.microsoft.com/services/data-catalog) , **Başlarken**' i seçerek Veri Kataloğu giriş sayfasına da ulaşabilirsiniz.

   ![Azure Veri Kataloğu--pazarlama giriş sayfası](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. **Ayarlar** sayfasına gidin.

    ![Azure Veri Kataloğu--veri kataloğu hazırlama](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. **Fiyatlandırma** ' yı genişletin ve Azure Veri Kataloğu **sürümünüzü** doğrulayın (ücretsiz veya standart).

    ![Azure Veri Kataloğu--sürüm seçme](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Fiyatlandırma katmanınız olarak *Standard* Edition ' ı seçerseniz, **güvenlik grupları** ' nı genişletebilir ve veri kataloğuna erişmek ve Otomatik faturalandırma ayarlamasını etkinleştirmek için Active Directory güvenlik grupları için yetkilendirme sağlayabilirsiniz.

    ![Azure Veri Kataloğu güvenlik grupları](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. **Katalog Kullanıcıları** seçeneğini genişletin ve **Ekle**’ye tıklayarak veri kataloğu için kullanıcı ekleyin. Bu gruba otomatik olarak eklendiniz.

    ![Azure Veri Kataloğu--kullanıcılar](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Fiyatlandırma katmanınız olarak *Standart* sürüm ' ü seçerseniz, sözlük **yöneticileri** ' ni genişletebilir ve **Ekle** ' ye tıklayarak sözlük yönetici kullanıcıları ekleyebilirsiniz. Bu gruba otomatik olarak eklendiniz.

    ![Azure Veri Kataloğu sözlük yöneticileri](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. **Katalog Yöneticileri** seçeneğini genişletin ve **Ekle**’ye tıklayarak veri kataloğu için başka yöneticiler ekleyin. Bu gruba otomatik olarak eklendiniz.

    ![Azure Veri Kataloğu--yöneticiler](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. **Portal başlığı** ' nı genişletin ve Portal başlığında görüntülenecek ek metni ekleyin.

    ![Azure Veri Kataloğu-portal başlığı](media/data-catalog-get-started/data-catalog-portal-title.png)

11. **Ayarlar** sayfasını tamamladıktan sonra, **Yayımla** sayfasına gidin.

    ![Azure Veri Kataloğu--oluşturuldu](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Azure portalında veri kataloğu bulma

1. Web tarayıcısının ayrı bir sekmesinde veya ayrı bir web tarayıcısı penceresinde [Azure portalına](https://portal.azure.com) gidin ve önceki adımda veri kataloğunu oluşturmak için kullandığınız hesabın aynısıyla oturum açın.

2. **Tüm hizmetler** ' i seçin ve ardından **Veri Kataloğu**' na tıklayın.

    ![Azure Veri Kataloğu--Azure 'a](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Oluşturduğunuz veri kataloğunu görürsünüz.

    ![Azure Veri Kataloğu--kataloğu listede görüntüleme](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Oluşturduğunuz kataloğa tıklayın. Portalda **Veri Kataloğu** dikey penceresini görürsünüz.

   ![Azure Veri Kataloğu--portaldaki dikey pencere](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Veri kataloğunun özelliklerini görüntüleyebilir ve güncelleştirebilirsiniz. Örneğin, **Fiyatlandırma katmanı**’na tıklayın ve sürümü değiştirin.

    ![Azure Veri Kataloğu--fiyatlandırma katmanı](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kuruluşunuz için bir Azure Veri Kataloğu oluşturmayı öğrendiniz. Artık veri kataloğunuza veri kaynaklarını kaydedebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Veri Kataloğu 'nda veri kaynaklarını kaydetme](data-catalog-how-to-register.md)
