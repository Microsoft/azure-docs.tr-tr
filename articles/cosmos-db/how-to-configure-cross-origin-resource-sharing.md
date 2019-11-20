---
title: Azure Cosmos DB 'de çıkış noktaları arası kaynak paylaşımı (CORS)
description: Bu makalede Azure portal ve Azure Resource Manager şablonları kullanılarak Azure Cosmos DB arası kaynak paylaşımının (CORS) nasıl yapılandırılacağı açıklanmaktadır.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 2823ae22c8128f52ae67cf283a9a619a03abd719
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580665"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımını (CORS) yapılandırma

Çıkış noktaları arası kaynak paylaşımı (CORS), bir etki alanı altında çalışan bir Web uygulamasının başka bir etki alanındaki kaynaklara erişmesine olanak tanıyan bir HTTP özelliğidir. Web tarayıcıları, bir Web sayfasının farklı bir etki alanındaki API 'Leri aramasını önleyen aynı kaynak ilkesi olarak bilinen bir güvenlik kısıtlaması uygular. Ancak CORS, kaynak etki alanının başka bir etki alanındaki API 'Leri çağırmasını sağlamak için güvenli bir yol sağlar. Azure Cosmos DB 'deki çekirdek (SQL) API 'SI artık "Allowedorigin" üst bilgisini kullanarak çıkış noktaları arası kaynak paylaşımını (CORS) desteklemektedir. Azure Cosmos hesabınız için CORS desteğini etkinleştirdikten sonra, belirttiğiniz kurallara göre izin verilip verilmeyeceğini belirlemede yalnızca kimliği doğrulanmış istekler değerlendirilir.

Çıkış noktaları arası kaynak paylaşımı (CORS) ayarını Azure portal veya bir Azure Resource Manager şablonundan yapılandırabilirsiniz. Çekirdek (SQL) API kullanan Cosmos hesaplarında, Azure Cosmos DB hem Node. js hem de tarayıcı tabanlı ortamlarda çalışacak bir JavaScript kitaplığını destekler. Bu kitaplık artık ağ geçidi modu kullanırken CORS desteğinin avantajlarından yararlanabilir. Bu özelliği kullanmak için hiçbir istemci tarafı yapılandırması gerekmez. CORS desteğiyle, bir tarayıcıdan alınan kaynaklar [JavaScript kitaplığı](https://www.npmjs.com/package/@azure/cosmos) aracılığıyla Azure Cosmos DB doğrudan erişebilir veya basit işlemler için doğrudan [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) .

> [!NOTE]
> CORS desteği yalnızca Azure Cosmos DB Core (SQL) API 'SI için geçerlidir ve desteklenir. Cassandra, Gremlin veya MongoDB için Azure Cosmos DB API 'Leri için geçerli değildir, çünkü bu protokoller istemci-sunucu iletişimi için HTTP kullanmaz.

## <a name="enable-cors-support-from-azure-portal"></a>Azure portal CORS desteğini etkinleştir

Azure portal kullanarak, çıkış noktaları arası kaynak paylaşımını etkinleştirmek için aşağıdaki adımları kullanın:

1. Azure Cosmos DB hesabınıza gidin. **CORS** dikey penceresini açın.

2. Azure Cosmos DB hesabınıza çapraz kaynak çağrıları yapaabilecek çıkış noktaları için virgülle ayrılmış bir liste belirtin. Örneğin, `https://www.mydomain.com`, `https://mydomain.com``https://api.mydomain.com`. Ayrıca, tüm kaynaklarına izin vermek ve **Gönder**' i seçmek için "\*" joker karakterini kullanabilirsiniz. 

   > [!NOTE]
   > Şu anda, etki alanı adının bir parçası olarak joker karakter kullanamazsınız. Örneğin `https://*.mydomain.net` biçim henüz desteklenmiyor. 

   ![Azure portal kullanarak Cross-Origin kaynak paylaşımını etkinleştirme](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>Kaynak Yöneticisi şablonundan CORS desteğini etkinleştir

CORS 'yi bir Kaynak Yöneticisi şablonu kullanarak etkinleştirmek için, "Allowedkaynakları" özelliği olan "CORS" bölümünü var olan herhangi bir şablona ekleyin. Aşağıdaki JSON, CORS 'nin etkinleştirildiği yeni bir Azure Cosmos hesabı oluşturan şablona bir örnektir.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
        }
    }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Tarayıcıdan Azure Cosmos DB JavaScript kitaplığını kullanma

Bugün, Azure Cosmos DB JavaScript kitaplığı yalnızca paketiyle birlikte sunulan kitaplığın CommonJS sürümüne sahiptir. Bu kitaplığı tarayıcıdan kullanmak için, bir tarayıcı uyumlu kitaplık oluşturmak üzere ROLLUP veya WebPack gibi bir araç kullanmanız gerekir. Belirli Node. js kitaplıklarında bunlara yönelik tarayıcı modülleri olmalıdır. Aşağıda, gerekli sahte ayarlarına sahip bir WebPack yapılandırma dosyası örneği verilmiştir.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Yeni öğeler oluşturulduğunda gerçek zamanlı güncelleştirmeler gönderen bir ToDo uygulaması oluşturmak için Azure Cosmos DB JavaScript SDK kitaplığıyla TypeScript ve WebPack kullanan bir [kod örneğidir](https://github.com/christopheranderson/cosmos-browser-sample) .
En iyi uygulama olarak, tarayıcıda Azure Cosmos DB iletişim kurmak için birincil anahtarı kullanmayın. Bunun yerine, iletişim kurmak için kaynak belirteçlerini kullanın. Kaynak belirteçleri hakkında daha fazla bilgi için bkz. [Azure Cosmos DB için erişimi güvenli hale getirme](secure-access-to-data.md#resource-tokens) .

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos hesabınızı güvenli hale getirmeye yönelik diğer yollar hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Cosmos DB makale için bir güvenlik duvarı yapılandırın](how-to-configure-firewall.md) .

* [Azure Cosmos DB hesabınız için sanal ağ ve alt ağ tabanlı erişimi yapılandırın](how-to-configure-vnet-service-endpoint.md)
