# Gihtub Actions

#### Continuous Integration (CI)

Continuous Integration is a practice where code changes are regularly pushed to a shared repository.\
Each push or merge triggers an automated build and testing process.\
This helps teams detect errors quickly, ensure code quality, and easily integrate new features.

#### Continuous Delivery / Continuous Deployment (CD)

**Continuous Deployment** means deploying the code directly to **production without manual approval**, after all tests pass.

**Continuous Delivery** means the code is automatically built and tested, but **manual approval is required** before deploying to production.

If you have done all the required tests correctly, then this approach will help deploy to production  without  problems.

In most companies, **CI** tests the code in isolation.\
After CI passes, **CD** deploys the code to an environment and tests it there.\
Deploying to **production usually requires manual approval**.

An environment can be:

* A physical server
* A virtual machine (VM)
* A Docker container
* A Kubernetes cluster

The application must work correctly in all environments.\
In CI, the code is tested in an isolated environment.

**GitHub Actions** is not just a CI/CD tool—it can do much more.\
There are many CI/CD systems, like CircleCI and Jenkins. But with GitHub Actions, you don’t need to do extra work to make tools work together—it’s already inside GitHub.

GitHub has thousands of pre-built actions made by companies like GitHub, Microsoft, and many others. Most of these actions are open source and also made by the community.

Anyone can create a GitHub Action for a specific task and share it publicly. Then, other people with the same need can use it easily.

<figure><img src=".gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

**Automation** is used for a task or a workflow.

* **Task**: a single automated action.
* **Workflow**: a sequence of connected tasks that you want to execute one after another extra

**Matrix build** means you have tasks, and you can give input about how many times you want them to run.

GitHub Actions workflows are written in **YAML** files (`.yml`).\
The actions themselves are usually written in **JavaScript**

<figure><img src=".gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

You can do almost everything with GitHub Actions, especially for **CI**, building apps, **deployment**, and **containers**.

Every GitHub Actions workflow has **three main parts**:

1. **Events**: Things that start or trigger the workflow. There are many types of events.
2. **Workflow**: The structure of all the tasks you want to perform.
3. **Actions**: Individual tasks inside the workflow that help you achieve your goal.

<figure><img src=".gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

As you can see, everything in GitHub Actions workflows is written in **YAML**.

* **Triggers** are the first part you specify in the YAML.
* After `on`, you can list all the **events** that will start the workflow.
* You can also **combine multiple events**, like `push` and `pull_request`, in the same workflow.

<figure><img src=".gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

**Best practice:** It is recommended to add `workflow_dispatch` to your workflow, at least during the **development process**.

This lets you **debug and test your workflow** without having to push code or create issues every time.

A **workflow run** is made up of one or more **jobs**, which can run **sequentially** or **in parallel**

<figure><img src=".gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

In GitHub Actions, you should **always put the events or triggers first** in the workflow.

After that, you define **steps**, which are basically the **actions you want to run**.

* You can use **pre-existing actions** (built by someone else) and pass parameters to perform the operation you want.
* Or, you can run **shell scripts** directly, depending on the OS:
  * On **Linux**, use **Bash**.
  * On **Windows**, use **PowerShell**.

<figure><img src=".gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

**Pre-built actions** are used for **complex operations**.

For example, `actions/setup-node` is an action that makes sure **Node.js is installed** on the machine where the action runs. If it’s not installed, the action installs it automatically. You could also do this yourself using **Bash**.

You can have **different strategies** for running jobs.

* At the **beginning of each job**, you specify `runs-on`, which tells GitHub Actions **where to run your workflow**.
* Jobs can run on a **specific machine** or use a **matrix** to run dynamically on multiple configurations.

<figure><img src=".gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

**GitHub Actions** automate operations, and you can use **two different approaches**:

1. **Reference existing actions** using `uses`.
2. **or You can Create your own actions**.

The GitHub Actions engine can run actions in a **Node.js runtime** or inside a **Docker container**, and you can **reference these actions** in your workflow

<figure><img src=".gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

To create a GitHub Action, you need **some required files**:

