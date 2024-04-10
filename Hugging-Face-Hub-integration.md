### NOTE: THIS FEATURE IS EXPERIMENTAL IN ANNIF V1.1 AND SUBJECT TO CHANGE

Annif integration with Hugging Face Hub allows to easily upload and download projects and their associated vocabularies to and from [Hugging Face Hub model repositories](https://huggingface.co/docs/hub/models). This integration provides a convenient way to share Annif projects and collaborate on text classification tasks within the Hugging Face ecosystem.

Downloads are possible from public repositories without logging in to Hugging Face Hub, but for all uploads and for downloads from private repositories you need to have logged in using the [`huggingface-cli login` command](https://huggingface.co/docs/huggingface_hub/guides/cli#huggingface-cli-login) or to give a [User Access token](https://huggingface.co/docs/hub/security-tokens) with the `--token` option of the `annif upload` or `annif download` commands.

The integration utilizes the [Hugging Face Hub cache-system](https://huggingface.co/docs/huggingface_hub/guides/manage-cache); to explore the cache, use `huggingface-cli scan-cache` command, and to delete items from the cache, use `huggingface-cli delete-cache`. 

Note that using [glob wildcards](https://en.wikipedia.org/wiki/Glob_(programming)) (e.g. `*` or `?`) in the projects patterns for the below commands can make the shell to [expand the wildcards](https://www.gnu.org/software/bash/manual/html_node/Filename-Expansion.html) to matching _filenames_, instead of the intended projects, because the shell expansion happens before Annif processes the command arguments. At least when targeting all projects with `*` pattern the shell expands it to all filenames in the directory. To avoid shell expansion, use quotion marks around the pattern, e.g. `"*"`.

# Downloading projects from Hugging Face Hub

The download command enables to download selected projects and their associated vocabularies from a specified Hugging Face Hub repository. This command retrieves project and vocabulary archives along with their configuration files from the designated repository and extracts them to the local `data/` directory and `projects.d/` directory, respectively. 

For example, download the yso-mllm-en project from [NatLibFi/FintoAI-data-YSO repository](https://huggingface.co/NatLibFi/FintoAI-data-YSO):
```
annif download yso-mllm-en NatLibFi/FintoAI-data-YSO

Downloading project(s): yso-mllm-en
projects/yso-mllm-en.zip: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2.11M/2.11M [00:00<00:00, 8.72MB/s]
yso-mllm-en.cfg: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 165/165 [00:00<00:00, 256kB/s]
vocabs/yso.zip: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 61.6M/61.6M [00:05<00:00, 11.2MB/s]
```

To download all YSO projects with English language, use projects pattern `"yso-*en"` in place of `yso-mllm-en`.

If any file contained in the archive already exists locally, the extraction does not overwrite the local file, but skips its extraction. To force overwrite of the existing local file, you can use the `--force/-f` option.

A specific revision (commit hash, branch name or tag) to download can be selected using the `--revision` option; see below for versioning projects with git tags.

# Uploading projects to Hugging Face Hub

The upload command allows to upload selected projects and their vocabularies to a specified Hugging Face Hub repository. The command zips the project directories and vocabularies of the projects whose project IDs match the given pattern to archive files, and uploads these archives along with the project configurations to the designated repository.

The yso-mllm-en project can be uploaded to [NatLibFi/FintoAI-data-YSO repository](https://huggingface.co/NatLibFi/FintoAI-data-YSO) like this:

    annif upload yso-mllm-en NatLibFi/FintoAI-data-YSO

    Uploading project(s): yso-mllm-en

Also the upload command targets the projects with a pattern of project IDs, so `"yso-*en"` could be used to upload all local YSO English projects.

The commit message can be specified with `--commit-message` option; the default commit message is _"Upload project(s) \<project-ID-pattern\> with Annif"_.

# Versioning projects

Git tags can be used for versioning Annif projects in Hugging Face Hub.
Currently ([v0.22](https://github.com/huggingface/huggingface_hub/releases/tag/v0.22.0)) the Hugging Face Hub commandline tool does not support git tag processing, but this will change in the future.
However, the tags can be processed with Hugging Face Python client. For example, to list tags in a repository, use command

    python -c "from huggingface_hub import HfApi; client=HfApi(); refs=client.list_repo_refs(repo_id='NatLibFi/FintoAI-data-YSO'); print([t.ref for t in refs.tags])"

A new tag, e.g. `2024-04`, can be created with command

    python -c "from huggingface_hub import HfApi; client=HfApi(); client.create_tag(repo_id='NatLibFi/FintoAI-data-YSO', tag='2024-04')"