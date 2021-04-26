---
title: Azure HDInsight 'ta özel Apache ambarı veritabanı
description: Kendi özel Apache ambarı veritabanınızla HDInsight kümeleri oluşturmayı öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946063"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight kümelerini özel bir ambarı DB ile ayarlama

Apache ambarı, bir Apache Hadoop kümesinin yönetimini ve izlenmesini basitleştirir. Ambarı kullanımı kolay bir Web Kullanıcı arabirimi ve REST API sağlar. Ambarı HDInsight kümelerine dahil edilmiştir ve kümeyi izlemek ve yapılandırma değişiklikleri yapmak için kullanılır.

Normal küme oluştururken, [HDInsight 'ta kümeleri ayarlama](hdinsight-hadoop-provision-linux-clusters.md)gibi diğer makalelerde açıklandığı gibi, ambarı HDInsight tarafından yönetilen ve kullanıcılar tarafından erişilemeyen [S0 Azure SQL veritabanında](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) dağıtılır.

Özel ambarı DB özelliği, yönettiğiniz bir dış veritabanında yeni bir küme dağıtmanıza ve ambarı ayarlamanıza olanak sağlar. Dağıtım bir Azure Resource Manager şablonuyla yapılır. Bu özellik aşağıdaki avantajlara sahiptir:

- Özelleştirme-veritabanının boyut ve işleme kapasitesini seçersiniz. Yoğun iş yüklerine sahip büyük kümeleriniz varsa, daha düşük belirtimlere sahip bir veri ambarı veritabanı yönetim işlemleri için performans sorunu haline gelebilir.
- Esneklik-veritabanını gereksinimlerinize uyacak şekilde ölçeklendirebilirsiniz.
- Denetim-veritabanlarınızın yedeklerini ve güvenliğini, kuruluşunuzun gereksinimlerine uygun bir şekilde yönetebilirsiniz.

Bu makalenin geri kalanında aşağıdaki noktaları ele alınmaktadır:

- Özel ambarı DB özelliğini kullanma gereksinimleri
- Apache ambarı için kendi dış veritabanınızı kullanarak HDInsight kümelerini sağlamak için gereken adımlar

## <a name="custom-ambari-db-requirements"></a>Özel ambarı DB gereksinimleri

Tüm küme türleri ve sürümleri ile özel bir ambarı DB dağıtabilirsiniz. Birden çok küme aynı ambarı DB 'yi kullanamaz.

Özel ambarı DB aşağıdaki diğer gereksinimlere sahiptir:

- Veritabanının adı kısa çizgi veya boşluk içeremez
- Mevcut bir Azure SQL VERITABANı sunucunuzun ve veritabanınızın olması gerekir.
- Ambarı kurulumu için sağladığınız veritabanının boş olması gerekir. Varsayılan dbo şemasında hiç tablo olmaması gerekir.
- Veritabanına bağlanmak için kullanılan kullanıcının veritabanında SELECT, CREATE TABLE ve INSERT izinlerine sahip olması gerekir.
- Ambarı barındıracak sunucuda [Azure hizmetlerine erişime Izin verme](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) seçeneğini açın.
- HDInsight hizmetinden yönetim IP adreslerine güvenlik duvarı kuralında izin verilmesi gerekir. Sunucu düzeyinde güvenlik duvarı kuralına eklenmesi gereken IP adreslerinin listesi için bkz. [HDInsight YÖNETIMI IP adresleri](hdinsight-management-ip-addresses.md) .

Apache ambarı DB 'nizi bir dış veritabanında barındırdığınızda, aşağıdaki noktaları hatırlayın:

- Azure SQL DB 'nin ambarı 'nı tutan ek maliyetleri sizin sorumluluğunuzdadır.
- Özel ambarı DB 'nizi düzenli olarak yedekleyin. Azure SQL veritabanı yedeklemeleri otomatik olarak oluşturur, ancak yedekleme bekletme zaman çerçevesi değişir. Daha fazla bilgi için bkz. [OTOMATIK SQL veritabanı yedeklemeleri hakkında bilgi edinin](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Özel bir ambarı DB ile küme dağıtma

Kendi dış ambarı veritabanınızı kullanan bir HDInsight kümesi oluşturmak için [özel AMBARı DB hızlı başlangıç şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)kullanın.

`azuredeploy.parameters.json`Yeni kümeniz ve ambarı barındıracak veritabanı hakkındaki bilgileri belirtmek için içindeki parametreleri düzenleyin.

Azure CLı kullanarak dağıtımı başlatabilirsiniz. `<RESOURCEGROUPNAME>`Kümenizi dağıtmak istediğiniz kaynak grubuyla değiştirin.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>Veritabanı boyutlandırma

Aşağıdaki tabloda, hangi Azure SQL DB katmanının HDInsight kümenizin boyutuna bağlı olarak seçilecek yönergeler sağlanmaktadır.

| Çalışan düğümlerinin sayısı | Gerekli DB katmanı |
|---|---|
| <= 4 | S0 |
| >4 && <= 8 | S1 |
| >8 && <= 16 | S2 |
| >16 && <= 32 | S3 |
| >32 && <= 64 | S4 |
| >64 && <= 128 | P2 |
| >128 | Destek birimine başvurma |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure HDInsight’ta dış meta veri depolarını kullanma](hdinsight-use-external-metadata-stores.md)
