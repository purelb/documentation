---
title: "Egress Controller"
description: "Describe Operation"
weight: 24
hide: toc, nextpage
---


The Egress controller is an extension to the Service LoadBalancer that ensures traffic originated by a POD that is also accessed externally by an address allocated by the Service LoadBalancer have the same IP Address.

Without the Egress controller traffic originated inside the POD will be processed by the CNI and the source address changed to the address of the node hosting the POD.  Using the Egress controller a uniform address can be used for both traffic accessing the service and traffic originating from PODs selected by that Service.

{{% notice danger %}}
The Egress controller adds IPtables rules that SNAT traffic originated from the selected PODs as well as rules that avoid the CNI SNAT rules that change the POD's address to the Nodes address.  The IPtables rules consist of rules and IPSETs.  Additionally for locally administered addresses Routing rules are added.The rules consist of a combination of IPtables rules

CNI's and Kubeproxy both update IPtables rules.  In particular CNI's change network forwarding in some cases using IPtables, in others using less obvious eBPF programs.  Many CNI's assume that they are authoritive on packet forwarding and therefore will overide rules added by the egress controller.  

We have not tested the Egress controller functionality with every CNI, should your traffic fail to be SNATed to the Service LoadBalancer allocated address, the probable cause is the CNI used by the cluster.

{{% /notice %}}



PureLB operates in two modes, Local and Routed.  In Local mode the allocated address is added to an elected nodes primary network interface.  