# IBM Cloud for SAP
Documentation repository for everything in the IBM Cloud for SAP portfolio.

* OM - Bradley Knapp
* SME - Sean Freeman, Rainer Vetter, Ingo Dahm (Contractor)
* PM - Sam Jellad


----

## Documentation theory and structure....

- **Get Started**
  - Overview
  - Executive solution brief
  - Introductions to:
    - Infrastructure environments `(what it is and how it works)`
    - Infrastructure for SAP `(what IaaS options)`
    - Infrastructure Profiles for SAP `(different profiles for each IaaS options)`
  - Design considerations of infrastructure `(considerations for different infrastructure components, on each Infrastructure environment when running SAP applications)`:
    - Networking
    - Storage
    - Compute and OS
  - Design considerations of applications `(considerations for X application on Y infrastructure)`:
    - SAP HANA
    - SAP NetWeaver
    - SAP Business Applications (e.g. S/4H, BW/4H, Commerce, B1 etc.)
    - SAP Technical Applications (e.g. Content Server etc.)
    - SAP AnyDB (e.g. IBM Db2, MS SQL etc.)
  - SAP Partner solutions
    - Veeam
  - Innovations
    - ABAP SDK
- **Tutorials**
  - Quick Study Deployments
    - SAP NWAS on Intel Bare Metal (Classic), using RHEL
    - SAP NWAS on Intel Bare Metal (Classic), using WinS
    - SAP NWAS on Intel Virtual Server (VPC), using RHEL
  - SAP Partner solutions
    - Veeam
- **How to**
  - Navigating IBM Cloud console
  - Pre-requisites
    - Credentials
    - IBM Cloud account structure
  - Provisioning....
    - Intel Bare Metal, for SAP HANA and SAP NetWeaver
    - Intel Virtual Server (Gen2), for SAP HANA and SAP NetWeaver
    - IBM Power Virtual Server, for SAP HANA and SAP NetWeaver
    - VMware SDDC, for SAP HANA and SAP NetWeaver
    - Red Hat OpenShift for SAP Data Hub
- **Reference**
  - Reference Architectures
    - SAP HANA scale-up
    - SAP HANA scale-out
- **Help**
  - IBM Cloud Support and SAP ONE Support
  - FAQs
    - Main FAQ
    - Move SAP workloads FAQ
    - Profile List with Benchmarks and Specs FAQ
  - Fast Path Site Map
  - Documentation Changelog

----

## Phase 2 - New content

Start of Phase 2:
- 348 A4 pages (no empty lines)
- 112k words

Date 2020-11-23:
- 386 A4 pages (no empty lines)
- 123k words


**Status legend:**
- :white_check_mark: = Ready
- :warning: = In Progress / Fixes not completed
- :sos: = Huge effort to fix
- :x: = Deprecated banner
- :large_blue_circle: = Unwritten new content

**Status tracking:**
- 2020-11-23: 51 files to update - 7 :white_check_mark:, 5 :warning:, 4 :x:, 35 :large_blue_circle:

