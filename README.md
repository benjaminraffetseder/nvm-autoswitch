# Automatic Node.js Version Switching with NVM and Zsh Hook

## Overview

This documentation provides a step-by-step guide for automatically switching Node.js versions using NVM (Node Version Manager) and the provided Zsh script. This setup allows you to switch Node.js versions seamlessly based on the `.nvmrc` file in your project's directory, ensuring that the appropriate Node.js version is used for each project.

## Prerequisites

- NVM (Node Version Manager) installed on your system.
- Zsh shell (the script is written for Zsh, but can be adapted for other shells).

## Script Explanation

The provided script utilizes NVM and Zsh hooks to automatically switch Node.js versions when you navigate to a directory containing an `.nvmrc` file. The script performs the following actions:

1. Sets the `NVM_DIR` environment variable.
2. Sources the `nvm.sh` script to load NVM into the current shell session.
3. Defines a Zsh hook named `load-nvmrc` that is triggered whenever the current working directory changes.
4. The `load-nvmrc` function:
    - Retrieves the currently active Node.js version using `nvm version`.
    - Searches for an `.nvmrc` file in the current directory and stores its path.
    - If an `.nvmrc` file is found:
        - Retrieves the Node.js version specified in the `.nvmrc` file using `nvm version`.
        - If the specified version is not installed, installs it using `nvm install`.
        - If the specified version is installed but different from the active version, switches to it using `nvm use`.
    - If no `.nvmrc` file is found:
        - Checks if the active version is different from the NVM default version.
        - If different, switches to the NVM default version using `nvm use default`.

5. Attaches the `load-nvmrc` function to the `chpwd` Zsh hook, ensuring it is automatically invoked when the working directory changes.

## Installation and Usage

1. Install NVM by following the instructions at [NVM repository](https://github.com/nvm-sh/nvm).

2. **Option 1: Copy-Paste Script into .zshrc** (Manual Approach):

    ```bash
    export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

    autoload -U add-zsh-hook
    load-nvmrc() {
      local node_version="$(nvm version)"
      local nvmrc_path="$(nvm_find_nvmrc)"

      if [ -n "$nvmrc_path" ]; then
        local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

        if [ "$nvmrc_node_version" = "N/A" ]; then
          nvm install
        elif [ "$nvmrc_node_version" != "$node_version" ]; then
          nvm use
        fi
      elif [ "$node_version" != "$(nvm version default)" ]; then
        echo "Reverting to nvm default version"
        nvm use default
      fi
    }
    add-zsh-hook chpwd load-nvmrc
    load-nvmrc
    ```

3. **Option 2: Append Script Directly from Command Line** (Alternative Approach):

   Run the following command in your terminal to directly append the script to your `.zshrc` file:

   ```bash
   echo "$(curl -sSL https://raw.githubusercontent.com/benjaminraffetseder/nvm-autoswitch/main/nvmswitch.zsh)" >> ~/.zshrc
   ```

   This command fetches the script content from the provided URL and appends it to your `.zshrc` file in a single step.

4. Save and close your `.zshrc` file.

5. Restart your terminal or run `source ~/.zshrc` to apply the changes.

6. Navigate to a directory containing an `.nvmrc` file (e.g., your project directory).

7. The script will automatically detect the Node.js version specified in the `.nvmrc` file and switch to it if necessary.

## Conclusion

By following this documentation, you've successfully set up automatic Node.js version switching using NVM and Zsh hooks. Now, whenever you switch to a directory with an `.nvmrc` file, the script will automatically switch to the specified Node.js version, ensuring a seamless development experience across different projects.
