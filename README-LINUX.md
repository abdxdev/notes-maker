[Windows](README.md) | Linux

# Markdown Latex PDF Builder

![alt text](https://github.com/abdxdev/markdown-latex-pdf-builder/blob/main/screenshots/screenshot_1.png?raw=true)

A vscode tool that converts advanced Markdown with diagrams, code execution, and custom macros into publication-ready LaTeX PDFs.

![alt text](https://github.com/abdxdev/markdown-latex-pdf-builder/blob/main/screenshots/screenshot_2.png?raw=true)

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [VS Code Setup](#vs-code-setup)
- [Usage](#usage)
- [Changing Default Values](#changing-default-values)
- [Changing the University Logo](#changing-the-university-logo)
- [Updating markdown-latex-pdf-builder](#updating-markdown-latex-pdf-builder)
- [Uninstallation](#uninstallation)
- [Tested Environments](#tested-environments)

## Prerequisites

Before starting, make sure you have the following:

### Python

Install via your package manager or from [python.org](https://www.python.org/downloads/). Preferably, above version 3.10.

```bash
# Ubuntu/Debian
sudo apt install python3 python3-pip

# Arch
sudo pacman -S python python-pip
```

Check installation:

```bash
python3 --version && python3 -m pip --version
```

### Visual Studio Code

Download from [code.visualstudio.com](https://code.visualstudio.com/download).

Check installation:

```bash
code --version
```

Install the [Command Runner](https://marketplace.visualstudio.com/items?itemName=edonet.vscode-command-runner) extension for VS Code.

### Node Package Manager (optional, for Mermaid diagrams)

Download and install Node.js from [nodejs.org](https://nodejs.org/en/download).

Check installation:

```bash
node --version && npm --version
```

## Installation

Open a terminal and run these commands:

> [!CAUTION]
> Do not run as root

#### Step 1: Download and install TinyTeX

Install TinyTeX to your user data folder:

```bash
curl -L "https://github.com/rstudio/tinytex-releases/releases/download/daily/TinyTeX.tar.gz" -o /tmp/TinyTeX.tar.gz
tar -xzf /tmp/TinyTeX.tar.gz -C ~/.local/share
rm /tmp/TinyTeX.tar.gz
export PATH="$PATH:$HOME/.local/share/.TinyTeX/bin/x86_64-linux"

```

To make the PATH change permanent:

```bash
echo 'export PATH="$PATH:$HOME/.local/share/.TinyTeX/bin/x86_64-linux"' >> ~/.bashrc
source ~/.bashrc
tlmgr emoji ifplatform markdown csvsimple lua-ul

```

> [!NOTE]
> The binary folder name may differ on non-x86 systems. Check `~/.local/share/.TinyTeX/bin/` after extraction and adjust the path accordingly (e.g. `aarch64-linux` on ARM).

#### Step 2: Install Pygments

Install the Pygments syntax highlighter for code blocks:

```bash
python3 -m pip install Pygments
```

> [!NOTE]
> After installation, make sure `pygmentize` is available on your system PATH. You can verify by running:
>
> ```bash
> pygmentize -V
> ```
>
> If the command is not found, add the Python scripts directory to your PATH:
>
> ```bash
> SCRIPTS_DIR=$(python3 -c "import sysconfig; print(sysconfig.get_path('scripts'))")
> echo "export PATH=\"\$PATH:$SCRIPTS_DIR\"" >> ~/.bashrc
> source ~/.bashrc
> 
> ```

#### Step 3: Download markdown-latex-pdf-builder

Download markdown-latex-pdf-builder to your user data folder:

```bash
curl -L "https://github.com/abdxdev/markdown-latex-pdf-builder/archive/refs/heads/main.zip" -o /tmp/main.zip
unzip -o /tmp/main.zip -d ~/.local/share/markdown-latex-pdf-builder
rm /tmp/main.zip
cp ~/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/default.json ~/.local/share/markdown-latex-pdf-builder/default.json

```

#### Step 4 (Optional): Install Mermaid CLI for diagram support

Install the Mermaid CLI and Chrome headless shell for rendering diagrams:

```bash
npm install -g @mermaid-js/mermaid-cli
npx puppeteer browsers install chrome-headless-shell

```

> [!NOTE]
> If you are using **pnpm**:
>
> ```bash
> pnpm setup && source ~/.bashrc
> pnpm add -g @mermaid-js/mermaid-cli
> pnpm -g exec puppeteer browsers install chrome-headless-shell
> 
> ```

#### Step 5 (Optional): Install Python dependencies

Install required Python packages for plotting inside python execution blocks:

```bash
python3 -m pip install numpy matplotlib
```

#### Step 6 (Optional): Install SVG image support

If your Markdown files reference SVG images:

```bash
python3 -m pip install svglib reportlab
```

#### Step 7: Verify installation

Run this command to test markdown-latex-pdf-builder with the comprehensive guide example. If everything is set up correctly, a PDF document should be generated and opened automatically.

```bash
python3 ~/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/script.py ~/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/test/COMPREHENSIVE-GUIDE.md --show
```

## VS Code Setup

1. Open VS Code and press `Ctrl + Shift + P`.
2. Search for **Preferences: Open User Settings (JSON)** and open it.
3. Add this to the bottom of your `settings.json` file just before the closing `}`:

   ```jsonc
   // ...other settings...,
    "command-runner.commands": {
        "markdown-latex-pdf-builder: Build Document (follow json settings)": "python3 \"$HOME/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/script.py\" \"${file}\"",
        "markdown-latex-pdf-builder: Build Assignment (add university title page)": "python3 \"$HOME/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/script.py\" \"${file}\" --titleTemplate university-title --enableContentPage false",
        "markdown-latex-pdf-builder: Build Notes (add title)": "python3 \"$HOME/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/script.py\" \"${file}\" --titleTemplate header-title --enableContentPage false",
        "markdown-latex-pdf-builder: Build Publication (add contents and title page)": "python3 \"$HOME/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/script.py\" \"${file}\" --titleTemplate separate-page-title --enableContentPage true"
    }
   ```

4. Save the file.

## Usage

1. Open any folder in VS Code.
2. Create a new Markdown file (for example, `report.md`).
3. Add your content. Check out our [comprehensive guide](https://github.com/abdxdev/markdown-latex-pdf-builder/blob/main/test/COMPREHENSIVE-GUIDE.pdf) for advanced features and examples.
4. Press `Ctrl + Shift + R` and select **markdown-latex-pdf-builder: Build Document**. (Extension: Command Runner must be installed. Follow prerequisites if you haven't done so.)
5. After a few moments, the PDF will be generated along with a `.json` metadata file. Edit this file to change document settings like title, university, and date. (To permanently change default values, see the next section.)
6. Re-run the build command from step 4 to generate the updated PDF.

> [!NOTE]
> You may delete the build folder (`_build_report` in this case) after the PDF is finalized.

## Changing Default Values

You can edit the default settings for document generation by modifying the `default.json` file.

Run this command to open it in VS Code:

```bash
code ~/.local/share/markdown-latex-pdf-builder/default.json
```

The next time you generate a document, it will use the updated defaults.

## Changing the University Logo

To replace the default logo, open the script directory:

```bash
xdg-open ~/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main/
```

Replace the existing `uni-logo.pdf` file with your logo file (use the same name).

## Updating markdown-latex-pdf-builder

To update markdown-latex-pdf-builder, run these commands in a terminal:

#### Step 1: Remove old version and download the latest

```bash
rm -rf ~/.local/share/markdown-latex-pdf-builder/markdown-latex-pdf-builder-main
curl -L "https://github.com/abdxdev/markdown-latex-pdf-builder/archive/refs/heads/main.zip" -o /tmp/main.zip
unzip -o /tmp/main.zip -d ~/.local/share/markdown-latex-pdf-builder
rm /tmp/main.zip

```

## Uninstallation

#### Step 1: Remove TinyTeX

```bash
rm -rf ~/.local/share/.TinyTeX
```

#### Step 2: Remove markdown-latex-pdf-builder

```bash
rm -rf ~/.local/share/markdown-latex-pdf-builder
```

## Tested Environments

This tool was tested on:

- Windows 11
- Command Runner v0.0.124
- Python 3.13.3
- TinyTeX 0.57
- tlmgr 76773
