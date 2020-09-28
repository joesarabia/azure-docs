---
title: Install VMware HCX
description: Set up the VMware HCX solution for your Azure VMware Solution private cloud
ms.topic: how-to
ms.date: 09/24/2020  
---

# Install VMware HCX for Azure VMware Solution

In this article, we walk through procedures for setting up the VMWare HCX solution for your Azure VMWare Solution private cloud. HCX enables migration of your VMware workloads to the cloud, and other connected sites through various built-in HCX supported migration types.

HCX Advanced, the default installation, supports up to three site connections (on-premises or cloud to cloud). If more than three site connections are needed, customers have the option to enable the HCX Enterprise add-on through Support, which is currently in preview. HCX Enterprise carries additional charges for customers after general availability (GA) but provides [additional features](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).

We will walk through all necessary procedures to:

> [!div class="checklist"]
> * Deploy the on-premises HCX OVA (Connector)
> * Activate and configure HCX
> * Configure the network uplink and service mesh
> * Complete setup by checking the appliance status

## Before you begin

Prior to beginning the VMware HCX deployment thoroughly review the following documentation;

> [!div class="checklist"]

> * [System Requirements for HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D64901F4-6AB4-4820-9303-27927648A34D.html)
> * [Software Version Requirements for HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)
> * [Network Port and Protocol Requirements for HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)

