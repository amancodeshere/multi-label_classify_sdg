# Abstract 2 SDG — Website diagrams

These diagrams describe the proposed website in the [Thesis B implementation plan](next-phase-proposal.md).

## 1. Sequence diagram — A reader browses papers by SDG

```mermaid
sequenceDiagram
    actor Reader as General reader
    participant Site as UNSW SDG website
    participant API as Catalogue API
    participant DB as Catalogue database

    Reader->>Site: Open the SDG landing page
    Site-->>Reader: Show the 17 goals
    Reader->>Site: Select an SDG
    Site->>API: Request papers for the selected goal
    API->>DB: Find papers by effective SDG label
    DB-->>API: Return matching papers and label status
    API-->>Site: Return a page of results
    Site-->>Reader: Show title, authors, year, label status and source link
    Reader->>Site: Open the paper's source link
    Site-->>Reader: Navigate to the original paper
```

## 2. Sequence diagram — A researcher reviews a newly published paper

```mermaid
sequenceDiagram
    actor Author as UNSW researcher
    participant OA as OpenAlex
    participant Site as Abstract 2 SDG service
    participant Model as SciBERT
    participant DB as Catalogue database

    Note over Author,OA: The researcher publishes a paper and OpenAlex indexes it
    Note over Site,OA: Proposed schedule is one scan per day
    Site->>OA: Scheduled worker fetches newly indexed UNSW works
    OA-->>Site: Return work ID, metadata and available abstract
    Site->>DB: Upsert the paper by DOI or OpenAlex work ID
    alt Abstract available
        Site->>Model: Classify the abstract
        Model-->>Site: Return suggested SDG labels
        Site->>DB: Save the versioned prediction
        opt Eligible new paper with a verified UNSW author
            Site-->>Author: Email suggested labels and a secure review link
            Author->>Site: Open review link
            Site-->>Author: Show the paper and proposed labels
            alt Author confirms the labels
                Author->>Site: Confirm the suggested labels
                Site->>DB: Save the confirmed label set as an author decision
            else Author proposes changes
                Author->>Site: Propose SDGs to add or remove
                Site->>DB: Save the proposal for review
                Note over Author,Site: Resolve the proposal with the author before publication
                Site->>DB: Save the agreed label set as an author decision
            end
            Site->>DB: Use the reviewed labels in the public goal lists
        end
    else Abstract missing
        Site->>DB: Record the paper as unclassified
    end
```

On a later sync, a new prediction can be saved for a changed abstract. The catalogue continues to use the agreed label set for that paper.

### Presentation version

This sequence follows a paper with an available abstract through author review.

```mermaid
sequenceDiagram
    participant Author as UNSW researcher
    participant OA as OpenAlex
    participant Site as Abstract 2 SDG
    participant Model as SciBERT
    participant DB as Catalogue database

    Note over Author,OA: OpenAlex indexes the researcher's paper
    Site->>OA: Check for new UNSW papers each day
    OA-->>Site: Return the paper and abstract
    Site->>Model: Classify the abstract
    Model-->>Site: Suggest SDG labels
    Site->>DB: Save the paper and suggested labels
    Site-->>Author: Send suggestions and a secure review link
    Author->>Site: Confirm or edit the suggested labels
    Site->>DB: Save the agreed labels as an author decision
    Note over Site,DB: The public catalogue shows the agreed labels
```

## 3. Data flow diagram — From SciBERT suggestions to reviewed labels

```mermaid
---
config:
  flowchart:
    nodeSpacing: 18
    rankSpacing: 20
    padding: 8
    wrappingWidth: 165
---
flowchart TB
    OA["OpenAlex"]
    Author["Verified UNSW author"]
    Import("Daily worker<br/>Import and deduplicate")
    Papers[("Paper records")]
    Model("Classify with SciBERT")
    Predictions[("Versioned model predictions")]
    Review("Secure author review")
    Decisions[("Author responses and agreed labels")]
    Resolve("Resolve labels for display")
    Catalogue["Public SDG catalogue"]

    OA -->|Work ID, DOI, metadata<br/>and abstract| Import
    Import -->|Paper record and<br/>abstract availability| Papers
    Papers -->|New or changed abstract| Model
    Model -->|Suggested labels<br/>and model version| Predictions

    Papers -->|Paper and verified<br/>author match| Review
    Predictions -->|Suggested labels| Review
    Review -->|Suggestions and<br/>secure link| Author
    Author -->|Confirm or propose<br/>additions and removals| Review
    Review -->|Response and agreed label set| Decisions

    Papers -->|Paper details and source link| Resolve
    Predictions -->|Suggested label set| Resolve
    Decisions -->|Agreed label set when available| Resolve
    Resolve -->|Effective SDG labels<br/>and review status| Catalogue

    classDef external fill:#f3edf7,stroke:#775393,color:#17112a
    classDef process fill:#ffffff,stroke:#775393,color:#17112a
    classDef store fill:#e8d7f1,stroke:#775393,color:#17112a
    class OA,Author,Catalogue external
    class Import,Model,Review,Resolve process
    class Papers,Predictions,Decisions store
```

The catalogue shows model suggestions while review is pending and switches to the agreed label set once review is complete. Keeping predictions and author decisions separate preserves the author’s corrections when SciBERT runs again. Papers without abstracts remain unclassified. Historical imports populate the catalogue, and author invitations go to eligible newly indexed papers.
