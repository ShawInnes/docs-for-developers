
# Equipment Interface

### Colour Key

| Colour | Significance | Protocol |
|---|---|---|
| Blue | AMR | PLC over HTTP | 
| Orange | Setup Station | PLC Over OPC/UA|
| Black | Server to Equipment Interface | MQTT |

## Pallet Transfer Sequence Diagram

```plantuml
@startuml
title Pallet AMR to Setup Station\n(Load a Pallet from AMR onto an incoming lane on the Setup Station)

participant server          order 10
participant amr             order 20
participant aei             order 30
participant setup           order 40

autonumber "<b>[000]"

server -> aei     : BEGIN_PALLET_LOAD_SAGA
aei -> server     : PALLET_LOAD_SAGA_IN_PROGRESS

server -> amr     : <color blue>AMR_START
note over server, amr: AMR moves to Setup Station lane and docks

amr -> server     : <color blue>DOCKED_AT_TARGET</color>

server -> aei     : REQUEST_PALLET_LOAD
aei -> setup  : <color darkorange>Lane[<lane_id>].TransferInReq true</color>
aei -> server     : WAITING_FOR_PLC_LOAD_READY
note over aei,setup: Start rollers on the lane
setup -> aei  : <color darkorange>Lane[<lane_id>].TransferInRdy true</color>

group Setup Station Incoming Lane
    aei -> server     : READY_FOR_PALLET_LOAD
    
    server -> amr     : <color blue>AMR_START_ROLLERS
    server -> aei     : START_PALLET_LOAD
    aei -> setup  : PALLET_TRANSFER_IN_BUSY
    note over aei,setup: Wait for Setup Station PLC to notify that pallet transfer is complete
    setup -> aei  : <color darkorange>Lane[<lane_id>].TransferInComplete true</color>
    aei -> server     : PALLET_LOAD_COMPLETED

    server -> amr     : <color blue>END_PALLET_LOAD_SAGA</color>
    note over server, amr: AMR undocks and departs

    aei -> server     : READY
end
@enduml
```