| Filename | Status | Reviewer 1 | Reviewer 2 | Notes |
| --- | --- | --- | --- | --- |
| sap-getting-started.md | | | | |
| sap-offerings-overview.md | | | | |
| sap-environment-classic-infrastructure.md | | | | |
| sap-environment-vpc-infrastructure.md | | | | |
| sap-environment-power-infrastructure.md | | | | |
| sap-offerings-certified-iaas.md | | | | |
| sap-offerings-hana-iaas-intel-bare-metal-profiles.md | | | | |
| sap-offerings-hana-iaas-intel-optane-profiles.md | :large_blue_circle: | | | |
| sap-offerings-hana-iaas-intel-virtual-server-gen2-profiles.md | | | | |
| sap-offerings-hana-iaas-power-virtual-server-profiles.md | | | | |
| sap-offerings-hana-iaas-vmware-sddc-profiles.md | | | | |
| sap-offerings-netweaver-iaas-intel-bare-metal-profiles.md | | | | |
| sap-offerings-netweaver-iaas-intel-virtual-server-gen2-profiles.md | | | | |
| sap-offerings-netweaver-iaas-power-virtual-server-profiles.md | | | | |
| sap-offerings-netweaver-iaas-vmware-sddc-profiles.md | | | | |
| sap-sizing-planning-process.md | | | | |
| sap-sizing-planning-your-system-landscape.md | | | | |
| sap-prereq-determining-access.md | | | | |
| sap-prereq-software.md | | | | |
| sap-prereq-byol.md | | | | |
| sap-design-considerations-networking.md | | | | |
| sap-design-considerations-storage.md | | | | |
| sap-design-considerations-compute-os.md | | | | |
| sap-design-considerations-sap-hana.md | | | | |
| sap-design-considerations-sap-netweaver.md | | | | |
| sap-app-s4hana.md | | | | |
| sap-app-bw4hana.md | | | | |
| sap-app-commerce.md | | | | |
| sap-app-ecc.md | :large_blue_circle: | | | |
| sap-app-bw.md | :large_blue_circle: | | | |
| sap-app-b1.md | | | | |
| sap-app-bo-bi.md | :large_blue_circle: | | | |
| sap-app-data-hub-2x.md | | | | |
| sap-app-data-intelligence-3x.md | :warning: | | | |
| sap-app-router.md | :large_blue_circle: | | | |
| sap-app-web-dispatcher.md | :large_blue_circle: | | | |
| sap-app-fiori-fes.md | :large_blue_circle: | | | |
| sap-app-gateway.md | :large_blue_circle: | | | |
| sap-app-solution-mgr.md | :large_blue_circle: | | | |
| sap-app-content-server.md | | | | |
| sap-app-adobe-doc.md | :large_blue_circle: | | | |
| sap-app-po-pi.md | :large_blue_circle: | | | |
| sap-app-lama.md | :large_blue_circle: | | | |
| sap-app-secure-logon-server.md | :large_blue_circle: | | | |
| sap-anydb-ibm-db2.md | | | | |
| sap-anydb-ms-sql.md | :large_blue_circle: | | | |
| sap-anydb-oracle-db.md | :warning: | | | |
| sap-anydb-sap-maxdb.md | | | | |
| sap-anydb-sap-ase.md | | | | |
| sap-anydb-sap-iq.md | | | | |
| sap-app-scp.md | :large_blue_circle: | | | |
| sap-app-scp-single-tenant.md | :large_blue_circle: | | | |
| sap-os-rhel.md | :large_blue_circle: | | | |
| sap-os-sles.md | :large_blue_circle: | | | |
| sap-os-ibm-aix.md | :large_blue_circle: | | | |
| sap-os-windows-server.md | :large_blue_circle: | | | |
| sap-partners-veeam.md | :x: | | | |
| sap-partners-actifio.md | :warning: | | | |
| sap-partners-f5.md | :large_blue_circle: | | | |
| sap-partners-hytrust.md | :large_blue_circle: | | | |
| sap-partners-ibm-security-services.md | | | | |
| sap-ibm-cloud-paks-mcm.md | :large_blue_circle: | | | |
| sap-ibm-cloud-paks-integration.md | :large_blue_circle: | | | |
| sap-ibm-abap-sdk-watson.md | | | | |
| sap-ibm-the-weather-company.md | :large_blue_circle: | | | |
| sap-ibm-cloud-migration-program.md | | | | |
| sap-billing-considerations-storage.md | | | | |
| sap-billing-considerations-networking.md | | | | |
| sap-quickstudy-nw-bm-rhel.md | | | | |
| sap-quickstudy-nw-bm-wins.md | | | | |
| sap-quickstudy-nw-vs-gen2-rhel.md | | | | |
| sap-quickstudy-nw-vs-gen2-wins.md | :large_blue_circle: | | | |
| sap-quickstudy-nw-power-vs-aix.md | :warning: | | | |
| sap-quickstudy-hana-power-vs-sles.md | :warning: | | | |
| sap-tutorial-veeam.md | :x: | | | |
| sap-prereq-necessary-credentials.md | | | | |
| sap-prereq-account-structure.md | | | | |
| sap-provision-intel-bare-metal-plan-deploy.md | | | | |
| sap-provision-intel-bare-metal-deploy-infrastructure.md | | | | |
| sap-provision-intel-bare-metal-optane-plan-deploy.md | | | | |
| sap-provision-intel-bare-metal-optane-deploy-infrastructure.md | | | | |
| sap-provision-intel-virtual-server-gen2-plan-deploy.md | | | | |
| sap-provision-intel-virtual-server-gen2-deploy-infrastructure.md | | | | |
| sap-ibm-metrics-collector-linux.md | | | | |
| sap-ibm-metrics-collector-windows.md | :white_check_mark: | | | |
| sap-provision-power-virtual-server-plan-deploy.md | | | | |
| sap-provision-power-virtual-server-deploy-infrastructure.md | | | | |
| sap-provision-power-virtual-server-prepare-aix-nw.md | :white_check_mark: | | | |
| sap-provision-power-virtual-server-prepare-sles-hana.md | :white_check_mark: | | | |
| sap-provision-vmware-sddc-plan-deploy.md | | | | |
| sap-provision-vmware-sddc-deploy-infrastructure.md | | | | |
| sap-provision-vmware-sddc-moving-workloads.md | :large_blue_circle: | | | |
| sap-datahub-provision-redhat-openshift-plan-deploy.md | | | | |
| sap-datahub-provision-redhat-openshift-deploy-clusters.md | | | | |
| sap-datahub-provision-redhat-openshift-install-datahub.md | | | | |
| sap-case-studies.md | :large_blue_circle: | | | |
| sap-refarch-hana-scaleup.md | :x: | | | |
| sap-refarch-hana-scaleout.md | :x: | | | |
| sap-refarch-nw-scaleup.md | :large_blue_circle: | | | |
| sap-refarch-nw-scaleout.md | :large_blue_circle: | | | |
| sap-deploypattern-bm-vs-gen2.md | :large_blue_circle: | | | |
| sap-deploypattern-bm-vmware-sddc.md | :large_blue_circle: | | | |
| sap-deploypattern-power-vs-vs-gen2.md | :large_blue_circle: | | | |
| sap-solarch-examples.md | :large_blue_circle: | | | |
| sap-roadmap.md | :large_blue_circle: | | | |
| sap-getting-help.md | | | | |
| sap-faq.md | | | | |
| sap-faq-moving-sap-workloads.md | | | | |
| sap-faq-profile-specs.md | | | | |
| sap-fast-path-site-map-intel-bm.md | :white_check_mark: | | | |
| sap-fast-path-site-map-intel-vs-gen2.md | :white_check_mark: | | | |
| sap-fast-path-site-map-ibm-power-vs.md | :white_check_mark: | | | |
| sap-fast-path-site-map-vmware-sddc.md | :white_check_mark: | | | |


