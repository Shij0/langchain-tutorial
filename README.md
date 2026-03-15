### 📁 Final Folder Structure
Your project will look like this:

```text
my-simple-api/
└── app/
    ├── __init__.py
    └── main.py
```

---

### 🚀 Step-by-Step Implementation

#### Step 1: Create Project Folder
Open your terminal (Command Prompt or Terminal) and run:

```bash
mkdir my-simple-api
cd my-simple-api
```

#### Step 2: Install FastAPI and Uvicorn
Run this command to install the necessary packages:

```bash
pip install fastapi "uvicorn[standard]"
```

#### Step 3: Create App Folder and Files
Create the folder structure and files:

1.  **Create the `app` directory:**
    ```bash
    mkdir app
    ```

2.  **Create the `__init__.py` file:**
    (This makes the folder a Python package. You can leave it empty.)
    ```bash
    # On Windows
    type nul > app\__init__.py
    # On macOS/Linux
    touch app/__init__.py
    ```

3.  **Create the `main.py` file:**
    Create a file named `main.py` inside the `app` folder.

    **File Path:** `app/main.py`

    Paste this code inside `app/main.py`:

    ```python
    from fastapi import FastAPI

    # Create the API instance
    app = FastAPI()

    # Define a root endpoint
    @app.get("/")
    def read_root():
        return {"message": "Hello World"}

    # Define an endpoint with a path parameter
    @app.get("/items/{item_id}")
    def read_item(item_id: int):
        return {"item_id": item_id}
    ```

---

### ▶️ Step 4: Run the API

Since your code is now inside the `app` folder, you run it using `app.main:app`.

Run this command in your terminal:

```bash
uvicorn app.main:app --reload
```

### ✅ Step 5: Test the API

Open your browser and go to:

1.  **Root URL:** [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
    *   You will see: `{"message": "Hello World"}`

2.  **Item URL:** [http://127.0.0.1:8000/items/5](http://127.0.0.1:8000/items/5)
    *   You will see: `{"item_id": 5}`

3.  **Documentation:** [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
    *   You will see the automatic interactive API documentation.
