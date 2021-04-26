---
title: Azure yerleşik rolleri - Azure RBAC
description: Bu makalede, Azure rol tabanlı erişim denetimi (Azure RBAC) için Azure yerleşik rolleri açıklanmaktadır. Eylemler, NotActions, DataActions ve NotDataActions öğelerini listeler.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 04/09/2021
ms.custom: generated
ms.openlocfilehash: 0e8363ee29df84478e5ab5c14b43ea07e3bea8ee
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815315"
---
# <a name="azure-built-in-roles"></a>Yerleşik Azure rolleri

[Azure rol tabanlı erişim denetimi (Azure RBAC)](overview.md) , kullanıcılara, gruplara, hizmet sorumlularına ve yönetilen kimliklere atayabileceğiniz birkaç Azure yerleşik rolüne sahiptir. Rol atamaları, Azure kaynaklarına erişimi denetlemenize olanak sağlar. Yerleşik roller kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, kendi [Azure özel rollerinizi](custom-roles.md)de oluşturabilirsiniz. Rol atama hakkında daha fazla bilgi için bkz. [Azure rolü atama adımları](role-assignments-steps.md).

Bu makalede, Azure yerleşik rolleri listelenir. Azure Active Directory (Azure AD) için yönetici rolleri arıyorsanız bkz. [Azure AD yerleşik rolleri](../active-directory/roles/permissions-reference.md).

Aşağıdaki tabloda, yerleşik her rolün kısa bir açıklaması verilmiştir. `Actions`Her rolün,, ve listesini görmek için rol adına `NotActions` tıklayın `DataActions` `NotDataActions` . Bu eylemlerin ne anlama geldiğini ve bunların yönetim ve veri düzlemleri için nasıl uygulandığını hakkında bilgi için bkz. [Azure rol tanımlarını anlama](role-definitions.md).

## <a name="all"></a>Tümü

