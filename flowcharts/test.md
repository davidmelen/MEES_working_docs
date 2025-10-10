```mermaid  
    graph TD;
    A[Start] --> B(Process Data);
    B --> C{Is data valid?};
    C -->|Yes| D[Store Data];
    C -->|No| E[Log Error];
    D --> F[End];
    E --> F;
```