* Optionally Review the basic Azure VMware Solution Software Defined Datacenter (SDDC) [tutorial series](tutorial-network-checklist.md).
* Optionally Review and reference the [VMware HCX documentation](https://docs.vmware.com/en/VMware-HCX/index.html) including the HCX user guide.
* Optionally review VMware docs [Migrating Virtual Machines with VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Optionally review [VMware HCX Deployment Considerations](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Optionally review related VMware materials on HCX, such as the VMware vSphere [blog series](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) on HCX. 
* If needed request an Azure VMware Solution HCX Enterprise activation through Azure VMware Solution support channels.

Sizing workloads against compute and storage resources is an essential planning step when you're preparing to use the Azure VMware Solution Private Cloud HCX solution. Address the sizing step as part of the initial private cloud environment planning. 

You also can size workloads by completing an [Azure VMware Solution Assessment](../migrate/how-to-create-azure-vmware-solution-assessment.md) in the Azure Migrate portal.

## Prerequisites

* ExpressRoute Global Reach must be configured between on-premises and Azure VMware Solution SDDC ExpressRoute circuits.

* All required ports need to be open between on-premises and Azure VMware Solution SDDC (see [VMware HCX documentation](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

IP Addressing

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## Deploy the VMware HCX OVA on-premises

Before you deploy the virtual appliance to your on-premises vCenter, you'll need to download the VMware HCX OVA.

For an end-to-end overview of this step, view the [Azure VMware Solution: HCX Appliance Deployment](https://www.youtube.com/embed/BwSnQeefnso) video.

1. Sign into Azure VMware Solution private cloud **HCX Manager**.  You can find the IP address of HCX Manager by going to the **Manage section** of the Azure VMware Solution blade, then selecting **Connectivity** and then **HCX**.

    > [!NOTE]
    > The IP Address of HCX Manager in the Azure portal should be x.x.x.9, if you happen to see an IP address other than one which ends in .9, disregard the final octet and use .9 in it's place.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-ip-address.png" alt-text="Sign into Azure VMware Solution private cloud **HCX Manager**.  You can find the IP address of HCX Manager by going to the **Manage section** of the Azure VMware Solution blade, then selecting **Connectivity** and then **HCX**":::

1. Point your browser to the IP address of HCX Manager and login with the same credentials as vCenter.  Username: ***cloudadmin@vsphere.local*** Password: ***the vCenter password you defined during the private cloud setup**.

1. Under **Administration**, select **System Updates** and then select **Request download link** to download the VMware HCX OVA file.

   :::image type="content" source="media/hybrid-cloud-extension-installation/administration-request-download-link.png" alt-text="Under **Administration**, select **System Updates** and then select **Request download link** to download the VMware HCX OVA file.":::

1. Go to the **on-premises vCenter** and select an OVF template to deploy to your on-premises vCenter (the OVA you just downloaded).

1. Select a name and location, a resource/cluster where you are deploying HCX, and then review the details and required resources.

1. Review license terms, and if you agree, select the required storage and network, and then select **Next**.

1. In **Customize template**, enter all required information as you would when deploying any virtual machine.

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="In **Customize template**, enter all required information as you would when deploying any virtual machine.":::

1. Select **Next**, verify configuration, and then select **Finish** to deploy HCX OVA.

    > [!NOTE]
    > Generally, the VMware HCX Manager that you are deploying now is deployed onto the cluster's management network.

    > [!IMPORTANT]
    > After the deployment completes, you may need to power on the virtual appliance manually. Wait 10-15 minutes after powering on the HCX appliance to move to the next step.1. Select the download link for the VMware HCX OVA file.

## Activate VMware HCX

After deploying the VMware HCX OVA on-premises, you are ready to activate VMware HCX.

For an end-to-end overview of this step, view the [Azure VMware Solution: Activate HCX](https://www.youtube.com/embed/BkAV_TNYxdE) video.

1. To activate VMware HCX, you will first need to retrieve a license.  To do that go to the **Manage** section of the Azure VMware Solution blade, then select **Connectivity** and then **HCX**, press the **Add** button.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key-addition.png" alt-text="To activate VMware HCX, you will first need to retrieve a license.  To do that go to the **Manage** section of the Azure VMware Solution blade, then select **Connectivity** and then **HCX**, press the **Add** button.":::

1. Sign in to the on-premises VMware HCX Manager at https://HCXManagerIP:9443 and sign in with the default username (admin) and the password you defined.

   > [!IMPORTANT]
   > This is the appliance you just deployed in the previous section.  Also, make sure to include the `9443` port number with the HCX Manager IP address.

1. In **Licensing**, enter your **HCX Advanced Key**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="In Licensing, enter your HCX Advanced Key.":::

    > [!NOTE]
    > VMware HCX Manager must have open internet access or a proxy configured.

1. In **Datacenter Location**, supply the nearest location where you installed the VMware HCX Manager on-premises.

   :::image type="content" source="media/hybrid-cloud-extension-installation/datacenter-location.png" alt-text="In **Datacenter Location**, supply the nearest location where you installed the VMware HCX Manager on-premises.":::

1. Modify the System Name or accept the default.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-name.png" alt-text="Modify the System Name or accept the default.":::

1. Select the option **Yes, Continue** to continue.

   :::image type="content" source="media/hybrid-cloud-extension-installation/continue-vmware-hcx-configuration.png" alt-text="Select the option **Yes, Continue** to continue.":::

1. In Connect your vCenter, provide the **FQDN or IP address of your vCenter server** and the appropriate **credentials**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/connect-vcenter.png" alt-text="In Connect your vCenter, provide the **FQDN or IP address of your vCenter server** and the appropriate **credentials**.":::

1. In Configure SSO/PSC, provide the **FQDN or IP address of your PSC. Typically it's the same as your vCenter FQDN/IP**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-sso-psc.png" alt-text="In Configure SSO/PSC, provide the **FQDN or IP address of your PSC. Typically it's the same as your vCenter FQDN/IP**.":::

1. Verify that all the inputs are correct and select **Restart**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/verify-and-restart.png" alt-text="Verify that all the inputs are correct and select **Restart**.":::

    > [!NOTE]
    > You'll experience a few minute delay after selecting Restart.  After the services restart, it's critical that you see the vCenter server listed with a Green dot next to it as outlined below.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/activation-done.png" alt-text="activation done":::

## Add Site Pairing

Now you're ready to add a site pairing, what this will do is connect your on premesis VMware HCX Manager to the Azure VMware Solution HCX Manager.

For an end-to-end overview of this step, view the [Azure VMware Solution: HCX Site Pairing](https://www.youtube.com/embed/sKizDCRHOko) video.

1. Sign into on-premises vCenter, and under **Home**, select **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/vcenter-vmware-hcx.png" alt-text="Sign into on-premises vCenter, and under Home, select HCX.":::

1. Under **Infrastructure**, select **Site Pairing**, and then select the **Connect To Remote Site**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/connect-remote-site.png" alt-text="Under Infrastructure, select Site Pairing > Add a site pairing.":::

1. Enter the Remote HCX URL or IP address, Azure VMware Solution cloudadmin@vsphere.local username and password, and then select **Connect**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/connect-remote-site-credentials.png" alt-text="Enter the Remote HCX URL or IP address, Azure VMware Solution cloudadmin@vsphere.local username and password, and then select **Connect**":::

   You should now see a screen showing your HCX Manager in Azure VMware Solution and your HCX Manager on- premises connected (paired).

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing-complete.png" alt-text="You should now see a screen showing your HCX Manager in Azure VMware Solution and your HCX Manager on- premises connected (paired).":::

## Create Network Profiles

Network profiles are required to support the creation of the virtual appliances by VMware HCX.  When you create your network profiles, you define IP segments, which you identified during the [VMware HCX Network Segments](#prerequisites) pre-deployment preparation and planning stage.

For an end-to-end overview of this step, view the [Azure VMware Solution: HCX Site Pairing](https://www.youtube.com/embed/NhyEcLco4JY) video.

You will create four network profiles:

* Management
* vMotion
* Replication
* Uplink

1. Under **Infrastructure**, select Interconnect, Multi-Site Service Mesh, Network Profiles, Create Network Profile.

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-start.png" alt-text="Under **Infrastructure**, select Interconnect, Multi-Site Service Mesh, Network Profiles, Create Network Profile":::

1. For each network profile, select the port group, provide a name, and create the IP pool for that particular segment.  Again, these port groups should align directly w/ the networks which you have identified and/or created during the [VMware HCX Network Segments](#prerequisites) prerequisites section above.

These are the example configuration screens of each of the network profiles.

   :::image type="content" source="media/hybrid-cloud-extension-installation/example-configurations-network-profile.png" alt-text="These are the example configuration screens of each of the network profiles.":::

## Create Compute Profile

Now that your sites are paired you will need to create a compute profile.  

For an end-to-end overview of this step, view the [Azure VMware Solution: Compute Profile](https://www.youtube.com/embed/qASXi5xrFzM) video.

1. Under the **Infrastructure** group, select **Interconnect**, then **Compute Profiles** and **Create Compute Profile**.

:::image type="content" source="media/hybrid-cloud-extension-installation/compute-profile-create.png" alt-text="Under the **Infrastructure** group, select **Interconnect**, then **Compute Profiles** and **Create Compute Profile**":::

1. Enter a **name** for the Compute Profile, select **Continue**.

:::image type="content" source="media/hybrid-cloud-extension-installation/name-compute-profile.png" alt-text="Enter a **name** for the Compute Profile, select **Continue**.":::

1. Select the services to enable, select Continue.

    > [!NOTE]
    > Generally nothing will be changed here.

:::image type="content" source="media/hybrid-cloud-extension-installation/vmware-hcx-services.png" alt-text="Generally nothing will be changed here.":::

1. In **Select Service Resources**, select one or more service resources (clusters) to enable for the selected VMware HCX services.  After you select the cluster(s) in your on-premises datacenter select **Continue**

:::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resource.png" alt-text="In **Select Service Resources**, select one or more service resources (clusters) to enable for the selected VMware HCX services.  After you select the cluster(s) in your on-premises datacenter select **Continue**":::

1. From **Select Datastore**, select the datastore storage resource for deploying the VMware HCX Interconnect appliances, and then select **Continue**.

When multiple resources are selected, VMware HCX uses the first resource selected until its capacity is exhausted.

:::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources-and-reservations.png" alt-text="From **Select Datastore**, select the datastore storage resource for deploying the VMware HCX Interconnect appliances, and then select **Continue**":::

1. Select **Management Network Profile**, the management network profile you created in previous steps, and then select **Continue**.

:::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Select **Management Network Profile**, the management network profile you created in previous steps, and then select **Continue**":::

1. Select **Uplink Network Profile**, the uplink network profile you created in previous steps, and then select **Continue**.

:::image type="content" source="media/hybrid-cloud-extension-installation/select-uplink-network-profile.png" alt-text="Select **Uplink Network Profile**, the uplink network profile you created in previous steps, and then select **Continue**":::

1. Select **vMotion Network Profile**, the vMotion network profile you created in prior steps, and then select **Continue**.

:::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Select vMotion Network Profile, the vMotion network profile you created in prior steps, and then select Continue.":::

1. Choose **vSphere Replication Network Profile**, the replication network profile you created in prior steps, and then choose **Continue**.

:::image type="content" source="media/hybrid-cloud-extension-installation/select-replication-network-profile.png" alt-text="Choose vSphere Replication Network Profile, the replication network profile you created in prior steps, and then choose Continue.":::

1. Select **Distributed Switches** for **Network Extensions**.  The distributed virtual switches you should be selecting are the ones that contain the virtual machines to be migrated to Azure VMware Solution on a layer-2 extended network, and then select **Continue**.

    > [!NOTE]
    > Even if you do not plan on extending any layer-2 networks, you still must select a Distributed Virtual Switch.

:::image type="content" source="media/hybrid-cloud-extension-installation/select-layer-2-distributed-virtual-switch.png" alt-text="Select Distributed Switches for Network Extensions, the distributed virtual switches that contain the virtual machines to be migrated to Azure VMware Solution on a layer-2 extended network, and then select Continue.":::

1. **Review** the connection rules and select **Continue**.

:::image type="content" source="media/hybrid-cloud-extension-installation/review-connection-rules.png" alt-text="**Review** the connection rules and select **Continue**.":::

1. Select **Finish** to create the compute profile.

:::image type="content" source="media/hybrid-cloud-extension-installation/ready-to-complete.png" alt-text="Select **Finish** to create the compute profile..":::

You see a screen similar to the one shown below.

:::image type="content" source="media/hybrid-cloud-extension-installation/compute-profile-done.png" alt-text="You see a screen similar to the one shown below.":::



