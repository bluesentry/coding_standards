# BSC Analytics Engineering Coding Standards

## Table of Contents

- [Introduction](#introduction)
- [General Coding Standards](#general-coding-standards)
  - [Code Formatting](#code-formatting)
  - [Naming Conventions](#naming-conventions)
  - [Commenting and Documentation](#commenting-and-documentation)
- [Error Handling](#error-handling)
- [Code Organization and Structure](#code-organization-and-structure)
- [Version Control Practices](#version-control-practices)
- [Security Best Practices](#security-best-practices)
- [Performance and Efficiency](#performance-and-efficiency)
- [Language-Specific Standards](#language-specific-standards)
  - [Terraform (Infrastructure as Code)](#terraform-infrastructure-as-code)
  - [JavaScript (Frontend)](#javascript-frontend)
  - [Python (Backend, Scripting, and Notebooks)](#python-backend-scripting-and-notebooks)
- [Cloud-Specific Best Practices (AWS, Azure, GCP)](#cloud-specific-best-practices-aws-azure-gcp)
- [Conclusion and Adherence](#conclusion-and-adherence)

## Introduction

This document defines the coding standards for BSC Analytics engineers. The goal is to ensure our code is consistent,
readable, secure, and maintainable across all projects. It covers general best practices and specific guidelines for
Terraform, JavaScript (frontend), and Python (scripting, backend, Jupyter notebooks). These standards incorporate
industry best practices, including cloud computing considerations for AWS, Azure, and GCP. Client-Specific Standards: If
a client project provides its own coding or style guidelines, engineers must follow those client-specific standards
first. In all other cases, the internal standards in this document apply as a default. The reasoning behind each
standard is explained so that engineers understand why it matters, not just what to do.

## General Coding Standards

Our general standards apply to all languages and projects. Following these guidelines will make our codebase uniform and
easy to collaborate on. Remember, code is read far more often than it is written, so we optimize for readability and
clarity.

### Code Formatting

Consistent formatting makes code easier to read and diff. We use automated formatters wherever possible to enforce
consistency:

* **Indentation:** Use a consistent indent per language (e.g. 2 spaces in Terraform and JavaScript, 4 spaces in Python).
  Never mix tabs and spaces. This uniformity prevents misalignment issues and improves readability.
* **Line Length:** Aim for a maximum of ~80-100 characters per line. Breaking long lines improves code legibility on
  GitHub and side-by-side reviews. If a line exceeds the limit, break it into multiple lines (e.g. long function
  arguments or expressions).
* **Braces & Spacing (Language-specific):** In JavaScript, adopt K&R style bracing – opening braces on the same line as
  declarations or control statements – and include spaces around operators and after commas for readability. In Python,
  rely on newlines and indentation (no braces) to delineate blocks, per PEP 8. In Terraform’s HCL, the terraform fmt
  tool will handle brace placement and alignment automatically.
* **Automatic Formatting:** Leverage formatter tools to avoid manual format errors. For example, run `terraform fmt` on
  all Terraform files to apply HashiCorp’s recommended style. Use Prettier for JavaScript to auto-format code, and
  Black (or autopep8) for Python. These tools enforce consistent indentation, spacing, and line breaks, so that all code
  looks the same regardless of who wrote it. Consistent style helps developers focus on what the code does rather than
  how it’s styled, and prevents stylistic debates.
* **Trailing Spaces/Newlines:** Remove any trailing whitespace. Ensure files end with a newline character. These minor
  details reduce unnecessary diffs and align with POSIX standards.
* **Example – Formatting:** Below is a correctly formatted snippet in each language:

  ```hcl
  
  # Terraform example (2-space indent, aligned braces by terraform fmt)
  resource "aws_s3_bucket" "prod_data_bucket" {
    bucket = "prod-app-data-bucket"
    acl    = "private"
  
    tags = {
      Name        = "ProdAppData"
      Environment = "Production"
    }
  }
  ```

  ```js
  // JavaScript example (2-space indent, spacing, semicolons via Prettier)
  function addUser(name, usersList) {
    if (!name) {
      throw new Error("Name is required");  // Proper spacing around operators
    }
    usersList.push(name);
  }
  ```

  ```python
  # Python example (4-space indent, line wrap at 79 chars)
  def calculate_average(values):
      total = sum(values)
      return total / len(values)  # simple expression fits on one line
  
  def long_function_name(param_one, param_two, 
                         param_three, param_four):
      # Indented continuation for long function signature
      return param_one + param_two + param_three + param_four
  ```

* _Rationale:_ A consistent formatting style across the team means any engineer can read any module without reorienting
  to a new style. Automated formatting also catches common mistakes and reduces nitpicks in code review. In short,
  clean, well-formatted code is more approachable and less error-prone.

### Naming Conventions

Choosing clear, consistent names for variables, functions, and other identifiers makes the code self-documenting. Use
naming styles appropriate to each language, but follow these general principles:

* **Be Descriptive:** Names should indicate what the item represents or does. Avoid single-letter or cryptic names (
  except simple loop indices like `i, j` when context is clear). For example, `total_sales_q1` is better than `x` for a
  variable holding quarterly sales.
* **Consistency:** Follow the customary case conventions of the language for each kind of identifier. This helps others
  instinctively understand the role of an identifier (e.g., a capitalized name in Python is likely a class). Refer to
  the table below for a summary of naming styles:

| **Identifier**         | **Terraform** (HCL)                                                                                    | **JavaScript** (Frontend)                                                                                                                                               | **Python**                                                                                       |
|------------------------|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| **Variables**          | lower_snake_case (e.g. `instance_count`)                                                               | camelCase (e.g. `userName`)                                                                                                                                             | lower_snake_case (e.g. `user_name`)&#8203;:contentReference[oaicite:4]{index=4}                  |
| **Functions/Methods**  | N/A (no user-defined functions; use modules)                                                           | camelCase (e.g. `calculateTotal()`)                                                                                                                                     | lower_snake_case (e.g. `calculate_total()`)&#8203;:contentReference[oaicite:5]{index=5}          |
| **Classes/Components** | N/A (use modules instead)                                                                              | PascalCase (e.g. `UserProfile` component)                                                                                                                               | PascalCase (CapWords, e.g. `DataProcessor`)                                                      |
| **Constants**          | Terraform variables treated as config (use snake_case); or use all caps in TF locals if truly constant | UPPER_SNAKE_CASE for constants (e.g. `MAX_RETRIES`)                                                                                                                     | UPPER_SNAKE_CASE (e.g. `MAX_RETRIES`)&#8203;:contentReference[oaicite:6]{index=6}                |
| **Files/Modules**      | lowercase, use underscores or hyphens (e.g. `networking.tf`, `variables.tf`)                           | lowercase or kebab-case for filenames (e.g. `api-client.js`, `user-service.js`); PascalCase for React component files (e.g. `UserProfile.jsx`, matching component name) | lowercase, underscores allowed (e.g. `data_processor.py`); Python packages lowercase (no spaces) |

* **Terraform:** Use lowercase with underscores for resource names, input variables, and outputs. For example, a
  variable might be named `db_instance_size`. Resource labels (the identifier after resource type) should also be
  lowercase with underscores (e.g. `aws_instance` resource named `web_server`). Use prefixes/suffixes in resource names
  to convey scope or environment (e.g. include `dev` or `prod` in resource names). This makes it easy to identify
  resources by name (for instance, `prod_web_server` vs `dev_web_server`).
* **JavaScript:** Use camelCase for variables and function names (e.g. `let responseData = fetchData();`). Use
  PascalCase for class names or React components (`class DataService {}` or `function MyComponent() {...}`). Constants
  that are truly constant (e.g. configuration values) can be written in ALL_CAPS with underscores (
  `const API_URL = "...";`). Filenames should be consistent – many projects use lowercase with hyphens (e.g.
  `user-profile.js`), especially for modules. Keeping names consistent in style prevents confusion (e.g., knowing that
  `UserProfile` is a component vs `userProfile` being a variable).
* **Python:** Follow PEP 8 conventions strictly. Function and variable names are lowercase with underscores. **Classes
  use CapitalizedWords**. Module (file) names are lowercase (underscores if needed for readability). Constants (
  module-level variables meant as constants) use **ALL_CAPS**. For instance, `MAX_OVERFLOW = 50` would denote a
  constant. Internal or "private" names start with a single underscore (e.g. `_helper_function`) to indicate limited
  usage. These conventions make Python code immediately familiar to any Python developer.

_Rationale:_ A uniform naming scheme makes it easier to understand code across the team. If everyone uses the same
patterns, one can infer the role of an identifier from its style. Descriptive names reduce the need for excessive
comments because the code explains itself. Consistent naming also prevents bugs (e.g., avoiding mix-ups in
case-sensitive contexts) and aids searchability (imagine trying to grep for a variable if everyone named it
differently). In short, good naming is key to code readability and maintainability.

### Commenting and Documentation

Write comments and documentation to clarify the intent of the code, not to restate the obvious. Use comments to explain
why something is done if it’s not evident from the code itself. Key guidelines include:

* **Self-Documenting Code First:** Strive to make your code self-explanatory through clear naming (as above) and simple
  structure. Reserve comments for insights that the code alone cannot convey (e.g. complex algorithms, workarounds for
  known issues, important business rules).
* **Keep Comments Up-to-Date:** Outdated comments that contradict the code are worse than no comments at all. Always
  update or remove comments if the code changes. Inconsistent comments can mislead readers and lead to errors, so
  accuracy is crucial.
    * **Comment Style:** Use language-specific conventions:

        * _Python:_ Use docstrings (triple-quoted strings) to document all public modules, functions, classes, and
          methods. A docstring at the top of a function should describe its purpose, parameters, return values, and
          exceptions raised. For example:

          ```python
          def fetch_data(url: str, timeout: int = 5) -> dict:
            """Fetch data from the given URL within the specified timeout.
    
            Args:
                url (str): The endpoint to retrieve data from.
                timeout (int, optional): Timeout in seconds. Defaults to 5.
    
            Returns:
                dict: Parsed JSON data from the response.
    
            Raises:
                ValueError: If the URL is invalid.
                TimeoutError: If the request exceeds the timeout.
            """
            # ... function implementation ...
          ```
          Non-public (internal) functions can have a brief comment instead of a full docstring. Inline comments in
          Python start with `#`. Use them sparingly and keep them short, aligned with code they reference.

        * _JavaScript:_ Use JSDoc-style comments for functions and complex logic, especially in larger front-end
          codebases. For example, a function can be documented as:

          ```js
          /**
          * Calculates the factorial of a number.
          * @param {number} n - A non-negative integer.
          * @returns {number} The factorial of n.
          * @throws {Error} If n is negative or not an integer.
          */
          function factorial(n) {
           if (n < 0 || !Number.isInteger(n)) {
             throw new Error("Input must be a non-negative integer.");
           }
           // ... calculation ...
          }
          ```

          For inline comments, use `//` for single line or `/* ... */` for multi-line. Do not comment out large blocks
          of code; remove dead code instead (version control can retrieve old code if needed).

        * _Terraform:_ Since Terraform configurations are declarative, they usually need less commenting line-by-line.
          However, always include a README for each Terraform module explaining usage. Within `.tf` files, use the `#`
          comment for any non-obvious configuration. Additionally, provide descriptions for variables and outputs in the
          `variables.tf` and `outputs.tf` files – these act as documentation for anyone using the module. For example:

          ```hcl
            variable "instance_count" {
               type        = number
               description = "Number of EC2 instances to launch for the web tier"
               validation {
                 condition     = var.instance_count > 0
                 error_message = "instance_count must be a positive number"
               }
            }
          ```
          The `description` explains the variable’s purpose, and even the validation error message guides the user. This
          is preferred over lengthy comments.

    * **Write Complete Sentences:** Especially for longer comments or docstrings, write in clear, complete sentences (
      and in English). This ensures the documentation is professional and understandable. For short inline comments,
      phrase can be fragmentary but should still be clear (e.g. `# compensate for border offset`).
    * **Avoid Redundant Comments:** Don’t state the obvious. For example, `i = i + 1 # increment i` is not useful.
      Instead, explain why something is done if it’s not obvious.
    * **Update Documentation:** If you find an important gotcha or assumption in the code, document it. Conversely, if
      code is refactored and a comment becomes irrelevant, remove it. Treat documentation as part of the codebase that
      requires maintenance.
    * **Rationale:** Good commenting practices ensure that anyone reading the code (including your future self) can
      quickly grasp complex sections and usage of components. The emphasis is on clarity and truthfulness: accurate
      comments reduce onboarding time for new engineers and help in debugging by clarifying intentions. Well-documented
      public APIs (functions, classes, modules) make reuse easier and reduce misuses. By enforcing documentation of
      important elements, we also encourage engineers to think through design (since writing a docstring often reveals
      if an interface is clunky). In summary, commenting and documentation are about making the code communicate as
      clearly as possible to other humans.

## Error Handling

Robust error handling is critical for building reliable systems. Code should anticipate possible error conditions and
handle them gracefully instead of crashing or silently failing:

* **Never Ignore Failures:** Check return values and catch exceptions where errors are expected or possible. Do not just
  assume an operation will always succeed. If an error can occur, handle it – either by retrying, providing an error
  handling functio to correct the problem, presenting an error message to the user, or cleanly shutting down. For example,
  if a file read might fail, wrap it in a try/except (  Python) or try/catch (JS) block. Always log error messages but
  just logging is almost never sufficient to bring awareness to the problem, as the error message may go undetected in a log.
* **Catch Specific Exceptions:** When catching exceptions, catch the most specific exception class that you can handle,
  rather than a blanket catch-all. This prevents unrelated errors from being accidentally swallowed. For instance, in
  Python if you expect a `KeyError`, catch that specifically instead of a generic `Exception`. Likewise, in JavaScript,
  catch specific error conditions if you can differentiate them. This approach is emphasized in Google’s standards:
  _“Always catch the most specific type of Exception, instead of a more general one.”_ A specific catch lets you handle
  that case properly while letting unexpected issues bubble up. Do allow for the outermost control block of your program
  to capture more generic exceptions; this enables resource cleanup and prevents silent failure.
* **Provide Useful Error Messages:** When throwing or logging errors, include context to aid debugging. For example,
  `"Invalid user ID provided to getProfile"` is more helpful than `"Error in getProfile"` with no details. In Terraform,
  use the `error_message` in `validation` blocks to guide the user as shown above. Clear messages make it easier to
  diagnose issues in testing and production.
* **Fail Fast, Fail Safe:** If a function receives bad input, it should quickly raise an error (with an informative
  message) rather than proceeding in a bad state. This “fail fast” principle localizes errors to their source.
  Conversely, in long-running systems or when interacting with external services, use safe fallbacks – e.g., if a config
  file is missing, maybe load defaults and log a warning rather than crashing entirely (depending on context).
* **Logging and Monitoring:** Where appropriate, log errors along with the context (but avoid logging sensitive data).
  In backend Python services, use the `logging` library to record exceptions and events (with stack traces on debug
  level if needed). In front-end JS, you might not log to console for every handled error (to avoid exposing internals),
  but ensure the UI can inform the user. For example, if an API call fails, catch the error and display a user-friendly
  message like “Unable to load data, please try again.”
* **No Silent Failures:** Do not write code that catches an exception and then does nothing (or just a comment like
  `// ignore`). If truly an error is safe to ignore, at minimum add a comment explaining why it's safe to ignore.
  Ideally, even ignored errors should be logged or counted (to detect if they happen frequently).
* **Resource Cleanup:** When an error occurs, ensure the code doesn't leak resources. Use `finally` blocks or context
  managers (`with` in Python) to release files, network connections, or locks, even if an error happens. In front-end,
  ensure any loading indicators or partial UI updates are reset if an error interrupts the normal flow.
* **Example – JavaScript Error Handling:**

    ```js
    try {
       const data = await fetchData(url);
       renderData(data);
    } catch (e) {
       console.error("Failed to fetch data:", e.message);  // Log the error for debugging
       showErrorToUser("Could not load data. Please check your connection.");  // User-facing message
    }
    ```

  In this example, we catch any error from `fetchData`. We log a detailed message to the console (which could be wired
  to a monitoring service in a real app), and we also provide feedback to the user. We do not simply swallow the
  exception without explanation.

* **Example – Python Error Handling:**

    ```python
    def load_config(path):
       try:
           with open(path) as f:
               return json.load(f)
       except FileNotFoundError:
           logging.error(f"Config file not found: {path}")
           raise  # re-raise, maybe the caller will handle this
       except json.JSONDecodeError as e:
           logging.error(f"Config file {path} is not valid JSON: {e}")
           # Return a default config or re-raise depending on criticality
           return {}
    ```

  Here we handle two specific exceptions: file not found and JSON parse error. Each is logged with context. The
  `FileNotFoundError` is re-raised after logging (since higher-level logic might want to handle that as fatal), whereas
  a JSON error results in an empty default config (assuming the application can proceed with defaults).

_Rationale:_ Proper error handling ensures our software is reliable and user-friendly. By catching errors, we prevent
uncontrolled crashes (which could lead to downtime or poor user experience). Using specific exceptions and meaningful
messages simplifies debugging and maintenance. It’s also a matter of professionalism: robust error handling shows we
anticipated issues and built the system to handle them gracefully. In production, good error handling paired with
logging/monitoring is our early warning system for problems. Overall, these practices reduce bugs, improve stability,
and make support easier.

## Code Organization and Structure

Organizing code in a logical manner makes the project easier to navigate and scale. Key guidelines for structuring
codebases:

* **Modularity:** Follow the single responsibility principle – each module, class, or function should have a clear
  purpose. Break up large files or functions into smaller, focused units. For example, in a Python project, separate
  data processing logic from API request logic into different modules. In a front-end project, keep independent
  components in separate files rather than one huge script. Modular code is easier to test, reuse, and replace if
  needed.
* **Project Structure:** Adopt a standard project layout:

    * _Terraform:_ Arrange Terraform configurations into directories by environment or component. For instance, you
      might have a top-level `prod/` and `dev/` directory, each with Terraform modules or configurations for that
      environment. Within a module, use a standard file naming: `main.tf` for main resources, `variables.tf` for inputs,
      `outputs.tf` for outputs, and `README.md` for documentation. A consistent structure makes it easy to find things (
      inputs, outputs, etc.) and supports reusability.
    * _Python:_ Use a package structure for projects (a root package directory with an `__init__.py`). Within the
      package, group related functionality into subpackages or modules. For example, keep database models in one module,
      API routes in another, utility functions in a `utils.py`, etc. If writing scripts, consider placing common code in
      libraries and having only the minimal script in `if __name__ == "__main__":` block to call into the library. This
      way, logic can be reused and tested. Also maintain a `requirements.txt` or `pyproject.toml` for dependencies at
      the project root.
    * _JavaScript:_ For front-end, structure by feature or component type. A common pattern is to have folders like
      `components/`, `services/`, `utils/`. In a React project, for instance, each major UI component might reside in
      its own folder with its JS(X) file and possibly related CSS/Assets. Ensure that index files or module exports are
      used to present a clean interface for each module. Use a consistent file naming scheme as noted (like
      `MyComponent.jsx` or `my-component.jsx`, according to team preference). Keep tests alongside code (e.g.,
      `component.test.js` next to `component.js`) or in a parallel tests directory, so it's easy to find `tests` for a
      given piece of code.
    * _Jupyter Notebooks:_ Notebooks can become disorganized if not managed. Keep notebooks focused on specific analysis
      or tasks. If a notebook is for data exploration, it may not need to be in the production code repository. For
      production or shared notebooks, structure them with clear sections (use Markdown headings within the notebook) and
      ensure any reusable logic is moved to Python modules. This avoids duplicate code and allows version control of
      logic outside the notebook. Also, name notebooks descriptively (e.g., `Data_Cleaning_2025Q1.ipynb`) and avoid
      spaces in filenames (prefer underscores). Consider using Jupytext or similar tools if notebooks are a significant
      part of the workflow, to allow diffing changes.

* **Separation of Concerns:** Separate code by concern. For example, in a web app, separate presentation (UI) from
  business logic from data access. In Terraform, separate networking resources from compute resources via modules. This
  makes it possible to modify one aspect (say, swap database technology) without rewriting the entire system.
* **Avoid God Objects/Files:** No single file should define an overwhelming portion of the system. If you find a file
  growing too large (hundreds of lines with multiple classes), consider refactoring into smaller pieces. Similarly,
  avoid global variables or giant classes that do too much.
* **Use of Libraries vs Custom Code:** Organize code to leverage libraries/frameworks appropriately. For example, if
  using a web framework (Flask/Django for Python or a UI framework for JS), structure code in the conventional way that
  framework expects. Follow their recommended project layout (which new engineers can quickly recognize).
* **Internal Package/Module Documentation:** At the top of each major module or package, it can be useful to have a
  comment or docstring describing the module’s purpose. This acts as an inline documentation of the code organization
  itself.

_Rationale:_ A well-organized codebase means developers spend less time searching for code or untangling dependencies,
and more time building features. Consistency in structure (especially across projects) means an engineer familiar with
one project can quickly orient themselves in another. By enforcing modularity, we make testing easier (small pieces can
be tested in isolation) and enable parallel development (different people can work on different modules with minimal
merge conflicts). Logical separation also reduces the risk that a change in one area inadvertently breaks another. In
essence, good code organization is vital for team productivity and long-term scalability of the codebase.

## Version Control Practices

All code (including infrastructure code and notebooks) must be checked into version control (we use Git) to track
changes and collaborate effectively. Adhere to the following practices:

* **Frequent Commits with Clear Messages:** Commit your work often, with each commit focusing on a logical chunk of
  change (e.g., a bug fix, a new function, or a refactor). Write descriptive commit messages that explain what the
  change does and why, if not obvious. A good commit message might be:
  `fix: handle null values in user input validation` rather than `update code` or `bug fix`. This helps others (and
  future you) understand the history of changes. Consider using a structured format like Conventional Commits (e.g.,
  prefix with `feat:`, `fix:`, `refactor:`) if the project follows that, as it can integrate with release tools.
* **Branching Strategy:** Use feature branches (and similarly, bugfix or hotfix branches) rather than committing directly
  to main/master branch. For example, create a branch feature/login-oauth for implementing OAuth login. This isolates
  your work and makes code review and integration safer. Merge into the main branch via Pull Requests (PRs) after
  review.
* **Pull Request Reviews:** All significant changes should be code-reviewed by at least one other engineer. This practice
  catches bugs, enforces standards, and spreads knowledge. When reviewing, ensure that the code adheres to this
  standards document (formatting, naming, etc.) and address any deviations. PR discussions should be constructive and
  focused on the code.
* **.gitignore and Sensitive Files:** Use .gitignore to exclude files that should not be in version control. This
  includes:

  Compiled or generated files (e.g. Python `__pycache__` directories, `.pyc` files, Node `node_modules/`, build
  artifacts).
  Credentials or environment config (do not commit AWS keys, DB passwords, etc., in files). For Terraform, ignore local
  state files (`*.tfstate` and backups), as well as Terraform variables files that contain secrets. For Python/JS,
  ignore `.env` files or config files that have secrets.
  Large data files or dependencies – if needed, use artifact storage or data versioning tools instead.

* **Version Control for Notebooks:** For Jupyter notebooks, be mindful that outputs (graphs, data) can make diffs large.
  It's often best to clear outputs before committing, or use tools to strip output. Alternatively, convert critical
  notebooks to scripts or markdown+code (using tools like Jupytext) to better track changes. Always commit the notebooks
  alongside any code changes they depend on, to keep repository consistent.
* **Tagging and Releases:** In projects that deliver artifacts (packages, releases), use Git tags or releases to mark
  version boundaries. For example, tag a commit as v1.0.0 for a release. This helps in rollback and in communicating
  version info. Terraform module versions can be managed via tags as well, allowing you to reference specific versions
  in a Terraform Registry or in modules sources.
* **Merge Strategy:** Prefer squash-and-merge or rebase to keep the main branch history clean (this is team preference,
  but consistency is key). Avoid merge commits that clutter history unless using a specific gitflow that dictates them.
* **Resolve Conflicts Cleanly:** When merging branches, resolve conflicts in a way that preserves standards. After
  resolving a conflict, run formatters/linters again – sometimes conflict markers or manual fixes can introduce style
  issues or bugs.
* **Continuous Integration (CI):** Our repos should include CI workflows that run on each PR/commit to main. These
  should at minimum run automated tests, linters, and format checks. The CI is an extension of version control, ensuring
  that nothing gets merged that breaks the build or violates basic standards. For example, a CI job might fail if
  terraform fmt finds differences (indicating someone didn’t format), or if ESLint finds a severe warning. This
  automation reinforces the standards and catches issues early.

_Rationale:_ Version control is the backbone of collaboration. By committing frequently with clear messages, we create a
detailed history that aids transparency and debugging. A strong branching and review culture ensures code is reviewed
for quality and adherence to standards before integration. Ignoring the right files keeps the repo clean and secure (
preventing secret leaks). These practices also enable continuous deployment workflows and quicker onboarding (a new
developer can easily see what was changed when and why). In essence, disciplined version control practices lead to
higher quality code and smoother teamwork.

## Security Best Practices

Security must be considered in every step of coding to protect client data, intellectual property, and ensure compliance
with regulations. Some universal security coding standards:

* **No Hard-Coded Secrets:** Never hard-code credentials, keys, or passwords in source code. This includes API keys,
  database passwords, cloud provider keys, etc. Use secure configuration: environment variables, vault services, or
  Terraform variables (with proper state backends) to handle secrets. For example, in Python use os.environ.get("
  DB_PASSWORD") or a library like python-dotenv, and in JS use build system or environment injection. For Terraform,
  leverage secret management (e.g., AWS SSM Parameter Store or Vault provider) instead of putting plain secrets in .tf
  files. This prevents accidental exposure of secrets in repositories and allows rotation without code changes.
* **Input Validation and Sanitization:** Any input that enters the system (user input, external API data, etc.) should
  be treated as untrusted. Validate inputs thoroughly: check types, ranges, and formats. In web development, guard
  against injection attacks (SQL injection, XSS). For Python, if constructing SQL queries, use parameterized queries
  rather than string concatenation. In JavaScript, be cautious with innerHTML or DOM APIs that can introduce HTML from
  users; prefer safer APIs (or properly escape content) to prevent XSS. If our code writes to a shell command or uses OS
  calls, escape or avoid unsanitized input to prevent command injection.
* **Use HTTPS/Encryption:** Always use secure protocols to communicate with external services. For example, use https://
  endpoints for APIs. In Terraform, default to enabling encryption on resources (S3 buckets with SSE, RDS with storage
  encryption, etc.). In Python or JS, if dealing with sensitive data at rest, consider encrypting it or using services
  that do (like storing secrets in a KMS). Encryption in transit and at rest is a default expectation for security.
* **Least Privilege:** When writing code that interacts with cloud resources or databases, ensure it uses the minimum
  necessary permissions. For example, if a Python script uses AWS, have it assume a role or user with limited IAM
  policy (just what it needs). The code should not assume root-like access to everything. Similarly, database
  connections should use a user account with limited rights (e.g., only to specific schemas). This way, even if the code
  is compromised, the blast radius is limited.
* **Dependency Management:** Keep libraries and packages up to date, especially when security patches are released. Use
  tools (like npm audit for Node, pip-audit or safety for Python, pip install --upgrade regularly) to identify known
  vulnerabilities in dependencies. If a library is no longer maintained and has issues, find alternatives. Pin versions
  in requirements files or lockfiles to known secure versions and update them regularly.
* **Secure Coding Practices:** Avoid patterns known to be risky. For instance, do not use eval() in JavaScript (unless
  absolutely necessary and input is controlled) as it can execute arbitrary code. In Python, avoid exec() or eval() on
  untrusted input. Be careful with serialization/deserialization (pickles in Python can execute code on load, so do not
  load pickles from untrusted sources).
* **Logging of Sensitive Data:** When adding logging, never log sensitive information in plaintext (passwords, credit
  card numbers, personal data). Mask or omit such details. Similarly, scrub sensitive data from error messages that
  might surface to users or logs.
* **Static Analysis & Linters:** Utilize tools to catch security issues. For Python, Bandit can find common security
  flaws (like use of eval or weak hashing). For Terraform, use Checkov or tfsec to catch insecure configurations (like
  open security groups, etc.) as part of code review or CI. ESLint has plugins for security to catch things like use of
  eval or insecure cookies in JS. These automated checks are an extra line of defense for code review.
* **Code Reviews for Security:** During peer reviews, explicitly consider security implications. For example, if code is
  handling a file upload, ask “What if a malicious file is uploaded?” If code constructs an OS command, check how inputs
  are handled. Bringing a security mindset to code reviews helps catch issues early.
* **Example – Secure Configuration Usage:**

  _In Python, instead of:_

    ```python
    API_KEY = "ABCD1234"  # Hard-coded (BAD)
    ```

* do:

    ```python
    API_KEY = os.environ.get("API_KEY")
    if API_KEY is None:
        raise RuntimeError("API_KEY not set in environment")

    and document in README that API_KEY must be provided via environment.
    ```

  _In Terraform, rather than embedding keys:_

    ```hcl
    variable "db_password" {
             type = string
             description = "Database password"
    }
    # and then pass it securely, e.g., via environment TF_VAR_db_password or a secret store
    ```

  Possibly use a provider integration with a vault, so the secret is never stored in state in plain text. If it must be
  in state, use state encryption and restrict state access.

_Rationale:_ Security is a cross-cutting concern that cannot be an afterthought. A single hard-coded secret or
unvalidated input can lead to catastrophic breaches or failures. By embedding security into our coding standards, we
ensure it's part of the development process for every engineer. This reduces the likelihood of vulnerabilities making it
into production. It also demonstrates to our clients that we take protecting their data seriously. In the era of
frequent cyber attacks, following these practices is essential risk management. Moreover, many industries have
compliance requirements (GDPR, HIPAA, etc.) that mandate these kinds of precautions. Secure coding practices protect our
clients, end-users, and our own company’s reputation.

## Performance and Efficiency

While writing code, keep in mind the performance implications and strive for efficient solutions, especially in
data-heavy analytics contexts and user-facing frontends. Guidelines for performance:

* **Efficient Algorithms:** Choose appropriate algorithms and data structures for the job. An O(n^2) solution on large
  data sets can be untenable where an O(n) or O(n log n) exists. For example, if searching through data, consider using
  sets/dictionaries for O(1) lookups rather than lists for O(n) scans. In Python, prefer built-in functions and
  libraries (which are often C-optimized) for heavy computations (e.g., use NumPy/pandas for large data operations
  instead of pure Python loops). In JavaScript, be mindful of algorithmic complexity in loops, especially in hot code
  paths that run frequently (like inside animations or frequent events).
* **Don’t Prematurely Micro-Optimize:** Readability and maintainability still come first. Write clear code and then
  optimize the bottlenecks. Use profiling tools (e.g., Python’s cProfile, Chrome DevTools Performance monitor) to find
  where the program spends time. Focus optimization efforts there. Avoid “clever” tricks that sacrifice clarity for
  minor performance gains unless necessary (and if used, comment why).
* **Memory Usage:** Be mindful of memory in your code. For Python, large lists or data structures can consume a lot of
  memory; consider generators or streaming processing for large datasets (process items one by one or in chunks rather
  than all at once in memory). In JavaScript, avoid holding onto large DOM elements or arrays if not needed, and be
  careful with global variables that might hang around. Free resources when done (in languages with manual memory
  management, but for Python and JS, mainly ensure references are dropped so GC can reclaim).
* **Front-End Performance:** In the frontend context, performance affects user experience directly. Best practices
  include:

    * Minimize DOM reflows and repaints: batch DOM updates (e.g., use document fragments or frameworks that batch
      updates). Excessive direct DOM manipulation inside loops can slow down the UI.
    * Use debouncing/throttling for events that fire rapidly (scroll, resize, keyup) to limit how often heavy handlers
      run.
    * Lazy-load images or data when possible, so initial load is faster.
    * Use bundling and minification for assets (webpack or similar) to reduce file size and number of HTTP requests.
      Leverage CDN and caching for static assets.
    * For React/Vue, follow their optimization guides (use keys on lists, avoid unnecessary re-renders, use memoization
      or shouldComponentUpdate equivalents when needed).

* **Backend Performance and Scalability:** In Python backends or scripts:

    * If concurrency is needed, consider multi-threading or async IO for I/O-bound tasks, and multiprocessing for
      CPU-bound tasks (or offload heavy CPU tasks to C extensions or optimized services).
    * Use caching where appropriate (memoize results of expensive computations, or cache database queries results if
      they are reused, respecting cache invalidation).
    * Database queries: avoid N+1 query problems (fetch related data in bulk), add indices for frequently queried
      fields, etc. Efficient database access is a common performance area.
    * In data engineering pipelines, prefer bulk operations to many small ones (e.g., one bulk insert vs thousands of
      single inserts).

* **Cloud Cost and Performance:** In cloud scenarios, performance ties to cost (faster code can mean less CPU time
  billed, etc.). A Lambda function that runs in 100ms costs half as much as one that runs in 200ms for the same memory
  size. Efficient code can let us use smaller instance sizes or handle more load per instance. So, optimizing
  performance can have direct cost savings for clients. Always consider the trade-off between development time and
  runtime performance – for critical pieces, it's worth optimizing.
* **Monitoring and Profiling:** Include performance monitoring in long-running applications. For web services, track
  response times and throughput. Identify slow endpoints and investigate. Use profiling in non-production to catch slow
  spots. Sometimes a small code change (like using a set instead of list membership checks) can drastically improve
  performance in a tight loop.
* **Example – Python Performance Improvement:**

  _Before (inefficient):_

    ```python
    # Summing even numbers from a large list
    total = 0
    for num in numbers_list:      # iterating in Python
        if num % 2 == 0:
            total += num
    ```

  _After (more efficient using Python built-ins):_

    ```python
    total = sum(num for num in numbers_list if num % 2 == 0)
    ```

  The second version uses a generator expression and built-in sum, which is not only more concise but can be faster in
  CPython due to internal optimizations. For even larger data, one might use NumPy for vectorized operations, which are
  considerably faster.

  _Example – JS Performance (Debounce):_

    ```js
    // Before: calling an API on every keystroke (inefficient)
    searchInput.addEventListener('input', (e) => {
      fetchResults(e.target.value);  // This might fire many times per second
    });

    // After: debounced input handling
    let timeout;
    searchInput.addEventListener('input', (e) => {
      clearTimeout(timeout);
      timeout = setTimeout(() => {
        fetchResults(e.target.value);
      }, 300);
    });
    ```

  In the improved version, fetchResults is called only after the user stops typing for 300ms, reducing the number of API
  calls and improving responsiveness.

_Rationale:_ Paying attention to performance ensures that our solutions remain responsive and scalable as data volumes
or user counts grow. Clients expect our analytics pipelines to handle large data efficiently and our front-end
applications to be snappy. By following efficient coding practices, we avoid costly rewrites later when a slow piece
becomes a bottleneck. However, the guidance is balanced: don’t obfuscate code for micro-optimizations—maintainability is
important. The key is to be aware of potential performance pitfalls (like nested loops over big data, or un-throttled
events) and address them in design or via refactoring once identified. In cloud contexts, efficient code can
significantly reduce running costs, which is a tangible benefit for our clients. Thus, performance-conscious development
leads to software that delights users and optimizes resources.

## Language-Specific Standards

In addition to the general standards above, here are specific guidelines and best practices for each of the primary
languages/tools we use:

#### Terraform (Infrastructure as Code)

Terraform is used to define cloud infrastructure in code. Maintaining high standards in Terraform ensures our cloud
environments are reproducible, safe, and scalable.

* **Latest Version:** Always use the latest stable version of Terraform (and relevant providers) in new projects, unless
  there’s a specific need to pin to an older version. Specify the `required_version` in configuration to ensure
  consistency across team machines and CI.
* **Formatting and Style:** Always run `terraform fmt` before committing Terraform code to automatically format it
  according to the official style. This standardizes indentation (2 spaces) and alignment. Also run `terraform validate`
  to catch syntax errors or misordered arguments. Do not disable these; they ensure your code is syntactically correct
  and standardized.
* **Linting:** Use TFLint to catch common Terraform issues and ensure best practices are followed. For example, TFLint can
  warn if you use deprecated syntax or if an AWS resource is misconfigured. We also integrate Checkov (or similar
  security scanners like tfsec) to detect security and compliance issues (like open security group ports, unencrypted
  storage) early. These tools should be run in CI and preferably as pre-commit hooks.
* **Modules and DRY Principle:** Structure Terraform code into reusable modules. Avoid copy-pasting resource definitions
  for similar components – instead, factor them into a module that can be parameterized. For instance, if multiple
  projects need an S3 bucket with standard settings, create an `s3_bucket` module. Modules promote DRY (Don't Repeat
  Yourself), reduce errors, and make large configurations manageable. Organize modules in a modules/ directory or
  separate repos as appropriate. Each module should be self-contained with its `variables.tf`, `outputs.tf`, and
  `README.md` documenting usage.
* **Environment Segregation:** Keep configurations for different environments (dev, staging, prod, etc.) separate. Use
  either separate workspaces or separate directories (or even separate state backends) for each environment. This
  prevents changes intended for one environment from accidentally affecting another. Name resources with environment
  identifiers (prefixes/suffixes) so that, for example, a production database instance and a staging one are distinct
  and easily identifiable.
* **Naming Conventions:** Follow a consistent naming scheme for Terraform resources:

    * Resource names (within the code) should be descriptive (e.g., `aws_instance "web_server"`). Use underscores to
      separate words.
    * When naming actual cloud resources (like the name of an AWS EC2 or S3 bucket), incorporate meaningful
      prefixes/suffixes such as project name, environment, and component. For example, an S3 bucket for app logs in dev
      might be named `dev-app-logs-bucket`. This makes it easier to identify resources in the cloud provider console.
    * Use tags/labels on cloud resources to encode metadata like environment, project, owner, etc. For AWS/Azure/GCP,
      ensure our Terraform code attaches tags/labels (like `Environment = Dev` or similar) to all taggable resources.
      Tags aid in management, cost allocation, and housekeeping.

* **Variables and Outputs:** Define all input variables in `variables.tf` with types and descriptions. Every variable
  must have a type (no untyped variables), to avoid type mismatch errors and improve clarity. Provide default values
  only when it makes sense (non-sensitive, truly optional inputs). Use the validation block for variables to enforce
  basic rules (as shown in the example earlier for positive numbers). Document any sensitive variables (mark
  `sensitive = true` for those so Terraform doesn't log them). Likewise, define `outputs.tf` for any values to export (
  like a database endpoint or VPC ID) and give them descriptions. These descriptions are used by Terraform doc
  generators to produce documentation.
* **State Management:** Use remote state (e.g., Terraform Cloud or S3 + DynamoDB for locking, or Azure Storage, etc.)
  for team collaboration, rather than local state files. This prevents conflicts and data loss. Locking must be enabled
  on the state backend to avoid concurrent write issues. Do not commit state files to git; they are local or in the
  remote backend. Also, periodically backup state (most remote backends do versioning).
* **Immutability & Change Management:** Whenever possible, structure resources to avoid disruptive changes. For example,
  if a piece of infrastructure can be created anew rather than modified in-place (to prevent downtime), do so using
  Terraform's `create_before_destroy` lifecycle settings. For instance, switching AMIs on an EC2 AutoScaling group: use
  launch template versions or new resources rather than modifying the existing in place, to achieve zero downtime
  deployments. Use `terraform plan` to review changes before applying, and have someone else review the plan for
  production changes to ensure nothing unexpected.
* **Terraform Cloud/Enterprise Integration:** If using Terraform Cloud or another automation, ensure it is configured to
  use the same standards (fmt, validations, etc.). Even with automated Terraform runs, code should be formatted and
  linted by developers.
* **Terragrunt (if used):** Some projects might use Terragrunt to manage Terraform. If so, still apply these standards
  within each Terraform module/config, and follow Terragrunt best practices (keep DRY with common.hcl, use generate
  blocks for backend configs, etc.).
* **Example – Terraform Module Structure:**

    ```text
    infra/
      networking/
         main.tf
         variables.tf
         outputs.tf
         README.md  (documentation of module usage)
      compute/
         main.tf
         variables.tf
         outputs.tf
         README.md
      prod/
         main.tf   (calls modules networking, compute with prod-specific vars)
         providers.tf (providers and backend config)
         terraform.tfvars (prod specific variable values)
      dev/
         main.tf (calls modules with dev vars)
         providers.tf
         terraform.tfvars
    ```

  In this layout, `networking` and `compute` are modules, and `prod` and `dev` are environment-specific configurations
  that instantiate those modules. This ensures a clear separation of concerns and reusability.

_Rationale:_ Terraform code manages critical infrastructure; thus, consistency and correctness are paramount. Using
`terraform fmt` and validations keeps the code clean and uniform, which is important when multiple engineers edit the
configs. Modules and proper organization prevent cut-and-paste errors and make large infra scalable across environments.
Naming conventions and tagging help in cloud governance, making it easier to trace resources back to code and owner.
Linting and policy tools (TFLint, Checkov) act as safeguards against known pitfalls (like accidentally opening a port to
the world) and ensure we align with best practices from day one. In summary, following these Terraform standards ensures
we deploy infrastructure that is consistent, secure, and maintainable, and reduces the risk of costly mistakes in cloud
environments.

### JavaScript (Frontend)

Our JavaScript standards focus on front-end development (though many principles apply to Node.js as well). Front-end
code often directly impacts user experience, so clarity, performance, and safety (security) are key.

* **Modern ES6+ Syntax:** Write JavaScript using modern ES6+ syntax and features. Use `let` and `const` instead of `var`
  to declare variables (block scope avoids many bugs). Prefer arrow functions (`() => {}`) for callbacks to keep the
  `this` context or for concise anonymous functions. Use template literals (`'${var}'`) instead of string concatenation
  for readability. Leverage destructuring, spread operator, and other syntactic sugar to write cleaner code. Modern
  syntax not only makes code more expressive but also often prevents common errors (e.g., `const` signals a variable
  should not be reassigned).
* **Strict Mode/Type Checking:** Always enable strict mode (`'use strict'`; or via modules which are strict by default)
  to catch silent errors (like assigning to undeclared variables). If using TypeScript (which some projects might),
  follow the TypeScript strict checks and linting; otherwise, consider using JSDoc annotations for complex data
  structures so tools can provide some type checking. In React, use propTypes or TypeScript interfaces for component
  props to catch type mismatches.
* **Code Style – Linting and Formatting:** Use ESLint with a well-defined rule set (Airbnb style guide or equivalent) to
  enforce code style and catch issues. Also use Prettier to auto-format code on save or pre-commit. Our standard style
  includes 2-space indentation, semicolons at end of statements, and single-quotes for strings (except JSON). No
  trailing commas in function calls (ES5), but allow trailing commas in multiline objects/arrays for cleaner diffs.
  Ensure ESLint is configured to flag unused variables, undefined variables, and common mistakes. Prettier will handle
  spacing, line wrapping, etc., so there should be little manual formatting needed.
* **Naming & Structure:** Follow the naming conventions as outlined (camelCase for variables/functions, PascalCase for
  components/classes). Organize code logically:

    * Group related functions or utilities into modules. Each module should ideally export one main thing (or a set of
      related functions). For example, a `dateUtils.js` might export functions related to date formatting and parsing.
    * In frameworks like React, use one component per file (the component name and file name should match, e.g.
      `LoginForm.jsx` contains `function LoginForm()` or a class of the same name).
    * Avoid long files. If a file grows beyond a few hundred lines, see if it can be broken into smaller modules or
      components.

* **DOM Access and jQuery:** If using a modern framework (React/Vue/Angular), direct DOM manipulation should be
  minimal (handled through the framework). If writing plain JS or jQuery, structure code to avoid overly entangled
  jQuery spaghetti. Use event delegation smartly, clean up event handlers when elements are removed, etc. But typically,
  prefer modern frameworks or vanilla JS with clear structure (e.g. module pattern) for complex apps.
* **Error Handling in UI:** As discussed, handle errors from asynchronous operations (like fetch calls). Use `try/catch`
  with async/await or `.catch()` with promises to handle rejections. Ensure the user gets feedback. For example, if a
  data load fails, perhaps display a message or a retry button. Also consider global error handlers – window `onerror`
  or unhandled promise rejections – to log errors to a monitoring service (like Sentry) so that client-side errors are
  not lost.
* **Accessibility (a11y):** Though not explicitly requested, it's worth mentioning: follow accessibility best practices.
  Use semantic HTML elements (e.g., `<button>` for clickable actions, not <div> with an onClick). Add ARIA attributes
  where needed. Ensure the code does not create keyboard traps and that all interactive elements are reachable via
  keyboard. This is part of quality front-end code.
* **Security (Frontend specific):** Be wary of Cross-Site Scripting (XSS). Do not insert untrusted data into the DOM
  using innerHTML or similar without sanitization. If using frameworks like React, this is mostly handled (React escapes
  content by default). If you must insert raw HTML (e.g., using `dangerouslySetInnerHTML` in React or setting
  `.innerHTML` manually), make sure the content is from a trusted source or sanitized. Avoid using `eval()` or new
  Function() with dynamic code. If you are processing JSON, use `JSON.parse` (which is safe for JSON) rather than `eval`
  on a JSON string. Keep dependencies updated (front-end frameworks, build tools) to get security patches, since
  front-end dependencies can also have vulnerabilities.

* **Performance Best Practices:** Use asynchronous techniques to keep the UI responsive. For example, don't do heavy
  computations on the main thread; if needed, use Web Workers for CPU-intensive tasks so the UI doesn't freeze. Optimize
  network calls: bundle multiple requests if possible, or use GraphQL to fetch in one round-trip instead of many (if
  applicable), use caching (browser cache or service workers for offline). Minimize reflows: if manipulating DOM
  manually, batch updates (e.g., use `document.createDocumentFragment` to append multiple elements off-DOM, then attach
  once). Images: use proper image sizes (don’t load a huge image if a thumbnail is enough), compress images, use modern
  formats (WebP/AVIF). Consider using lazy loading for components not immediately needed (code-splitting in SPAs) to
  reduce initial load times.

* **Testing:** Write front-end tests (unit tests for logic, component tests for React, and possibly end-to-end tests for
  user flows). While not purely a coding standard, having tests ensures code is working as expected. Use Jest/Mocha for
  unit tests and tools like React Testing Library for components. This also forces better code structure (e.g.,
  decoupling logic from the DOM, so it’s easier to test).

* **Example – Clean and Modern JS:**

    ```js
    // Using modern syntax and clear patterns
    import { getUserData } from './api.js';   // importing a utility function
    
    export class UserWidget {
      constructor(containerElement) {
        this.container = containerElement;
      }
    
      async loadAndRender(userId) {
        try {
          const data = await getUserData(userId);
          this.render(data);
        } catch (err) {
          console.error(`Error loading user ${userId}:`, err);
          this.container.innerText = "Failed to load user data.";
        }
      }
    
      render(userData) {
        // Clear existing content
        this.container.innerHTML = '';
        // Create elements and append (or use a frontend framework ideally)
        const nameEl = document.createElement('h2');
        nameEl.textContent = userData.name;
        this.container.appendChild(nameEl);
        // ... render other parts of userData ...
      }
    }
    ```

  This snippet demonstrates: module import/export, a class with methods, usage of async/await with error handling, and
  DOM manipulation kept within a controlled method. It also logs an error to console with context and gives user
  feedback.

_Rationale:_ Following these JavaScript standards ensures our front-end code is maintainable, modern, and robust. A
consistent style (enforced by ESLint/Prettier) means any engineer can jump into the code without tripping over
formatting issues. Modern practices (ES6+ syntax, using frameworks) lead to more concise and reliable code. Good error
handling and security practices prevent common web vulnerabilities and runtime issues, improving user trust.
Performance-conscious coding keeps our interfaces smooth and responsive. By adhering to these guidelines, we create
front-end applications that are not only efficient but also easier to extend and debug, ultimately delivering a better
experience to users and confidence to clients.

#### Python (Backend, Scripting, and Notebooks)

Python is widely used for our data analytics, scripting, and backend services. We follow Pythonic conventions and aim
for readable, efficient code.

* **PEP 8 Compliance:** Adhere to PEP 8 style guidelines for all Python code. Use 4 spaces for indentation (never tabs),
  limit line length to 79 characters (you may go up to 99 or 120 if needed for readability, but 79 is the baseline).
  Break long expressions across lines with continuation indents (aligned in an easily readable way). Add spaces around
  operators and after commas for clarity (e.g., `x = a + b`, not `x=a+b`). Follow the naming conventions from PEP 8 (as
  already summarized in the naming section) for functions, variables, classes, and constants.
* **Docstrings and Documentation:** Use docstrings to document modules, classes, functions, and methods. A docstring at
  the top of a Python file can describe the module’s purpose. Class docstrings describe the class and important usage
  notes. Function docstrings (especially for public functions) should describe the parameters, return type, and any
  exceptions or side effects. We prefer the Google style or NumPy style for docstrings for consistency (but any clear
  style is fine, just be consistent within a project).
* **Typing:** In modern Python (3.8+), use type hints for function signatures and variable declarations when it adds
  clarity. For example:

    ```python
    def process(data: list[str], transform: Callable[[str], str]) -> list[str]:
        ...
    ```

  Type hints are checked by tools like mypy and help with autocomplete and documentation. They are not mandatory for
  every single function (especially trivial ones), but for complex interfaces or library code they greatly aid
  understandability.

* **Imports:** Keep imports organized. Use absolute imports (relative imports only within a package’s internal modules).
  Group imports into three sections: standard library, third-party, and local application imports, separated by blank
  lines. E.g.:

    ```python
    import os
    import sys
    
    import pandas as pd
    
    from myproject.utils import calculate_something
    ```

  Avoid wildcard imports (from module import *) as they obscure what names are present. Explicit is better than
  implicit.

* **Code Structure:** For larger Python applications, follow typical project structure: a `setup.py/pyproject.toml` if
  packaging, a main package directory, tests directory, etc. Each module should have a clear purpose. For web backends,
  follow the framework's best practices (Django: use the MTV structure, Flask: blueprint or app factory patterns, etc.).
  For scripts, keep the script logic small and push as much as possible into importable functions (so they can be tested
  or reused).
* **Error Handling:** Use exceptions for error cases, not error codes. Python exceptions are the idiomatic way to signal
  errors. Catch exceptions at a high level (to log or handle them) but avoid catching exceptions too broadly. Catch
  specific exceptions as noted before. If you catch an exception and cannot handle it meaningfully, consider logging it
  and re-raising, or use raise without arguments inside except to propagate after logging. This prevents burying
  unexpected problems.
* **Logging:** Use the `logging` module for debug/info/warning/error logs instead of print statements, especially in
  long-running applications or library code. Configure log format and level via a config or at program start. Logging
  allows easy control over output and is thread-safe, etc. For simple scripts, print is fine for user-facing output, but
  for any sort of service or complex script, logging is preferred.
* **Testing:** Write unit tests for your Python code where feasible. Use `pytest` or `unittest` frameworks. Tests not
  only validate correctness but also enforce a modular design. For example, a function that does too much is hard to
  test. Aim for functions and methods that do one thing and can be tested in isolation. Also consider using
  type-checking in CI and linters (like flake8, pylint) to catch style or error issues.
* **Performance Considerations:** Python can be slow if not used properly. As mentioned, utilize optimized libraries (
  NumPy, pandas, etc.) for heavy data work. Avoid excessive abstraction if performance is critical (each layer of
  abstraction can add overhead). Use list comprehensions or generator expressions in place of manual loops where
  appropriate – they are often faster and more Pythonic. But also be mindful of memory – for example, a list
  comprehension will create the whole list in memory, whereas a generator will yield items one by one. Choose
  accordingly based on data size. In web apps, be mindful of response time – use caching, avoid doing too much work per
  request (offload to background jobs if needed).
* **Parallelism and Concurrency:** If your Python code needs concurrency, prefer using `asyncio` for IO-bound tasks (
  network calls, etc.) or multiprocessing for CPU-bound tasks, since Python threads are limited by the GIL for CPU
  tasks. Use concurrent.futures or libraries like `joblib` for simple parallelism when appropriate (e.g., parallelizing
  API calls or computations).
* **Jupyter Notebook Practices:** When writing notebooks, keep in mind they are a form of code sharing:

    * Clean up the notebook before committing or sharing: remove extraneous outputs, ensure the notebook runs from top
      to bottom (restart kernel and run all to verify).
    * Use markdown cells to explain what the code is doing, your thought process, and any results interpretation. This
      makes the notebook useful for others (and future you).
    * Do not leave secrets or passwords in notebooks (same rule as code). If you must access something secure, use
      environment variables or a secrets file that's not committed.
    * For any code in notebooks that needs to be reused, consider moving it to a `.py` module in the repository, and
      import it in the notebook. This way, the logic can be tested and reused outside the notebook context.

* **Version control:** treat notebooks as code for purpose of reviews. If a notebook is critical (like defines a data
  pipeline), it should be reviewed and tested. Using Jupyter is fine, but ensure it meets the same standards of clarity
  and correctness as our scripts.

  Example – Python Code with Standards:

  ```python
  import logging
  from typing import Optional
  
  logger = logging.getLogger(__name__)
  
  class DataProcessor:
      """Processes raw data into cleaned format for analysis."""
      def __init__(self, config: dict):
          self.config = config
  
      def load_data(self, path: str) -> list[str]:
          """Load data from the given file path.
          
          Args:
              path (str): Path to the data file (CSV).
          Returns:
              list[str]: List of data records as strings.
          Raises:
              FileNotFoundError: If the file does not exist.
          """
          logger.info("Loading data from %s", path)
          with open(path, 'r') as f:
              lines = f.readlines()
          return [line.strip() for line in lines]
  
      def process_record(self, record: str) -> Optional[dict]:
          """Process a single record line into a structured dict.
          
          Returns None if the record is invalid.
          """
          fields = record.split(',')
          if len(fields) != 5:
              logger.warning("Invalid record (wrong field count): %s", record)
              return None
          return {
              "id": fields[0],
              "name": fields[1],
              "value": float(fields[2]) if fields[2] else None,
              "status": fields[3],
              "date": fields[4]
          }
  ```

  In this example, we see PEP8-compliant naming and spacing, docstrings explaining purpose and usage, logging instead of
  prints, and clear structure. The class DataProcessor and its methods each have single responsibilities.

_Rationale:_ Python is known for its emphasis on readability – “Readability counts” as the Zen of Python says. By
following PEP 8 and writing clear docstrings, we ensure our Python code lives up to that ideal. This makes it easier for
others to understand and build upon our code. Incorporating type hints and tests improves reliability and catches errors
early, which is especially important as codebases grow. Proper error handling and logging in Python are critical for
debugging data pipelines or backends in production, as they often run without direct supervision. By adhering to these
standards, we produce Python code that is clean, pythonic, and reliable, whether it’s a quick data script or a complex
web service.

## Cloud-Specific Best Practices (AWS, Azure, GCP)

When writing code or configurations for cloud environments, there are additional considerations to ensure our solutions
are robust, cost-efficient, and take advantage of cloud capabilities. The following best practices apply across AWS,
Azure, and GCP (with slight variations as noted):

* **Infrastructure as Code & Automation:** Always manage cloud resources through code (Terraform, CloudFormation,
  ARM/Bicep templates, etc.) rather than manual point-and-click. This ensures that environments can be recreated and
  that changes are tracked. Use Terraform as our primary IaC tool for multi-cloud consistency. Validate and test
  infrastructure changes in staging before production. Use CI pipelines to apply Terraform with proper review gates.
  Automating everything (infrastructure, configuration, deployments) reduces human error and enables rapid recovery and
  scaling.
* **Environment Isolation:** Keep different environments (Dev, QA, Prod, etc.) isolated in the cloud. This might mean
  separate AWS accounts, separate Azure resource groups, or separate GCP projects for each environment or client. Code (
  including Terraform) should reflect this by not mixing configurations. This isolation improves security and makes
  testing safer. For example, never run a development test script against the production database by accident – use
  distinct credentials/IDs for each environment.
* **Resource Naming and Tagging:** Use a consistent naming convention for cloud resources (this often goes hand-in-hand
  with Terraform naming). Incorporate identifiers for project, environment, and purpose. For example, an Azure storage
  account might be named `bsanalyticsprodlogs` for BSC Analytics Prod logs. Since different clouds have different naming
  rules (Azure is lowercase only, GCP allows hyphens, AWS varies by service), tailor the naming convention to each but
  keep it logical and documented. Tag/label resources with key metadata: at minimum, `Environment`, `Project` (or
  `Application`), and `Owner` tags. Tags are crucial for cost tracking and housekeeping. They enable scripts or cloud
  automation to find and act on resources (like cleaning up all `Environment:Dev` resources). Many cloud best practice
  frameworks (AWS Well-Architected, etc.) emphasize consistent tagging.
* **Security and Identity:**

    * Use managed identities and roles instead of embedding credentials. For AWS, prefer IAM roles (attached to EC2
      instances or Lambda functions) to give permissions, rather than using access key ID and secret. For Azure, use
      Managed Identity for VMs, containers, and functions; prefer Azure built-in Roles GCP uses service accounts. In
      all cases, scope the permissions narrowly (least privilege principle). For example, if a function only needs
      read access to a storage bucket, do not grant it write or admin rights.
    * Store sensitive configuration in cloud secret managers: AWS Secrets Manager or SSM Parameter Store, Azure Key
      Vault, Google Secret Manager. The code can retrieve secrets at runtime (with the appropriate IAM permissions)
      rather than having them in code or config files. This centralizes audit and rotation of secrets.
    * Enable platform security features: e.g., AWS GuardDuty, Azure Security Center, GCP Security Command Center, which
      can detect anomalies. While this is more on the ops side, the coding angle is to integrate with these (for
      instance, have Lambda functions or scripts respond to security events if relevant).

* **Network security:** When writing cloud infrastructure code, follow best practices like using private subnets for
  application servers, security groups/NACLs to restrict traffic, and using cloud WAF or API Gateway for public
  endpoints. Ensure any code that opens ports or sets up networking does so with least exposure (e.g., no `0.0.0.0/0`
  wide-open access in security groups unless absolutely required for a public service).
* **Reliability and Scalability:**

    * Design for failure: Assume any network call can fail and code accordingly (with retries and backoff). All three
      major clouds have transient errors (throttling, etc.), so implement retry logic using exponential backoff for API
      calls or use their provided SDK methods for retries.
    * Use cloud-native features for reliability: e.g., AWS offers Auto Scaling groups – our Terraform code should
      configure auto-scaling for stateless servers to handle load, rather than assuming fixed capacity. Use load
      balancers (ELB/ALB, Azure Load Balancer, GCP LB) instead of a single server address. In code, that means perhaps just
      ensuring endpoints or configurations are not hardcoded to a single instance.
    * Multi-AZ and backups: For databases and critical services, deploy multi-AZ (or multi-region if needed) for high
      availability. Our Terraform should by default prefer multi-AZ where it makes sense (like RDS, Azure SQL, etc.).
      Also ensure backups are enabled (DB snapshots, etc.). If writing scripts to manipulate data, consider the impact
      on replicas or across regions.
    * Monitoring and Logging: Implement logging and metrics for cloud deployments. For AWS, ensure CloudWatch Logs are
      capturing logs from Lambda or EC2. For Azure, use Log Analytics and consider Application Insights, enabling all
      diagnostic settings on your resource. For GCP, use Stackdriver (Cloud Logging/Monitoring). From a coding perspective,
      that means using the cloud SDK or environment to log in a structured way (e.g., printing JSON logs that CloudWatch
      can parse, or using an official logging library to send logs). Also, add application-level metrics where appropriate
      (for instance, a Python service might use CloudWatch custom metrics or Prometheus to track key metrics). Monitoring
      allows us to catch issues early and auto-scale or alert as needed.

* **Cost Optimization:**

    * Choose the right services for the task. For example, if we need to process messages asynchronously, using AWS
      SQS/Lambda or Azure Functions with a Queue might be more cost-efficient and scalable than running a constantly
      polling VM.
    * Use serverless and managed services when possible to minimize maintenance overhead and cost for low-to-medium
      workloads. But be mindful of their limits – e.g., Lambdas have timeouts (15 min on AWS), Azure Functions
      consumption plan has cold starts, etc. Code should handle these (e.g., not rely on a Lambda running more than 15
      minutes; if needed, break task or use a different service like Fargate or a VM).
    * Schedule or scale down non-prod environments off-hours. This might involve writing scripts or using cloud
      scheduler to stop VMs at night, etc. As a coding standard, perhaps incorporate this thinking into Terraform (tag
      resources with schedule tags or write Lambda to auto-stop). While this is more DevOps, it's worth mentioning that
      as engineers we should design solutions that consider cost (for example, not processing something in an extremely
      expensive way when a simpler approach exists).
    * Use pricing APIs or cost analysis tools if building internal tools – for example, if writing a deployment script,
      maybe integrate a step that forecasts cost. Not usually part of code, but an awareness.

* **Cloud Specific Nuances:**

    * **AWS:** Use the AWS SDK for Python (boto3) or JS (aws-sdk) rather than raw HTTP calls, to benefit from built-in
      retries and auth handling. Follow AWS well-architected framework pillars (operational excellence, security,
      reliability, performance, cost, sustainability) as a checklist for designs. E.g., ensure our design has no single
      points of failure (reliability), and that we use IAM roles properly (security).
    * **Azure:** Be aware of Azure's regional availability and quotas. For example, some Azure services are regional and
      need failover strategies. Use Azure SDKs to interact with services (they handle details like token renewal for
      Managed Identities). Tagging in Azure is especially important for cost management as well.
    * **GCP:** Use service accounts for GCP auth and scoped IAM roles. Utilize Google’s Cloud Build or Deployment
      Manager if not using Terraform for some reason. Ensure GCP resource names follow naming rules (often lowercase
      with hyphens). Use Stackdriver for logs/monitoring. GCP projects are a natural isolation unit; use separate
      projects for isolation similar to AWS accounts.
    * **Multi-Cloud Awareness:** While we typically implement per client on a chosen cloud, if a solution is intended to
      be cloud-agnostic, abstract cloud-specific implementations behind interfaces. For example, if writing Python code
      that could store data on S3 or Azure Blob, have a storage interface and implement two versions rather than
      littering code with if-AWS vs if-Azure. This isn’t always needed, but something to consider if portability is a
      goal.

* **CI/CD for Cloud Deployments:** Use continuous delivery pipelines to deploy code and infrastructure. E.g., GitHub
  Actions or Jenkins that run tests, then `terraform plan/apply` for infra, then deploy application code (to Lambda,
  Kubernetes, App Service, etc.). Include automated tests for infrastructure if possible (using tools like Terratest or
  simply doing `plan` and scanning output). This automation ensures that the standards are enforced every deployment (no
  manual fiddling). Also, rollbacks should be considered: e.g., using versioned artifacts and blue-green or canary
  deployments for safer releases.
* **Documentation and Knowledge Sharing:** Document cloud setup and architecture in README or Wiki. New team members
  should have references for how the system is structured (VPC design, data flow, etc.). Also document any
  client-specific deviations (if a client requires certain cloud practices, note them clearly).

_Rationale:_ Cloud best practices ensure that the systems we build are secure, resilient, and efficient in a cloud
environment. The cloud introduces unique challenges (ephemeral resources, distributed systems issues) but also provides
powerful features (auto-scaling, managed services). By codifying these practices, we ensure we make good use of what
cloud offers while mitigating its risks. Security is amplified in cloud because misconfigurations can expose entire
databases; our standards guard against that by using proven tools and patterns (like secret managers and least privilege
IAM). Reliability and performance guidelines mean our solutions can scale on-demand and handle failures gracefully,
which is expected in cloud deployments. Cost awareness is crucial for client satisfaction – a well-architected cloud
solution avoids wasting resources (like running huge servers at low utilization or forgetting to turn off dev
resources). In summary, following cloud best practices in our code and configuration helps us deliver cloud solutions
that are robust, secure, and cost-effective, aligned with the best that AWS/Azure/GCP have to offer.

## Conclusion and Adherence

All BSC Analytics engineers are expected to follow these coding standards in their daily work. Doing so will result in a
codebase that is consistent in style and quality, which in turn makes our teamwork more effective and our deliverables
more reliable. We recognize that sometimes specific projects or clients may impose different requirements – in those
cases, use your judgment to reconcile them with our internal standards, and when in doubt, discuss with team leads or
the CTO for guidance.

Remember that the intent behind each rule is just as important as the rule itself. We encourage engineers to understand
the “why” (as explained above for each section) – this helps apply the standards thoughtfully rather than mechanically.
For instance, if a unique situation arises where deviating from a guideline is beneficial, understanding the underlying
principle lets you make an informed decision (and be able to justify it).

We will periodically review and update these standards as technologies and best practices evolve. Engineers are welcome
to suggest improvements. By adhering to these guidelines, we ensure our code is maintainable for ourselves and our
successors, and we deliver high-quality solutions to our clients. Consistent, clean, and well-reasoned code is a
hallmark of professionalism, and it’s something we strive for in every project.
