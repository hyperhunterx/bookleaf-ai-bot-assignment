# Identity Unification System - Design Approach and Explanation

This document explains the strategy and logic behind the proposed User Identity Unification System, as visualized in the accompanying flowchart.

### 1. Core Strategy: Hierarchical Matching

The fundamental design principle is **Hierarchical Matching**, which prioritizes efficiency and accuracy. The system does not treat all identifiers equally. Instead, it processes them in a specific order, from most reliable to least reliable.

*   **Hard Keys (High Confidence):** These are unique and verifiable identifiers like `email` addresses and `phone numbers`. The system always attempts to match on these first because a successful match is 100% certain and computationally inexpensive.
*   **Soft Keys (Variable Confidence):** These are ambiguous or partial identifiers like a `name` ("Sara J.") or a `social media handle` ("@sarapoetry23"). These require more complex logic (fuzzy matching, LLM analysis) and always produce a confidence score rather than a certain match.

This approach ensures that the vast majority of queries are resolved instantly and accurately, reserving the more resource-intensive AI analysis for the edge cases that truly need it.

### 2. Proposed Data Structure

To make this system scalable, a new table, `author_identities`, would be introduced. This table acts as a fast lookup cache, linking various platform identifiers back to a single, canonical author ID in the main `authors` table. This prevents re-running expensive fuzzy or LLM matches for the same user in the future.

**`author_identities` Table Schema:**

| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| `id` | `uuid` | Primary Key |
| `author_id` | `uuid` | Foreign Key to `authors.id` |
| `platform` | `text` | 'Email', 'WhatsApp', 'Instagram' |
| `platform_identifier` | `text` | The user's ID on that platform |
| `is_verified` | `boolean` | `true` if this link is 100% confirmed |

### 3. Logic Breakdown (Following the Flowchart)

1.  **Step 1: Exact Match (The Fast Path):** When a query arrives, the system first checks if the identifier (e.g., `sara.johnson@xyz.com`) is a "Hard Key." If so, it performs a direct, fast search against the `author_identities` and `authors` tables. A successful match here has a 100% confidence score.

2.  **Step 2: Fuzzy Name Matching:** If no exact match is found, the system proceeds to fuzzy logic. It compares the identifier (e.g., "Sara J.") against all names in the `authors` database. Using algorithms like Token Set Ratio, it calculates a similarity score.
    *   A very high score (>90%) can be treated as a confident match.
    *   A moderate score (70-90%) would link the query but flag it for a human to quickly verify.

3.  **Step 3: LLM-Based Matching (The Smart Path):** For highly ambiguous identifiers like social media handles (`@sarapoetry23`), which have no structural similarity to a real name ("Sara Johnson"), an LLM is used. The LLM is given the handle and a list of potential author names and asked to make a contextual judgment. This is the most powerful but also the most expensive step, which is why it is performed last.

4.  **Step 4: The Fallback (The Safe Path):** A core requirement is the fallback rule. If at any point the system cannot find a match or if the highest confidence score is below a set threshold (e.g., 70%), the process stops. The query is flagged for manual review and escalated to a human agent, ensuring no author is ever incorrectly identified or ignored.