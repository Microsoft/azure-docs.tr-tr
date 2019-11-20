---
title: Azure Izleyici günlük sorgularıyla bilgisayar grupları | Microsoft Docs
description: Azure Izleyici 'de bilgisayar grupları, günlük sorgularını belirli bir bilgisayar kümesiyle kapsamlarınıza izin verir.  Bu makalede, bilgisayar grupları oluşturmak için kullanabileceğiniz farklı yöntemler ve bunların bir günlük sorgusunda nasıl kullanılacağı açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 9ef0f2810252b73921fc0a72f2e523262c760bab
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932651"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularının bilgisayar grupları
Azure Izleyici 'de bilgisayar grupları, [günlük sorgularını](../log-query/log-query-overview.md) belirli bir bilgisayar kümesiyle kapsamlarınıza izin verir.  Her grup, tanımladığınız bir sorgu kullanılarak veya farklı kaynaklardan grupları içeri aktararak bilgisayarlarla doldurulur.  Grup bir günlük sorgusuna eklendiğinde, sonuçlar gruptaki bilgisayarlarla eşleşen kayıtlarla sınırlıdır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Bilgisayar grubu oluşturma
Aşağıdaki tablodaki yöntemlerden birini kullanarak Azure Izleyici 'de bir bilgisayar grubu oluşturabilirsiniz.  Her yöntemin ayrıntıları aşağıdaki bölümlerde verilmiştir. 

| Yöntem | Açıklama |
|:--- |:--- |
| Günlük sorgusu |Bilgisayarların listesini döndüren bir günlük sorgusu oluşturun. |
| Günlük Arama API’si |Günlük sorgusunun sonuçlarına göre program aracılığıyla bir bilgisayar grubu oluşturmak için günlük araması API 'sini kullanın. |
| Active Directory |Active Directory bir etki alanının üyesi olan herhangi bir aracı bilgisayarın Grup üyeliğini otomatik olarak tarayın ve her güvenlik grubu için Azure Izleyici 'de bir grup oluşturun. (Yalnızca Windows makineler)|
| Configuration Manager | Koleksiyonları System Center Configuration Manager içeri aktarın ve Azure Izleyici 'de her biri için bir grup oluşturun. |
| Windows Server Update Services |Grupları hedeflemek için WSUS sunucularını veya istemcilerini otomatik olarak tarayın ve her biri için Azure Izleyici 'de bir grup oluşturun. |

### <a name="log-query"></a>Günlük sorgusu
Bir günlük sorgusundan oluşturulan bilgisayar grupları, tanımladığınız bir sorgu tarafından döndürülen tüm bilgisayarları içerir.  Bu sorgu, bilgisayar grubu oluşturulduğu her seferinde çalıştırılır, böylece Grup oluşturulduktan sonra yapılan tüm değişiklikler yansıtılır.  

Bilgisayar grubu için herhangi bir sorgu kullanabilirsiniz, ancak `distinct Computer`kullanarak farklı bir bilgisayar kümesi döndürmesi gerekir.  Aşağıda, bilgisayar grubu olarak kullanabileceğiniz tipik örnek bir sorgu verilmiştir.

    Heartbeat | where Computer contains "srv" | distinct Computer

Azure portal bir günlük aramasından bir bilgisayar grubu oluşturmak için aşağıdaki yordamı kullanın.

1. Azure portal **Azure izleyici** menüsünde **Günlükler** ' e tıklayın.
1. Grupta istediğiniz bilgisayarları döndüren bir sorgu oluşturun ve çalıştırın.
1. Ekranın en üstündeki **Kaydet** ' e tıklayın.
1. **Farklı kaydet** **işlevini işlevine** değiştirin ve **Bu sorguyu bir bilgisayar grubu olarak kaydet**' i seçin.
1. Tabloda açıklanan bilgisayar grubu için her bir özellik için değerler sağlayın ve **Kaydet**' e tıklayın.

