Annif provides a command line interface (CLI) for administrative tasks such as training and evaluating projects and also sharing them via the 🤗 Hugging Face Hub. The documentation for CLI commands is available on ReadTheDocs for:

- [latest Annif release](https://annif.readthedocs.io/en/stable/source/commands.html)
- [current development version](https://annif.readthedocs.io/en/latest/source/commands.html)

> [!TIP]
> You can also run `annif --help` or `annif <command> --help` in your terminal to see available commands and options.

## Shell compeletions

Annif CLI commands supports tab-key completion in bash, zsh and fish shells for commands and options and project id, vocabulary id and path parameters.

To enable the completion support in your current terminal session use `annif completion` command with the option according to your shell to produce the completion script and source it. For example, run

    source <(annif completion --bash)

To enable the completion support in all new sessions first add the completion script in your home directory:

    annif completion --bash > ~/.annif-complete.bash

Then make the script to be automatically sourced for new terminal sessions by adding the following to your `~/.bashrc` file (or in some [alternative startup file](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)):

    source ~/.annif-complete.bash

For details and usage for other shells see [Click documentation](https://click.palletsprojects.com/en/8.1.x/shell-completion/).

---
[[← Running as a WSGI service|Running-as-a-WSGI-service]] | [[Web user interface →|Web-user-interface]]
