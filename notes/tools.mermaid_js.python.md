---
id: yn9a60e1jniixpejpwc4dxm
title: Python
desc: ''
updated: 1696698473552
created: 1696698214169
---
<https://code.likeagirl.io/creating-flowcharts-with-mermaid-in-python-3cbca0058ecb>

## Prerequisites

- Python is installed on your system.
- The PyExecJS library to execute JavaScript within Python. To install, use `pip install PyExecJS`
- A text editor or an integrated development environment (IDE) for writing Python scripts.

### Creating a Simple Flowchart

```py
import execjs
# Define the Mermaid code for the flowchart
mermaid_code = """
graph TD;
    A[Start] --> B[Decision]
    B -- Yes --> C[Option 1]
    B -- No --> D[Option 2]
    C --> E[End]
    D --> E
    E[End] --> F[End]
"""
# Create an ExecJS context
context = execjs.compile("""
    var mermaid = require('mermaid');
    mermaid.initialize({startOnLoad:true});
    function renderMermaid(mermaidCode) {
        mermaid.mermaidAPI.render('mermaid', mermaidCode, function(svgCode, bindFunctions) {
            document.getElementById('diagram').innerHTML = svgCode;
        });
    }
""")
# Render the flowchart
context.call("renderMermaid", mermaid_code)
# Print the Mermaid code for reference
print(mermaid_code)
```

### Running the Script

Save the script to a .py file and execute it using Python. The flowchart will be displayed in your web browser, and the Mermaid code will be printed in the terminal.
