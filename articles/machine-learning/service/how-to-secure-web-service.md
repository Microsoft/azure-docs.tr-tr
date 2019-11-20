---
title: SSL kullanarak güvenli Web Hizmetleri
titleSuffix: Azure Machine Learning
description: Azure Machine Learning aracılığıyla dağıtılan bir Web hizmetinin çok güvenli bir şekilde HTTPS 'yi nasıl etkinleştireceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: f6a6f50a86dc58299a1c1b5994dd1d19cc915e6c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076891"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Azure Machine Learning aracılığıyla bir Web hizmetini güvenli hale getirmek için SSL kullanma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning aracılığıyla dağıtılan bir Web hizmetinin güvenliğini sağlama gösterilmektedir.

Web hizmetlerine erişimi kısıtlamak ve istemcilerin gönderebileceği verileri güvenli hale getirmek için [https](https://en.wikipedia.org/wiki/HTTPS) 'yi kullanırsınız. HTTPS, iki arasındaki iletişimleri şifreleyerek bir istemci ve Web hizmeti arasındaki iletişimin güvenliğini sağlamaya yardımcı olur. Şifreleme, [Aktarım Katmanı Güvenliği (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)kullanır. TLS, bazen TLS 'nin öncülü olan *Güvenli Yuva Katmanı* (SSL) olarak adlandırılır.

> [!TIP]
> Azure Machine Learning SDK, güvenli iletişimlerle ilgili özellikler için "SSL" terimini kullanır. Bu, Web hizmetinizin *TLS*kullanmayacağınız anlamına gelmez. SSL yalnızca daha yaygın olarak tanınan bir terimdir.

TLS ve SSL her ikisi de şifreleme ve kimlik doğrulamaya yardımcı olan *dijital sertifikaları*kullanır. Dijital sertifikaların nasıl çalıştığı hakkında daha fazla bilgi için Vikipedi topic [ortak anahtar altyapısına](https://en.wikipedia.org/wiki/Public_key_infrastructure)bakın.

> [!WARNING]
> Web hizmetiniz için HTTPS kullanmıyorsanız, hizmete gönderilen ve hizmetten gönderilen veriler Internet 'te başkaları tarafından görülebilir.
>
> HTTPS Ayrıca istemcinin bağlandığı sunucunun orijinalliğini doğrulamasını sağlar. Bu özellik [, istemcileri ortadaki adam](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) saldırılarına karşı korur.

Bu, bir Web hizmetinin güvenliğini sağlamaya yönelik genel bir işlemdir:

1. Bir etki alanı adını alın.

2. Dijital bir sertifika alın.

3. Web hizmetini SSL etkinken dağıtın veya güncelleştirin.

4. Web hizmetine işaret edecek şekilde güncelleştirin.

> [!IMPORTANT]
> Azure Kubernetes Service 'e (AKS) dağıtım yapıyorsanız, kendi sertifikanızı satın alabilir veya Microsoft tarafından sağlanmış bir sertifikayı kullanabilirsiniz. Microsoft 'tan bir sertifika kullanıyorsanız, bir etki alanı adı veya SSL sertifikası almanız gerekmez. Daha fazla bilgi için bu makalenin [SSL ve dağıtımı etkinleştirme](#enable) bölümüne bakın.

[Dağıtım hedefleri](how-to-deploy-and-where.md)genelinde güvenli hale getirilçalışırken küçük farklılıklar vardır.

## <a name="get-a-domain-name"></a>Bir etki alanı adı

Zaten bir etki alanı adınız yoksa, bir *etki alanı adı kaydedicisinde*bir tane satın alın. İşlem ve fiyat kayıt şirketlerinde arasında farklılık gösterir. Kaydedici, etki alanı adını yönetmek için araçlar sağlar. Tam etki alanı adını (FQDN) (örneğin, www\.contoso.com) Web hizmetinizi barındıran IP adresine eşlemek için bu araçları kullanabilirsiniz.

## <a name="get-an-ssl-certificate"></a>Bir SSL sertifikası alma

SSL sertifikası almanın pek çok yolu vardır (dijital sertifika). En yaygın olarak, bir *sertifika yetkilisinden* (CA) bir sertifika satın alımdır. Sertifikayı nereden alacağınız bağımsız olarak, aşağıdaki dosyalar gereklidir:

* A **sertifika**. Sertifika, tam sertifika zincirini içermelidir ve "pek-Encoded" olmalıdır.
* A **anahtar**. Anahtar Ayrıca pek kodlu olmalıdır.

Bir sertifika istediğinizde, Web hizmeti için kullanmayı planladığınız adresin FQDN 'sini sağlamanız gerekir (örneğin, www\.contoso.com). Sertifikaya atılabilecek adres ve istemcilerin kullandığı adres, Web hizmetinin kimliğini doğrulamak için karşılaştırılır. Bu adresler eşleşmezse istemci bir hata iletisi alır.

> [!TIP]
> Sertifika yetkilisi sertifikayı ve anahtarı pek kodlu dosyalar olarak sağlayamıyorum, biçimi değiştirmek için [OpenSSL](https://www.openssl.org/) gibi bir yardımcı program kullanabilirsiniz.

> [!WARNING]
> *Otomatik olarak imzalanan* sertifikaları yalnızca geliştirme amacıyla kullanın. Bunları üretim ortamlarında kullanmayın. Otomatik olarak imzalanan sertifikaları, uygulamaları istemcinizde sorunlara neden olabilir. Daha fazla bilgi için, istemci uygulamanızın kullandığı ağ kitaplıklarının belgelerine bakın.

## <a id="enable"></a>SSL 'yi etkinleştirme ve dağıtma

SSL etkinken hizmeti dağıtmak (veya yeniden dağıtmak) için, *ssl_enabled* parametresini uygun olduğunda "true" olarak ayarlayın. *Ssl_certificate* parametresini *sertifika* dosyasının değerine ayarlayın. *Ssl_key* *anahtar* dosyasının değerine ayarlayın.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>AKS ve Field üzerinde dağıtma-programlanabilir kapı dizisi (FPGA)

  > [!NOTE]
  > Bu bölümdeki bilgiler, tasarımcı için güvenli bir Web hizmeti dağıttığınızda de geçerlidir. Python SDK 'yı kullanmayı bilmiyorsanız bkz. [Python için Azure MACHINE LEARNING SDK nedir?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

AKS 'e dağıttığınızda, yeni bir AKS kümesi oluşturabilir veya var olan bir küme ekleyebilirsiniz. Küme oluşturma veya ekleme hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmet kümesine model dağıtma](how-to-deploy-azure-kubernetes-service.md).
  
-  Yeni bir küme oluşturursanız, **[Akscompute. provisionining_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** kullanılır.
- Var olan bir kümeyi eklerseniz, **[Akscompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** kullanırsınız. Her ikisi de **Enable_ssl** yöntemi olan bir yapılandırma nesnesi döndürür.

**Enable_ssl** yöntemi, Microsoft tarafından veya satın aldığınız bir sertifika tarafından sunulan bir sertifikayı kullanabilir.

  * Microsoft 'tan bir sertifika kullandığınızda *leaf_domain_label* parametresini kullanmanız gerekir. Bu parametre, hizmetin DNS adını oluşturur. Örneğin, "contoso" değeri bir etki alanı adı "contoso\<altı-rastgele-karakter > oluşturur.\<azureregion >. cloudapp. Azure. com ", burada \<azureregion >, hizmeti içeren bölgedir. İsteğe bağlı olarak, mevcut *leaf_domain_label*üzerine yazmak için *overwrite_existing_domain* parametresini kullanabilirsiniz.

    SSL etkinken hizmeti dağıtmak (veya yeniden dağıtmak) için, *ssl_enabled* parametresini uygun olduğunda "true" olarak ayarlayın. *Ssl_certificate* parametresini *sertifika* dosyasının değerine ayarlayın. *Ssl_key* *anahtar* dosyasının değerine ayarlayın.

    > [!IMPORTANT]
    > Microsoft 'tan bir sertifika kullandığınızda, kendi sertifikanızı veya etki alanı adınızı satın almanız gerekmez.

    Aşağıdaki örnek, Microsoft 'tan bir SSL sertifikası sağlayan bir yapılandırmanın nasıl oluşturulacağını göstermektedir:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * *Satın aldığınız bir sertifikayı*kullandığınızda *ssl_cert_pem_file*, *ssl_key_pem_file*ve *ssl_cname* parametrelerini kullanırsınız. Aşağıdaki örnek, satın aldığınız bir SSL sertifikası kullanan bir yapılandırma oluşturmak için *. pek* dosyalarının nasıl kullanılacağını gösterir:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

*Enable_ssl*hakkında daha fazla bilgi için bkz. [aksprovisioningconfiguration. Enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) ve [aksattachconfiguration. Enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Azure Container Instances dağıtma

Azure Container Instances ' a dağıtırken, aşağıdaki kod parçacığı gösterdiği gibi, SSL ile ilgili parametrelerin değerlerini sağlarsınız:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Daha fazla bilgi için bkz. [Aciwebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Güncelleştirin

Ardından, DNS sunucunuzun web hizmetine işaret edecek şekilde güncelleştirmeniz gerekir.

+ **Container Instances için:**

  Etki alanı adınız için DNS kaydını güncelleştirmek üzere etki alanı ad kaydedicinizden araçları kullanın. Kayıt, hizmetin IP adresini göstermelidir.

  İstemci, kayıt alanına ve etki alanı adı için yapılandırılmış "yaşam süresi" (TTL) değerine bağlı olarak, etki alanı adını çözebilmek için dakika veya saat gecikme süresi olabilir.

+ **AKS için:**

  > [!WARNING]
  > Hizmeti Microsoft 'un bir sertifikası kullanarak oluşturmak için *leaf_domain_label* kullandıysanız, kümenin DNS değerini el ile güncelleştirin. Değer otomatik olarak ayarlanmalıdır.

  Sol bölmedeki **Ayarlar** ' ın altındaki **yapılandırma** sekmesinde aks KÜMESININ genel IP adresinin DNS 'sini güncelleştirin. (Aşağıdaki resme bakın.) Genel IP adresi, AKS aracı düğümlerini ve diğer ağ kaynaklarını içeren kaynak grubu altında oluşturulan bir kaynak türüdür.

  [![Azure Machine Learning: SSL ile Web hizmetlerinin güvenliğini sağlama](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>SSL sertifikasını güncelleştirme

SSL sertifikalarının süre sonu ve yenilenmesi gerekiyor. Genellikle bu her yıl gerçekleşir. Azure Kubernetes hizmetine dağıtılan modellerle ilgili sertifikanızı güncelleştirmek ve yenilemek için aşağıdaki bölümlerdeki bilgileri kullanın:

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft tarafından oluşturulan bir sertifikayı güncelleştirme

Sertifika ilk olarak Microsoft tarafından oluşturulduysa (hizmeti oluşturmak için *leaf_domain_label* kullanılırken), sertifikayı güncelleştirmek için aşağıdaki örneklerden birini kullanın:

**SDK 'Yı kullanma**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**CLI kullanma**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Daha fazla bilgi için aşağıdaki başvuru belgelerine bakın:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Özel sertifikayı Güncelleştir

Sertifika ilk olarak bir sertifika yetkilisi tarafından oluşturulduysa, aşağıdaki adımları kullanın:

1. Sertifikayı yenilemek için sertifika yetkilisi tarafından verilen belgeleri kullanın. Bu işlem yeni sertifika dosyaları oluşturur.

1. Hizmeti yeni sertifikayla güncelleştirmek için SDK veya CLı kullanın:

    **SDK 'Yı kullanma**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **CLI kullanma**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Daha fazla bilgi için aşağıdaki başvuru belgelerine bakın:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>SSL 'yi devre dışı bırak

Azure Kubernetes hizmetine dağıtılan bir modelin SSL 'yi devre dışı bırakmak için, `status="Disabled"`bir `SslConfiguration` oluşturun ve sonra bir güncelleştirme gerçekleştirin:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Sonraki adımlar
Şunları nasıl yapacağınızı öğrenin:
+ [Bir Web hizmeti olarak dağıtılan makine öğrenimi modelini kullanma](how-to-consume-web-service.md)
+ [Bir Azure sanal ağı içinde denemeleri ve çıkarımı güvenle çalıştırın](how-to-enable-virtual-network.md)
