# Identity Unification System - Flowchart

This flowchart outlines the logical process for identifying an author from various platforms. The system prioritizes exact matches for speed and accuracy, falling back to fuzzy logic and LLM-based analysis for more ambiguous cases.

```mermaid
graph TD
    A["Start: Receive Incoming Query <br> (Platform, Identifier, Message)"] --> B{"Is Identifier a known Hard Key? <br> (Email or Phone Number)"};
    B -- Yes --> C["Query `author_identities` & `authors` tables for exact match"];
    C --> D{Match Found?};
    D -- Yes --> E["Link to Author Profile <br> Confidence: 100% <br> Store new identity if needed"];
    D -- No --> F[Proceed to Fuzzy Matching];
    B -- No --> F;

    F --> G["Attempt Fuzzy Match against Author Names <br> (e.g., &quot;Sara J.&quot; vs &quot;Sara Johnson&quot;)"];
    G --> H{"High-Confidence Match? <br> (e.g., Score > 90)"};
    H -- Yes --> I["Link to Author Profile <br> Store new identity <br> Confidence: [Score]"];
    H -- No --> J{Is Platform Social Media?};
    J -- Yes --> K["Use LLM to Match Handle/Name <br> (e.g., &quot;@sarapoetry23&quot;)"];
    K --> L{"Confident Match from LLM? <br> (e.g., Score > 75)"};
    L -- Yes --> I;
    L -- No --> M[Flag for Manual Verification];
    J -- No --> M;
    G -- No Match --> J;

    E --> N[End: Process Query];
    I --> N;
    M --> O[End: Escalate to Human Agent];
```