1. **`action.yml`** – This file contains the **metadata** of your action, similar to `package.json`. It defines the **inputs, outputs, and other information** about your action.
2. **`index.js`** – This is the **main file** and **entry point** of your action. You can split your action into multiple files, but these two files are required when running in the **Node.js context**.

You can develop actions in **any language**, as long as it can be **converted to JavaScript**. For example, there is an SDK that allows you to create actions in **.NET**. When you compile, it translates your code into proper JavaScript for GitHub Actions.

**Administrative actions** allow you to decide what kind of actions you want to execute in your **environment or repository**.

If you develop an action, you can **save it in your repo** and use it from there, or create a **separate repository** for that action.

For example, GitHub has a user called `actions` who created a repo called `setup-node`. That repo contains the `action.yml` file and JavaScript files for the action. Because the repo is public, you can **reference it in your workflow**.

The syntax for referencing a public action is:

```
user/repo-name@version
```

<figure><img src=".gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

If you decide to **share an action**, the repository must be **public**. Currently, GitHub Actions **cannot be shared privately**.

There are **different types of actions** that you can use in a workflow.

<figure><img src=".gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

This action **does not relate to CI/CD**.

<figure><img src=".gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

Follow the instructions and **create your own action**.

<figure><img src=".gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

If you create your own actions, there are **two ways**.

The first way is **monolithic actions**, which are actions that do **many things** in one.

For example, in a workflow, the first two actions might be **pre-built actions**, and the third action could be a **custom-created action**.

<figure><img src=".gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

It is **bad practice** to create a single action that does **deployment, testing, and calls some API** all in one.

Putting too many things in a single action or step makes it **difficult to debug**, because if something goes wrong, you won’t know **what caused the problem**.

The **better approach** is to create **modular actions**, where each action does **one specific task**.

<figure><img src=".gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

**Starter workflow** for the most common operations you want to achieve.

<figure><img src=".gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

**Demo:**

You can create your workflow **manually** or **automatically**.\
GitHub can **suggest workflows** based on your repository content.

You can write your GitHub workflow **directly on GitHub** or in **VS Code** using the **GitHub Actions extension**.

This means your workflow can **download the content of your repo** to the current workspace and perform other operations that **use or access that code**.

<figure><img src=".gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

All these jobs can **run at the same time**. They may do **different things**, and if they **don’t depend on each other**, they can **complete independently**.

<figure><img src=".gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

The **test job** will **wait for the build job to succeed** before it starts.

* **Steps** are basically **tasks** inside a job.
* We have **layers of nesting**: a workflow has **jobs**, and each job can have **multiple steps**.

In GitHub Actions, we **don’t have stages** like in Jenkins. Instead:

* **Jobs** act like **stages**.
* Everything in the image are **jobs**, not stages.

Workflow visualization is not very important if your GitHub Actions **does only one thing**, but it is useful when it **does multiple things in multiple environments**.

Jobs can be **connected together** or **run in parallel**.

<figure><img src=".gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

Sometimes, **logs don’t clearly show what went wrong** when using an action. To fix this, GitHub Actions provides **action debugging**.

There are **two types of debugging**, and both require **setting some secrets to true**.

* In other CI systems, you might use **environment variables**, but in GitHub Actions, you use **secrets**.

You can debug either **steps** or the **runner**.

It is **recommended to do step debugging first**, because it gives **detailed information about what each action, step, or task is doing**. You can usually get all the information you need from the logs.

This method is used when **simple logging doesn’t solve the problem**.

**`ACTIONS_RUNNER_DEBUG`** allows you to see logs at the **runner level**.

* You will **not see the logs directly** in the workflow output. Instead, GitHub Actions saves log files in the runner’s **`/diagnostics` directory** in the log archive.
* **Important:** Disable this setting when you don’t need it anymore. It **slows down workflow execution** and generates **a lot of logs and noise**.

Other tools for debugging and testing actions:

* **VS Code extension**: Use it to **write and debug your actions**.
* **`nektos/act`**: Allows you to **run workflows locally** on your machine. You must install all **dependencies** your workflow needs for it to run successfully.

If other debugging options fail, `nektos/act` is a **fast alternative** for troubleshooting your actions locally.

<figure><img src=".gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

**Versioning**

