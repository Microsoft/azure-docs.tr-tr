---
title: Azure aboneliğini farklı bir Azure AD dizinine aktarma
description: Azure aboneliğini ve bilinen ilgili kaynakları farklı bir Azure Active Directory (Azure AD) dizinine aktarmayı öğrenin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 72dc92ae211034e2a49bc77f60880f17ab15dec7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868186"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Azure aboneliğini farklı bir Azure AD dizinine aktarma

Kuruluşların çeşitli Azure abonelikleri olabilir. Her abonelik belirli bir Azure Active Directory (Azure AD) diziniyle ilişkilendirilir. Yönetimi kolaylaştırmak için, farklı bir Azure AD dizinine bir abonelik aktarmak isteyebilirsiniz. Bir aboneliği farklı bir Azure AD dizinine aktardığınızda, bazı kaynaklar hedef dizine aktarılmaz. Örneğin, Azure rol tabanlı erişim denetimindeki (Azure RBAC) tüm rol atamaları ve özel roller, kaynak dizinden **kalıcı olarak** silinir ve hedef dizine aktarılmaz.

Bu makalede, bir aboneliği farklı bir Azure AD dizinine aktarmak ve aktarımdan sonra bazı kaynakları yeniden oluşturmak için izleyebileceğiniz temel adımlar açıklanmaktadır.

> [!NOTE]
> Azure bulut çözümü sağlayıcıları (CSP) abonelikleri için, aboneliğin Azure AD dizinini değiştirme desteklenmez.

## <a name="overview"></a>Genel Bakış

Azure aboneliğini farklı bir Azure AD dizinine aktarmak, dikkatlice planlanmalıdır ve yürütülmesi gereken karmaşık bir işlemdir. Birçok Azure hizmeti, normal şekilde çalışması için güvenlik sorumlularını (kimlikler) gerektirir veya diğer Azure kaynaklarını yönetebilir. Bu makale, güvenlik ilkelerine büyük ölçüde bağlı olan, ancak kapsamlı olmayan Azure hizmetlerinin çoğunu kapsamaya çalışır.

> [!IMPORTANT]
> Bazı senaryolarda, bir aboneliğin aktarılması işlemi tamamlaması için kapalı kalma süresi gerekebilir. Aktarımınız için kapalı kalma süresinin gerekli olup olmadığını değerlendirmek için dikkatli bir planlama yapılması gerekir.

Aşağıdaki diyagramda, bir aboneliği farklı bir dizine aktardığınızda izlemeniz gereken temel adımlar gösterilmektedir.

1. Aktarım için hazırlanma

1. Azure aboneliğini farklı bir dizine aktarma

