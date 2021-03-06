
## GAP API for bletiny

<br>

Generic Access Profile (GAP) defines the generic procedures related to discovery of Bluetooth devices (idle mode procedures) and link management aspects of connecting to Bluetooth devices (connecting mode procedures). It also defines procedures related to use of different security levels. 

Several different modes and procedures may be performed simultaneously over an LE physical transport. The following modes and procedures are defined for use over an LE physical transport:

1. **Broadcast mode and observation procedure**
    - These allow two devices to communicate in a unidirectional connectionless manner using the advertising events.
2. **Discovery modes and procedures**
    - All devices shall be in either non-discoverable mode or one of the discoverable modes.
    - A device in the discoverable mode shall be in either the general discoverable mode or the limited discoverable mode.
    - A device in non-discoverable mode will not be discovered by any device that is performing either the general discovery procedure or the limited discovery procedure.
3. **Connection modes and procedures**
    - allow a device to establish a connection to another device.
    - allow updating of parameters of the connection 
    - allow termination of the connection 
4. **Bonding modes and procedures**
    - Bonding allows two connected devices to exchange and store security and identity information to create a trusted relationship. 
    - Bonding can occur only between two devices in bondable mode.


<br>


### Usage API


|**Item No.** | **Modes and Procedures** | **nimBLE command** |
|----|---------|---------------|
|  1 | Broadcast Mode | `b adv conn=non disc=x` |
|    | Observation Procedure | `b scan dur=x disc=x type=x filt=x`  |
|  2 | Non-Discoverable mode   | `b adv conn=x disc=non`  |
|    | Limited Discoverable mode   | `b adv conn=x disc=ltd`  |
|    | General Discoverable mode   | `b adv conn=x disc=gen`  |
|    | Limited Discovery procedure | `b scan dur=x disc=ltd type=active filt=no_wl`  |
|    | General Discovery procedure | `b scan dur=x disc=gen type=active filt=no_wl`  |
|    | Name Discovery procedure  | `b scan dur=x` <br> `b scan cancel` <br> `b conn peer_addr_type=x peer_addr=x` <br> `b read conn=x uuid=0x2a00` |
|  3 | Non-connectable mode   | `b adv conn=non disc=x`  |
|    | Directed connectable mode  | `b adv conn=dir [own_addr_type=x] [disc=x] [dur=x]`  |
|    | Undirected connectable mode  | `b adv conn=und [own_addr_type=x] [disc=x] [dur=x]`  |
|    | Auto connection establishment procedure   | `b wl addr_type=x addr=x [addr_type=y addr=y] [...]` <br> `b conn addr_type=wl`  |
|    | General connection establishment procedure  | `b scan dur=x` <br> `b scan cancel` <br> `b conn peer_addr_type=x peer_addr=x` |
|    | Selective connection establishment procedure | `b wl addr_type=x addr=x [addr_type=y addr=y] [...]` <br> `b scan filt=use_wl dur=x` <br> `b scan cancel` <br> `b conn peer_addr_type=x peer_addr=x [own_addr_type=x]` |
|    | Direct connection establishment procedure  | `b conn addr_type=x addr=x [params]`  |
|    | Connection parameter update procedure   | `b update conn=x <params>` |
|    | Terminate connection procedure  | `b term conn=x`  |
|  4 | Non-Bondable mode    | `b set sm_data bonding=0` [\*] |
|    | Bondable mode        | `b set sm_data bonding=1` [\*] |
|    | Bonding procedure    | `b sec start conn=x` [\*] |

**[\*]** Security is disabled by default in bletiny.  To use the bonding modes and procedures, add `BLE_SM_LEGACY: 1` or `BLE_SM_SC: 1` to your syscfg.yml file depending on your needs.

<br>

### Address Types

| *bletiny string* | *Description* | *Notes* |
|------------------|---------------|
| public           | Public address. | |
| random           | Random static address. | |
| rpa_pub          | Resolvable private address, public identity. | Not available for all commands. |
| rpa_rnd          | Resolvable private address, random static identity. | Not available for all commands. |
| wl               | Use white list; ignore peer_addr parameter. | Only availble for "conn" command. |

### Connection Types

Bluetooth Specification Version 5.0 allows for different types of connections:

| *Description*                                  | *bletiny ext parameter value* |
|------------------------------------------------|-------------------------------|
| Legacy connection                              | none                          |
| Extended connection with 1M PHY                | 1M                            |
| Extended connection with coded PHY             | coded                         |
| Extended connection with both 1M and coded PHY | both                          |
| Extended connection with 1M, 2M and coded PHYs | all                           |

### Connection Parameters

Connection parameter definitions can be found in Section 7.8.12 of the BLUETOOTH SPECIFICATION Version 5.0 [Vol 2, Part E].

Connection parameters for all types of connections:

| *Name*            | *Description*                                                                       | *bletiny string* |
|-------------------|-------------------------------------------------------------------------------------|------------------|
| Connection Type   | Parameter indicating the type of connection                                         | ext              |
| Peer_Address_Type | Whether the peer is using a public or random address (see Address types table).     | peer_addr_type   |
| Peer_Address      | The 6-byte device address of the peer; ignored if white list is used                | peer_addr        |
| Own_Address_Type  | The type of address to use when initiating the connection (see Address types table) | own_addr_type    |
| Duration          | Number of milliseconds before aborting the connect attempt                          | dur              |

Connection parameters for legacy and 1M PHY extended connection:

| *Name*              | *Description*                                                                                           | *bletiny string* |
|---------------------|---------------------------------------------------------------------------------------------------------|------------------|
| LE_Scan_Interval    | Recommendation from the Host on how long the Controller should scan                                     | scan_itvl        |
| LE_Scan_Window      | Recommendation from the Host on how frequently the Controller should scan                               | scan_window      |
| Conn_Interval_Min   | Defines minimum allowed connection interval                                                             | itvl_min         |
| Conn_Interval_Max   | Defines maximum allowed connection interval                                                             | itvl_max         |
| Conn_Latency        | Defines the maximum allowed connection latency                                                          | latency          |
| Supervision_Timeout | Link supervision timeout for the connection.                                                            | timeout          |
| Minimum_CE_Length   | Informative parameter providing the Controller with the expected minimum length of the connection event | min_ce_len       |
| Maximum_CE_Length   | Informative parameter providing the Controller with the expected maximum length of the connection event | max_ce_len       |

Extended Connection parameters for coded PHY connection:

| *Name*              | *Description*                                                                                           | *bletiny string*  |
|---------------------|---------------------------------------------------------------------------------------------------------|-------------------|
| LE_Scan_Interval    | Recommendation from the Host on how long the Controller should scan                                     | coded_scan_itvl   |
| LE_Scan_Window      | Recommendation from the Host on how frequently the Controller should scan                               | coded_scan_window |
| Conn_Interval_Min   | Defines minimum allowed connection interval                                                             | coded_itvl_min    |
| Conn_Interval_Max   | Defines maximum allowed connection interval                                                             | coded_itvl_max    |
| Conn_Latency        | Defines the maximum allowed connection latency                                                          | coded_latency     |
| Supervision_Timeout | Link supervision timeout for the connection.                                                            | coded_timeout     |
| Minimum_CE_Length   | Informative parameter providing the Controller with the expected minimum length of the connection event | coded_min_ce_len  |
| Maximum_CE_Length   | Informative parameter providing the Controller with the expected maximum length of the connection event | coded_max_ce_len  |

Extended Connection parameters for 2M PHY connection:

| *Name*              | *Description*                                                                                           | *bletiny string* |
|---------------------|---------------------------------------------------------------------------------------------------------|------------------|
| Conn_Interval_Min   | Defines minimum allowed connection interval                                                             | 2M_itvl_min      |
| Conn_Interval_Max   | Defines maximum allowed connection interval                                                             | 2M_itvl_max      |
| Conn_Latency        | Defines the maximum allowed connection latency                                                          | 2M_latency       |
| Supervision_Timeout | Link supervision timeout for the connection.                                                            | 2M_timeout       |
| Minimum_CE_Length   | Informative parameter providing the Controller with the expected minimum length of the connection event | 2M_min_ce_len    |
| Maximum_CE_Length   | Informative parameter providing the Controller with the expected maximum length of the connection event | 2M_max_ce_len    |

### Scan Types

Bluetooth Specification Version 5.0 allows for different types of scan:

