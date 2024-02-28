# PortML Document Library

## 1. About

PortML stands for Port Markup Language. It is a descriptive language or document which describes 
container port assets, such as yard blocks, quay cranes, wharves, and AGVs, as a markup code for modelling 
and simulation.

PortML document follows the PortML specification which is defined in the schema file PortML.xsd. The schema 
defines all of the datatypes and base infrastructure types.

## 2. How to Use

```csharp
using (StreamReader file = new StreamReader("<path to PortML document>"))
{
    try
    {
        string portMLDocument = file.ReadToEnd();

        var parser = new PortML.Documents.PortMLParser();
        var portML = parser.Parse(portMLDocument);
        ...
    }
    catch (Exception ex)
    {
        ...
    }
}
```

## 3. Design

To retrieve the design metadata from the `PortML` object, use the following code.

```csharp
var design = portML.GetDesign();
```

The code above will return a list of `DesignInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| DesignToActualScale | double | The scale from design pixel to meter in actual world. |

## 4. Terminal

There will be one and only one Terminal module in the same PortML document. It is to specify the boundary of the 
terminal and to provide static properties which are general to the entire port system.

To retrieve the terminal information from the `PortML` object, use the following code.

```csharp
var terminal = portML.GetTerminal();
```

If you would like the `BoundaryPoints` to be in meter, use the following code.

```csharp
var terminal = portML.GetTerminal(true);
```

The code above will return a list of `TerminalInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| BoundaryPoints | List<Vector2> | Boundary points of the terminal border which is a polygon. The area inside of the polygon is the terminal area. |
| NumberOfAGVs | int | Total number of AGVs running inside the terminal. |
| AverageAGVSpeed | double | Average AGV speed in m/s. |
| ClearanceDistance | double | Clearance distance between vessels, in pixel. |
| Forty2TwentyRatio | double | Forty-foot container to twenty-foot container ratio. |
| NumberOfQuayCranes | int | Total number of quay cranes available inside the terminal. |
| NumberOfAGVsServingEachQuayCrane  | int | Number of AGVs serving per quay crane. |
| NumberOfTEUsProcessedByEachQuayCrane  | int | Number of TEUs processed per quay crane. |

## 5. Wharf

Wharf is the side of the port terminal where vessels will be parked at.

Quay cranes will be located at the land side of the wharf while vessels will be parked at the sea side.
As shown in the following diagram, the direction of the wharf, defined by StartingAt and EndingAt, is
important. The wharf direction will decide which side of it is land side and which side of it is sea side.

