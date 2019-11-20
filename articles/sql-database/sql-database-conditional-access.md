---
title: Koşullu Erişim
description: Azure SQL veritabanı ve veri ambarı için koşullu erişimi yapılandırmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827154"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Azure SQL veritabanı ve veri ambarı ile koşullu erişim (MFA)  

Azure [SQL veritabanı](sql-database-technical-overview.md), [yönetilen örnek](sql-database-managed-instance.md)ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Microsoft koşullu erişimini destekler. 

> [!NOTE]
> Bu konu başlığı, Azure SQL sunucusunun yanı sıra Azure SQL sunucusu üzerinde oluşturulmuş olan SQL Veritabanı ve SQL Veri Ambarı veritabanları için de geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır.

Aşağıdaki adımlarda, SQL Database 'in koşullu erişim ilkesini zorlamak üzere nasıl yapılandırılacağı gösterilmektedir.  

## <a name="prerequisites"></a>Ön koşullar  
- SQL veritabanınızı veya SQL veri Ambarınızı Azure Active Directory kimlik doğrulamasını destekleyecek şekilde yapılandırmanız gerekir. Belirli adımlar için bkz. [SQL veritabanı veya SQL veri ambarı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md).  
- Multi-Factor Authentication etkin olduğunda, en son SSMS gibi desteklenen bir araçla bağlanmanız gerekir. Daha fazla bilgi için bkz. [SQL Server Management Studio Için Azure SQL veritabanı Multi-Factor Authentication 'ı yapılandırma](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Azure SQL DB/DW için CA 'yı yapılandırma  
1. Portalda oturum açın, **Azure Active Directory**' yi seçin ve **koşullu erişim**' i seçin. Daha fazla bilgi için bkz. [koşullu erişim teknik başvurusu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   Koşullu erişim dikey penceresini ![](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. **Koşullu erişim ilkeleri** dikey penceresinde **Yeni ilke**' yi tıklatın, bir ad girin ve **kuralları Yapılandır**' ı tıklatın.  
3. **Atamalar**' ın altında **Kullanıcılar ve gruplar**' ı seçin, **Kullanıcılar ve gruplar**' ı seçin ve ardından koşullu erişim için kullanıcıyı veya grubu seçin. **Seç**' e ve ardından seçiminizi kabul etmek için **bitti** ' ye tıklayın.  
   ![kullanıcıları ve grupları seçin](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. **Bulut uygulamaları**' nı seçin, **Uygulama Seç**' e tıklayın. Koşullu erişim için kullanılabilen tüm uygulamaları görürsünüz. **Azure SQL veritabanı**' nı seçin, alt kısımdaki **Seç**' e tıklayın ve ardından **bitti**' ye tıklayın.  
   SQL veritabanı](./media/sql-database-conditional-access/select-sql-database.png) ![seçin  
   Aşağıdaki üçüncü ekran görüntüsünde listelenen **Azure SQL veritabanı** 'nı bulamıyorsanız, aşağıdaki adımları izleyin:   
   - Bir AAD yönetici hesabı ile SSMS kullanarak Azure SQL DB/DW örneğinde oturum açın.  
   - `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`yürütün.  
   - AAD 'de oturum açın ve Azure SQL veritabanı ve veri ambarının AAD 'nizdeki uygulamalarda listelendiğini doğrulayın.  

5. **Erişim denetimleri**' ni seçin, **izin ver**' i seçin ve ardından uygulamak istediğiniz ilkeyi kontrol edin. Bu örnekte, **çok faktörlü kimlik doğrulaması gerektir**' i seçeceğiz.  
   ![erişim ver ' i seçin](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Özet  
Azure AD Premium kullanarak Azure SQL DB/DW 'ye bağlanmasına izin veren seçili uygulama (Azure SQL veritabanı), artık seçili Koşullu erişim ilkesini zorunlu kılar, **çok faktörlü kimlik doğrulaması gerekir.**  
Multi-Factor Authentication ile ilgili Azure SQL veritabanı ve veri ambarı hakkında sorularınız için MFAforSQLDB@microsoft.combaşvurun.  

## <a name="next-steps"></a>Sonraki adımlar  

Öğretici için bkz. [Azure SQL veritabanınızı güvenli hale getirme](sql-database-security-tutorial.md).
