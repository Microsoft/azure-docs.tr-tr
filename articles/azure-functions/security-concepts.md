---
title: Azure Işlevlerinin güvenliğini sağlama
description: Azure 'da çalışan işlev kodunuzun genel saldırılara karşı daha güvenli hale getirme hakkında bilgi edinin.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368498"
---
# <a name="securing-azure-functions"></a>Azure Işlevlerinin güvenliğini sağlama

Birçok şekilde, sunucusuz işlevlerin güvenli geliştirilmesini, dağıtılmasını ve çalışmasını planlamak, Web tabanlı veya bulutta barındırılan tüm uygulamalar için çok benzer. [Azure App Service](../app-service/index.yml) , işlev uygulamalarınız için barındırma altyapısı sağlar. Bu makalede, işlev kodunuzu çalıştırmaya yönelik güvenlik stratejileri ve App Service işlevlerinizin güvenliğini sağlamanıza nasıl yardımcı olabilir. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

[Azure Güvenlik kıyaslaması](../security/benchmarks/overview.md)' nı izleyen bir dizi güvenlik önerisi için bkz. Azure [Için Azure Güvenlik temeli işlevleri](security-baseline.md).

## <a name="secure-operation"></a>Güvenli işlem 

Bu bölüm, işlev uygulamanızı mümkün olduğunca güvenli şekilde yapılandırmak ve çalıştırmak için size rehberlik eder. 

### <a name="security-center"></a>Güvenlik Merkezi

