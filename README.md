# Using LLMs to Accelerate Threat Detection

This guide exists to accompany this workshop at Bsides Ljubljana.

## Requirements

- Linux or macOS
- Internet connection

## Non-requirements

- SQL experience - You won't be writing the SQL queries, the language model will
- LLM experience
- Python experience
- Computer science degrees

## Install Language Model runners

- <https://jan.ai> (recommended) or <https://ollama.com/> + <https://github.com/open-webui/open-webui> (advanced - less help will be given)

## Install a database viewer for sqlite3

- <https://sqlitebrowser.org/> (recommended)

## Download the cloud asset inventory database

For this workshop we will be using a simulated cloud asset inventory system database. It's using SQLITE3 as the database technology

### From Github

<https://github.com/RichardoC/Using-LLMs-to-Accelerate-Threat-Detection-Bsides-Ljubljana>

### From SSD

- Copy `bsides-Ljubljana.sqlite3.zip` to your machine
- Unzip `bsides-Ljubljana.sqlite3.zip`to release `bsides-Ljubljana.sqlite3`

## Download models

### Jan.ai

#### Models

- model id - model description

**Recommended**

- llama3.1-8b-instruct - (Llama 3.1 8B Instruct Q4)
  **Not recommended but possible for slow machines (more likely to work)**

- deepseek-coder-1.3b - (Deepseek Coder 1.3B Instruct Q8)

**Not recommended but possible for slow machines (less likely to work)**

- gemma-2-2b-it - (Gemma 2 2B Q4)

#### Downloading the models

- Select hub from left panel (the icon is four squares)
- Search for the model by id or description in the search bar - top middle with magnifiying glass icon
- Click `Download` beside the model (right hand side)

If you've already downloaded the model, it will say `Use` instead of `Download`

#### Importing the models from SSD (jan.ai)

- Paste the model folders from SSD (`gemma-2-2b-it` and `llama3.1-8b-instruct`) into `~/jan/models` on macos and `~/.config/Jan/models/` on Linux

### ollama

`ollama pull MODEL_ID`

#### Models

- model id

**Recommended**

[TODO include actual model ids]

- llama3.1:8b

**Not recommended but possible for slow machines**

- llama3.2:3b

## Runbooks

These runbooks might help with the challenges, or not

### Table schemas - Using DB Browser for SQLite

- Navigate to `Database Structure` Panel
- Open `Tables` Dropdown
- Right click the relevant table and click `Copy Create statement

### Running SQL queries

- Navigate to `Execute SQL`
- Type/Paste the SQL in the text box
- Execute the SQL with the `run` button, it's the play icon/right facing triangle
- Results are shown below the text box, info about the execution including errors are displayed below that.

### Foodoo Linux launched after license change

Foodoo changed their licensing policy on purple cloud last year. This query was used for our internal audit to make sure we aren't running any of the machines with the more expensive license.

```sql
SELECT pm.*
FROM purple_machines pm
JOIN purple_disk_images pdi ON pdi.image_id = pdi.image_id
WHERE datetime(pm.launch_time) >= date('now', '-12 month')
AND pdi.description LIKE 'foodoo-v1.0.8';
```

### Desktop brewed coffee

Don't tell anyone, but it turns out someone connected the 2nd floor coffee vending machine to the network. It takes about 50 seconds to brew a coffee once requested... Use this knowledge how you will.

Now to get the "assistant" to make it for me...

```python3
from datetime import datetime
from pydantic import BaseModel, Field
from urllib.parse import urlencode
from urllib.request import Request, urlopen


class Tools:
    class Valves(BaseModel):
        pass

    class UserValves(BaseModel):
        pass

    def __init__(self):
        self.valves = self.Valves()
        self.user_valves = self.UserValves()
        pass

    def make_coffee(self) -> str:
        """
        Make the user a coffee
        """
        try:
            url = "https://coffeemachine.example.com/post"  # Set destination URL here
            post_fields = {
                "type": "latte",
                "size'": "large",
                "decaff": "false",
            }  # Set POST fields here

            request = Request(url, urlencode(post_fields).encode())
            with urlopen(request) as response:
                resp = response.read()
                return rep
        except Exception as e:
            return f"Got exception: {e} instead of coffee"


# Usage
# tools = Tools()
# print("Make me a coffee:", tools.make_coffee())

```

### Find machine learning nodes

The machine learning team are taking advantage of GPUs for accelerating their training runs. The machine ids can be found using the query below

```sql
SELECT self_link FROM red_compute_instances WHERE NOT guest_accelerators <> '{}';
```

## Challenges

### 1 - How many RCE vulnerable machines?

You've discovered that version `5.0.2` of foodoo linux has a bug that allows Remote Code Execution if it was started since `2024-08-16`. How many Virtual Machines are affected across Orange and Red vendors?

### 2 - User triggered crashes

The Virtual machines based on `Hackers unite! Version v4.8.2` have a driver issue on `arm64` which can lead to stack corruption and cause crashes when commands sent by users. How many machines are affected and how old is the oldest affected machine?

### 3 - Mr. Babbage

Using your existing and new skills, find the attacker's VM(s) before they capsize the tankers.

## Bonus challenge

**Requires use of ollama/openwebui**

**Do not do this against databases you actually care about**

**Really, don't do this in production, and definitely not with write access!**

**Do as I say, not as I do - every instructor ever**

Write a `tool` <https://docs.openwebui.com/tutorial/tools/> for the language model, to allow it to run SQL on your behalf, and use that to attempt these challenges again by asking it to find these things.
