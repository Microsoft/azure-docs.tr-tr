---
title: Azure Logic Apps için yönetilen bağlayıcılar
description: Azure Logic Apps kullanarak diğer uygulamaları, verileri, hizmetleri ve sistemleri tümleştiren otomatik iş akışları oluşturmak için Microsoft tarafından yönetilen Tetikleyicileri ve eylemleri kullanın.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797180"
---
# <a name="managed-connectors-for-logic-apps"></a>Logic Apps için yönetilen bağlayıcılar

[Yönetilen bağlayıcılar](apis-list.md) , [yerleşik Tetikleyiciler ve eylemlerin](built-in.md) kullanılamadığı diğer hizmetlere ve sistemlere erişmeniz için yollar sağlar. Bu Tetikleyicileri ve eylemleri, verileri, uygulamaları, bulut tabanlı Hizmetleri ve şirket içi sistemleri tümleştiren iş akışları oluşturmak için kullanabilirsiniz. Yerleşik Tetikleyiciler ve eylemlerle karşılaştırıldığında, bu bağlayıcılar genellikle Azure Blob depolama, Office 365, SQL, Salesforce veya SFTP sunucuları gibi belirli bir hizmete veya sisteme bağlanır. Microsoft tarafından yönetilen ve Azure 'da barındırılan, yönetilen bağlayıcılar genellikle ilk olarak iş akışınızda bir bağlantı oluşturmanızı ve kimliğinizi doğrulayacağınızı gerektirir. Yineleme tabanlı ve Web kancası tabanlı tetikleyiciler kullanılabilir, bu nedenle yineleme tabanlı bir tetikleyici kullanıyorsanız [yinelenme davranışına genel bakış](apis-list.md#recurrence-behavior)' ı inceleyin.

Azure Service Bus, Azure Işlevleri, SQL, AS2 vb. gibi birkaç hizmet, sistem ve protokol için, Logic Apps yerleşik sürümler de sağlar. Sayı ve Aralık, çok kiracılı bir mantıksal uygulama veya tek kiracılı mantıksal uygulama oluşturup oluşturmadığına göre farklılık gösterir. Birkaç durumda, hem yerleşik bir sürüm hem de bir yönetilen bağlayıcı sürümü mevcuttur. Çoğu durumda, yerleşik sürüm daha iyi performans, özellik, fiyatlandırma vb. sağlar. Örneğin, [AS2 protokolünü kullanarak B2B iletileri alışverişi](../logic-apps/logic-apps-enterprise-integration-as2.md)yapmak için, yalnızca (kullanım dışı) yönetilen bağlayıcı sürümünde kullanılabilen izleme özelliklerine ihtiyacınız yoksa yerleşik sürümü seçin.