* Make a **new version** whenever you make changes to your action.

**Documentation**

* Add a clear **`README.md`** describing **inputs, outputs, examples, and permissions**.
* Include **comments in your YAML workflows** for clarity.

**Test Coverage**

* Make sure your actions **work as expected** before publishing or running in production.
* Write **unit tests**.

**Maintain Metadata**

* Define all the **metadata** for an action and **keep it up to date**.

<figure><img src=".gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

#### GitHub Actions Workflow Structure

Each **workflow** in GitHub Actions has:

1. **A name**
2. **An event trigger** – Something that starts the workflow.
   * Examples: `on: [push]`, `on: [pull_request]`, etc.
3. **One or more jobs** – Define what tasks the workflow will perform.
   * Jobs can be thought of as **stages**.

Each **job** has:

* **A name**
* **A runner**, defined by `runs-on`, which specifies the virtual machine (VM) where the job will run (e.g., `ubuntu-latest`).
* **Steps** – Individual tasks such as checking out code, setting up Node.js, installing dependencies, running tests, etc.

***

#### Example Workflow

**Single job (build) with four steps:**

1. **Checkout the code** → `uses: actions/checkout@v2`
   * Downloads your repository’s code into the runner VM.
2. **Set up Node.js** → `uses: actions/setup-node@v1` with a **matrix Node version**
   * Installs the Node.js version from the matrix value (e.g., `12.x`).
3. **Install dependencies, build, and test** → `npm ci`, `npm run build`, `npm test`
   * Installs packages, builds the project, runs tests, and sends logs to GitHub.
4. **Upload artifacts** → `uses: actions/upload-artifact@master`
   * Saves the build output (files or folders) from the runner to GitHub.
   *   Example:

       ```yaml
       name: webpack artifacts
       path: public/
       ```

       * Uploads everything inside `public/` folder as `webpack artifacts.zip`.

***

#### Matrix in GitHub Actions

A **matrix** allows you to run the **same job multiple times** with different configurations, such as **Node.js versions, operating systems, or Python versions**, all in parallel.

Example:

```yaml
strategy:
  matrix:
    node-version: [10.x, 12.x, 14.x]
```

This will run **three separate build jobs**:

1. Node.js 10.x
2. Node.js 12.x
3. Node.js 14.x

* **Matrix with 1 value → 1 job**
* **Matrix with multiple values → multiple parallel jobs**
* Each job runs on a **separate runner** and **executes the same steps** independently.

***

#### Notes for CI Workflows

* **Checkout action** is built-in: downloads your repo to the runner.
* **Install language runtime** (e.g., Node.js) using an action: ensures the correct version is installed.
* Many times, the required runtime or framework is already installed on the runner, so the action **runs quickly**.
* If you want to **use artifacts from another workflow**, you can use a dedicated action to download them.

The **artifact upload action** is **not meant to be used as input for CD or deployment**.

* It is **advisable to upload the results** of your build or CI into **GitHub infrastructure or a server**.
* You can **download the artifact** later to **check the files** (it will be a `.zip` file).

<figure><img src=".gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

f you **don’t have any linter**, you can use **GitHub Super Linter**.

* A **linter** is a tool that checks your code—whether programming or scripting—to make sure it follows **specific rules** for that language.
* There are linters for **each language**, like JavaScript, Python, C#, etc.

The **power of GitHub Super Linter**:

* With only **five lines of YAML**, you can use it on **any code**—programming, scripting, infrastructure as code, or configuration files.
* This action **scans the content of your repo** after checkout and uses the **appropriate linter** for each file.
* There are **many customization options** available.

**Super Linter** is one of the **most powerful linting tools** you can use to ensure code quality.

<figure><img src=".gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

**CD (Continuous Deployment)** can be used to **deploy to Dev, QA, or Production**.

* **GitHub Actions** can also work as a **CD engine**.

In the image below:

* The **last step of the CI job** (in purple) stores the **images into packages**.

**Deploy to Azure job**:

* Uses `needs` to reference the **previous job**.
* Logs in to **Azure** and **Docker repository**.
* Deploys the **container**.

In other cloud providers, you might have a **different example**—not using containers, just a **normal application**.

