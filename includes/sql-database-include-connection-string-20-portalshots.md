---
title: Azure portal bağlantı dizesini alın
description: Azure portal bağlantı dizesini alın
keywords: SQL bağlantısı, bağlantı dizesi
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: b06004143d4a1ec5a2808e21b1f78833e410c195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557375"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure portal bağlantı dizesini alın
İstemci programınızın Azure SQL veritabanı ile etkileşim kurması için gerekli olan bağlantı dizesini almak için [Azure Portal](https://portal.azure.com/) kullanın.

1. **Tüm hizmetler**  >  **SQL veritabanlarını** seçin.

2. **SQL veritabanları** dikey penceresinin sol üst kısmındaki filtre metin kutusuna veritabanınızın adını girin.

3. Veritabanınızın satırını seçin.

4. Veritabanınız için dikey pencere görüntülendikten sonra, görsel olarak kolaylık sağlaması için, göz atma ve veritabanı filtrelemesinde kullandığınız dikey pencereleri daraltmak için **simge durumuna küçült** düğmesini seçin.

5. Veritabanınızın dikey penceresinde **veritabanı bağlantı dizelerini göster**' i seçin.

6. Uygun bağlantı dizesini kopyalayın. Örneğin, ADO.NET bağlantı kitaplığını kullanmayı düşünüyorsanız, **ADO.net** sekmesinden uygun dizeyi kopyalayın.

    ![Veritabanınız için ADO bağlantı dizesini kopyalayın][20-CopyAdoConnectionString]

7. Bağlantı dizesini gerektiği gibi düzenleyin. Yani, parolayı bağlantı dizesine ekleyin veya &lt; &gt; Kullanıcı adı ya da sunucu adı çok uzunsa Kullanıcı adından "@ ServerName" öğesini kaldırın.

8. Tek bir biçimde veya başka bir biçimde, bağlantı dizesi bilgilerini istemci program kodunuza yapıştırın.

Daha fazla bilgi için bkz. [bağlantı dizeleri ve yapılandırma dosyaları](/dotnet/framework/data/adonet/connection-strings-and-configuration-files).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->