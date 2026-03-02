---
sidebar_position: 2
---

# Case 2. Interact Mode

## Input a Question
Example Question:
```
Recommend potential accounts in the graph that are most likely to have transactional interactions with Hodges Mitchell.
```

## Collaborative Planning
After inputting the question, EtuGraph will plan an executable DAG (Directed Acyclic Graph) and display the plan to you. You can provide modification suggestions to refine the DAG. The modification methods are as follows:

- **Web Interface**: Enter modification suggestions directly in the chat box of the chat interface.
- **Terminal Interaction Mode**: Use the following command:
```
modify [your modification suggestions]
```

**Example of Modification Suggestions**:
```
modify Change Node 1 to identify the account group closely connected to Hodges Mitchell. Then identify the most transactionally active accounts within this group, and separately calculate the total transaction volume of Hodges Mitchell and the most active accounts.
```

After confirming the plan is correct, start the analysis and wait for EtuGraph to generate an analysis report.