Logic Apps için bazı yönetilen bağlayıcılar birden çok alt kategoriye ait. Örneğin, SAP Bağlayıcısı hem [Kurumsal bağlayıcı](#enterprise-connectors) hem de [Şirket içi bağlayıcıdır](#on-premises-connectors).

* [Standart bağlayıcılar](#standard-connectors) , Azure Blob depolama, Office 365, SharePoint, Salesforce, Power BI, OneDrive ve çok daha fazlası gibi hizmetlere erişim sağlar.
* Şirket [içi bağlayıcılar](#on-premises-connectors) SQL Server, SharePoint Server, SAP, Oracle DB, dosya paylaşımları ve diğerleri gibi şirket içi sistemlere erişim sağlar.
* [Tümleştirme hesabı bağlayıcıları](#integration-account-connectors) , XML 'yi dönüştürmenizi ve doğrulamanızı, düz dosyaları kodlayıp kodunu ÇÖZMENIZI ve AS2, Ediolgu ve x12 protokollerini kullanarak işletmeden IŞLETMEYE (B2B) iletileri işlemesini sağlar. 

## <a name="standard-connectors"></a>Standart bağlayıcılar

Azure Logic Apps, bu hizmetleri ve sistemleri kullanarak otomatik iş akışları oluşturmak için bu popüler standart bağlayıcıları sağlar. Bazı standart bağlayıcılar [Şirket içi sistemleri](#on-premises-connectors) veya [tümleştirme hesaplarını](#integration-account-connectors)da destekler.

Bazı Logic Apps Standart bağlayıcılar [Şirket içi sistemleri](#on-premises-connectors) veya [tümleştirme hesaplarını](#integration-account-connectors)destekler.

:::row:::
    :::column:::
        [![Logic Apps yönetilen bağlayıcı simgesini Azure Service Bus][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Logic Apps’te en yaygın kullanılan bağlayıcı ile zaman uyumsuz iletileri, oturumları ve konu aboneliklerini yönetin.
    :::column-end:::
    :::column:::
        [![Logic Apps yönetilen bağlayıcı simgesini SQL Server][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Kayıtları yönetebilmeniz, saklı yordamları çalıştırabilmeniz veya sorgular gerçekleştirebilmeniz için, şirket içi SQL Server veya buluttaki bir Azure SQL veritabanı 'na bağlanın.
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure blog depolama yönetilen bağlayıcı simgesi][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure blog depolaması**][azure-blob-storage-doc]
        \
        \
        Blob içeriğini oluşturabilmeniz ve yönetebilmeniz için Azure depolama hesabınıza bağlanın.
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Office 365 Outlook Managed Connector simgesi][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        E-postalar, görevler, takvim olayları ve toplantılar, kişiler, istekler ve daha fazlasını oluşturabilmeniz ve yönetebilmeniz için iş veya okul e-posta hesabınıza bağlanın.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![STFP-SSH Managed Connector simgesi Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Dosyalarınızla ve klasörlerinizle çalışabilmeniz amacıyla SSH kullanarak İnternet'ten erişim sağlamak için SFTP sunucularına bağlanın.
    :::column-end:::
    :::column:::
        [![Logic Apps 'da SharePoint Online Managed Connector simgesi][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Dosyaları, ekleri, klasörleri ve daha fazlasını yönetmek için SharePoint Online'a bağlanın.
    :::column-end:::
    :::column:::
        [![Azure kuyrukları yönetilen bağlayıcı simgesi Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure Kuyrukları**][azure-queues-doc]
        \
        \
        Kuyrukları ve iletileri oluşturabilmeniz ve yönetebilmeniz için Azure depolama hesabınıza bağlanın.
    :::column-end:::
    :::column:::
        [![Logic Apps 'de FTP yönetilen bağlayıcı simgesi][ftp-icon]][ftp-doc]
        \
        \
        [**FTP**][ftp-doc]
        \
        \
        Dosya ve klasörlerle çalışabilmeniz için internet 'ten erişebileceğiniz FTP sunucularına bağlanın.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps 'de dosya sistemi tarafından yönetilen bağlayıcı simgesi][file-system-icon]][file-system-doc]
        \
        \
        [**Dosya sistemi**][file-system-doc]
        \
        \
        Dosya oluşturup yönetebilmek için şirket içi dosya paylaşımınıza bağlanın.
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure Event Hubs yönetilen bağlayıcı simgesi][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Olayları bir olay hub'ı aracılığıyla kullanın ve yayımlayın. Örneğin, Event Hubs ile mantıksal uygulamanızdan çıkış alın ve ardından söz konusu çıkışı gerçek zamanlı bir analiz sağlayıcısına gönderin.
    :::column-end:::
    :::column:::
        [![Logic Apps yönetilen bağlayıcı simgesini Azure Event Grid][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Örneğin, Azure kaynakları veya üçüncü taraf kaynakları değiştiğinde Event Grid tarafından yayımlanan olayları izleyin.
    :::column-end:::
    :::column:::
        [![Logic Apps 'da Salesforce yönetilen bağlayıcı simgesi][salesforce-icon]][salesforce-doc]
        \
        \
        [**Satış**][salesforce-doc]
        \
        \
        Kayıtlar, işler, nesneler ve daha fazlası gibi öğeleri oluşturup yönetebilmeniz için Salesforce hesabınıza bağlanın.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Şirket içi bağlayıcılar

Şirket içi bir sisteme bağlantı oluşturabilmeniz için önce şirket [içi veri ağ geçidini indirmeniz, yüklemeniz ve ayarlamanız][gateway-doc]gerekir. Bu ağ geçidi, gerekli ağ altyapısını ayarlamak zorunda kalmadan güvenli bir iletişim kanalı sağlar. 

Aşağıdaki bağlayıcılar, şirket içi sistemlerdeki veri ve kaynaklara erişmek için Logic Apps, yaygın olarak kullanılan bazı [Standart bağlayıcılardır](#standard-connectors) . Şirket içi bağlayıcılar listesi için bkz. [desteklenen veri kaynakları](../logic-apps/logic-apps-gateway-connection.md#supported-connections).

:::row:::
    :::column:::
        [![Logic Apps 'de BizTalk Server şirket içi bağlayıcı simgesi][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de dosya sistemi şirket içi bağlayıcı simgesi][file-system-icon]][file-system-doc]
        \
        \
        [**Dosya sistemi**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de IBM DB2 şirket içi bağlayıcı simgesi][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de IBM Informix şirket içi bağlayıcı simgesi][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps içinde MySQL şirket içi bağlayıcı simgesi][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB şirket içi bağlayıcı simgesini Logic Apps][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL şirket içi bağlayıcı simgesi Logic Apps][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de SharePoint Server şirket içi bağlayıcı simgesi][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server şirket içi bağlayıcı simgesini Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'da Teradata şirket içi bağlayıcı simgesi][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Tümleştirme hesabı bağlayıcıları

Tümleştirme hesabı bağlayıcıları, Azure Logic Apps içinde [işletmeler arası (B2B) iletişim senaryolarını](../logic-apps/logic-apps-enterprise-integration-overview.md) özellikle destekler. [Bir tümleştirme hesabı oluşturup](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , ticari iş ortakları, sözleşmeler, haritalar ve şemalar gibi B2B yapıtlarınızı tanımladıktan sonra, tümleştirme hesabı bağlayıcılarını kullanarak iletileri kodlayıp kodunu çözer, içerik dönüştürebilir ve daha fazlasını yapabilirsiniz.

Örneğin, Microsoft BizTalk Server kullanıyorsanız, [Şirket içi BizTalk Server bağlayıcısını](#on-premises-connectors)kullanarak iş akışınızda bir bağlantı oluşturabilirsiniz. Daha sonra bu tümleştirme hesabı bağlayıcılarını kullanarak iş akışınızda BizTalk benzeri işlemleri genişletebilir veya yapabilirsiniz.

> [!NOTE]
> Tümleştirme hesabı bağlayıcıları kullanabilmeniz [için, mantıksal uygulamanızı bir tümleştirme hesabına bağlamanız](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)gerekir.


:::row:::
    :::column:::
        [![Logic Apps AS2 şifre çözme eylemi simgesi][as2-icon]][as2-doc]
        \
        \
        [**AS2 kodunu çözme**][as2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps AS2 kodlama eylemi simgesi][as2-icon]][as2-doc]
        \
        \
        [**AS2 kodlaması**][as2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de EDIOLGU kod çözme eylemi simgesi][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT kodunu çözme**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps için EDIOLGU kodlama eylem simgesi][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT kodlama**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps düz dosya şifre çözme eylemi simgesi][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Düz dosya kodunu çözme**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps düz dosya kodlama eylem simgesi][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Düz dosya kodlama**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps tümleştirme hesabı eylem simgesi][integration-account-icon]][integration-account-doc]
        \
        \
        [**Tümleştirme hesabı**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps sıvı dönüştürmeleri eylem simgesi][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Sıvı dönüştürmeleri**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps x12 şifre çözme eylemi simgesi][x12-icon]][x12-decode-doc]
        \
        \
        [**X12 kodunu çözme**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps x12 kodlama eylemi simgesi][x12-icon]][x12-encode-doc]
        \
        \
        [**X12 kodlama**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps XML dönüşümleri eylem simgesi][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML dönüşümleri**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps XML doğrulama eylemi simgesi][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML doğrulaması**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Kurumsal bağlayıcılar

Aşağıdaki bağlayıcılar ek bir maliyet için kurumsal sistemlere erişim sağlar:

:::row:::
    :::column:::
        [![Logic Apps 'de IBM 3270 kurumsal bağlayıcı simgesi][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**Ibm 3270** kurumsal Bağlayıcısı][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de IBM MQ kurumsal bağlayıcı simgesi][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** kurumsal bağlayıcı][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de SAP Enterprise Connector simgesi][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** kurumsal bağlayıcı][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>ISE bağlayıcıları

Bir tümleştirme hizmeti ortamında (ıSE), bu yönetilen bağlayıcılar aynı zamanda çok kiracılı sürümlerinden farklı yetenekler içeren [Ise sürümlerine](apis-list.md#ise-and-connectors)sahiptir:

> [!NOTE]
> ISE ve bağlayıcılarında çalışan Logic Apps, bu bağlayıcıların çalıştığı durumlar ne olursa olsun, tüketim tabanlı fiyatlandırma planına karşı sabit bir fiyatlandırma planını takip edin. Daha fazla bilgi için bkz. [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md) ve [Logic Apps fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/logic-apps/).

:::row:::
    :::column:::
        [![Logic Apps AS2 ıSE bağlayıcı simgesi][as2-icon]][as2-doc]
        \
        \
        [**AS2** KESIN][as2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure Otomasyonu ıSE Bağlayıcısı simgesi][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Otomasyonu** KESIN][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure Blob Storage ıSE bağlayıcı simgesi][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob depolama** KESIN][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps Azure Cosmos DB ıSE bağlayıcı simgesi][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** KESIN][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps Azure Event Hubs ıSE bağlayıcı simgesi][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** KESIN][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps Azure Event Grid ıSE bağlayıcı simgesi][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** KESIN][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure dosya depolama ıSE bağlayıcı simgesi][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure dosya depolama** KESIN][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps Azure Key Vault ıSE bağlayıcı simgesi][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** KESIN][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Izleyici oturum ıSE bağlayıcı simgesi Logic Apps][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Izleyici günlükleri** KESIN][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps Azure Service Bus ıSE bağlayıcı simgesi][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** KESIN][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure SYNAPSE Analytics ıSE Bağlayıcısı simgesi][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure SYNAPSE Analizi** KESIN][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure Tablo depolama ıSE bağlayıcı simgesi][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Tablo depolama** KESIN][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Kuyrukları ıSE bağlayıcı simgesi Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure Kuyrukları** KESIN][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de EDIOLGU bağlayıcı simgesi][edifact-icon]][edifact-doc]
        \
        \
        [**Ediolgu** KESIN][edifact-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps dosya sistemi ıSE bağlayıcı simgesi][file-system-icon]][file-system-doc]
        \
        \
        [**Dosya sistemi** KESIN][file-system-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps FTP ıSE bağlayıcı simgesi][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** KESIN][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Logic Apps 'de IBM 3270 ıSE bağlayıcı simgesi][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**Ibm 3270** KESIN][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de IBM DB2 ıSE bağlayıcı simgesi][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** KESIN][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de IBM MQ ıSE bağlayıcı simgesi][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** KESIN][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de SAP ıSE bağlayıcı simgesi][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** KESIN][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ıSE bağlayıcı simgesi Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** KESIN][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps 'de SMTP ıSE Bağlayıcısı simgesi][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** KESIN][smtp-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps SQL Server ıSE bağlayıcı simgesi][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** KESIN][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps x12 ıSE bağlayıcı simgesi][x12-icon]][x12-doc]
        \
        \
        [**X12** KESIN][x12-doc]
    :::column-end:::
:::row-end:::

Daha fazla bilgi için şu konulara bakın:

* [Azure Logic Apps Azure sanal ağ kaynaklarına erişim](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Logic Apps çağırabilmeniz için özel API 'Ler oluşturun](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Bulut ve şirket içi altyapınız için otomasyon işleri oluşturma ve yönetme"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Blob kapsayıcınızdaki dosyaları Azure blob depolama bağlayıcısı ile yönetin"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Belgelere ve saklı yordamlara erişebilmek için Azure Cosmos DB bağlanın"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Örneğin, Azure kaynakları veya üçüncü taraf kaynakları değiştiğinde Event Grid tarafından yayımlanan olayları izleme"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Logic Apps ile Event Hubs arasında olay alabilmeniz ve gönderebilmeniz için Azure Event Hubs 'e bağlanın"
[azure-file-storage-doc]: /connectors/azurefile/ "Dosya oluşturabilmek, güncelleştirebilmeniz, alabilmeniz ve silmeniz için Azure depolama hesabınıza bağlanın"
[azure-key-vault-doc]: /connectors/keyvault/ "Gizli dizilerinizi ve anahtarlarınızı yönetebilmeniz için Azure Key Vault bağlanın"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Log Analytics çalışma alanları ve Application Insights bileşenleri arasında Azure Izleyici günlüklerine yönelik sorguları çalıştırma"
[azure-queues-doc]: /connectors/azurequeues/ "Kuyrukları ve iletileri oluşturabilmeniz ve yönetebilmeniz için Azure depolama hesabınıza bağlanın"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus kuyruklardan ve konulardan ileti gönderin ve Service Bus kuyruklardan ve aboneliklerden ileti alın"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Verilerinizi görüntüleyebilmeniz için Azure SYNAPSE Analytics 'e bağlanın"
[azure-table-storage-doc]: /connectors/azuretables/ "Tabloları ve daha fazlasını oluşturabilmeniz, güncelleştirebilmeniz ve sorgulamanızı sağlamak için Azure depolama hesabınıza bağlanın"
[biztalk-server-doc]: /connectors/biztalk/ "BizTalk tabanlı uygulamaları Azure Logic Apps ile yan yana çalıştırabilmeniz için BizTalk Server bağlanın"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Şirket içi dosya sistemine bağlanın"
[ftp-doc]: ./connectors-create-api-ftp.md "Dosyaları karşıya yükleme, alma, silme ve diğer FTP görevleri için bir FTP / FTPS sunucusuna bağlanın"
[github-doc]: ./connectors-create-api-github.md "GitHub’a bağlanın ve sorunları izleyin"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google takvimine bağlanır ve takvimi yönetebilir"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Sayfalarınızı değiştirebilmeniz için Google sayfalarına bağlanın"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Görevlerinizi yönetebilmeniz için Google görevlerine bağlanır"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM ana bilgisayarları üzerinde 3270 uygulamalarına bağlanma"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Bulutta veya şirket içinde IBM DB2 'ye bağlanın. Bir satırı güncelleştirin, tablo alın ve daha fazlasını yapın"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Bulutta veya şirket içinde Informix 'e bağlanın. Bir satırı okuyun, tabloları listeleyin ve daha fazlasını yapın"
[ibm-mq-doc]: ./connectors-create-api-mq.md "İleti göndermek ve almak için IBM MQ şirket içi veya Azure 'a bağlanma"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram 'a bağlanın. Olayları tetikleme veya üzerinde işlem yapın"
[mandrill-doc]: ./connectors-create-api-mandrill.md "İletişim için Mandrill’e bağlanın"
[mysql-doc]: /connectors/mysql/ "Verileri okuyabilmeniz ve yazabilmeniz için şirket içi MySQL veritabanınıza bağlanın"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "E-posta gönderip alabilmeniz, takviminizi ve kişilerinizi yönetmek ve daha fazlasını yapmak için iş veya okul hesabınıza bağlanın"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Dosyaları karşıya yükleyebilir, silebilir, listeleyebilir ve daha fazlasını yapmak için kişisel Microsoft OneDrive 'ınıza bağlanın"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Dosyalarınızı karşıya yükleyebilir, silebilir, listeleyebilir ve daha fazlasını yapmak için iş Microsoft OneDrive 'a bağlanın"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Satırları ekleyebileceğiniz, ekleyebileceğiniz, silebilmeniz ve daha fazlasını yapmak için bir Oracle veritabanına bağlanın"
[outlook.com-doc]: ./connectors-create-api-outlook.md "E-postanızı, takvimlerinizi, kişilerinizi ve daha fazlasını yönetebilmeniz için Outlook posta kutunuza bağlanın"
[postgre-sql-doc]: /connectors/postgresql/ "Tablolardan verileri okuyabilmeniz için PostgreSQL veritabanınıza bağlanın"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce hesabınıza bağlanın. Hesapları, müşteri adaylarını, fırsatları ve daha fazlasını yönetin"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Şirket içi SAP sistemine bağlanın"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid 'e bağlanın. E-posta gönderme ve alıcı listelerini yönetme"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH kullanarak SFTP hesabınıza bağlanın. Dosyaları karşıya yükleyin, alın, silin ve daha fazlasını yapın"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "SharePoint şirket içi sunucusuna bağlanın. Belgeleri, liste öğelerini ve daha fazlasını yönetin"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "SharePoint Online 'a bağlanın. Belgeleri, liste öğelerini ve daha fazlasını yönetin"
[slack-doc]: ./connectors-create-api-slack.md "Slack’e bağlanın ve Slack kanallarında iletiler yayınlayın"
[smtp-doc]: ./connectors-create-api-smtp.md "Bir SMTP sunucusuna bağlanın ve ekleri olan e-posta gönderin"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "İletişim için SparkPost’a bağlanın"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL veritabanı 'na veya SQL Server bağlanın. SQL veritabanı tablosunda giriş oluşturma, güncelleştirme, edinme ve silme"
[teradata-doc]: /connectors/teradata/ "Tablolardaki verileri okumak için Teradata veritabanınıza bağlanın"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio 'e bağlanın. İleti gönderin ve alın, kullanılabilir numaraları alın, gelen telefon numaralarını yönetin ve daha fazlasını yapın"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube 'a bağlanın. Videolarınızı ve kanallarınızı yönetin"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 protokolünü kullanan iletileri kodlama ve kodunu çözme"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIOLGU protokolünü kullanan iletileri kodlama ve kodunu çözme"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIOLGU protokolünü kullanan iletilerin kodunu çözme"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIOLGU protokolünü kullanan iletileri kodla"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Enterprise Integration düz dosyası hakkında bilgi edinin"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Enterprise Integration düz dosyası hakkında bilgi edinin"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Tümleştirme hesabı yapıtları için meta verileri yönetme"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Likit JSON şablonlarıyla dönüştürme"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 protokolünü kullanan iletileri kodlama ve kodunu çözme"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 protokolünü kullanan iletilerin kodunu çözme"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 protokolünü kullanan iletileri kodla"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML iletilerini dönüştürme"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML iletilerini doğrula"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Şirket içi veri ağ geçidiyle mantıksal uygulamalardan şirket içi veri kaynaklarına bağlanın"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 protokolünü kullanan iletileri kodlama ve kodunu çözme"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIOLGU protokolünü kullanan iletileri kodlama ve kodunu çözme"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIOLGU protokolünü kullanan iletilerin kodunu çözme"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIOLGU protokolünü kullanan iletileri kodla"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Enterprise Integration düz dosyası hakkında bilgi edinin"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Enterprise Integration düz dosyası hakkında bilgi edinin"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Tümleştirme hesabı yapıtları için meta verileri yönetme"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Likit JSON şablonlarıyla dönüştürme"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 protokolünü kullanan iletileri kodlama ve kodunu çözme"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 protokolünü kullanan iletilerin kodunu çözme"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 protokolünü kullanan iletileri kodla"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML iletilerini dönüştürme"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML iletilerini doğrula"