Aşağıdaki tabloda bir bilgisayar grubunu tanımlayan özellikler açıklanmaktadır.

| Özellik | Açıklama |
|:---|:---|
| Adı   | Portalda görüntülenecek sorgunun adı. |
| İşlev diğer adı | Bir sorgudaki bilgisayar grubunu tanımlamak için kullanılan benzersiz bir diğer ad. |
| Kategori       | Portaldaki sorguları düzenlemek için kategori. |


### <a name="active-directory"></a>Active Directory
Azure Izleyici 'yi Active Directory grup üyeliklerini içeri aktarmak üzere yapılandırdığınızda, Windows etki alanına katılmış herhangi bir bilgisayarın Grup üyeliğini Log Analytics aracısıyla analiz eder.  Active Directory içindeki her güvenlik grubu için Azure Izleyici 'de bir bilgisayar grubu oluşturulur ve her Windows bilgisayarı, üyesi oldukları güvenlik gruplarına karşılık gelen bilgisayar gruplarına eklenir.  Bu üyelik, her 4 saatte bir sürekli güncelleştirilir.  

> [!NOTE]
> İçeri aktarılan Active Directory grupları yalnızca Windows makinelerini içerir.

Azure Izleyici 'yi, Azure portal Log Analytics çalışma alanınızdaki **Gelişmiş ayarlardan** Active Directory güvenlik gruplarını içeri aktaracak şekilde yapılandırırsınız.  **Bilgisayar grupları**' nı seçin, **Active Directory**ve sonra **Active Directory grup üyeliklerini bilgisayarlardan içeri aktarın**.  Başka bir yapılandırma işlemi gerekmez.

![Active Directory bilgisayardan bilgisayar grupları](media/computer-groups/configure-activedirectory.png)

Gruplar içeri aktarıldığında, menü, algılanan grup üyeliğine sahip bilgisayarların sayısını ve içeri aktarılan grupların sayısını listeler.  Bu bağlantılardan birine tıklayarak **Computergroup** kayıtlarını bu bilgilerle döndürebilirsiniz.

### <a name="windows-server-update-service"></a>Windows Server güncelleştirme hizmeti
Azure Izleyici 'yi WSUS grup üyeliklerini içeri aktarmak üzere yapılandırdığınızda, Log Analytics aracısına sahip bilgisayarların hedefleme grubu üyeliğini analiz eder.  İstemci tarafı hedefleme kullanıyorsanız, Azure Izleyici 'ye bağlı olan ve herhangi bir WSUS hedefleme grubunun parçası olan herhangi bir bilgisayar, Grup üyeliğini Azure Izleyici 'ye içeri aktarmıştır. Sunucu tarafı hedefleme kullanıyorsanız, Grup üyeliği bilgilerinin Azure Izleyici 'ye aktarılması için Log Analytics aracısının WSUS sunucusunda yüklü olması gerekir.  Bu üyelik, her 4 saatte bir sürekli güncelleştirilir. 

Azure Izleyici 'yi, Azure portal Log Analytics çalışma alanınızdaki **Gelişmiş AYARLARDAN** WSUS gruplarını içeri aktaracak şekilde yapılandırırsınız.  **Bilgisayar grupları**, **WSUS**ve ardından **WSUS grup üyeliklerini içeri aktar**' ı seçin.  Başka bir yapılandırma işlemi gerekmez.

