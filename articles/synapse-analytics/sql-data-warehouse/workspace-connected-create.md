---
title: SYNAPSE çalışma alanı özelliklerini etkinleştirme
description: Bu belgede, bir kullanıcının var olan ayrılmış bir SQL havuzunda (eski adıyla SQL DW) SYNAPSE çalışma alanı özelliklerini nasıl etkinleştirebilmesi açıklanmaktadır.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 605f495fbc5913663499f5411abcaa531cea97ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312221"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Adanmış bir SQL havuzu için SYNAPSE çalışma alanı özelliklerini etkinleştirme (eski adıyla SQL DW)

Tüm SQL veri ambarı kullanıcıları artık SYNAPSE Studio ve çalışma alanı aracılığıyla mevcut bir adanmış SQL havuzuna (eski adıyla SQL DW) erişebilir ve bunları kullanabilir. Kullanıcılar Otomasyon, bağlantı veya araç araçlarını etkilemeden SYNAPSE Studio ve çalışma alanını kullanabilir. Bu makalede mevcut bir Azure SYNAPSE Analytics kullanıcısının, var olan ayrılmış bir SQL Havuzu (eski adıyla SQL DW) için SYNAPSE çalışma alanı özelliklerini nasıl etkinleştirebileceği açıklanmaktadır. Kullanıcı, artık SYNAPSE çalışma alanı ve Studio aracılığıyla kullanılabilen yeni özellik zengin özelliklerinden yararlanarak mevcut analiz çözümünü genişletebilir.   

## <a name="prerequisites"></a>Önkoşullar
Veri Ambarınızda SYNAPSE çalışma alanı özelliklerini etkinleştirmeden önce, aşağıdakileri kullandığınızdan emin olmalısınız
- SQL mantıksal sunucusunda barındırılan SQL kaynaklarını oluşturma ve yönetme hakları.
- SQL Server ana bilgisayar üzerinde yazma izinleri. 
- Azure SYNAPSE kaynakları oluşturma hakkı.
- Mantıksal sunucuda tanımlanan bir Azure Active Directory Yöneticisi

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Var olan ayrılmış bir SQL Havuzu (eski adıyla SQL DW) için SYNAPSE çalışma alanı özelliklerini etkinleştirme

SYNAPSE çalışma alanı özellikleri, desteklenen bir bölgedeki herhangi bir mevcut adanmış SQL havuzunda (eski adıyla SQL DW) etkinleştirilebilir. Bu özellik yalnızca Azure portal ile kullanılabilir.

Mevcut veri Ambarınızla ilgili bir Synapse çalışma alanı oluşturmak için bu adımları izleyin.
1. Mevcut bir adanmış SQL havuzunu (eski adıyla SQL DW) seçin ve genel bakış sayfasını açın.
2. En üstteki menü çubuğunda **Yeni SYNAPSE çalışma alanı** ' nı veya hemen aşağıda bulunan iletiyi seçin.
3. **Yeni Azure SYNAPSE çalışma alanı oluştur** sayfasında yeni SYNAPSE çalışma alanı aracılığıyla kullanılabilir hale getirilen veri ambarlarının listesini inceledikten sonra. Devam etmek için **devam** ' ı seçin.
4. Temel bilgiler sayfasında, var olan adanmış SQL Havuzu ( **Proje** ayrıntıları bölümü, mantıksal sunucu altında dağıtılan aynı **abonelik** ve **kaynak grubuyla** önceden doldurulmalıdır). Çalışma alanı **ayrıntıları**' nın altında, yeni SYNAPSE çalışma alanınız ve mantıksal sunucu arasındaki bağlantının sağlama sürecinde oluşturulabilmesi Için, **çalışma alanı** adı SQL mantıksal sunucusunun aynı adı ve bölgesiyle önceden doldurulur. Sağlama sonrası bu bağlantının, çalışma alanı ve Studio aracılığıyla adanmış SQL Havuzu (eski adıyla SQL DW) örneklerine sürekli erişiminin sağlanması için korunması gerekir.
5. Data Lake Storage Gen 2 ' yi seçmek için gidin.
6. **Yeni oluştur** ' u seçip Ileri ' yi seçmeden mevcut bir **Data Lake Storage 2.** seçin **: ağ iletişimi**.
7. **Sunucusuz örneğiniz** Için bir **SQL yönetici parolası** seçin. Bu parolanın değiştirilmesi, mantıksal sunucu SQL kimlik bilgilerini güncelleştirmez veya değiştirmez. Sistem tarafından tanımlanan bir parolayı tercih ediyorsanız bu alanları boş bırakın. Bu parola, yeni çalışma alanı içinde herhangi bir zamanda değiştirilebilir. Yönetici adı, SQL Server kullanılan aynı olmalıdır.
8. Tercih edilen **ağ ayarlarınızı** seçin ve çalışma alanı sağlamayı başlatmak Için **gözden geçir + oluştur** ' u seçin.
9. Yeni çalışma alanınızı açmak için **kaynağı git** ' i seçin.

    > [!NOTE]
    > Mantıksal sunucuda barındırılan tüm adanmış SQL Havuzu (eski adıyla SQL DW) örnekleri yeni çalışma alanı aracılığıyla kullanılabilir.

## <a name="post-provisioning-steps"></a>Sağlama sonrası adımlar
Mevcut adanmış SQL Havuzu (eski adıyla SQL DW) örneklerine SYNAPSE Studio aracılığıyla erişilebildiğinden emin olmak için aşağıdaki adımlar tamamlanmalıdır.
1. SYNAPSE çalışma alanına genel bakış sayfasında **bağlı sunucu**' yı seçin. **Bağlı sunucu** sizi, veri Ambarlarınızı BARıNDıRAN bağlı SQL mantıksal sunucusuna yönlendirir. Gerekli menüsünde **bağlı sunucu**' yı seçin.
2. **Güvenlik duvarları ve sanal ağlar** ' i açın ve istemci IP 'nizin veya önceden BELIRLENMIŞ bir IP aralığının mantıksal sunucuya erişimi olduğundan emin olun.
3. **Active Directory Yöneticisi** 'ni açın ve mantıksal sunucuda bir AAD yöneticisinin ayarlanmış olduğundan emin olun.
4. Mantıksal sunucuda barındırılan adanmış SQL Havuzu (eski adıyla SQL DW) örneklerinden birini seçin. Genel Bakış sayfasında, **SYNAPSE Studio 'Yu Başlat** ' ı seçin veya [SYNAPSE Studio 'da oturum açın](https://web.azuresynapse.net) ve çalışma alanınızda oturum açın.

5. **Veri merkezini açın ve veri** Ambarınızı sorgulayabilir ve sorgulayabilmeniz için nesne GEZGININDE adanmış SQL havuzunu genişletin.

    > [!NOTE] 
    > Bağlı bir çalışma alanı dilediğiniz zaman silinebilir. Çalışma alanının silinmesi, bağlı adanmış SQL havuzunu (eski adıyla SQL DW) silmez. Çalışma alanı özelliği, silme işlemi tamamlandığında adanmış SQL havuzunda (eski adıyla SQL DW) yeniden etkinleştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar
[SYNAPSE Workspace ve Studio](../get-started.md)ile çalışmaya başlama.
