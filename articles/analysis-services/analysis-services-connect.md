---
title: Azure Analysis Services sunucularına bağlanma | Microsoft Docs
description: Azure 'da bir Analysis Services sunucusuna bağlanmayı ve bu sunucudan veri almayı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3231b46060cbb755ada000473c8fbe873cc51ef1
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147304"
---
# <a name="connecting-to-servers"></a>Sunuculara bağlanma

Bu makalede, SQL Server Management Studio (SSMS) veya Visual Studio gibi Analysis Services projelerine ya da Microsoft Excel gibi istemci raporlama uygulamalarıyla veri modelleme ve yönetim uygulamalarını kullanarak bir sunucuya bağlanma açıklanmaktadır Power BI Masaüstü veya özel uygulamalar. Azure Analysis Services bağlantıları HTTPS kullanır.

## <a name="client-libraries"></a>İstemci kitaplıkları

[En son Istemci kitaplıklarını al](analysis-services-data-providers.md)

Türüne bakılmaksızın sunucuya yapılan tüm bağlantılar, bir Analysis Services sunucusuyla bağlantı kurmak için güncelleştirilmiş AMO, ADOMD.NET ve OLEDB istemci kitaplıklarını gerektirir. SSMS, Visual Studio, Excel 2016 ve üzeri ve Power BI için en son istemci kitaplıkları, aylık yayınlar yüklenir veya güncelleştirilir. Ancak bazı durumlarda, bir uygulamanın en son sürümü bulunmayabilir. Örneğin, ilkelerin güncelleştirmeleri ertelenmesi veya Office 365 güncelleştirmeleri ertelenmiş kanaldır.

## <a name="server-name"></a>Sunucu adı

Azure 'da bir Analysis Services sunucusu oluşturduğunuzda, benzersiz bir ad ve sunucunun oluşturulacağı bölgeyi belirtirsiniz. Sunucu adını bir bağlantıda belirtirken, sunucu adlandırma şeması şu şekilde olur:

```
<protocol>://<region>/<servername>
```
 Protokol, bir dize **asazure**olduğunda, bölgesi sunucunun oluşturulduğu URI 'dir (örneğin, westus.asazure.Windows.net) ve ServerName bölge içindeki benzersiz sunucunuzun adıdır.

### <a name="get-the-server-name"></a>Sunucu adını al

**Azure portal** > Server > **genel bakış** > **sunucu adı**' nda, tüm sunucu adını kopyalayın. Kuruluşunuzdaki diğer kullanıcılar da bu sunucuya bağlanıyorsa, bu sunucu adını bunlarla paylaşabilirsiniz. Bir sunucu adı belirtirken, yolun tamamının kullanılması gerekir.

![Azure'da sunucu adını alma](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Doğu ABD 2 bölgenin Protokolü **aspaaseastus2**.

## <a name="connection-string"></a>Bağlantı dizesi

Tablosal nesne modeli kullanarak Azure Analysis Services bağlanırken aşağıdaki bağlantı dizesi biçimlerini kullanın:

###### <a name="integrated-azure-active-directory-authentication"></a>Tümleşik Azure Active Directory kimlik doğrulaması

Tümleşik kimlik doğrulaması, varsa Azure Active Directory kimlik bilgisi önbelleğini seçer. Aksi takdirde, Azure oturum açma penceresi gösterilir.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Kullanıcı adı ve parolayla kimlik doğrulaması Azure Active Directory

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows kimlik doğrulaması (tümleşik güvenlik)

Geçerli işlemi çalıştıran Windows hesabını kullanın.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>. Odc dosyası kullanarak bağlanma

Excel 'in daha eski sürümleriyle, kullanıcılar bir Office veri bağlantısı (. odc) dosyası kullanarak bir Azure Analysis Services sunucusuna bağlanabilir. Daha fazla bilgi için bkz. [Office veri bağlantısı (. odc) dosyası oluşturma](analysis-services-odc.md).


## <a name="next-steps"></a>Sonraki adımlar

[Excel    bağlanma](analysis-services-connect-excel.md)  
[Power BI  bağlama](analysis-services-connect-pbi.md)  
[Sunucunuzu Yönetin](analysis-services-manage.md)   

