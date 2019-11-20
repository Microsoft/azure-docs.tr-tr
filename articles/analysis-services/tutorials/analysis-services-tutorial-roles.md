---
title: Öğretici-Azure Analysis Services rollerini yapılandırma | Microsoft Docs
description: Azure portal veya SQL Server Management Studio kullanarak Azure Analysis Services Yönetici ve Kullanıcı rollerinin nasıl yapılandırılacağını öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 3abcfe61f365c3c96dfb6b8eb2ca6cc9e5d3797e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572365"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Öğretici: Sunucu yöneticisi ve kullanıcı rollerini yapılandırma

 Bu öğreticide, sunucu yöneticisi ve model veritabanı rollerini yapılandırmak üzere Azure sunucunuza bağlanmak için SQL Server Management Studio (SSMS) kullanacaksınız. Ayrıca [Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) ile tanışacaksınız. TMSL, 1200 ve daha yüksek uyumluluk düzeylerindeki tablo modelleri için JSON tabanlı bir betik dilidir. Pek çok tablo modelleme görevini otomatikleştirmek için kullanılabilir. TMSL çoğunlukla PowerShell ile kullanılır, ancak bu öğreticide SSMS'deki XMLA sorgu düzenleyicisini kullanacaksınız. Bu öğreticide aşağıdaki görevleri tamamlayacaksınız: 
  
> [!div class="checklist"]
> * Portaldan sunucu adınızı alma
> * SSMS kullanarak sunucunuza bağlanma
> * Sunucu yöneticisi rolüne bir kullanıcı veya grup ekleme 
> * Model veritabanı yöneticisi rolüne bir kullanıcı veya grup ekleme
> * Yeni bir model veritabanı rolü ekleme ve bir kullanıcı veya grup ekleme

Azure Analysis Services'de kullanıcı güvenliği hakkında daha fazla bilgi edinmek için bkz: [Kimlik doğrulaması ve kullanıcı izinleri](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Ön koşullar