----

## Phase 1 - Overhaul content

Start:
- 347 A4 pages (no empty lines)
- 104k words

Re-factored to:
- 254 A4 pages (no empty lines)
- 72k words

Re-written to:
- 348 A4 pages (no empty lines)
- 112k words

**_Summary_** = 40k~ new words written in Phase 1


### Phase 1 - status tracking

:white_check_mark: = Ready
:warning: = Fixes not completed
:sos: = Huge effort to fix
:x: = Launching with deprecated banner

- 2020-08-24: 71 files for launch - 24 :white_check_mark:, 41 :warning:, 6 :sos:
- 2020-08-27: 66 files for launch - 25 :white_check_mark:, 35 :warning:, 6 :sos: `descoping of Intel Optane complete`
- 2020-09-06: 64 files for launch - 29 :white_check_mark:, 31 :warning:, 5 :sos:
- 2020-09-14: 66 files for launch - 37 :white_check_mark:, 22 :warning:, 7 :sos: `add 2 files to scope, S/4H and BW/4H considerations`
- 2020-09-16: 66 files for launch - 42 :white_check_mark:, 18 :warning:, 6 :sos:
- 2020-09-17: 64 files for launch - 44 :white_check_mark:, 13 :warning:, 7 :sos: `remove accidental count of LINK pages`
- 2020-09-17: 59 files for launch - 44 :white_check_mark:, 10 :warning:, 5 :sos: `removed support and connectivity duplicate files`
- 2020-09-19: 60 files for launch - 46 :white_check_mark:, 9 :warning:, 5 :sos: `add AnyDB IQ, mark FAQ table as incomplete`
- 2020-09-20: 61 files for launch - 48 :white_check_mark:, 9 :warning:, 4 :sos: `reached 301 A4 pages (no empty lines), 94k words`
- 2020-09-26: 61 files for launch - 54 :white_check_mark:, 2 :warning:, 2 :sos:, 3 :x:
- 2020-09-28: 61 files for launch - 55 :white_check_mark:, 2 :warning:, 1 :sos:, 3 :x:
- 2020-09-30: 61 files for launch - 58 :white_check_mark:, 3 :x:


