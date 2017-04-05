# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Frequently asked questions about classic to Azure Resource Manager migration

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Does this migration plan affect any of my existing services or applications that run on Azure virtual machines? 

No. The VMs (classic) are fully supported services in general availability. You can continue to use these resources to expand your footprint on Microsoft Azure.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>What happens to my VMs if I don’t plan on migrating in the near future? 

We are not deprecating the existing classic APIs and resource model. We want to make migration easy, considering the advanced features that are available in the Resource Manager deployment model. We highly recommend that you review [some of the advancements](../articles/azure-resource-manager/resource-manager-deployment-model.md) that are part of IaaS under Resource Manager.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>What does this migration plan mean for my existing tooling? 

Updating your tooling to the Resource Manager deployment model is one of the most important changes that you have to account for in your migration plans.

## <a name="how-long-will-the-management-plane-downtime-be"></a>How long will the management-plane downtime be? 

It depends on the number of resources that are being migrated. For smaller deployments (a few tens of VMs), the whole migration should take less than an hour. For large-scale deployments (hundreds of VMs), the migration can take a few hours.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Can I roll back after my migrating resources are committed in Resource Manager? 

You can abort your migration as long as the resources are in the prepared state. Rollback is not supported after the resources have been successfully migrated through the commit operation.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Can I roll back my migration if the commit operation fails? 

You cannot abort migration if the commit operation fails. All migration operations, including the commit operation, are idempotent. So we recommend that you retry the operation after a short time. If you still face an error, create a support ticket or create a forum post with the ClassicIaaSMigration tag on our [VM forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Do I have to buy another express route circuit if I have to use IaaS under Resource Manager? 

No. We recently enabled [moving ExpressRoute circuits from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-move.md). You don’t have to buy a new ExpressRoute circuit if you already have one.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>What if I had configured Role-Based Access Control policies for my classic IaaS resources? 

During migration, the resources transform from classic to Resource Manager. So we recommend that you plan the RBAC policy updates that need to happen after migration.

## <a name="what-if-im-using-azure-site-recovery-or-azure-backup-today"></a>What if I’m using Azure Site Recovery or Azure Backup today? 

To migrate your Virtual Machine that are enabled for backup, see [I have backed up my classic VMs in backup vault. Now I want to migrate my VMs from classic mode to Resource Manager mode. How Can I backup them in recovery services vault?](../articles/backup/backup-azure-backup-ibiza-faq.md)i have backed up my classic VMs in backup vault. Now I want to migrate my VMs from classic mode to Resource Manager mode.  How Can I backup them in recovery services vault?

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Can I validate my subscription or resources to see if they're capable of migration? 

Yes. In the platform-supported migration option, the first step in preparing for migration is to validate that the resources are capable of migration. In case the validate operation fails, you receive messages for all the reasons the migration cannot be completed.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>What happens if I run into a quota error while preparing the IaaS resources for migration? 

We recommend that you abort your migration and then log a support request to increase the quotas in the region where you are migrating the VMs. After the quota request is approved, you can start executing the migration steps again.

## <a name="how-do-i-report-an-issue"></a>How do I report an issue? 

Post your issues and questions about migration to our [VM forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows), with the keyword ClassicIaaSMigration. We recommend posting all your questions on this forum. If you have a support contract, you're welcome to log a support ticket as well.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>What if I don't like the names of the resources that the platform chose during migration? 

All the resources that you explicitly provide names for in the classic deployment model are retained during migration. In some cases, new resources are created. For example: a network interface is created for every VM. We currently don't support the ability to control the names of these new resources created during migration. Log your votes for this feature on the [Azure feedback forum](http://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Can I migrate ExpressRoute circuits used across subscriptions with authorization links? 

ExpressRoute circuits which use cross-subscription authorization links cannot be migrated automatically without downtime. We have guidance on how these can be migrated using manual steps. See [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-migration-classic-resource-manager.md) for steps and more information.

## <a name="i-got-a-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated-"></a>I got a message *"VM is reporting the overall agent status as Not Ready. Hence, the VM cannot be migrated. Ensure that the VM Agent is reporting overall agent status as Ready"* or *"VM contains Extension whose Status is not being reported from the VM. Hence, this VM cannot be migrated." *

This message is received when the VM does not have outbound connectivity to the internet. The VM agent uses outbound connectivity to reach the Azure storage account for updating the agent status every five minutes.
