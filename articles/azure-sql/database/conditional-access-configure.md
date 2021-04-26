---
title: Koşullu Erişim
description: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics için koşullu erişimi yapılandırma hakkında bilgi edinin.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936422"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL veritabanı ve Azure SYNAPSE Analytics ile koşullu erişim

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure [SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) , Microsoft koşullu erişimini destekler.

Aşağıdaki adımlarda, koşullu erişim ilkesini zorlamak için Azure SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE 'in nasıl yapılandırılacağı gösterilmektedir.  

## <a name="prerequisites"></a>Önkoşullar

- Azure Active Directory (Azure AD) kimlik doğrulamasını desteklemek için Azure SQL veritabanı, Azure SQL yönetilen örneği veya adanmış SQL havuzunu Azure SYNAPSE ' de yapılandırmanız gerekir. Belirli adımlar için bkz. [SQL veritabanı veya Azure SYNAPSE ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](authentication-aad-configure.md).  
- Multi-Factor Authentication etkinleştirildiğinde, en son SQL Server Management Studio (SSMS) gibi desteklenen bir araçla bağlanmanız gerekir. Daha fazla bilgi için bkz. [SQL Server Management Studio Için Azure SQL veritabanı Multi-Factor Authentication 'ı yapılandırma](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Koşullu erişim yapılandırma

> [!NOTE]
> Aşağıdaki örnekte Azure SQL veritabanı kullanılmaktadır, ancak koşullu erişimi yapılandırmak istediğiniz uygun ürünü seçmeniz gerekir.

1. Azure portal oturum açın, **Azure Active Directory**' i seçin ve **koşullu erişim**' i seçin. Daha fazla bilgi için bkz. [koşullu erişim teknik başvurusu Azure Active Directory](../../active-directory/conditional-access/concept-conditional-access-conditions.md).  
   ![Koşullu erişim dikey penceresi](./media/conditional-access-configure/conditional-access-blade.png)

2. **Koşullu erişim ilkeleri** dikey penceresinde **Yeni ilke**' yi tıklatın, bir ad girin ve **kuralları Yapılandır**' ı tıklatın.  
3. **Atamalar**' ın altında **Kullanıcılar ve gruplar**' ı seçin, **Kullanıcılar ve gruplar**' ı seçin ve ardından koşullu erişim için kullanıcıyı veya grubu seçin. **Seç**' e ve ardından seçiminizi kabul etmek için **bitti** ' ye tıklayın.  
   ![kullanıcıları ve grupları seçin](./media/conditional-access-configure/select-users-and-groups.png)  

4. **Bulut uygulamaları**' nı seçin, **Uygulama Seç**' e tıklayın. Koşullu erişim için kullanılabilen tüm uygulamaları görürsünüz. **Azure SQL veritabanı**' nı seçin, alt kısımdaki **Seç**' e tıklayın ve ardından **bitti**' ye tıklayın.  
   ![SQL veritabanı seçin](./media/conditional-access-configure/select-sql-database.png)  
   Aşağıdaki üçüncü ekran görüntüsünde listelenen **Azure SQL veritabanı** 'nı bulamıyorsanız, aşağıdaki adımları izleyin:
   - Azure AD yönetici hesabı ile SSMS 'yi kullanarak Azure SQL veritabanı 'nda veritabanınıza bağlanın.  
   - Yürütün `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Azure AD 'de oturum açın ve Azure SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE 'ın Azure AD örneğindeki uygulamalarda listelendiğini doğrulayın.  

5. **Erişim denetimleri**' ni seçin, **izin ver**' i seçin ve ardından uygulamak istediğiniz ilkeyi kontrol edin. Bu örnekte, **çok faktörlü kimlik doğrulaması gerektir**' i seçeceğiz.  
   ![erişim ver ' i seçin](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Özet

Azure AD Premium kullanan seçili uygulama (Azure SQL veritabanı) artık seçili Koşullu erişim ilkesini zorunlu kılar, **çok faktörlü kimlik doğrulaması gerekir.**

Multi-Factor Authentication ile ilgili Azure SQL veritabanı ve Azure SYNAPSE hakkında sorularınız için bkz <MFAforSQLDB@microsoft.com> ..  

## <a name="next-steps"></a>Sonraki adımlar  

Öğretici için bkz. [SQL veritabanı 'nda veritabanınızı güvenli hale getirme](secure-database-tutorial.md).