| File | Launch scope | Rainer | Brian/Karen | Notes |
| --- | --- | --- | --- | --- |
| sap-getting-started.md | :white_check_mark: | :white_check_mark: |:white_check_mark:  | |
| sap-offerings-overview.md | :white_check_mark: | | :white_check_mark: | Power content may remain here? |
| LINK_PDF: Solution Brief | :white_check_mark: | | | Changed to "smart whitepaper" |
| sap-environment-classic-infrastructure.md | :white_check_mark: | | :white_check_mark:| |
| sap-environment-vpc-infrastructure.md | :white_check_mark: | | :white_check_mark: | |
| sap-environment-power-infrastructure.md | :white_check_mark: | |:white_check_mark: | |
| sap-offerings-certified-iaas.md | :white_check_mark: | |:white_check_mark:  | |
| sap-offerings-hana-iaas-intel-bare-metal-profiles.md | :white_check_mark: | | :white_check_mark: | |
| sap-offerings-hana-iaas-intel-optane-profiles.md | | | | Missing profiles |
| sap-offerings-hana-iaas-intel-virtual-server-gen2-profiles.md | :white_check_mark: | |:white_check_mark:  | |
| sap-offerings-hana-iaas-power-virtual-server-profiles.md | :white_check_mark: | | :white_check_mark:| Missing storage specifications, fix after launch |
| sap-offerings-hana-iaas-vmware-sddc-profiles.md | :white_check_mark: | | :white_check_mark:| Missing storage specifications, fix after launch |
| sap-offerings-netweaver-iaas-intel-bare-metal-profiles.md | :white_check_mark: | |:white_check_mark:| Missing storage specifications, fix after launch |
| sap-offerings-netweaver-iaas-intel-virtual-server-gen2-profiles.md | :white_check_mark: | | :white_check_mark:| |
| sap-offerings-netweaver-iaas-power-virtual-server-profiles.md | :white_check_mark: | | :white_check_mark:| Missing storage specifications, fix after launch |
| sap-offerings-netweaver-iaas-vmware-sddc-profiles.md | :white_check_mark: | | :white_check_mark:| Missing storage specifications, fix after launch |
| sap-sizing-planning-process.md | :white_check_mark: | |:white_check_mark: | |
| sap-sizing-planning-your-system-landscape.md | :white_check_mark: | |:white_check_mark: | |
| sap-prereq-determining-access.md | :white_check_mark: | |:white_check_mark: | |
| sap-prereq-software.md | :white_check_mark: | | :white_check_mark:| |
| sap-prereq-byol.md | :white_check_mark: | | :white_check_mark:| |
| sap-design-considerations-networking.md | :white_check_mark: | :white_check_mark: | :white_check_mark:| |
| sap-design-considerations-storage.md | :white_check_mark: | :white_check_mark: | :white_check_mark: | |
| sap-design-considerations-compute-os.md | :white_check_mark: | :white_check_mark: |:white_check_mark: | |
| sap-design-considerations-sap-hana.md | :white_check_mark: | |:white_check_mark: | |
| sap-design-considerations-sap-netweaver.md | :white_check_mark: | |:white_check_mark: | |
| sap-app-s4hana.md | :white_check_mark: | | :white_check_mark:| |
| sap-app-bw4hana.md | :white_check_mark: | :white_check_mark: |MN pending | |
| sap-app-commerce.md | :white_check_mark: | |:white_check_mark: | Additional content required after launch, removed placeholder structure |
| sap-app-ecc.md | | | | |
| sap-app-bw.md | | | | |
| sap-app-b1.md | :white_check_mark: | | :white_check_mark:| Additional content required after launch, removed placeholder structure, KT Acrolinx only |
| sap-app-bo-bi.md | | | | |
| sap-app-data-hub-2x.md | :white_check_mark: | | :white_check_mark: | Additional content required after launch, removed placeholder structure, KT Acrolinx only |
| sap-app-data-intelligence-3x.md | | | | |
| sap-app-router.md | | | | |
| sap-app-web-dispatcher.md | | | | |
| sap-app-fiori-fes.md | | | | |
| sap-app-gateway.md | | | | |
| sap-app-solution-mgr.md | | | | |
| sap-app-content-server.md | :white_check_mark: | | :white_check_mark:| Additional content required after launch, removed placeholder structure, KT Acrolinx only  |
| sap-app-adobe-doc.md | | | | |
| sap-app-po-pi.md | | | | |
| sap-app-lama.md | | | | |
| sap-app-secure-logon-server.md | | | | |
| sap-anydb-ibm-db2.md | :white_check_mark: | | :white_check_mark:| |
| sap-anydb-ms-sql.md | | | | |
| sap-anydb-oracle-db.md | | | | |
| sap-anydb-sap-maxdb.md | :white_check_mark: | |:white_check_mark: | |
| sap-anydb-sap-ase.md | :white_check_mark: | |:white_check_mark: | |
| sap-anydb-sap-iq.md | :white_check_mark: | | :white_check_mark:| |
| sap-app-scp.md | | | | |
| sap-app-scp-single-tenant.md | | | | |
| sap-os-rhel.md | | | | |
| sap-os-sles.md | | | | |
| sap-os-ibm-aix.md | | | | |
| sap-os-windows-server.md | | | | |
| sap-partners-veeam.md | :x: | | | Launch without update to Veeam 10.x, will need to match structure with Actifio |
| sap-partners-actifio.md | | | | Partial re-write, need to match structure with Veeam |
| sap-partners-f5.md | | | | |
| sap-partners-hytrust.md | | | | Still needed? |
| sap-partners-ibm-security-services.md | :white_check_mark: | |:white_check_mark: | |
| sap-abap-sdk-watson.md | :white_check_mark: | | :white_check_mark: | KT Acrolinx only|
| sap-the-weather-company.md | | | | |
| sap-cloud-migration-program.md | :white_check_mark: | | :white_check_mark:| KT Acrolinx only|
| sap-billing-considerations-storage.md | | | | |
| sap-billing-considerations-networking.md | | | | |
| LINK_MD: Navigating | :white_check_mark: | | | |
| sap-prereq-necessary-credentials.md | :white_check_mark: | |:white_check_mark: | |
| sap-prereq-account-structure.md | :white_check_mark: | |:white_check_mark: | |
| sap-provision-intel-bare-metal-plan-deploy.md | :white_check_mark: | | :white_check_mark:| |
| sap-provision-intel-bare-metal-deploy-infrastructure.md | :white_check_mark: | | :white_check_mark:| |
| sap-provision-intel-bare-metal-optane-plan-deploy.md | | | | |
| sap-provision-intel-bare-metal-optane-deploy-infrastructure.md | | | | |
| sap-provision-intel-virtual-server-gen2-plan-deploy.md | :white_check_mark: | | :white_check_mark:| |
| sap-provision-intel-virtual-server-gen2-deploy-infrastructure.md | :white_check_mark: | | :white_check_mark:| |
| sap-ibm-metrics-collector.md | :white_check_mark: | |:white_check_mark: | |
| sap-provision-power-virtual-server-plan-deploy.md | :white_check_mark: | | :white_check_mark: | |
| sap-provision-power-virtual-server-deploy-infrastructure.md | :white_check_mark: | |:white_check_mark:  | |
| sap-provision-vmware-sddc-plan-deploy.md | :white_check_mark: | :white_check_mark: | :white_check_mark:| |
| sap-provision-vmware-sddc-deploy-infrastructure.md | :white_check_mark: | :white_check_mark: | :white_check_mark:| |
| sap-provision-vmware-sddc-moving-workloads.md | | | | |
| sap-datahub-provision-redhat-openshift-plan-deploy.md | :white_check_mark: | :white_check_mark: |:white_check_mark:  | |
| sap-datahub-provision-redhat-openshift-deploy-infrastructure.md | :white_check_mark: | :white_check_mark: |:white_check_mark:  | |
| sap-datahub-provision-redhat-openshift-install-datahub.md | :white_check_mark: | :white_check_mark: | :white_check_mark: | Links to Creating Buckets to store data didn't work - topic not there -- Cloud Object Storage repo - couldn't find any topic in that repo about creating buckets|
| sap-quickstudy-bm-nw-rhel.md | :white_check_mark: | | :white_check_mark:| Very old copied content. Previously called Quick Reference Guide |
| sap-quickstudy-bm-nw-wins.md | :white_check_mark: | |:white_check_mark: | Very old copied content. Previously called Quick Reference Guide |
| sap-quickstudy-vs-gen2-nw-rhel.md | :white_check_mark: | |:white_check_mark: | Copied content. Previously called sample deployment |
| sap-case-studies.md | | | | |
| sap-refarch-hana-scaleup.md | :x: | | | HUGE EFFORT, add deprecated label at top saying new version due? |
| sap-refarch-hana-scaleout.md | :x: | | | HUGE EFFORT, add deprecated label at top saying new version due? |
| sap-refarch-nw-scaleup.md | | | | |
| sap-refarch-nw-scaleout.md | | | | |
| sap-deploypattern-bm-vs-gen2.md | | | | |
| sap-deploypattern-bm-vmware-sddc.md | | | | |
| sap-deploypattern-power-vs-vs-gen2.md | | | | |
| sap-solarch-examples.md | | | | |
| sap-roadmap.md | | | | |
| sap-getting-help.md | :white_check_mark: | :white_check_mark: | :white_check_mark:| |
| sap-faq.md | :white_check_mark: | :white_check_mark: | :white_check_mark: | |
| sap-faq-moving-sap-workloads.md | :white_check_mark: | :white_check_mark: | :white_check_mark:| Add VMware HCX approach in the future |
| sap-faq-profile-specs.md | :white_check_mark: | :white_check_mark: |:white_check_mark:  | Why can table not be displayed small, with "Full-screen" expansion button? Ask UX team. Add a new FAQ list, which has the Profile name and the available OS versions of RHEL, SLES and VMware |
| - | - | - | - | - |
| sap-power-jump-server.md | | | | |
| sap-power-adding-routes.md | | | | |
| sap-power-ntp-time-server-configuration.md | | | | |
| sap-power-nim-service-handler.md | | | | |
| sap-power-ssh-tunneling.md | | | | |
| sap-power-adding-storage.md | | | | |
| sap-power-extending-tmp.md | | | | |
| sap-power-paging-space.md | | | | |
| sap-power-aix-toolbox-for-linux-extensions.md | | | | |
| sap-power-filesets-for-oracle.md | | | | |
| sap-power-filesets-for-db2.md | | | | |
| sap-power-filesets-for-maxdb.md | | | | |
| sap-power-storage-aix-lvm.md | | | | |
| sap-power-installation_overview.md | | | | |
| sap-power-installation_client_machine.md | | | | |
| sap-power-installation_jump_server.md | | | | |
| sap-power-information-resources.md | | | | |