![WSUS 'den bilgisayar grupları](media/computer-groups/configure-wsus.png)

Gruplar içeri aktarıldığında, menü, algılanan grup üyeliğine sahip bilgisayarların sayısını ve içeri aktarılan grupların sayısını listeler.  Bu bağlantılardan birine tıklayarak **Computergroup** kayıtlarını bu bilgilerle döndürebilirsiniz.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Azure Izleyicisini Configuration Manager koleksiyon üyeliklerini içeri aktaracak şekilde yapılandırdığınızda, her koleksiyon için bir bilgisayar grubu oluşturur.  Koleksiyon üyelik bilgileri, bilgisayar gruplarının güncel tutulması için 3 saatte bir alınır. 

Configuration Manager koleksiyonlarını içeri aktarabilmeniz [için önce Configuration Manager Azure izleyici 'ye bağlamanız](collect-sccm.md)gerekir.  

![SCCM 'den bilgisayar grupları](media/computer-groups/configure-sccm.png)

Koleksiyonlar içeri aktarıldığında, menü, algılanan grup üyeliğine sahip bilgisayarların sayısını ve içeri aktarılan grupların sayısını listeler.  Bu bağlantılardan birine tıklayarak **Computergroup** kayıtlarını bu bilgilerle döndürebilirsiniz.

## <a name="managing-computer-groups"></a>Bilgisayar gruplarını yönetme
Bir günlük sorgusundan oluşturulmuş bilgisayar gruplarını veya Azure portal Log Analytics çalışma alanınızdaki **Gelişmiş ayarlardan** günlük araması API 'sini görüntüleyebilirsiniz.  **Bilgisayar grupları** ' nı ve ardından **kayıtlı gruplar**' ı seçin.  

Bilgisayar grubunu silmek için **Kaldır** sütunundaki **x** simgesini tıklatın.  Grubun üyelerini döndüren grubun günlük aramasını çalıştırmak için bir grubun **üyeleri görüntüle** simgesine tıklayın.  Bir bilgisayar grubunu değiştiremezsiniz, bunun yerine, değiştirilen ayarlarla onu silip yeniden oluşturmanız gerekir.

![Kayıtlı bilgisayar grupları](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Bir günlük sorgusunda bilgisayar grubu kullanma
Bir sorgudaki bir günlük sorgusundan oluşturulmuş bir bilgisayar grubunu, diğer adını bir işlev olarak düşünerek, genellikle aşağıdaki sözdizimi ile kullanabilirsiniz:

  `Table | where Computer in (ComputerGroup)`

Örneğin, yalnızca mycomputergroup adlı bir bilgisayar grubundaki bilgisayarlar için UpdateSummary kayıtlarını döndürmek üzere aşağıdakileri kullanabilirsiniz.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


İçeri aktarılan bilgisayar grupları ve dahil edilen bilgisayarları **Computergroup** tablosunda depolanır.  Örneğin, aşağıdaki sorgu Active Directory etki alanı bilgisayarları grubundaki bilgisayarların listesini döndürür. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Aşağıdaki sorgu, yalnızca etki alanı bilgisayarlarındaki bilgisayarlar için UpdateSummary kayıtları döndürür.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Bilgisayar grubu kayıtları
Active Directory veya WSUS 'tan oluşturulan her bilgisayar grubu üyeliğinin Log Analytics çalışma alanında bir kayıt oluşturulur.  Bu kayıtlar bir **bilgisayar grubu** türüne sahiptir ve aşağıdaki tabloda bulunan özelliklere sahiptir.  Günlük sorgularına dayalı bilgisayar grupları için kayıtlar oluşturulmaz.

| Özellik | Açıklama |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*Dir* |
| `Computer` |Üye bilgisayarın adı. |
| `Group` |Grubun adı. |
| `GroupFullName` |Kaynak ve kaynak adı dahil olmak üzere grubun tam yolu. |
| `GroupSource` |Grubun toplandığı kaynak. <br><br>Ayarlandığı<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Grubun toplandığı kaynağın adı.  Active Directory için, bu etki alanı adıdır. |
| `ManagementGroupName` |SCOM aracıları için yönetim grubunun adı.  Diğer aracılar için bu, AOI-\<çalışma alanı KIMLIĞIDIR\> |
| `TimeGenerated` |Bilgisayar grubunun oluşturulduğu veya güncelleştirildiği tarih ve saat. |

## <a name="next-steps"></a>Sonraki adımlar
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.  

