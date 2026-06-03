### **Repository Template: Software Engineering Concepts**

โครงสร้างนี้ถูกออกแบบตามหลักการ "Monorepo" แบบง่ายๆ ในช่วงเริ่มต้น และพร้อมที่จะวิวัฒนาการไปสู่โครงสร้างแบบ Microservices ในภายหลัง

#### **โครงสร้างโฟลเดอร์และไฟล์ (Folder & File Structure)**

```
.
├── .github/
│   └── workflows/
│       └── ci.yml         # GitHub Actions workflow for Continuous Integration
├── .vscode/
│   └── settings.json      # Recommended VS Code settings for the project
├── docs/
│   ├── architecture/
│   │   ├── adr/
│   │   │   └── 0001-tech-stack-selection.md
│   │   ├── diagrams/
│   │   │   ├── c4-level-1-context.png
│   │   │   └── c4-level-2-container.png
│   │   └── README.md
│   └── requirements/
│       ├── personas/
│       │   └── example_persona.md
│       └── main_scenario.md
├── prototypes/
│   └── sprint1/
│       ├── prototype.html
│       └── style.css
├── services/
│   └── stats-service/     # Example microservice folder
│       ├── app.py
│       ├── requirements.txt
│       └── Dockerfile
├── .dockerignore          # Specifies which files to ignore when building Docker images
├── .gitignore             # Specifies which files/folders Git should ignore
└── README.md              # The main entry point for your project documentation
```

---

#### **รายละเอียดและ Template ของไฟล์สำคัญ**

**1. `README.md` (ไฟล์หลัก)**

นี่คือหน้าตาของโปรเจกต์ ควรมีข้อมูลที่จำเป็นสำหรับทุกคนที่เข้ามาดู

```markdown
# Project: [ชื่อโปรเจกต์ของคุณ]
_A brief, one-sentence description of what your project does._

## Team: [ชื่อทีมของคุณ]
- [ชื่อ-นามสกุล], GitHub: `[github_username]`
- [ชื่อ-นามสกุล], GitHub: `[github_username]`
- [ชื่อ-นามสกุล], GitHub: `[github_username]`

---

## 🚀 Getting Started

This section explains how to get a local copy of the project up and running for development and testing purposes.

### Prerequisites

- Git
- Python 3.10+
- Docker & Docker Compose

### Running the Application (with Docker)

1.  **Clone the repository:**
    ```bash
    git clone [your-repository-url]
    cd [your-project-folder]
    ```

2.  **Run the services:**
    *(This section will be updated in later weeks when using Docker Compose)*
    ```bash
    # Example for a single service
    cd services/stats-service
    docker build -t your-team/stats-service .
    docker run -p 5001:5000 your-team/stats-service
    ```

---

## 🏛️ Architecture & Design

Key architectural decisions and diagrams are documented in the `/docs/architecture` directory.

- **[C4 Models & Tech Stack](./docs/architecture/README.md)**
- **[Architectural Decision Records (ADRs)](./docs/architecture/adr/)**

## 👥 Requirements

User personas and key scenarios that drive our development are located in the `/docs/requirements` directory.

- **[User Personas](./docs/requirements/personas/)**
- **[Main User Scenario](./docs/requirements/main_scenario.md)**
```

**2. `.gitignore`**

ไฟล์นี้สำคัญอย่างยิ่งยวดเพื่อป้องกันไม่ให้ไฟล์ที่ไม่จำเป็นหรือเป็นความลับหลุดเข้าไปใน Repository

```gitignore
# Python
__pycache__/
*.pyc
*.pyo
*.pyd
.Python
env/
venv/
*.venv/
.supercharged/ # ชื่อ virtual env ที่ใช้ในแล็บ

# Environment variables
.env
.env.*

# IDE / OS specific
.vscode/
.idea/
*.DS_Store

# Build artifacts
dist/
build/
*.egg-info/
```

**3. `.dockerignore`**

ไฟล์นี้ทำงานคล้าย `.gitignore` แต่สำหรับ Docker โดยจะบอก Docker ว่าไม่ต้องคัดลอกไฟล์หรือโฟลเดอร์ใดเข้าไปใน Image ซึ่งช่วยให้ Image มีขนาดเล็กลงและปลอดภัยขึ้น

```dockerignore
# Git
.git
.gitignore

# Python virtual environment
.venv/
venv/
env/
.supercharged/

# IDE / OS specific
.vscode/
.idea/
*.DS_Store

# Documentation and prototypes (not needed in production image)
docs/
prototypes/

# Local development files
*.pyc
__pycache__/
```

**4. `.github/workflows/ci.yml`**

นี่คือ Template เริ่มต้นสำหรับ CI Pipeline ที่จะรันทุกครั้งที่มีการ Push ไปยัง `main` branch หรือมีการเปิด Pull Request

```yaml
name: Python CI Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Set up Python 3.10
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 pytest
        # Install dependencies for each service
        if [ -f services/stats-service/requirements.txt ]; then pip install -r services/stats-service/requirements.txt; fi

    - name: Lint with flake8
      run: |
        # Stop the build if there are Python syntax errors or undefined names
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # Exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics

    - name: Test with pytest
      run: |
        pytest
```

---

**คำแนะนำสำหรับอาจารย์:**
ท่านสามารถสร้าง Repository ใหม่บน GitHub Organization โดยใช้ Template นี้ได้โดยตรง จากนั้นจึงทำการสร้าง Repository สำหรับแต่ละกลุ่มโดยใช้ Repository นี้เป็นต้นแบบ (Template Repository) ซึ่งจะช่วยให้นิสิตทุกกลุ่มเริ่มต้นจากโครงสร้างที่เป็นมาตรฐานเดียวกันและพร้อมสำหรับกิจกรรมในแต่ละสัปดาห์ทันที
