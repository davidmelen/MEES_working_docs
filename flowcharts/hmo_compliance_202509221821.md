```mermaid  
flowchart TD
    Start[Identify what is being let - property type, tenancy, EPC status] --> HMO{Is it an HMO multiple households?}

    HMO -- {No (single dwelling let to one household)} --> TenancyType
    HMO -- {Yes (HMO)} --> HMOType

    %% Non-HMO single-let branch
    TenancyType{Tenancy type<br/>assured/regulated/agricultural?:contentReference[oaicite:45]{index=45}}
    TenancyType -- "No (e.g. company let, license)" --> OutNotQual[/"MEES does not apply (not an<br/>eligible tenancy type)":contentReference[oaicite:46]{index=46}/]
    TenancyType -- "Yes (private AST/etc)" --> EPCReq

    EPCReq{Legally required to have EPC?:contentReference[oaicite:47]{index=47}:contentReference[oaicite:48]{index=48}}
    EPCReq -- "No (no EPC obligation,<br/>e.g. short let, listed)" --> OutNoEPC[/"MEES does not apply<br/>(EPC not required)":contentReference[oaicite:49]{index=49}/]
    EPCReq -- "Yes" --> EPCAvailable

    EPCAvailable{Valid EPC available<br/>for the unit?:contentReference[oaicite:50]{index=50}}
    EPCAvailable -- "No or expired" --> OutNoInfo[/"Insufficient information:<br/>EPC needed to assess compliance":contentReference[oaicite:51]{index=51}:contentReference[oaicite:52]{index=52}/]
    EPCAvailable -- "Yes" --> RatingCheck

    RatingCheck{EPC rating F or G<br/>(below E)?:contentReference[oaicite:53]{index=53}}
    RatingCheck -- "No (E or above)" --> OutCompliant[/"MEES applies – compliant<br/>(>=E rating met)":contentReference[oaicite:54]{index=54}/]
    RatingCheck -- "Yes (sub-standard)" --> ExemptCheck

    %% Exemption checks chain
    ExemptCheck --> AllImprovs
    subgraph Exemptions["Does a valid exemption apply? (If yes, must register):contentReference[oaicite:55]{index=55}"]
      direction TB
      AllImprovs{All relevant improvements made<br/>or none possible?:contentReference[oaicite:56]{index=56}}
      AllImprovs -- "Yes" --> ExemptAll[/"MEES applies – exempt<br/>(\"all improvements made\", 5 yrs):contentReference[oaicite:57]{index=57}"/]
      AllImprovs -- "No" --> CostCap
      CostCap{No improvement < £3,500? (High cost):contentReference[oaicite:58]{index=58}}
      CostCap -- "Yes" --> ExemptCost[/"MEES applies – exempt<br/>(high cost, 5 yrs):contentReference[oaicite:59]{index=59}"/]
      CostCap -- "No" --> Consent
      Consent{Needed third-party consent<br/>refused?:contentReference[oaicite:60]{index=60}}
      Consent -- "Yes" --> ExemptConsent[/"MEES applies – exempt<br/>(no consent, 5 yrs*):contentReference[oaicite:61]{index=61}"/]
      Consent -- "No" --> Devalue
      Devalue{Would improvement devalue<br/>property >5%?:contentReference[oaicite:62]{index=62}}
      Devalue -- "Yes" --> ExemptDeval[/"MEES applies – exempt<br/>(devaluation, 5 yrs):contentReference[oaicite:63]{index=63}"/]
      Devalue -- "No" --> Wall
      Wall{Wall insulation<br/>not appropriate?:contentReference[oaicite:64]{index=64}}
      Wall -- "Yes" --> ExemptWall[/"MEES applies – exempt<br/>(wall insulation, 5 yrs):contentReference[oaicite:65]{index=65}"/]
      Wall -- "No" --> NewLandlord
      NewLandlord{Landlord <br/>just acquired property?:contentReference[oaicite:66]{index=66}}
      NewLandlord -- "Yes" --> ExemptNew[/"MEES applies – exempt<br/>(new landlord, 6&nbsp;mo):contentReference[oaicite:67]{index=67}"/]
      NewLandlord -- "No (no exemption)" --> OutNonComp[/"MEES applies – <br/>non-compliant":contentReference[oaicite:68]{index=68}/]
    end

    %% HMO branch
    HMOType{HMO letting arrangement?}
    HMOType -- "Whole building let on one tenancy<br/>(single household or company)" --> TenancyType

    HMOType -- "Multiple separate lettings in HMO" --> HMOUnits
    HMOUnits{Does the HMO contain<br/>any self-contained units (flats)?}
    HMOUnits -- "Yes (mixed HMO: flats + room-lets)" --> FlatAssess
    HMOUnits -- "No (only room lets)" --> RoomsAssess

    FlatAssess[[For each self-contained flat:<br/>→ Check tenancy type and EPC requirement;<br/>→ Then follow EPC rating and exemption decisions<br/>as per single-let flow above:contentReference[oaicite:69]{index=69}.]]
    FlatAssess --> RoomsAssess

    RoomsAssess{Rooms with shared facilities<br/>(no flat of their own)?:contentReference[oaicite:70]{index=70}}
    RoomsAssess -- "No such rooms (none, or already handled)" --> End
    RoomsAssess -- "Yes (let by individual room ASTs)" --> WholeEPC

    WholeEPC{Was a whole-building EPC legally required<br/>(e.g. property sold or one-tenancy let in last 10 yrs)?:contentReference[oaicite:71]{index=71}}
    WholeEPC -- "No (no EPC for building)" --> OutNoEPCRooms[/"MEES does not apply to these room-lets<br/>(no EPC requirement per room or building)":contentReference[oaicite:72]{index=72}:contentReference[oaicite:73]{index=73}/]
    WholeEPC -- "Yes (building has EPC)" --> RoomsEPCRating

    RoomsEPCRating{Whole-building EPC rating F or G?}
    RoomsEPCRating -- "No (E or above)" --> OutCompliantRooms[/"MEES applies – compliant<br/>(HMO rooms: building EPC ≥E)":contentReference[oaicite:74]{index=74}/]
    RoomsEPCRating -- "Yes" --> ExemptCheck

    %% Note: Outcomes for room-lets with EPC F/G go through same Exemptions subflow above.

    classDef decision fill:#ffd,stroke:#000,stroke-width:1px,color:#000;
    classDef outcome fill:#dfd,stroke:#333,stroke-width:1px,color:#000;
    class HMO,HMOType,TenancyType,EPCReq,EPCAvailable,RatingCheck,ExemptCheck,AllImprovs,CostCap,Consent,Devalue,Wall,NewLandlord,RoomsAssess,WholeEPC,RoomsEPCRating decision;
    class OutNotQual,OutNoEPC,OutNoInfo,OutCompliant,OutNonComp,OutNoEPCRooms,OutCompliantRooms,ExemptAll,ExemptCost,ExemptConsent,ExemptDeval,ExemptWall,ExemptNew outcome;
```