| *Description*                            | *bletiny ext parameter value* |
|------------------------------------------|-------------------------------|
| Legacy scan                              | 0                             |
| Extended scan with 1M PHY                | 1M                            |
| Extended scan with coded PHY             | coded                         |
| Extended scan with both 1M and coded PHY | both                          |

### Scan Parameters

Scan parameter definitions can be found in Section 7.8.10 of the BLUETOOTH SPECIFICATION Version 5.0 [Vol 2, Part E].

| *Name*                 | *Description*                                                             | *bletiny string* |
|------------------------|---------------------------------------------------------------------------|------------------|
| Scan Type              | Parameter indicating the type of scan                                     | ext              |
| LE_Scan_Type           | Controls the type of scan to perform (passive or active)                  | passive          |
| LE_Scan_Interval       | Recommendation from the Host on how long the Controller should scan       | itvl             |
| LE_Scan_Window         | Recommendation from the Host on how frequently the Controller should scan | window           |
| Scanning_Filter_Policy | Policy about which advertising packets to accept                          | filter           |
| Duration               | Number of milliseconds before canceling scan procedure                    | dur              |
| Limited                | Limited scan procedure                                                    | ltd              |
| No duplicates          | Filter out duplicates in shell output                                     | nodups           |
| Own_Address_Type       | The type of address to use when scanning (see Address types table)        | own_addr_type    |

Extended Scan parameters:

| *Name*           | *Description*                                                             | *bletiny string* |
|------------------|---------------------------------------------------------------------------|------------------|
| Duration         | Number of milliseconds before canceling scan procedure                    | duration         |
| Period           | Period in which scan should be enabled for specified duration             | period           |
| LE_Scan_Type     | Controls the type of scan to perform (passive or active)                  | lr_passive       |
| LE_Scan_Interval | Recommendation from the Host on how long the Controller should scan       | lr_itvl          |
| LE_Scan_Window   | Recommendation from the Host on how frequently the Controller should scan | lr_window        |

### Advertisment Parameters

| *bletiny string* | *Description*                         | *Notes*                                                      | *Default*                 |
|------------------|---------------------------------------|--------------------------------------------------------------|---------------------------|
| conn             | Connectable mode                      | See Connectable Modes table.                                 | und                       |
| disc             | Discoverable mode                     | See Discoverable Modes table.                                | gen                       |
| own_addr_type    | The type of address to advertise with | See Address Types table.                                     | public                    |
| peer_addr_type   | The peer's address type               | Only used for directed advertising; see Address Types table. | public                    |
| peer_addr        | The peer's address                    | Only used for directed advertising                           | N/A                       |
| chan_map         |                                       |                                                              | 0                         |
| filt             | The filter policy                     | See Advertisement Filter Policies table.                     | none                      |
| itvl_min         |                                       | units=0.625ms                                                | non: 100ms; und/dir: 30ms |
| itvl_max         |                                       | units=0.625ms                                                | non: 150ms; und/dir: 60ms |
| hd               | Whether to use high-duty-cycle        | 0/1                                                          | 0                         |
| dur              |                                       | Milliseconds                                                 | Forever                   |

Extended Advertising parameters:

| *bletiny string* | *Description*                                                                             | *Notes*        | *Default*                    |
|------------------|-------------------------------------------------------------------------------------------|----------------|------------------------------|
| tx_power         | Maximum power level at which the advertising packets are to be transmitted                | -127 - 127 dBm | 127 (Host has no preference) |
| primary_phy      | PHY on which the advertising packets are transmitted on the primary advertising channel   |                | none                         |
| secondary_phy    | PHY on which the advertising packets are transmitted on the secondary advertising channel |                | primary_phy                  |


### Advertising PHY Types

| *Description*                       | *bletiny parameter value* |
|-------------------------------------|---------------------------|
| Legacy advertising                  | none                      |
| Extended advertising with 1M PHY    | 1M                        |
| Extended advertising with 2M PHY    | 2M                        |
| Extended advertising with coded PHY | coded                     |

### Advertisement Filter Policies

| *btshell string*  | *Description*                                                          | *Notes*   |
| ----------------- | ---------------                                                        | --------- |
| none              | No filtering. No whitelist used.                                       | Default   |
| scan              | Process all connection requests but only scans from white list.        |           |
| conn              | Process all scan request but only connection requests from white list. |           |
| both              | Ignore all scan and connection requests unless in white list.          |           |
