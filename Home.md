# Annif Wiki

Welcome to the Annif usage documentation!

## 🧑‍💻 Introduction & Getting Started
Get started with Annif, understand system requirements, and learn about supported corpus formats and configuration options.
- [[Getting started]]: The first steps
- [[System requirements]]: OS, hardware, and dependencies
- [[Subject vocabulary formats]]: Supported formats for subject vocabularies
- [[Corpus formats]]: Supported formats for collections of documents (with or without assigned subjects) used for training, evaluation and testing
- [[Project configuration]]: How to set up and manage projects
- [[Optional features and dependencies]]: Some Annif features and backends need installing extra dependencies
- [[Backward compatibility between releases]]: What parts of Annif can and cannot change
- [[Architecture]]: Overview of Annif’s design

## 🚀 Deployment
Guides for running Annif in production environments.
- [[Usage with Docker]]: Containerized deployment
- [[Running as a WSGI service]]: Deployment as a traditional web service

## 🖥️ User Interfaces
Explore the different ways to interact with Annif.
- [[Command line interface]]: For Annif projects administration
- [[Web user interface]]: For testing and simple end-user usage
- [[REST API]]: For integrating other systems

## ⚙️ Preprocessing & Supporting Features
Features to tweak text inputs and help in workflows.
- [[Analyzers]]: Tokenization, stemming, etc.
- [[Transforms]]: Truncation and language filtering
- [[Language detection]]: Automatic language identification with CLI or API methods
- [[Subject exclusion and inclusion]]: Exclude certain subjects from a vocabulary
- [[🤗 Hugging Face Hub integration|Hugging Face Hub integration]]: Share and reuse models via Hugging Face

## 🎯 Optimization Techniques
Tips and tricks for improving results.
- [[Achieving good results]]: Best practices
- [[Reusing preprocessed training data]]: Save time and resources
- [[Generating synthetic training data]]: Augment your datasets

## 🧩 Backends
Annif supports a variety of backends (algorithms) for different use cases. See a [[comparison table|Backends]] and each backend’s page for details and configuration options.
- [[TF-IDF|Backend: TF-IDF]]
- [[fastText|Backend: fastText]]
- [[Omikuji|Backend: Omikuji]]
- [[MLLM|Backend: MLLM]]
- [[STWFSA|Backend: STWFSA]]
- [[YAKE|Backend: YAKE]]
- [[SVC|Backend: SVC]]
- [[Ensemble|Backend: Ensemble]]
- [[PAV|Backend: PAV]]
- [[nn_ensemble|Backend: nn_ensemble]]
- [[HTTP|Backend: HTTP]]
- [[Dummy|Backend: Dummy]]

## 🛠️ Development & Contribution
For contributors.
- [[Development flow, branches and tags]]: Workflow and versioning
- [[Release process]]: How to make a new release
- [[Creating a new backend]]: Extend Annif with new algorithms

## 🆘 Troubleshooting & Support
<!--- - [[Troubleshooting]]: Common issues and solutions--->
- [Annif-users mailing list/forum](https://groups.google.com/forum/#!forum/annif-users): Community support ↗️
- [GitHub Issues](https://github.com/NatLibFi/Annif/issues): Create an issue here to report bugs or request features ↗️
- [Security Policy](https://github.com/NatLibFi/Annif/security/policy): Guidelines for reporting vulnerabilities and security issues ↗️

---
[[Getting started →|Getting-started]]