----

## Phase 0 - Merge existing content and de-duplicate

**Existing documentations merged:**
- https://github.ibm.com/cloud-docs/sap-b1 :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-data-hub :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-hana :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-hana-power :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-iaas-offering :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-netweaver :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-netweaver-ms-qrg :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-netweaver-power :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-netweaver-rhel-qrg :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-reference-architecture :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-vpc :white_check_mark: 
- https://github.ibm.com/cloud-docs/sap-vpc-hana :white_check_mark: 

Start:
- 347 A4 pages (no empty lines)
- 104k words

Re-factored to:
- 254 A4 pages (no empty lines)
- 72k words


----

## Feature requests to IBM Cloud Docs team
- Support for same Markdown file, to be used in multiple places without duplicating the current item highlighted in the left-side menu bar
- Why can table not be displayed small, with "Full-screen" expansion button? Ask UX team
- Save individual page as PDF, not just the entire repo as PDF
- Why was the Nav Group "learn" re-titled Get Started?
- Why must the Nav Group "tutorial" be before the How-To section, why can it not be placed where desired in the toc?


## Documentation capitalization consistency

- IBM Cloud
- Cloud `?`
- Virtual Server `? more product name than technology description`
- Bare Metal
- Infrastructure-as-a-Service
- host
- machine `? use host instead`