1. Hedef dizinde rol atamaları, özel roller ve yönetilen kimlikler gibi kaynakları yeniden oluşturun

    ![Aktarım aboneliği diyagramı](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Bir aboneliğin farklı bir dizine aktarılmaya karar verme

Abonelik aktarmak isteyebileceğiniz bazı nedenler aşağıda verilmiştir:

- Bir şirket merceği veya alımı nedeniyle, birincil Azure AD dizininizde alınan bir aboneliği yönetmek istiyorsunuz.
- Kuruluşunuzdaki birisi bir abonelik oluşturdu ve yönetimi belirli bir Azure AD dizini ile birleştirmek istiyorsunuz.
- Belirli bir abonelik KIMLIĞINE veya URL 'ye bağlı olan uygulamalarınız vardır ve uygulama yapılandırmasını veya kodunu değiştirmek kolay değildir.
- İşletmenizin bir kısmı ayrı bir şirkete bölünür ve kaynaklarınızın bazılarını farklı bir Azure AD dizinine taşımanız gerekir.
- Güvenlik yalıtımı amacıyla, farklı bir Azure AD dizininde kaynaklarınızın bazılarını yönetmek istiyorsunuz.

### <a name="alternate-approaches"></a>Alternatif yaklaşımlar

Aboneliğin aktarılması işlemi tamamlaması için kapalı kalma süresi gerekir. Senaryonuza bağlı olarak, aşağıdaki alternatif yaklaşımları göz önünde bulundurun:

- Kaynakları yeniden oluşturun ve hedef dizine ve aboneliğe verileri kopyalayın.
- Çoklu Dizin mimarisini benimseyin ve aboneliği kaynak dizinde bırakın. Hedef dizindeki kullanıcıların kaynak dizindeki aboneliğe erişebilmesi için Azure açık Thouse ' i kullanarak kaynakları devretmek. Daha fazla bilgi için bkz. [Kurumsal senaryolarda Azure açık Thouse](../lighthouse/concepts/enterprise.md).

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Abonelik aktarma etkisini anlayın

Birkaç Azure kaynağı bir aboneliğe veya dizine bağımlılığı vardır. Durumunuza bağlı olarak, aşağıdaki tabloda bir aboneliği aktarmanın bilinen etkileri listelenmektedir. Bu makaledeki adımları gerçekleştirerek, abonelik aktarımından önce var olan kaynakların bazılarını yeniden oluşturabilirsiniz.

> [!IMPORTANT]
> Bu bölüm, aboneliğinize bağlı olan bilinen Azure hizmetlerini veya kaynaklarını listeler. Azure 'daki kaynak türleri sürekli olarak geliştiğinden, ortamınızda önemli bir değişikliğe neden olabilecek ek bağımlılıklar olabilir. 

| Hizmet veya kaynak | Etkilendiğini | Gider | Etkilensin mi? | Yapabilecekleriniz |
| --------- | --------- | --------- | --------- | --------- |
| Rol atamaları | Yes | Yes | [Rol atamalarını listeleme](#save-all-role-assignments) | Tüm rol atamaları kalıcı olarak silinir. Kullanıcıları, grupları ve hizmet sorumlularını hedef dizindeki ilgili nesnelerle eşlemeniz gerekir. Rol atamalarını yeniden oluşturmanız gerekir. |
| Özel roller | Yes | Yes | [Özel rolleri listeleme](#save-custom-roles) | Tüm özel roller kalıcı olarak silinir. Özel rolleri ve tüm rol atamalarını yeniden oluşturmanız gerekir. |
| Sistem tarafından atanan Yönetilen kimlikler | Yes | Yes | [Yönetilen kimlikleri listeleme](#list-role-assignments-for-managed-identities) | Yönetilen kimlikleri devre dışı bırakıp yeniden etkinleştirmeniz gerekir. Rol atamalarını yeniden oluşturmanız gerekir. |
| Kullanıcı tarafından atanan Yönetilen kimlikler | Yes | Yes | [Yönetilen kimlikleri listeleme](#list-role-assignments-for-managed-identities) | Yönetilen kimlikleri silmeniz, yeniden oluşturmanız ve uygun kaynağa bağlamanız gerekir. Rol atamalarını yeniden oluşturmanız gerekir. |
| Azure Key Vault | Yes | Yes | [Key Vault erişim ilkelerini listeleme](#list-key-vaults) | Anahtar kasaları ile ilişkili kiracı KIMLIĞINI güncelleştirmeniz gerekir. Yeni erişim ilkelerini kaldırmalı ve eklemeniz gerekir. |
| Azure AD kimlik doğrulaması tümleştirmesinin etkinleştirildiği Azure SQL veritabanları | Yes | Hayır | [Azure AD kimlik doğrulamasıyla Azure SQL veritabanlarını denetleme](#list-azure-sql-databases-with-azure-ad-authentication) | Azure AD kimlik doğrulaması etkin bir Azure SQL veritabanı 'nı farklı bir dizine aktaramazsınız. Daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kullanma](../azure-sql/database/authentication-aad-overview.md). | 
| Azure depolama ve Azure Data Lake Storage 2. | Yes | Yes |  | Tüm ACL 'Leri yeniden oluşturmanız gerekir. |
| Azure Data Lake Storage 1. Nesil | Evet | Yes |  | Tüm ACL 'Leri yeniden oluşturmanız gerekir. |
| Azure Dosyaları | Yes | Yes |  | Tüm ACL 'Leri yeniden oluşturmanız gerekir. |
| Azure Dosya Eşitleme | Yes | Yes |  | Depolama eşitleme hizmeti ve/veya depolama hesabı farklı bir dizine taşınabilir. Daha fazla bilgi için bkz. [Azure dosyaları hakkında sık sorulan sorular (SSS)](../storage/files/storage-files-faq.md#azure-file-sync) |
| Azure Yönetilen Diskleri | Yes | Yes |  |  Yönetilen diskleri müşteri tarafından yönetilen anahtarlarla şifrelemek için disk şifreleme kümeleri kullanıyorsanız, disk şifreleme kümeleriyle ilişkili sistem tarafından atanan kimlikleri devre dışı bırakıp yeniden etkinleştirmeniz gerekir. Ve rol atamalarını yeniden oluşturmanız gerekir, yani anahtar kasalarındaki disk şifreleme kümelerine gerekli izinleri verin. |
| Azure Kubernetes Service | Yes | Hayır |  | AKS kümenizi ve ilişkili kaynaklarını farklı bir dizine aktaramazsınız. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) hakkında sık sorulan sorular](../aks/faq.md) |
| Azure İlkesi | Yes | Hayır | Özel tanımlar, Atamalar, muafiyetler ve uyumluluk verileri dahil olmak üzere tüm Azure Ilke nesneleri. | Tanımları [dışarı](../governance/policy/how-to/export-resources.md)ve içeri aktarmanız gerekir. Ardından, yeni ilke atamaları ve gerekli [ilke muafiyetleri](../governance/policy/concepts/exemption-structure.md)oluşturun. |
| Azure Active Directory Domain Services | Yes | Hayır |  | Azure AD Domain Services yönetilen bir etki alanını farklı bir dizine aktaramazsınız. Daha fazla bilgi için bkz. [Azure Active Directory (ad) etki alanı Hizmetleri hakkında sık sorulan sorular (SSS)](../active-directory-domain-services/faqs.md) |
| Uygulama kayıtları | Yes | Yes |  |  |

> [!WARNING]
> Aktarılmakta olan abonelikte aynı abonelikte **olmayan** bir anahtar kasasına bağımlılığı olan bir depolama HESABı veya SQL veritabanı gibi bir kaynak için geri kalan şifrelemeyi kullanıyorsanız kurtarılamaz bir senaryoya yol açabilir. Bu durumda, başka bir anahtar kasası kullanmak veya bu kurtarılamaz senaryoyu önlemek için müşteri tarafından yönetilen anahtarları geçici olarak devre dışı bırakmak için gerekli adımları uygulamanız gerekir.

## <a name="prerequisites"></a>Önkoşullar

Bu adımları tamamlayabilmeniz için şunlar gerekir:

- Azure Cloud Shell veya [Azure CLI](/cli/azure) ['da Bash](../cloud-shell/overview.md)
- Kaynak dizinde aktarmak istediğiniz aboneliğin Hesap Yöneticisi
- Hedef dizinde [sahip](built-in-roles.md#owner) rolü

## <a name="step-1-prepare-for-the-transfer"></a>1. Adım: aktarım için hazırlanma

### <a name="sign-in-to-source-directory"></a>Kaynak dizinde oturum açın

1. Azure 'da yönetici olarak oturum açın.

1. [Az Account List](/cli/azure/account#az_account_list) komutuyla aboneliklerinizin bir listesini alın.

    ```azurecli
    az account list --output table
    ```

1. Aktarmak istediğiniz etkin aboneliği ayarlamak için [az Account set](/cli/azure/account#az_account_set) kullanın.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Azure Kaynak Grafiği uzantısını yükler

 [Azure Kaynak Grafiği](../governance/resource-graph/index.yml)IÇIN Azure CLI uzantısı *kaynak* grafiği, Azure Resource Manager tarafından yönetilen kaynakları sorgulamak için [az Graph](/cli/azure/graph) komutunu kullanmanıza olanak sağlar. Sonraki adımlarda bu komutu kullanacaksınız.

1. *Kaynak grafik* uzantısının yüklenip yüklenmediğini görmek için [az Extension List](/cli/azure/extension#az_extension_list) kullanın.

    ```azurecli
    az extension list
    ```

1. Aksi takdirde, *kaynak grafik* uzantısını yükler.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Tüm rol atamalarını Kaydet

1. Tüm rol atamalarını (devralınan rol atamaları dahil) listelemek için [az role atama listesini](/cli/azure/role/assignment#az_role_assignment_list) kullanın.

    Listeyi gözden geçirmeyi kolaylaştırmak için çıktıyı JSON, TSV veya tablo olarak dışarı aktarabilirsiniz. Daha fazla bilgi için bkz. [Azure RBAC ve Azure CLI kullanarak rol atamalarını listeleme](role-assignments-list-cli.md).

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Rol atamalarının listesini kaydedin.

    Bir aboneliği aktardığınızda, tüm rol atamaları **kalıcı olarak** silinir, böylece bir kopya kaydetmeniz önemlidir.

1. Rol atamalarının listesini gözden geçirin. Hedef dizinde ihtiyacınız olmayacak rol atamaları olabilir.

### <a name="save-custom-roles"></a>Özel rolleri Kaydet

1. Özel rollerinizi listelemek için [az role Definition listesini](/cli/azure/role/definition#az_role_definition_list) kullanın. Daha fazla bilgi için bkz. [Azure CLI kullanarak Azure özel rolleri oluşturma veya güncelleştirme](custom-roles-cli.md).

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Hedef dizinde gerekli olacak her bir özel rolü ayrı bir JSON dosyası olarak kaydedin.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Özel rol dosyalarının kopyalarını oluşturun.

1. Her kopyayı aşağıdaki biçimi kullanacak şekilde değiştirin.

    Bu dosyaları daha sonra hedef dizindeki özel rolleri yeniden oluşturmak için kullanacaksınız.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Kullanıcı, Grup ve hizmet sorumlusu eşlemelerini belirleme

1. Rol atamalarınızın listesine göre, hedef dizinde eşlenecek kullanıcıları, grupları ve hizmet sorumlularını saptayın.

    `principalType`Her rol atamasında özelliğine bakarak asıl türünü belirleyebilirsiniz.

1. Gerekirse, hedef dizinde, ihtiyacınız olacak tüm kullanıcıları, grupları veya hizmet sorumlularını oluşturun.

### <a name="list-role-assignments-for-managed-identities"></a>Yönetilen kimlikler için rol atamalarını listeleyin

Yönetilen kimlikler, bir abonelik başka bir dizine aktarıldığında güncellenmez. Sonuç olarak, sistem tarafından atanan mevcut veya Kullanıcı tarafından atanan Yönetilen kimlikler bozulur. Aktarımdan sonra, sistem tarafından atanan yönetilen kimlikleri yeniden etkinleştirebilirsiniz. Kullanıcı tarafından atanan Yönetilen kimlikler için, bunları hedef dizinde yeniden oluşturmanız ve bağlamanız gerekir.

1. Yönetilen kimlikleri [destekleyen Azure hizmetlerinin listesini](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) gözden geçirin ve yönetilen kimlikleri nerede kullanabileceğinizi aklınızda bulabilirsiniz.

1. Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimliklerinizi listelemek için [az ad SP listesini](/cli/azure/ad/sp#az_ad_sp_list) kullanın.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Yönetilen kimlikler listesinde, hangisinin sistem tarafından atandığını ve Kullanıcı tarafından atandığını saptayın. Türü öğrenmek için aşağıdaki ölçütleri kullanabilirsiniz.

    | Ölçütler | Yönetilen kimlik türü |
    | --- | --- |
    | `alternativeNames` özellik eklemeleri `isExplicit=False` | Sistem tarafından atanan |
    | `alternativeNames` Özellik şunu içermez `isExplicit` | Sistem tarafından atanan |
    | `alternativeNames` özellik eklemeleri `isExplicit=True` | Kullanıcı tarafından atanan |

    Yalnızca Kullanıcı tarafından atanan yönetilen kimlikleri listelemek için [az Identity List](/cli/azure/identity#az_identity_list) ' i de kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure CLI kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md).

    ```azurecli
    az identity list
    ```

1. `objectId`Yönetilen kimliklerinizin değerlerinin listesini alın.

1. Yönetilen kimlikleriniz için herhangi bir rol ataması olup olmadığını görmek için rol atamaları listenizde arama yapın.

### <a name="list-key-vaults"></a>Anahtar kasalarını Listele

Bir Anahtar Kasası oluşturduğunuzda, otomatik olarak oluşturulduğu aboneliğin varsayılan Azure Active Directory kiracı KIMLIĞINE bağlanır. Tüm erişim ilkesi girdileri de bu kiracı kimliğine bağlanır. Daha fazla bilgi için bkz. [Azure Key Vault başka bir aboneliğe taşıma](../key-vault/general/move-subscription.md).

> [!WARNING]
> Aktarılmakta olan abonelikte aynı abonelikte **olmayan** bir anahtar kasasına bağımlılığı olan bir depolama HESABı veya SQL veritabanı gibi bir kaynak için geri kalan şifrelemeyi kullanıyorsanız kurtarılamaz bir senaryoya yol açabilir. Bu durumda, başka bir anahtar kasası kullanmak veya bu kurtarılamaz senaryoyu önlemek için müşteri tarafından yönetilen anahtarları geçici olarak devre dışı bırakmak için gerekli adımları uygulamanız gerekir.

- Anahtar kasanız varsa, erişim ilkelerini listelemek için [az keykasa Show](/cli/azure/keyvault#az_keyvault_show) komutunu kullanın. Daha fazla bilgi için bkz. [Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-cli.md).

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Azure AD kimlik doğrulamasıyla Azure SQL veritabanlarını listeleme

- Azure AD kimlik doğrulaması tümleştirmesinin etkin olduğu Azure SQL veritabanlarını kullanıp kullankullandığınızı görmek için [az SQL Server ad-yönetici listesi](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) ' ni ve [az Graph](/cli/azure/graph) Extension ' i kullanın. Daha fazla bilgi için bkz. [SQL ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>ACL 'Leri listeleme

1. Azure Data Lake Storage 1. kullanıyorsanız, herhangi bir dosyaya uygulanan ACL 'Leri Azure portal veya PowerShell kullanarak listeleyin.

1. Azure Data Lake Storage 2. kullanıyorsanız, herhangi bir dosyaya uygulanan ACL 'Leri Azure portal veya PowerShell kullanarak listeleyin.

1. Azure dosyaları kullanıyorsanız, herhangi bir dosyaya uygulanan ACL 'Leri listeleyin.

### <a name="list-other-known-resources"></a>Diğer bilinen kaynakları listeleme

1. Abonelik KIMLIĞINIZI almak için [az Account Show](/cli/azure/account#az_account_show) kullanın.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Diğer Azure kaynaklarını bilinen Azure AD dizin bağımlılıklarıyla listelemek için [az Graph](/cli/azure/graph) uzantısını kullanın.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>2. Adım: aboneliği aktarma

Bu adımda, aboneliği Kaynak dizinden hedef dizine aktarırsınız. Bu adımlar, faturalandırma sahipliğini de aktarmak istediğinize bağlı olarak farklılık belirtir.

> [!WARNING]
> Aboneliği aktardığınızda, kaynak dizindeki tüm rol atamaları **kalıcı olarak** silinir ve geri yüklenemez. Aboneliği aktardıktan sonra geri dönemezsiniz. Bu adımı gerçekleştirmeden önce önceki adımları tamamladığınızdan emin olun.

1. Aynı zamanda faturalandırma sahipliğini başka bir hesaba aktarmak isteyip istemediğinizi belirleme.

1. Aboneliği farklı bir dizine aktarın.

    - Geçerli faturalandırma sahipliğini korumak istiyorsanız, [ilişkilendir veya Azure Active Directory Kiracınıza Azure aboneliği ekleme](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)bölümündeki adımları izleyin.
    - Faturalandırma sahipliğini de aktarmak istiyorsanız, bir [Azure aboneliğinin faturalandırma sahipliğini başka bir hesaba aktarma](../cost-management-billing/manage/billing-subscription-transfer.md)bölümündeki adımları izleyin. Aboneliği farklı bir dizine aktarmak için **abonelik Azure AD kiracısı** onay kutusunu denetlemeniz gerekir.

1. Aboneliği aktarmayı tamamladıktan sonra, hedef dizindeki kaynakları yeniden oluşturmak için bu makaleye geri dönün.

## <a name="step-3-re-create-resources"></a>3. Adım: kaynakları yeniden oluşturma

### <a name="sign-in-to-target-directory"></a>Hedef dizinde oturum açın

1. Hedef dizinde, aktarım isteğini kabul eden kullanıcı olarak oturum açın.

    Yalnızca, aktarım isteğini kabul eden yeni hesaptaki Kullanıcı, kaynakları yönetmek için erişime sahip olur.

1. [Az Account List](/cli/azure/account#az_account_list) komutuyla aboneliklerinizin bir listesini alın.

    ```azurecli
    az account list --output table
    ```

1. Kullanmak istediğiniz etkin aboneliği ayarlamak için [az Account set](/cli/azure/account#az_account_set) kullanın.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Özel roller oluşturma
        
- Daha önce oluşturduğunuz dosyalardaki her bir özel rolü oluşturmak için [az role Definition Create](/cli/azure/role/definition#az_role_definition_create) ' i kullanın. Daha fazla bilgi için bkz. [Azure CLI kullanarak Azure özel rolleri oluşturma veya güncelleştirme](custom-roles-cli.md).

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Rolleri atama

- Kullanıcılara, gruplara ve hizmet sorumlularına roller atamak için [az role atama oluştur](/cli/azure/role/assignment#az_role_assignment_create) ' u kullanın. Daha fazla bilgi için bkz. [Azure CLI kullanarak Azure rolleri atama](role-assignments-cli.md).

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Sistem tarafından atanan yönetilen kimlikleri güncelleştirme

1. Sistem tarafından atanan yönetilen kimlikleri devre dışı bırakın ve yeniden etkinleştirin.

    | Azure hizmeti | Daha fazla bilgi | 
    | --- | --- |
    | Sanal makineler | [Azure CLı kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Sanal makine ölçek kümeleri | [Azure CLı kullanarak bir sanal makine ölçek kümesindeki Azure kaynakları için Yönetilen kimlikler yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Diğer hizmetler | [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Rolleri sistem tarafından atanan yönetilen kimliklere atamak için [az role atama Create](/cli/azure/role/assignment#az_role_assignment_create) kullanın. Daha fazla bilgi için bkz. [Azure CLI kullanarak bir kaynağa yönetilen kimlik erişimi atama](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Kullanıcı tarafından atanan yönetilen kimlikleri Güncelleştir

1. Kullanıcı tarafından atanan yönetilen kimlikleri silin, yeniden oluşturun ve ekleyin.

    | Azure hizmeti | Daha fazla bilgi | 
    | --- | --- |
    | Sanal makineler | [Azure CLı kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Sanal makine ölçek kümeleri | [Azure CLı kullanarak bir sanal makine ölçek kümesindeki Azure kaynakları için Yönetilen kimlikler yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Diğer hizmetler | [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Azure CLı kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Kullanıcı tarafından atanan yönetilen kimliklere roller atamak için [az role atama oluştur](/cli/azure/role/assignment#az_role_assignment_create) kullanın. Daha fazla bilgi için bkz. [Azure CLI kullanarak bir kaynağa yönetilen kimlik erişimi atama](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Anahtar kasalarını Güncelleştir

Bu bölümde, anahtar kasalarınızı güncelleştirmek için temel adımlar açıklanmaktadır. Daha fazla bilgi için bkz. [Azure Key Vault başka bir aboneliğe taşıma](../key-vault/general/move-subscription.md).

1. Abonelikteki tüm mevcut anahtar kasaları ile ilişkili kiracı KIMLIĞINI hedef dizine güncelleştirin.

1. Mevcut tüm erişim ilkesi girdilerini kaldırın.

1. Hedef dizinle ilişkili yeni erişim ilkesi girdileri ekleyin.

### <a name="update-acls"></a>ACL 'Leri Güncelleştir

1. Azure Data Lake Storage 1. kullanıyorsanız, uygun ACL 'Leri atayın. Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. depolanan verileri güvenli hale getirme](../data-lake-store/data-lake-store-secure-data.md).

1. Azure Data Lake Storage 2. kullanıyorsanız, uygun ACL 'Leri atayın. Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Nesil'de erişim denetimi](../storage/blobs/data-lake-storage-access-control.md).

1. Azure dosyaları kullanıyorsanız, uygun ACL 'Leri atayın.

### <a name="review-other-security-methods"></a>Diğer güvenlik yöntemlerini gözden geçirme

Aktarım sırasında rol atamaları kaldırılsa da, özgün sahip hesabındaki kullanıcılar şu gibi diğer güvenlik yöntemleriyle aboneliğe erişime sahip olmaya devam edebilir:

- Depolama gibi hizmetler için erişim anahtarları.
- Kullanıcı yöneticisine abonelik kaynaklarına erişim izni veren [yönetim sertifikaları](../cloud-services/cloud-services-certs-create.md) .
- Azure Sanal Makineleri gibi hizmetler için Uzaktan Erişim kimlik bilgileri.

Amaç, hedef dizinde erişimleri olmaması için kaynak dizindeki kullanıcılardan erişimi kaldırmak istiyorsanız, herhangi bir kimlik bilgisi döndürmeyi göz önünde bulundurmanız gerekir. Kimlik bilgileri güncelleştirilene kadar, kullanıcılar aktarımdan sonra erişime sahip olmaya devam edecektir.

1. Depolama hesabı erişim anahtarlarını döndürün. Daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md).

1. Azure SQL veritabanı veya Azure Service Bus mesajlaşma gibi diğer hizmetler için erişim anahtarları kullanıyorsanız, erişim anahtarlarını döndürün.

1. Gizli dizileri kullanan kaynaklar için, kaynak ayarlarını açın ve parolayı güncelleştirin.

1. Sertifikaları kullanan kaynaklar için sertifikayı güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure aboneliğinin faturalama sahipliğini başka bir hesaba aktarma](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Azure aboneliklerini aboneler ile CSP'ler arasında aktarma](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure Active Directory kiracınıza bir Azure aboneliğini ekleme veya ilişkilendirme](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Kurumsal senaryolarda Azure Lighthouse](../lighthouse/concepts/enterprise.md)