> [!div class="mx-tableFixed"]
> | Yerleşik rol | Description | ID |
> | --- | --- | --- |
> | **Genel** |  |  |
> | [Katkıda Bulunan](#contributor) | Tüm kaynakları yönetmek için tam erişim verir, ancak Azure RBAC 'de roller atamanıza, Azure 'da atamaları yönetmenize veya görüntü galerileri paylaşmanıza izin vermez. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Sahibi](#owner) | Azure RBAC 'de rol atama özelliği de dahil olmak üzere tüm kaynakları yönetmek için tam erişim verir. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Okuyucu](#reader) | Tüm kaynakları görüntüleyin, ancak herhangi bir değişiklik yapmanıza izin vermez. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Kullanıcı Erişimi Yöneticisi](#user-access-administrator) | Azure kaynaklarına Kullanıcı erişimini yönetmenizi sağlar. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **İşlem** |  |  |
> | [Klasik sanal makine Katılımcısı](#classic-virtual-machine-contributor) | Klasik sanal makineleri yönetmenizi sağlar ancak bunlara yönelik erişimi, bağlı oldukları sanal ağ veya depolama hesabı için değil. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Sanal Makine Yöneticisi oturum açma](#virtual-machine-administrator-login) | Portalda sanal makineleri görüntüleme ve yönetici olarak oturum açma | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Sanal Makine Katılımcısı](#virtual-machine-contributor) | Sanal makineleri yönetmenize izin verir, ancak bunlara bağlı oldukları sanal ağ veya depolama hesabına erişemez. | 9980e02c-c2be-4d73-94E8-173b1dc7cf3c |
> | [Sanal makine Kullanıcı oturumu açma](#virtual-machine-user-login) | Portalda sanal makineleri görüntüleyin ve normal bir kullanıcı olarak oturum açın. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Ağ** |  |  |
> | [CDN uç noktası Katılımcısı](#cdn-endpoint-contributor) | CDN uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN uç nokta okuyucusu](#cdn-endpoint-reader) | CDN uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz. | 871e35f6-b5c1-49cc-A043-bde969a0f2cd |
> | [CDN profili Katılımcısı](#cdn-profile-contributor) | CDN profillerini ve uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN profili okuyucu](#cdn-profile-reader) | CDN profillerini ve uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz. | 8f96442b-4075-438f-813D-ad51ab4019af |
> | [Klasik Ağ Katılımcısı](#classic-network-contributor) | Klasik ağları yönetmenize izin verir, ancak bunlara erişemez. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS bölgesi Katılımcısı](#dns-zone-contributor) | Azure DNS, DNS bölgelerini ve kayıt kümelerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Ağ Katılımcısı](#network-contributor) | Ağları yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. | 4d97b98b-1d4f-4787-A291-c67834d212e7 |
> | [Özel DNS bölge Katılımcısı](#private-dns-zone-contributor) | Özel DNS bölge kaynaklarını yönetmenizi sağlar, ancak bağlandıkları sanal ağları yönetemez. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Katkıda bulunan Traffic Manager](#traffic-manager-contributor) | Traffic Manager profillerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Depolama** |  |  |
> | [Avere Katılımcısı](#avere-contributor) | , Bir avere vFXT kümesi oluşturabilir ve yönetebilir. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere Işleci](#avere-operator) | Kümeyi yönetmek için avere vFXT kümesi tarafından kullanılır | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Yedek Katılımcısı](#backup-contributor) | Yedekleme hizmetini yönetmenizi sağlar, ancak kasaların oluşturamaz ve başkalarına erişim izni verebilir | 5e467623-bb1f-42F4-a55d-6e525e11384b |
> | [Yedekleme Operatörü](#backup-operator) | Yedekleme kaldırma, kasa oluşturma ve başkalarına erişim verme dışında yedekleme hizmetlerini yönetmenizi sağlar | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Yedekleme okuyucusu](#backup-reader) | Yedekleme hizmetlerini görüntüleyebilir, ancak değişiklik yapamaz | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klasik depolama hesabı Katılımcısı](#classic-storage-account-contributor) | Klasik depolama hesaplarını yönetmenize izin verir, ancak bunlara erişimi kalmaz. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klasik depolama hesabı anahtar operatörü hizmet rolü](#classic-storage-account-key-operator-service-role) | Klasik depolama hesabı anahtar Işleçleri klasik depolama hesaplarında anahtarları listeleme ve yeniden oluşturma izni verilir | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Katkıda bulunan Data Box](#data-box-contributor) | , Diğer kullanıcılara erişim izni hariç Data Box hizmeti altındaki her şeyi yönetmenizi sağlar. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box okuyucu](#data-box-reader) | Sipariş oluşturma veya düzenleme sırası ayrıntıları ve başkalarına erişim verme dışında Data Box hizmetini yönetmenizi sağlar. | 028f4ed7-e2a9-465E-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics geliştirici](#data-lake-analytics-developer) | Kendi işlerinizi göndermenize, izlemenize ve yönetmenize izin verir, ancak Data Lake Analytics Hesapları oluşturamaz veya silemezsiniz. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Okuyucu ve veri erişimi](#reader-and-data-access) | Her şeyi görüntülemenize izin verir, ancak bir depolama hesabını veya kapsanan kaynağı silmenize veya oluşturmanıza izin vermez. Ayrıca depolama hesabı anahtarlarına erişim aracılığıyla bir depolama hesabında bulunan tüm verilere okuma/yazma erişimi de sağlar. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Depolama Hesabı Katılımcısı](#storage-account-contributor) | Depolama hesaplarının yönetimine izin verir. Paylaşılan anahtar yetkilendirmesi aracılığıyla verilere erişmek için kullanılabilen hesap anahtarına erişim sağlar. | 17d1049b-9a84-46fb-8F53-869881c3d3ab |
> | [Depolama hesabı anahtar operatörü hizmet rolü](#storage-account-key-operator-service-role) | Depolama hesabı erişim anahtarlarının listelenmesi ve yeniden oluşturulmasına izin verir. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Depolama Blob Verileri Katkıda Bulunanı](#storage-blob-data-contributor) | Azure depolama kapsayıcıları ve bloblarını okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Depolama Blob Verileri Sahibi](#storage-blob-data-owner) | , POSIX erişim denetimi atama dahil olmak üzere Azure depolama blob kapsayıcılarına ve verilerine tam erişim sağlar. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Depolama Blob Verileri Okuyucusu](#storage-blob-data-reader) | Azure depolama kapsayıcıları ve bloblarını okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Depolama Blobu Delegator](#storage-blob-delegator) | Azure AD kimlik bilgileriyle imzalanan bir kapsayıcı veya blob için paylaşılan erişim imzası oluşturmak üzere kullanılabilen bir Kullanıcı temsili anahtarı alın. Daha fazla bilgi için bkz. [Kullanıcı TEMSILI SAS oluşturma](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Depolama Dosyası Verileri SMB Paylaşımı Katkıda Bulunanı](#storage-file-data-smb-share-contributor) | Azure dosya paylaşımlarında dosya/dizinlerde okuma, yazma ve silme erişimine izin verir. Bu rolün Windows dosya sunucularında yerleşik bir eşdeğeri yoktur. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Depolama Dosyası Verileri SMB Paylaşımı Yükseltilmiş Katkıda Bulunanı](#storage-file-data-smb-share-elevated-contributor) | Azure dosya paylaşımlarında dosya/dizinlerde okuma, yazma, silme ve değiştirme ACL 'Lerine izin verir. Bu rol, Windows dosya sunucularındaki değişikliğin bir dosya paylaşımının ACL 'sine eşdeğerdir. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Depolama Dosyası Verileri SMB Paylaşımı Okuyucusu](#storage-file-data-smb-share-reader) | Azure dosya paylaşımlarında dosya/dizinlerde okuma erişimine izin verir. Bu rol, Windows dosya sunucularında okunan bir dosya paylaşma ACL 'sine eşdeğerdir. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Depolama kuyruğu verileri Katılımcısı](#storage-queue-data-contributor) | Azure depolama kuyruklarını ve sıra iletilerini okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Depolama kuyruğu veri Iletisi Işlemcisi](#storage-queue-data-message-processor) | Azure depolama kuyruğundan bir iletiyi göz atın, alın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233bes |
> | [Depolama kuyruğu veri Iletisi gönderici](#storage-queue-data-message-sender) | Bir Azure depolama kuyruğuna ileti ekleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Depolama kuyruğu veri okuyucusu](#storage-queue-data-reader) | Azure depolama kuyruklarını ve sıra iletilerini okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4F77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure haritalar veri Katılımcısı](#azure-maps-data-contributor) | Azure haritalar hesabından ilgili verileri eşlemek için okuma, yazma ve silme erişimi verir. | 8f5e0ce6-4F7B-4dcf-bddf-e6f48634a204 |
> | [Azure haritalar veri okuyucu](#azure-maps-data-reader) | Azure haritalar hesabından ilgili harita okuma verilerine erişim izni verir. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure yay bulutu veri okuyucu](#azure-spring-cloud-data-reader) | Azure yay bulut verilerine okuma erişimine izin ver | b5537268-8956-4941-a8f0-646150406f0c |
> | [Katkıda bulunan Arama Hizmeti](#search-service-contributor) | Arama hizmetlerini yönetmenize izin verir, ancak bunlara erişim izni vermez. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey okuyucusu](#signalr-accesskey-reader) | SignalR hizmeti erişim anahtarlarını okuyun | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR App Server (Önizleme)](#signalr-app-server-preview) | Uygulama sunucunuzun SignalR hizmetine AAD kimlik doğrulama seçenekleriyle erişmesini sağlar. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR Katılımcısı](#signalr-contributor) | SignalR hizmeti kaynaklarını oluşturun, okuyun, güncelleştirin ve silin | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [SignalR sunucusuz katılımcısı (Önizleme)](#signalr-serverless-contributor-preview) | Uygulama erişim hizmetinize AAD kimlik doğrulama seçenekleriyle sunucusuz modda sahip olmanızı sağlar. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR hizmeti sahibi (Önizleme)](#signalr-service-owner-preview) | Azure SignalR hizmeti REST API 'Lerine tam erişim | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR hizmeti okuyucu (Önizleme)](#signalr-service-reader-preview) | Azure SignalR hizmeti REST API 'Lerine salt okuma erişimi | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Web planı Katılımcısı](#web-plan-contributor) | Web siteleri için Web planlarını yönetmenizi sağlar, ancak bunlara erişemez. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Web sitesi Katılımcısı](#website-contributor) | Web sitelerini yönetmenizi sağlar, ancak bunlara erişemez. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Kapsayıcılar** |  |  |
> | [AcrDelete](#acrdelete) | ACR silme | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [Acrimageimzalayan](#acrimagesigner) | ACR görüntü imzalayan | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR çekme | 7f951ddav-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR gönderimi | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [Acrquaranınereader](#acrquarantinereader) | ACR karantina veri okuyucusu | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR karantina veri yazıcısı | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes hizmet kümesi yönetici rolü](#azure-kubernetes-service-cluster-admin-role) | Küme Yöneticisi kimlik bilgisi eylemini listeleyin. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes hizmet kümesi Kullanıcı rolü](#azure-kubernetes-service-cluster-user-role) | Küme kullanıcı kimlik bilgilerini Listele eylemi. | 4abbcc35-E782-43d8-92C5-2d3f1bd2253f |
> | [Azure Kubernetes hizmet katılımcısı rolü](#azure-kubernetes-service-contributor-role) | Azure Kubernetes hizmet kümelerini okuma ve yazma erişimi verir | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes hizmeti RBAC Yöneticisi](#azure-kubernetes-service-rbac-admin) | Kaynak kotalarını ve ad alanlarını güncelleştirme veya silme dışında, küme/ad alanı altındaki tüm kaynakları yönetmenizi sağlar. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes hizmeti RBAC kümesi Yöneticisi](#azure-kubernetes-service-rbac-cluster-admin) | Kümedeki tüm kaynakları yönetmenizi sağlar. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes hizmeti RBAC okuyucusu](#azure-kubernetes-service-rbac-reader) | Bir ad alanındaki birçok nesneyi görmek için salt okuma erişimine izin verir. Rollerin veya rol bağlamalarının görüntülenmesine izin vermez. Bu rol, gizli dizi içeriğini okumak için ad alanındaki ServiceAccount kimlik bilgilerine erişim sağladığından ad alanındaki (ayrıcalık yükseltme bir biçimde) API erişimine izin verecek şekilde gizli dizileri görüntülemeye izin vermez. Bu rolün küme kapsamına uygulanması, tüm ad alanları üzerinde erişim sağlar. | 7f6c6a51-bcf8-42BA-9220-52d62157d7db |
> | [Azure Kubernetes hizmeti RBAC yazıcı](#azure-kubernetes-service-rbac-writer) | Bir ad alanındaki nesnelerin çoğuna okuma/yazma erişimi sağlar. Bu rol, rolleri veya rol bağlamalarını görüntülemeye veya değiştirmeye izin vermiyor. Ancak, bu rol, ad alanındaki herhangi bir ServiceAccount 'nin API erişim düzeylerini kazanmak için kullanılabilir, bu nedenle gizli dizi ve Dizin çalıştırmaya erişim sağlar. Bu rolün küme kapsamına uygulanması, tüm ad alanları üzerinde erişim sağlar. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Veritabanları** |  |  |
> | [Cosmos DB hesabı okuyucu rolü](#cosmos-db-account-reader-role) | Azure Cosmos DB hesabı verilerini okuyabilir. Azure Cosmos DB hesaplarını yönetmek için [DocumentDB hesabı katılımcısı](#documentdb-account-contributor) konusuna bakın. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB Işleci](#cosmos-db-operator) | Azure Cosmos DB hesaplarını yönetmenizi sağlar ancak içerdikleri verilere erişemez. Hesap anahtarlarına ve bağlantı dizelerine erişimi engeller. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Bir Cosmos DB veritabanı veya bir hesabın kapsayıcısı için geri yükleme isteği gönderebilir | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Sürekli yedekleme moduyla Cosmos DB veritabanı hesabı için geri yükleme eylemi gerçekleştirebilir | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [DocumentDB hesabı Katılımcısı](#documentdb-account-contributor) | , Azure Cosmos DB hesaplarını yönetebilir. Azure Cosmos DB daha önce DocumentDB olarak bilinirdi. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Katkıda bulunan Redis Cache](#redis-cache-contributor) | Redsıs önbellekler yönetmenize izin verir, ancak bunlara erişimi olmaz. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB Katılımcısı](#sql-db-contributor) | SQL veritabanlarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. Ayrıca, güvenlikle ilgili ilkeleri veya bunların üst SQL sunucularını yönetemezsiniz. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL yönetilen örnek Katılımcısı](#sql-managed-instance-contributor) | SQL yönetilen örnekleri ve gerekli ağ yapılandırmasını yönetmenizi sağlar, ancak başkalarına erişim izni veremeyiz. | 4939a1f6-9ae0-4E48-A1E0-f2cbe897382d |
> | [SQL Güvenlik Yöneticisi](#sql-security-manager) | , SQL Server ve veritabanlarının güvenlikle ilgili ilkelerini yönetmenizi sağlar, ancak bunlara erişemez. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Katkıda bulunan SQL Server](#sql-server-contributor) | SQL Server ve veritabanlarını yönetmenizi sağlar, ancak bunlara yönelik erişimleri ve güvenlikle ilgili ilkeleri yönetemez. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analiz** |  |  |
> | [Azure Event Hubs veri sahibi](#azure-event-hubs-data-owner) | Azure Event Hubs kaynaklarına tam erişim sağlar. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs veri alıcısı](#azure-event-hubs-data-receiver) | Azure Event Hubs kaynaklarına erişim izni verir. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs veri gönderici](#azure-event-hubs-data-sender) | Azure Event Hubs kaynaklarına erişim gönderilmesine izin verir. | 2b629674-e913-4C01-ae53-ef4638d8f975 |
> | [Katkıda bulunan Data Factory](#data-factory-contributor) | Veri fabrikalarının yanı sıra bunların içindeki alt kaynakları oluşturun ve yönetin. | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | [Veri Takiger](#data-purger) | Log Analytics çalışma alanından özel verileri silin. | 150f5e0c-0603-4f03-8C7F-cf70034c4e90 |
> | [HDInsight küme operatörü](#hdinsight-cluster-operator) | HDInsight küme yapılandırmasını okuyup değiştirmenize izin verir. | 61ed4efc-fab3-44fd-B111-e24485cc132a |
> | [HDInsight etki alanı Hizmetleri Katılımcısı](#hdinsight-domain-services-contributor) | HDInsight için gereken etki alanı Hizmetleri ile ilgili işlemleri okuyabilir, oluşturabilir, değiştirebilir ve silebilir Kurumsal Güvenlik Paketi | 8d8d5a11-05d3-4bdav-A417-a08778121c7c |
> | [Log Analytics Katkıda Bulunan](#log-analytics-contributor) | Log Analytics katkı, tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. İzleme ayarlarını düzenlediğinizde VM 'lere VM uzantısının eklenmesi dahildir; Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okuma; Otomasyon hesapları oluşturma ve yapılandırma; çözümler ekleme; ve Azure tanılama 'yı tüm Azure kaynaklarında yapılandırma. | 92aaf0dad-9dadb-42b6-94a3-d43ce8d16293 |
> | [Log Analytics Okuyucusu](#log-analytics-reader) | Log Analytics okuyucu tüm izleme verilerini görüntüleyip arayabilir ve tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntüleme dahil olmak üzere izleme ayarlarını görüntüleyebilir. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Veri Seçisini takip etme](#purview-data-curator) | Microsoft. purview verileri Curator, Katalog veri nesnelerini oluşturabilir, okuyabilir, değiştirebilir ve silebilir ve nesneler arasında ilişkiler oluşturabilir. Bu rol önizleme aşamasındadır ve değiştirilebilir. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Purview veri okuyucu](#purview-data-reader) | Microsoft. purview veri okuyucu, Katalog veri nesnelerini okuyabilir. Bu rol önizleme aşamasındadır ve değiştirilebilir. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Takip görünümü veri kaynağı Yöneticisi](#purview-data-source-administrator) | Microsoft. purview veri kaynağı Yöneticisi veri kaynaklarını ve veri taramalarını yönetebilir. Bu rol önizleme aşamasındadır ve değiştirilebilir. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Şema kayıt defterine katkıda bulunan (Önizleme)](#schema-registry-contributor-preview) | Şema kayıt defteri gruplarını ve şemalarını okuyun, yazın ve silin. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Şema kayıt defteri okuyucusu (Önizleme)](#schema-registry-reader-preview) | Şema kayıt defteri gruplarını ve şemaları okuyun ve listeleyin. | 2c56ea50-C6B3-40a6-83c0-9d98858bc7d2 |
> | **Blok Zinciri** |  |  |
> | [Blok zinciri üye düğümü erişimi (Önizleme)](#blockchain-member-node-access-preview) | Blok zinciri üye düğümlerine erişim sağlar | 31a002a1-acaf-453E-8a5b-297c9ca1ea24 |
> | **AI + makine öğrenimi** |  |  |
> | [Bilişsel hizmetler Katılımcısı](#cognitive-services-contributor) | Bilişsel hizmetler için anahtar oluşturma, okuma, güncelleştirme, silme ve yönetme olanağı sağlar. | 25fbc0a9-bd7c-42A3-aa1a-3b75d497ee68 |
> | [Bilişsel Hizmetler Özel Görüntü İşleme Katılımcısı](#cognitive-services-custom-vision-contributor) | Projeleri görüntüleme, oluşturma, düzenleme veya silme özelliği de dahil olmak üzere projeye tam erişim. | c1ff6cc2-C111-46fe-8896-e0ef812ad9f3 |
> | [Bilişsel Hizmetler Özel Görüntü İşleme dağıtımı](#cognitive-services-custom-vision-deployment) | Modelleri yayımlayın, yayımdan kaldırın veya dışarı aktarın. Dağıtım projeyi görüntüleyebilir, ancak güncelleştiremez. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Bilişsel Hizmetler Özel Görüntü İşleme etiketleyici](#cognitive-services-custom-vision-labeler) | Eğitim görüntülerini görüntüleyin, düzenleyin, görüntü etiketlerini oluşturun, ekleyin, kaldırın veya silin. Etiketleyiciler projeyi görüntüleyebilir, ancak eğitim görüntüleri ve Etiketler dışında herhangi bir şeyi güncelleştiremez. | 88424f51-ebe7-446F-BC41-7fa16989e96c |
> | [Bilişsel Hizmetler Özel Görüntü İşleme okuyucu](#cognitive-services-custom-vision-reader) | Projedeki salt okuma eylemleri. Okuyucular projeyi oluşturamaz veya güncelleştiremez. | 93586559-C37D-4a6b-BA08-b9f0940c2d73 |
> | [Bilişsel Hizmetler Özel Görüntü İşleme Trainer](#cognitive-services-custom-vision-trainer) | Modelleri yayımlama, yayımdan kaldırma, dışarı aktarma özelliği de dahil olmak üzere projeleri görüntüleyin, düzenleyin ve modelleri eğitme. Traıners proje oluşturamaz veya silemez. | 0a5ae4ab-0d65-4EEB-be61-29fc9b54394b |
> | [Bilişsel hizmetler veri okuyucu (Önizleme)](#cognitive-services-data-reader-preview) | Bilişsel hizmetler verilerini okumanızı sağlar. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Bilişsel hizmetler ölçüm Danışmanı Yöneticisi](#cognitive-services-metrics-advisor-administrator) | Sistem düzeyi yapılandırması da dahil olmak üzere, projeye tam erişim. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Bilişsel hizmetler Soru-Cevap Oluşturma Düzenleyicisi](#cognitive-services-qna-maker-editor) | KB oluşturma, düzenleme, içeri aktarma ve dışarı aktarma işlemi yapmanızı sağlar. Bir KB yayımlayamaz veya silemezsiniz. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Bilişsel hizmetler Soru-Cevap Oluşturma okuyucu](#cognitive-services-qna-maker-reader) | Yalnızca bir KB 'yi okuyup test edelim. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Bilişsel hizmetler kullanıcısı](#cognitive-services-user) | Bilişsel hizmetler 'in anahtarlarını okuyup listelemenizi sağlar. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Nesnelerin İnterneti** |  |  |
> | [Cihaz Güncelleştirme Yöneticisi](#device-update-administrator) | , Yönetim ve içerik işlemlerine tam erişim sağlar | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Cihaz güncelleştirme Içerik Yöneticisi](#device-update-content-administrator) | , İçerik işlemlerine tam erişim sağlar | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Cihaz güncelleştirme Içeriği okuyucusu](#device-update-content-reader) | İçerik işlemlerine yönelik okuma erişimi sağlar, ancak değişiklik yapmaya izin vermez | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Cihaz güncelleştirme dağıtımları Yöneticisi](#device-update-deployments-administrator) | Yönetim işlemlerine tam erişim sağlar | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Cihaz güncelleştirme dağıtımları okuyucu](#device-update-deployments-reader) | Yönetim işlemlerine yönelik okuma erişimi sağlar, ancak değişiklik yapmaya izin vermez | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Cihaz güncelleştirme okuyucusu](#device-update-reader) | Yönetim ve içerik işlemlerine yönelik okuma erişimi sağlar, ancak değişiklik yapmaya izin vermez | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Karma Gerçeklik** |  |  |
> | [Uzaktan Işleme Yöneticisi](#remote-rendering-administrator) | Azure uzaktan Işleme için kullanıcıya dönüştürme, oturum yönetme, işleme ve Tanılama özelliklerini sağlar | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Uzaktan Işleme Istemcisi](#remote-rendering-client) | Azure uzaktan Işleme için Kullanıcı yönetme, işleme ve tanılama özellikleri sağlar. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Uzamsal bağlayıcı hesabı Katılımcısı](#spatial-anchors-account-contributor) | Hesabınızdaki uzamsal bağlantıları yönetmenizi sağlar, ancak onları silmez | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Uzamsal bağlayıcı hesap sahibi](#spatial-anchors-account-owner) | Hesabınızdaki uzamsal bağlayıcıları, silme dahil olmak üzere yönetmenizi sağlar | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Uzamsal bağlayıcı hesap okuyucu](#spatial-anchors-account-reader) | Hesabınızdaki uzamsal Tutturucuların özelliklerini bulmanıza ve okumanızı sağlar | 5d51204f-EB77-4B1C-b86a-2ec626c49413 |
> | **Tümleştirme** |  |  |
> | [API Management hizmet Katılımcısı](#api-management-service-contributor) | Hizmeti ve API 'Leri yönetebilir | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Hizmet Işletmeni rolü API Management](#api-management-service-operator-role) | Hizmeti yönetebilir, ancak API 'Leri yönetemez | e022efe7-F5BA-4159-bbe4-b44f577e9b61 |
> | [API Management hizmeti okuyucu rolü](#api-management-service-reader-role) | Hizmet ve API 'lere salt okuma erişimi | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Uygulama yapılandırma veri sahibi](#app-configuration-data-owner) | Uygulama yapılandırma verilerine tam erişim sağlar. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Uygulama yapılandırma veri okuyucusu](#app-configuration-data-reader) | Uygulama yapılandırma verilerine okuma erişimi sağlar. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus veri sahibi](#azure-service-bus-data-owner) | Azure Service Bus kaynaklara tam erişim sağlar. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus veri alıcısı](#azure-service-bus-data-receiver) | Azure Service Bus kaynaklarına erişim izni verir. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Veri Göndericisini Azure Service Bus](#azure-service-bus-data-sender) | Azure Service Bus kaynaklarına erişim izni verir. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Kayıt sahibini Azure Stack](#azure-stack-registration-owner) | Azure Stack kayıtlarını yönetmenizi sağlar. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid Katılımcısı](#eventgrid-contributor) | EventGrid işlemlerini yönetmenizi sağlar. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [EventGrid EventSubscription Katılımcısı](#eventgrid-eventsubscription-contributor) | EventGrid olay aboneliği işlemlerini yönetmenizi sağlar. | 428e0ff0-5e57-4d9c-A221-2c70d0e0a443 |
> | [EventGrid EventSubscription okuyucusu](#eventgrid-eventsubscription-reader) | EventGrid olay aboneliklerini okumanızı sağlar. | 2414bbcf-6497-4FAF-8c65-045460748405 |
> | [FHıR verileri Katılımcısı](#fhir-data-contributor) | Rol, Kullanıcı veya sorumlunun FHıR verilerine tam erişimini sağlar | 5a1fc7df-4BF1-4951-a576-89034ee01acd |
> | [FHıR veri aktarıcı](#fhir-data-exporter) | Rol, kullanıcının veya sorumlunun FHıR verilerini okumasına ve dışa almasına izin verir | 3db33094-8700-4567-8dad5-1501d4e7e843 |
> | [FHıR veri okuyucu](#fhir-data-reader) | Rol, kullanıcının veya sorumlunun FHıR verilerini okumasına izin verir | 4c8d0bbc-75d3-4935-991F-5f3c56d81508 |
> | [FHıR veri yazıcısı](#fhir-data-writer) | Rol, kullanıcının veya sorumlunun FHıR verilerini okumasına ve yazmasına izin verir | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Katkıda bulunan Tümleştirme Hizmeti Ortamı](#integration-service-environment-contributor) | Tümleştirme hizmeti ortamlarını yönetmenize izin verir, ancak bunlara erişimi kalmaz. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Tümleştirme Hizmeti Ortamı geliştirici](#integration-service-environment-developer) | Geliştiricilerin, tümleştirme hizmeti ortamlarında iş akışları, tümleştirme hesapları ve API bağlantıları oluşturmalarına ve güncelleştirmesine izin verir. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligent Systems hesabı Katılımcısı](#intelligent-systems-account-contributor) | Akıllı sistem hesaplarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Mantıksal uygulama Katılımcısı](#logic-app-contributor) | Mantıksal uygulamaları yönetmenize izin verir, ancak bunlara erişimi değiştirmeyin. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic App Işleci](#logic-app-operator) | Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremez. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Kimlik** |  |  |
> | [Yönetilen kimlik Katılımcısı](#managed-identity-contributor) | Kullanıcı tarafından atanan kimlik oluşturma, okuma, güncelleştirme ve silme | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Yönetilen kimlik Işleci](#managed-identity-operator) | Kullanıcı tarafından atanan kimliği okuma ve atama | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Güvenlik** |  |  |
> | [Kanıtlama Katılımcısı](#attestation-contributor) | Kanıtlama sağlayıcısı örneğini okuyabilir veya silebilir | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Kanıtlama okuyucusu](#attestation-reader) | Kanıtlama sağlayıcısı özelliklerini okuyabilir | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Otomasyon Katılımcısı](#azure-sentinel-automation-contributor) | Azure Sentinel Otomasyon Katılımcısı | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Azure Sentinel Katkıda Bulunanı](#azure-sentinel-contributor) | Azure Sentinel Katkıda Bulunanı | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Okuyucusu](#azure-sentinel-reader) | Azure Sentinel Okuyucusu | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Yanıtlayıcısı](#azure-sentinel-responder) | Azure Sentinel Yanıtlayıcısı | 3e150937-b8fe-4CFB-8069-0eaf05ecd056 |
> | [Key Vault Yöneticisi](#key-vault-administrator) | Tüm veri düzlemi işlemlerini bir anahtar kasasında ve içindeki tüm nesneleri (sertifikalar, anahtarlar ve gizli diziler dahil) gerçekleştirin. Anahtar Kasası kaynakları yönetemez veya rol atamaları yönetilemez. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault sertifikaları müdürü](#key-vault-certificates-officer) | İzinleri Yönet dışında bir anahtar kasasının sertifikaları üzerinde herhangi bir işlem gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Katkıda bulunan Key Vault](#key-vault-contributor) | Anahtar kasalarını yönetin, ancak Azure RBAC 'de roller atamanıza izin vermez ve gizli dizi, anahtar veya sertifikalara erişmenize izin vermez. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault şifre müdürü](#key-vault-crypto-officer) | Anahtar kasasının anahtarları üzerinde, izinleri yönet dışında herhangi bir işlem gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | 14b46e9e-c2b7-41B4-b07b-48a6ebf60603 |
> | [Key Vault şifreleme hizmeti şifreleme kullanıcısı](#key-vault-crypto-service-encryption-user) | Anahtarların meta verilerini okuyun ve sarmalama/sarmalama işlemleri gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault şifrelenmiş Kullanıcı](#key-vault-crypto-user) | Anahtarları kullanarak şifreleme işlemleri gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | 12338af0-0e69-4776-kir7-57ae8d297424 |
> | [Key Vault okuyucu](#key-vault-reader) | Anahtar kasalarının ve sertifika, anahtar ve gizli dizileri için meta verileri okuyun. Gizli içerik veya anahtar malzeme gibi hassas değerler okunamaz. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | 21090545-7CA7-4776-B22C-e363652d74d2 |
> | [Key Vault gizlilikler müdürü](#key-vault-secrets-officer) | Anahtar kasasının gizli dizileri üzerinde, izinleri yönet dışında herhangi bir işlem gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault gizli dizi kullanıcısı](#key-vault-secrets-user) | Gizli dizi içeriğini okuyun. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir. | 4633458b-17de-408A-b874-0445c86b69e6 |
> | [Yönetilen HSM Katılımcısı](#managed-hsm-contributor) | Yönetilen HSM havuzlarını yönetmenize izin verir, ancak bunlara erişimi olmaz. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Güvenlik Yöneticisi](#security-admin) | Güvenlik Merkezi için izinleri görüntüleyin ve güncelleştirin. Güvenlik okuyucu rolüyle aynı izinler ve ayrıca güvenlik ilkesini güncelleştirebilir ve uyarıları ve önerileri kapatabilir. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Güvenlik değerlendirmesi Katılımcısı](#security-assessment-contributor) | Değerlendirmelere Güvenlik Merkezi 'ne gönderim olanağı sağlar | 612c2aa1-CB24-443B-ac28-3ab7272de6f5 |
> | [Güvenlik Yöneticisi (eski)](#security-manager-legacy) | Bu eski bir roldür. Lütfen bunun yerine Güvenlik Yöneticisi 'ni kullanın. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Güvenlik Okuyucusu](#security-reader) | Güvenlik Merkezi için izinleri görüntüleyin. Önerileri, uyarıları, güvenlik ilkesini ve güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs kullanıcısı](#devtest-labs-user) | Azure DevTest Labs sanal makinelerinizi bağlamanıza, başlatmanıza, yeniden başlatmanıza ve kapatımanıza olanak sağlar. | 76283e04-6283-4c54-8F91-bcf1374a3c64 |
> | [Laboratuvar Oluşturucu](#lab-creator) | Azure Laboratuvar hesaplarınız altında yeni laboratuvarlar oluşturmanızı sağlar. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **İzleme** |  |  |
> | [Application Insights bileşeni Katılımcısı](#application-insights-component-contributor) | Application Insights bileşenlerini yönetebilir | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights Snapshot Debugger ile toplanan hata ayıklama anlık görüntülerini görüntülemek ve indirmek için kullanıcıya izin verir. Bu izinlerin [sahip](#owner) veya [katkıda](#contributor) bulunan rollerine dahil edilmediğini unutmayın. Kullanıcılara Application Insights Snapshot Debugger rolü verirken, rolü doğrudan kullanıcıya vermeniz gerekir. Rol, özel bir role eklendiğinde tanınmaz. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Katkıda bulunan izleniyor](#monitoring-contributor) | Tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Ölçüm yayımcısını izleme](#monitoring-metrics-publisher) | Azure kaynaklarında ölçüm yayımlamaya izin vermez | 3913510d-42F4-4E42-8A64-420c390055eb |
> | [İzleme okuyucusu](#monitoring-reader) | Tüm izleme verilerini okuyabilir (ölçümler, Günlükler vb.). Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Çalışma kitabı Katılımcısı](#workbook-contributor) | , Paylaşılan çalışma kitaplarını kaydedebilir. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Çalışma kitabı okuyucusu](#workbook-reader) | , Çalışma kitaplarını okuyabilir. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Yönetim + idare** |  |  |
> | [Automation Iş Işleci](#automation-job-operator) | Otomasyon Runbook 'Larını kullanarak Iş oluşturun ve yönetin. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation Işleci](#automation-operator) | Automation Işleçleri, işleri başlatabilir, durdurabilir, askıya alabilir ve sürdürebilir | d3881f73-407A-4167-8283-e981cbba0404 |
> | [Otomasyon Runbook Işleci](#automation-runbook-operator) | Runbook 'un Işlerini oluşturabilmek için Runbook özelliklerini okuyun. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Arc etkin Kubernetes kümesi Kullanıcı rolü](#azure-arc-enabled-kubernetes-cluster-user-role) | Küme kullanıcı kimlik bilgilerini Listele eylemi. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc Kubernetes Yöneticisi](#azure-arc-kubernetes-admin) | Kaynak kotalarını ve ad alanlarını güncelleştirme veya silme dışında, küme/ad alanı altındaki tüm kaynakları yönetmenizi sağlar. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc Kubernetes Küme Yöneticisi](#azure-arc-kubernetes-cluster-admin) | Kümedeki tüm kaynakları yönetmenizi sağlar. | 8393591c-06b9-48A2-A542-1bd6b377f6a2 |
> | [Azure Arc Kubernetes Görüntüleyici](#azure-arc-kubernetes-viewer) | Gizli dizileri hariç küme/ad alanındaki tüm kaynakları görüntülemenize izin verir. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes yazıcı](#azure-arc-kubernetes-writer) | Küme/ad alanı (küme) rolleri ve (küme) rolü bağlamaları dışındaki her şeyi güncelleştirmenizi sağlar. | 5b999177-9696-4545-85C7-50de3797e5a1 |
> | [Azure bağlı makine ekleme](#azure-connected-machine-onboarding) | , Azure bağlantılı makineler ekleyebilir. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure bağlı makine kaynak yöneticisi](#azure-connected-machine-resource-administrator) | Azure bağlı makinelerini okuyabilir, yazabilir, silebilir ve yeniden ekleyebilir. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Faturalama Okuyucusu](#billing-reader) | Faturalandırma verilerine okuma erişimi sağlar | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Blueprint Katılımcısı](#blueprint-contributor) | Şema tanımlarını yönetebilir, ancak atamazsınız. | 41077137-e803-4205-871C-5a86e6a753b4 |
> | [Blueprint Işleci](#blueprint-operator) | Varolan yayımlanmış şemaları atayabilir, ancak yeni şemaları oluşturamaz. Bu, yalnızca atama Kullanıcı tarafından atanan yönetilen bir kimlikle yapıldığında işe yarar. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Maliyet Yönetimi Katkıda Bulunanı](#cost-management-contributor) | Maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir (örn. bütçeler, dışarı aktarmalar) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Maliyet yönetimi okuyucusu](#cost-management-reader) | Maliyet verilerini ve yapılandırmayı görüntüleyebilir (örneğin, bütçeler, dışarı aktarmalar) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Hiyerarşi Ayarları Yöneticisi](#hierarchy-settings-administrator) | Kullanıcıların hiyerarşi ayarlarını düzenlemesine ve silmesine izin verir | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes kümesi-Azure yay ekleme](#kubernetes-cluster---azure-arc-onboarding) | Herhangi bir kullanıcıya/hizmete Connectedkümeler kaynağı oluşturma yetkisi vermek için rol tanımı | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Yönetilen uygulama katılımcısı rolü](#managed-application-contributor-role) | Yönetilen uygulama kaynakları oluşturulmasına izin verir. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Yönetilen uygulama Işletmeni rolü](#managed-application-operator-role) | Yönetilen uygulama kaynakları üzerinde işlemleri okuyup gerçekleştirmenize olanak tanır | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Yönetilen uygulamalar okuyucusu](#managed-applications-reader) | Yönetilen bir uygulamadaki kaynakları okumanızı ve JıT erişimi isteğinizi yapmanızı sağlar. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Yönetilen hizmetler kayıt ataması rol silme](#managed-services-registration-assignment-delete-role) | Yönetilen hizmetler kayıt ataması silme rolü, kiracı kullanıcılarının kiracıya atanan kayıt atamasını silmesine izin verir. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Yönetim grubu Katılımcısı](#management-group-contributor) | Yönetim grubu katılımcısı rolü | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Yönetim grubu okuyucusu](#management-group-reader) | Yönetim grubu okuyucusu rolü | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Yeni relik APM hesabı Katılımcısı](#new-relic-apm-account-contributor) | New Relic Application Performance Management hesaplarını ve uygulamaları yönetmenize izin verir, ancak bunlara erişimi kalmaz. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [İlke öngörüleri veri yazıcısı (Önizleme)](#policy-insights-data-writer-preview) | Kaynak ilkelerine okuma erişimine ve kaynak bileşen ilkesi olaylarına yazma erişimine izin verir. | 66bb4e9e-B016-4A94-8249-4c0511c2be84 |
> | [Kota Isteği operatörü](#quota-request-operator) | Kota isteklerini okuyup oluşturun, kota isteği durumunu alın ve destek bileti oluşturun. | 0e5f05e5-9AB9-446B-b98d-1e2157c94125 |
> | [Rezervasyon Satınalmacı](#reservation-purchaser) | Ayırmaları satın almanızı sağlar | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Kaynak İlkesine Katkıda Bulunan](#resource-policy-contributor) | Kaynak ilkesi oluşturma/değiştirme, destek bileti oluşturma ve kaynakları/hiyerarşisi okuma haklarına sahip kullanıcılar. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery Katkıda Bulunanı](#site-recovery-contributor) | Kasa oluşturma ve rol atama dışında Site Recovery hizmetini yönetmenizi sağlar | 6670b86e-a3f7-4917-AC9B-5d6ab1be4567 |
> | [Site Recovery Operatörü](#site-recovery-operator) | Yük devretme ve yeniden çalışma ve diğer Site Recovery yönetim işlemlerini gerçekleştirmenize izin verir | 494ae006-DB33-4328-BF46-533a6560a3ca |
> | [Site Recovery Okuyucusu](#site-recovery-reader) | Site Recovery durumunu görüntülemenize izin verir, ancak diğer yönetim işlemlerini gerçekleştirmenize izin vermez | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Destek Isteği Katılımcısı](#support-request-contributor) | Destek istekleri oluşturmanızı ve yönetmenizi sağlar | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Etiket Katılımcısı](#tag-contributor) | Varlıklarda kendilerine erişim sağlamamanıza gerek kalmadan varlıklarda etiketleri yönetmenizi sağlar. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Diğer** |  |  |
> | [Azure dijital TWINS veri sahibi](#azure-digital-twins-data-owner) | Dijital TWINS veri düzlemi için tam erişim rolü | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure dijital TWINS veri okuyucusu](#azure-digital-twins-data-reader) | Dijital TWINS veri düzlemi özellikleri için salt okunurdur rol | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk Katılımcısı](#biztalk-contributor) | BizTalk hizmetlerini yönetmenizi sağlar ancak onlara erişim izni vermez. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Masaüstü Sanallaştırma uygulama grubu Katılımcısı](#desktop-virtualization-application-group-contributor) | Masaüstü Sanallaştırma uygulama grubunun katılımcısı. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Masaüstü Sanallaştırma uygulama grubu okuyucusu](#desktop-virtualization-application-group-reader) | Masaüstü Sanallaştırma uygulama grubunun okuyucusu. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Masaüstü Sanallaştırma Katılımcısı](#desktop-virtualization-contributor) | Masaüstü Sanallaştırma katılımcısı. | 082f0a83-3be5-4BA1-904c-961cca79b387 |
> | [Masaüstü Sanallaştırma Ana bilgisayar havuzu Katılımcısı](#desktop-virtualization-host-pool-contributor) | Masaüstü Sanallaştırma Ana bilgisayar havuzunun katılımcısı. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Masaüstü Sanallaştırma Ana bilgisayar havuzu okuyucusu](#desktop-virtualization-host-pool-reader) | Masaüstü Sanallaştırma Ana bilgisayar havuzunun okuyucusu. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Masaüstü Sanallaştırma okuyucusu](#desktop-virtualization-reader) | Masaüstü Sanallaştırma okuyucusu. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Masaüstü Sanallaştırma oturumu ana bilgisayar Işleci](#desktop-virtualization-session-host-operator) | Masaüstü Sanallaştırma oturumu ana bilgisayarının işleci. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Masaüstü Sanallaştırma kullanıcısı](#desktop-virtualization-user) | Kullanıcının uygulama grubundaki uygulamaları kullanmasına izin verir. | 1d18fff3-a72a-46b5-B4A9-0b38a3cd7e63 |
> | [Masaüstü Sanallaştırma Kullanıcı oturumu Işleci](#desktop-virtualization-user-session-operator) | Masaüstü Sanallaştırma uesr oturumunun işleci. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Masaüstü Sanallaştırma çalışma alanı Katılımcısı](#desktop-virtualization-workspace-contributor) | Masaüstü Sanallaştırma çalışma alanının katılımcısı. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Masaüstü Sanallaştırma çalışma alanı okuyucusu](#desktop-virtualization-workspace-reader) | Masaüstü Sanallaştırma çalışma alanının okuyucusu. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Disk yedekleme okuyucusu](#disk-backup-reader) | Disk yedeklemesi gerçekleştirmek için Yedekleme Kasası için izin sağlar. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Disk geri yükleme Işleci](#disk-restore-operator) | Disk geri yükleme işlemini gerçekleştirmek için Yedekleme Kasası için izin sağlar. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Disk anlık görüntüsü Katılımcısı](#disk-snapshot-contributor) | Disk anlık görüntülerini yönetmek için Yedekleme Kasası için izin sağlar. | 7efff54f-A5B4-42b5-a1c5-5411624893ce |
> | [Zamanlayıcı Iş koleksiyonları Katılımcısı](#scheduler-job-collections-contributor) | Zamanlayıcı iş koleksiyonlarını yönetmenizi sağlar, ancak bunlara erişimi kalmaz. | 188a0f2f-5c9e-469B-ae67-2aa5ce574b94 |
> | [Hizmetler hub 'ı Işleci](#services-hub-operator) | Hizmet hub 'ı operatörü, Service hub bağlayıcılarıyla ilgili tüm okuma, yazma ve silme işlemlerini gerçekleştirmenize olanak tanır. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Genel


### <a name="contributor"></a>Katılımcı

Tüm kaynakları yönetmek için tam erişim verir, ancak Azure RBAC 'de roller atamanıza, Azure 'da atamaları yönetmenize veya görüntü galerileri paylaşmanıza izin vermez. [Daha fazla bilgi edinin](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | * | Tüm türlerin kaynaklarını oluşturma ve yönetme |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Rolleri, ilke atamalarını, ilke tanımlarını ve ilke kümesi tanımlarını Sil |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Roller, rol atamaları, ilke atamaları, ilke tanımları ve ilke kümesi tanımları oluşturun |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/yükseltir Teaccess/Action | Çağrı yapana kiracı kapsamında Kullanıcı Erişimi Yöneticisi erişim izni verir |
> | [Microsoft. BLUEPRINT](resource-provider-operations.md#microsoftblueprint)/Blueprintassignments/Write | Şema atamalarını oluşturun veya güncelleştirin |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/Blueprintassignments/Delete | Tüm şema atamalarını silme |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Galleries/Share/Action | Bir galeriyi farklı kapsamlarla paylaşır |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Sahip

Azure RBAC 'de rol atama özelliği de dahil olmak üzere tüm kaynakları yönetmek için tam erişim verir. [Daha fazla bilgi edinin](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | * | Tüm türlerin kaynaklarını oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Okuyucu

Tüm kaynakları görüntüleyin, ancak herhangi bir değişiklik yapmanıza izin vermez. [Daha fazla bilgi edinin](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Kullanıcı Erişimi Yöneticisi

Azure kaynaklarına Kullanıcı erişimini yönetmenizi sağlar. [Daha fazla bilgi edinin](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/* | Yetkilendirmeyi Yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>İşlem


### <a name="classic-virtual-machine-contributor"></a>Klasik sanal makine Katılımcısı

Klasik sanal makineleri yönetmenizi sağlar ancak bunlara yönelik erişimi, bağlı oldukları sanal ağ veya depolama hesabı için değil.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/DomainNames/* | Klasik işlem etki alanı adları oluşturma ve yönetme |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/Virtual, ines/* | Sanal makine oluşturma ve yönetme |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/Networksecuritygroups/Join/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/Rezervedips/link/Action | Ayrılmış bir IP 'yi bağlama |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/Rezervedips/Read | Ayrılmış IP 'Leri alır |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/Virtualnetworks/Join/Action | Sanal ağı birleştirir. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/Virtualnetworks/Read | Sanal ağı alın. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/Disks/Read | Depolama hesabı diskini döndürür. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/images/Read | Depolama hesabı görüntüsünü döndürür. Kullanım dışı. ' Microsoft. ClassicStorage/storageAccounts/Vmımages ' kullanın) |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/ListKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/Read | Verilen hesaba sahip depolama hesabını döndürün. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Sanal Makine Yöneticisi oturum açma

Portalda sanal makineleri görüntüleyin ve yönetici olarak oturum açın [daha fazla bilgi edinin](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/Read | Genel IP adresi tanımını alır. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/Read | Yük dengeleyici tanımını alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, ines/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/Login/Action | Bir sanal makinede normal kullanıcı olarak oturum açın |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtualırines/loginasadmin/Action | Windows yönetici veya Linux kök kullanıcı ayrıcalıklarına sahip bir sanal makinede oturum açma |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Sanal Makine Katılımcısı

Sanal makineleri yönetmenize izin verir, ancak bunlara bağlı oldukları sanal ağ veya depolama hesabına erişemez. [Daha fazla bilgi edinin](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/kullanılabilirliği Bilitysets/* | İşlem kullanılabilirlik kümeleri oluşturma ve yönetme |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Locations/* | İşlem konumları oluşturma ve yönetme |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, ines/* | Sanal makineler oluşturma, güncelleştirme, silme, başlatma, yeniden başlatma ve kapatma dahil olmak üzere tüm sanal makine eylemlerini gerçekleştirin. Sanal makinelerde önceden tanımlanmış betikleri yürütün. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Sanal makine ölçek kümeleri oluşturma ve yönetme |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/Write | Yeni bir disk oluşturur veya var olan bir diski güncelleştirir |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/Read | Bir diskin özelliklerini al |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/Delete | Diski siler |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Applicationgateways/backendadddresspools/Join/Action | Bir uygulama ağ geçidi arka uç adres havuzunu birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/backendadddresspools/Join/Action | Yük dengeleyici arka uç adres havuzunu birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/ınboundnatpools/Join/Action | Yük dengeleyici gelen NAT havuzunu birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/ınboundnatrules/Join/Action | Yük dengeleyici gelen NAT kuralına katılır. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/probes/Join/Action | Yük dengeleyicinin araştırmalarını sağlar. Örneğin, bu izinle VM Ölçek kümesinin Healtharaştırma özelliği araştırmasına başvurabilir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/Read | Yük dengeleyici tanımını alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Locations/* | Ağ konumları oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/* | Ağ arabirimlerini oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Networksecuritygroups/Join/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Networksecuritygroups/Read | Bir ağ güvenlik grubu tanımını alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/Join/Action | Genel bir IP adresini birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/Read | Genel IP adresi tanımını alır. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/Join/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/backupprotectionert/Write | Yedekleme koruma hedefi oluşturma |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Dıtems/*/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Dıtems/Write | Yedekleme korumalı öğesi oluştur |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/Read | Tüm koruma Ilkelerini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/Write | Koruma Ilkesi oluşturur |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Kasa oluşturma işlemi, ' kasa ' türünde bir Azure kaynağı oluşturur |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Sanal makine Kullanıcı oturumu açma

Portalda sanal makineleri görüntüleyin ve normal bir kullanıcı olarak oturum açın. [Daha fazla bilgi edinin](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/Read | Genel IP adresi tanımını alır. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/Read | Yük dengeleyici tanımını alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, ines/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/Login/Action | Bir sanal makinede normal kullanıcı olarak oturum açın |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Ağ


### <a name="cdn-endpoint-contributor"></a>CDN uç noktası Katılımcısı

CDN uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN uç nokta okuyucusu

CDN uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN profili Katılımcısı

CDN profillerini ve uç noktalarını yönetebilir, ancak diğer kullanıcılara erişim izni veremez. [Daha fazla bilgi edinin](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN profili okuyucu

CDN profillerini ve uç noktalarını görüntüleyebilir, ancak değişiklik yapamaz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Klasik Ağ Katılımcısı

Klasik ağları yönetmenize izin verir, ancak bunlara erişemez. [Daha fazla bilgi edinin](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Klasik ağları oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS bölgesi Katılımcısı

Azure DNS, DNS bölgelerini ve kayıt kümelerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez. [Daha fazla bilgi edinin](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Dnsbölgesi es/* | DNS bölgeleri ve kayıtları oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Ağ Katılımcısı

Ağları yönetmenizi sağlar ancak onlara yönelik erişimleri vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Ağ oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Özel DNS bölge Katılımcısı

Özel DNS bölge kaynaklarını yönetmenizi sağlar, ancak bağlandıkları sanal ağları yönetemez. [Daha fazla bilgi edinin](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Join/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Katkıda bulunan Traffic Manager

Traffic Manager profillerini yönetmenizi sağlar, ancak bunlara kimlerin erişebileceğini denetlemenize izin vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Depolama


### <a name="avere-contributor"></a>Avere Katılımcısı

, Bir avere vFXT kümesi oluşturabilir ve yönetebilir. [Daha fazla bilgi edinin](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/kullanılabilirliği Bilitysets/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, ines/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/Read | Bir sanal ağ alt ağ tanımını alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/Join/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/jodavetaserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Networksecuritygroups/Join/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/* | Depolama hesapları oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Resources/Read | Kaynak grubunun kaynaklarını alır. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Delete | Bir blobu silmenin sonucunu döndürür |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Read | Blob veya Blobların listesini döndürür |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Write | Blob yazma sonucunu döndürür |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere Işleci

Kümeyi yönetmek için avere vFXT kümesi tarafından kullanılır [daha fazla bilgi](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/Read | Bir sanal makinenin özelliklerini al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Write | Ağ arabirimi oluşturur veya var olan bir ağ arabirimini güncelleştirir.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/Read | Bir sanal ağ alt ağ tanımını alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/Join/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Networksecuritygroups/Join/Action | Bir ağ güvenlik grubuna katılır. Alertable değil. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Delete | Bir kapsayıcıyı silmenin sonucunu döndürür |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Read | Kapsayıcıların listesini döndürür |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Write | Put blob kapsayıcısının sonucunu döndürür |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Delete | Bir blobu silmenin sonucunu döndürür |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Read | Blob veya Blobların listesini döndürür |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Write | Blob yazma sonucunu döndürür |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Yedek Katılımcısı

Yedekleme hizmetini yönetmenizi sağlar, ancak kasaların oluşturamaz ve başkalarına erişim verebilir [daha fazla bilgi edinin](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/operationresults/* | Yedekleme yönetiminde işlemin sonuçlarını yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/* | Kurtarma Hizmetleri kasasının yedekleme dokuları içinde yedekleme kapsayıcıları oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/refreshcontainers/Action | Kapsayıcı listesini yeniler |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobs/* | Yedekleme işleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobsexport/Action | Işleri dışarı aktar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupoperationresults/* | Yedekleme yönetimi işlemlerinin sonuçlarını oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/* | Yedekleme ilkeleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupkorunabilir TableItem/* | Yedeklenebilir öğeleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupkorunabilir Teditems/* | Yedeklenen öğeleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupprotectioncontainers/* | Yedekleme öğelerini tutan kapsayıcılar oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupsecuritypin/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupusagesummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/* | Kurtarma Hizmetleri kasasında yedekleme ile ilgili sertifikalar oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/* | Kasa ile ilgili genişletilmiş bilgileri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Read | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringconfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/* | Kayıtlı kimlikler oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Kurtarma Hizmetleri kasasının kullanımını oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupvalidateoperation/Action | Korumalı öğe üzerinde Işlemi doğrula |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Kasa oluşturma işlemi, ' kasa ' türünde bir Azure kaynağı oluşturur |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupoperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupengines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/backupprotectionert/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/korunabilir Tablecontainers/Read | Tüm korunabilir kapsayıcıları al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/BackupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupprevalidateprotection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupvalidatefeatures/Action | Özellikleri doğrulama |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Write | Uyarıyı çözer. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/OperationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupprotectionerts/Read | Tüm yedekleme koruma amaçlarını Listele |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Yedekleme Operatörü

Yedekleme kaldırma, kasa oluşturma ve [diğer kullanıcılara erişim](../backup/backup-rbac-rs-vault.md) verme dışında yedekleme hizmetlerini yönetmenizi sağlar

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/operationresults/Read | İşlemin durumunu döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/operationresults/Read | Koruma kapsayıcısında gerçekleştirilen Işlemin sonucunu alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Dıtems/Backup/Action | Korumalı öğe için yedekleme gerçekleştirir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/operationresults/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin sonucunu alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/operationsstatus/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin durumunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Korumalı öğe için anında öğe kurtarma sağla |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/recoverypoints/accesstoken/Action | Bölgeler arası geri yükleme için AccessToken alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/recoverypoints/Read | Korumalı öğeler için kurtarma noktalarını alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/recoverypoints/restore/Action | Korumalı öğeler için kurtarma noktalarını geri yükleyin. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Korumalı öğe için anında öğe kurtarmayı iptal et |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Dıtems/Write | Yedekleme korumalı öğesi oluştur |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/Read | Tüm kayıtlı kapsayıcıları döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/refreshcontainers/Action | Kapsayıcı listesini yeniler |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobs/* | Yedekleme işleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobsexport/Action | Işleri dışarı aktar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupoperationresults/* | Yedekleme yönetimi işlemlerinin sonuçlarını oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/operationresults/Read | Ilke Işleminin sonuçlarını alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/Read | Tüm koruma Ilkelerini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupkorunabilir TableItem/* | Yedeklenebilir öğeleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupkorunabilir Teditems/Read | Tüm korumalı öğelerin listesini döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupprotectioncontainers/Read | Aboneliğe ait tüm kapsayıcıları döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupusagesummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Kaynak sertifikası güncelleştirme işlemi kaynak/kasa kimlik bilgisi sertifikasını güncelleştirir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/Write | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Read | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringconfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/operationresults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentitıes/Write | Hizmet kapsayıcısını Kaydet işlemi, bir kapsayıcıyı kurtarma hizmeti 'ne kaydetmek için kullanılabilir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupvalidateoperation/Action | Korumalı öğe üzerinde Işlemi doğrula |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupoperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/Operations/Read | Ilke Işleminin durumunu alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/Write | Kayıtlı bir kapsayıcı oluşturur |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/Action | Bir kapsayıcı içindeki iş yükleri için sorgulama yap |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupengines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/backupprotectionert/Write | Yedekleme koruma hedefi oluşturma |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/backupprotectionert/Read | Yedekleme koruması hedefi al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/korunabilir Tablecontainers/Read | Tüm korunabilir kapsayıcıları al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/Item/Read | Bir kapsayıcıdaki tüm öğeleri Al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/BackupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupprevalidateprotection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupvalidatefeatures/Action | Özellikleri doğrulama |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupAadProperties/Read | Çapraz bölge geri yüklemesi için üçüncü bölgede kimlik doğrulama için AAD özelliklerini alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupcrrjobs/Action | Kurtarma Hizmetleri Kasası için ikincil bölgedeki çapraz bölge geri yükleme Işlerini listeleyin. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupcrrjob/Action | Kurtarma Hizmetleri Kasası için ikincil bölgedeki çapraz bölge geri yükleme Işi ayrıntılarını alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupcrossregionrestore/Action | Çapraz bölge geri yüklemeyi tetikle. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupcrroperationresults/Read | Kurtarma Hizmetleri Kasası için CRR Işlem sonucunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupcrroperationsstatus/Read | Kurtarma Hizmetleri Kasası için CRR Işlem durumunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Write | Uyarıyı çözer. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/OperationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupprotectionerts/Read | Tüm yedekleme koruma amaçlarını Listele |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Yedekleme okuyucusu

Yedekleme hizmetlerini görüntüleyebilir, ancak değişiklik yapamaz [daha fazla bilgi edinin](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedstamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/operationresults/Read | İşlemin durumunu döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/operationresults/Read | Koruma kapsayıcısında gerçekleştirilen Işlemin sonucunu alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/operationresults/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin sonucunu alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/operationsstatus/Read | Korumalı öğeler üzerinde gerçekleştirilen Işlemin durumunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/Read | Korumalı öğenin nesne ayrıntılarını döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/korunabilir Teditems/recoverypoints/Read | Korumalı öğeler için kurtarma noktalarını alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/Read | Tüm kayıtlı kapsayıcıları döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobs/operationresults/Read | Iş Işleminin sonucunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobs/Read | Tüm Iş nesnelerini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupjobsexport/Action | Işleri dışarı aktar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupoperationresults/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi sonucunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/operationresults/Read | Ilke Işleminin sonuçlarını alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/Read | Tüm koruma Ilkelerini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupkorunabilir Teditems/Read | Tüm korumalı öğelerin listesini döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupprotectioncontainers/Read | Aboneliğe ait tüm kapsayıcıları döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupusagesummaries/Read | Kurtarma Hizmetleri için korumalı öğeler ve korumalı sunucular için özetler döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Read | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/operationresults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/Read | Kurtarma Hizmetleri Kasası için depolama yapılandırmasını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/Read | Kurtarma Hizmetleri Kasası için yapılandırmayı döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupoperations/Read | Kurtarma Hizmetleri Kasası için yedekleme Işlemi durumunu döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backuppolicies/Operations/Read | Ilke Işleminin durumunu alın. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupengines/Read | Kasaya kayıtlı tüm yedekleme yönetimi sunucularını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/backupprotectionert/Read | Yedekleme koruması hedefi al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupfabrics/protectioncontainers/Item/Read | Bir kapsayıcıdaki tüm öğeleri Al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/BackupStatus/Action | Kurtarma Hizmetleri kasaları için yedekleme durumunu denetle |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringconfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Write | Uyarıyı çözer. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | İşlem, bir kaynak sağlayıcısı için Işlem listesini döndürür |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/OperationStatus/Read | Belirli bir Işlem için Işlem durumunu alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupprotectionerts/Read | Tüm yedekleme koruma amaçlarını Listele |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupvalidatefeatures/Action | Özellikleri doğrulama |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Klasik depolama hesabı Katılımcısı

Klasik depolama hesaplarını yönetmenize izin verir, ancak bunlara erişimi kalmaz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/* | Depolama hesapları oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Klasik depolama hesabı anahtar operatörü hizmet rolü

Klasik depolama hesabı anahtar Işleçleri klasik depolama hesaplarında anahtarları listeleme ve yeniden oluşturma izni verilir [daha fazla bilgi](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/ListKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/RegenerateKey/Action | Depolama hesabı için mevcut erişim anahtarlarını yeniden oluşturur. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Katkıda bulunan Data Box

, Diğer kullanıcılara erişim izni hariç Data Box hizmeti altındaki her şeyi yönetmenizi sağlar. [Daha fazla bilgi edinin](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box okuyucu

Sipariş oluşturma veya düzenleme sırası ayrıntıları ve başkalarına erişim verme dışında Data Box hizmetini yönetmenizi sağlar. [Daha fazla bilgi edinin](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/*/Read |  |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listcredentials/Action | Siparişle ilgili şifrelenmemiş kimlik bilgilerini listeler. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Locations/availableskus/Action | Bu yöntem, kullanılabilir SKU 'ların listesini döndürür. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Locations/ValidateInputs/Action | Bu yöntem, tüm doğrulama türlerini yapar. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Locations/regionconfiguration/Action | Bu yöntem, bölgenin yapılandırmasını döndürür. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Locations/ValidateAddress/Action | Sevkiyat adresini doğrular ve varsa alternatif adresler sağlar. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics geliştirici

Kendi işlerinizi göndermenize, izlemenize ve yönetmenize izin verir, ancak Data Lake Analytics Hesapları oluşturamaz veya silemezsiniz. [Daha fazla bilgi edinin](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. BigAnalytics/accounts/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/hesaplar/Sil |  |
> | Microsoft. BigAnalytics/hesaplar/Takesahiplik/eylem |  |
> | Microsoft. BigAnalytics/hesaplar/yazma |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Bir DataLakeAnalytics hesabını silin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/Action | Diğer kullanıcılar tarafından gönderilen işleri iptal etmek için izin verin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Bir DataLakeAnalytics hesabı oluşturun veya güncelleştirin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Bir DataLakeAnalytics hesabının bağlı DataLakeStore hesabını oluşturun veya güncelleştirin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Bir DataLakeStore hesabının bir DataLakeAnalytics hesabıyla bağlantısını kaldırın. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageaccounts/Write | Bir DataLakeAnalytics hesabı için bağlı bir depolama hesabı oluşturun veya güncelleştirin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageaccounts/Delete | Bir depolama hesabının bir DataLakeAnalytics hesabından bağlantısını kaldırın. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallkurallarını/Write | Bir güvenlik duvarı kuralı oluşturun veya güncelleştirin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallkurallarını/Delete | Bir güvenlik duvarı kuralını silin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Bir işlem ilkesi oluşturun veya güncelleştirin. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | İşlem ilkesini silin. |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Okuyucu ve veri erişimi

Her şeyi görüntülemenize izin verir, ancak bir depolama hesabını veya kapsanan kaynağı silmenize veya oluşturmanıza izin vermez. Ayrıca depolama hesabı anahtarlarına erişim aracılığıyla bir depolama hesabında bulunan tüm verilere okuma/yazma erişimi de sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/lıstaccountsas/Action | Belirtilen depolama hesabı için hesap SAS belirtecini döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Depolama Hesabı Katılımcısı

Depolama hesaplarının yönetimine izin verir. Paylaşılan anahtar yetkilendirmesi aracılığıyla verilere erişmek için kullanılabilen hesap anahtarına erişim sağlar. [Daha fazla bilgi edinin](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Diagnosticsettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/jodavetaserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/* | Depolama hesapları oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Depolama hesabı anahtar operatörü hizmet rolü

Depolama hesabı erişim anahtarlarının listelenmesi ve yeniden oluşturulmasına izin verir. [Daha fazla bilgi edinin](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/RegenerateKey/Action | Belirtilen depolama hesabı için erişim anahtarlarını yeniden oluşturur. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Depolama Blob Verileri Katkıda Bulunanı

Azure depolama kapsayıcıları ve bloblarını okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Delete | Kapsayıcıyı silin. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Read | Bir kapsayıcı veya kapsayıcıların listesini döndürün. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Write | Kapsayıcının meta verilerini veya özelliklerini değiştirin. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/generateuserdelegationkey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Delete | Bir blobu silin. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Read | Blob veya Blobların listesini döndürün. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Write | Bir bloba yazın. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Move/Action | Blobu bir yoldan diğerine kaydırır |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Add/Action | Blob içeriği ekleme sonucunu döndürür |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Depolama Blob Verileri Sahibi

, POSIX erişim denetimi atama dahil olmak üzere Azure depolama blob kapsayıcılarına ve verilerine tam erişim sağlar. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/* | Kapsayıcılarda tam izinler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/generateuserdelegationkey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/* | Bloblarda tam izinler. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Depolama Blob Verileri Okuyucusu

Azure depolama kapsayıcıları ve bloblarını okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Read | Bir kapsayıcı veya kapsayıcıların listesini döndürün. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/generateuserdelegationkey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/containers/Blobs/Read | Blob veya Blobların listesini döndürün. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Depolama Blobu Delegator

Azure AD kimlik bilgileriyle imzalanan bir kapsayıcı veya blob için paylaşılan erişim imzası oluşturmak üzere kullanılabilen bir Kullanıcı temsili anahtarı alın. Daha fazla bilgi için bkz. [Kullanıcı TEMSILI SAS oluşturma](/rest/api/storageservices/create-user-delegation-sas). [Daha fazla bilgi edinin](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/blobservices/generateuserdelegationkey/Action | Blob hizmeti için Kullanıcı temsili anahtarını döndürür. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Depolama Dosyası Verileri SMB Paylaşımı Katkıda Bulunanı

Azure dosya paylaşımlarında dosya/dizinlerde okuma, yazma ve silme erişimine izin verir. Bu rolün Windows dosya sunucularında yerleşik bir eşdeğeri yoktur. [Daha fazla bilgi edinin](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fıleservices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesini döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fıleservices/fileshares/Files/Write | Bir dosya yazmanın veya bir klasör oluşturmanın sonucunu döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fileservices/fileshares/Files/Delete | Dosya/klasör silmenin sonucunu döndürür. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Depolama Dosyası Verileri SMB Paylaşımı Yükseltilmiş Katkıda Bulunanı

Azure dosya paylaşımlarında dosya/dizinlerde okuma, yazma, silme ve değiştirme ACL 'Lerine izin verir. Bu rol, Windows dosya sunucularındaki değişikliğin bir dosya paylaşımının ACL 'sine eşdeğerdir. [Daha fazla bilgi edinin](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fıleservices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesini döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fıleservices/fileshares/Files/Write | Bir dosya yazmanın veya bir klasör oluşturmanın sonucunu döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fileservices/fileshares/Files/Delete | Dosya/klasör silmenin sonucunu döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fıleservices/fileshares/Files/modifypermissions/Action | Bir dosya/klasör üzerinde izin değiştirme sonucunu döndürür. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Depolama Dosyası Verileri SMB Paylaşımı Okuyucusu

Azure dosya paylaşımlarında dosya/dizinlerde okuma erişimine izin verir. Bu rol, Windows dosya sunucularında okunan bir dosya paylaşma ACL 'sine eşdeğerdir. [Daha fazla bilgi edinin](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/fıleservices/fileshares/Files/Read | Dosya/klasör veya dosya/klasör listesini döndürür. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Depolama kuyruğu verileri Katılımcısı

Azure depolama kuyruklarını ve sıra iletilerini okuyun, yazın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/Delete | Kuyruğu silme. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/Read | Kuyruğu veya kuyruk listesini döndürün. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/Write | Sıra meta verilerini veya özelliklerini değiştirin. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Delete | Bir kuyruktan bir veya daha fazla iletiyi silin. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Read | Bir kuyruktaki bir veya daha fazla iletiyi Peek veya alma. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Write | Bir kuyruğa ileti ekleyin. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Process/Action | İleti işlemenin sonucunu döndürür |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Depolama kuyruğu veri Iletisi Işlemcisi

Azure depolama kuyruğundan bir iletiyi göz atın, alın ve silin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Read | İletiye göz atın. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Process/Action | Bir iletiyi alın ve silin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Depolama kuyruğu veri Iletisi gönderici

Bir Azure depolama kuyruğuna ileti ekleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Add/Action | Bir kuyruğa ileti ekleyin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Depolama kuyruğu veri okuyucusu

Azure depolama kuyruklarını ve sıra iletilerini okuyun ve listeleyin. Belirli bir veri işlemi için hangi eylemlerin gerekli olduğunu öğrenmek için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/Read | Kuyruğu veya kuyruk listesini döndürür. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/queueservices/Queues/ileti/Read | Bir kuyruktaki bir veya daha fazla iletiyi Peek veya alma. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-contributor"></a>Azure haritalar veri Katılımcısı

Azure haritalar hesabından ilgili verileri eşlemek için okuma, yazma ve silme erişimi verir. [Daha fazla bilgi edinin](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Write |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Delete |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure haritalar veri okuyucu

Azure haritalar hesabından ilgili harita okuma verilerine erişim izni verir. [Daha fazla bilgi edinin](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-data-reader"></a>Azure yay bulutu veri okuyucu

Azure yay bulut verilerine okuma erişimine izin ver [daha fazla bilgi](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/Read |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Katkıda bulunan Arama Hizmeti

Arama hizmetlerini yönetmenize izin verir, ancak bunlara erişim izni vermez. [Daha fazla bilgi edinin](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Search](resource-provider-operations.md#microsoftsearch)/Searchservices/* | Arama Hizmetleri oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey okuyucusu

SignalR hizmeti erişim anahtarlarını okuyun

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/Read |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ListKeys/Action | Yönetim portalında veya API aracılığıyla SignalR erişim anahtarlarının değerini görüntüleme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR App Server (Önizleme)

Uygulama sunucunuzun SignalR hizmetine AAD kimlik doğrulama seçenekleriyle erişmesini sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/AccessKey/Action | Clienttoken imzalamak için geçici bir AccessKey oluşturun. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ServerConnection/Write | Sunucu bağlantısı başlatın. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>SignalR Katılımcısı

SignalR hizmeti kaynaklarını oluşturun, okuyun, güncelleştirin ve silin

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>SignalR sunucusuz katılımcısı (Önizleme)

Uygulama erişim hizmetinize AAD kimlik doğrulama seçenekleriyle sunucusuz modda sahip olmanızı sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clienttoken/Action | İstemci bağlantısı başlatmak için bir ClientToken oluşturun. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>SignalR hizmeti sahibi (Önizleme)

Azure SignalR hizmeti REST API 'Lerine tam erişim

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/AccessKey/Action | Clienttoken imzalamak için geçici bir AccessKey oluşturun. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clienttoken/Action | İstemci bağlantısı başlatmak için bir ClientToken oluşturun. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/Send/Action | Hub 'daki tüm istemci bağlantılarına ileti yayınlayın. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Gruba yayın iletisi. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Grup varlığını veya Kullanıcı varlığını denetleyin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Write | Grubu birleştir/bırak. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ClientConnection/Send/Action | İletileri doğrudan bir istemci bağlantısına gönderin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ClientConnection/Read | İstemci bağlantısı varlığını denetleyin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ClientConnection/Write | İstemci bağlantısını kapatın. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Kullanıcıya birden çok istemci bağlantısından oluşabilen iletiler gönderin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Kullanıcı varlığını denetleyin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Write | Bir kullanıcıyı değiştirin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>SignalR hizmeti okuyucu (Önizleme)

Azure SignalR hizmeti REST API 'Lerine salt okuma erişimi

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Grup varlığını veya Kullanıcı varlığını denetleyin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ClientConnection/Read | İstemci bağlantısı varlığını denetleyin. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Kullanıcı varlığını denetleyin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Web planı Katılımcısı

Web siteleri için Web planlarını yönetmenizi sağlar, ancak bunlara erişemez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Serverfarms/* | Sunucu grupları oluşturma ve yönetme |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Hostingenvironments/Join/Action | Bir App Service Ortamı birleştirir |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Web sitesi Katılımcısı

Web sitelerini yönetmenizi sağlar, ancak bunlara erişemez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Öngörüler bileşenleri oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Certificates/* | Web sitesi sertifikaları oluşturma ve yönetme |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Listsitesassignedtohostname/Read | Ana bilgisayar adına atanan sitelerin adlarını alın. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Serverfarms/Join/Action | Bir App Service planına katılır |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Serverfarms/Read | App Service planında özellikleri al |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Sites/* | Web siteleri oluşturma ve yönetme (site oluşturma, ayrıca ilişkili App Service planına yazma izinleri gerektirir) |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Kapsayıcılar


### <a name="acrdelete"></a>AcrDelete

ACR Delete [daha fazla bilgi](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Artifacts/Delete | Bir kapsayıcı kayıt defterinde yapıtı silin. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>Acrimageimzalayan

ACR görüntü imzalayan [daha fazla bilgi](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Sign/Write | Kapsayıcı kayıt defteri için gönderme/çekme içeriği güven meta verileri. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

ACR çekme [daha fazla bilgi](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Pull/Read | Bir kapsayıcı kayıt defterinden görüntüleri çekin veya alın. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

ACR Push [daha fazla bilgi](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Pull/Read | Bir kapsayıcı kayıt defterinden görüntüleri çekin veya alın. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Push/Write | Bir kapsayıcı kayıt defterine görüntü gönderin veya yazın. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>Acrquaranınereader

ACR karantina veri okuyucusu

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Quarantine/Read | Kapsayıcı kayıt defterinden karantinaya alınmış görüntüleri çekme veya alma |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR karantina veri yazıcısı

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Quarantine/Read | Kapsayıcı kayıt defterinden karantinaya alınmış görüntüleri çekme veya alma |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/kayıt Tries/Quarantine/Write | Karantinaya alınan görüntülerin karantina durumunu yazma/değiştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes hizmet kümesi yönetici rolü

Küme Yöneticisi kimlik bilgisi eylemini listeleyin. [Daha fazla bilgi edinin](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/listclusteradmincredential/Action | Yönetilen kümenin clusterAdmin kimlik bilgisini listeleyin |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/accessprofiles/listcredential/Action | Liste kimlik bilgisini kullanarak rol adına göre yönetilen küme erişim profili al |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Read | Yönetilen bir küme al |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes hizmet kümesi Kullanıcı rolü

Küme kullanıcı kimlik bilgilerini Listele eylemi. [Daha fazla bilgi edinin](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/listclusterusercredential/Action | Yönetilen kümenin clusterUser kimlik bilgisini listeleyin |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Read | Yönetilen bir küme al |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes hizmet katılımcısı rolü

Azure Kubernetes hizmet kümelerine okuma ve yazma erişimi verir [daha fazla bilgi](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Read | Yönetilen bir küme al |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Write | Yeni bir yönetilen küme oluşturur veya var olan bir kümeyi güncelleştirir |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes hizmeti RBAC Yöneticisi

Kaynak kotalarını ve ad alanlarını güncelleştirme veya silme dışında, küme/ad alanı altındaki tüm kaynakları yönetmenizi sağlar. [Daha fazla bilgi edinin](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/listclusterusercredential/Action | Yönetilen kümenin clusterUser kimlik bilgisini listeleyin |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/resourcequotas/Write | Resourcequotas yazar |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/resourcequotas/Delete | Resourcequotas 'ı siler |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Write | Ad alanlarını yazma |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Delete | Ad alanlarını siler |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes hizmeti RBAC kümesi Yöneticisi

Kümedeki tüm kaynakları yönetmenizi sağlar. [Daha fazla bilgi edinin](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/listclusterusercredential/Action | Yönetilen kümenin clusterUser kimlik bilgisini listeleyin |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes hizmeti RBAC okuyucusu

Bir ad alanındaki birçok nesneyi görmek için salt okuma erişimine izin verir. Rollerin veya rol bağlamalarının görüntülenmesine izin vermez. Bu rol, gizli dizi içeriğini okumak için ad alanındaki ServiceAccount kimlik bilgilerine erişim sağladığından ad alanındaki (ayrıcalık yükseltme bir biçimde) API erişimine izin verecek şekilde gizli dizileri görüntülemeye izin vermez. Bu rolün küme kapsamına uygulanması, tüm ad alanları üzerinde erişim sağlar. [Daha fazla bilgi edinin](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/controllerrevisions/Read | Controllerdüzeltmelerinizi okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/Read | Daemonsets okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/Deployments/Read | Dağıtımları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/replicasets/Read | Replicasets 'i okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/statefulsets/Read | Statefulsets 'i okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/otomatik Scaling/horizontalpodadutoscalers/Read | Horizontalpodadutoscalers okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Batch/cronjobs/Read | Cronjobs okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Batch/Jobs/Read | İşleri okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/configmaps/Read | Configmaps 'u okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/Read | Uç noktaları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.k8s.io/Events/Read | Olayları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Events/Read | Olayları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/Read | Daemonsets okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/Deployments/Read | Dağıtımları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/ingresses/Read | Gelen parolaları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/networkpolicies/Read | Networkpolicies okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/replicasets/Read | Replicasets 'i okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/limitranges/Read | Limitaralıkları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Read | Ad alanlarını okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.io/ingresses/Read | Gelen parolaları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.io/networkpolicies/Read | Networkpolicies okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/persistentvolumeclaims/Read | Persistentvolumeclaim okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/pods/Read | Pod 'leri okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/Read | Pod kesintiden oluşan bütçeleri okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/replicationcontrollers/Read | Replicationcontrollers okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/replicationcontrollers/Read | Replicationcontrollers okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/resourcequotas/Read | Resourcequotas okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/serviceaccounts/Read | Hizmet hesaplarını okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Services/Read | Hizmetleri okur |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes hizmeti RBAC yazıcı

Bir ad alanındaki nesnelerin çoğuna okuma/yazma erişimi sağlar. Bu rol, rolleri veya rol bağlamalarını görüntülemeye veya değiştirmeye izin vermiyor. Ancak, bu rol, ad alanındaki herhangi bir ServiceAccount 'nin API erişim düzeylerini kazanmak için kullanılabilir, bu nedenle gizli dizi ve Dizin çalıştırmaya erişim sağlar. Bu rolün küme kapsamına uygulanması, tüm ad alanları üzerinde erişim sağlar. [Daha fazla bilgi edinin](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/controllerrevisions/Read | Controllerdüzeltmelerinizi okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Apps/statefulsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/otomatik Scaling/horizontalpodadutoscalers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Batch/cronjobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Batch/Jobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/configmaps/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.k8s.io/Events/Read | Olayları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Events/Read | Olayları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Extensions/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/limitranges/Read | Limitaralıkları okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Read | Ad alanlarını okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.io/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.io/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/persistentvolumeclaims/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/pods/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/resourcequotas/Read | Resourcequotas okur |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Secrets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/serviceaccounts/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/Managedclusters/Services/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Veritabanları


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB hesabı okuyucu rolü

Azure Cosmos DB hesabı verilerini okuyabilir. Azure Cosmos DB hesaplarını yönetmek için [DocumentDB hesabı katılımcısı](#documentdb-account-contributor) konusuna bakın. [Daha fazla bilgi edinin](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Tüm koleksiyonları okuyun |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/readonlykeys/Action | Veritabanı hesabı salt okunur anahtarlarını okur. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ölçümleri oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB Işleci

Azure Cosmos DB hesaplarını yönetmenizi sağlar ancak içerdikleri verilere erişemez. Hesap anahtarlarına ve bağlantı dizelerine erişimi engeller. [Daha fazla bilgi edinin](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/jodavetaserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/readonlykeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/RegenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/ListKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/sqlroledefinitions/Write | SQL rol tanımı oluşturma veya güncelleştirme |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/sqlroledefinitions/Delete | SQL rol tanımını silme |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/sqlroleatamas/Write | SQL rol ataması oluşturma veya güncelleştirme |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/sqlroleatamas/Delete | SQL rolü atamasını silme |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Bir Cosmos DB veritabanı veya bir hesabın kapsayıcısı için geri yükleme isteği gönderebilir [daha fazla bilgi edinin](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/Backup/Action | Yedeklemeyi yapılandırmak için bir istek gönder |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/restore/Action | Geri yükleme isteği gönder |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

Sürekli yedekleme moduyla Cosmos DB veritabanı hesabı için geri yükleme eylemi gerçekleştirebilir

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restoraybledatabaseaccounts/restore/Action | Geri yükleme isteği gönder |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restoraybledatabaseaccounts/*/Read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restoraybledatabaseaccounts/Read | Geri yüklenebilen bir veritabanı hesabını okuyun veya tüm geri yüklenebilen veritabanı hesaplarını listeleyin |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB hesabı Katılımcısı

, Azure Cosmos DB hesaplarını yönetebilir. Azure Cosmos DB daha önce DocumentDB olarak bilinirdi. [Daha fazla bilgi edinin](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/Databaseaccounts/* | Azure Cosmos DB hesapları oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/jodavetaserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Katkıda bulunan Redis Cache

Redsıs önbellekler yönetmenize izin verir, ancak bunlara erişimi olmaz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Cache](resource-provider-operations.md#microsoftcache)/Register/Action | ' Microsoft. cache ' kaynak sağlayıcısını bir abonelikle kaydeder |
> | [Microsoft. Cache](resource-provider-operations.md#microsoftcache)/Redis/* | Redsıs önbellekler oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB Katılımcısı

SQL veritabanlarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez. Ayrıca, güvenlikle ilgili ilkeleri veya bunların üst SQL sunucularını yönetemezsiniz. [Daha fazla bilgi edinin](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/* | SQL veritabanı oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ölçümleri oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/securityalcertpolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/sımıyassessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | Denetim ayarlarını Düzenle |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/Read | Veritabanı blobu denetim kayıtlarını alma |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Veri maskeleme ilkelerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | Güvenlik Uyarısı ilkelerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | Güvenlik ölçümlerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/ |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL yönetilen örnek Katılımcısı

SQL yönetilen örnekleri ve gerekli ağ yapılandırmasını yönetmenizi sağlar, ancak başkalarına erişim izni veremeyiz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Networksecuritygroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Routetables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/ınstancefailovergroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ölçümleri oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/azureadonlyauthentications/Delete | Yalnızca kimlik doğrulama nesnesini Azure Active Directory belirli bir yönetilen sunucuyu siler |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/azureadonlyauthentications/Write | Yalnızca kimlik doğrulama nesnesi Azure Active Directory belirli bir yönetilen sunucu ekler veya güncelleştirir |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL Güvenlik Yöneticisi

, SQL Server ve veritabanlarının güvenlikle ilgili ilkelerini yönetmenizi sağlar, ancak bunlara erişemez. [Daha fazla bilgi edinin](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/jodavetaserviceendpoint/Action | Depolama hesabı veya SQL veritabanı gibi kaynağı bir alt ağa birleştirir. Alertable değil. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/yönetimzureasyncoperation/Read | Yönetilen örnek Azure zaman uyumsuz yönetici işlemleri sonucunu alır. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/securityalcertpolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/sımıyassessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingsettings/* | SQL Server denetim ayarı oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedauditingsettings/Read | Belirli bir sunucuda yapılandırılmış genişletilmiş sunucu blobu denetim ilkesinin ayrıntılarını alma |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | SQL Server veritabanı denetim ayarlarını oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/Read | Veritabanı blobu denetim kayıtlarını alma |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | SQL Server veritabanı veri maskeleme ilkeleri oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/Read | Belirli bir veritabanında yapılandırılan genişletilmiş blob denetim ilkesinin ayrıntılarını alma |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/Read | Veritabanlarının listesini döndürün veya belirtilen veritabanının özelliklerini alır. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Read | Veritabanı şeması alın. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/Read | Veritabanı sütunu al. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Read | Veritabanı tablosu al. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | SQL Server veritabanı güvenlik uyarısı ilkeleri oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | SQL Server veritabanı güvenlik ölçümleri oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/devOpsAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/firewallkuralları/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Sunucu listesini döndürün veya belirtilen sunucunun özelliklerini alır. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityalcertpolicies/* | SQL Server güvenlik uyarı ilkeleri oluşturma ve yönetme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/ |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureadonlyauthentications/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/Read | Yönetilen örneklerin listesini döndürün veya belirtilen yönetilen örnek için özellikleri alır. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/azureadonlyauthentications/* |  |
> | [Microsoft. Security/Sqlgüvenlik](resource-provider-operations.md#microsoftsecurity)açıcısı |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/TINS/Read | Yönetilen örnek yöneticilerinin bir listesini alır. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/TINS/Read | Belirli bir Azure Active Directory yönetici nesnesini alır |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Katkıda bulunan SQL Server

SQL Server ve veritabanlarını yönetmenizi sağlar, ancak bunlara yönelik erişimleri ve güvenlikle ilgili ilkeleri yönetemez. [Daha fazla bilgi edinin](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/* | SQL Server 'lar oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ölçümleri oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/securityalcertpolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/ManagedInstances/sımıyassessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingsettings/* | SQL Server denetim ayarlarını Düzenle |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | SQL Server veritabanı denetim ayarlarını Düzenle |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/Read | Veritabanı blobu denetim kayıtlarını alma |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | SQL Server veritabanı veri maskeleme ilkelerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | SQL Server veritabanı güvenlik uyarısı ilkelerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | SQL Server veritabanı güvenlik ölçümlerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/devOpsAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedauditingsettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityalcertpolicies/* | SQL Server güvenlik uyarısı ilkelerini düzenleme |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/ |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureadonlyauthentications/Delete | Yalnızca kimlik doğrulama nesnesini Azure Active Directory belirli bir sunucuyu siler |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureadonlyauthentications/Write | Yalnızca kimlik doğrulama nesnesi Azure Active Directory belirli bir sunucu ekler veya güncelleştirir |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analiz


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs veri sahibi

Azure Event Hubs kaynaklarına tam erişim sağlar. [Daha fazla bilgi edinin](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs veri alıcısı

Azure Event Hubs kaynaklarına erişim izni verir. [Daha fazla bilgi edinin](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs veri gönderici

Azure Event Hubs kaynaklarına erişim gönderilmesine izin verir. [Daha fazla bilgi edinin](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Katkıda bulunan Data Factory

Veri fabrikalarının yanı sıra bunların içindeki alt kaynakları oluşturun ve yönetin. [Daha fazla bilgi edinin](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/Datafactories/* | Veri fabrikaları ve bunların içinde alt kaynaklar oluşturun ve yönetin. |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/Factories/* | Veri fabrikaları ve bunların içinde alt kaynaklar oluşturun ve yönetin. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/EventSubscriptions/Write | EventSubscription oluşturma veya güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Veri Takiger

Log Analytics çalışma alanından özel verileri silin. [Daha fazla bilgi edinin](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/Purge/Action | Application Insights verileri temizleme |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics verilerini görüntüleme |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Purge/Action | Belirtilen verileri çalışma alanından Sil |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight küme operatörü

HDInsight küme yapılandırmasını okuyup değiştirmenize izin verir. [Daha fazla bilgi edinin](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/getgatewaysettings/Action | HDInsight kümesi için ağ geçidi ayarlarını al |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/updategatewaysettings/Action | HDInsight kümesi için ağ geçidi ayarlarını güncelleştirme |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/Configurations/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Dağıtım işlemlerini alır veya listeler. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight etki alanı Hizmetleri Katılımcısı

HDInsight için gereken etki alanı Hizmetleri ile ilgili işlemleri okuyabilir, oluşturabilir, değiştirebilir ve silebilir Kurumsal Güvenlik Paketi [daha fazla bilgi edinin](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/DomainServices/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/DomainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics Katkıda Bulunan

Log Analytics katkı, tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. İzleme ayarlarını düzenlediğinizde VM 'lere VM uzantısının eklenmesi dahildir; Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okuma; Otomasyon hesapları oluşturma ve yapılandırma; çözümler ekleme; ve Azure tanılama 'yı tüm Azure kaynaklarında yapılandırma. [Daha fazla bilgi edinin](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/Virtuallarines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/ListKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/larines/Extensions/Write | Bir Azure Arc uzantısını yükleme veya güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Diagnosticsettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics Okuyucusu

Log Analytics okuyucu tüm izleme verilerini görüntüleyip arayabilir ve tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntüleme dahil olmak üzere izleme ayarlarını görüntüleyebilir. [Daha fazla bilgi edinin](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Yeni altyapıyı kullanarak arama yapın. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Arama sorgusu yürütür |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Read | Çalışma alanının paylaşılan anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator"></a>Veri Seçisini takip etme

Microsoft. purview verileri Curator, Katalog veri nesnelerini oluşturabilir, okuyabilir, değiştirebilir ve silebilir ve nesneler arasında ilişkiler oluşturabilir. Bu rol önizleme aşamasındadır ve değiştirilebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Read | Microsoft purview sağlayıcısı için hesap kaynağını okuyun. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Data/Read | Veri nesnelerini okuyun. |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Data/Write | Veri nesneleri oluşturun, güncelleştirin ve silin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Purview veri okuyucu

Microsoft. purview veri okuyucu, Katalog veri nesnelerini okuyabilir. Bu rol önizleme aşamasındadır ve değiştirilebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Read | Microsoft purview sağlayıcısı için hesap kaynağını okuyun. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Data/Read | Veri nesnelerini okuyun. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Takip görünümü veri kaynağı Yöneticisi

Microsoft. purview veri kaynağı Yöneticisi veri kaynaklarını ve veri taramalarını yönetebilir. Bu rol önizleme aşamasındadır ve değiştirilebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Read | Microsoft purview sağlayıcısı için hesap kaynağını okuyun. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Read | Veri kaynaklarını ve taramaları okuyun. |
> | [Microsoft. purview](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Write | Veri kaynaklarını oluşturun, güncelleştirin ve silin ve taramaları yönetin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Şema kayıt defterine katkıda bulunan (Önizleme)

Şema kayıt defteri gruplarını ve şemalarını okuyun, yazın ve silin.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Şema kayıt defteri okuyucusu (Önizleme)

Şema kayıt defteri gruplarını ve şemaları okuyun ve listeleyin.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/Read | SchemaGroup kaynak açıklamaları listesini al |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/Read | Şemaları al |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blok Zinciri


### <a name="blockchain-member-node-access-preview"></a>Blok zinciri üye düğümü erişimi (Önizleme)

Blok zinciri üye düğümlerine erişim sağlar [daha fazla bilgi](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Blockzinciri](resource-provider-operations.md#microsoftblockchain)/Blockchainmembers/transactionnodes/Read | Varolan blok zinciri üye Işlem düğümlerini alır veya listeler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Blockzinciri](resource-provider-operations.md#microsoftblockchain)/Blockchainmembers/transactionnodes/Connect/Action | Bir blok zinciri üye Işlem düğümüne bağlanır. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>Yapay zeka + makine öğrenmesi


### <a name="cognitive-services-contributor"></a>Bilişsel hizmetler Katılımcısı

Bilişsel hizmetler için anahtar oluşturma, okuma, güncelleştirme, silme ve yönetme olanağı sağlar. [Daha fazla bilgi edinin](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)/Features/Read | Bir aboneliğin özelliklerini alır. |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)/Providers/Features/Read | Belirli bir kaynak sağlayıcısındaki bir aboneliğin özelliğini alır. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Diagnosticsettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Logdefinitions/Read | Günlük tanımlarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ölçümleri oku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Dağıtım işlemlerini alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Bilişsel Hizmetler Özel Görüntü İşleme Katılımcısı

Projeleri görüntüleme, oluşturma, düzenleme veya silme özelliği de dahil olmak üzere projeye tam erişim. [Daha fazla bilgi edinin](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Bilişsel Hizmetler Özel Görüntü İşleme dağıtımı

Modelleri yayımlayın, yayımdan kaldırın veya dışarı aktarın. Dağıtım projeyi görüntüleyebilir, ancak güncelleştiremez. [Daha fazla bilgi edinin](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/predictions/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/iterations/Publish/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/iterations/Export/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/QuickTest/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Projeyi dışa aktarır. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Bilişsel Hizmetler Özel Görüntü İşleme etiketleyici

Eğitim görüntülerini görüntüleyin, düzenleyin, görüntü etiketlerini oluşturun, ekleyin, kaldırın veya silin. Etiketleyiciler projeyi görüntüleyebilir, ancak eğitim görüntüleri ve Etiketler dışında herhangi bir şeyi güncelleştiremez. [Daha fazla bilgi edinin](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/predictions/Query/Action | Tahmin uç noktanıza gönderilen görüntüleri alın. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/images/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Tags/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/images/Suggested/* |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/tagsandregions/suggestions/Action | Bu API, Etiketler için bir dizi/etiketsiz görüntü için önerilen Etiketler ve bölgeler alır. Hiçbir etiket bulunamazsa boş bir dizi döndürür. |
> | **NotDataActions** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Projeyi dışa aktarır. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Bilişsel Hizmetler Özel Görüntü İşleme okuyucu

Projedeki salt okuma eylemleri. Okuyucular projeyi oluşturamaz veya güncelleştiremez. [Daha fazla bilgi edinin](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/predictions/Query/Action | Tahmin uç noktanıza gönderilen görüntüleri alın. |
> | **NotDataActions** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Projeyi dışa aktarır. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Bilişsel Hizmetler Özel Görüntü İşleme Trainer

Modelleri yayımlama, yayımdan kaldırma, dışarı aktarma özelliği de dahil olmak üzere projeleri görüntüleyin, düzenleyin ve modelleri eğitme. Traıners proje oluşturamaz veya silemez. [Daha fazla bilgi edinin](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Action | Bir proje oluşturun. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Delete | Belirli bir projeyi silin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/import/Action | Bir projeyi içeri aktarır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Projeyi dışa aktarır. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Bilişsel hizmetler veri okuyucu (Önizleme)

Bilişsel hizmetler verilerini okumanızı sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Bilişsel hizmetler ölçüm Danışmanı Yöneticisi

Sistem düzeyi yapılandırması da dahil olmak üzere, projeye tam erişim. [Daha fazla bilgi edinin](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/metricsadvisor/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Bilişsel hizmetler Soru-Cevap Oluşturma Düzenleyicisi

KB oluşturma, düzenleme, içeri aktarma ve dışarı aktarma işlemi yapmanızı sağlar. Bir KB yayımlayamaz veya silemezsiniz. [Daha fazla bilgi edinin](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Roleatamas/Read | Rol ataması hakkında bilgi alın. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Roledefinitions/Read | Rol tanımı hakkında bilgi alın. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Read | Belirli bir knowledgeun listesini veya belirli bir knowledgeelleyici 'nin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Download/Read | Bilgi Bankası 'nı indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Create/Write | Yeni bir Bilgi Bankası oluşturmak için zaman uyumsuz işlem. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Write | Bir Bilgi Bankası 'yi değiştirme veya Bilgi Bankası içeriğini değiştirme için zaman uyumsuz işlem. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | Bilgi Bankası 'nı sorgulamak için GenerateAnswer çağrısı. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Train/Action | Bilgi Bankası 'na öneriler eklemek için çağrıyı eğitme. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/alterations/Read | Çalışma zamanının değişikliklerini indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/alterations/Write | Değişiklikleri değiştirme verileri. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/endpointkeys/Read | Uç nokta için uç nokta anahtarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/endpointkeys/refreshkeys/Action | Bir uç nokta anahtarını yeniden oluşturur. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/endpointsettings/Read | Uç nokta için uç nokta ayarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/endpointsettings/Write | Uç nokta için uç nokta seetkileni güncelleştirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/Operations/Read | Belirli bir uzun süre çalışan işlemin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Read | Belirli bir knowledgeun listesini veya belirli bir knowledgeelleyici 'nin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Download/Read | Bilgi Bankası 'nı indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Create/Write | Yeni bir Bilgi Bankası oluşturmak için zaman uyumsuz işlem. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Write | Bir Bilgi Bankası 'yi değiştirme veya Bilgi Bankası içeriğini değiştirme için zaman uyumsuz işlem. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | Bilgi Bankası 'nı sorgulamak için GenerateAnswer çağrısı. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Train/Action | Bilgi Bankası 'na öneriler eklemek için çağrıyı eğitme. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/alterations/Read | Çalışma zamanının değişikliklerini indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/alterations/Write | Değişiklikleri değiştirme verileri. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/endpointkeys/Read | Uç nokta için uç nokta anahtarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/endpointkeys/refreshkeys/Action | Bir uç nokta anahtarını yeniden oluşturur. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/endpointsettings/Read | Uç nokta için uç nokta ayarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/endpointsettings/Write | Uç nokta için uç nokta seetkileni güncelleştirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/Operations/Read | Belirli bir uzun süre çalışan işlemin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Read | Belirli bir knowledgeun listesini veya belirli bir knowledgeelleyici 'nin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Download/Read | Bilgi Bankası 'nı indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Create/Write | Yeni bir Bilgi Bankası oluşturmak için zaman uyumsuz işlem. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Write | Bir Bilgi Bankası 'yi değiştirme veya Bilgi Bankası içeriğini değiştirme için zaman uyumsuz işlem. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/Action | Bilgi Bankası 'nı sorgulamak için GenerateAnswer çağrısı. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Train/Action | Bilgi Bankası 'na öneriler eklemek için çağrıyı eğitme. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/Read | Çalışma zamanının değişikliklerini indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/Write | Değişiklikleri değiştirme verileri. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/Read | Uç nokta için uç nokta anahtarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/Action | Bir uç nokta anahtarını yeniden oluşturur. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/Read | Uç nokta için uç nokta ayarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/Write | Uç nokta için uç nokta seetkileni güncelleştirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/Operations/Read | Belirli bir uzun süre çalışan işlemin ayrıntılarını alır. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Bilişsel hizmetler Soru-Cevap Oluşturma okuyucu

Yalnızca bir KB 'yi okuyup test edelim. [Daha fazla bilgi edinin](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Roleatamas/Read | Rol ataması hakkında bilgi alın. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Roledefinitions/Read | Rol tanımı hakkında bilgi alın. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Read | Belirli bir knowledgeun listesini veya belirli bir knowledgeelleyici 'nin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/knowledgebases/Download/Read | Bilgi Bankası 'nı indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | Bilgi Bankası 'nı sorgulamak için GenerateAnswer çağrısı. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/alterations/Read | Çalışma zamanının değişikliklerini indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/endpointkeys/Read | Uç nokta için uç nokta anahtarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker/endpointsettings/Read | Uç nokta için uç nokta ayarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Read | Belirli bir knowledgeun listesini veya belirli bir knowledgeelleyici 'nin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/knowledgebases/Download/Read | Bilgi Bankası 'nı indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | Bilgi Bankası 'nı sorgulamak için GenerateAnswer çağrısı. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/alterations/Read | Çalışma zamanının değişikliklerini indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/endpointkeys/Read | Uç nokta için uç nokta anahtarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/qnamaker.exe v2/endpointsettings/Read | Uç nokta için uç nokta ayarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Read | Belirli bir knowledgeun listesini veya belirli bir knowledgeelleyici 'nin ayrıntılarını alır. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Download/Read | Bilgi Bankası 'nı indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/Action | Bilgi Bankası 'nı sorgulamak için GenerateAnswer çağrısı. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/Read | Çalışma zamanının değişikliklerini indirin. |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/Read | Uç nokta için uç nokta anahtarlarını alır |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/Read | Uç nokta için uç nokta ayarlarını alır |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Bilişsel hizmetler kullanıcısı

Bilişsel hizmetler 'in anahtarlarını okuyup listelemenizi sağlar. [Daha fazla bilgi edinin](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/ListKeys/Action | Anahtarları Listele |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/Read | Klasik ölçüm uyarısını okuyun |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Diagnosticsettings/Read | Kaynak tanılama ayarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Logdefinitions/Read | Günlük tanımlarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ölçüm tanımlarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ölçümleri oku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Dağıtım işlemlerini alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Biliveservices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Nesnelerin interneti


### <a name="device-update-administrator"></a>Cihaz Güncelleştirme Yöneticisi

Yönetim ve içerik işlemlerine tam erişim sağlar [daha fazla bilgi edinin](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Güncelleştirmelerle ilgili bir okuma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Write | Güncelleştirmelerle ilgili bir yazma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Delete | Güncelleştirmelerle ilgili silme işlemini gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Yönetim ile ilgili bir okuma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Write | Yönetim ile ilgili bir yazma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Delete | Yönetimiyle ilgili silme işlemini gerçekleştirir |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Cihaz güncelleştirme Içerik Yöneticisi

İçerik işlemlerine tam erişim sağlar [daha fazla bilgi](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Güncelleştirmelerle ilgili bir okuma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Write | Güncelleştirmelerle ilgili bir yazma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Delete | Güncelleştirmelerle ilgili silme işlemini gerçekleştirir |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Cihaz güncelleştirme Içeriği okuyucusu

İçerik işlemlerine yönelik okuma erişimi sağlar, ancak değişiklikler yapmaya izin vermez [daha fazla bilgi edinin](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Güncelleştirmelerle ilgili bir okuma işlemi gerçekleştirir |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Cihaz güncelleştirme dağıtımları Yöneticisi

Yönetim işlemlerine tam erişim sağlar [daha fazla bilgi](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Yönetim ile ilgili bir okuma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Write | Yönetim ile ilgili bir yazma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Delete | Yönetimiyle ilgili silme işlemini gerçekleştirir |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Cihaz güncelleştirme dağıtımları okuyucu

Yönetim işlemlerine yönelik okuma erişimi sağlar, ancak değişiklik yapmaya izin vermez [daha fazla bilgi edinin](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Yönetim ile ilgili bir okuma işlemi gerçekleştirir |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Cihaz güncelleştirme okuyucusu

Yönetim ve içerik işlemlerine yönelik okuma erişimi sağlar, ancak değişiklik yapmaya izin vermez [daha fazla bilgi edinin](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Güncelleştirmelerle ilgili bir okuma işlemi gerçekleştirir |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Yönetim ile ilgili bir okuma işlemi gerçekleştirir |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Karma gerçeklik


### <a name="remote-rendering-administrator"></a>Uzaktan Işleme Yöneticisi

Kullanıcıya dönüştürme, oturum yönetme, Azure uzaktan Işleme için işleme ve tanılama özellikleri sağlar [daha fazla bilgi edinin](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/Convert/Action | Varlık dönüştürmeyi Başlat |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/Convert/Read | Varlık dönüştürme özelliklerini al |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/Convert/Delete | Varlık dönüştürmeyi durdur |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/managesessions/Read | Oturum özelliklerini al |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/managesessions/Action | Başlangıç oturumları |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/managesessions/Delete | Oturumları durdur |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/render/Read | Bir oturuma bağlanma |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/Diagnostic/Read | Uzaktan Işleme denetçisine bağlanma |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Uzaktan Işleme Istemcisi

Azure uzaktan Işleme için Kullanıcı yönetme, işleme ve tanılama özellikleri sağlar. [Daha fazla bilgi edinin](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/managesessions/Read | Oturum özelliklerini al |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/managesessions/Action | Başlangıç oturumları |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/managesessions/Delete | Oturumları durdur |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/render/Read | Bir oturuma bağlanma |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/Remoterenderingaccounts/Diagnostic/Read | Uzaktan Işleme denetçisine bağlanma |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Uzamsal bağlayıcı hesabı Katılımcısı

Hesabınızdaki uzamsal bağlantıları yönetmenizi sağlar, ancak onları silmez [daha fazla bilgi edinin](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/action | Uzamsal bağlayıcı oluşturma |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Uzamsal Tutturucuların özelliklerini al |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Uzamsal bağlayıcıları bul |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Uzamsal Tutturucuların özelliklerini güncelleştirme |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Uzamsal bağlayıcı hesap sahibi

Hesabınızdaki uzamsal bağlantıları yönetmenizi sağlar, böylece bunları silme [hakkında daha fazla bilgi edinin](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/action | Uzamsal bağlayıcı oluşturma |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Delete | Uzamsal bağlayıcıları Sil |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Uzamsal Tutturucuların özelliklerini al |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Uzamsal bağlayıcıları bul |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Uzamsal Tutturucuların özelliklerini güncelleştirme |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Uzamsal bağlayıcı hesap okuyucu

Hesabınızdaki uzamsal Tutturucuların özelliklerini bulmanıza ve okumanızı sağlar [daha fazla bilgi edinin](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Yakın uzamsal bağlayıcıları bul |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Uzamsal Tutturucuların özelliklerini al |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Uzamsal bağlayıcıları bul |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Azure uzamsal bağlayıcı hizmeti 'nin kalitesini artırmaya yardımcı olmak için tanılama verileri gönderme |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Tümleştirme


### <a name="api-management-service-contributor"></a>API Management hizmet Katılımcısı

Hizmeti ve API 'Leri yönetebilir [daha fazla bilgi](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Apimanan](resource-provider-operations.md#microsoftapimanagement)/Service/* | API Management hizmeti oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Hizmet Işletmeni rolü API Management

Hizmeti yönetebilir, ancak API 'Leri yönetebilir [daha fazla bilgi edinin](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Apimanan](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | API Management hizmeti örneklerini oku |
> | [Microsoft. Apimana,](resource-provider-operations.md#microsoftapimanagement)/Service/Backup/Action | API Management hizmetini Kullanıcı tarafından sağlanmış bir depolama hesabında belirtilen kapsayıcıya Yedekle |
> | [Microsoft. Apimana,](resource-provider-operations.md#microsoftapimanagement)/Service/Delete | API Management hizmeti örneğini Sil |
> | [Microsoft. Apimana,](resource-provider-operations.md#microsoftapimanagement)/Service/managedeployments/Action | SKU/birimleri değiştirme, API Management hizmetinin bölgesel dağıtımlarını ekleme/kaldırma |
> | [Microsoft. Apimanaya](resource-provider-operations.md#microsoftapimanagement)/Service/Read | API Management hizmet örneği için meta verileri oku |
> | [Microsoft. Apimana,](resource-provider-operations.md#microsoftapimanagement)/Service/restore/Action | API Management hizmetini Kullanıcı tarafından sağlanmış bir depolama hesabındaki belirtilen kapsayıcıdan geri yükle |
> | [Microsoft. Apimana,](resource-provider-operations.md#microsoftapimanagement)/Service/updatecertificate/Action | API Management hizmeti için TLS/SSL sertifikası yükleme |
> | [Microsoft. Apimana,](resource-provider-operations.md#microsoftapimanagement)/Service/updatehostname/Action | API Management hizmeti için özel etki alanı adlarını kurma, güncelleştirme veya kaldırma |
> | [Microsoft. Apimanaya](resource-provider-operations.md#microsoftapimanagement)/Service/Write | API Management hizmet örneği oluştur veya güncelleştir |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | [Microsoft. Apimanami](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Kullanıcıyla ilişkili anahtarları al |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management hizmeti okuyucu rolü

Hizmet ve API 'lere salt okuma erişimi [daha fazla bilgi](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Apimanan](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | API Management hizmeti örneklerini oku |
> | [Microsoft. Apimanaya](resource-provider-operations.md#microsoftapimanagement)/Service/Read | API Management hizmet örneği için meta verileri oku |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | [Microsoft. Apimanami](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Kullanıcıyla ilişkili anahtarları al |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Uygulama yapılandırma veri sahibi

Uygulama yapılandırma verilerine tam erişim sağlar. [Daha fazla bilgi edinin](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/Configurationstores/*/Read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/Configurationstores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/Configurationstores/*/Delete |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Uygulama yapılandırma veri okuyucusu

Uygulama yapılandırma verilerine okuma erişimi sağlar. [Daha fazla bilgi edinin](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/Configurationstores/*/Read |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus veri sahibi

Azure Service Bus kaynaklara tam erişim sağlar. [Daha fazla bilgi edinin](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus veri alıcısı

Azure Service Bus kaynaklarına erişim izni verir. [Daha fazla bilgi edinin](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Veri Göndericisini Azure Service Bus

Azure Service Bus kaynaklarına erişim izni verir. [Daha fazla bilgi edinin](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Kayıt sahibini Azure Stack

Azure Stack kayıtlarını yönetmenizi sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Edgesubscriptions/Read | Azure Stack Edge aboneliğinin özelliklerini al |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Registrations/Products/*/Action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Registrations/Products/Read | Azure Stack Market ürününün özelliklerini alır |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Registrations/Read | Azure Stack kaydın özelliklerini alır |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-contributor"></a>EventGrid Katılımcısı

EventGrid işlemlerini yönetmenizi sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Event Grid kaynaklarını oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Katılımcısı

EventGrid olay aboneliği işlemlerini yönetmenizi sağlar. [Daha fazla bilgi edinin](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/EventSubscriptions/* | Bölgesel olay abonelikleri oluşturma ve yönetme |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Topictypes/EventSubscriptions/Read | Küresel olay aboneliklerini konu türüne göre Listele |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/EventSubscriptions/Read | Bölgesel olay aboneliklerini listeleme |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topictypes/EventSubscriptions/Read | TopicType 'a göre bölgesel olay aboneliklerini listeleyin |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription okuyucusu

EventGrid olay aboneliklerini okumanızı sağlar. [Daha fazla bilgi edinin](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/EventSubscriptions/Read | Bir eventSubscription okuma |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Topictypes/EventSubscriptions/Read | Küresel olay aboneliklerini konu türüne göre Listele |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/EventSubscriptions/Read | Bölgesel olay aboneliklerini listeleme |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topictypes/EventSubscriptions/Read | TopicType 'a göre bölgesel olay aboneliklerini listeleyin |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHıR verileri Katılımcısı

Rol, Kullanıcı veya sorumlunun FHıR verilerine tam erişimini sağlar [daha fazla bilgi edinin](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Healthgelişme API 'leri/Hizmetleri/fhır/kaynaklar/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHıR veri aktarıcı

Rol, kullanıcının veya sorumlunun FHıR verilerini okumasına ve dışa almasına izin verir [daha fazla bilgi](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Healthgelişme API 'leri/Hizmetleri/fhır/kaynaklar/okuma | FHıR kaynaklarını okuyun (arama ve sürümü tutulan geçmişi içerir).  |
> | Microsoft. Healthgelişme API 'leri/Hizmetleri/fhır/kaynaklar/dışarı aktarma/eylem | Dışarı aktarma işlemi ($export). |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHıR veri okuyucu

Rol, kullanıcının veya sorumlunun FHıR verilerini okumasına izin verir [daha fazla bilgi edinin](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Healthgelişme API 'leri/Hizmetleri/fhır/kaynaklar/okuma | FHıR kaynaklarını okuyun (arama ve sürümü tutulan geçmişi içerir).  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHıR veri yazıcısı

Rol, kullanıcının veya sorumlunun FHıR verilerini okumasına ve yazmasına izin verir [daha fazla bilgi edinin](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | Microsoft. Healthgelişme API 'leri/Hizmetleri/fhır/kaynaklar/* |  |
> | **NotDataActions** |  |
> | Microsoft. Healthgelişme API 'leri/Hizmetleri/fhır/kaynaklar/hardDelete/ACTION | Sabit silme (sürüm geçmişi dahil). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Katkıda bulunan Tümleştirme Hizmeti Ortamı

Tümleştirme hizmeti ortamlarını yönetmenize izin verir, ancak bunlara erişimi kalmaz. [Daha fazla bilgi edinin](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/ıntegrationserviceenvironments/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Tümleştirme Hizmeti Ortamı geliştirici

Geliştiricilerin, tümleştirme hizmeti ortamlarında iş akışları, tümleştirme hesapları ve API bağlantıları oluşturmalarına ve güncelleştirmesine izin verir. [Daha fazla bilgi edinin](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/ıntegrationserviceenvironments/Read | Tümleştirme hizmeti ortamını okur. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/ıntegrationserviceenvironments/*/Join/Action |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems hesabı Katılımcısı

Akıllı sistem hesaplarını yönetmenizi sağlar ancak onlara yönelik erişimleri vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | Microsoft. ıntelligentsystems/accounts/* | Akıllı sistem hesapları oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Mantıksal uygulama Katılımcısı

Mantıksal uygulamaları yönetmenize izin verir, ancak bunlara erişimi değiştirmeyin. [Daha fazla bilgi edinin](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/ListKeys/Action | Depolama hesaplarının erişim anahtarlarını listeler. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/Storageaccounts/Read | Verilen hesaba sahip depolama hesabını döndürün. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metricalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Diagnosticsettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. Etkinlik günlüğündeki günlük kategorilerini listeleyin. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Ölçüm tanımlarını oku (bir kaynak için kullanılabilen ölçüm türlerinin listesi). |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Logic Apps kaynaklarını yönetir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connectiongateways/* | Bir bağlantı ağ geçidi oluşturun ve yönetir. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Bir bağlantı oluşturun ve yönetir. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Özel bir API oluşturur ve yönetir. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Serverfarms/Join/Action | Bir App Service planına katılır |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Serverfarms/Read | App Service planında özellikleri al |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Sites/Functions/listsecrets/Action | Işlev gizli dizileri listeleyin. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic App Işleci

Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremez. [Daha fazla bilgi edinin](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/*/Read | Öngörüler uyarı kurallarını okuyun |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metricalerts/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Diagnosticsettings/*/Read | Logic Apps için tanılama ayarlarını alır |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/*/Read | Logic Apps için kullanılabilir ölçümleri alır. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Logic Apps kaynaklarını okur. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Disable/Action | İş akışını devre dışı bırakır. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Enable/Action | İş akışını izin vermez. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Validate/Action | İş akışını doğrular. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Dağıtım işlemlerini alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connectiongateways/*/Read | Bağlantı ağ geçitlerini okuyun. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Bağlantıları oku. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Özel API 'YI okuyun. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Serverfarms/Read | App Service planında özellikleri al |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Kimlik


### <a name="managed-identity-contributor"></a>Yönetilen kimlik Katılımcısı

Kullanıcı tarafından atanan kimlik oluşturma, okuma, güncelleştirme ve silme [daha fazla bilgi](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Managedıdentity](resource-provider-operations.md#microsoftmanagedidentity)/Useratandıdentitıes/Read | Mevcut kullanıcı tarafından atanan kimliği alır |
> | [Microsoft. Managedıdentity](resource-provider-operations.md#microsoftmanagedidentity)/Useratandıdentitıes/Write | Yeni Kullanıcı tarafından atanmış bir kimlik oluşturur veya var olan bir kullanıcı tarafından atanan kimlikle ilişkili etiketleri güncelleştirir |
> | [Microsoft. Managedıdentity](resource-provider-operations.md#microsoftmanagedidentity)/Useratandıdentitıes/Delete | Mevcut kullanıcı tarafından atanan bir kimliği siler |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Yönetilen kimlik Işleci

Kullanıcı tarafından atanan kimliği okuma ve atama [daha fazla bilgi](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Managedıdentity](resource-provider-operations.md#microsoftmanagedidentity)/Useratandıdentities/*/Read |  |
> | [Microsoft. Managedıdentity](resource-provider-operations.md#microsoftmanagedidentity)/Useratandıdentitıes/*/atama/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Güvenlik


### <a name="attestation-contributor"></a>Kanıtlama Katılımcısı

Kanıtlama sağlayıcısı örneğini okuma veya silme [hakkında daha fazla bilgi](../attestation/quickstart-powershell.md) edinebilirsiniz

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. kanıtlama/attestationProviders/kanıtlama/okuma |  |
> | Microsoft. kanıtlama/attestationProviders/kanıtlama/yazma |  |
> | Microsoft. kanıtlama/attestationProviders/kanıtlama/silme |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Kanıtlama okuyucusu

Kanıtlama sağlayıcısı özelliklerini okuyabilir [daha fazla bilgi edinin](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. kanıtlama/attestationProviders/kanıtlama/okuma |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel Otomasyon Katılımcısı

Azure Sentinel Otomasyon katılımcısı [daha fazla bilgi](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Triggers/Read | Tetikleyiciyi okur. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Triggers/listcallbackurl/Action | Tetikleyici için geri çağırma URL 'sini alır. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Runs/Read | İş akışı çalıştırmasını okur. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Azure Sentinel Katkıda Bulunanı

Azure Sentinel katılımcısı [daha fazla bilgi](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Yeni altyapıyı kullanarak arama yapın. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics verilerini görüntüleme |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | OMS çözümünü çıkmadan al |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Özel çalışma kitabını okuma |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel Okuyucusu

Azure Sentinel okuyucu [daha fazla bilgi](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Dataconnectorscheckgereksinims/Action | Kullanıcı yetkilendirmesini ve lisansını denetle |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/Indicators/Query/Action | Sorgu tehdit bilgileri göstergeleri |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/queryındicators/Action | Sorgu tehdit bilgileri göstergeleri |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Yeni altyapıyı kullanarak arama yapın. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics verilerini görüntüleme |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/LinkedServices/Read | Belirtilen çalışma alanı altındaki bağlı hizmetleri alın. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Kayıtlı bir arama sorgusu alır |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | OMS çözümünü çıkmadan al |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Çalışma kitabını okuma |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Özel çalışma kitabını okuma |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel Yanıtlayıcısı

Azure Sentinel Yanıtlayıcı [daha fazla bilgi](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Dataconnectorscheckgereksinims/Action | Kullanıcı yetkilendirmesini ve lisansını denetle |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Automationkurallarını/* |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/* |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/Indicators/appendtags/Action | Tehdit zekası göstergesine Etiketler Ekle |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/Indicators/Query/Action | Sorgu tehdit bilgileri göstergeleri |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/bulakg/Action | Toplu Etiketler tehdit bilgileri |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/Indicators/appendtags/Action | Tehdit zekası göstergesine Etiketler Ekle |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/Indicators/replacetags/Action | Tehdit zekası göstergesinin etiketlerini değiştirme |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Threatıntelligence/queryındicators/Action | Sorgu tehdit bilgileri göstergeleri |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Yeni altyapıyı kullanarak arama yapın. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics verilerini görüntüleme |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Kayıtlı bir arama sorgusu alır |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | OMS çözümünü çıkmadan al |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Çalışma alanındaki veriler üzerinde sorgu çalıştırma |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Bir çalışma alanı altındaki veri kaynaklarını alın. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Çalışma kitabını okuma |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Özel çalışma kitabını okuma |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/*/Delete |  |
> | [Microsoft. Securityınsights](resource-provider-operations.md#microsoftsecurityinsights)/Incidents/*/Delete |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault Yöneticisi

Tüm veri düzlemi işlemlerini bir anahtar kasasında ve içindeki tüm nesneleri (sertifikalar, anahtarlar ve gizli diziler dahil) gerçekleştirin. Anahtar Kasası kaynakları yönetemez veya rol atamaları yönetilemez. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Anahtar Kasası adının geçerli olduğunu ve kullanımda olup olmadığını denetler |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Deletedvaults/Read | Geçici olarak silinen anahtar kasalarının özelliklerini görüntüleyin |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Microsoft. Keykasası kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault sertifikaları müdürü

İzinleri Yönet dışında bir anahtar kasasının sertifikaları üzerinde herhangi bir işlem gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Anahtar Kasası adının geçerli olduğunu ve kullanımda olup olmadığını denetler |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Deletedvaults/Read | Geçici olarak silinen anahtar kasalarının özelliklerini görüntüleyin |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Microsoft. Keykasası kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/certificatecas/* |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Certificates/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Katkıda bulunan Key Vault

Anahtar kasalarını yönetin, ancak Azure RBAC 'de roller atamanıza izin vermez ve gizli dizi, anahtar veya sertifikalara erişmenize izin vermez. [Daha fazla bilgi edinin](../key-vault/general/security-features.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Locations/deletedVaults/Purge/Action | Geçici olarak silinen bir anahtar kasasını temizle |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Managedhsms/* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Key Vault şifre müdürü

Anahtar kasasının anahtarları üzerinde, izinleri yönet dışında herhangi bir işlem gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Anahtar Kasası adının geçerli olduğunu ve kullanımda olup olmadığını denetler |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Deletedvaults/Read | Geçici olarak silinen anahtar kasalarının özelliklerini görüntüleyin |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Microsoft. Keykasası kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault şifreleme hizmeti şifreleme kullanıcısı

Anahtarların meta verilerini okuyun ve sarmalama/sarmalama işlemleri gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/EventSubscriptions/Write | EventSubscription oluşturma veya güncelleştirme |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/EventSubscriptions/Read | Bir eventSubscription okuma |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/EventSubscriptions/Delete | Bir eventSubscription silme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Read | Belirtilen kasadaki anahtarları listeleyin veya bir anahtarın özelliklerini ve genel malzemesini okuyun. Asimetrik anahtarlar için bu işlem ortak anahtarı kullanıma sunar ve imzayı şifrelemek ve doğrulamak gibi ortak anahtar algoritmaları gerçekleştirme olanağını içerir. Özel anahtarlar ve simetrik anahtarlar hiçbir şekilde gösterilmez. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Wrap/Action | Bir simetrik anahtarı Key Vault anahtarla kaydırır. Key Vault anahtarı asimetrik ise, bu işlemin okuma erişimi olan sorumlular tarafından gerçekleştirilebileceğini unutmayın. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Unwrap/Action | Bir simetrik anahtarın Key Vault anahtarla sarmalanmış olduğunu kaldırır. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault şifrelenmiş Kullanıcı

Anahtarları kullanarak şifreleme işlemleri gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Read | Belirtilen kasadaki anahtarları listeleyin veya bir anahtarın özelliklerini ve genel malzemesini okuyun. Asimetrik anahtarlar için bu işlem ortak anahtarı kullanıma sunar ve imzayı şifrelemek ve doğrulamak gibi ortak anahtar algoritmaları gerçekleştirme olanağını içerir. Özel anahtarlar ve simetrik anahtarlar hiçbir şekilde gösterilmez. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Update/Action | Verilen anahtarla ilişkili belirtilen öznitelikleri güncelleştirir. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Backup/Action | Bir anahtarın yedek dosyasını oluşturur. Dosya, aynı aboneliğin Key Vault anahtarı geri yüklemek için kullanılabilir. Kısıtlamalar uygulanabilir. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Encrypt/Action | Bir anahtarla düz metin şifreler. Anahtar asimetrik ise, bu işlemin okuma erişimi olan sorumlular tarafından gerçekleştirilebileceğini unutmayın. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Decrypt/Action | Bir anahtarla şifreli metin şifresini çözer. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Wrap/Action | Bir simetrik anahtarı Key Vault anahtarla kaydırır. Key Vault anahtarı asimetrik ise, bu işlemin okuma erişimi olan sorumlular tarafından gerçekleştirilebileceğini unutmayın. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Unwrap/Action | Bir simetrik anahtarın Key Vault anahtarla sarmalanmış olduğunu kaldırır. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Sign/Action | Bir ileti özetini (karma) bir anahtarla imzalar. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Verify/Action | Bir ileti özetinin (karma) imzasını bir anahtarla doğrular. Anahtar asimetrik ise, bu işlemin okuma erişimi olan sorumlular tarafından gerçekleştirilebileceğini unutmayın. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault okuyucu

Anahtar kasalarının ve sertifika, anahtar ve gizli dizileri için meta verileri okuyun. Gizli içerik veya anahtar malzeme gibi hassas değerler okunamaz. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Anahtar Kasası adının geçerli olduğunu ve kullanımda olup olmadığını denetler |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Deletedvaults/Read | Geçici olarak silinen anahtar kasalarının özelliklerini görüntüleyin |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Microsoft. Keykasası kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/ReadMetadata/Action | Gizli dizi özelliklerini listeleyin veya görüntüleyin, ancak değerini değil. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault gizlilikler müdürü

Anahtar kasasının gizli dizileri üzerinde, izinleri yönet dışında herhangi bir işlem gerçekleştirin. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Anahtar Kasası adının geçerli olduğunu ve kullanımda olup olmadığını denetler |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Deletedvaults/Read | Geçici olarak silinen anahtar kasalarının özelliklerini görüntüleyin |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Microsoft. Keykasası kaynak sağlayıcısında kullanılabilir işlemleri listeler |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault gizli dizi kullanıcısı

Gizli dizi içeriğini okuyun. Yalnızca ' Azure rol tabanlı erişim denetimi ' izin modelini kullanan anahtar kasaları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/getSecret/Action | Gizli dizi değerini alır. |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/ReadMetadata/Action | Gizli dizi özelliklerini listeleyin veya görüntüleyin, ancak değerini değil. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Yönetilen HSM Katılımcısı

Yönetilen HSM havuzlarını yönetmenize izin verir, ancak bunlara erişimi olmaz. [Daha fazla bilgi edinin](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Keykasası](resource-provider-operations.md#microsoftkeyvault)/Managedhsms/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Güvenlik Yöneticisi

Güvenlik Merkezi için izinleri görüntüleyin ve güncelleştirin. Güvenlik okuyucu rolüyle aynı izinler ve ayrıca güvenlik ilkesini güncelleştirebilir ve uyarıları ve önerileri kapatabilir. [Daha fazla bilgi edinin](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Policyassignments/* | İlke atamaları oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | İlke tanımları oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Policymuaf tions/* | İlke muafiyetlerini oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Policysetdefinitions/* | İlke kümeleri oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics verilerini görüntüleme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Güvenlik bileşenleri ve ilkeleri oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Güvenlik değerlendirmesi Katılımcısı

Değerlendirmelere Güvenlik Merkezi 'ne gönderim olanağı sağlar

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/assessments/Write | Aboneliğinizde güvenlik değerlendirmeleri oluşturun veya güncelleştirin |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Güvenlik Yöneticisi (eski)

Bu eski bir roldür. Lütfen bunun yerine Güvenlik Yöneticisi 'ni kullanın.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Yapılandırma bilgileri klasik sanal makineler 'i okuyun |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/Virtual, ines/*/Write | Klasik sanal makineler için yazma yapılandırması |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Klasik ağla ilgili yapılandırma bilgilerini okuyun |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Güvenlik bileşenleri ve ilkeleri oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Güvenlik okuyucusu

Güvenlik Merkezi için izinleri görüntüleyin. Önerileri, uyarıları, güvenlik ilkesini ve güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz. [Daha fazla bilgi edinin](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/Read | Klasik ölçüm uyarısını okuyun |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics verilerini görüntüleme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Güvenlik bileşenlerini ve ilkelerini okuyun |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/Iotsavunma Dersettings/packagedownloads/Action | İndirilebilir IoT Defender paketleri bilgilerini alır |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/Iotsavunma Dersettings/downloadmanageractivation/Action | Abonelik kota verileri içeren yönetici etkinleştirme dosyasını indir |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/Iotsensors/downloadresetpassword/Action | IoT sensörleri için parola sıfırlama dosyası indirilir |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs kullanıcısı

Azure DevTest Labs sanal makinelerinizi bağlamanıza, başlatmanıza, yeniden başlatmanıza ve kapatımanıza olanak sağlar. [Daha fazla bilgi edinin](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/kullanılabilirliği Bilitysets/Read | Bir kullanılabilirlik kümesinin özelliklerini al |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, ines/*/Read | Bir sanal makinenin özelliklerini okuyun (VM boyutları, çalışma zamanı durumu, VM uzantıları vb.) |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/anlaşlocate/Action | Sanal makineyi güçlendirir ve işlem kaynaklarını serbest bırakır |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/Read | Bir sanal makinenin özelliklerini al |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/Action | Sanal makineyi yeniden başlatır |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Virtual, Ines/start/Action | Sanal makineyi başlatır |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/Read | Laboratuvarın özelliklerini okuyun |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimanyvm/Action | Laboratuvarda rastgele bir çakışan sanal makine talep edin. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/createenvironment/Action | Laboratuvarda sanal makineler oluşturun. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensurecurrentuserprofile/Action | Geçerli kullanıcının laboratuvarda geçerli bir profile sahip olduğundan emin olun. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formüls/Delete | Formülleri silin. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formüls/okuma | Formülleri okuyun. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formüls/Write | Formüller ekleme veya değiştirme. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Laboratuvar ilkesini değerlendirir. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtuallarines/Claim/Action | Mevcut bir sanal makinenin sahipliğini al |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualmachines/listApplicableSchedules/Action | Varsa, geçerli başlatma/durdurma zamanlamalarını listeler. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Sanal makine için RDP dosyasının içeriğini temsil eden bir dize alır |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/backendadddresspools/Join/Action | Yük dengeleyici arka uç adres havuzunu birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Loadbalancers/ınboundnatrules/Join/Action | Yük dengeleyici gelen NAT kuralına katılır. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/*/Read | Bir ağ arabiriminin özelliklerini okuyun (örneğin, ağ arabiriminin bir parçası olduğu tüm yük dengeleyiciler) |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Join/Action | Bir sanal makineyi bir ağ arabirimine birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Read | Bir ağ arabirimi tanımını alır.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/NetworkInterfaces/Write | Ağ arabirimi oluşturur veya var olan bir ağ arabirimini güncelleştirir.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/*/Read | Genel IP adresinin özelliklerini okuyun |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/Join/Action | Genel bir IP adresini birleştirir. Alertable değil. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Publicıpaddresses/Read | Genel IP adresi tanımını alır. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Subnets/Join/Action | Bir sanal ağı birleştirir. Alertable değil. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Dağıtım işlemlerini alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Dağıtımları alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | **NotActions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/Read | Sanal makinenin güncelleştirileceği kullanılabilir boyutları listeler |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Laboratuvar Oluşturucu

Azure Laboratuvar hesaplarınız altında yeni laboratuvarlar oluşturmanızı sağlar. [Daha fazla bilgi edinin](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/Labaccounts/*/Read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/Labaccounts/createlab/Action | Laboratuvar hesabında laboratuvar oluşturun. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Laboratuvar hesabı için boyut, coğrafi grafik ve işletim sistemi birleşimlerinin fiyatlandırmasını ve kullanılabilirliğini öğrenin. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Bu abonelik için çekirdek kısıtlamaları ve kullanımı al |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>İzleyici


### <a name="application-insights-component-contributor"></a>Application Insights bileşeni Katılımcısı

Application Insights bileşenlerini yönetebilir [daha fazla bilgi](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik uyarı kuralları oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Generatelivetoken/Read | Canlı ölçümler belirteç al |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metricalerts/* | Yeni uyarı kuralları oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Öngörüler bileşenleri oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledquerykurallarını/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/topologyı/Read | Okuma topolojisi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Transactions/Read | Işlemleri oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Öngörüler Web testleri oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Application Insights Snapshot Debugger ile toplanan hata ayıklama anlık görüntülerini görüntülemek ve indirmek için kullanıcıya izin verir. Bu izinlerin [sahip](#owner) veya [katkıda](#contributor) bulunan rollerine dahil edilmediğini unutmayın. Kullanıcılara Application Insights Snapshot Debugger rolü verirken, rolü doğrudan kullanıcıya vermeniz gerekir. Rol, özel bir role eklendiğinde tanınmaz. [Daha fazla bilgi edinin](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Katkıda bulunan izleniyor

Tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Daha fazla bilgi edinin](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Activitylogalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Öngörüler bileşenleri oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/datacollectionkuralları/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Datacollectionruleassociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Analiz Sunucusu için tanılama ayarını oluşturur, güncelleştirir veya okur |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/eventTypes/* | Bir abonelikteki etkinlik günlüğü olaylarını (yönetim olayları) listeleyin. Bu izin, etkinlik günlüğüne hem programlı hem de portala erişim için geçerlidir. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Logdefinitions/* | Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. Etkinlik günlüğündeki günlük kategorilerini listeleyin. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Ölçüm tanımlarını oku (bir kaynak için kullanılabilen ölçüm türlerinin listesi). |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Bir kaynak için ölçümleri okuyun. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights sağlayıcısını kaydedin |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledquerykurallarını/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Öngörüler Web testleri oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Privatelinkscopes/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Privatelinkscopeoperationkara ses/* |  |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Write | Mevcut çalışma alanından müşteri kimliğini sağlayarak yeni bir çalışma alanı veya mevcut bir çalışma alanına bağlantılar oluşturur. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/intelligencepacks/* | Log Analytics çözüm paketlerini okuma/yazma/silme. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* | Log Analytics kayıtlı aramalarını okuma/yazma/silme. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Arama sorgusu yürütür |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Action | Çalışma alanının paylaşılan anahtarlarını alır. Bu anahtarlar, Microsoft operasyonel içgörüler aracılarını çalışma alanına bağlamak için kullanılır. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/storageinsightconfigs/* | Log Analytics depolama öngörülerini okuma/yazma/silme. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Konuk VM sistem durumu izleyicileri hakkında bilgi alın. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Smartdetectoralertrules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Actionkurallarını/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Smartgroups/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Ölçüm yayımcısını izleme

Azure kaynaklarında ölçüm yayımlamaya izin verebilir [daha fazla bilgi](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights sağlayıcısını kaydedin |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Ölçümleri yaz |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>İzleme okuyucusu

Tüm izleme verilerini okuyabilir (ölçümler, Günlükler vb.). Ayrıca bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Daha fazla bilgi edinin](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Operationalınsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Arama sorgusu yürütür |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Çalışma kitabı Katılımcısı

, Paylaşılan çalışma kitaplarını kaydedebilir. [Daha fazla bilgi edinin](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Write | Çalışma kitabı oluşturma veya güncelleştirme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Delete | Bir çalışma kitabını silme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Çalışma kitabını okuma |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Çalışma kitabı okuyucusu

, Çalışma kitaplarını okuyabilir. [Daha fazla bilgi edinin](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Çalışma kitabını okuma |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Yönetim + idare


### <a name="automation-job-operator"></a>Automation Iş Işleci

Otomasyon Runbook 'Larını kullanarak Iş oluşturun ve yönetin. [Daha fazla bilgi edinin](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/hybridrunbookworkergroups/Read | Karma runbook çalışanı kaynaklarını okur |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/Read | Bir Azure Otomasyonu işini alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/Resume/Action | Bir Azure Otomasyonu işini sürdürür |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/stop/Action | Bir Azure Otomasyonu işini durduruyor |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/streamms/Read | Bir Azure Otomasyonu iş akışı alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Bir Azure Otomasyonu işini askıya alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/Write | Azure Otomasyonu işi oluşturur |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/output/Read | Bir işin çıkışını alır |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Otomasyon Operatörü

Automation Işleçleri, işleri başlatabilir, durdurabilir, askıya alabilir ve sürdürebilir [daha fazla bilgi edinin](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/hybridrunbookworkergroups/Read | Karma runbook çalışanı kaynaklarını okur |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/Read | Bir Azure Otomasyonu işini alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/Resume/Action | Bir Azure Otomasyonu işini sürdürür |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/stop/Action | Bir Azure Otomasyonu işini durduruyor |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/streamms/Read | Bir Azure Otomasyonu iş akışı alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Bir Azure Otomasyonu işini askıya alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/Write | Azure Otomasyonu işi oluşturur |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Read | Bir Azure Otomasyonu iş zamanlaması alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Write | Bir Azure Otomasyonu iş zamanlaması oluşturur |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/linkedworkspace/Read | Otomasyon hesabına bağlı çalışma alanını alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Read | Bir Azure Otomasyonu hesabını alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/runbooks/Read | Bir Azure Otomasyonu runbook 'unu alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Read | Bir Azure Otomasyonu zamanlama varlığını alır |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Write | Bir Azure Otomasyonu zamanlama varlığı oluşturur veya güncelleştirir |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/Jobs/output/Read | Bir işin çıkışını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Otomasyon Runbook Işleci

Runbook 'un Işlerini oluşturabilmek için Runbook özelliklerini okuyun. [Daha fazla bilgi edinin](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/Automationaccounts/runbooks/Read | Bir Azure Otomasyonu runbook 'unu alır |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc etkin Kubernetes kümesi Kullanıcı rolü

Küme kullanıcı kimlik bilgilerini Listele eylemi.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/listclusterusercredentials/Action | ClusterUser kimlik bilgisini Listele |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes Yöneticisi

Kaynak kotalarını ve ad alanlarını güncelleştirme veya silme dışında, küme/ad alanı altındaki tüm kaynakları yönetmenizi sağlar. [Daha fazla bilgi edinin](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/controllerrevisions/Read | Controllerdüzeltmelerinizi okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Apps/daemonsets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/Deployments/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/replicasets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/statefulsets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Authorization.k8s.io/localsubjectaccessreviews/Write | Localsubjectaccessincelemeleri yazar |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/otomatik Scaling/horizontalpodavutoscalers/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Batch/cronjobs/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Batch/Jobs/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/configmaps/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Events.k8s.io/Events/Read | Olayları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Events/Read | Olayları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/Deployments/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/ingresses/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/networkpolicies/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/replicasets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/limitranges/Read | Limitaralıkları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/Read | Ad alanlarını okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Networking.k8s.io/ingresses/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Networking.k8s.io/networkpolicies/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/persistentvolumeclaims/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/pods/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/RBAC.Authorization.k8s.io/rolebindings/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/RBAC.Authorization.k8s.io/Roles/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/replicationcontrollers/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/replicationcontrollers/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/resourcequotas/Read | Resourcequotas okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Secrets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/serviceaccounts/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Services/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc Kubernetes Küme Yöneticisi

Kümedeki tüm kaynakları yönetmenizi sağlar. [Daha fazla bilgi edinin](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc Kubernetes Görüntüleyici

Gizli dizileri hariç küme/ad alanındaki tüm kaynakları görüntülemenize izin verir. [Daha fazla bilgi edinin](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/controllerrevisions/Read | Controllerdüzeltmelerinizi okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Apps/daemonsets/Read | Daemonsets okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/Deployments/Read | Dağıtımları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/replicasets/Read | Replicasets 'i okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/statefulsets/Read | Statefulsets 'i okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/otomatik Scaling/horizontalpodadutoscalers/Read | Horizontalpodadutoscalers okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Batch/cronjobs/Read | Cronjobs okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Batch/Jobs/Read | İşleri okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/configmaps/Read | Configmaps 'u okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/Read | Uç noktaları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Events.k8s.io/Events/Read | Olayları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Events/Read | Olayları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Extensions/daemonsets/Read | Daemonsets okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/Deployments/Read | Dağıtımları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/ingresses/Read | Gelen parolaları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/networkpolicies/Read | Networkpolicies okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/replicasets/Read | Replicasets 'i okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/limitranges/Read | Limitaralıkları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/Read | Ad alanlarını okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Networking.k8s.io/ingresses/Read | Gelen parolaları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Networking.k8s.io/networkpolicies/Read | Networkpolicies okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/persistentvolumeclaims/Read | Persistentvolumeclaim okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/pods/Read | Pod 'leri okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Policy/poddisruptionbudgets/Read | Pod kesintiden oluşan bütçeleri okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/replicationcontrollers/Read | Replicationcontrollers okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/replicationcontrollers/Read | Replicationcontrollers okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/resourcequotas/Read | Resourcequotas okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/serviceaccounts/Read | Hizmet hesaplarını okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Services/Read | Hizmetleri okur |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes yazıcı

Küme/ad alanı (küme) rolleri ve (küme) rolü bağlamaları dışındaki her şeyi güncelleştirmenizi sağlar. [Daha fazla bilgi edinin](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/controllerrevisions/Read | Controllerdüzeltmelerinizi okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Apps/daemonsets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/Deployments/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/replicasets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Apps/statefulsets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/otomatik Scaling/horizontalpodavutoscalers/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Batch/cronjobs/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Batch/Jobs/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/configmaps/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Events.k8s.io/Events/Read | Olayları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Events/Read | Olayları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/Deployments/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/ingresses/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/networkpolicies/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Extensions/replicasets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/limitranges/Read | Limitaralıkları okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/Read | Ad alanlarını okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Networking.k8s.io/ingresses/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Networking.k8s.io/networkpolicies/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/persistentvolumeclaims/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/pods/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/replicationcontrollers/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/replicationcontrollers/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/resourcequotas/Read | Resourcequotas okur |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Secrets/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/serviceaccounts/* |  |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Services/* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure bağlı makine ekleme

, Azure bağlantılı makineler ekleyebilir. [Daha fazla bilgi edinin](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/, ines/Read | Tüm Azure Arc makinelerini okuyun |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/, ines/Write | Bir Azure Arc makinesi yazar |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Privatelinkscopes/Read | Tüm Azure Arc privateLinkScopes 'ı okuyun |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/Guestconfigurationatamaments/Read | Konuk yapılandırma atamasını al. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/privateLinkScopes/read",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure bağlı makine kaynak yöneticisi

Azure bağlı makinelerini okuyabilir, yazabilir, silebilir ve yeniden ekleyebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/, ines/Read | Tüm Azure Arc makinelerini okuyun |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/, ines/Write | Bir Azure Arc makinesi yazar |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/, ines/Delete | Azure yay makinelerini siler |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/larines/Extensions/Write | Bir Azure Arc uzantısını yükleme veya güncelleştirme |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Privatelinkscopes/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/privateLinkScopes/*",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Faturalama Okuyucusu

Faturalandırma verilerine okuma erişimi sağlar [daha fazla bilgi](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. faturalandırma](resource-provider-operations.md#microsoftbilling)/*/Read | Fatura bilgilerini okuyun |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Microsoft. tüketim](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Blueprint Katılımcısı

Şema tanımlarını yönetebilir, ancak atamazsınız. [Daha fazla bilgi edinin](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/Blueprints/* | Şema tanımlarını veya şema yapılarını oluşturun ve yönetin. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Blueprint Işleci

Varolan yayımlanmış şemaları atayabilir, ancak yeni şemaları oluşturamaz. Bu, yalnızca atama Kullanıcı tarafından atanan yönetilen bir kimlikle yapıldığında işe yarar. [Daha fazla bilgi edinin](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/Blueprintassignments/* | Şema atamaları oluşturun ve yönetin. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Maliyet Yönetimi Katkıda Bulunanı

Maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir (örn. bütçeler, dışarı aktarmalar) [daha fazla bilgi edinin](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. tüketim](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft. faturalandırma](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Configurations/Read | Yapılandırma al |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/Read | Önerileri okur |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | [Microsoft. faturalandırma](resource-provider-operations.md#microsoftbilling)/Billingproperty/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Maliyet yönetimi okuyucusu

Maliyet verilerini ve yapılandırmayı görüntüleyebilir (örneğin, bütçeler, dışarı aktarmalar) [daha fazla bilgi edinin](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. tüketim](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. faturalandırma](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Configurations/Read | Yapılandırma al |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/Read | Önerileri okur |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | [Microsoft. faturalandırma](resource-provider-operations.md#microsoftbilling)/Billingproperty/Read |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Hiyerarşi Ayarları Yöneticisi

Kullanıcıların hiyerarşi ayarlarını düzenlemesine ve silmesine izin verir

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Settings/Write | Yönetim grubu hiyerarşisi ayarlarını oluşturur veya güncelleştirir. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Settings/Delete | Yönetim grubu hiyerarşisi ayarlarını siler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes kümesi-Azure yay ekleme

Herhangi bir kullanıcıyı/hizmeti, Connectedkümeler kaynağı oluşturmak üzere yetkilendirmek için rol tanımı [daha fazla bilgi](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Bir dağıtımı oluşturur veya güncelleştirir. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Abonelik işlem sonuçlarını alın. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Write | Connectedkümeler yazar |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/Connectedclusters/Read | Connectedkümelerini oku |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Yönetilen uygulama katılımcısı rolü

Yönetilen uygulama kaynakları oluşturulmasına izin verir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Register/Action | Çözümlere kaydolun. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Yönetilen uygulama Işletmeni rolü

Yönetilen uygulama kaynakları üzerinde işlemleri okuyup gerçekleştirmenize olanak tanır

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/Read | Uygulamaların bir listesini alır. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Yönetilen uygulamalar okuyucusu

Yönetilen bir uygulamadaki kaynakları okumanızı ve JıT erişimi isteğinizi yapmanızı sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Jımrequests/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Yönetilen hizmetler kayıt ataması rol silme

Yönetilen hizmetler kayıt ataması silme rolü, kiracı kullanıcılarının kiracıya atanan kayıt atamasını silmesine izin verir. [Daha fazla bilgi edinin](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/Registrationatama/okuma | Yönetilen hizmetler kayıt atamalarının bir listesini alır. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/Registrationatamasıns/Delete | Yönetilen hizmetler kayıt atamasını kaldırır. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/Operationkara ses/Read | Kaynak için işlem durumunu okur. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Yönetim grubu Katılımcısı

Yönetim grubu katkıda bulunan rolü [daha fazla bilgi](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Delete | Yönetim grubunu silin. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Subscriptions/Delete | Aboneliği yönetim grubundan kaldır. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Subscriptions/Write | Mevcut aboneliği yönetim grubuyla ilişkilendirir. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Write | Bir yönetim grubu oluşturun veya güncelleştirin. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Subscriptions/Read | Verilen yönetim grubu altındaki aboneliği listeler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Yönetim grubu okuyucusu

Yönetim grubu okuyucusu rolü

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Read | Kimliği doğrulanmış kullanıcı için Yönetim gruplarını listeleyin. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementgroups/Subscriptions/Read | Verilen yönetim grubu altındaki aboneliği listeler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Yeni relik APM hesabı Katılımcısı

New Relic Application Performance Management hesaplarını ve uygulamaları yönetmenize izin verir, ancak bunlara erişimi kalmaz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | Newrelik. APM/hesaplar/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>İlke öngörüleri veri yazıcısı (Önizleme)

Kaynak ilkelerine okuma erişimine ve kaynak bileşen ilkesi olaylarına yazma erişimine izin verir. [Daha fazla bilgi edinin](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/Read | İlke atama hakkında bilgi alın. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/Read | Bir ilke tanımı hakkında bilgi alın. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policymuaf tions/Read | İlke muafiyeti hakkında bilgi alın. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/Read | İlke kümesi tanımı hakkında bilgi alın. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. Poliyeleghts](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Veri ilkelerine karşı belirli bir bileşenin uyumluluk durumunu kontrol edin. |
> | [Microsoft. Poliyeleghts](resource-provider-operations.md#microsoftpolicyinsights)/Policyevents/logdataevents/Action | Kaynak bileşen ilkesi olaylarını günlüğe kaydedin. |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="quota-request-operator"></a>Kota Isteği operatörü

Kota isteklerini okuyup oluşturun, kota isteği durumunu alın ve destek bileti oluşturun. [Daha fazla bilgi edinin](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity)/Resourceproviders/Locations/servicelimits/Read | Belirtilen kaynak ve konumun geçerli hizmet sınırını veya kotasını al |
> | [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity)/Resourceproviders/Locations/servicelimits/Write | Belirtilen kaynak ve konum için hizmet sınırı veya kota oluştur |
> | [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity)/Resourceproviders/Locations/servicelimitsrequests/Read | Belirtilen kaynak ve konum için herhangi bir hizmet sınırı isteği al |
> | [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity)/Register/Action | Kapasite kaynak sağlayıcısını kaydeder ve kapasite kaynaklarının oluşturulmasına izin vermez. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Rezervasyon Satınalmacı

Ayırmaları satın almanızı sağlar [daha fazla bilgi](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Aboneliklerin listesini alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity)/Register/Action | Kapasite kaynak sağlayıcısını kaydeder ve kapasite kaynaklarının oluşturulmasına izin vermez. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Register/Action | Aboneliği Microsoft. COMPUTE kaynak sağlayıcısına kaydeder |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Register/Action | Microsoft SQL veritabanı kaynak sağlayıcısı için aboneliği kaydeder ve Microsoft SQL veritabanlarının oluşturulmasına izin vermez. |
> | [Microsoft. tüketim](resource-provider-operations.md#microsoftconsumption)/Register/Action | Tüketim RP 'ye kaydol |
> | [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity)/Catalogs/Read | Ayırma kataloğunu okuyun |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/Roleatamas/Read | Rol ataması hakkında bilgi alın. |
> | [Microsoft. tüketim](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/Read | Bir abonelik için ayrılmış örnekler için tek veya paylaşılan öneriler listeleyin. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/supportbilet s/Write | Destek bileti oluşturulmasına ve güncelleştirilmesine izin verir |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Kaynak İlkesine Katkıda Bulunan

Kaynak ilkesi oluşturma/değiştirme, destek bileti oluşturma ve kaynakları/hiyerarşisi okuma haklarına sahip kullanıcılar. [Daha fazla bilgi edinin](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | */Read | Gizli dizileri hariç tüm türlerin kaynaklarını okuyun. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | İlke atamaları oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | İlke tanımları oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policymuaf tions/* | İlke muafiyetlerini oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | İlke kümeleri oluşturma ve yönetme |
> | [Microsoft. Poliyelei](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery Katkıda Bulunanı

Kasa oluşturma ve [rol atama dışında](../site-recovery/site-recovery-role-based-linked-access-control.md) Site Recovery hizmetini yönetmenizi sağlar

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedstamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatestamp/Action | AllocateStamp, hizmet tarafından kullanılan iç işlemdir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Kaynak sertifikası güncelleştirme işlemi kaynak/kasa kimlik bilgisi sertifikasını güncelleştirir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/* | Kasa ile ilgili genişletilmiş bilgileri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshcontainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/* | Kayıtlı kimlikler oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationalertsettings/* | Çoğaltma uyarı ayarlarını oluşturma veya güncelleştirme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationevents/Read | Tüm olayları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/* | Çoğaltma yapıları oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationjobs/* | Çoğaltma işleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationpolicies/* | Çoğaltma ilkeleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Kurtarma planlarını oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageconfig/* | Kurtarma Hizmetleri kasasının depolama yapılandırmasını oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/TokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaulttokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/* | Kurtarma Hizmetleri Kasası için uyarıları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringconfigurations/notificationconfiguration/Read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationoperationstatus/Read | Tüm kasa çoğaltma Işlemi durumunu okuyun |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery Operatörü

Yük devretme ve yeniden çalışma yapmanıza izin verir ancak diğer Site Recovery yönetim işlemlerini gerçekleştirmenize [daha fazla bilgi](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Virtualnetworks/Read | Sanal ağ tanımını al |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedstamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatestamp/Action | AllocateStamp, hizmet tarafından kullanılan iç işlemdir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshcontainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/operationresults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationalertsettings/Read | Tüm uyarı ayarlarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationevents/Read | Tüm olayları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/checkmı \ eylem | Dokunun tutarlılığını denetler |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/Read | Tüm yapıları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/ilişkilendirme Ategateway/Action | Ağ geçidini yeniden ilişkilendir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/renewcertificate/Action | Doku için Sertifikayı Yenile |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationnetworks/Read | Tüm ağları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationnetworks/replicationnetworkmappings/Read | Tüm ağ eşlemelerini okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/Read | Tüm koruma kapsayıcılarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Tabloıtem/Read | Korunabilir öğeleri oku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/applyrecoverypoint/Action | Kurtarma noktası Uygula |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/failovercommit/Action | Yük devretme yürütmesi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/PlannedFailOver/Action | Planlı Yük Devretme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/Read | Tüm korumalı öğeleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/recoverypoints/Read | Tüm çoğaltma kurtarma noktalarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/repairreplication/Action | Çoğaltmayı Onar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Korumalı öğeyi yeniden koru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/switchprotection/Action | Koruma kapsayıcısını Değiştir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/testfailover/Action | Test Yük Devretmesi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/testfailovercleanup/Action | Yük devretme sınamasını Temizleme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/unplannedfailover/Action | Yük devretme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/updatedraityservice/Action | Mobility hizmetini Güncelleştir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationprotectioncontainermappings/Read | Tüm koruma kapsayıcısı eşlemelerini okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationrecoveryservicesproviders/Read | Tüm kurtarma hizmetleri sağlayıcılarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationrecoveryservicesproviders/refreshprovider/Action | Sağlayıcıyı Yenile |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationstorageclassıfications/Read | Tüm depolama sınıflandırmalarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationstorageclassıfications/replicationstorageclassıficationmappings/Read | Tüm depolama sınıflandırması eşlemelerini okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationvcenters/Read | Tüm sanal merkezleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationjobs/* | Çoğaltma işleri oluşturma ve yönetme |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationpolicies/Read | Tüm Ilkeleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/failovercommit/Action | Yük devretme yürütmesi kurtarma planı |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/PlannedFailOver/Action | Planlı Yük devretme kurtarma planı |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/Read | Tüm kurtarma planlarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Kurtarma planını yeniden koru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/testfailover/Action | Yük devretme kurtarma planını sına |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/testfailovercleanup/Action | Sınama yük devretmesi Temizleme kurtarma planı |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/unplannedfailover/Action | Yük devretme kurtarma planı |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/* | Kurtarma Hizmetleri Kasası için uyarıları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringconfigurations/notificationconfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageconfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/TokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaulttokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery Okuyucusu

Site Recovery durumunu görüntülemenize izin verir ancak diğer yönetim işlemlerini gerçekleştirmenize [daha fazla bilgi edinin](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedstamp/Read | GetAllocatedStamp, hizmet tarafından kullanılan iç işlemdir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendeınformation/Read | Genişletilmiş bilgileri al işlemi, bir nesnenin,? Kasası türünde Azure kaynağını temsil eden genişletilmiş bilgilerini alır mi? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringalerts/Read | Kurtarma Hizmetleri Kasası için uyarıları alır. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringconfigurations/notificationconfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Kasayı al işlemi, ' kasa ' türündeki Azure kaynağını temsil eden bir nesneyi alır |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshcontainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/operationresults/Read | Işlem sonuçlarını al işlemi, zaman uyumsuz olarak gönderilen işlemin işlem durumunu ve sonucunu almak için kullanılabilir |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredidentities/Read | Kapsayıcıları al işlemi, bir kaynak için kayıtlı olan kapsayıcıları almak için kullanılabilir. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationalertsettings/Read | Tüm uyarı ayarlarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationevents/Read | Tüm olayları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/Read | Tüm yapıları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationnetworks/Read | Tüm ağları okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationnetworks/replicationnetworkmappings/Read | Tüm ağ eşlemelerini okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/Read | Tüm koruma kapsayıcılarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Tabloıtem/Read | Korunabilir öğeleri oku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/Read | Tüm korumalı öğeleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationkorunabilir Dıtems/recoverypoints/Read | Tüm çoğaltma kurtarma noktalarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationprotectioncontainers/replicationprotectioncontainermappings/Read | Tüm koruma kapsayıcısı eşlemelerini okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationrecoveryservicesproviders/Read | Tüm kurtarma hizmetleri sağlayıcılarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationstorageclassıfications/Read | Tüm depolama sınıflandırmalarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationstorageclassıfications/replicationstorageclassıficationmappings/Read | Tüm depolama sınıflandırması eşlemelerini okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationfabrics/replicationvcenters/Read | Tüm sanal merkezleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationjobs/Read | Tüm Işleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationpolicies/Read | Tüm Ilkeleri okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationrecoveryplans/Read | Tüm kurtarma planlarını okuyun |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageconfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/TokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Bir kurtarma hizmetleri Kasası için kullanım ayrıntılarını döndürür. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaulttokens/Read | Kasa düzeyi arka uç işlemlerine ait kasa belirteci almak için kasa belirteci işlemi kullanılabilir. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Destek Isteği Katılımcısı

Destek istekleri oluşturmanızı ve yönetmenizi sağlar [daha fazla bilgi](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Etiket Katılımcısı

Varlıklarda kendilerine erişim sağlamamanıza gerek kalmadan varlıklarda etiketleri yönetmenizi sağlar. [Daha fazla bilgi edinin](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Resources/Read | Kaynak grubunun kaynaklarını alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Resources/Read | Bir aboneliğin kaynaklarını alır. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Diğer


### <a name="azure-digital-twins-data-owner"></a>Azure dijital TWINS veri sahibi

Dijital TWINS veri düzlemi için tam erişim rolü [daha fazla bilgi edinin](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventkabtes/* | Herhangi bir olay yolunu okuma, silme, oluşturma veya güncelleştirme |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwıns/* | Herhangi bir dijital Ikizi okuma, oluşturma, güncelleştirme veya silme |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Commands/* | Dijital Ikizi üzerinde herhangi bir komutu çağırma |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/* | Herhangi bir dijital Ikizi Ilişkisini okuyun, oluşturun, güncelleştirin veya silin |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Herhangi bir modeli okuyun, oluşturun, güncelleştirin veya silin |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/* | Herhangi bir dijital TWINS grafiğini sorgulama |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure dijital TWINS veri okuyucusu

Dijital TWINS veri düzlemi özellikleri için salt okunurdur rol [daha fazla bilgi edinin](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwıns/Read | Tüm dijital Ikizi okuma |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/Read | Herhangi bir dijital Ikizi Ilişkisini okuyun |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventkabtes/Read | Herhangi bir olay yolunu okuyun |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/Read | Herhangi bir modeli okuyun |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/Action | Herhangi bir dijital TWINS grafiğini sorgulama |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk Katılımcısı

BizTalk hizmetlerini yönetmenizi sağlar ancak onlara erişim izni vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | Microsoft. BizTalkServices/BizTalk/* | BizTalk Hizmetleri oluşturma ve yönetme |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>Masaüstü Sanallaştırma uygulama grubu Katılımcısı

Masaüstü Sanallaştırma uygulama grubunun katılımcısı. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Barındırma havuzlarını oku |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Hostpools/sessionkonakları oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Masaüstü Sanallaştırma uygulama grubu okuyucusu

Masaüstü Sanallaştırma uygulama grubunun okuyucusu. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/Read |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Applicationgroups 'ı okuyun |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Barındırma havuzlarını oku |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Hostpools/sessionkonakları oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Dağıtımları alır veya listeler. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/Read | Klasik ölçüm uyarısını okuyun |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Masaüstü Sanallaştırma Katılımcısı

Masaüstü Sanallaştırma katılımcısı. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Masaüstü Sanallaştırma Ana bilgisayar havuzu Katılımcısı

Masaüstü Sanallaştırma Ana bilgisayar havuzunun katılımcısı. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Masaüstü Sanallaştırma Ana bilgisayar havuzu okuyucusu

Masaüstü Sanallaştırma Ana bilgisayar havuzunun okuyucusu. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/Read |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Barındırma havuzlarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Dağıtımları alır veya listeler. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/Read | Klasik ölçüm uyarısını okuyun |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Masaüstü Sanallaştırma okuyucusu

Masaüstü Sanallaştırma okuyucusu. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Dağıtımları alır veya listeler. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/Read | Klasik ölçüm uyarısını okuyun |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Masaüstü Sanallaştırma oturumu ana bilgisayar Işleci

Masaüstü Sanallaştırma oturumu ana bilgisayarının işleci. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Barındırma havuzlarını oku |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Masaüstü Sanallaştırma kullanıcısı

Kullanıcının uygulama grubundaki uygulamaları kullanmasına izin verir. [Daha fazla bilgi edinin](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | *yok* |  |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Applicationgroups/useapplications/Action | ApplicationGroup kullanma |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Masaüstü Sanallaştırma Kullanıcı oturumu Işleci

Masaüstü Sanallaştırma uesr oturumunun işleci. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Barındırma havuzlarını oku |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Hostpools/sessionkonakları oku |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Masaüstü Sanallaştırma çalışma alanı Katılımcısı

Masaüstü Sanallaştırma çalışma alanının katılımcısı. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/* |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Applicationgroups 'ı okuyun |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Masaüstü Sanallaştırma çalışma alanı okuyucusu

Masaüstü Sanallaştırma çalışma alanının okuyucusu. [Daha fazla bilgi edinin](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/Read | Çalışma alanlarını oku |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Applicationgroups 'ı okuyun |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Dağıtımları alır veya listeler. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/Read | Klasik ölçüm uyarısını okuyun |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Disk yedekleme okuyucusu

Disk yedeklemesi gerçekleştirmek için Yedekleme Kasası için izin sağlar. [Daha fazla bilgi edinin](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/Read | Bir diskin özelliklerini al |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/begingetaccess/Action | Blob erişimi için diskin SAS URI 'sini al |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Disk geri yükleme Işleci

Disk geri yükleme işlemini gerçekleştirmek için Yedekleme Kasası için izin sağlar. [Daha fazla bilgi edinin](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/Write | Yeni bir disk oluşturur veya var olan bir diski güncelleştirir |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/Read | Bir diskin özelliklerini al |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Disk anlık görüntüsü Katılımcısı

Disk anlık görüntülerini yönetmek için Yedekleme Kasası için izin sağlar. [Daha fazla bilgi edinin](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/anlık görüntüyle TS/Delete | Anlık görüntüyü silme |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/anlık görüntüyle TS/Write | Yeni bir anlık görüntü oluşturma veya var olanı güncelleştirme |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/anlık görüntüyle TS/Read | Anlık görüntünün özelliklerini al |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/anlık görüntüyle TS/begingetaccess/Action | Blob erişimi için anlık görüntünün SAS URI 'sini alma |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/anlık görüntüyle TS/endgetaccess/Action | Anlık görüntünün SAS URI 'sini iptal et |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Disks/begingetaccess/Action | Blob erişimi için diskin SAS URI 'sini al |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/ListKeys/Action | Belirtilen depolama hesabı için erişim anahtarlarını döndürür. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Write | Belirtilen parametrelerle bir depolama hesabı oluşturur veya özellikleri ya da etiketleri güncelleştirir veya belirtilen depolama hesabı için özel etki alanı ekler. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Read | Depolama hesaplarının listesini döndürür veya belirtilen depolama hesabının özelliklerini alır. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/Storageaccounts/Delete | Var olan bir depolama hesabını siler. |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Zamanlayıcı Iş koleksiyonları Katılımcısı

Zamanlayıcı iş koleksiyonlarını yönetmenizi sağlar, ancak bunlara erişimi kalmaz.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Alertrules/* | Klasik ölçüm uyarısı oluşturma ve yönetme |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/kullanılabilirliği bilitykara ses/Read | Belirtilen kapsamdaki tüm kaynaklar için Kullanılabilirlik durumlarını alır |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | İş koleksiyonları oluşturma ve yönetme |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Destek bileti oluşturma ve güncelleştirme |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>Hizmetler hub 'ı Işleci

Hizmet hub 'ı operatörü, Service hub bağlayıcılarıyla ilgili tüm okuma, yazma ve silme işlemlerini gerçekleştirmenize olanak tanır. [Daha fazla bilgi edinin](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rolleri ve rol atamalarını oku |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Read | Kaynak gruplarını alır veya listeler. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Dağıtım oluşturma ve yönetme |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Write | Service hub Bağlayıcısı oluşturma veya güncelleştirme |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Read | Hizmet Merkezi bağlayıcılarını görüntüleme veya listeleme |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Delete | Service hub bağlayıcılarını Sil |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/checkassessmententitlement/Action | Belirli bir hizmet hub 'ı çalışma alanı için değerlendirme yetkilendirmelerini listeler |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Supportofferingentitlement/Read | Belirli bir hizmet merkezi çalışma alanı için destek teklifi yetkilendirmelerini görüntüleyin |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Workspaces/Read | Belirli bir kullanıcı için hizmet hub 'ı çalışma alanlarını listeleyin |
> | **NotActions** |  |
> | *yok* |  |
> | **Veri eylemleri** |  |
> | *yok* |  |
> | **NotDataActions** |  |
> | *yok* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak Azure rolleri atama](role-assignments-portal.md)
- [Özel Azure rolleri](custom-roles.md)
- [Azure Güvenlik Merkezi'nde İzinler](../security-center/security-center-permissions.md)
