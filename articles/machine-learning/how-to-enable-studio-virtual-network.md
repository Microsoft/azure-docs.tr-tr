---
title: Sanal ağda Azure Machine Learning Studio 'yu etkinleştirme
titleSuffix: Azure Machine Learning
description: Bir sanal ağ içinde depolanan verilere erişmek için Azure Machine Learning Studio 'yu nasıl yapılandıracağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: da8007a651b62430055f263f082fabf2aa4bf610
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574297"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure sanal ağında Azure Machine Learning Studio 'yu kullanma

Bu makalede, bir sanal ağda Azure Machine Learning Studio 'yu kullanmayı öğreneceksiniz. Studio, oto ml, tasarımcı ve veri etiketleme gibi özellikler içerir. Bu özellikleri bir sanal ağda kullanabilmek için, bu makaledeki adımları izlemeniz gerekir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> - Bir sanal ağ içinde depolanan verilere Studio erişimi verin.
> - Bir sanal ağın içindeki bir kaynaktan Studio 'ya erişin.
> - Studio 'nun depolama güvenliğini nasıl etkilediğini anlayın.

Bu makale, bir Azure Machine Learning iş akışını güvenli hale getirmek için size kılavuzluk eden beş bölümlü bir serinin beş bölümüdür. Bir sanal ağ ortamı ayarlamak için önceki bölümleri okumanız önemle tavsiye ederiz.

Bu serideki diğer makalelere göz atın:

