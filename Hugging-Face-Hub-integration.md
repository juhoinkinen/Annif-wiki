Annif integration with Hugging Face Hub allows to easily upload and download projects and their associated vocabularies to and from [Hugging Face Hub model repositories](https://huggingface.co/docs/hub/models). This integration provides a convenient way to share Annif projects and collaborate on text classification tasks within the Hugging Face ecosystem.

Downloads are possible from public repositories without logging in to Hugging Face Hub, but for all uploads and for downloads from private repositories you need to have logged in using the [`huggingface-cli login` command](https://huggingface.co/docs/huggingface_hub/guides/cli#huggingface-cli-login) or to give a [User Access token](https://huggingface.co/docs/hub/security-tokens) with the `--token` option of the `annif upload` or `annif download` commands.

The integration utilizes the [Hugging Face Hub cache-system](https://huggingface.co/docs/huggingface_hub/guides/manage-cache); to explore the cache, use `huggingface-cli scan-cache` command, and to delete items from the cache, use `huggingface-cli delete-cache`. 

Note that using [glob wildcards](https://en.wikipedia.org/wiki/Glob_(programming)) (e.g. `*` or `?`) in the projects id patterns for the below commands can make the shell to [expand the wildcards](https://www.gnu.org/software/bash/manual/html_node/Filename-Expansion.html) to matching _filenames_, instead of the intended projects, because the shell expansion happens before Annif processes the command arguments. At least when targeting all projects with `*` pattern the shell expands it to all filenames in the directory. To avoid shell expansion, use quotion marks around the pattern, e.g. `"*"`.

# Downloading projects from Hugging Face Hub

The download command enables to download selected projects and their associated vocabularies from a specified Hugging Face Hub repository. This command retrieves project and vocabulary archives along with their configuration files from the designated repository and extracts them to the local `data/` directory and `projects.d/` directory, respectively. After download the projects are directly usable by Annif (if `projects.{cfg,toml}` does not exists or by using the `--projects` option to override them).

> [!CAUTION]
> The `annif download` command has a `--trust-repo` option, which needs to be used if the repository to download from has not been used previously (that is if the repository does not appear in the local Hugging Face Hub cache). This option is to remind of the risks of using models from the internet; **the project downloads should only be done from trusted sources**. For more information of the risks see the [Hugging Face Hub documentation](https://huggingface.co/docs/hub/en/security-pickle).

For example, download the yso-mllm-en project from [NatLibFi/FintoAI-data-YSO repository](https://huggingface.co/NatLibFi/FintoAI-data-YSO) and show the fetched files:
```
$ annif download yso-mllm-en NatLibFi/FintoAI-data-YSO  # --trust-repo is needed if this is the first download from the repo 

Downloading project(s): yso-mllm-en
projects/yso-mllm-en.zip: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2.11M/2.11M [00:00<00:00, 8.72MB/s]
yso-mllm-en.cfg: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 165/165 [00:00<00:00, 256kB/s]
vocabs/yso.zip: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 61.6M/61.6M [00:05<00:00, 11.2MB/s]

$ tree data projects.d
data
├── projects
│   └── yso-mllm-en
│       └── mllm-model.gz
└── vocabs
    └── yso
        ├── subjects.csv
        ├── subjects.dump.gz
        └── subjects.ttl
projects.d
└── yso-mllm-en.cfg

```

To download all YSO projects with English language, assuming their project ids end with "en", use projects id pattern `"yso-*en"` in place of `yso-mllm-en`.

If any file contained in the archive already exists locally, the extraction does not overwrite the local file, but skips its extraction. To force overwrite of the existing local file, you can use the `--force/-f` option.

A specific revision (commit hash, branch name or tag) to download can be selected using the `--revision` option; see below for versioning projects.

# Uploading projects to Hugging Face Hub

The upload command allows to upload selected projects and their vocabularies to a specified Hugging Face Hub repository. The command zips the project directories and vocabularies of the projects whose project IDs match the given pattern to archive files, and uploads these archives along with the project configurations to the designated repository.

The yso-mllm-en project can be uploaded to [NatLibFi/FintoAI-data-YSO repository](https://huggingface.co/NatLibFi/FintoAI-data-YSO) like this:

    $ annif upload yso-mllm-en NatLibFi/FintoAI-data-YSO

    Uploading project(s): yso-mllm-en

Also the upload command targets the projects with a pattern of project IDs, so `"yso-*en"` could be used to upload all local YSO English projects.

The commit message can be specified with `--commit-message` option; the default commit message is _"Upload project(s) \<project-ID-pattern\> with Annif"_.

The branch to upload to can set by the `--revision` option, the default branch is `main`. Note that the branch needs to exist before upload, see below how to create branches.

## Model Cards
The Hugging Face Hub has good support for metadata of the models by using [Model Cards](https://huggingface.co/docs/hub/model-cards). 
Annif automatically creates or updates some information of the Model Cards on project uploads. By default when running `annif upload` (without `--no-modelcard` option):
- if Model Card does not exist in the destination repository, it is created with default contents,
- if Model Card exists, its project list and metadata are updated as necessary.

The metadata of the Model Card that are automatically set include model task "text-classification", a custom tag "annif" and the languages of the projects. The automatically inserted text content consist of a heading, Usage section and Projects section with a list of the projects that are stored in the repository (detected by the `.cfg` files in the repository); the project list is updated on every upload.

# Versioning projects

Git branches and tags can be used for versioning Annif projects in Hugging Face Hub.
Currently (by release [0.22](https://github.com/huggingface/huggingface_hub/releases/tag/v0.22.0)) the Hugging Face Hub commandline tool does not support accessing git branches or tags, but this will change in the future.
However, the tags can be accessed and manipulated using the Hugging Face Hub Python client. For example, to list branches and tags in the NatLibFi/FintoAI-data-YSO repository, use the command

    $ python -c "from huggingface_hub import HfApi; client=HfApi(); refs=client.list_repo_refs(repo_id='NatLibFi/FintoAI-data-YSO'); print([t.ref for t in refs.branches]); print([t.ref for t in refs.tags])"

A new branch, e.g. `release-2024-04`, can be created with command

    $ python -c "from huggingface_hub import HfApi; client=HfApi(); client.create_branch(repo_id='NatLibFi/FintoAI-data-YSO', branch='release-2024-04')"

A new tag, e.g. `2024-04`, can be created with command

    $ python -c "from huggingface_hub import HfApi; client=HfApi(); client.create_tag(repo_id='NatLibFi/FintoAI-data-YSO', tag='2024-04')"