Güvenlik Merkezi, portaldaki işlev uygulamanızla tümleştirilir. Yapılandırma ile ilgili olası güvenlik açıklarına yönelik hızlı bir değerlendirme sağlar. Adanmış bir planda çalışan işlev uygulamaları, ek bir maliyet için Güvenlik Merkezi 'nin gerçek zamanlı güvenlik özelliklerini de kullanabilir. Daha fazla bilgi edinmek için bkz. [Azure App Service Web uygulamalarınızı ve API 'Lerinizi koruma](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Günlüğe kaydetme ve izleme

Saldırıları algılamadaki yollardan biri, etkinlik izleme etkinliği ve günlüğe kaydetme analizinden biridir. İşlevler, işlev uygulamanız için günlüğü, performansı ve hata verilerini toplamak üzere Application Insights ile tümleşir. Application Insights, performans bozuklularını otomatik olarak algılar ve sorunları tanılamanıza ve işlevlerinizin nasıl kullanıldığını anlamanıza yardımcı olacak güçlü analiz araçları içerir. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

İşlevler ayrıca, daha kolay analize yönelik işlev uygulama günlüklerini sistem olaylarıyla birleştirmenize olanak tanımak için Azure Izleyici günlükleri ile tümleşir. Bir günlük Analizi çalışma alanı gibi işlevleriniz için tercih ettiğiniz hedefe yönelik platform günlüklerinin ve ölçümlerinin akış dışa aktarılmasını yapılandırmak için tanılama ayarlarını kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. Azure [Izleme günlükleri Ile Azure Işlevlerini izleme](functions-monitor-log-analytics.md). 

Kurumsal düzeyde tehdit algılama ve yanıt otomasyonu için günlüklerinizi ve olaylarınızı bir günlük Analizi çalışma alanına akış. Daha sonra bu çalışma alanına Azure Sentinel 'i bağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure Sentinel nedir](../sentinel/overview.md).  

Observability hakkında daha fazla güvenlik önerisi için bkz. Azure [Için Azure Güvenlik temeli işlevleri](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>HTTPS gerektir

Varsayılan olarak, istemciler hem HTTP ya da HTTPS kullanarak işlev uç noktalarına bağlanabilir. HTTPS SSL/TLS protokolünü kullandığından, hem şifreli hem de kimliği doğrulanmış güvenli bir bağlantı sağlamak için HTTP 'yi HTTPs 'ye yönlendirmelisiniz. Hakkında bilgi edinmek için bkz. [https 'Yi zorlama](../app-service/configure-ssl-bindings.md#enforce-https).

HTTPS 'yi zorunlu kıldığınızda, en son TLS sürümünü de Ihtiyacınız vardır. Hakkında bilgi edinmek için bkz. [TLS sürümlerini zorlama](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Daha fazla bilgi için bkz. [güvenli bağlantılar (TSL)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>İşlev erişim tuşları

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Sistem anahtarı 

Belirli Uzantılar, Web kancası uç noktalarına erişmek için sistem tarafından yönetilen bir anahtar gerektirebilir. Sistem anahtarları, iç bileşenler tarafından çağrılan uzantıya özgü işlev uç noktaları için tasarlanmıştır. Örneğin, [Event Grid tetikleyicisi](functions-bindings-event-grid-trigger.md) , aboneliğin tetikleyici uç noktasını çağırırken bir sistem anahtarı kullanmasını gerektirir. Dayanıklı İşlevler Ayrıca, [dayanıklı görev uzantısı API 'lerini](durable/durable-functions-http-api.md)çağırmak için sistem anahtarlarını kullanır. 

Sistem anahtarlarının kapsamı uzantı tarafından belirlenir, ancak genellikle tüm işlev uygulaması için geçerlidir. Sistem anahtarları yalnızca belirli Uzantılar tarafından oluşturulabilir ve değerlerini açıkça ayarlayamazsınız. Diğer anahtarlar gibi, portaldan anahtar için veya anahtar API 'Lerini kullanarak yeni bir değer oluşturabilirsiniz.

#### <a name="keys-comparison"></a>Anahtarlar karşılaştırması

Aşağıdaki tabloda, çeşitli erişim anahtarları türleri için kullanımlar karşılaştırılır:

| Eylem                                        | Kapsam                    | Geçerli anahtarlar         |
|-----------------------------------------------|--------------------------|--------------------|
| İşlev yürütme                            | Belirli işlev        | İşlev           |
| İşlev yürütme                            | Any işlevi             | İşlev veya ana bilgisayar   |
| Yönetici uç noktası çağırma                        | İşlev uygulaması             | Ana bilgisayar (yalnızca ana) |
| Dayanıklı görev uzantısı API 'Lerini çağırın              | İşlev uygulaması<sup>1</sup> | Sistem<sup>2</sup> |
| Uzantıya özgü Web kancasını çağırma (iç) | İşlev uygulaması<sup>1</sup> | Sistem<sup>2</sup> |

<sup>1</sup> Uzantı tarafından belirlenen kapsam.  
<sup>2</sup> Uzantıya göre ayarlanan belirli adlar.

Erişim anahtarları hakkında daha fazla bilgi için bkz. [http tetikleyici bağlama makalesi](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Gizli depolar

Anahtarlar varsayılan olarak, ayar tarafından belirtilen hesapta bir BLOB depolama kapsayıcısında depolanır `AzureWebJobsStorage` . Belirli uygulama ayarlarını, bu davranışı geçersiz kılmak ve anahtarları farklı bir konumda depolamak için kullanabilirsiniz.

|Konum  |Ayar | Değer | Açıklama  |
|---------|---------|---------|---------|
|Farklı depolama hesabı     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Anahtarları, belirtilen SAS URL 'sini temel alarak ikinci bir depolama hesabının BLOB depolama alanında depolar. Anahtarlar, işlev uygulamanıza özel bir gizli anahtar kullanılarak depolanmadan önce şifrelenir. |
|Dosya sistemi   | `AzureWebJobsSecretStorageType`   |  `files`       | Dosyalar, depolama öncesinde, işlev uygulamanıza özel bir gizli anahtar kullanılarak şifrelenmiş olarak şifrelenir. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | Kasasının, barındırma kaynağının sistem tarafından atanan yönetilen kimliğine karşılık gelen bir erişim ilkesi olmalıdır. Erişim ilkesi, kimliğe aşağıdaki gizli izinleri vermelidir: `Get` , `Set` , `List` , ve `Delete` . <br/>Yerel olarak çalışırken geliştirici kimliği kullanılır ve ayarlar [ dosyadalocal.settings.js](functions-run-local.md#local-settings-file)olmalıdır. | 
|Kubernetes Gizli Dizileri  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (isteğe bağlı) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Yalnızca Kubernetes 'te Işlevler çalışma zamanı çalıştırılırken desteklenir. `AzureWebJobsKubernetesSecretName`Ayarlanmadıysa, depo salt okunurdur olarak kabul edilir. Bu durumda, değerlerin dağıtımdan önce oluşturulması gerekir. Azure Functions Core Tools, Kubernetes 'e dağıtım yaparken değerleri otomatik olarak oluşturur.|

### <a name="authenticationauthorization"></a>Kimlik doğrulama/yetkilendirme

İşlev anahtarları istenmeyen erişim için bazı hafifler sağlayacağından, işlev uç noktalarınızı gerçekten güvenli hale getirmenin tek yolu, işlevlerimize erişen istemcilerin pozitif kimlik doğrulamasını uygulamalarıdır. Ardından kimliğe göre yetkilendirme kararları alabilirsiniz.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>İzinler

Herhangi bir uygulama veya hizmette olduğu gibi, amaç, işlev uygulamanızı en düşük olası izinlerle çalıştırır. 

#### <a name="user-management-permissions"></a>Kullanıcı yönetimi izinleri

İşlevler, yerleşik [Azure rol tabanlı erişim denetimini (Azure RBAC)](../role-based-access-control/overview.md)destekler. Işlevler tarafından desteklenen Azure rolleri [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor), [sahip](../role-based-access-control/built-in-roles.md#owner)ve [okuyucudur](../role-based-access-control/built-in-roles.md#owner). 

İzinler, işlev uygulama düzeyinde etkilidir. Birçok işlev uygulama düzeyi görevi gerçekleştirmek için katkıda bulunan rolü gereklidir. Bir işlev uygulamasını yalnızca sahip rolü silebilir. 

#### <a name="organize-functions-by-privilege"></a>İşlevleri ayrıcalığa göre düzenleme 

Uygulama ayarlarında depolanan bağlantı dizeleri ve diğer kimlik bilgileri, işlev uygulamasındaki tüm işlevleri ilişkili kaynakta aynı izin kümesi sağlar. Bu kimlik bilgilerini kullanmayan işlevleri ayrı bir işlev uygulamasına taşıyarak belirli kimlik bilgilerine erişimi olan işlev sayısını en aza indirgemeniz önerilir. Farklı işlev uygulamalarındaki işlevler arasında veri geçirmek için her zaman [işlev zinciri](/learn/modules/chain-azure-functions-data-using-bindings/) gibi teknikler kullanabilirsiniz.  

#### <a name="managed-identities"></a>Yönetilen kimlikler

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Yönetilen kimlikler, bazı tetikleyicilerden ve bağlamalardan bağlantılar için gizli dizi yerine kullanılabilir. Bkz. [kimlik tabanlı bağlantılar](#identity-based-connections).

Daha fazla bilgi için bkz. [App Service ve Azure işlevleri için Yönetilen kimlikler kullanma](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>CORS erişimini kısıtla

[Çıkış noktaları arası kaynak paylaşımı (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) , başka bir etki alanında çalışan Web uygulamalarının http tetikleyici uç noktalarınıza istek yapmasına izin vermek için bir yoldur. App Service, HTTP isteklerinde gerekli CORS üst bilgilerini teslim etmek için yerleşik destek sağlar. CORS kuralları bir işlev uygulama düzeyinde tanımlanmıştır.  

Tüm sitelerin uç noktanıza erişmesine izin veren bir joker karakter kullanmak mümkün olsa da. Ancak, bu, siteler arası betik saldırılarını önlemeye yardımcı olmak üzere CORS 'nin amacını erteler. Bunun yerine, uç noktanıza erişmesi gereken her bir Web uygulamasının etki alanı için ayrı bir CORS girişi ekleyin. 

### <a name="managing-secrets"></a>Gizli dizileri yönetme 

Çeşitli hizmetlere bağlanabilme ve kaynakların kodunuzu çalıştırması için ihtiyaç duyduğu için, işlev uygulamalarının bağlantı dizeleri ve hizmet anahtarları gibi gizli dizilerle erişebilmeleri gerekir. Bu bölüm, işlevleriniz için gereken gizli dizileri nasıl depolayabileceğinizi açıklar.

Kod kodunuzda gizli dizileri hiçbir şekilde depolamaýþın. 

#### <a name="application-settings"></a>Uygulama ayarları

Varsayılan olarak, işlev uygulamanız ve bağlamalarınız tarafından kullanılan bağlantı dizelerini ve gizli dizileri uygulama ayarları olarak depoladığınızda. Bu, bu kimlik bilgilerini hem işlev kodunuzun hem de işlev tarafından kullanılan çeşitli bağlamalarda kullanılabilir hale getirir. Uygulama ayarı (anahtar) adı, gizli olan gerçek değeri almak için kullanılır. 

Örneğin, her işlev uygulaması, çalışma zamanı tarafından kullanılan ilişkili bir depolama hesabı gerektirir. Varsayılan olarak, bu depolama hesabı bağlantısı adlı bir uygulama ayarında saklanır `AzureWebJobsStorage` .

Uygulama ayarları ve bağlantı dizeleri Azure 'da şifreli olarak depolanır. Bunlar, yalnızca uygulama başlatıldığında uygulamanızın işlem belleğine eklenmeden önce çözülür. Şifreleme anahtarları düzenli olarak döndürülür. Bunun yerine, Sırlarınızın güvenli depolama alanını yönetmeyi tercih ediyorsanız, uygulama ayarının Azure Key Vault başvurular olması gerekir. 

Ayrıca, yerel bilgisayarınızda işlevleri geliştirirken dosya local.settings.js, ayarları varsayılan olarak şifreleyebilirsiniz. Daha fazla bilgi edinmek için `IsEncrypted` [yerel ayarlar dosyasındaki](functions-run-local.md#local-settings-file)özelliğine bakın.  

#### <a name="key-vault-references"></a>Key Vault başvuruları

Uygulama ayarları birçok sayıda işlev için yeterli olsa da, aynı gizli dizileri birden çok hizmet arasında paylaşmak isteyebilirsiniz. Bu durumda, parolaların yedekli şekilde depolanması daha olası güvenlik açıklarına neden olur. Daha güvenli bir yaklaşım, merkezi bir gizli depolama hizmetidir ve gizli dizileri yerine bu hizmete yönelik başvuruları kullanır.      

[Azure Key Vault](../key-vault/general/overview.md) , erişim ilkeleri ve denetim geçmişi üzerinde tam denetim ile merkezi gizli dizi yönetimi sağlayan bir hizmettir. Uygulama ayarlarınızda bir bağlantı dizesi veya anahtar yerine bir Key Vault başvurusu kullanabilirsiniz. Daha fazla bilgi için bkz. [App Service ve Azure işlevleri için Key Vault başvurularını kullanma](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="identity-based-connections"></a>Kimlik tabanlı bağlantılar

Kimlikler, bazı kaynaklara bağlanmak için gizli dizileri yerine kullanılabilir. Bu, bir gizli dizi yönetimine gerek duyulmayan avantajına sahiptir ve daha ayrıntılı erişim denetimi ve denetimi sağlar. 

[Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)bağlantısını oluşturan kodu yazarken, bir parola veya bağlantı dizesi yerine bir kimlik kullanmayı seçebilirsiniz. Her iki bağlantı yönteminin ayrıntıları, her hizmet için belgelerde ele alınmıştır.

Bazı Azure Işlevleri tetikleyici ve bağlama uzantıları, kimlik tabanlı bağlantı kullanılarak yapılandırılabilir. Bugün, [Azure Blob](./functions-bindings-storage-blob.md) ve [Azure kuyruk](./functions-bindings-storage-queue.md) uzantılarını içerir. Bu uzantıların bir kimlik kullanacak şekilde nasıl yapılandırılacağı hakkında bilgi için bkz. [Azure işlevleri 'nde kimlik tabanlı bağlantıları kullanma](./functions-reference.md#configure-an-identity-based-connection).

### <a name="set-usage-quotas"></a>Kullanım kotalarını ayarlama

Tüketim planında çalışan işlevlerde kullanım kotası ayarlamayı göz önünde bulundurun. İşlev uygulamanızdaki işlevlerin toplam yürütülmesi için günlük GB-sn sınırı ayarladığınızda, sınıra ulaşıldığında yürütme durdurulur. Bu, işlevlerinizi yürüten kötü amaçlı kodun etkisini azaltmaya yardımcı olabilir. İşlevleriniz için tüketimin nasıl tahmin alınacağını öğrenmek için bkz. [Tüketim planı maliyetlerini tahmin](functions-consumption-costs.md)etme. 

### <a name="data-validation"></a>Veri doğrulama

İşlevleriniz tarafından kullanılan Tetikleyiciler ve bağlamalar hiçbir ek veri doğrulaması sağlamaz. Kodunuzun, bir tetikleyiciden veya giriş bağlamalarından alınan tüm verileri doğrulaması gerekir. Bir yukarı akış hizmeti tehlikedeyse, kendi işlevleriniz aracılığıyla doğrulanmamış girişlerin akışını istemezsiniz. Örneğin, işleviniz bir Azure depolama kuyruğundan verileri ilişkisel bir veritabanında depoluyorsa, SQL ekleme saldırılarını önlemek için verileri doğrulamanız ve komutlarınızı parametreleştirebilirsiniz. 

İşlevinizde gelen verilerin zaten onaylanmış veya ayıklanmış olduğunu varsaymayın. Ayrıca, çıkış bağlamalarına yazılan verilerin geçerli olduğunu doğrulamak iyi bir fikirdir. 

### <a name="handle-errors"></a>Hataları işleme

Temel görünse de, işlevlerinizi doğru hata işleme yazmak önemlidir. İşlenmemiş hatalar balonu ana bilgisayara kadar yapılır ve çalışma zamanı tarafından işlenir. Farklı bağlamalar hataların işlenmesini farklı şekilde işler. Daha fazla bilgi için bkz. [Azure işlevleri hata işleme](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Uzaktan hata ayıklamayı devre dışı bırak

İşlevlerinizin etkin bir şekilde ayıklanmasının dışında, uzaktan hata ayıklamanın devre dışı bırakıldığından emin olun. Portalda işlev uygulaması **yapılandırmanızın** **Genel ayarlar** sekmesinde uzaktan hata ayıklamayı devre dışı bırakabilirsiniz. 

### <a name="restrict-cors-access"></a>CORS erişimini kısıtla

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

İzin verilen çıkış listenizde joker karakter kullanmayın. Bunun yerine, isteklerini almak istediğiniz belirli etki alanlarını listeleyin.

### <a name="store-data-encrypted"></a>Şifrelenmiş veri depolama

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Güvenli dağıtım

Tümleştirme araçları Azure Işlevleri, yerel işlev proje kodunu Azure 'a yayımlamayı kolaylaştırır. Azure Işlevleri topolojisi için güvenliği göz önünde bulundurarak dağıtımın nasıl çalıştığını anlamak önemlidir.   

### <a name="deployment-credentials"></a>Dağıtım kimlik bilgileri

App Service dağıtımları bir dağıtım kimlik bilgileri kümesi gerektirir. Bu dağıtım kimlik bilgileri, işlev uygulaması dağıtımlarınızın güvenliğini sağlamak için kullanılır. Dağıtım kimlik bilgileri App Service platformu tarafından yönetilir ve bekleyen olarak şifrelenir. 

İki tür dağıtım kimlik bilgisi vardır:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Şu anda Key Vault dağıtım kimlik bilgileri için desteklenmez. Dağıtım kimlik bilgilerini yönetme hakkında daha fazla bilgi için bkz. [Azure App Service için dağıtım kimlik bilgilerini yapılandırma](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>FTP 'yi devre dışı bırak

Varsayılan olarak, her bir işlev uygulamasında bir FTP uç noktası etkindir. FTP uç noktasına dağıtım kimlik bilgileri kullanılarak erişilir. 

İşlev kodunuzu dağıtmak için FTP önerilmez. FTP dağıtımları el ile yapılır ve Tetikleyicileri eşitlemeniz gerekir. Daha fazla bilgi için bkz. [FTP dağıtımı](functions-deployment-technologies.md#ftp). 

FTP kullanmayı planlamadığınızda, portalda devre dışı bırakmanız gerekir. FTP kullanmayı seçerseniz, [FTPS 'yi zorunlu](../app-service/deploy-ftp.md#enforce-ftps)kılabilirsiniz.

### <a name="secure-the-scm-endpoint"></a>SCM uç noktası güvenliğini sağlama

Her işlev uygulamasının, `scm` dağıtımlar ve diğer App Service [site uzantıları](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)için gelişmiş araçlar (kudu) hizmeti tarafından kullanılan karşılık gelen bir hizmet uç noktası vardır. İşlev uygulaması için SCM uç noktası, formdaki her zaman bir URL 'dir `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . İşlevlerinizin güvenliğini sağlamak için ağ yalıtımı kullandığınızda, bu uç nokta için de hesabınız olmalıdır. 

Ayrı bir SCM uç noktası sunarak, bir sanal ağda yalıtılmış veya çalışan işlev uygulaması için dağıtımları ve diğer gelişmiş araç işlevlerini kontrol edebilirsiniz. SCM uç noktası hem temel kimlik doğrulamasını (dağıtım kimlik bilgilerini kullanarak) hem de Azure portal kimlik bilgilerinizle çoklu oturum açmayı destekler. Daha fazla bilgi edinmek için bkz. [kudu hizmetine erişme](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Sürekli güvenlik doğrulaması

Güvenliğin geliştirme işlemindeki her adım kabul edilmesi gerektiğinden, sürekli bir dağıtım ortamında güvenlik doğrulamaları da uygulamak mantıklı olur. Bu bazen DevSecOps olarak adlandırılır. Dağıtım işlem hatlarınız için Azure DevOps 'u kullanmak, doğrulamayı dağıtım işlemiyle tümleştirmenize imkan tanır. Daha fazla bilgi için bkz. [CI/CD işlem hattınızla sürekli güvenlik doğrulaması eklemeyi öğrenin](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Ağ güvenliği

İşlev uygulamanıza ağ erişimini kısıtlamak, işlevlerinizin uç noktalarına kimlerin erişebileceğini denetlemenize olanak tanır. İşlevler, işlevlerinizin internet 'e yönlendirilebilir adresler kullanmadan veya bir işlev uç noktasına internet erişimini kısıtlamadan kaynaklara erişmesini sağlamak için App Service altyapıdan yararlanır. Bu ağ seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri ağ seçenekleri](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Erişim kısıtlamalarını ayarla

Erişim kısıtlamaları, uygulamanıza gelen trafiği denetlemek için izin verme/reddetme kurallarının listesini tanımlamanızı sağlar. Kurallar öncelik sırasına göre değerlendirilir. Tanımlı kural yoksa, uygulamanız herhangi bir adresten gelen trafiği kabul eder. Daha fazla bilgi edinmek için bkz. [Azure App Service erişim kısıtlamaları #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Özel site erişimi

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>İşlev uygulamanızı yalıtımına dağıtın

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Ağ geçidi hizmeti kullanma

[Azure Application Gateway](../application-gateway/overview.md) ve [Azure ön kapısı](../frontdoor/front-door-overview.md) gibi ağ geçidi Hizmetleri, bir Web uygulaması güvenlik duvarı (WAF) ayarlamanıza olanak sağlar. WAF kuralları, işlevleriniz için ek bir koruma katmanı sağlayan algılanan saldırıları izlemek veya engellemek için kullanılır. Bir WAF ayarlamak için, işlev uygulamanızın bir aya veya özel uç noktaları (Önizleme) kullanılarak çalışıyor olması gerekir. Daha fazla bilgi için bkz. [Özel uç noktaları kullanma](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Işlevleri için Azure Güvenlik temeli](security-baseline.md)
+ [Azure Işlevleri tanılaması](functions-diagnostics.md)
