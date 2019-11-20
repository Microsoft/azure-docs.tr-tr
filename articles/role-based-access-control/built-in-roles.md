---
title: Azure kaynakları için yerleşik roller | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) ve Azure kaynakları için yerleşik rolleri açıklar. Eylemleri, NotActions, DataActions ve NotDataActions öğelerini listeler.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/28/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: dff222788480eea882614ad29478df1dce359199
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101288"
---
# <a name="built-in-roles-for-azure-resources"></a>Azure kaynakları için yerleşik roller

[Rol tabanlı erişim denetimi (RBAC)](overview.md) , Azure kaynakları için kullanıcılara, gruplara, hizmet sorumlularına ve yönetilen kimliklere atayabileceğiniz birkaç yerleşik role sahiptir. Rol atamaları, Azure kaynaklarına erişimi denetlemenize olanak sağlar. Yerleşik roller kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, [Azure kaynakları için kendi özel rollerinizi](custom-roles.md)de oluşturabilirsiniz.

Bu makalede, Azure kaynakları için her zaman gelişen yerleşik roller listelenmektedir. En son rolleri almak için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) veya [az role Definition List](/cli/azure/role/definition#az-role-definition-list)komutunu kullanın. Azure Active Directory için yönetici rolleri arıyorsanız, [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)bölümüne bakın.

## <a name="built-in-role-descriptions"></a>Yerleşik rol açıklamaları

Aşağıdaki tabloda, yerleşik her rolün kısa bir açıklaması verilmiştir. Her rol için `Actions`, `NotActions`, `DataActions`ve `NotDataActions` listesini görmek için rol adına tıklayın. Bu eylemlerin ne anlama geldiğini ve bunların yönetim ve veri düzlemleri için nasıl uygulandığını öğrenmek için bkz. [Azure kaynakları için rol tanımlarını anlama](role-definitions.md).


| Yerleşik rol | Açıklama |
| --- | --- |
| [Sahip](#owner) | Kaynaklara erişim de dahil olmak üzere her şeyi yönetmenizi sağlar. |
| [Katılımcı](#contributor) | Kaynaklara erişim verme dışında her şeyi yönetmenizi sağlar. |
| [Okuyucu](#reader) | Her şeyi görüntülemenize izin verir, ancak hiçbir değişiklik yapmamalıdır. |
| [AcrDelete](#acrdelete) | ACR silme |
| [Acrimageimzalayan](#acrimagesigner) | ACR görüntü imzalayan |
| [AcrPull](#acrpull) | ACR çekme |
| [AcrPush](#acrpush) | ACR gönderimi |
| [Acrquaranınereader](#acrquarantinereader) | ACR karantina veri okuyucusu |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR karantina veri yazıcısı |
| [API Management hizmet Katılımcısı](#api-management-service-contributor) | Hizmeti ve API 'Leri yönetebilir |
| [Hizmet Işletmeni rolü API Management](#api-management-service-operator-role) | Hizmeti yönetebilir, ancak API 'Leri yönetemez |
| [API Management hizmeti okuyucu rolü](#api-management-service-reader-role) | Hizmet ve API 'lere salt okuma erişimi |
| [Application Insights bileşeni Katılımcısı](#application-insights-component-contributor) | Application Insights bileşenlerini yönetebilir |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights Snapshot Debugger ile toplanan hata ayıklama anlık görüntülerini görüntülemek ve indirmek için kullanıcıya izin verir. Bu izinlerin [sahip](#owner) veya [katkıda](#contributor) bulunan rollerine dahil edilmediğini unutmayın. |
| [Automation Iş Işleci](#automation-job-operator) | Otomasyon Runbook 'Larını kullanarak Iş oluşturun ve yönetin. |
| [Automation Işleci](#automation-operator) | Automation Işleçleri, işleri başlatabilir, durdurabilir, askıya alabilir ve sürdürebilir |
| [Otomasyon Runbook Işleci](#automation-runbook-operator) | Runbook 'un Işlerini oluşturabilmek için Runbook özelliklerini okuyun. |
| [Avere Katılımcısı](#avere-contributor) | , Bir avere vFXT kümesi oluşturabilir ve yönetebilir. |
| [Avere Işleci](#avere-operator) | Kümeyi yönetmek için avere vFXT kümesi tarafından kullanılır |
| [Azure Event Hubs veri sahibi](#azure-event-hubs-data-owner) | Azure Event Hubs kaynaklarına tam erişim sağlar. |
| [Azure Event Hubs veri alıcısı](#azure-event-hubs-data-receiver) | Azure Event Hubs kaynaklarına erişim izni verir. |
| [Azure Event Hubs veri gönderici](#azure-event-hubs-data-sender) | Azure Event Hubs kaynaklarına erişim gönderilmesine izin verir. |
| [Azure Kubernetes hizmet kümesi yönetici rolü](#azure-kubernetes-service-cluster-admin-role) | Küme Yöneticisi kimlik bilgisi eylemini listeleyin. |
| [Azure Kubernetes hizmet kümesi Kullanıcı rolü](#azure-kubernetes-service-cluster-user-role) | Küme kullanıcı kimlik bilgilerini Listele eylemi. |
| [Azure haritalar veri okuyucu (Önizleme)](#azure-maps-data-reader-preview) | Azure haritalar hesabından ilgili harita okuma verilerine erişim izni verir. |
| [Azure Sentinel Katılımcısı](#azure-sentinel-contributor) | Azure Sentinel Katılımcısı |
| [Azure Sentinel okuyucusu](#azure-sentinel-reader) | Azure Sentinel okuyucusu |
| [Azure Sentinel Yanıtlayıcısı](#azure-sentinel-responder) | Azure Sentinel Yanıtlayıcısı |
| [Azure Service Bus veri sahibi](#azure-service-bus-data-owner) | Azure Service Bus kaynaklara tam erişim sağlar. |
| [Azure Service Bus veri alıcısı](#azure-service-bus-data-receiver) | Azure Service Bus kaynaklarına erişim izni verir. |
| [Veri Göndericisini Azure Service Bus](#azure-service-bus-data-sender) | Azure Service Bus kaynaklarına erişim izni verir. |
| [Kayıt sahibini Azure Stack](#azure-stack-registration-owner) | Azure Stack kayıtlarını yönetmenizi sağlar. |
| [Yedek Katılımcısı](#backup-contributor) | Yedekleme hizmetini yönetmenizi sağlar, ancak kasaların oluşturamaz ve başkalarına erişim izni verebilir |
| [Yedekleme Işletmeni](#backup-operator) | Yedekleme kaldırma, kasa oluşturma ve başkalarına erişim verme dışında yedekleme hizmetlerini yönetmenizi sağlar |
| [Yedekleme okuyucusu](#backup-reader) | Yedekleme hizmetlerini görüntüleyebilir, ancak değişiklik yapamaz |
| [Faturalama okuyucusu](#billing-reader) | Faturalandırma verilerine okuma erişimi sağlar |
| [BizTalk Katılımcısı](#biztalk-contributor) | BizTalk hizmetlerini yönetmenizi sağlar ancak onlara erişim izni vermez. |
| [Blok zinciri üye düğümü erişimi (Önizleme)](#blockchain-member-node-access-preview) | Blok zinciri üye düğümlerine erişim sağlar |
| [Blueprint Katılımcısı](#blueprint-contributor) | Şema tanımlarını yönetebilir, ancak atamazsınız. |
| [Blueprint Işleci](#blueprint-operator) | Varolan yayımlanmış şemaları atayabilir, ancak yeni şemaları oluşturamaz. Not: Bu yalnızca atama Kullanıcı tarafından atanan yönetilen bir kimlikle yapıldığında geçerlidir. |
| [CDN uç noktası Katılımcısı](#cdn-endpoint-contributor) | CDN uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. |
| [CDN uç nokta okuyucusu](#cdn-endpoint-reader) | CDN uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz. |
| [CDN profili Katılımcısı](#cdn-profile-contributor) | CDN profillerini ve uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. |
| [CDN profili okuyucu](#cdn-profile-reader) | CDN profillerini ve uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz. |
| [Klasik Ağ Katılımcısı](#classic-network-contributor) | Klasik ağları yönetmenize izin verir, ancak bunlara erişemez. |
| [Klasik depolama hesabı Katılımcısı](#classic-storage-account-contributor) | Klasik depolama hesaplarını yönetmenize izin verir, ancak bunlara erişimi kalmaz. |
| [Klasik depolama hesabı anahtar operatörü hizmet rolü](#classic-storage-account-key-operator-service-role) | Klasik depolama hesabı anahtar Işleçleri klasik depolama hesaplarında anahtarları listeleme ve yeniden oluşturma izni verilir |
| [Klasik sanal makine Katılımcısı](#classic-virtual-machine-contributor) | Klasik sanal makineleri yönetmenizi sağlar ancak bunlara yönelik erişimi, bağlı oldukları sanal ağ veya depolama hesabı için değil. |
| [Bilişsel hizmetler Katılımcısı](#cognitive-services-contributor) | Bilişsel hizmetler için anahtar oluşturma, okuma, güncelleştirme, silme ve yönetme olanağı sağlar. |
| [Bilişsel hizmetler veri okuyucu (Önizleme)](#cognitive-services-data-reader-preview) | Bilişsel hizmetler verilerini okumanızı sağlar. |
| [Bilişsel hizmetler kullanıcısı](#cognitive-services-user) | Bilişsel hizmetler 'in anahtarlarını okuyup listelemenizi sağlar. |
| [Cosmos DB hesabı okuyucu rolü](#cosmos-db-account-reader-role) | Azure Cosmos DB hesabı verilerini okuyabilir. Azure Cosmos DB hesaplarını yönetmek için [DocumentDB hesabı katılımcısı](#documentdb-account-contributor) konusuna bakın. |
| [Cosmos DB Işleci](#cosmos-db-operator) | Azure Cosmos DB hesaplarını yönetmenizi sağlar ancak içerdikleri verilere erişemez. Hesap anahtarlarına ve bağlantı dizelerine erişimi engeller. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Bir Cosmos DB veritabanı veya bir hesabın kapsayıcısı için geri yükleme isteği gönderebilir |
| [Maliyet yönetimi Katılımcısı](#cost-management-contributor) | Maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir (örn. bütçeler, dışarı aktarmalar) |
| [Maliyet yönetimi okuyucusu](#cost-management-reader) | Maliyet verilerini ve yapılandırmayı görüntüleyebilir (örneğin, bütçeler, dışarı aktarmalar) |
| [Katkıda bulunan Data Box](#data-box-contributor) | , Diğer kullanıcılara erişim izni hariç Data Box hizmeti altındaki her şeyi yönetmenizi sağlar. |
| [Data Box okuyucu](#data-box-reader) | Sipariş oluşturma veya düzenleme sırası ayrıntıları ve başkalarına erişim verme dışında Data Box hizmetini yönetmenizi sağlar. |
| [Katkıda bulunan Data Factory](#data-factory-contributor) | Veri fabrikalarının yanı sıra bunların içindeki alt kaynakları oluşturun ve yönetin. |
| [Data Lake Analytics geliştirici](#data-lake-analytics-developer) | Kendi işlerinizi göndermenize, izlemenize ve yönetmenize izin verir, ancak Data Lake Analytics Hesapları oluşturamaz veya silemezsiniz. |
| [Veri Takiger](#data-purger) | Analiz verilerini temizedebilir |
| [DevTest Labs kullanıcısı](#devtest-labs-user) | Azure DevTest Labs sanal makinelerinizi bağlamanıza, başlatmanıza, yeniden başlatmanıza ve kapatımanıza olanak sağlar. |
| [DNS bölgesi Katılımcısı](#dns-zone-contributor) | Azure DNS, DNS bölgelerini ve kayıt kümelerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. |
| [DocumentDB hesabı Katılımcısı](#documentdb-account-contributor) | , Azure Cosmos DB hesaplarını yönetebilir. Azure Cosmos DB daha önce DocumentDB olarak bilinirdi. |
| [EventGrid EventSubscription Katılımcısı](#eventgrid-eventsubscription-contributor) | EventGrid olay aboneliği işlemlerini yönetmenizi sağlar. |
| [EventGrid EventSubscription okuyucusu](#eventgrid-eventsubscription-reader) | EventGrid olay aboneliklerini okumanızı sağlar. |
| [HDInsight küme operatörü](#hdinsight-cluster-operator) | HDInsight küme yapılandırmasını okuyup değiştirmenize izin verir. |
| [HDInsight etki alanı Hizmetleri Katılımcısı](#hdinsight-domain-services-contributor) | HDInsight için gereken etki alanı Hizmetleri ile ilgili işlemleri okuyabilir, oluşturabilir, değiştirebilir ve silebilir Kurumsal Güvenlik Paketi |
| [Intelligent Systems hesabı Katılımcısı](#intelligent-systems-account-contributor) | Akıllı sistem hesaplarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. |
| [Katkıda bulunan Key Vault](#key-vault-contributor) | Anahtar kasalarını yönetmenize izin verir, ancak bunlara erişim sağlamaz. |
| [Laboratuvar Oluşturucu](#lab-creator) | Azure Laboratuvar hesaplarınız kapsamında yönetilen Laboratuvarlarınızı oluşturmanıza, yönetmenize ve silmenize olanak sağlar. |
| [Katkıda bulunan Log Analytics](#log-analytics-contributor) | Log Analytics katkı, tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. İzleme ayarlarını düzenlediğinizde VM 'lere VM uzantısının eklenmesi dahildir; Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okuma; Otomasyon hesapları oluşturma ve yapılandırma; çözümler ekleme; ve Azure tanılama 'yı tüm Azure kaynaklarında yapılandırma. |
| [Log Analytics okuyucu](#log-analytics-reader) | Log Analytics okuyucu tüm izleme verilerini görüntüleyip arayabilir ve tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntüleme dahil olmak üzere izleme ayarlarını görüntüleyebilir. |
| [Mantıksal uygulama Katılımcısı](#logic-app-contributor) | Mantıksal uygulamaları yönetmenize izin verir, ancak bunlara erişimi değiştirmeyin. |
| [Logic App Işleci](#logic-app-operator) | Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremez. |
| [Yönetilen uygulama Işletmeni rolü](#managed-application-operator-role) | Yönetilen uygulama kaynakları üzerinde işlemleri okuyup gerçekleştirmenize olanak tanır |
| [Yönetilen uygulamalar okuyucusu](#managed-applications-reader) | Yönetilen bir uygulamadaki kaynakları okumanızı ve JıT erişimi isteğinizi yapmanızı sağlar. |
| [Yönetilen kimlik Katılımcısı](#managed-identity-contributor) | Kullanıcı tarafından atanan kimlik oluşturma, okuma, güncelleştirme ve silme |
| [Yönetilen kimlik Işleci](#managed-identity-operator) | Kullanıcı tarafından atanan kimliği okuma ve atama |
| [Yönetilen hizmetler kayıt ataması rol silme](#managed-services-registration-assignment-delete-role) | Yönetilen hizmetler kayıt ataması silme rolü, kiracı kullanıcılarının kiracıya atanan kayıt atamasını silmesine izin verir. |
| [Yönetim grubu Katılımcısı](#management-group-contributor) | Yönetim grubu katılımcısı rolü |
| [Yönetim grubu okuyucusu](#management-group-reader) | Yönetim grubu okuyucusu rolü |
| [Katkıda bulunan izleniyor](#monitoring-contributor) | Tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Ölçüm yayımcısını izleme](#monitoring-metrics-publisher) | Azure kaynaklarında ölçüm yayımlamaya izin vermez |
| [İzleme okuyucusu](#monitoring-reader) | Tüm izleme verilerini okuyabilir (ölçümler, Günlükler vb.). Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Ağ Katılımcısı](#network-contributor) | Ağları yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. |
| [Yeni relik APM hesabı Katılımcısı](#new-relic-apm-account-contributor) | New Relic Application Performance Management hesaplarını ve uygulamaları yönetmenize izin verir, ancak bunlara erişimi kalmaz. |
| [Okuyucu ve veri erişimi](#reader-and-data-access) | Her şeyi görüntülemenize izin verir, ancak bir depolama hesabını veya kapsanan kaynağı silmenize veya oluşturmanıza izin vermez. Ayrıca depolama hesabı anahtarlarına erişim aracılığıyla bir depolama hesabında bulunan tüm verilere okuma/yazma erişimi de sağlar. |
| [Katkıda bulunan Redis Cache](#redis-cache-contributor) | Redsıs önbellekler yönetmenize izin verir, ancak bunlara erişimi olmaz. |
| [Kaynak Ilkesi katılımcısı (Önizleme)](#resource-policy-contributor-preview) | Önizle Kaynak ilkesi oluşturma/değiştirme, destek bileti oluşturma ve kaynakları/hiyerarşiyi okuma haklarıyla EA 'dan Kullanıcı geri alma. |
| [Zamanlayıcı Iş koleksiyonları Katılımcısı](#scheduler-job-collections-contributor) | Zamanlayıcı iş koleksiyonlarını yönetmenizi sağlar, ancak bunlara erişimi kalmaz. |
| [Katkıda bulunan Arama Hizmeti](#search-service-contributor) | Arama hizmetlerini yönetmenize izin verir, ancak bunlara erişim izni vermez. |
| [Güvenlik Yöneticisi](#security-admin) | Yalnızca güvenlik merkezi 'nde: güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, güvenlik ilkelerini düzenleyebilir, uyarıları ve önerileri görüntüleyebilir, uyarıları ve önerileri kapatabilir |
| [Güvenlik Yöneticisi (eski)](#security-manager-legacy) | Bu eski bir roldür. Lütfen bunun yerine Güvenlik Yöneticisi 'ni kullanın |
| [Güvenlik okuyucusu](#security-reader) | Yalnızca güvenlik merkezi 'nde: önerileri ve uyarıları görüntüleyebilir, güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz |
| [Katkıda bulunan Site Recovery](#site-recovery-contributor) | Kasa oluşturma ve rol atama dışında Site Recovery hizmetini yönetmenizi sağlar |
| [Site Recovery Işleci](#site-recovery-operator) | Yük devretme ve yeniden çalışma ve diğer Site Recovery yönetim işlemlerini gerçekleştirmenize izin verir |
| [Site Recovery okuyucu](#site-recovery-reader) | Site Recovery durumunu görüntülemenize izin verir, ancak diğer yönetim işlemlerini gerçekleştirmenize izin vermez |
| [Uzamsal bağlayıcı hesabı Katılımcısı](#spatial-anchors-account-contributor) | Hesabınızdaki uzamsal bağlantıları yönetmenizi sağlar, ancak onları silmez |
| [Uzamsal bağlayıcı hesap sahibi](#spatial-anchors-account-owner) | Hesabınızdaki uzamsal bağlayıcıları, silme dahil olmak üzere yönetmenizi sağlar |
| [Uzamsal bağlayıcı hesap okuyucu](#spatial-anchors-account-reader) | Hesabınızdaki uzamsal Tutturucuların özelliklerini bulmanıza ve okumanızı sağlar |
| [SQL DB Katılımcısı](#sql-db-contributor) | SQL veritabanlarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. Ayrıca, güvenlikle ilgili ilkeleri veya bunların üst SQL sunucularını yönetemezsiniz. |
| [SQL yönetilen örnek Katılımcısı](#sql-managed-instance-contributor) | SQL yönetilen örnekleri ve gerekli ağ yapılandırmasını yönetmenizi sağlar, ancak başkalarına erişim izni veremeyiz. |
| [SQL Güvenlik Yöneticisi](#sql-security-manager) | , SQL Server ve veritabanlarının güvenlikle ilgili ilkelerini yönetmenizi sağlar, ancak bunlara erişemez. |
| [Katkıda bulunan SQL Server](#sql-server-contributor) | SQL Server ve veritabanlarını yönetmenizi sağlar, ancak bunlara yönelik erişimleri ve güvenlikle ilgili ilkeleri yönetemez. |
| [Depolama Hesabı Katılımcısı](#storage-account-contributor) | Depolama hesaplarının yönetimine izin verir. Paylaşılan anahtar yetkilendirmesi aracılığıyla verilere erişmek için kullanılabilen hesap anahtarına erişim sağlar. |
| [Depolama hesabı anahtar operatörü hizmet rolü](#storage-account-key-operator-service-role) | Depolama hesabı erişim anahtarlarının listelenmesi ve yeniden oluşturulmasına izin verir. |
| [Depolama Blobu veri Katılımcısı](#storage-blob-data-contributor) | Azure depolama kapsayıcıları ve bloblarını okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Depolama Blobu veri sahibi](#storage-blob-data-owner) | , POSIX erişim denetimi atama dahil olmak üzere Azure depolama blob kapsayıcılarına ve verilerine tam erişim sağlar. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Depolama Blobu veri okuyucusu](#storage-blob-data-reader) | Azure depolama kapsayıcıları ve bloblarını okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Depolama Blobu Delegator](#storage-blob-delegator) | Azure AD kimlik bilgileriyle imzalanan bir kapsayıcı veya blob için paylaşılan erişim imzası oluşturmak üzere kullanılabilen bir Kullanıcı temsili anahtarı alın. Daha fazla bilgi için bkz. [Kullanıcı TEMSILI SAS oluşturma](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
| [Depolama dosyası veri SMB paylaşımında katkıda bulunan](#storage-file-data-smb-share-contributor) | SMB üzerinden Azure depolama dosya paylaşımlarında okuma, yazma ve silme erişimine izin verir |
| [Depolama dosyası veri SMB paylaşımında yükseltilmiş katkıda bulunan](#storage-file-data-smb-share-elevated-contributor) | SMB üzerinden Azure depolama dosya paylaşımlarında NTFS izin erişimini okuma, yazma, silme ve değiştirme sağlar |
| [Depolama dosyası veri SMB paylaşma okuyucusu](#storage-file-data-smb-share-reader) | SMB üzerinden Azure dosya paylaşımında okuma erişimine izin verir |
| [Depolama kuyruğu verileri Katılımcısı](#storage-queue-data-contributor) | Azure depolama kuyruklarını ve sıra iletilerini okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Depolama kuyruğu veri Iletisi Işlemcisi](#storage-queue-data-message-processor) | Azure depolama kuyruğundan bir iletiyi göz atın, alın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Depolama kuyruğu veri Iletisi gönderici](#storage-queue-data-message-sender) | Bir Azure depolama kuyruğuna ileti ekleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Depolama kuyruğu veri okuyucusu](#storage-queue-data-reader) | Azure depolama kuyruklarını ve sıra iletilerini okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Destek Isteği Katılımcısı](#support-request-contributor) | Destek istekleri oluşturmanızı ve yönetmenizi sağlar |
| [Katkıda bulunan Traffic Manager](#traffic-manager-contributor) | Traffic Manager profillerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. |
| [Kullanıcı Erişimi Yöneticisi](#user-access-administrator) | Azure kaynaklarına Kullanıcı erişimini yönetmenizi sağlar. |
| [Sanal Makine Yöneticisi oturum açma](#virtual-machine-administrator-login) | Portalda sanal makineleri görüntüleme ve yönetici olarak oturum açma |
| [Sanal Makine Katılımcısı](#virtual-machine-contributor) | Sanal makineleri yönetmenize izin verir, ancak bunlara bağlı oldukları sanal ağ veya depolama hesabına erişemez. |
| [Sanal makine Kullanıcı oturumu açma](#virtual-machine-user-login) | Portalda sanal makineleri görüntüleyin ve normal bir kullanıcı olarak oturum açın. |
| [Web planı Katılımcısı](#web-plan-contributor) | Web siteleri için Web planlarını yönetmenizi sağlar, ancak bunlara erişemez. |
| [Web sitesi Katılımcısı](#website-contributor) | Web sitelerini yönetmenizi sağlar, ancak bunlara erişemez. |


## <a name="owner"></a>Sahip
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kaynaklara erişim de dahil olmak üzere her şeyi yönetmenizi sağlar. |
> | **Kimlik** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Eylemler** |  |
> | * | Tüm türlerin kaynaklarını oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="contributor"></a>Katılımcı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kaynaklara erişim verme dışında her şeyi yönetmenizi sağlar. |
> | **Kimlik** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Eylemler** |  |
> | * | Tüm türlerin kaynaklarını oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. Authorization/*/Delete | Rolleri, ilke atamalarını, ilke tanımlarını ve ilke kümesi tanımlarını Sil |
> | Microsoft. Authorization/*/Write | Roller, rol atamaları, ilke atamaları, ilke tanımları ve ilke kümesi tanımları oluşturun |
> | Microsoft. Authorization/yükseltir Teaccess/Action | Arayan kullanıcının kiracı kapsamındaki Kullanıcı erişimi yöneticisi erişimine izin verir |
> | Microsoft. Blueprint/Blueprintasbir/Write | Şema atamalarını oluşturun veya güncelleştirin |
> | Microsoft. Blueprint/Blueprintasbir/Delete | Tüm şema atamalarını silme |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="reader"></a>Okuyucu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Her şeyi görüntülemenize izin verir, ancak hiçbir değişiklik yapmamalıdır. |
> | **Kimlik** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | ACR silme |
> | **Kimlik** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Eylemler** |  |
> | Microsoft. ContainerRegistry/kayıt defterleri/yapıtlar/silme | Bir kapsayıcı kayıt defterinde yapıtı silin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="acrimagesigner"></a>Acrimageimzalayan
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | ACR görüntü imzalayan |
> | **Kimlik** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Eylemler** |  |
> | Microsoft. ContainerRegistry/kayıt defterleri/imzala/yaz | Kapsayıcı kayıt defteri için gönderme/çekme içeriği güven meta verileri. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | ACR çekme |
> | **Kimlik** | 7f951ddav-4ed3-4680-a7ca-43fe172d538d |
> | **Eylemler** |  |
> | Microsoft. ContainerRegistry/kayıt defterleri/çekme/okuma | Bir kapsayıcı kayıt defterinden görüntüleri çekin veya alın. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | ACR gönderimi |
> | **Kimlik** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Eylemler** |  |
> | Microsoft. ContainerRegistry/kayıt defterleri/çekme/okuma | Bir kapsayıcı kayıt defterinden görüntüleri çekin veya alın. |
> | Microsoft. ContainerRegistry/kayıt defterleri/gönderme/yazma | Bir kapsayıcı kayıt defterine görüntü gönderin veya yazın. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="acrquarantinereader"></a>Acrquaranınereader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | ACR karantina veri okuyucusu |
> | **Kimlik** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Eylemler** |  |
> | Microsoft. ContainerRegistry/kayıt defterleri/karantina/okuma | Kapsayıcı kayıt defterinden karantinaya alınmış görüntüleri çekme veya alma |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | ACR karantina veri yazıcısı |
> | **Kimlik** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Eylemler** |  |
> | Microsoft. ContainerRegistry/kayıt defterleri/karantina/okuma | Kapsayıcı kayıt defterinden karantinaya alınmış görüntüleri çekme veya alma |
> | Microsoft. ContainerRegistry/kayıt defterleri/karantina/yazma | Karantinaya alınan görüntülerin karantina durumunu yazma/değiştirme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="api-management-service-contributor"></a>API Management hizmet Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Hizmeti ve API 'Leri yönetebilir |
> | **Kimlik** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Eylemler** |  |
> | Microsoft. Apimanan/hizmet/* | API Management hizmeti oluşturma ve yönetme |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="api-management-service-operator-role"></a>Hizmet Işletmeni rolü API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Hizmeti yönetebilir, ancak API 'Leri yönetemez |
> | **Kimlik** | e022efe7-F5BA-4159-bbe4-b44f577e9b61 |
> | **Eylemler** |  |
> | Microsoft. Apimanayönetimi/hizmeti/*/Read | API Management hizmeti örneklerini oku |
> | Microsoft. Apimanayönetimi/hizmeti/yedekleme/eylem | API Management hizmetini Kullanıcı tarafından sağlanmış bir depolama hesabında belirtilen kapsayıcıya Yedekle |
> | Microsoft. Apimanayönetimi/hizmeti/silme | API Management hizmeti örneğini Sil |
> | Microsoft. Apimanayönetimi/hizmet/managedağıtımlar/eylem | SKU/birimleri değiştirme, API Management hizmetinin bölgesel dağıtımlarını ekleme/kaldırma |
> | Microsoft. Apimanayönetimi/hizmeti/okuma | API Management hizmet örneği için meta verileri oku |
> | Microsoft. Apimanayönetimi/hizmeti/geri yükleme/eylem | API Management hizmetini Kullanıcı tarafından sağlanmış bir depolama hesabındaki belirtilen kapsayıcıdan geri yükle |
> | Microsoft. Apimanayönetimi/hizmet/updatecertificate/Action | API Management hizmeti için SSL sertifikası yükleme |
> | Microsoft. Apimanaya/Service/updatehostname/Action | API Management hizmeti için özel etki alanı adlarını kurma, güncelleştirme veya kaldırma |
> | Microsoft. Apimanayönetimi/hizmeti/yazma | API Management hizmeti 'nin yeni bir örneğini oluşturma |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/anahtarları/okuma | Kullanıcıyla ilişkili anahtarları al |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="api-management-service-reader-role"></a>API Management hizmeti okuyucu rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Hizmet ve API 'lere salt okuma erişimi |
> | **Kimlik** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Eylemler** |  |
> | Microsoft. Apimanayönetimi/hizmeti/*/Read | API Management hizmeti örneklerini oku |
> | Microsoft. Apimanayönetimi/hizmeti/okuma | API Management hizmet örneği için meta verileri oku |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. Apimanayönetimi/hizmeti/kullanıcıları/anahtarları/okuma | Kullanıcıyla ilişkili anahtarları al |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="application-insights-component-contributor"></a>Application Insights bileşeni Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Application Insights bileşenlerini yönetebilir |
> | **Kimlik** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/bileşenler/* | Öngörüler bileşenleri oluşturma ve yönetme |
> | Microsoft. Insights/webtests/* | Web testleri oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Application Insights Snapshot Debugger ile toplanan hata ayıklama anlık görüntülerini görüntülemek ve indirmek için kullanıcıya izin verir. Bu izinlerin [sahip](#owner) veya [katkıda](#contributor) bulunan rollerine dahil edilmediğini unutmayın. |
> | **Kimlik** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/bileşenler/*/Read |  |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="automation-job-operator"></a>Automation Iş Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Otomasyon Runbook 'Larını kullanarak Iş oluşturun ve yönetin. |
> | **Kimlik** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Karma runbook çalışanı kaynaklarını okur |
> | Microsoft. Automation/automationAccounts/Jobs/okuma | Bir Azure Otomasyonu işini alır |
> | Microsoft. Automation/automationAccounts/Jobs/sürdürülecek/Action | Bir Azure Otomasyonu işini sürdürür |
> | Microsoft. Automation/automationAccounts/Jobs/durdur/eylem | Bir Azure Otomasyonu işini durduruyor |
> | Microsoft. Automation/automationAccounts/Jobs/akışlar/okuma | Bir Azure Otomasyonu iş akışı alır |
> | Microsoft. Automation/automationAccounts/Jobs/beklet/eylem | Bir Azure Otomasyonu işini askıya alır |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Azure Otomasyonu işi oluşturur |
> | Microsoft. Automation/automationAccounts/Jobs/output/Read | Bir işin çıkışını alır |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="automation-operator"></a>Otomasyon Operatörü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Automation Işleçleri, işleri başlatabilir, durdurabilir, askıya alabilir ve sürdürebilir |
> | **Kimlik** | d3881f73-407A-4167-8283-e981cbba0404 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Karma runbook çalışanı kaynaklarını okur |
> | Microsoft. Automation/automationAccounts/Jobs/okuma | Bir Azure Otomasyonu işini alır |
> | Microsoft. Automation/automationAccounts/Jobs/sürdürülecek/Action | Bir Azure Otomasyonu işini sürdürür |
> | Microsoft. Automation/automationAccounts/Jobs/durdur/eylem | Bir Azure Otomasyonu işini durduruyor |
> | Microsoft. Automation/automationAccounts/Jobs/akışlar/okuma | Bir Azure Otomasyonu iş akışı alır |
> | Microsoft. Automation/automationAccounts/Jobs/beklet/eylem | Bir Azure Otomasyonu işini askıya alır |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Azure Otomasyonu işi oluşturur |
> | Microsoft. Automation/automationAccounts/Jobzamanlamalar/okuma | Bir Azure Otomasyonu iş zamanlaması alır |
> | Microsoft. Automation/automationAccounts/Jobzamanlamalar/yazma | Bir Azure Otomasyonu iş zamanlaması oluşturur |
> | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Otomasyon hesabına bağlı çalışma alanını alır |
> | Microsoft. Automation/automationAccounts/Read | Bir Azure Otomasyonu hesabını alır |
> | Microsoft. Automation/automationAccounts/runbook 'lar/okuma | Bir Azure Otomasyonu runbook 'unu alır |
> | Microsoft. Automation/automationAccounts/zamanlamalar/okuma | Bir Azure Otomasyonu zamanlama varlığını alır |
> | Microsoft. Automation/automationAccounts/zamanlamalar/yazma | Bir Azure Otomasyonu zamanlama varlığı oluşturur veya güncelleştirir |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. Automation/automationAccounts/Jobs/output/Read | Bir işin çıkışını alır |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="automation-runbook-operator"></a>Otomasyon Runbook Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Runbook 'un Işlerini oluşturabilmek için Runbook özelliklerini okuyun. |
> | **Kimlik** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Automation/automationAccounts/runbook 'lar/okuma | Bir Azure Otomasyonu runbook 'unu alır |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="avere-contributor"></a>Avere Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | , Bir avere vFXT kümesi oluşturabilir ve yönetebilir. |
> | **Kimlik** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. COMPUTE/*/Read |  |
> | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/* |  |
> | Microsoft. COMPUTE/virtualMachines/* |  |
> | Microsoft. COMPUTE/Disks/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft. Network/NetworkInterfaces/* |  |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. Network/virtualNetworks/alt ağlar/okuma | Bir sanal ağ alt ağ tanımını alır |
> | Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | Microsoft. Network/virtualNetworks/alt ağlar/Jodavetli Aserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | Microsoft. Network/networkSecurityGroups/JOIN/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/*/Read |  |
> | Microsoft. Storage/storageAccounts/* |  |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/kaynaklar/okuma | Kaynak grubunun kaynaklarını alır. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/Sil | Bir blobu silmenin sonucunu döndürür |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/okuma | Blob veya Blobların listesini döndürür |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/yazma | Blob yazma sonucunu döndürür |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="avere-operator"></a>Avere Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kümeyi yönetmek için avere vFXT kümesi tarafından kullanılır |
> | **Kimlik** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Eylemler** |  |
> | Microsoft. COMPUTE/virtualMachines/okuma | Bir sanal makinenin özelliklerini al |
> | Microsoft. Network/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | Microsoft. Network/NetworkInterfaces/Write | Ağ arabirimi oluşturur veya var olan bir ağ arabirimini güncelleştirir.  |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. Network/virtualNetworks/alt ağlar/okuma | Bir sanal ağ alt ağ tanımını alır |
> | Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | Microsoft. Network/networkSecurityGroups/JOIN/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Delete | Bir kapsayıcıyı silmenin sonucunu döndürür |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/okuma | Kapsayıcıların listesini döndürür |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Write | Put blob kapsayıcısının sonucunu döndürür |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/Sil | Bir blobu silmenin sonucunu döndürür |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/okuma | Blob veya Blobların listesini döndürür |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/yazma | Blob yazma sonucunu döndürür |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs veri sahibi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Event Hubs kaynaklarına tam erişim sağlar. |
> | **Kimlik** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Eylemler** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs veri alıcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Event Hubs kaynaklarına erişim izni verir. |
> | **Kimlik** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Eylemler** |  |
> | Microsoft. EventHub/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. EventHub/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs veri gönderici
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Event Hubs kaynaklarına erişim gönderilmesine izin verir. |
> | **Kimlik** | 2b629674-e913-4C01-ae53-ef4638d8f975 |
> | **Eylemler** |  |
> | Microsoft. EventHub/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes hizmet kümesi yönetici rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Küme Yöneticisi kimlik bilgisi eylemini listeleyin. |
> | **Kimlik** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Eylemler** |  |
> | Microsoft. ContainerService/Managedkümeler/listClusterAdminCredential/ACTION | Yönetilen kümenin clusterAdmin kimlik bilgisini listeleyin |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes hizmet kümesi Kullanıcı rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Küme kullanıcı kimlik bilgilerini Listele eylemi. |
> | **Kimlik** | 4abbcc35-E782-43d8-92C5-2d3f1bd2253f |
> | **Eylemler** |  |
> | Microsoft. ContainerService/Managedkümeler/listClusterUserCredential/Action | Yönetilen kümenin clusterUser kimlik bilgisini listeleyin |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure haritalar veri okuyucu (Önizleme)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure haritalar hesabından ilgili harita okuma verilerine erişim izni verir. |
> | **Kimlik** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Maps/hesaplar/veri/okuma | Haritalar hesabına veri okuma erişimi verir. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-sentinel-contributor"></a>Azure Sentinel Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Sentinel Katılımcısı |
> | **Kimlik** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Eylemler** |  |
> | Microsoft. Securityınsights/* |  |
> | Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/eylem | Yeni altyapıyı kullanarak arama yapın. |
> | Microsoft. Operationalınsights/çalışma alanları/okuma | Mevcut bir çalışma alanını alır |
> | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/* |  |
> | Microsoft. OperationsManagement/Solutions/Read | OMS çözümünü çıkmadan al |
> | Microsoft. Operationalınsights/çalışma alanları/sorgu/okuma | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/okuma | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | Microsoft. Insights/çalışma kitapları/* |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-sentinel-reader"></a>Azure Sentinel okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Sentinel okuyucusu |
> | **Kimlik** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Eylemler** |  |
> | Microsoft. Securityınsights/*/Read |  |
> | Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/eylem | Yeni altyapıyı kullanarak arama yapın. |
> | Microsoft. Operationalınsights/çalışma alanları/okuma | Mevcut bir çalışma alanını alır |
> | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/okuma | Kayıtlı bir arama sorgusu alır |
> | Microsoft. OperationsManagement/Solutions/Read | OMS çözümünü çıkmadan al |
> | Microsoft. Operationalınsights/çalışma alanları/sorgu/okuma | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/okuma | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | Microsoft. Insights/çalışma kitapları/okuma | Çalışma kitabını okuma |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-sentinel-responder"></a>Azure Sentinel Yanıtlayıcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Sentinel Yanıtlayıcısı |
> | **Kimlik** | 3e150937-b8fe-4CFB-8069-0eaf05ecd056 |
> | **Eylemler** |  |
> | Microsoft. Securityınsights/*/Read |  |
> | Microsoft. Securityınsights/Cases/* |  |
> | Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/eylem | Yeni altyapıyı kullanarak arama yapın. |
> | Microsoft. Operationalınsights/çalışma alanları/okuma | Mevcut bir çalışma alanını alır |
> | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/okuma | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/okuma | Kayıtlı bir arama sorgusu alır |
> | Microsoft. OperationsManagement/Solutions/Read | OMS çözümünü çıkmadan al |
> | Microsoft. Operationalınsights/çalışma alanları/sorgu/okuma | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | Microsoft. Operationalınsights/çalışma alanları/veri kaynakları/okuma | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | Microsoft. Insights/çalışma kitapları/okuma | Çalışma kitabını okuma |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-service-bus-data-owner"></a>Azure Service Bus veri sahibi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Service Bus kaynaklara tam erişim sağlar. |
> | **Kimlik** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Eylemler** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-service-bus-data-receiver"></a>Azure Service Bus veri alıcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Service Bus kaynaklarına erişim izni verir. |
> | **Kimlik** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Eylemler** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-service-bus-data-sender"></a>Veri Göndericisini Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Service Bus kaynaklarına erişim izni verir. |
> | **Kimlik** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Eylemler** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="azure-stack-registration-owner"></a>Kayıt sahibini Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Stack kayıtlarını yönetmenizi sağlar. |
> | **Kimlik** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Eylemler** |  |
> | Microsoft. AzureStack/kayıtlar/ürünler/*/Action |  |
> | Microsoft. AzureStack/kayıtlar/ürünler/okuma | Azure Stack Market ürününün özelliklerini alır |
> | Microsoft. AzureStack/kayıtlar/okuma | Azure Stack kaydın özelliklerini alır |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="backup-contributor"></a>Yedek Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yedekleme hizmetini yönetmenizi sağlar, ancak kasaların oluşturamaz ve başkalarına erişim izni verebilir |
> | **Kimlik** | 5e467623-bb1f-42F4-a55d-6e525e11384b |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. RecoveryServices/konumlar/* |  |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/operationResults/* | Yedekleme yönetiminde işlemin sonuçlarını yönetme |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/* | Kurtarma Hizmetleri kasasının yedekleme dokuları içinde yedekleme kapsayıcıları oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/refreshContainers/eylem | Kapsayıcı listesini yeniler |
> | Microsoft. RecoveryServices/Vaults/backupJobs/* | Yedekleme işleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupJobsExport/eylem | Işleri dışarı aktar |
> | Microsoft. RecoveryServices/Vaults/backupManagementMetaData/* | Yedekleme yönetimiyle ilgili meta verileri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupOperationResults/* | Yedekleme yönetimi işlemlerinin sonuçlarını oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/* | Yedekleme ilkeleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/* | Yedeklenebilir öğeleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/* | Yedeklenen öğeleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupProtectionContainers/* | Yedekleme öğelerini tutan kapsayıcılar oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft. RecoveryServices/Vaults/backupUsageSummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | Microsoft. RecoveryServices/Kasaults/sertifikalar/* | Kurtarma Hizmetleri kasasında yedekleme ile ilgili sertifikalar oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/* | Kasa ile ilgili genişletilmiş bilgileri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Kasaults/monitoringAlerts/okuma | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/* | Kayıtlı kimlikler oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/* | Kurtarma Hizmetleri kasasının kullanımını oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Microsoft. RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft. RecoveryServices/Vaults/backupValidateOperation/Action | Korumalı öğe üzerinde Işlemi doğrula |
> | Microsoft. RecoveryServices/Vaults/Write | Kasa oluşturma işlemi, ' kasa ' türünde bir Azure kaynağı oluşturur |
> | Microsoft. RecoveryServices/Vaults/backupOperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupEngines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/Backupprotectionamaç/* |  |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/korunabilir | Tüm korunabilir kapsayıcıları al |
> | Microsoft. RecoveryServices/konumlar/backupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Özellikleri doğrulama |
> | Microsoft. RecoveryServices/Vaults/monitoringAlerts/Write | Uyarıyı çözer. |
> | Microsoft. RecoveryServices/işlemler/okuma | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | Microsoft. RecoveryServices/konumlar/operationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | Microsoft. RecoveryServices/Vaults/Backupprotectionhedefleri/okuma | Tüm yedekleme koruma amaçlarını Listele |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="backup-operator"></a>Yedekleme Işletmeni
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yedekleme kaldırma, kasa oluşturma ve başkalarına erişim verme dışında yedekleme hizmetlerini yönetmenizi sağlar |
> | **Kimlik** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/operationResults/Read | İşlemin durumunu döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/operationResults/Read | Koruma kapsayıcısında gerçekleştirilen Işlemin sonucunu alır. |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/korunabilir/yedekleme/eylem | Korumalı öğe için yedekleme gerçekleştirir. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/operationResults/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin sonucunu alır. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/operationsStatus/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin durumunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/provisionInstantItemRecovery/eylem | Korumalı öğe için anında öğe kurtarma sağla |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/kurtarma noktaları/okuma | Korumalı öğeler için kurtarma noktalarını alın. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/kurtarma noktaları/geri yükleme/eylem | Korumalı öğeler için kurtarma noktalarını geri yükleyin. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/revokeInstantItemRecovery/eylem | Korumalı öğe için anında öğe kurtarmayı iptal et |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/yazma | Yedekleme korumalı öğesi oluştur |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/okuma | Tüm kayıtlı kapsayıcıları döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/refreshContainers/eylem | Kapsayıcı listesini yeniler |
> | Microsoft. RecoveryServices/Vaults/backupJobs/* | Yedekleme işleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupJobsExport/eylem | Işleri dışarı aktar |
> | Microsoft. RecoveryServices/Vaults/backupManagementMetaData/okuma |  |
> | Microsoft. RecoveryServices/Vaults/backupOperationResults/* | Yedekleme yönetimi işlemlerinin sonuçlarını oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/operationResults/Read | Ilke Işleminin sonuçlarını alın. |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/Read | Tüm koruma Ilkelerini döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/* | Yedeklenebilir öğeleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/Read | Tüm korumalı öğelerin listesini döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupProtectionContainers/Read | Aboneliğe ait tüm kapsayıcıları döndürür |
> | Microsoft. RecoveryServices/Vaults/backupUsageSummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | Microsoft. RecoveryServices/Vaults/sertifikalar/yazma | Kaynak sertifikası güncelleştirme işlemi kaynak/kasa kimlik bilgisi sertifikasını güncelleştirir. |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Write | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Microsoft. RecoveryServices/Kasaults/monitoringAlerts/okuma | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/operationResults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/Write | Hizmet kapsayıcısını Kaydet işlemi, bir kapsayıcıyı kurtarma hizmeti 'ne kaydetmek için kullanılabilir. |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Microsoft. RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/Vaults/backupValidateOperation/Action | Korumalı öğe üzerinde Işlemi doğrula |
> | Microsoft. RecoveryServices/Vaults/backupOperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/işlemler/okuma | Ilke Işleminin durumunu alın. |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/Write | Kayıtlı bir kapsayıcı oluşturur |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/sorgu/eylem | Bir kapsayıcı içindeki iş yükleri için sorgulama yap |
> | Microsoft. RecoveryServices/Vaults/backupEngines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/Backupprotectionamacını/Write | Yedekleme koruma hedefi oluşturma |
> | Microsoft. RecoveryServices/Vaults/Backupdokuların/Backupprotectionamacını/Read | Yedekleme koruması hedefi al |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/korunabilir | Tüm korunabilir kapsayıcıları al |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/öğeler/okuma | Bir kapsayıcıdaki tüm öğeleri Al |
> | Microsoft. RecoveryServices/konumlar/backupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Özellikleri doğrulama |
> | Microsoft. RecoveryServices/Vaults/monitoringAlerts/Write | Uyarıyı çözer. |
> | Microsoft. RecoveryServices/işlemler/okuma | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | Microsoft. RecoveryServices/konumlar/operationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | Microsoft. RecoveryServices/Vaults/Backupprotectionhedefleri/okuma | Tüm yedekleme koruma amaçlarını Listele |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="backup-reader"></a>Yedekleme okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yedekleme hizmetlerini görüntüleyebilir, ancak değişiklik yapamaz |
> | **Kimlik** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. RecoveryServices/konumlar/allocatedStamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/operationResults/Read | İşlemin durumunu döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/operationResults/Read | Koruma kapsayıcısında gerçekleştirilen Işlemin sonucunu alır. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/operationResults/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin sonucunu alır. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/operationsStatus/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin durumunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/kurtarma noktaları/okuma | Korumalı öğeler için kurtarma noktalarını alın. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/okuma | Tüm kayıtlı kapsayıcıları döndürür |
> | Microsoft. RecoveryServices/Vaults/backupJobs/operationResults/Read | Iş Işleminin sonucunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupJobs/Read | Tüm Iş nesnelerini döndürür |
> | Microsoft. RecoveryServices/Vaults/backupJobsExport/eylem | Işleri dışarı aktar |
> | Microsoft. RecoveryServices/Vaults/backupManagementMetaData/okuma |  |
> | Microsoft. RecoveryServices/Vaults/backupOperationResults/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi sonucunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/operationResults/Read | Ilke Işleminin sonuçlarını alın. |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/Read | Tüm koruma Ilkelerini döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupkorunabilir/Read | Tüm korumalı öğelerin listesini döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupProtectionContainers/Read | Aboneliğe ait tüm kapsayıcıları döndürür |
> | Microsoft. RecoveryServices/Vaults/backupUsageSummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Microsoft. RecoveryServices/Kasaults/monitoringAlerts/okuma | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/operationResults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | Microsoft. RecoveryServices/Vaults/backupstorageconfig/Read | Kurtarma Hizmetleri Kasası için depolama yapılandırmasını döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupconfig/Read | Kurtarma Hizmetleri Kasası için yapılandırmayı döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupOperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/işlemler/okuma | Ilke Işleminin durumunu alın. |
> | Microsoft. RecoveryServices/Vaults/backupEngines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | Microsoft. RecoveryServices/Vaults/Backupdokuların/Backupprotectionamacını/Read | Yedekleme koruması hedefi al |
> | Microsoft. RecoveryServices/Vaults/Backupyapılar/protectionContainers/öğeler/okuma | Bir kapsayıcıdaki tüm öğeleri Al |
> | Microsoft. RecoveryServices/konumlar/backupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/Vaults/monitoringAlerts/Write | Uyarıyı çözer. |
> | Microsoft. RecoveryServices/işlemler/okuma | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | Microsoft. RecoveryServices/konumlar/operationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | Microsoft. RecoveryServices/Vaults/Backupprotectionhedefleri/okuma | Tüm yedekleme koruma amaçlarını Listele |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="billing-reader"></a>Faturalama Okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Faturalandırma verilerine okuma erişimi sağlar |
> | **Kimlik** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. faturalandırma/*/Read | Fatura bilgilerini okuyun |
> | Microsoft. Commerce/*/Read |  |
> | Microsoft. tüketim/*/Read |  |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="biztalk-contributor"></a>BizTalk Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | BizTalk hizmetlerini yönetmenizi sağlar ancak onlara erişim izni vermez. |
> | **Kimlik** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. BizTalkServices/BizTalk/* | BizTalk Hizmetleri oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="blockchain-member-node-access-preview"></a>Blok zinciri üye düğümü erişimi (Önizleme)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Blok zinciri üye düğümlerine erişim sağlar |
> | **Kimlik** | 31a002a1-acaf-453E-8a5b-297c9ca1ea24 |
> | **Eylemler** |  |
> | Microsoft. Blockzincirini/blockchainMembers/transactionNodes/Read | Varolan blok zinciri üye Işlem düğümlerini alır veya listeler. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Blockzincirini/blockchainMembers/transactionNodes/Connect/ACTION | Bir blok zinciri üye Işlem düğümüne bağlanır. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="blueprint-contributor"></a>Blueprint Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Şema tanımlarını yönetebilir, ancak atamazsınız. |
> | **Kimlik** | 41077137-e803-4205-871C-5a86e6a753b4 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Blueprint/planlar/* | Şema tanımlarını veya şema yapılarını oluşturun ve yönetin. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="blueprint-operator"></a>Blueprint Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Varolan yayımlanmış şemaları atayabilir, ancak yeni şemaları oluşturamaz. Not: Bu yalnızca atama Kullanıcı tarafından atanan yönetilen bir kimlikle yapıldığında geçerlidir. |
> | **Kimlik** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Blueprint/Blueprintasbir/* | Şema atamaları oluşturun ve yönetin. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cdn-endpoint-contributor"></a>CDN uç noktası Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | CDN uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. |
> | **Kimlik** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. CDN/edgenodes/okuma |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profiller/uç noktaları/* |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cdn-endpoint-reader"></a>CDN uç nokta okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | CDN uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz. |
> | **Kimlik** | 871e35f6-b5c1-49cc-A043-bde969a0f2cd |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. CDN/edgenodes/okuma |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/uç noktaları/*/Read |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cdn-profile-contributor"></a>CDN profili Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | CDN profillerini ve uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. |
> | **Kimlik** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. CDN/edgenodes/okuma |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/* |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cdn-profile-reader"></a>CDN profili okuyucu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | CDN profillerini ve uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz. |
> | **Kimlik** | 8f96442b-4075-438f-813D-ad51ab4019af |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. CDN/edgenodes/okuma |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profiller/*/Read |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="classic-network-contributor"></a>Klasik ağ katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Klasik ağları yönetmenize izin verir, ancak bunlara erişemez. |
> | **Kimlik** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. ClassicNetwork/* | Klasik ağları oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="classic-storage-account-contributor"></a>Klasik depolama hesabı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Klasik depolama hesaplarını yönetmenize izin verir, ancak bunlara erişimi kalmaz. |
> | **Kimlik** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. ClassicStorage/storageAccounts/* | Depolama hesapları oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Klasik depolama hesabı anahtar operatörü hizmet rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Klasik depolama hesabı anahtar Işleçleri klasik depolama hesaplarında anahtarları listeleme ve yeniden oluşturma izni verilir |
> | **Kimlik** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Eylemler** |  |
> | Microsoft. ClassicStorage/storageAccounts/ListKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | Microsoft. ClassicStorage/storageAccounts/RegenerateKey/Action | Depolama hesabı için mevcut erişim anahtarlarını yeniden oluşturur. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="classic-virtual-machine-contributor"></a>Klasik sanal makine Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Klasik sanal makineleri yönetmenizi sağlar ancak bunlara yönelik erişimi, bağlı oldukları sanal ağ veya depolama hesabı için değil. |
> | **Kimlik** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. ClassicCompute/domainNames/* | Klasik işlem etki alanı adları oluşturma ve yönetme |
> | Microsoft. ClassicCompute/virtualMachines/* | Sanal makineler oluşturma ve yönetme |
> | Microsoft. ClassicNetwork/networkSecurityGroups/JOIN/Action |  |
> | Microsoft. ClassicNetwork/Rezervedips/bağlantı/eylem | Ayrılmış bir IP 'yi bağlama |
> | Microsoft. ClassicNetwork/Rezervedips/Read | Ayrılmış IP 'Leri alır |
> | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action | Sanal ağı birleştirir. |
> | Microsoft. ClassicNetwork/virtualNetworks/Read | Sanal ağı alın. |
> | Microsoft. ClassicStorage/storageAccounts/Disks/Read | Depolama hesabı diskini döndürür. |
> | Microsoft. ClassicStorage/storageAccounts/Images/Read | Depolama hesabı görüntüsünü döndürür. Kullanım dışı. ' Microsoft. ClassicStorage/storageAccounts/Vmımages ' kullanın) |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | Microsoft. ClassicStorage/storageAccounts/Read | Verilen hesaba sahip depolama hesabını döndürün. |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cognitive-services-contributor"></a>Bilişsel hizmetler Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Bilişsel hizmetler için anahtar oluşturma, okuma, güncelleştirme, silme ve yönetme olanağı sağlar. |
> | **Kimlik** | 25fbc0a9-bd7c-42A3-aa1a-3b75d497ee68 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Biliveservices/* |  |
> | Microsoft. Features/Features/Read | Bir aboneliğin özelliklerini alır. |
> | Microsoft. Özellikler/sağlayıcılar/Özellikler/okuma | Belirli bir kaynak sağlayıcısındaki bir aboneliğin özelliğini alır. |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/diagnosticSettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | Microsoft. Insights/logDefinitions/okuma | Günlük tanımlarını oku |
> | Microsoft. Insights/MetricDefinitions/okuma | Ölçüm tanımlarını oku |
> | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Microsoft. resources/abonelikler/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | Microsoft. resources/abonelikler/okuma | Aboneliklerin listesini alır. |
> | Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/* |  |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cognitive-services-data-reader-preview"></a>Bilişsel hizmetler veri okuyucu (Önizleme)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Bilişsel hizmetler verilerini okumanızı sağlar. |
> | **Kimlik** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Biliveservices/*/Read |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cognitive-services-user"></a>Bilişsel hizmetler kullanıcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Bilişsel hizmetler 'in anahtarlarını okuyup listelemenizi sağlar. |
> | **Kimlik** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Eylemler** |  |
> | Microsoft. Biliveservices/*/Read |  |
> | Microsoft. Biliveservices/accounts/ListKeys/Action | Anahtarları Listele |
> | Microsoft. Insights/alertRules/Read | Klasik ölçüm uyarısını okuyun |
> | Microsoft. Insights/diagnosticSettings/Read | Kaynak tanılama ayarını oku |
> | Microsoft. Insights/logDefinitions/okuma | Günlük tanımlarını oku |
> | Microsoft. Insights/MetricDefinitions/okuma | Ölçüm tanımlarını oku |
> | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Microsoft. resources/abonelikler/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | Microsoft. resources/abonelikler/okuma | Aboneliklerin listesini alır. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Biliveservices/* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB hesabı okuyucu rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Cosmos DB hesabı verilerini okuyabilir. Azure Cosmos DB hesaplarını yönetmek için [DocumentDB hesabı katılımcısı](#documentdb-account-contributor) konusuna bakın. |
> | **Kimlik** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını okuyun, her kullanıcıya verilen izinleri okuyabilir |
> | Microsoft. DocumentDB/*/Read | Tüm koleksiyonları okuyun |
> | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Veritabanı hesabı salt okunur anahtarlarını okur. |
> | Microsoft. Insights/MetricDefinitions/okuma | Ölçüm tanımlarını oku |
> | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Cosmos DB hesaplarını yönetmenizi sağlar ancak içerdikleri verilere erişemez. Hesap anahtarlarına ve bağlantı dizelerine erişimi engeller. |
> | **Kimlik** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Eylemler** |  |
> | Microsoft. DocumentDb/databaseAccounts/* |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Bir Cosmos DB veritabanı veya bir hesabın kapsayıcısı için geri yükleme isteği gönderebilir |
> | **Kimlik** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Eylemler** |  |
> | Microsoft. DocumentDB/databaseAccounts/Backup/Action | Yedeklemeyi yapılandırmak için bir istek gönder |
> | Microsoft. DocumentDB/databaseAccounts/restore/Action | Geri yükleme isteği gönder |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cost-management-contributor"></a>Maliyet yönetimi Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir (örn. bütçeler, dışarı aktarmalar) |
> | **Kimlik** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Eylemler** |  |
> | Microsoft. tüketim/* |  |
> | Microsoft. CostManagement/* |  |
> | Microsoft. faturalandırma/Billingdönemler/okuma |  |
> | Microsoft. resources/abonelikler/okuma | Aboneliklerin listesini alır. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. danışman/yapılandırma/okuma | Yapılandırma al |
> | Microsoft. Advisor/öneriler/okuma | Önerileri okur |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="cost-management-reader"></a>Maliyet yönetimi okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Maliyet verilerini ve yapılandırmayı görüntüleyebilir (örneğin, bütçeler, dışarı aktarmalar) |
> | **Kimlik** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Eylemler** |  |
> | Microsoft. tüketim/*/Read |  |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. faturalandırma/Billingdönemler/okuma |  |
> | Microsoft. resources/abonelikler/okuma | Aboneliklerin listesini alır. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. danışman/yapılandırma/okuma | Yapılandırma al |
> | Microsoft. Advisor/öneriler/okuma | Önerileri okur |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="data-box-contributor"></a>Katkıda bulunan Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | , Diğer kullanıcılara erişim izni hariç Data Box hizmeti altındaki her şeyi yönetmenizi sağlar. |
> | **Kimlik** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. databox/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="data-box-reader"></a>Data Box okuyucu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Sipariş oluşturma veya düzenleme sırası ayrıntıları ve başkalarına erişim verme dışında Data Box hizmetini yönetmenizi sağlar. |
> | **Kimlik** | 028f4ed7-e2a9-465E-a8f4-9c0ffdfdc027 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. databox/*/Read |  |
> | Microsoft. databox/Jobs/listgizlilikler/Action |  |
> | Microsoft. databox/Jobs/listcredentials/Action | Siparişle ilgili şifrelenmemiş kimlik bilgilerini listeler. |
> | Microsoft. databox/Locations/Availablesku 'Lar/eylem | Bu yöntem, kullanılabilir SKU 'ların listesini döndürür. |
> | Microsoft. databox/Locations/validateAddress/Action | Sevkiyat adresini doğrular ve varsa alternatif adresler sağlar. |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="data-factory-contributor"></a>Katkıda bulunan Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Veri fabrikalarının yanı sıra bunların içindeki alt kaynakları oluşturun ve yönetin. |
> | **Kimlik** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. DataFactory/DataFactory/* | Veri fabrikaları ve bunların içinde alt kaynaklar oluşturun ve yönetin. |
> | Microsoft. DataFactory/Factory/* | Veri fabrikaları ve bunların içinde alt kaynaklar oluşturun ve yönetin. |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics geliştirici
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kendi işlerinizi göndermenize, izlemenize ve yönetmenize izin verir, ancak Data Lake Analytics Hesapları oluşturamaz veya silemezsiniz. |
> | **Kimlik** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. BigAnalytics/accounts/* |  |
> | Microsoft. DataLakeAnalytics/accounts/* |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/hesaplar/Sil |  |
> | Microsoft. BigAnalytics/hesaplar/Takesahiplik/eylem |  |
> | Microsoft. BigAnalytics/hesaplar/yazma |  |
> | Microsoft. DataLakeAnalytics/hesaplar/Sil | Bir DataLakeAnalytics hesabını silin. |
> | Microsoft. DataLakeAnalytics/hesaplar/Takesahiplik/eylem | Diğer kullanıcılar tarafından gönderilen işleri iptal etmek için izin verin. |
> | Microsoft. DataLakeAnalytics/hesaplar/yazma | Bir DataLakeAnalytics hesabı oluşturun veya güncelleştirin. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Bir DataLakeAnalytics hesabının bağlı DataLakeStore hesabını oluşturun veya güncelleştirin. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Bir DataLakeStore hesabının bir DataLakeAnalytics hesabıyla bağlantısını kaldırın. |
> | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/Write | Bir DataLakeAnalytics hesabı için bağlı bir depolama hesabı oluşturun veya güncelleştirin. |
> | Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/Delete | Bir depolama hesabının bir DataLakeAnalytics hesabından bağlantısını kaldırın. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Bir güvenlik duvarı kuralı oluşturun veya güncelleştirin. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Bir güvenlik duvarı kuralını silin. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Bir işlem ilkesi oluşturun veya güncelleştirin. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | İşlem ilkesini silin. |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="data-purger"></a>Veri Takiger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Analiz verilerini temizedebilir |
> | **Kimlik** | 150f5e0c-0603-4f03-8C7F-cf70034c4e90 |
> | **Eylemler** |  |
> | Microsoft. Insights/bileşenler/*/Read |  |
> | Microsoft. Insights/bileşenler/Temizleme/eylem | Application Insights verileri temizleme |
> | Microsoft. Operationalınsights/çalışma alanları/*/Read |  |
> | Microsoft. Operationalınsights/çalışma alanları/temizleme/eylem | Belirtilen verileri çalışma alanından Sil |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="devtest-labs-user"></a>DevTest Labs kullanıcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure DevTest Labs sanal makinelerinizi bağlamanıza, başlatmanıza, yeniden başlatmanıza ve kapatımanıza olanak sağlar. |
> | **Kimlik** | 76283e04-6283-4c54-8F91-bcf1374a3c64 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/Read | Bir kullanılabilirlik kümesinin özelliklerini al |
> | Microsoft. COMPUTE/virtualMachines/*/Read | Bir sanal makinenin özelliklerini okuyun (VM boyutları, çalışma zamanı durumu, VM uzantıları vb.) |
> | Microsoft. COMPUTE/virtualMachines/serbest bırakma/eylem | Sanal makineyi güçlendirir ve işlem kaynaklarını serbest bırakır |
> | Microsoft. COMPUTE/virtualMachines/okuma | Bir sanal makinenin özelliklerini al |
> | Microsoft. COMPUTE/virtualMachines/yeniden Başlat/eylem | Sanal makineyi yeniden başlatır |
> | Microsoft. COMPUTE/virtualMachines/Başlat/eylem | Sanal makineyi başlatır |
> | Microsoft. DevTestLab/*/Read | Laboratuvarın özelliklerini okuyun |
> | Microsoft. DevTestLab/Labs/claimAnyVm/eylem | Laboratuvarda rastgele bir çakışan sanal makine talep edin. |
> | Microsoft. DevTestLab/Labs/createEnvironment/eylem | Laboratuvarda sanal makineler oluşturun. |
> | Microsoft. DevTestLab/Labs/ensureCurrentUserProfile/Action | Geçerli kullanıcının laboratuvarda geçerli bir profile sahip olduğundan emin olun. |
> | Microsoft. DevTestLab/Labs/formüller/Sil | Formülleri silin. |
> | Microsoft. DevTestLab/Labs/formüller/okuma | Formülleri okuyun. |
> | Microsoft. DevTestLab/Labs/formüller/yazma | Formüller ekleme veya değiştirme. |
> | Microsoft. DevTestLab/Labs/policySets/evaluatePolicies/Action | Laboratuvar ilkesini değerlendirir. |
> | Microsoft. DevTestLab/Labs/virtualMachines/talep/eylem | Mevcut bir sanal makinenin sahipliğini al |
> | Microsoft. DevTestLab/Labs/virtualmachines/Listapperepblezamanlamalar/eylem | Varsa, geçerli başlatma/durdurma zamanlamalarını listeler. |
> | Microsoft. DevTestLab/Labs/virtualMachines/getRdpFileContents/Action | Sanal makine için RDP dosyasının içeriğini temsil eden bir dize alır |
> | Microsoft. Network/loadBalancers/Backendadddresspoir/JOIN/Action | Yük dengeleyici arka uç adres havuzunu birleştirir. Alertable değil. |
> | Microsoft. Network/loadBalancers/ınboundnatrules/JOIN/Action | Yük dengeleyici gelen NAT kuralına katılır. Alertable değil. |
> | Microsoft. Network/NetworkInterfaces/*/Read | Bir ağ arabiriminin özelliklerini okuyun (örneğin, ağ arabiriminin bir parçası olduğu tüm yük dengeleyiciler) |
> | Microsoft. Network/NetworkInterfaces/JOIN/Action | Bir sanal makineyi bir ağ arabirimine birleştirir. Alertable değil. |
> | Microsoft. Network/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | Microsoft. Network/NetworkInterfaces/Write | Ağ arabirimi oluşturur veya var olan bir ağ arabirimini güncelleştirir.  |
> | Microsoft. Network/publicIPAddresses/*/Read | Genel IP adresinin özelliklerini okuyun |
> | Microsoft. Network/publicIPAddresses/JOIN/Action | Genel bir IP adresini birleştirir. Alertable değil. |
> | Microsoft. Network/publicIPAddresses/Read | Genel IP adresi tanımını alır. |
> | Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | Microsoft. resources/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Microsoft. resources/dağıtımlar/okuma | Dağıtımları alır veya listeler. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | **NotActions** |  |
> | Microsoft. COMPUTE/virtualMachines/vmSizes/Read | Sanal makinenin güncelleştirileceği kullanılabilir boyutları listeler |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="dns-zone-contributor"></a>DNS bölgesi Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure DNS, DNS bölgelerini ve kayıt kümelerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. |
> | **Kimlik** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/dnsZones/* | DNS bölgeleri ve kayıtları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="documentdb-account-contributor"></a>DocumentDB hesabı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | , Azure Cosmos DB hesaplarını yönetebilir. Azure Cosmos DB daha önce DocumentDB olarak bilinirdi. |
> | **Kimlik** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. DocumentDb/databaseAccounts/* | Azure Cosmos DB hesapları oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | EventGrid olay aboneliği işlemlerini yönetmenizi sağlar. |
> | **Kimlik** | 428e0ff0-5e57-4d9c-A221-2c70d0e0a443 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. EventGrid/Eventabonelikleriniz/* |  |
> | Microsoft. EventGrid/topicTypes/Eventaboneliklerde/okunan | Küresel olay aboneliklerini konu türüne göre Listele |
> | Microsoft. EventGrid/Locations/Eventaboneliklerde/okunan | Bölgesel olay aboneliklerini listeleme |
> | Microsoft. EventGrid/Locations/topicTypes/Eventabonelikleri/okuma | TopicType 'a göre bölgesel olay aboneliklerini listeleyin |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | EventGrid olay aboneliklerini okumanızı sağlar. |
> | **Kimlik** | 2414bbcf-6497-4FAF-8c65-045460748405 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. EventGrid/Eventaboneliklerde/okunan | Bir eventSubscription okuma |
> | Microsoft. EventGrid/topicTypes/Eventaboneliklerde/okunan | Küresel olay aboneliklerini konu türüne göre Listele |
> | Microsoft. EventGrid/Locations/Eventaboneliklerde/okunan | Bölgesel olay aboneliklerini listeleme |
> | Microsoft. EventGrid/Locations/topicTypes/Eventabonelikleri/okuma | TopicType 'a göre bölgesel olay aboneliklerini listeleyin |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight küme operatörü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | HDInsight küme yapılandırmasını okuyup değiştirmenize izin verir. |
> | **Kimlik** | 61ed4efc-fab3-44fd-B111-e24485cc132a |
> | **Eylemler** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/kümeler/getGatewaySettings/Action | HDInsight kümesi için ağ geçidi ayarlarını al |
> | Microsoft. HDInsight/kümeler/updateGatewaySettings/Action | HDInsight kümesi için ağ geçidi ayarlarını güncelleştirme |
> | Microsoft. HDInsight/kümeler/konfigürasyonlar/* |  |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. resources/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight etki alanı Hizmetleri Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | HDInsight için gereken etki alanı Hizmetleri ile ilgili işlemleri okuyabilir, oluşturabilir, değiştirebilir ve silebilir Kurumsal Güvenlik Paketi |
> | **Kimlik** | 8d8d5a11-05d3-4bdav-A417-a08778121c7c |
> | **Eylemler** |  |
> | Microsoft. AAD/*/Read |  |
> | Microsoft. AAD/domainServices/*/Read |  |
> | Microsoft. AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems hesabı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Akıllı sistem hesaplarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. |
> | **Kimlik** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ıntelligentsystems/accounts/* | Akıllı sistem hesapları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="key-vault-contributor"></a>Katkıda bulunan Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Anahtar kasalarını yönetmenize izin verir, ancak bunlara erişim sağlamaz. |
> | **Kimlik** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Keykasası/* |  |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. Keykasası/konumlar/Silinkaults/Temizleme/eylem | Geçici olarak silinen bir anahtar kasasını temizle |
> | Microsoft. Keykasası/hsmPools/* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="lab-creator"></a>Laboratuvar Oluşturucu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure Laboratuvar hesaplarınız kapsamında yönetilen Laboratuvarlarınızı oluşturmanıza, yönetmenize ve silmenize olanak sağlar. |
> | **Kimlik** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. LabServices/labAccounts/*/Read |  |
> | Microsoft. LabServices/labAccounts/createLab/eylem | Laboratuvar hesabında laboratuvar oluşturun. |
> | Microsoft. LabServices/labAccounts/boyutlar/getRegionalAvailability/eylem |  |
> | Microsoft. LabServices/labAccounts/getRegionalAvailability/eylem | Laboratuvar hesabı altında yapılandırılan her boyut kategorisi için bölgesel kullanılabilirlik bilgilerini al |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="log-analytics-contributor"></a>Log Analytics Katkıda Bulunan
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Log Analytics katkı, tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. İzleme ayarlarını düzenlediğinizde VM 'lere VM uzantısının eklenmesi dahildir; Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okuma; Otomasyon hesapları oluşturma ve yapılandırma; çözümler ekleme; ve Azure tanılama 'yı tüm Azure kaynaklarında yapılandırma. |
> | **Kimlik** | 92aaf0dad-9dadb-42b6-94a3-d43ce8d16293 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. Automation/automationAccounts/* |  |
> | Microsoft. ClassicCompute/virtualMachines/Extensions/* |  |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | Microsoft. COMPUTE/virtualMachines/Extensions/* |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/diagnosticSettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | Microsoft. Operationalınsights/* |  |
> | Microsoft. OperationsManagement/* |  |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/* |  |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="log-analytics-reader"></a>Log Analytics Okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Log Analytics okuyucu tüm izleme verilerini görüntüleyip arayabilir ve tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntüleme dahil olmak üzere izleme ayarlarını görüntüleyebilir. |
> | **Kimlik** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/eylem | Yeni altyapıyı kullanarak arama yapın. |
> | Microsoft. Operationalınsights/çalışma alanları/arama/eylem | Arama sorgusu yürütür |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | Microsoft. Operationalınsights/çalışma alanları/sharedKeys/Read | Çalışma alanının paylaşılan anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="logic-app-contributor"></a>Mantıksal uygulama Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Mantıksal uygulamaları yönetmenize izin verir, ancak bunlara erişimi değiştirmeyin. |
> | **Kimlik** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | Microsoft. ClassicStorage/storageAccounts/Read | Verilen hesaba sahip depolama hesabını döndürün. |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/metricAlerts/* |  |
> | Microsoft. Insights/diagnosticSettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | Microsoft. Insights/logdefinitions/* | Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. Etkinlik günlüğündeki günlük kategorilerini listeleyin. |
> | Microsoft. Insights/metricDefinitions/* | Ölçüm tanımlarını oku (bir kaynak için kullanılabilen ölçüm türlerinin listesi). |
> | Microsoft. Logic/* | Logic Apps kaynaklarını yönetir. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Web/Connectiongateway/* | Bir bağlantı ağ geçidi oluşturun ve yönetir. |
> | Microsoft. Web/Connections/* | Bir bağlantı oluşturun ve yönetir. |
> | Microsoft. Web/Customapsıs/* | Özel bir API oluşturur ve yönetir. |
> | Microsoft. Web/Sunucugrupları/JOIN/Action |  |
> | Microsoft. Web/Sunucugrupları/okuma | App Service planında özellikleri al |
> | Microsoft. Web/Sites/Functions/Listgizlilikler/Action | Işlev gizli dizileri listeleyin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="logic-app-operator"></a>Logic App Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremez. |
> | **Kimlik** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/*/Read | Öngörüler uyarı kurallarını okuyun |
> | Microsoft. Insights/metricAlerts/*/Read |  |
> | Microsoft. Insights/diagnosticSettings/*/Read | Logic Apps için tanılama ayarlarını alır |
> | Microsoft. Insights/metricDefinitions/*/Read | Logic Apps için kullanılabilir ölçümleri alır. |
> | Microsoft. Logic/*/Read | Logic Apps kaynaklarını okur. |
> | Microsoft. Logic/iş akışları/devre dışı bırakma/eylem | İş akışını devre dışı bırakır. |
> | Microsoft. Logic/iş akışları/etkinleştir/eylem | İş akışını izin vermez. |
> | Microsoft. Logic/iş akışları/doğrula/eylem | İş akışını doğrular. |
> | Microsoft. resources/dağıtımlar/işlemler/okuma | Dağıtım işlemlerini alır veya listeler. |
> | Microsoft. resources/abonelikler/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Web/Connectiongateway/*/Read | Bağlantı ağ geçitlerini okuyun. |
> | Microsoft. Web/Connections/*/Read | Bağlantıları oku. |
> | Microsoft. Web/Customapsıs/*/Read | Özel API 'YI okuyun. |
> | Microsoft. Web/Sunucugrupları/okuma | App Service planında özellikleri al |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="managed-application-operator-role"></a>Yönetilen uygulama Işletmeni rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yönetilen uygulama kaynakları üzerinde işlemleri okuyup gerçekleştirmenize olanak tanır |
> | **Kimlik** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. Solutions/Applications/Read | Uygulamaların bir listesini alır. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="managed-applications-reader"></a>Yönetilen uygulamalar okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yönetilen bir uygulamadaki kaynakları okumanızı ve JıT erişimi isteğinizi yapmanızı sağlar. |
> | **Kimlik** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. Solutions/Jrequests/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="managed-identity-contributor"></a>Yönetilen kimlik Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kullanıcı tarafından atanan kimlik oluşturma, okuma, güncelleştirme ve silme |
> | **Kimlik** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Eylemler** |  |
> | Microsoft. Managedıdentity/Useratandıdentities/Read | Mevcut kullanıcı tarafından atanan kimliği alır |
> | Microsoft. Managedıdentity/Useratandıdentities/Write | Yeni Kullanıcı tarafından atanmış bir kimlik oluşturur veya var olan bir kullanıcı tarafından atanan kimlikle ilişkili etiketleri güncelleştirir |
> | Microsoft. Managedıdentity/Useratandıdentities/Delete | Mevcut kullanıcı tarafından atanan bir kimliği siler |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="managed-identity-operator"></a>Yönetilen kimlik Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kullanıcı tarafından atanan kimliği okuma ve atama |
> | **Kimlik** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Eylemler** |  |
> | Microsoft. Managedıdentity/Useratandıdentities/*/Read |  |
> | Microsoft. Managedıdentity/Useratandıdentities/*/atama/Action |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="managed-services-registration-assignment-delete-role"></a>Yönetilen hizmetler kayıt ataması rol silme
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yönetilen hizmetler kayıt ataması silme rolü, kiracı kullanıcılarının kiracıya atanan kayıt atamasını silmesine izin verir. |
> | **Kimlik** | 91c1777a-f3dc-4fae-B103-61d183457e46 |
> | **Eylemler** |  |
> | Microsoft. ManagedServices/Registrationatamaları/okuma | Yönetilen hizmetler kayıt atamalarının bir listesini alır. |
> | Microsoft. ManagedServices/Registrationatamaları/silme | Yönetilen hizmetler kayıt atamasını kaldırır. |
> | Microsoft. ManagedServices/Operationdurumlar/okuma | Kaynak için işlem durumunu okur. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="management-group-contributor"></a>Yönetim grubu Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yönetim grubu katılımcısı rolü |
> | **Kimlik** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Eylemler** |  |
> | Microsoft. Management/managementGroups/Delete | Yönetim grubunu silin. |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | Microsoft. Management/managementGroups/abonelikler/Sil | Aboneliği yönetim grubundan kaldır. |
> | Microsoft. Management/managementGroups/abonelikler/Write | Mevcut aboneliği yönetim grubuyla ilişkilendirir. |
> | Microsoft. Management/managementGroups/Write | Bir yönetim grubu oluşturun veya güncelleştirin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="management-group-reader"></a>Yönetim grubu okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yönetim grubu okuyucusu rolü |
> | **Kimlik** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Eylemler** |  |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="monitoring-contributor"></a>Katkıda bulunan izleniyor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Kimlik** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. AlertsManagement/uyarılar/* |  |
> | Microsoft. AlertsManagement/alertsSummary/* |  |
> | Microsoft. Insights/actiongroups/* |  |
> | Microsoft. Insights/activityLogAlerts/* |  |
> | Microsoft. Insights/AlertRules/* | Uyarı kurallarını okuma/yazma/silme. |
> | Microsoft. Insights/bileşenler/* | Application Insights bileşenlerini okuma/yazma/silme. |
> | Microsoft. Insights/DiagnosticSettings/* | Tanılama ayarlarını okuma/yazma/silme. |
> | Microsoft. Insights/eventTypes/* | Bir abonelikteki etkinlik günlüğü olaylarını (yönetim olayları) listeleyin. Bu izin, etkinlik günlüğüne hem programlı hem de portala erişim için geçerlidir. |
> | Microsoft. Insights/LogDefinitions/* | Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. Etkinlik günlüğündeki günlük kategorilerini listeleyin. |
> | Microsoft. Insights/metricalerts/* |  |
> | Microsoft. Insights/MetricDefinitions/* | Ölçüm tanımlarını oku (bir kaynak için kullanılabilen ölçüm türlerinin listesi). |
> | Microsoft. Insights/ölçümler/* | Bir kaynak için ölçümleri okuyun. |
> | Microsoft. Insights/Register/ACTION | Microsoft Insights sağlayıcısını kaydedin |
> | Microsoft. Insights/scheduledqueryrules/* |  |
> | Microsoft. Insights/webtests/* | Web testlerini okuma/yazma/silme Application Insights. |
> | Microsoft. Insights/çalışma kitapları/* |  |
> | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/* | Log Analytics çözüm paketlerini okuma/yazma/silme. |
> | Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/* | Log Analytics kayıtlı aramalarını okuma/yazma/silme. |
> | Microsoft. Operationalınsights/çalışma alanları/arama/eylem | Arama sorgusu yürütür |
> | Microsoft. Operationalınsights/Workspaces/sharedKeys/Action | Çalışma alanının paylaşılan anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | Microsoft. Operationalınsights/Workspaces/storageınsiizconfigs/* | Log Analytics depolama öngörülerini okuma/yazma/silme. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. WorkloadMonitor/izleyicileri/* |  |
> | Microsoft. WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="monitoring-metrics-publisher"></a>Ölçüm yayımcısını izleme
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure kaynaklarında ölçüm yayımlamaya izin vermez |
> | **Kimlik** | 3913510d-42F4-4E42-8A64-420c390055eb |
> | **Eylemler** |  |
> | Microsoft. Insights/Register/ACTION | Microsoft Insights sağlayıcısını kaydedin |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Insights/ölçümler/yazma | Ölçümleri yaz |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="monitoring-reader"></a>İzleme okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Tüm izleme verilerini okuyabilir (ölçümler, Günlükler vb.). Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Kimlik** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. Operationalınsights/çalışma alanları/arama/eylem | Arama sorgusu yürütür |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="network-contributor"></a>Ağ katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Ağları yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. |
> | **Kimlik** | 4d97b98b-1d4f-4787-A291-c67834d212e7 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/* | Ağ oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="new-relic-apm-account-contributor"></a>Yeni relik APM hesabı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | New Relic Application Performance Management hesaplarını ve uygulamaları yönetmenize izin verir, ancak bunlara erişimi kalmaz. |
> | **Kimlik** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Newrelik. APM/hesaplar/* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="reader-and-data-access"></a>Okuyucu ve veri erişimi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Her şeyi görüntülemenize izin verir, ancak bir depolama hesabını veya kapsanan kaynağı silmenize veya oluşturmanıza izin vermez. Ayrıca depolama hesabı anahtarlarına erişim aracılığıyla bir depolama hesabında bulunan tüm verilere okuma/yazma erişimi de sağlar. |
> | **Kimlik** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/eylem | Belirtilen depolama hesabı için hesap SAS belirtecini döndürür. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="redis-cache-contributor"></a>Katkıda bulunan Redis Cache
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Redsıs önbellekler yönetmenize izin verir, ancak bunlara erişimi olmaz. |
> | **Kimlik** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Cache/reddir/* | Redsıs önbellekler oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="resource-policy-contributor-preview"></a>Kaynak Ilkesi katılımcısı (Önizleme)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Önizle Kaynak ilkesi oluşturma/değiştirme, destek bileti oluşturma ve kaynakları/hiyerarşiyi okuma haklarıyla EA 'dan Kullanıcı geri alma. |
> | **Kimlik** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. Authorization/poliyasatamaları/* | İlke atamaları oluşturma ve yönetme |
> | Microsoft. Authorization/PolicyDefinitions/* | İlke tanımları oluşturma ve yönetme |
> | Microsoft. Authorization/policysetdefinitions/* | İlke kümeleri oluşturma ve yönetme |
> | Microsoft. Poliyeleghts/* |  |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="scheduler-job-collections-contributor"></a>Zamanlayıcı Iş koleksiyonları Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Zamanlayıcı iş koleksiyonlarını yönetmenizi sağlar, ancak bunlara erişimi kalmaz. |
> | **Kimlik** | 188a0f2f-5c9e-469B-ae67-2aa5ce574b94 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Scheduler/jobcollections/* | İş koleksiyonları oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="search-service-contributor"></a>Katkıda bulunan Arama Hizmeti
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Arama hizmetlerini yönetmenize izin verir, ancak bunlara erişim izni vermez. |
> | **Kimlik** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Search/searchServices/* | Arama Hizmetleri oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="security-admin"></a>Güvenlik Yöneticisi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yalnızca güvenlik merkezi 'nde: güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, güvenlik ilkelerini düzenleyebilir, uyarıları ve önerileri görüntüleyebilir, uyarıları ve önerileri kapatabilir |
> | **Kimlik** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Authorization/Poliyasatamaları/* | İlke atamaları oluşturma ve yönetme |
> | Microsoft. Authorization/policyDefinitions/* | İlke tanımları oluşturma ve yönetme |
> | Microsoft. Authorization/policySetDefinitions/* | İlke kümeleri oluşturma ve yönetme |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | Microsoft. Operationalınsights/çalışma alanları/*/Read | Log Analytics verilerini görüntüleme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Security/* |  |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="security-manager-legacy"></a>Güvenlik Yöneticisi (eski)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Bu eski bir roldür. Lütfen bunun yerine Güvenlik Yöneticisi 'ni kullanın |
> | **Kimlik** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. ClassicCompute/*/Read | Yapılandırma bilgileri klasik sanal makineler 'i okuyun |
> | Microsoft. ClassicCompute/virtualMachines/*/Write | Klasik sanal makineler için yazma yapılandırması |
> | Microsoft. ClassicNetwork/*/Read | Klasik ağla ilgili yapılandırma bilgilerini okuyun |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Security/* | Güvenlik bileşenleri ve ilkeleri oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="security-reader"></a>Güvenlik okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yalnızca güvenlik merkezi 'nde: önerileri ve uyarıları görüntüleyebilir, güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz |
> | **Kimlik** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Operationalınsights/çalışma alanları/*/Read | Log Analytics verilerini görüntüleme |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Security/*/Read | Güvenlik bileşenlerini ve ilkelerini okuyun |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Management/managementGroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="site-recovery-contributor"></a>Katkıda bulunan Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Kasa oluşturma ve rol atama dışında Site Recovery hizmetini yönetmenizi sağlar |
> | **Kimlik** | 6670b86e-a3f7-4917-AC9B-5d6ab1be4567 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. RecoveryServices/konumlar/allocatedStamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | Microsoft. RecoveryServices/Locations/Allocatedamga/eylem | AllocateStamp, hizmet tarafından kullanılan iç işlemdir |
> | Microsoft. RecoveryServices/Vaults/sertifikalar/yazma | Kaynak sertifikası güncelleştirme işlemi kaynak/kasa kimlik bilgisi sertifikasını güncelleştirir. |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/* | Kasa ile ilgili genişletilmiş bilgileri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Vaults/refreshContainers/Read |  |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/* | Kayıtlı kimlikler oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/replicationAlertSettings/* | Çoğaltma uyarı ayarlarını oluşturma veya güncelleştirme |
> | Microsoft. RecoveryServices/kasaults/replicationEvents/okuma | Tüm olayları okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/* | Çoğaltma yapıları oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/replicationJobs/* | Çoğaltma işleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/replicationPolicies/* | Çoğaltma ilkeleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/* | Kurtarma planlarını oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/storageConfig/* | Kurtarma Hizmetleri kasasının depolama yapılandırmasını oluşturma ve yönetme |
> | Microsoft. RecoveryServices/Vaults/TokenInfo/Read |  |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | Microsoft. RecoveryServices/Vaults/vaultTokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | Microsoft. RecoveryServices/Vaults/monitoringAlerts/* | Kurtarma Hizmetleri Kasası için uyarıları okuyun |
> | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="site-recovery-operator"></a>Site Recovery Işleci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Yük devretme ve yeniden çalışma ve diğer Site Recovery yönetim işlemlerini gerçekleştirmenize izin verir |
> | **Kimlik** | 494ae006-DB33-4328-BF46-533a6560a3ca |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. RecoveryServices/konumlar/allocatedStamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | Microsoft. RecoveryServices/Locations/Allocatedamga/eylem | AllocateStamp, hizmet tarafından kullanılan iç işlemdir |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Vaults/refreshContainers/Read |  |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/operationResults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | Microsoft. RecoveryServices/Vaults/replicationAlertSettings/Read | Tüm uyarı ayarlarını okuyun |
> | Microsoft. RecoveryServices/kasaults/replicationEvents/okuma | Tüm olayları okuyun |
> | Microsoft. RecoveryServices/kasaults/Replicationdokuların/Checktutarlılığı/eylemi | Dokunun tutarlılığını denetler |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/okuma | Tüm yapıları okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/yeniden Ilişkilendirilmesi/eylem | Ağ geçidini yeniden ilişkilendir |
> | Microsoft. RecoveryServices/kasaults/Replicationyapılar/yenilenebilir sertifika/eylem | Doku için Sertifikayı Yenile |
> | Microsoft. RecoveryServices/Vaults/Replicationdokular/replicationNetworks/Read | Tüm ağları okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationNetworks/replicationNetworkMappings/Read | Tüm ağ eşlemelerini okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Read | Tüm koruma kapsayıcılarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir | Korunabilir öğeleri oku |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/applyRecoveryPoint/Action | Kurtarma noktası Uygula |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/işlem | Yük devretme yürütmesi |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Plannedyük devretme/eylem | Planlı Yük devretme |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Read | Tüm korumalı öğeleri okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/recoveryPoints/Read | Tüm çoğaltma kurtarma noktalarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/\ çoğaltma/eylem | Çoğaltmayı Onar |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorudıtems/reProtect/ACTION | Korumalı öğeyi yeniden koru |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/switchprotection/eylem | Koruma kapsayıcısını Değiştir |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/TEMS/testFailover/ACTION | Test Yük Devretmesi |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuları/replicationProtectionContainers/Replicationkorunabilir/test Failovercleanup/Action | Yük devretme sınamasını Temizleme |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/unplannedFailover/ACTION | Yük Devretme |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Updatebir Dıtems/Update, Ityservice/Action | Mobility hizmetini Güncelleştir |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Tüm koruma kapsayıcısı eşlemelerini okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationRecoveryServicesProviders/Read | Tüm kurtarma hizmetleri sağlayıcılarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationRecoveryServicesProviders/refreshProvider/Action | Sağlayıcıyı Yenile |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/Replicationstorageclassıfler/okuma | Tüm depolama sınıflandırmalarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/Replicationstorageclassıflıya/Replicationstorageclassıficationmappings/Read | Tüm depolama sınıflandırması eşlemelerini okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationvCenters/Read | Tüm sanal merkezleri okuyun |
> | Microsoft. RecoveryServices/Vaults/replicationJobs/* | Çoğaltma işleri oluşturma ve yönetme |
> | Microsoft. RecoveryServices/kasaults/replicationPolicies/okuma | Tüm Ilkeleri okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/Failovercommıt/Action | Yük devretme yürütmesi kurtarma planı |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/plannedFailover/ACTION | Planlı Yük devretme kurtarma planı |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/okuma | Tüm kurtarma planlarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/yeniden koruma/eylem | Kurtarma planını yeniden koru |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/testFailover/Action | Yük devretme kurtarma planını sına |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/testFailoverCleanup/Action | Sınama yük devretmesi Temizleme kurtarma planı |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/unplannedFailover/ACTION | Yük devretme kurtarma planı |
> | Microsoft. RecoveryServices/Vaults/monitoringAlerts/* | Kurtarma Hizmetleri Kasası için uyarıları okuyun |
> | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. RecoveryServices/Vaults/storageConfig/Read |  |
> | Microsoft. RecoveryServices/Vaults/TokenInfo/Read |  |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | Microsoft. RecoveryServices/Vaults/vaultTokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="site-recovery-reader"></a>Site Recovery okuyucu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Site Recovery durumunu görüntülemenize izin verir, ancak diğer yönetim işlemlerini gerçekleştirmenize izin vermez |
> | **Kimlik** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. RecoveryServices/konumlar/allocatedStamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | Microsoft. RecoveryServices/Kasaults/Extendedınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | Microsoft. RecoveryServices/Kasaults/monitoringAlerts/okuma | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | Microsoft. RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Vaults/refreshContainers/Read |  |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/operationResults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | Microsoft. RecoveryServices/Vaults/registeredIdentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | Microsoft. RecoveryServices/Vaults/replicationAlertSettings/Read | Tüm uyarı ayarlarını okuyun |
> | Microsoft. RecoveryServices/kasaults/replicationEvents/okuma | Tüm olayları okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/okuma | Tüm yapıları okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokular/replicationNetworks/Read | Tüm ağları okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationNetworks/replicationNetworkMappings/Read | Tüm ağ eşlemelerini okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Read | Tüm koruma kapsayıcılarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir | Korunabilir öğeleri oku |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationProtectionContainers/Replicationkorunabilir/Read | Tüm korumalı öğeleri okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationkorunabilir/recoveryPoints/Read | Tüm çoğaltma kurtarma noktalarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Tüm koruma kapsayıcısı eşlemelerini okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/replicationRecoveryServicesProviders/Read | Tüm kurtarma hizmetleri sağlayıcılarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/Replicationstorageclassıfler/okuma | Tüm depolama sınıflandırmalarını okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationdokuların/Replicationstorageclassıflıya/Replicationstorageclassıficationmappings/Read | Tüm depolama sınıflandırması eşlemelerini okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationvCenters/Read | Tüm sanal merkezleri okuyun |
> | Microsoft. RecoveryServices/kasaults/replicationJobs/okuma | Tüm Işleri okuyun |
> | Microsoft. RecoveryServices/kasaults/replicationPolicies/okuma | Tüm Ilkeleri okuyun |
> | Microsoft. RecoveryServices/Vaults/Replicationrecoveryplan/okuma | Tüm kurtarma planlarını okuyun |
> | Microsoft. RecoveryServices/Vaults/storageConfig/Read |  |
> | Microsoft. RecoveryServices/Vaults/TokenInfo/Read |  |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | Microsoft. RecoveryServices/Vaults/vaultTokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="spatial-anchors-account-contributor"></a>Uzamsal bağlayıcı hesabı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Hesabınızdaki uzamsal bağlantıları yönetmenizi sağlar, ancak onları silmez |
> | **Kimlik** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/ACTION | Uzamsal bağlayıcı oluşturma |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Özellikler/okuma | Uzamsal Tutturucuların özelliklerini al |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/sorgu/okuma | Uzamsal bağlayıcıları bul |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uzamsal Tutturucuların özelliklerini güncelleştirme |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="spatial-anchors-account-owner"></a>Uzamsal bağlayıcı hesap sahibi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Hesabınızdaki uzamsal bağlayıcıları, silme dahil olmak üzere yönetmenizi sağlar |
> | **Kimlik** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/ACTION | Uzamsal bağlayıcı oluşturma |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Uzamsal bağlayıcıları Sil |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Özellikler/okuma | Uzamsal Tutturucuların özelliklerini al |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/sorgu/okuma | Uzamsal bağlayıcıları bul |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uzamsal Tutturucuların özelliklerini güncelleştirme |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="spatial-anchors-account-reader"></a>Uzamsal bağlayıcı hesap okuyucu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Hesabınızdaki uzamsal Tutturucuların özelliklerini bulmanıza ve okumanızı sağlar |
> | **Kimlik** | 5d51204f-EB77-4B1C-b86a-2ec626c49413 |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Özellikler/okuma | Uzamsal Tutturucuların özelliklerini al |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/sorgu/okuma | Uzamsal bağlayıcıları bul |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="sql-db-contributor"></a>SQL DB Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | SQL veritabanlarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. Ayrıca, güvenlikle ilgili ilkeleri veya bunların üst SQL sunucularını yönetemezsiniz. |
> | **Kimlik** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. SQL/konumlar/*/Read |  |
> | Microsoft. SQL/Servers/veritabanları/* | SQL veritabanlarını oluşturma ve yönetme |
> | Microsoft. SQL/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | Microsoft. Insights/metricDefinitions/okuma | Ölçüm tanımlarını oku |
> | **NotActions** |  |
> | Microsoft. SQL/ManagedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/Securityalcertpolicies/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/veritabanları/ |  |
> | Microsoft. SQL/ManagedInstances/Securityalcertpolicies/* |  |
> | Microsoft. SQL/ManagedInstances/ |  |
> | Microsoft. SQL/Servers/veritabanları/auditingPolicies/* | Denetim ilkelerini düzenleme |
> | Microsoft. SQL/Servers/Databases/auditingSettings/* | Denetim ayarlarını Düzenle |
> | Microsoft. SQL/Servers/veritabanları/auditRecords/okuma | Veritabanı blobu denetim kayıtlarını alma |
> | Microsoft. SQL/Servers/veritabanları/connectionPolicies/* | Bağlantı ilkelerini düzenleme |
> | Microsoft. SQL/Servers/veritabanları/currentSensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/* | Veri maskeleme ilkelerini düzenleme |
> | Microsoft. SQL/Servers/Databases/extendedAuditingSettings/* |  |
> | Microsoft. SQL/Servers/veritabanları/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/Databases/Securityalcertpolicies/* | Güvenlik Uyarısı ilkelerini düzenleme |
> | Microsoft. SQL/Servers/Databases/Securityölçümlerini/* | Güvenlik ölçümlerini düzenleme |
> | Microsoft. SQL/Servers/veritabanları/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/ |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="sql-managed-instance-contributor"></a>SQL yönetilen örnek Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | SQL yönetilen örnekleri ve gerekli ağ yapılandırmasını yönetmenizi sağlar, ancak başkalarına erişim izni veremeyiz. |
> | **Kimlik** | 4939a1f6-9ae0-4E48-A1E0-f2cbe897382d |
> | **Eylemler** |  |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft. SQL/konumlar/*/Read |  |
> | Microsoft. SQL/ManagedInstances/* |  |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Network/virtualNetworks/alt ağları/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | Microsoft. Insights/metricDefinitions/okuma | Ölçüm tanımlarını oku |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="sql-security-manager"></a>SQL Güvenlik Yöneticisi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | , SQL Server ve veritabanlarının güvenlikle ilgili ilkelerini yönetmenizi sağlar, ancak bunlara erişemez. |
> | **Kimlik** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Microsoft yetkilendirmesini okuyun |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/virtualNetworks/alt ağlar/Jodavetli Aserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. SQL/ManagedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/Securityalcertpolicies/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/veritabanları/ |  |
> | Microsoft. SQL/ManagedInstances/Securityalcertpolicies/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/transparentDataEncryption/* |  |
> | Microsoft. SQL/ManagedInstances/ |  |
> | Microsoft. SQL/Servers/auditingPolicies/* | SQL Server denetim ilkeleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/auditingSettings/* | SQL Server denetim ayarı oluşturma ve yönetme |
> | Microsoft. SQL/Servers/extendedAuditingSettings/Read | Belirli bir sunucuda yapılandırılmış genişletilmiş sunucu blobu denetim ilkesinin ayrıntılarını alma |
> | Microsoft. SQL/Servers/veritabanları/auditingPolicies/* | SQL Server veritabanı denetim ilkeleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/Databases/auditingSettings/* | SQL Server veritabanı denetim ayarlarını oluşturma ve yönetme |
> | Microsoft. SQL/Servers/veritabanları/auditRecords/okuma | Denetim kayıtlarını oku |
> | Microsoft. SQL/Servers/veritabanları/connectionPolicies/* | SQL Server veritabanı bağlantı ilkeleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/veritabanları/currentSensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/* | SQL Server veritabanı veri maskeleme ilkeleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/veritabanları/extendedAuditingSettings/Read | Belirli bir veritabanında yapılandırılan genişletilmiş blob denetim ilkesinin ayrıntılarını alma |
> | Microsoft. SQL/Servers/veritabanları/okuma | Veritabanlarının listesini döndürün veya belirtilen veritabanının özelliklerini alır. |
> | Microsoft. SQL/Servers/veritabanları/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/şemalar/okuma | Veritabanı şeması alın. |
> | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/okuma | Veritabanı sütunu al. |
> | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/okuma | Veritabanı tablosu al. |
> | Microsoft. SQL/Servers/Databases/Securityalcertpolicies/* | SQL Server veritabanı güvenlik uyarısı ilkeleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/Databases/Securityölçümlerini/* | SQL Server veritabanı güvenlik ölçümleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/veritabanları/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/transparentDataEncryption/* |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/firewallRules/* |  |
> | Microsoft. SQL/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | Microsoft. SQL/Servers/Securityalcertpolicies/* | SQL Server güvenlik uyarı ilkeleri oluşturma ve yönetme |
> | Microsoft. SQL/Servers/ |  |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="sql-server-contributor"></a>Katkıda bulunan SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | SQL Server ve veritabanlarını yönetmenizi sağlar, ancak bunlara yönelik erişimleri ve güvenlikle ilgili ilkeleri yönetemez. |
> | **Kimlik** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. SQL/konumlar/*/Read |  |
> | Microsoft. SQL/Servers/* | SQL Server 'lar oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Insights/ölçümler/okuma | Ölçümleri oku |
> | Microsoft. Insights/metricDefinitions/okuma | Ölçüm tanımlarını oku |
> | **NotActions** |  |
> | Microsoft. SQL/ManagedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/Securityalcertpolicies/* |  |
> | Microsoft. SQL/ManagedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft. SQL/ManagedInstances/veritabanları/ |  |
> | Microsoft. SQL/ManagedInstances/Securityalcertpolicies/* |  |
> | Microsoft. SQL/ManagedInstances/ |  |
> | Microsoft. SQL/Servers/auditingPolicies/* | SQL Server denetim ilkelerini düzenleme |
> | Microsoft. SQL/Servers/auditingSettings/* | SQL Server denetim ayarlarını Düzenle |
> | Microsoft. SQL/Servers/veritabanları/auditingPolicies/* | SQL Server veritabanı denetim ilkelerini düzenleme |
> | Microsoft. SQL/Servers/Databases/auditingSettings/* | SQL Server veritabanı denetim ayarlarını Düzenle |
> | Microsoft. SQL/Servers/veritabanları/auditRecords/okuma | Denetim kayıtlarını oku |
> | Microsoft. SQL/Servers/veritabanları/connectionPolicies/* | SQL Server veritabanı bağlantı ilkelerini düzenleme |
> | Microsoft. SQL/Servers/veritabanları/currentSensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/dataMaskingPolicies/* | SQL Server veritabanı veri maskeleme ilkelerini düzenleme |
> | Microsoft. SQL/Servers/Databases/extendedAuditingSettings/* |  |
> | Microsoft. SQL/Servers/veritabanları/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/Databases/Securityalcertpolicies/* | SQL Server veritabanı güvenlik uyarısı ilkelerini düzenleme |
> | Microsoft. SQL/Servers/Databases/Securityölçümlerini/* | SQL Server veritabanı güvenlik ölçümlerini düzenleme |
> | Microsoft. SQL/Servers/veritabanları/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/veritabanları/ |  |
> | Microsoft. SQL/Servers/extendedAuditingSettings/* |  |
> | Microsoft. SQL/Servers/Securityalcertpolicies/* | SQL Server güvenlik uyarısı ilkelerini düzenleme |
> | Microsoft. SQL/Servers/ |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-account-contributor"></a>Depolama hesabı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Depolama hesaplarının yönetimine izin verir. Paylaşılan anahtar yetkilendirmesi aracılığıyla verilere erişmek için kullanılabilen hesap anahtarına erişim sağlar. |
> | **Kimlik** | 17d1049b-9a84-46fb-8F53-869881c3d3ab |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Tüm yetkilendirmeyi oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/diagnosticSettings/* | Tanılama ayarlarını yönetme |
> | Microsoft. Network/virtualNetworks/alt ağlar/Jodavetli Aserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. Storage/storageAccounts/* | Depolama hesapları oluşturma ve yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-account-key-operator-service-role"></a>Depolama hesabı anahtar operatörü hizmet rolü
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Depolama hesabı erişim anahtarlarının listelenmesi ve yeniden oluşturulmasına izin verir. |
> | **Kimlik** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | Microsoft. Storage/storageAccounts/RegenerateKey/ACTION | Belirtilen depolama hesabı için erişim anahtarlarını yeniden oluşturur. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-blob-data-contributor"></a>Depolama Blobu veri Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure depolama kapsayıcıları ve bloblarını okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Delete | Kapsayıcıyı silin. |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/okuma | Bir kapsayıcı veya kapsayıcıların listesini döndürün. |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Write | Kapsayıcının meta verilerini veya özelliklerini değiştirin. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/Sil | Bir blobu silin. |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/okuma | Blob veya Blobların listesini döndürün. |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/yazma | Bir bloba yazın. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-blob-data-owner"></a>Depolama Blobu veri sahibi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | , POSIX erişim denetimi atama dahil olmak üzere Azure depolama blob kapsayıcılarına ve verilerine tam erişim sağlar. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/* | Kapsayıcılarda tam izinler. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/* | Bloblarda tam izinler. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-blob-data-reader"></a>Depolama Blobu veri okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure depolama kapsayıcıları ve bloblarını okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/okuma | Bir kapsayıcı veya kapsayıcıların listesini döndürün. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/okuma | Blob veya Blobların listesini döndürün. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-blob-delegator"></a>Depolama Blobu Delegator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure AD kimlik bilgileriyle imzalanan bir kapsayıcı veya blob için paylaşılan erişim imzası oluşturmak üzere kullanılabilen bir Kullanıcı temsili anahtarı alın. Daha fazla bilgi için bkz. [Kullanıcı TEMSILI SAS oluşturma](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Kimlik** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Depolama dosyası veri SMB paylaşımında katkıda bulunan
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | SMB üzerinden Azure depolama dosya paylaşımlarında okuma, yazma ve silme erişimine izin verir |
> | **Kimlik** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesini döndürür. |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Write | Bir dosya yazmanın veya bir klasör oluşturmanın sonucunu döndürür. |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Delete | Dosya/klasör silmenin sonucunu döndürür. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Depolama dosyası veri SMB paylaşımında yükseltilmiş katkıda bulunan
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | SMB üzerinden Azure depolama dosya paylaşımlarında NTFS izin erişimini okuma, yazma, silme ve değiştirme sağlar |
> | **Kimlik** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesini döndürür. |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Write | Bir dosya yazmanın veya bir klasör oluşturmanın sonucunu döndürür. |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Delete | Dosya/klasör silmenin sonucunu döndürür. |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/modifypermissions/Action | Bir dosya/klasör üzerinde izin değiştirme sonucunu döndürür. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-file-data-smb-share-reader"></a>Depolama dosyası veri SMB paylaşma okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | SMB üzerinden Azure dosya paylaşımında okuma erişimine izin verir |
> | **Kimlik** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/fileServices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesini döndürür. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-queue-data-contributor"></a>Depolama kuyruğu verileri Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure depolama kuyruklarını ve sıra iletilerini okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/Sil | Kuyruğu silme. |
> | Microsoft. Storage/storageAccounts/queueServices/kuyrukları/okuma | Kuyruğu veya kuyruk listesini döndürün. |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/Write | Sıra meta verilerini veya özelliklerini değiştirin. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/Sil | Bir kuyruktan bir veya daha fazla iletiyi silin. |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/okundu | Bir kuyruktaki bir veya daha fazla iletiyi Peek veya alma. |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/yazma | Bir kuyruğa ileti ekleyin. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-queue-data-message-processor"></a>Depolama kuyruğu veri Iletisi Işlemcisi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure depolama kuyruğundan bir iletiyi göz atın, alın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | 8a0f0c08-91a1-4084-bc3d-661d67233bes |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/okundu | İletiye göz atın. |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/işlem/eylem | Bir iletiyi alın ve silin. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-queue-data-message-sender"></a>Depolama kuyruğu veri Iletisi gönderici
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Bir Azure depolama kuyruğuna ileti ekleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Eylemler** |  |
> | *seçim* |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/ekleme/eylem | Bir kuyruğa ileti ekleyin. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="storage-queue-data-reader"></a>Depolama kuyruğu veri okuyucusu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure depolama kuyruklarını ve sıra iletilerini okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Kimlik** | 19e7f393-937e-4F77-808e-94535e297925 |
> | **Eylemler** |  |
> | Microsoft. Storage/storageAccounts/queueServices/kuyrukları/okuma | Kuyruğu veya kuyruk listesini döndürür. |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Storage/storageAccounts/queueServices/kuyruklar/iletiler/okundu | Bir kuyruktaki bir veya daha fazla iletiyi Peek veya alma. |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="support-request-contributor"></a>Destek Isteği Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Destek istekleri oluşturmanızı ve yönetmenizi sağlar |
> | **Kimlik** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="traffic-manager-contributor"></a>Katkıda bulunan Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Traffic Manager profillerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. |
> | **Kimlik** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/trafficManagerProfiles/* |  |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="user-access-administrator"></a>Kullanıcı Erişimi Yöneticisi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Azure kaynaklarına Kullanıcı erişimini yönetmenizi sağlar. |
> | **Kimlik** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Eylemler** |  |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | Microsoft. Authorization/* | Yetkilendirmeyi Yönetme |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="virtual-machine-administrator-login"></a>Sanal Makine Yöneticisi oturum açma
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Portalda sanal makineleri görüntüleme ve yönetici olarak oturum açma |
> | **Kimlik** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Eylemler** |  |
> | Microsoft. Network/publicIPAddresses/Read | Genel IP adresi tanımını alır. |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. Network/loadBalancers/Read | Yük dengeleyici tanımını alır |
> | Microsoft. Network/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | Microsoft. COMPUTE/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. COMPUTE/virtualMachines/LOGIN/Action | Bir sanal makinede normal kullanıcı olarak oturum açın |
> | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/Action | Windows yönetici veya Linux kök kullanıcı ayrıcalıklarına sahip bir sanal makinede oturum açma |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="virtual-machine-contributor"></a>Sanal makine Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Sanal makineleri yönetmenize izin verir, ancak bunlara bağlı oldukları sanal ağ veya depolama hesabına erişemez. |
> | **Kimlik** | 9980e02c-c2be-4d73-94E8-173b1dc7cf3c |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/* | İşlem kullanılabilirlik kümeleri oluşturma ve yönetme |
> | Microsoft. COMPUTE/Locations/* | İşlem konumları oluşturma ve yönetme |
> | Microsoft. COMPUTE/virtualMachines/* | Sanal makineler oluşturma ve yönetme |
> | Microsoft. COMPUTE/virtualMachineScaleSets/* | Sanal Makine Ölçek Kümeleri oluşturma ve yönetme |
> | Microsoft. DevTestLab/zamanlamalar/* |  |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Network/Applicationgateway/Backendavddresspoir/JOIN/Action | Bir uygulama ağ geçidi arka uç adres havuzunu birleştirir. Alertable değil. |
> | Microsoft. Network/loadBalancers/Backendadddresspoir/JOIN/Action | Yük dengeleyici arka uç adres havuzunu birleştirir. Alertable değil. |
> | Microsoft. Network/loadBalancers/ınboundnatpools/JOIN/Action | Yük dengeleyici gelen NAT havuzunu birleştirir. Alertable değil. |
> | Microsoft. Network/loadBalancers/ınboundnatrules/JOIN/Action | Yük dengeleyici gelen NAT kuralına katılır. Alertable değil. |
> | Microsoft. Network/loadBalancers/araştırmaları/JOIN/Action | Yük dengeleyicinin araştırmalarını sağlar. Örneğin, bu izinle VM Ölçek kümesinin Healtharaştırma özelliği araştırmasına başvurabilir. Alertable değil. |
> | Microsoft. Network/loadBalancers/Read | Yük dengeleyici tanımını alır |
> | Microsoft. Network/Locations/* | Ağ konumları oluşturma ve yönetme |
> | Microsoft. Network/NetworkInterfaces/* | Ağ arabirimlerini oluşturma ve yönetme |
> | Microsoft. Network/networkSecurityGroups/JOIN/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | Microsoft. Network/networkSecurityGroups/Read | Bir ağ güvenlik grubu tanımını alır |
> | Microsoft. Network/publicIPAddresses/JOIN/Action | Genel bir IP adresini birleştirir. Alertable değil. |
> | Microsoft. Network/publicIPAddresses/Read | Genel IP adresi tanımını alır. |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | Microsoft. RecoveryServices/konumlar/* |  |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/Backupprotectionamacını/Write | Yedekleme koruma hedefi oluşturma |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/*/Read |  |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/yazma | Yedekleme korumalı öğesi oluştur |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/Read | Tüm koruma Ilkelerini döndürür |
> | Microsoft. RecoveryServices/Vaults/backupPolicies/Write | Koruma Ilkesi oluşturur |
> | Microsoft. RecoveryServices/Vaults/okuma | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | Microsoft. RecoveryServices/Kasaults/kullanımlar/okuma | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | Microsoft. RecoveryServices/Vaults/Write | Kasa oluşturma işlemi, ' kasa ' türünde bir Azure kaynağı oluşturur |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. SqlVirtualMachine/* |  |
> | Microsoft. Storage/storageAccounts/listKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | Microsoft. Storage/storageAccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="virtual-machine-user-login"></a>Sanal makine Kullanıcı oturumu açma
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Portalda sanal makineleri görüntüleyin ve normal bir kullanıcı olarak oturum açın. |
> | **Kimlik** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Eylemler** |  |
> | Microsoft. Network/publicIPAddresses/Read | Genel IP adresi tanımını alır. |
> | Microsoft. Network/virtualNetworks/Read | Sanal ağ tanımını al |
> | Microsoft. Network/loadBalancers/Read | Yük dengeleyici tanımını alır |
> | Microsoft. Network/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | Microsoft. COMPUTE/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | Microsoft. COMPUTE/virtualMachines/LOGIN/Action | Bir sanal makinede normal kullanıcı olarak oturum açın |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="web-plan-contributor"></a>Web planı Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Web siteleri için Web planlarını yönetmenizi sağlar, ancak bunlara erişemez. |
> | **Kimlik** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Web/Sunucugrupları/* | Sunucu grupları oluşturma ve yönetme |
> | Microsoft. Web/hostingEnvironments/JOIN/Action | Bir App Service Ortamı birleştirir |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="website-contributor"></a>Web sitesi Katılımcısı
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Açıklama** | Web sitelerini yönetmenizi sağlar, ancak bunlara erişemez. |
> | **Kimlik** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Eylemler** |  |
> | Microsoft. Authorization/*/Read | Yetkilendirmeyi oku |
> | Microsoft. Insights/alertRules/* | Öngörüler uyarı kuralları oluşturma ve yönetme |
> | Microsoft. Insights/bileşenler/* | Öngörüler bileşenleri oluşturma ve yönetme |
> | Microsoft. ResourceHealth/kullanılabilirlik Bilitydurumlar/okuma | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | Microsoft. resources/dağıtımlar/* | Kaynak grubu dağıtımlarını oluşturma ve yönetme |
> | Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | Microsoft. support/* | Destek biletleri oluşturma ve yönetme |
> | Microsoft. Web/Certificates/* | Web sitesi sertifikaları oluşturma ve yönetme |
> | Microsoft. Web/listSitesAssignedToHostName/Read | Ana bilgisayar adına atanan sitelerin adlarını alın. |
> | Microsoft. Web/Sunucugrupları/JOIN/Action |  |
> | Microsoft. Web/Sunucugrupları/okuma | App Service planında özellikleri al |
> | Microsoft. Web/Sites/* | Web siteleri oluşturma ve yönetme (site oluşturma, ayrıca ilişkili App Service planına yazma izinleri gerektirir) |
> | **NotActions** |  |
> | *seçim* |  |
> | **Veri eylemleri** |  |
> | *seçim* |  |
> | **NotDataActions** |  |
> | *seçim* |  |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için özel roller](custom-roles.md)
- [RBAC ve Azure portalını kullanarak Azure kaynaklarına erişimi yönetme](role-assignments-portal.md)
- [Azure Güvenlik Merkezi'nde İzinler](../security-center/security-center-permissions.md)