- IBM and SAP [do not use IBM-SAP co-branding?]
- SAP Workloads
- SAP-certified Infrastructure
- SAP AnyDB
- IBM Db2
- SAP HANA
- Appliance delivery model for SAP HANA
- TDI delivery model for SAP HANA

- SAP Landscape
  - SAP Systems
    - SAP Instances


## Workarounds on writing for IBM Cloud Docs

- Keywords metadata cannot start with placeholder {{...}}, it must start with alphabetic characters otherwise metadata header will be shown on the pages instead
- Headings must go in Level sequence (L2, L3, L4 i.e. ##, ###, ####). If under an L2 Header (##) there is an L4 Header (####) then the "On this page" table of contents breaks
- `code backticks` will break caption elements if used inside the text, e.g. {: caption="content `here`"}


## Quick estimation of time delivering the content

Quick output of effort spent (no automatic timestamp recognition for positive timezone i.e. +0100):
```
cat <<< '"author_date","author_name","committer_name","commit_message","ref_name"' > timelog.csv
git log --pretty=format:'"%ad","%an","%cn","[%s]","%D"' --date=iso >> timelog.csv
```

## Quick estimation of content word count

Quick merge of all .md files and remove all blank lines:
```
cat *.md > all.md
sed -i .bak '/^$/d' all.md
```

## Prior contacts on content

- refarch-sap-bw-hana-scale-out.png - this image is owned by Marketing. Contact Jacquelyn R Lehmann/Houston/IBM with any questions or changes