[1. VNET 'e genel bakış](how-to-network-security-overview.md)  >  [2. Çalışma alanı](how-to-secure-workspace-vnet.md)3 ' ü güvenli hale getirin  >  [. Eğitim ortamının](how-to-secure-training-vnet.md)4 ' ü koruyun  >  [. Invenli ortam](how-to-secure-inferencing-vnet.md)5 ' i güvenli hale getirin  >  **. Studio işlevselliğini etkinleştir**


> [!IMPORTANT]
> Çalışma alanınız, Azure Kamu veya Azure Çin 21Vianet gibi bir bağımsız __bulutta__ ise, tümleşik Not defterleri sanal bir ağdaki depolamanın _kullanımını desteklemez._ Bunun yerine işlem örneğinde Jupyter Notebook'larını kullanabilirsiniz. Daha fazla bilgi için, [Işlem örneği Not Defteri Içindeki erişim verileri](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

+ Genel sanal ağ senaryolarını ve mimarisini anlamak için [ağ güvenliğine genel bakış](how-to-network-security-overview.md) makalesini okuyun.

+ Kullanılacak önceden var olan bir sanal ağ ve alt ağ.

+ [Özel bağlantı etkin olan bir Azure Machine Learning çalışma alanı](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Mevcut bir [Azure depolama hesabı, Sanal ağınızı ekledi](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Studio 'da veri erişimini yapılandırma

Bir sanal ağda bazı Studio özelliklerinden bazıları varsayılan olarak devre dışıdır. Bu özellikleri yeniden etkinleştirmek için, Studio 'da kullanmayı düşündüğünüz depolama hesapları için yönetilen kimliği etkinleştirmeniz gerekir. 

Aşağıdaki işlemler bir sanal ağda varsayılan olarak devre dışıdır:

* Studio 'daki verileri önizleyin.
* Tasarımcıda verileri görselleştirin.
* Tasarımcıda bir model dağıtın ([varsayılan depolama hesabı](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Bir oto ml denemesi ([varsayılan depolama hesabı](#enable-managed-identity-authentication-for-default-storage-accounts)) gönderebilirsiniz.
* Etiketleme projesi başlatın.

Studio, bir sanal ağdaki aşağıdaki veri deposu türlerinden veri okumayı destekler:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Veritabanı

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Veri depolarını çalışma alanı tarafından yönetilen kimliği kullanacak şekilde yapılandırma

Bir [hizmet uç noktası](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) veya [Özel uç nokta](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)Ile sanal ağınıza bir Azure depolama hesabı ekledikten sonra, veri deposundan [yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md) doğrulaması kullanmak için yapılandırmanız gerekir. Bunu yaptığınızda, Studio 'nun Depolama hesabınızdaki verilere erişmesine izin verir.

Azure Machine Learning, depolama hesaplarına bağlanmak için [veri depolarını](concept-data.md#datastores) kullanır. Bir veri deposunu yönetilen kimlik kullanacak şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. Studio 'da __veri depoları__' nı seçin.

1. Mevcut bir veri deposunu güncelleştirmek için veri deposunu seçin ve __kimlik bilgilerini güncelleştir__' i seçin.

    Yeni bir veri deposu oluşturmak için __+ Yeni veri deposu__' nu seçin.

1. Veri deposu ayarlarında, __Azure Machine Learning Studio 'da veri önizleme ve profil oluşturma için çalışma alanı yönetilen kimliğini kullan__ için __Evet__ ' i seçin.

    ![Yönetilen çalışma alanı kimliğini etkinleştirmeyi gösteren ekran görüntüsü](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Bu adımlar, Azure RBAC kullanarak, çalışma alanı tarafından yönetilen kimliği depolama hizmetine __okuyucu__ olarak ekler. __Okuyucu__ erişimi, çalışma alanının, verilerin sanal ağdan çıkmadığınızdan emin olmak için güvenlik duvarı ayarlarını almasına izin verir. Değişikliklerin etkili olması 10 dakika kadar sürebilir.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Varsayılan depolama hesapları için yönetilen kimlik kimlik doğrulamasını etkinleştir

Her Azure Machine Learning çalışma alanı, varsayılan bir BLOB depolama hesabı ve çalışma alanınızı oluştururken tanımlanan varsayılan bir dosya deposu hesabı olan iki varsayılan depolama hesabına sahiptir. Ayrıca, **veri deposu** Yönetim sayfasında Yeni varsayılanlar de ayarlayabilirsiniz.

![Varsayılan veri depolarının nerede bulunileceğini gösteren ekran görüntüsü](./media/how-to-enable-studio-virtual-network/default-datastores.png)

Aşağıdaki tabloda, çalışma alanı varsayılan depolama hesaplarınız için yönetilen kimlik kimlik doğrulamasını neden etkinleştirmeniz gerekir.

|Depolama hesabı  | Notlar  |
|---------|---------|
|Çalışma alanı varsayılan blob depolaması| Tasarımcıda Model varlıklarını depolar. Tasarımcıyı tasarımcıda dağıtmak için bu depolama hesabında yönetilen kimlik kimlik doğrulamasını etkinleştirmeniz gerekir. <br> <br> Yönetilen kimlik kullanmak üzere yapılandırılmış varsayılan olmayan bir veri deposu kullanıyorsa, tasarımcı işlem hattını görselleştirebilir ve çalıştırabilirsiniz. Ancak, varsayılan veri deposunda yönetilen kimlik olmadan eğitilen bir model dağıtmaya çalışırsanız, kullanımda olan diğer veri depolarından bağımsız olarak dağıtım başarısız olur.|
|Çalışma alanı varsayılan dosya deposu| Oto ml deneme varlıklarını depolar. Bu depolama hesabında, oto ml denemeleri göndermek için yönetilen kimlik kimlik doğrulamasını etkinleştirmeniz gerekir. |

> [!WARNING]
> Varsayılan dosya deposunun klasörü otomatik olarak oluşturmadığından `azureml-filestore` , otomatik ml denemeleri göndermek için gerekli olduğu bilinen bir sorun vardır. Bu durum, kullanıcılar çalışma alanı oluşturma sırasında varsayılan dosya deposu olarak ayarlamak üzere var olan bir dosya deposunu getirmediğinde oluşur.
> 
> Bu sorundan kaçınmak için iki seçeneğiniz vardır: 1) çalışma alanı oluşturma işlemi yaparken otomatik olarak oluşturulan varsayılan dosya deposunu kullanın. 2) kendi dosya deponuzu getirmek için, çalışma alanı oluşturma sırasında Filestore 'un VNet 'in dışında olduğundan emin olun. Çalışma alanı oluşturulduktan sonra, depolama hesabını sanal ağa ekleyin.
>
> Bu sorunu çözmek için, Filestore hesabını sanal ağdan kaldırın ve sonra sanal ağa geri ekleyin.

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Depolama özel bağlantısına çalışma alanı yönetilen kimlik __okuyucusu__ erişimi verme

Azure depolama hesabınız özel bir uç nokta kullanıyorsa, çalışma alanı tarafından yönetilen kimlik **okuyucusu** özel bağlantı için erişim izni vermeniz gerekir. Daha fazla bilgi için bkz. [Reader](../role-based-access-control/built-in-roles.md#reader) yerleşik rolü. 

Depolama hesabınız bir hizmet uç noktası kullanıyorsa, bu adımı atlayabilirsiniz.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 'in içindeki bir kaynaktaki Studio 'ya erişme

Studio 'ya bir sanal ağın içindeki bir kaynaktan erişiyorsanız (örneğin, bir işlem örneği veya sanal makine), sanal ağdan Studio 'ya giden trafiğe izin vermeniz gerekir. 

Örneğin, giden trafiği kısıtlamak için ağ güvenlik grupları (NSG) kullanıyorsanız, __Azurefrontkapısı. ön uç__'nin __hizmet etiketi__ hedefine bir kural ekleyin.

## <a name="technical-notes-for-managed-identity"></a>Yönetilen kimlik için teknik notlar

Depolama hizmetlerine erişmek için yönetilen kimlik kullanmak güvenlik konularını etkiler. Bu bölümde, her depolama hesabı türü için değişiklikler açıklanmaktadır. 

Bu konular, eriştiğiniz __depolama hesabı türüne__ özeldir.

### <a name="azure-blob-storage"></a>Azure Blob depolama

__Azure Blob depolama__ için çalışma alanı tarafından yönetilen kimlik, blob depolamadan veri okuyabilmesi Için bir [BLOB veri okuyucusu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) olarak da eklenir.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage 2. Access Control

Bir sanal ağ içindeki veri erişimini denetlemek için hem Azure RBAC hem de POSIX stili erişim denetim listelerini (ACL 'Ler) kullanabilirsiniz.

Azure RBAC kullanmak için, [BLOB veri okuyucusu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) rolüne çalışma alanı tarafından yönetilen kimliği ekleyin. Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

ACL 'Leri kullanmak için, çalışma alanı tarafından yönetilen kimliğe, tıpkı diğer güvenlik ilkelerine benzer şekilde erişim atanabilir. Daha fazla bilgi için bkz. [dosya ve dizinlerdeki erişim denetim listeleri](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage 1. Access Control

Azure Data Lake Storage 1. yalnızca POSIX stili erişim denetim listelerini destekler. Diğer güvenlik ilkelerine benzer şekilde, çalışma alanı tarafından yönetilen kimlik erişimini kaynaklara atayabilirsiniz. Daha fazla bilgi için [Azure Data Lake Storage 1. erişim denetimi](../data-lake-store/data-lake-store-access-control.md)bölümüne bakın.

### <a name="azure-sql-database-contained-user"></a>Azure SQL veritabanı kullanıcı içeriyordu

Yönetilen kimlik kullanarak bir Azure SQL veritabanında depolanan verilere erişmek için, yönetilen kimliğe eşlenen bir SQL kapsanan Kullanıcı oluşturmanız gerekir. Bir dış sağlayıcıdan Kullanıcı oluşturma hakkında daha fazla bilgi için bkz. [Azure AD kimliklerine eşlenmiş içerilen kullanıcılar oluşturma](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Bir SQL kapsanan Kullanıcı oluşturduktan sonra, bu kullanıcıya izin ver [T-SQL komutunu](/sql/t-sql/statements/grant-object-permissions-transact-sql)kullanarak izin verin.

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Azure Machine Learning Designer ara modül çıktısı

Tasarımcıda herhangi bir modülün çıkış konumunu belirtebilirsiniz. Ara veri kümelerini güvenlik, günlüğe kaydetme veya denetim amaçlarıyla ayrı bir konumda depolamak için bunu kullanın. Çıktıyı belirtmek için:

1. Çıktısını belirtmek istediğiniz modülün bulunduğu modülü seçin.
1. Sağ tarafta görüntülenen modül ayarları bölmesinde **Çıkış ayarları**' nı seçin.
1. Her modül çıktısı için kullanmak istediğiniz veri deposunu belirtin.
 
Sanal ağınızdaki ara depolama hesaplarına erişiminiz olduğundan emin olun. Aksi takdirde, işlem hattı başarısız olur.

Ayrıca, çıkış verilerini görselleştirmek için ara depolama hesapları için [yönetilen kimlik kimlik doğrulamasını etkinleştirmeniz](#configure-datastores-to-use-workspace-managed-identity) gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, beş bölümlü bir sanal ağ serisinin beş bölümüdür. Bir sanal ağın güvenliğini nasıl sağlayacağınızı öğrenmek için makalelerin geri kalanına bakın:

* [1. kısım: sanal ağa genel bakış](how-to-network-security-overview.md)
* [2. Bölüm: çalışma alanı kaynaklarını güvenli hale getirme](how-to-secure-workspace-vnet.md)
* [3. kısım: eğitim ortamının güvenliğini sağlama](how-to-secure-training-vnet.md)
* [4. Bölüm: ikinci dereceden sınırlama ortamının güvenliğini sağlama](how-to-secure-inferencing-vnet.md)

Ayrıca, ad çözümlemesi için [özel DNS](how-to-custom-dns.md) kullanma makalesine bakın.