![Wharf Diagram](https://o2desstorage.blob.core.windows.net/public/portml/portml_wharf.png)

To retrieve the wharf information from the `PortML` object, use the following code.

```csharp
var wharfs = portML.GetAllWharfs();
```

If you would like the `StartingAt`, `EndingAt` to be in meter, use the following code.

```csharp
var wharfs = portML.GetAllWharfs(true);
```

The code above will return a list of `WharfInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| ReferenceId | string | Reference ID of the wharf.  |
| StartingAt | Vector2 | The coordinate of the starting point of the wharf. |
| EndingAt | Vector2 | The coordinate of the ending point of the wharf.  |
| Length | double | Wharf length in meter. |


## 6. Yard Block
Yard block module is corresponding to the container yard in the sea port terminal.

The starting point and facing direction of a yard block is defined in the way shown in the following diagram.
The `Facing` property defines the direction of the yard block in radian rotating around the `StartingAt` point.
`Start Point` is located at the top left corner of the yard block when `Facing` value is 0.

![Yard Block Diagram](https://o2desstorage.blob.core.windows.net/public/portml/portml_yard_block.png)

To retrieve the yard block information from the `PortML` object, use the following code.

```csharp
var yardBlocks = portML.GetAllYardBlocks();
```

If you would like the `StartingAt`, `SlotWidth`, `SlotLength` to be in meter, use the following code.

```csharp
var yardBlocks = portML.GetAllYardBlocks(true);
```

The code above will return a list of `YardBlockInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| ReferenceId | string | Reference ID of the yard block. |
| StartingAt | Vector2 | The coordinate of the starting point of the yard block. |
| Facing | double | The direction of the yard block in radian.  |
| NumberOfRows | int | Number of rows of the yard block. |
| NumberOfBays | int | Number of bays of the yard block. |
| NumberOfTiers | int | Number of tiers of the yard block. |
| NumberOfOperatingTiers | int | Number of operating stacking height (tier) of the yard block. |
| BayTypes | BayType[] | Type (either exchange or transhipment) of each bay. |
| SlotWidth | double | Average width of each slot in the yard block. The total width of the yard block is thus SlotWidth * NumberOfRows. |
| SlotLength | double | Average length of each slot in the yard block. The total width of the yard block is thus SlotLength * NumberOfBays. |
| NumberOfYardCranes | int | Number of yard cranes in the yard block. |

## 7. Traffic Network

To retrieve the traffic network from the `PortML` object, use the following code.

```csharp
var trafficNetwork = portML.GetTrafficNetwork();
```

If you would like the `PositioningAt` to be in meter, use the following code.

```csharp
var trafficNetwork = portML.GetTrafficNetwork(true);
```

### 7.1 Quay Points

To retrieve the quay point information from the `trafficNetwork` object, use the following code.

```csharp
var quayPoints = trafficNetwork.QuayPoints;
```

The code above will return a list of `ControlPointInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| ReferenceId | string | Unique reference ID of the control point. |
| Tag | string | Tag of the control point. |
| PositioningAt | Vector2 | The coordinate of the control point. |
| WharfId | string | Reference ID of the wharf corresponding to the quay point. |

### 7.2 Yard Points

To retrieve the yard point information from the `trafficNetwork` object, use the following code.

```csharp
var yardPoints = trafficNetwork.YardPoints;
```

The code above will return a list of `ControlPointInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| ReferenceId | string | Unique reference ID of the control point. |
| Tag | string | Tag of the control point. It is made unique in the same traffic network. |
| PositioningAt | Vector2 | The coordinate of the control point. |
| YardBlockId | string | Reference ID of the yard block corresponding to the yard point. |
| YardCraneId | string | Reference ID of the yard crane corresponding to the yard point. |
| NumberOfRelatedGroundSlots | int | Number of ground slots which are corresponding to the yard point. |
| IsExchange | bool | True if the yard point is an Exchange Yard Point. False if it's a Transhipment Yard Point. |

### 7.3 Quay-Point-to-Yard-Point Distance

To retrieve the distance from each quay point in the traffic network to another yard point, use the following code.

```csharp
var distanceMatrix = trafficNetwork.PointDistancesFromQuayPointToYardPoint;
```

The code above will return a list of `PointToPointDistanceInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| SourceControlPointReferenceId | string | The unique reference ID of the quay point. |
| TargetControlPointReferenceId | string | The unique reference ID of the yard point. |
| Distance | double | Distance between the two control points in meter. |
| InBetweenControlPointReferenceIds | string[] | Control point reference IDs on the path between the two control points. |

This distance is calculated using Dijkstra's algorithm in the graph of the traffic network.

### 7.4 Yard-Point-to-Quay-Point Distance

To retrieve the distance from each yard point in the traffic network to another quay point, use the following code.

```csharp
var distanceMatrix = trafficNetwork.PointDistancesFromYardPointToQuayPoint;
```

The code above will return a list of `PointToPointDistanceInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| SourceControlPointReferenceId | string | The unique reference ID of the yard point. |
| TargetControlPointReferenceId | string | The unique reference ID of the quay point. |
| Distance | double | Distance between the two control points in meter. |
| InBetweenControlPointReferenceIds | string[] | Control point reference IDs on the path between the two control points. |

This distance is calculated using Dijkstra's algorithm in the graph of the traffic network.

### 7.5 Yard-Point-to-Yard-Point Distance

To retrieve the distance from each yard point in the traffic network to another yard point, use the following code.

```csharp
var distanceMatrix = trafficNetwork.PointDistancesFromYardPointToYardPoint;
```

The code above will return a list of `PointToPointDistanceInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| SourceControlPointReferenceId | string | The unique reference ID of the yard point. |
| TargetControlPointReferenceId | string | The unique reference ID of the yard point. |
| Distance | double | Distance between the two control points in meter. |
| InBetweenControlPointReferenceIds | string[] | Control point reference IDs on the path between the two control points. |

This distance is calculated using Dijkstra's algorithm in the graph of the traffic network.

## 8. Quay Crane Statistics

To retrieve the operating time statistics of a quay crane, use the following code.

```csharp
var quayCraneStatistic = portML.GetQuayCraneStatistics();
```

The code above will return a dictionary of `QuayCraneInfo` objects. The key is the quay crane name and the value contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| LoadingServiceTime | double | Time of a quay crane in servicing the loading operation. Unit of time is second. |
| LoadingRestoreTime | double | Time of a quay crane in restoring the loading operation. Unit of time is second. |
| UnloadingServiceTime | double | Time of a quay crane in servicing the unloading operation. Unit of time is second. |
| UnloadingRestoreTime | double | Time of a quay crane in restoring the unloading operation. Unit of time is second. |

## 9. Yard Crane Statistics

To retrieve the operating time statistics of a yard crane, use the following code.

```csharp
var yardCraneStatistic = portML.GetYardCraneStatistic();
```

The code above will return a dictionary of `YardCraneInfo` objects. The key is the yard crane name and the value contains contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| UnstackingServiceTime | double | Time of a yard crane in servicing the unstacking operation. Unit of time is second. |
| UnstackingRestoreTime | double | Time of a yard crane in restoring the unstacking operation. Unit of time is second. |
| StackingServiceTime | double | Time of a yard crane in servicing the stacking operation. Unit of time is second. |
| StackingRestoreTime | double | Time of a yard crane in restoring the stacking operation. Unit of time is second. |

## 10. AGV Parking Lot

AGV Parking Lot is where AGVs will be parked at.

To retrieve the AGV parking lot information from the PortML object, use the following code.

```csharp
var agvParkingLots = portML.GetAllAGVParkingLots();
```

If you would like the `StartingAt`, `SlotWidth`, `SlotLength` to be in meter, use the following code.

```csharp
var agvParkingLots = portML.GetAllAGVParkingLots(true);
```

The code above will return a list of `AGVParkingLotInfo` object which contains the following information.

| Property | Data Type | Description  |
|-|-|-|
| ReferenceId | string | Reference ID of the AGV parking lot. |
| StartingAt | Vector2 | The coordinate of the starting point of the AGV parking lot. |
| Facing | double | The direction of the AGV parking lot in radian.  |
| NumberOfSlots | int | Number of slots in the AGV parking lot. |
| SlotWidth | double | Average width of each slot in the AGV parking lot. The total width of the parking lot is thus SlotWidth * NumberOfSlots.. |
| SlotLength | double | Length of each slot in the AGV parking lot. |

## 11 PortML Document

PortML document can be retrieved from PortML object by below code:

```csharp
portML.ToPortMLDocument();
```

## 12. Release Notes

**14th Feb 2024**
- Provide PortML document from PortML object through ToPortMLDocument member function

**22th Aug 2023**
- Bug fix for quay crane statistic collection

**18th Oct 2022**
- Bug fix for traffic info summary

**4th Jul 2022**
- The control point tag information may not be unique. Reference ID should be applied for locating control point in PointToPointDistanceInfo
- Support multiple quay crans and yard cranes statistics outputs. Each type of cranes should have its own statistical information.

**24th Feb 2022**

- Updated TerminalInfo, WharfInfo, YardBlockInfo, ControlPointInfo, and AGVParkingLotInfo to 
able to return coordinates, slot width, and slot length in meter.

**22nd Feb 2022**

- Updated YardBlockInfo to provide NumberOfYardCranes.

**11th Feb 2022**

- Updated TerminalPortMLModule and YardSetPortMLModule to support SINGAPort Studio handling new configuration parameters.

**10th Feb 2022**

- Introduced DesignInfo with `MeterToVector` info.
- Updated TerminalInfo to have `ClearanceDistance`, `Forty2TwentyRatio`, `MaxQcNum`, `MaxAgvServeEachQC`, `QcAgvProcessTeu`, `AgvNum`, `AgvSpeed` info.
- Updated YardBlockInfo to have `OperationStackingHeight` info.

**27th Jan 2022**

- Introduced AGVParkingLotInfo.

**15th Jan 2022**

- Updated the shortest distance retrieval methods to return the tag of control points on the path.

**11th Jan 2022**

- Updated static properties in Yard Block module.

**4th Jan 2022**

- Added service time and restore time info of each quay crane and yard crane.

**2nd Jan 2022**

- Introduced traffic network and distance matrices.

**31st Dec 2021**

- Introduced TerminalInfo.

**30th Dec 2021**

- Introduced WharfInfo;
- Introduced YardBlockInfo.


## 13. Contributors

### Domain Experts
- Dr Li Haobin;
- Peng Yuming.

### Developers
- Riza Marhaban;
- Carl Chung Yuk Kay;
- Tan Sin Cho.

## 14. Feedback

Bug reports and contributions are welcome at [PortML Contact Us Page](https://portml.io/contact-us/).