- Aboneliğinizde bir Azure Active Directory.
- Aboneliğinizde bir [Azure Analysis Services sunucusu](../analysis-services-create-server.md) oluşturmuş olmanız.
- [Sunucu yöneticisi](../analysis-services-server-admins.md) izinlerinizin olması.
- Sunucunuza [adventureworks örnek modelini ekleyin](../analysis-services-create-sample-model.md).
- [En son SQL Server Management Studio (SSMS) sürümünü yükleyin](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Portalda](https://portal.azure.com/) oturum açın.

## <a name="get-server-name"></a>Sunucu adını alma
SSMS'den sunucunuza bağlanmak için önce sunucu adını bilmelisiniz. Sunucu adını portaldan alabilirsiniz.

**Azure portalı** > sunucu > **Genel Bakış** > **Sunucu adı** menüsünde sunucu adını kopyalayın.
   
   ![Azure'da sunucu adını alma](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>SSMS'de bağlanma

Kalan görevlerde sunucunuza bağlanmak ve sunucunuzu yönetmek için SSMS kullanacaksınız.

1. SSMS > **Nesne Gezgini**'nde **Bağlan** > **Analysis Services**'e tıklayın.

    ![Bağlan](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. **Sunucuya Bağlan** iletişim kutusunda, **Sunucu adı**'na portaldan kopyaladığınız sunucu adını yapıştırın. **Kimlik doğrulaması**'nda **MFA Desteğiyle Active Directory Universal**'ı seçin, sonra kullanıcı hesabınızı girin ve **Bağlan**'a basın.
   
    ![SSMS'de bağlanma](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > MFA Desteğiyle Active Directory Universal'ın seçilmesi önerilir. Bu tür bir kimlik doğrulaması, [etkileşimsiz ve çok faktörlü kimlik doğrulamasını](../../sql-database/sql-database-ssms-mfa-authentication.md) destekler. 

3. **Nesne Gezgini**'nde, sunucu nesnelerini görmek için sunucuyu genişletin. Sunucu özelliklerini görmek için sağ tıklatın.
   
    ![SSMS'de bağlanma](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Sunucu yöneticisi rolüne bir kullanıcı hesabı ekleme

Bu görevde, Azure AD'nizden sunucu yöneticisi rolüne bir kullanıcı veya grup hesabı ekleyeceksiniz. Güvenlik grubu ekliyorsanız `MailEnabled` özelliğini `True` olarak ayarlamanız gerekir.

1. **Nesne Gezgini**'nde sunucu adınızı sağ tıklatın, sonra **Özellikler**'i tıklatın. 
2. **Analysis Server Özellikleri** penceresinde **Güvenlik** > **Ekle**'ye tıklayın.
3. **Kullanıcı veya Grup Seç** penceresinde, Azure AD'nize bir kullanıcı veya grup hesabı girin, sonra **Ekle**'ye tıklayın. 
   
     ![Sunucu yöneticisi ekleme](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. **Analysis Server Özellikleri**'ni kapatmak için **Tamam**'a tıklayın.

    > [!TIP]
    > Ayrıca portalda **Analysis Services Yöneticileri**'ni kullanarak sunucu yöneticileri de ekleyebilirsiniz. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Model veritabanı yöneticisi rolüne bir kullanıcı ekleme

Bu görevde, Internet Satış Yöneticisi modelde zaten var olan bir kullanıcı veya grup hesabı rolü ekleyeceksiniz. Bu rol, adventureworks örnek model veritabanı için Tam denetim (Yönetici) izinlerine sahiptir. Bu görev, kendi oluşturduğunuz bir betikte [CreateOrReplace](https://docs.microsoft.com/bi-reference/tmsl/createorreplace-command-tmsl) TMSL komutunu kullanmaktadır.

1. **Nesne Gezgini**'nde **Veritabanları** > **adventureworks** > **Roller**'i genişletin. 
2. **Internet Satış Yöneticisi**'ne sağ tıklayın, sonra **Rol Betiği** > **CREATE OR REPLACE** > **Yeni Sorgu Düzenleyicisi Penceresi**'ne tıklayın.

    ![Yeni Sorgu Düzenleyicisi Penceresi](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. **XMLAQuery**'de, **"memberName":** değerini Azure AD'nizdeki bir kullanıcı veya grup hesabı ile değiştirin. Varsayılan olarak, oturum açtığınız hesap dahil edilir; ancak zaten sunucu yöneticisi olduğunuzdan kendi hesabınızı eklemeniz gerekmez.

    ![XMLA sorgusunda TMSL betiği](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Betiği yürütmek için **F5**'e basın.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Yeni bir model veritabanı rolü ekleme ve bir kullanıcı veya grup ekleme

Bu görevde yeni bir Internet Satış Genel rolü oluşturmak için bir TMSL betiğinde [Oluştur](https://docs.microsoft.com/bi-reference/tmsl/create-command-tmsl) komutunu kullanacak, role *okuma* izinleri verecek ve Azure AD'nizden bir kullanıcı veya grup hesabı ekleyeceksiniz.

1. **Nesne Gezgini**'nde **adventureworks**'e sağ tıklayın, sonra **Yeni Sorgu** > **XMLA**'ya tıklayın. 
2. Aşağıdaki TMSL betiğini kopyalayın ve sorgu düzenleyicisine yapıştırın:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. `"memberName": "globalsales@adventureworks.com"` nesnesinin değerini Azure AD'nizdeki bir kullanıcı veya grup hesabıyla değiştirin.
4. Betiği yürütmek için **F5**'e basın.

## <a name="verify-your-changes"></a>Değişikliklerinizi doğrulama

1. **Nesne Gezgini**'nde, servername'inize tıklayın, sonra **Yenile**'yi tıklayın ve **F5**'e basın.
2. **Veritabanları** > **adventureworks** > **Roller**'i genişletin. Önceki görevlerde eklediğiniz kullanıcı hesabının ve yeni rol değişikliklerinin göründüğünü doğrulayın.   

    ![Nesne Gezgini'nde doğrulama](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekmediğinde, kullanıcı ve grup hesaplarını ve rolleri silin. Bunu yapmak için, kullanıcı hesaplarını kaldırmak üzere **Rol Özellikleri** > **Üyelik**'i kullanın veya role sağ tıklayıp **Sil**'e tıklayın.


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide Azure AS sunucunuza bağlanmayı ve SSMS'de adventureworks örnek model veritabanlarını ve özelliklerini keşfetmeyi öğrendiniz. Ayrıca var olan ve yeni rollere kullanıcı veya grup eklemek için SSMS ve TMSL betiklerini kullanmayı öğrendiniz. Artık sunucunuz ve örnek model veritabanınız için izinleri yapılandırdığınıza göre, size ve başka kullanıcılar Power BI gibi istemci uygulamalarını kullanarak veritabanına bağlanabilir. Daha fazla bilgi edinmek için sonraki öğreticiye devam edin. 

> [!div class="nextstepaction"]
> [Öğretici: Power BI Desktop ile bağlanma](analysis-services-tutorial-pbid.md)

