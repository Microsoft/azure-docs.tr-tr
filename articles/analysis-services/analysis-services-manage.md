---
title: Azure Analysis Services Yönet | Microsoft Docs
description: Bu makalede, bir Azure Analysis Services sunucusu için yönetim ve yönetim görevlerini yönetmek üzere kullanılan araçlar açıklanmaktadır.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572804"
---
# <a name="manage-analysis-services"></a>Analysis Services’ı yönetme
Azure 'da bir Analysis Services sunucusu oluşturduktan sonra, hemen gerçekleştirmeniz gereken bazı yönetim ve yönetim görevleri olabilir. Örneğin, işleme verilerini yenileme, sunucunuzdaki modellere kimlerin erişebileceğini denetleme veya sunucunuzun sistem durumunu izleme gibi işlemler gerçekleştirin. Bazı yönetim görevleri yalnızca Azure portal, diğer SQL Server Management Studio (SSMS) ve bazı görevlerde gerçekleştirilebilir.

## <a name="azure-portal"></a>Azure portal
[Azure Portal](https://portal.azure.com/) , sunucuları oluşturabileceğiniz ve silebileceğiniz, sunucu kaynaklarını izleyebileceğiniz, boyutu değiştirebileceğiniz ve sunucularınıza kimlerin erişebileceğini yönetebilen yerdir.  Bazı sorunlarla karşılaşırsanız, bir destek isteği de gönderebilirsiniz.

![Azure'da sunucu adını alma](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Azure 'da sunucunuza bağlanmak tıpkı kendi kuruluşunuzdaki bir sunucu örneğine bağlanma gibidir. SSMS 'den, işlem verileri gibi aynı görevlerin birçoğunu gerçekleştirebilir veya bir işlem betiği oluşturabilir, rolleri yönetebilir ve PowerShell kullanabilirsiniz.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>SSMS indirme ve yükleme
En son özellikleri ve Azure Analysis Services sunucunuza bağlanırken en iyi deneyimi almak için SSMS 'nin en son sürümünü kullandığınızdan emin olun. 

[SQL Server Management Studio indirin](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>SSMS ile bağlantı kurmak için
 SSMS kullanırken, sunucunuza ilk kez bağlanmadan önce, Kullanıcı adınızın Analysis Services yöneticileri grubuna eklendiğinden emin olun. Daha fazla bilgi edinmek için bu makalenin ilerleyen kısımlarında [sunucu yöneticileri ve veritabanı kullanıcıları](#server-administrators-and-database-users) bölümüne bakın.

1. Bağlanmadan önce sunucu adını almanız gerekir. **Azure portalı** > sunucu > **Genel Bakış** > **Sunucu adı** menüsünde sunucu adını kopyalayın.
   
    ![Azure'da sunucu adını alma](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSMS > **Nesne Gezgini**'nde **Bağlan** > **Analysis Services**'e tıklayın.
3. **Sunucuya Bağlan** iletişim kutusunda sunucu adını yapıştırın, ardından **kimlik doğrulaması**' nda aşağıdaki kimlik doğrulama türlerinden birini seçin:   
    > [!NOTE]
    > **MFA desteğiyle Active Directory**kimlik doğrulaması türü, önerilir.

    > [!NOTE]
    > Bir Microsoft hesabı, Live ID, Yahoo, Gmail vb. ile oturum açarsanız, parola alanını boş bırakın. Bağlan ' a tıkladıktan sonra parola girmeniz istenir.

    Windows Kullanıcı adı ve parola kimlik bilgilerinizi kullanmak için **Windows kimlik doğrulaması** .

    Bir kuruluş hesabı kullanmak için **parola kimlik doğrulaması Active Directory** . Örneğin, etki alanına katılmış bir bilgisayardan bağlantı kurulurken.

    [Etkileşimli olmayan veya Multi-Factor Authentication](../sql-database/sql-database-ssms-mfa-authentication.md)kullanmak için **MFA desteğiyle Active Directory** . 
   
    ![SSMS'de bağlanma](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Sunucu yöneticileri ve veritabanı kullanıcıları
Azure Analysis Services, iki tür Kullanıcı, Sunucu Yöneticisi ve veritabanı kullanıcısı vardır. Her iki tür Kullanıcı Azure Active Directory olmalıdır ve kurumsal e-posta adresi veya UPN tarafından belirtilmelidir. Daha fazla bilgi edinmek için bkz. [Kimlik doğrulaması ve kullanıcı izinleri](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Bağlantı sorunlarını giderme
SSMS kullanarak bağlanırken sorunlarla karşılaşırsanız, oturum açma önbelleğini temizlemeniz gerekebilir. Diske hiçbir şey önbelleğe alınmaz. Önbelleği temizlemek için, bağlanma işlemini kapatın ve yeniden başlatın. 

## <a name="next-steps"></a>Sonraki adımlar
Yeni sunucunuza henüz tablolu bir model dağıtmadıysanız, artık iyi bir zamandır. Daha fazla bilgi için bkz. [Azure Analysis Services’a Dağıtma](analysis-services-deploy.md).

Sunucunuza bir model dağıttıysanız, bir istemci veya tarayıcı kullanarak buna bağlanmaya hazırsınız demektir. Daha fazla bilgi edinmek için bkz. [Azure Analysis Services sunucusundan veri alma](analysis-services-connect.md).

