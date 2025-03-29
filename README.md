# Custom `copy` Command for Linux

A Bash script providing a custom `copy` command designed to easily grab the content of single files or the concatenated content of text files within a folder structure, respecting `.gitignore` rules and excluding common binary/media types. The output is sent directly to the system clipboard (`+` register).

## Features

*   **Dual Mode:** Handles both single file paths and folder paths as input.
*   **Clipboard Integration:** Copies content directly to the system clipboard (using `xclip` for X11 or `wl-copy` for Wayland).
*   **File Copy:** Copies the *entire content* of the specified file.
*   **Folder Copy (Recursive):**
    *   Recursively scans the specified folder and its subfolders.
    *   Respects `.gitignore` files found within the directory structure.
    *   Excludes common non-text file extensions (images, video, audio, archives, etc.).
    *   Prepends each file's relative path (`[./path/to/file.txt]`) to its content.
    *   Adds a prominent separator line between the content of different files.
    *   Concatenates the results and copies the final combined text to the clipboard.
*   **Robust Error Handling:** Includes checks for dependencies, path existence, file types, and provides informative messages.

## Prerequisites

*   **Bash:** The script is written for Bash.
*   **`ripgrep` (`rg`):** Used for efficient, `.gitignore`-aware file searching and filtering.
    *   Install on Arch Linux: `sudo pacman -S ripgrep`
*   **Clipboard Utility:** Requires either `xclip` (for X11) or `wl-clipboard` (which provides `wl-copy` for Wayland). The script auto-detects the appropriate tool based on your session.
    *   Install both on Arch Linux: `sudo pacman -S xclip wl-clipboard`

*(Note: Installation commands may differ for other Linux distributions).*

## Installation

1.  **Save the Script:**
    Download or copy the script code into a file named `copy`. A common location for user scripts is `~/.local/bin/`.
    ```bash
    # Example: Create the directory if it doesn't exist
    mkdir -p ~/.local/bin

    # Save the script content into the file (e.g., using nano or your preferred editor)
    nano ~/.local/bin/copy
    ```

2.  **Make it Executable:**
    ```bash
    chmod +x ~/.local/bin/copy
    ```

3.  **Ensure it's in your PATH:**
    The directory containing the script (`~/.local/bin` in this example) needs to be in your system's `PATH` environment variable so you can run it from anywhere. Most modern Linux systems (including Arch with standard setup) add `~/.local/bin` to the PATH automatically if it exists when you log in.

    *   **Check:** Log out and log back in, or open a new terminal, and run:
        ```bash
        which copy
        ```
        If it prints the path (e.g., `/home/your_user/.local/bin/copy`), you're set!
    *   **Manual Add (if needed):** If `which copy` doesn't find it, edit your shell configuration file (e.g., `~/.bashrc` for Bash, `~/.zshrc` for Zsh):
        ```bash
        nano ~/.bashrc  # Or ~/.zshrc
        ```
        Add this line at the end:
        ```bash
        export PATH="$HOME/.local/bin:$PATH"
        ```
        Save the file, then reload the configuration:
        ```bash
        source ~/.bashrc # Or source ~/.zshrc
        ```
        Verify again with `which copy`.

## Usage

The command accepts a single argument: the path to the file or folder.

**Copy a single file's content:**

```bash
copy path/to/your/file.txt
copy ~/.config/nvim/init.lua
```
*(Output: Content of the file copied to clipboard)*

**Copy content of text files within a folder:**

```bash
copy path/to/your/project/src
copy . # Copies applicable files from the current directory
```
*(Output: Concatenated content of text files (respecting filters), each block prepended with `[./relative/path/file.ext]` and separated by a prominent marker, copied to clipboard)*

**Example Output (Folder):**

```
[./src/main.js]
console.log("Hello");
// More code...


#--------------------------------------#
# File Separator
#--------------------------------------#


[./src/utils/helper.js]
export function help() {
  return "I helped!";
}
// More code...


#--------------------------------------#
# File Separator
#--------------------------------------#


[./README.md]
# Project Title
Description...
```
*(This combined text block would be in your clipboard)*

## How it Works

*   The script checks if the input is a file or directory.
*   For files, it directly `cat`s the content (after a basic non-text check) to the clipboard tool (`xclip` or `wl-copy`).
*   For directories, it uses `ripgrep` (`rg`) with `--files` to list files respecting `.gitignore`. Globs (`--glob '!.ext'`) are used to exclude binary/media types.
*   `xargs` is used to process the file list from `rg`. It runs a small `sh -c` script for each file.
*   The `sh -c` script prints the filename header `[./path/to/file]` and then `cat`s the file content. It adds separator lines between files.
*   Error detection for `cat` is included within the `sh -c` script.
*   The combined output (headers, content, separators) is captured and piped to the clipboard tool.

## License

This script is provided under the permissive license: MIT.