* We can use the **upload-artifact action**, which takes files from a folder (e.g., `public/`) and **zips them**.
* The artifact is then **available to any other job** inside the **same workflow**.

<figure><img src=".gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

**Note:** The **download-artifact action** will download the artifact and make it available inside a specified folder (e.g., `public/`).

* If you **don’t specify a directory**, it will be placed in the **root directory** of the workspace.
* You can then use these files in a **deployment job** or in **another job** that performs a different task, such as **analyzing the artifact**.

<figure><img src=".gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

#### Deployment Environments in GitHub Actions

You can **represent the whole environment** in a workflow using **slices**.

* A **slice** is a small, isolated part of an environment.
  * It can be **temporary or limited**, sometimes running on a **specific runner** for testing or deployment instead of the full environment.

**Examples of slices:**

1. **Frontend-only deployment** → Deploying just the web app, not the backend or database.
2. **Feature branch slice** → Temporary environment for a pull request (e.g., `feature-login-page`).
3. **Service slice** → Testing only one microservice (e.g., payment) in isolation.
4. **Runner slice** → Running tests in a Docker container on a specific runner (without touching shared environments).
5. **Namespace slice (Kubernetes)** → Using a separate namespace for one developer or team.

<figure><img src=".gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

#### Deployment Approvals and Protection in GitHub Actions

* You can **define required reviewers**, so that **approvals are needed** before deployment.
  * Often, a **project manager** clicks the **deploy button** to approve deployment on a specific environment.
* With **environment protection rules**, you can:
  1. **Set wait timers** – The action engine will wait for a specified time (seconds or minutes) before deploying.
  2. **Specify allowed branches** – Only workflows from certain branches (e.g., release branches) are allowed to deploy.
  3. **Integrate with third-party APIs** – For example, security scanners or reporting tools can **approve or deny deployment** based on environment checks.



<figure><img src=".gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

hanks to **environments**, we have **deployment logs**.

* These logs can be for a **single environment** or for **multiple environments**.



<figure><img src=".gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

#### Demo: How to Create Environments in GitHub Actions

* Environments can be created in **Settings** at the **repository** or **organization** level.
* Environments are available only in **public repositories** or in your **cloud provider**.

**Steps to create an environment:**

1. Go to the **Environments** section in your repository settings.
2. Click to **create a new environment** and give it a **name**.
   * The name must **match the name** used in your workflow.
3. For each environment, you can specify a **URL**:
   * It can be **hard-coded**, or
   * If you have a deployment step (e.g., using **IaC** to create resources), the **URL or IP** may be created dynamically.
   * You can use an **output parameter from one of your jobs** to fill the field.

<figure><img src=".gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

#### GitHub Actions Runners

A **runner** is a **server (machine)** that runs the jobs in your GitHub Actions workflows.

* Each **job** needs a runner to **execute the steps**.

**GitHub-hosted runners**:

* A **virtual machine** provided by GitHub.
* You **don’t manage it**.
* GitHub automatically **creates a clean environment** every time you run a workflow.

**Examples:**

* `runs-on: ubuntu-latest` → GitHub provides a fresh **Ubuntu VM**.
* `runs-on: windows-latest` → GitHub provides a fresh **Windows VM**.

Runners

<figure><img src=".gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

#### Self-Hosted Runners

A **self-hosted runner** is **your own machine or server**.

* It can be your **laptop**, an **on-prem server**, or a **cloud VM** (like AWS EC2).
* You install the **GitHub Actions runner software** on it.
* You have **full control**: you can pre-install tools, manage resources, and customize everything.
* It is **open source**.

**Important:**

* **Not recommended** for public repositories.
* Only the **runner app** updates automatically; you must manage everything else.

**Use GitHub-hosted runners because:**

* They are **safe** — each workflow runs in a **temporary, isolated VM** created and destroyed by GitHub.
* You **don’t risk exposing your own machine or secrets**.
* GitHub handles **security, updates, and cleanup**.

<figure><img src=".gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

#### Setting Up a Self-Hosted Runner

**1. Download and extract the runner package**

* Create a folder and download the runner from GitHub:

```bash
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.263.0.tar.gz -L \
https://github.com/actions/runner/releases/download/v2.263.0/actions-runner-linux-x64-2.263.0.tar.gz
tar xzf ./actions-runner-linux-x64-2.263.0.tar.gz
```

**2. Configure and authenticate the runner**

* Connect the runner to your GitHub repository using a **registration token**:

```bash
./config.sh --url https://github.com/your-org/your-repo --token YOUR_TOKEN
```

During configuration, it will:

* Connect to GitHub
* Register the runner
* Ask for a **runner name** and optional **labels**
* Start **listening for jobs**

**3. Start the runner**

```bash
./run.sh
```

* You’ll see logs showing **“Listening for Jobs”**, meaning it’s ready to execute workflow jobs.

**Note:** You can have **multiple VMs (runners)** in the same **runner group**.

<figure><img src=".gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

ou can **register multiple self-hosted runners** to allow **parallel job execution**.

* The **number of parallel jobs** is limited by how many self-hosted runners you have available.
* You can create **pools of runners** for different teams, so each team has its own runners.

<figure><img src=".gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

#### Best Practices for Self-Hosted Runners

**1. Create a dedicated user for the Actions runner**

* Set up a **separate system user** (e.g., `github-runner`) to run the Actions service.
* This **improves security** by isolating runner processes from other users or applications.

**2. Enable limited sudo**

* Give the runner user **only the necessary sudo permissions** (e.g., installing dependencies).
* Avoid giving **full admin/root access** to minimize security risks.

**3. Multiple pools with specific tools**

* Organize runners into **different pools** based on the tools or environments they provide.
  * Example: one pool has **Java and Maven**, another has **Node.js or Python**.
* This helps **optimize workflows** and **reduce setup time**.

<figure><img src=".gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

&#x20;

f you **don’t use Docker runners**, your jobs will run **directly on the VM or machine**.

* You need to **manually manage dependencies, tools, and environment setup** on the runner.
* You must ensure the runner has the **correct language runtimes, libraries, and software** installed.
* This approach can be less isolated and may **affect other workflows** running on the same machine.

<figure><img src=".gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>



**GitHub Secrets** are **environment variables** that you can store in a **repository, organization, or environment**.

* They allow you to **keep sensitive information out of your code** instead of hard-coding it.

<figure><img src=".gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

You can **modify GitHub Secrets** in the **same place where you created them**.

A **GitHub token** is a **secret value** used to **authenticate with GitHub** instead of using your username and password.

* It works like a **password for programs and workflows**.

#### sing GitHub Secrets

* **Syntax:** `secrets.<NAME>`
  * The **secret name** is the key, and the value is stored in the **repository, environment, or organization**.

**Example in a workflow:**

```yaml
env:
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

* `secrets` → Contains all secrets defined in the repository, environment, or organization.
* `DB_PASSWORD` → Name of the secret you created in GitHub.

**Using GitHub CLI to manage secrets:**

```bash
gh secret set DB_PASSWORD --body "mySecret123"
```

* The CLI **stores the secret in GitHub**, but does **not pass the value to the environment variable** automatically.

**Limits:**

* **Size:** Each secret can be up to **64 KB**.
* **Number of secrets:** Up to **100 per repository**.

**Forked repositories:**

* By default, secrets are **not passed to workflows triggered by forks**.
* For **private repositories**, you can enable this behavior.

**Security notes:**

* Secrets **cannot be read by apps**.
* You cannot directly **read or print the secret value**, even using the GitHub Actions API.

**Example usage:**

```yaml
name: Use secret
run: echo "Connecting with token..."
env:
  TOKEN: ${{ secrets.MY_TOKEN }}
