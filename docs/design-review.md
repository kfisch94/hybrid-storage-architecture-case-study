# Storage Design Review

## Source and scope

This case study is based on my `KyleFischer Storage Solution Proposal Template v2.docx` and the retained final PDF in the CMPH308 coursework folder. The proposal cover is dated August 1, 2023; the Word file was last modified on August 2, 2023. Its older creation timestamp reflects the reused template and is not used as the project date.

The assignment defined an independent proposal for a fictional business with 200 employees, 200 TB across five servers, and 20% annual growth. Course instructions were used only to understand scope, not as evidence that any system was implemented. The original document includes fictional consultancy branding and template correspondence; this portfolio does not present those as employment or client experience.

The retained Word and PDF texts were reviewed, and all three embedded Word images were inspected. Two branding images were omitted. The original topology is the only retained image. The original documents were not published because the portfolio summary is clearer and avoids exposing contact fields.

## Capacity

The planning formula is `200 × 1.2^year`, producing 200, 240, 288, and 345.6 TB for years 0–3. The accompanying [CSV](capacity-model.csv) uses decimal TB consistently. It is a dataset forecast, not a benchmark or a measurement of usable storage.

The original drive inventory lists 16 drives of 22 TB, giving 352 TB raw. The 6.4 TB difference between raw capacity and the year-three dataset is not sufficient evidence of a usable-capacity design. Protection overhead, filesystem usage, snapshots, spare capacity, and operating headroom are not specified. The original statement that this capacity supports 16 years of growth is not supported by its own annual-growth assumption.

The proposal also assumes that only 30% of catalogued data is regularly accessed within 90 days. That could inform tiering analysis, but it does not remove the need to account for all data, cloud recall, backup copies, and recovery requirements.

Before procurement, validate the exact PowerScale platform, supported node and drive configuration, minimum cluster requirements, OneFS release, licenses, and protected usable capacity. The third-party drive list and three-unit description are retained as historical proposal choices, not certified vendor configurations. No deduplication or compression saving is assumed here.

## Cloud tiering and backup

The original narrative combines CloudPools, NDMP, Azure Storage, and Azure Backup without defining a supported end-to-end backup workflow.

Dell documents CloudPools as tiering infrequently accessed data to cloud storage. It separately describes CloudPools-aware NDMP backup modes, including modes that preserve file data and modes that preserve SmartLink metadata. The proposal needs to identify the backup application, supported destination, copy contents, retention, restore dependencies, and version compatibility. Cloud tiering by itself does not demonstrate an independent recoverable backup. See [Dell's CloudPools and NDMP documentation](https://infohub.delltechnologies.com/en-uk/l/dell-powerscale-ndmp-technical-overview-and-design-considerations/cloudpools-23/).

The archive tier named in the original proposal also needs compatibility validation for the intended tiering workflow. This review does not certify CloudPools integration with that specific Azure tier or direct NAS backup through Azure Backup.

## Recovery objectives

The proposed RPO is 24 hours and the proposed RTO is 4 hours. There is no retained backup schedule, recovery test, or throughput measurement demonstrating either target.

Microsoft documents Azure Blob archive data as offline until rehydrated. Standard-priority rehydration can take up to 15 hours for objects under 10 GB, while high priority is faster but is not an unconditional completion guarantee. A four-hour recovery objective therefore cannot be inferred from an archive-storage choice. See [Microsoft's archive rehydration documentation](https://learn.microsoft.com/en-us/azure/storage/blobs/archive-rehydrate-overview).

A revised design should define which services and dataset must be restored within four hours, keep their recovery data in an appropriate accessible tier, and test the complete sequence: retrieving data, transferring it, restoring permissions and services, and validating usability. Bulk recovery of the full dataset would require its own bandwidth and duration model. The sources linked here were consulted during the portfolio review; they are not claimed to represent every product capability as it existed in 2023.

## Networking and availability

The proposal intends redundant network connectivity, but the image and narrative do not fully agree on switch quantities, roles, or paths. Some passages also introduce equipment not listed in the budget. Multiple switches alone do not prove that a client path survives a switch, link, node, or site failure.

A revised topology should explicitly separate client access, cluster communication, management, and cloud connectivity, with supported port layouts and failure domains. It should also document uplinks, routing, power dependencies, and expected behavior during each failure test. No such tests are evidenced in this case study.

## Historical cost reconciliation

The [cost CSV](cost-reconciliation.csv) recalculates the proposal's recorded figures without refreshing vendor prices.

| Category | Original summary CAD | Recorded line-item sum CAD |
| --- | ---: | ---: |
| Hardware | 95,721.64 | 95,721.64 |
| Software and cloud | 678,287.04 | 659,313.06 |
| Installation and support | 39,000.00 | 39,000.00 |
| Training | 5,500.00 | 5,500.00 |
| Total | 818,508.68 | 799,534.70 |

The software discrepancy is CAD 18,973.98. Separately, three units at the stated CAD 12,638.07 unit price equal CAD 37,914.21, while the recorded extended amount is CAD 37,914.20; the reconciliation above preserves the recorded extended amount. The proposal's total-fee field remains a placeholder.

These calculations do not establish procurement-ready costs. The cloud lines lack enough detail to reproduce service selection, region, redundancy, billing period, transfer and retrieval costs, and workload assumptions. Taxes, ongoing operating costs, and a validated hardware bill of materials would also need explicit treatment. No present-day pricing or savings claim is made.

## Proposed next steps

- Validate vendor-supported hardware and protected usable capacity against the three-year dataset and operating headroom.
- Reconcile the equipment list, switch roles, and topology into one design.
- Separate cloud tiering, backups, and recovery copies; identify supported products and restore dependencies.
- Define the critical recovery scope and test it against the proposed RPO and RTO.
- Rebuild the quote with consistent quantities, licensing, time periods, and operating costs.

These are retrospective recommendations. They have not been implemented or tested as part of this portfolio publication.
