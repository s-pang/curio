# Onboarding Document for UTK, Curio, and Urbanite

This document is meant for **undergraduate students** involved in UTK, Curio, and Urbanite projects to get you started quickly and independently.

## Table of Contents

1. [Overview: UTK, Curio, and Urbanite](#1-overview-utk-curio-and-urbanite)
2. [Core Technologies](#2-core-technologies)
3. [Essential Installations](#3-essential-installations)
4. [General Organization of Documents](#4-general-organization-of-documents)
5. [Making Contributions](#5-making-contributions)
6. [Usage and Quick Start](#6-usage-and-quick-start)
7. [Tips for Seeking Help](#7-tips-for-seeking-help)
8. [Task List](#8-task-list)
9. [FAQ](#9-faq)
    - [Should I use Windows?](#should-i-use-windows)
    - [What is Bash?](#what-is-bash)
    - [What is Git?](#what-is-git)
    - [What is the difference between Conda, Miniconda, and Mamba?](#what-is-the-difference-between-conda-miniconda-and-mamba)
    - [How do I fix errors or debug issues?](#how-do-i-fix-errors-or-debug-issues)
    - [Why do I get “no such file” when running `python curio.py start`?](#why-do-i-get-no-such-file-when-running-python-curiopy-start)
    - [Why are there two different instances of `curio` when I use pip versus cloning?](#why-are-there-two-different-instances-of-curio-when-i-use-pip-versus-cloning)
    - [Why do I get `bash: conda: command not found`?](#why-do-i-get-bash-conda-command-not-found)
    - [How do I know if my issue is with Curio, Conda, or Pip?](#how-do-i-know-if-my-issue-is-with-curio-conda-or-pip)
    - [Why does `curio start` take so long to run the first time?](#why-does-curio-start-take-so-long-to-run-the-first-time)
    - [I am getting a "No such file or directory" error when loading a file](#i-am-getting-a-no-such-file-or-directory-error-when-loading-a-file)

## 1. Overview: UTK, Curio, and Urbanite

- **UTK (Urban Toolkit):** A flexible and extensible visualization framework that enables the easy authoring of web-based visualizations through a new high-level grammar specifically built with common urban use cases in mind. [GitHub](https://github.com/urban-toolkit/utk)

- **Curio:** Built on top of UTK, Curio is a framework for collaborative urban visual analytics that uses a dataflow model with multiple abstraction levels (code, grammar, GUI elements) to facilitate collaboration across the design and implementation of visual analytics components. The framework allows experts to intertwine preprocessing, managing, and visualization stages while tracking provenance of code and visualizations. [GitHub](https://github.com/urban-toolkit/curio)

- **Urbanite:** Building on Curio, Urbanite extends the dataflow-based approach by incorporating large language models (LLMs) to enable human-AI collaboration in urban visual analytics. It allows users to specify intent at multiple scopes, enabling interactive alignment across specification, process, and evaluation stages, with features for explainability, multi-resolution task definition, and interaction provenance. [GitHub](https://github.com/urban-toolkit/urbanite)

Curio uses UTK while Urbanite builds on Curio with LLM-powered capabilities. If you would like to learn more about the design and research behind these tools, please see the research papers linked in each repository.


> "We have seen that computer programming is an art, because it applies accumulated knowledge to the world, because it requires skill and ingenuity, and especially because it produces objects of beauty. A programmer who subconsciously views himself as an artist will enjoy what he does and will do it better."  
> — Donald Knuth

As you code for Curio, remember that things not working is a normal, expected part of programming. Debugging is not just about fixing mistakes; it is how programmers learn, understand, and improve their work. Each error you encounter is an opportunity to clarify your thinking, discover how the systems and frameworks truly work, and refine your design. **This is the craft you are developing.** Treat debugging and refinement as part of your learning, not interruptions to it.

As you learn, consider reading [Peter Norvig’s "Teach Yourself Programming in Ten Years"](https://norvig.com/21-days.html). Norvig emphasizes that programming is not about shortcuts or overnight mastery but about learning deeply, practicing consistently, and staying curious while building meaningful systems. Likewise, Donald Knuth, one of the most respected computer scientists, describes programming as an art that requires creativity, skill, and the pursuit of clarity and beauty in your work. You can read his classic perspective in ["Computer Programming as an Art"](https://dl.acm.org/doi/pdf/10.1145/1283920.1283929).

When your code breaks or your pipeline fails, take a breath: this is not a reason for frustration, but an invitation to engage with programming and strengthen your ability to think, debug, and build effectively.

## 2. Core Technologies

Curio is built using **React** and **TypeScript**. For an overview of React, see [this tutorial](https://react.dev/learn) and [this tutorial](https://beta.reactjs.org/learn).

### How a React program is usually built and deployed

A typical React application development and deployment pipeline follows these stages:

#### 1. Development

- Code is written in **TypeScript** using React components, typically with state management via hooks (e.g., `useState`, `useEffect`).
- Development happens locally using **Webpack Dev Server**, which provides hot module reloading for a fast feedback loop.
- Styling is handled using CSS Modules.

#### 2. Building

- The app is bundled and transpiled using **Webpack**, converting TypeScript and JSX into browser-compatible JavaScript.
- This process:
  - Minifies assets
  - Tree-shakes unused code
  - Optimizes images and static assets for performance
- The output is a `dist/` or `build/` folder containing:
  - `index.html`
  - Minified JS bundles (e.g., `main.[hash].js`)
  - CSS files
  - Other static assets

#### 3. Deployment

- The `dist/` or `build/` folder can be deployed:
- Environment variables (API keys, feature flags) are injected during build time or runtime using environment files (`.env`).

### How this is handled in Curio

In Curio, these stages are automated using `curio.py`, which manages building, serving, and orchestrating the **frontend, backend, and sandbox environments** during local development and testing.

However, you can also **build and serve each component manually** if needed:

- **Frontend**:
  - Build manually using the appropriate `npm run build` in `frontend/urban-workflows`.
  - Serve using:
    - `npm run start` (development with Webpack Dev Server)
    - or `python -m http.server 8080 --directory dist` (serving built files).

- **Backend**:
  - Run using `python -m backend.server`.
  - Initializes the database if necessary before serving requests.

- **Sandbox**:
  - Run using `python -m sandbox.server`
  - Useful for isolated environment testing.


These functions handle environment setup, server startup, and process management.

> **Note:** You need to **start these servers in the appropriate folder** (check `utk_curio/frontend/urban-workflows`, `utk_curio/backend`, `utk_curio/sandbox` folders) to ensure relative imports and environment paths work correctly.

> **Note:** There are several ways to install and use Curio (pip, Docker, manual installation). Check the [USAGE.md](USAGE.md) document for more details.

### Frontend Codebase Overview

The `mainCanvas` component (located here: `utk_curio/frontend/urban-workflows/src/components/MainCanvas.tsx`) is responsible for building and rendering the entire editor canvas. Inside the `components` folder, you will find modular subcomponents. For example:

- `TableBox` → renders and manages table nodes
- `ImageBox` → handles image nodes
- `UserMenu`, `ToolsMenu`, `TopMenu` → UI layers

If you check the `MainCanvas.tsx` file, at around line 97, you will see something similar to:

```tsx
return (
    <div>
        <ReactFlow />
        <UserMenu />
        <ToolsMenu />
        {/* other components */}
    </div>
);
```

This JSX structure defines the editor layout with React components, not raw HTML. Components are implemented in other files. For examples:

```tsx
<TopMenu />
```

References to: `components/menu/top/TopMenu.tsx`. Similarly, style classes (e.g., `className="rightSide"`) are defined in other files (e.g., `UpMenu.module.css`).

## 3. Essential Installations

Install these tools before you start:

- **Git for Windows:** Download and install [Git for Windows](https://git-scm.com/download/win). It comes with Git Bash, which you will use for command-line operations. See [here](https://rogerdudler.github.io/git-guide/) for a quick Git tutorial.
  
- **Python and Pip:** Download and install [Anaconda](https://www.anaconda.com/products/distribution) for Python environment management. For understanding how to manage Python with Conda, see [Getting Started with Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html).

## 4. General Organization of Documents

This folder structure represents the **overall structure followed in each of the UTK, Curio, and Urbanite repositories**:

- `docs/` – Usage guides, examples, contribution guidelines, developer references
- `tests/` – Unit tests and integration tests
- `utk_curio/` – Source code for the UTK and Curio frameworks

This **structure is followed across each repository (UTK, Curio, Urbanite) with minor variations**.

## 5. Making Contributions

You are encouraged to contribute! Please read our [Contribution Guidelines](https://github.com/urban-toolkit/curio/blob/main/docs/CONTRIBUTIONS.md) before submitting pull requests. Also check [USAGE.md](USAGE.md) for more details on required installations.

Contributions can include:
- Fixing bugs
- Adding examples
- Improving documentation
- Building new visualizations with Curio or Urbanite

Remember: **what you get is what you give**. The more you invest in exploring, building, and contributing, the more you will learn and gain from these projects. It is up to you how much you get out of this experience.

## 6. Usage and Quick Start

To learn how to use Curio:

 - Quick Start: Follow the Quick Start Guide for immediate steps to run your first visualization.
 - Usage Guide: Review the Usage Guide to understand the workflows, architecture, and examples in Curio.

These guides will help you learn how to run, modify, and contribute to the projects independently.

## 7. Tips for Seeking Help

- Use **Discord channels** to ask questions and share issues.
- Use email for help requests only if it is specifically requested or truly necessary.
- Being proactive is important; there may be days when help is delayed, so take initiative to search the documentation, check previous issues, and try debugging on your own.
- When asking for help, provide as much information as possible:
  - The error message.
  - Steps to reproduce the error.
  - What you have tried already.
  - Screenshots if relevant.

## 8. Task List

Use this list to guide your first week of onboarding and to ensure your environment is correctly set up:

- [ ] **Install Git for Windows** and confirm `git` is available in your terminal.
- [ ] **Install Anaconda** (or Miniconda + Mamba) and confirm `conda` is available.
- [ ] **Fork and clone the repository** you will work on (UTK, Curio, or Urbanite).
- [ ] **Create and activate your Conda environment.**
- [ ] **Refer to the repository for installation steps.**
- [ ] **Run the tool** to confirm everything is working.
- [ ] **Explore the folder structure** and identify:
    - Where examples live
    - Where to add new scripts or visualization specs
    - How documentation is organized
- [ ] **Join the Discord channel**.
- [ ] **Read the CONTRIBUTIONS.md** to understand how to submit contributions.
- [ ] **Complete a first small task** (e.g., fixing a typo in documentation or running an existing example).

## 9. FAQ

### Should I use Windows?

Yes, you can use Windows with Git Bash or WSL for bash support. Alternatively, MacOS and Linux are also fully supported.

### What is Bash?

Bash is a command-line shell used to run commands and scripts. It is essential for running many scripts used in these projects. There are several options for Bash on Windows (e.g., Git Bash, Hyper, Windows Terminal with WSL). It’s up to you which one you prefer, but Git Bash is the easiest to get started.

### What is Git?

Git is a version control system that allows you to track changes in your code, collaborate with others, and manage your codebase systematically.

### What is the difference between Conda, Miniconda, and Mamba?

- **Conda** is a package and environment manager bundled with Anaconda, which includes many pre-installed packages useful for data science and visualization.
- **Miniconda** is a minimal installer for Conda, letting you install only what you need, saving disk space.
- **Mamba** is a fast, drop-in replacement for Conda with the same commands, speeding up environment creation and package installation.

You can use any of these for managing your Python environments. If you want simplicity, use Anaconda; for lightweight flexibility, use Miniconda or Mamba.

### How do I fix errors or debug issues?

First, read the documentation in the repository. Check the documentation for setup. If you need to make code changes to debug, create a fork of the repository, make your changes there, and test locally before submitting any issues or pull requests for help.

### Why do I get “no such file” when running `python curio.py start`?

If you are getting an error like:
```bash
python: can't open file 'curio.py': [Errno 2] No such file or directory
```
it means Python cannot find `curio.py` in your current working directory. To solve it, you must run the command inside the folder where you cloned the repository, where curio.py is located:

```bash
cd path/to/your/cloned/repo
python curio.py start
```

If you installed the project using pip (e.g., pip install curio), it will install the entry point automatically, allowing you to run the following from any folder without needing to be in the project directory:

```bash
curio start
```

### Why are there two different instances of `curio` when I use pip versus cloning?

When you install `curio` with pip, it places the `curio` executable in your Python environment’s Scripts (Windows) or bin (macOS/Linux) folder, making it accessible system-wide from any folder.

When you clone the repository directly, `curio` (or `curio.py`) remains in the folder where you cloned it, and you must run it from that folder unless you set up a manual path or install it using pip.

In short:
  - Installed via pip → curio is globally available from any folder.
  - Cloned manually → curio is local to the folder where you cloned it and must be run from there unless you install it.

### Why do I get `bash: conda: command not found`?

This error means Git Bash cannot find your `conda` installation because:
  - Git Bash does not automatically inherit the PATH used by Anaconda/Miniconda installed on Windows.
  - By default, `conda` is added to the Windows environment PATH, but Git Bash may need manual configuration to access it.

To fix it, verify where Anaconda/Miniconda is installed. Typical installation paths include `C:\Users\<YourUsername>\Anaconda3` or `C:\Users\<YourUsername>\Miniconda3`. Then, add `conda` to your Git Bash path, replacing the appropriate paths:

```bash
echo 'export PATH="/c/Users/<YourUsername>/Anaconda3:/c/Users/<YourUsername>/Anaconda3/Scripts:/c/Users/<YourUsername>/Anaconda3/Library/bin:$PATH"' >> ~/.bashrc
```

(replace <YourUsername> with your Windows username and make sure the path is correct)

Then, apply the changes:
```bash
source ~/.bashrc
```

Test if it works:
```bash
conda --version
```

If it returns a version, `conda` is now accessible in Git Bash.

Also, if you want `conda activate` to work fully in Git Bash, initialize Conda for bash:
```bash
conda init bash
```
And then restart Git Bash. Check [this](https://discuss.codecademy.com/t/setting-up-conda-in-git-bash/534473) for even more information.

### How do I know if my issue is with Curio, Conda, or Pip?

When troubleshooting, it helps to identify **where the problem actually is**:

**Check if it’s a Curio problem:**
- Does `curio start` (or `python curio.py start`) fail with errors inside the app?
- If the error mentions Curio-specific stack traces, it’s a **Curio project issue** (wrong folder, misconfiguration, or a Curio bug).

**Check if it’s a Conda problem:**
- If `conda` commands give `command not found` or environment activation fails (`conda activate` errors), it’s a **Conda installation or PATH issue**.
- If packages are missing despite installation attempts inside Conda, verify you are in the correct environment:
    ```bash
    conda info --envs
    ```

**Check if it’s a Pip problem:**
- If `pip install` fails, or packages are missing after installation, it’s typically a **pip issue** (wrong environment, missing dependencies, or permissions).
- Check which environment `pip` is using:
    ```bash
    which pip
    pip --version
    ```
Ensure it matches your active environment (`conda info`).

If you are still unsure, please gather the following so we can pinpoint whether it’s **Curio, Conda, or Pip** and resolve it.
- The exact command you ran.
- The **full error message**.
- Your current working directory:
    ```bash
    pwd
    ```
- Outputs of:
    ```bash
    which python
    which pip
    conda info --envs
    ```
### Why does `curio start` (or `python curio.py start`) take so long to run the first time?

If your first run of:
```bash
curio start
```

or

```bash
python curio.py start
```

is taking a long time, this is normal. On the first run, Curio needs to process Node.js files (frontend assets, JavaScript/TypeScript, etc.) for the first time. First run may take 30 seconds to a few minutes as Node.js compiles and bundles assets. Subsequent runs will be significantly faster since the build artifacts are cached.

### I am getting a "No such file or directory" error when loading a file

Check the folder you are running Curio from; the file path you provide is relative to that location.