```

<figure><img src=".gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

**Organization-level secrets** apply to **a group of repositories** within a single organization.

<figure><img src=".gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

There are **three settings for secrets** in GitHub:

1. **Repository-level secrets** – Secrets specific to a single repository.
2. **Environment-level secrets** – Secrets tied to a specific environment within a repository.
3. **Organization-level secrets** – Secrets shared across multiple repositories in an organization.

<figure><img src=".gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

#### Demo

* Try it out on your **GitHub account**.
* **You will not see the secret value** in the repository or settings.
* The workflow can **print the secret value** during execution.
* If your workflow has a `workflow_dispatch` event, it allows you to **start the workflow manually**.

<figure><img src=".gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

You can **run a workflow manually** and add **parameters** in the `workflow_dispatch` event.

* This allows you to **add custom fields** where you can **type or select a value**.
* The value will then be **passed to the workflow** during execution.

<figure><img src=".gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

When you **run the workflow**, you can see it **executing**, but the **secret values are not shown**.

<figure><img src=".gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

Look at the **stars (\*\*\*\*)** — this means the **secret value is hidden**.

* It’s a **security feature** to **prevent secrets from being leaked**.

<figure><img src=".gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

You can **integrate HashiCorp Vault** with GitHub Actions to **manage secrets securely** in your CI/CD workflows.

* It is a **third-party tool**.
* There are **actions available in the GitHub Marketplace** to consume these secrets

<figure><img src=".gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

You can use an **action to connect to a key vault** and retrieve **one or more secrets**.

* If you execute actions related to the key vault **before the steps that need the secrets**, you can reference the secrets using **output variables** from that action.
* Everything you can do from the **user interface** can also be done via **APIs**.
* **Note:** You cannot see the **secret values** directly from the UI.

<figure><img src=".gitbook/assets/image (199).png" alt=""><figcaption></figcaption></figure>

If you talk about **automation parameters**, you can **pass an event name** and reference it as a **client payload** during workflow execution



<figure><img src=".gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

If you **check the actions that are calling it**, you can see how the workflow is **triggered and executed**.

<figure><img src=".gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

If the **condition is matched**, the action will run.

* You can execute a **job or action based on the results** of a previous job or action.

<figure><img src=".gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>

**Coming from the main branch**

<figure><img src=".gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

**Exmaple CI:**

<figure><img src=".gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

&#x20;

* `name`: This is the name of the workflow. You can name it anything you want.
* `on`: This defines the event that triggers the workflow to start.
* `push`: The workflow runs when code is pushed.
* `branches`: It specifies which branch triggers the workflow.
* `jobs`: A workflow can have many jobs.
* This workflow has **one job**, and it has a name.
* The job runs on a **virtual machine with Ubuntu**.
* Then come the **steps** of the job.
* Each step has a `name`.
* `uses` is used to run a **predefined action** that does a specific task, such as setting up the environment or logging in.
* `secrets`: These are secure values stored in the repository, like the **Docker Hub username and password/token**.
* Secrets are used in GitHub Actions to keep sensitive data safe so set them on your github project repo&#x20;
* **Docker token get it from your docker account**&#x20;

<figure><img src=".gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

* **Checkout the code happen to everything outside the the workflow directly**&#x20;

<div data-full-width="true"><figure><img src=".gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure></div>

<figure><img src=".gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

&#x20;

* **Try to Push changes on github**
* **If you want to run the workflow manually from github**&#x20;

<figure><img src=".gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

* The workflow will run again because you push changes
* What if I want to run the workflow that we add in dev but from the main branch but you will not because you can only run the workflow in the main or dev&#x20;
* so let's suppose the workflow that we have in the main branch is old and I push new changes related to that workflow but I want to run it in the main branch
*   &#x20; allows you to run the workflow on any branch manually from github&#x20;

    <figure><img src=".gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>
* Push The changes and submit pull request then run that workflow manually

**Exmaple CD:**

* &#x20;**if we have only ci workflow so you will deploy manually**
* **CI Workflow trigger CD Workflow automatically**&#x20;

<figure><img src=".gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

When you push docker image on docker hub,you need tag the image by username/repo name

But with ECR you tag the image by ECR URI/ECR Repo name

<figure><img src=".gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

To Communicate with the cluster from  The workflow  machine you need kubeconfig to deploy mainfest files on the cluster&#x20;

Download The kubeconfig file&#x20;

<figure><img src=".gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Github actions will get data  from github secrets but There is a problem which is kubeconfig file content because The workflow fails to read it  so we will convert the file content to another format&#x20;

<figure><img src=".gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

**Copy The encoded data to github secrets**&#x20;

