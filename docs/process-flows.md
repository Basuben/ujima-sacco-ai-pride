# Ujima Loan Desk: Process Flows

Two views of the same journey. The first shows who does what. The second shows how the parts of the system talk to each other.

The first diagram is a swimlane flow in the style of BPMN, drawn in Mermaid. It is not a formal BPMN 2.0 file. The second is a UML sequence diagram.

## 1. Application to final decision (swimlanes)

```mermaid
flowchart TD
  subgraph MEM["Member"]
    A([Start]) --> B[Fill in the application form]
  end
  subgraph SYS["Application"]
    C{Inputs within limits?}
    D[Show error and stop]
    E[Send application for scoring]
    H{Response has the required structure?}
    I[Show error and store nothing]
    J[Save decision to the officer queue]
    Q[Update queue counts and analytics]
  end
  subgraph AI["AI underwriter"]
    F[Score against SACCO policy]
    G[Return structured recommendation]
  end
  subgraph OFF["Loan officer"]
    K[Open queue and read the rationale]
    L{Agree with the AI?}
    M[Keep the AI decision]
    N[Override the decision]
    O([Final decision recorded])
  end
  B --> C
  C -- No --> D
  C -- Yes --> E
  E --> F --> G --> H
  H -- No --> I
  H -- Yes --> J
  J --> K --> L
  L -- Yes --> M --> O
  L -- No --> N --> O
  O --> Q
```

## 2. System interaction (sequence)

```mermaid
sequenceDiagram
  actor M as Member
  participant App as Web app
  participant Srv as Server function
  participant AI as AI gateway
  participant Store as Browser storage
  actor O as Loan officer
  M->>App: Submit application
  App->>Srv: Send application data
  Srv->>Srv: Validate inputs
  alt Inputs invalid
    Srv-->>App: Error
    App-->>M: Show error message
  else Inputs valid
    Srv->>AI: Policy instructions and application
    AI-->>Srv: Recommendation as JSON
    Srv->>Srv: Check JSON structure
    alt Structure invalid
      Srv-->>App: Error
      App-->>M: Show error message
    else Structure valid
      Srv-->>App: Decision
      App->>Store: Save application and decision
      App-->>M: Show result and open officer queue
    end
  end
  O->>App: Open queue and choose an override
  App->>Store: Save override
  App-->>O: Update queue and analytics
```

## 3. Notes for the next release (to-be)

- **B-01:** the last step in the swimlane should update the disbursed by purpose chart from approved loans only. Today it also counts rejected ones.
- **B-02:** when the officer overrides, the flow should ask for a short reason and store it next to the override, so the audit trail explains why a person disagreed with the AI.
