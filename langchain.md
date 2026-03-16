# 🚀 FastAPI with LangChain Agent: Virtual Environment & Requirements Setup


## 📁 Updated Project Structure

```
my-langchain-api/
├── venv/                    # Virtual environment folder
├── app/
│   ├── __init__.py
│   └── main.py
└── requirements.txt         # Project dependencies
```

## 🛠️ Step-by-Step Implementation with Virtual Environment

### **Step 1: Create Project Folder and Virtual Environment**

```bash
# Create project folder
mkdir my-langchain-api
cd my-langchain-api

# Create virtual environment
# Windows:
python -m venv venv
# macOS/Linux:
python3 -m venv venv
```

### **Step 2: Activate Virtual Environment**

```bash
# Windows (Command Prompt):
venv\Scripts\activate.bat

# Windows (PowerShell):
venv\Scripts\Activate.ps1

# macOS/Linux:
source venv/bin/activate
```

> ⚠️ **Important**: Always activate the virtual environment before installing packages or running your application 【turn0search7】.

### **Step 3: Create requirements.txt File**

Create a `requirements.txt` file in your project root with the following content:

```txt
fastapi==0.115.6
uvicorn[standard]==0.32.1
langchain==0.3.14
langchain-google-vertexai==2.0.8
langchain-core==0.3.29
```

<details>
<summary>🔧 Alternative: Generate requirements.txt automatically</summary>

After installing packages in your virtual environment, you can generate the requirements file:

```bash
# Generate requirements.txt with exact versions
pip freeze > requirements.txt

# Or use pipreqs for minimal requirements (only what you import)
pip install pipreqs
pipreqs . --force
```

The `pip freeze` method lists all installed packages with exact versions, while `pipreqs` scans your code to include only necessary dependencies 【turn0search11】.

</details>

### **Step 4: Install Dependencies from requirements.txt**

```bash
# Install all dependencies at once
pip install -r requirements.txt
```

### **Step 5: Setup Google Cloud Authentication (ADC)**

```bash
# Install gcloud CLI if not already installed
# Then authenticate with Application Default Credentials
gcloud auth application-default login
```

### **Step 6: Create Application Code**

Create the `app` folder and `main.py` file:

```bash
# Create app directory
mkdir app

# Create __init__.py (makes it a Python package)
# Windows:
type nul > app\__init__.py
# macOS/Linux:
touch app/__init__.py
```

Create `app/main.py` with this enhanced code:

```python
from fastapi import FastAPI
from langchain_google_vertexai import ChatVertexAI
from langchain.agents import AgentExecutor, create_tool_calling_agent
from langchain_core.tools import tool
from langchain_core.prompts import ChatPromptTemplate
import os

# Initialize FastAPI
app = FastAPI(
    title="LangChain Agent API",
    description="A simple FastAPI application with LangChain Agent using Vertex AI",
    version="1.0.0"
)

# Initialize Vertex AI Model - Uses ADC automatically
llm = ChatVertexAI(model_name="gemini-1.5-flash")

# Define tools
@tool
def calculate(expression: str) -> str:
    """Calculate mathematical expression"""
    try:
        return str(eval(expression))
    except Exception as e:
        return f"Error: {str(e)}"

@tool
def get_current_time() -> str:
    """Get current date and time"""
    from datetime import datetime
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

# Create agent
tools = [calculate, get_current_time]
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant with access to tools."),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}"),
])
agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# API Endpoints
@app.get("/")
def root():
    return {
        "message": "API is running. Use /docs for interactive documentation.",
        "endpoints": {
            "docs": "/docs",
            "ask": "/ask (POST)"
        }
    }

@app.post("/ask")
async def ask_question(question: str):
    """Ask the agent a question"""
    try:
        result = agent_executor.invoke({"input": question})
        return {
            "question": question,
            "answer": result["output"],
            "tools_used": result.get("intermediate_steps", [])
        }
    except Exception as e:
        return {"error": str(e)}

@app.get("/health")
def health_check():
    return {"status": "healthy", "service": "langchain-agent-api"}
```

### **Step 7: Run the Application**

```bash
# Run from the project root directory
# Note the app.main:app structure
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### **Step 8: Test the API**

Visit `http://localhost:8000/docs` and test with:
- `What is 25 * 4 + 10?`
- `What time is it now?`
- `Calculate 100 / 5`
