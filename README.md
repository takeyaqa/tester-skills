# tester-skills

## Overview

`tester-skills` is a repository of [agent skills](https://agentskills.io/) for software testing. It currently focuses on helping testers and test designers structure test inputs, apply specialized workflows, and delegate the deterministic parts of the workflow to the right tools.

## Available Skills

- [`design-pairwise-tests`](./skills/design-pairwise-tests/SKILL.md): Design pairwise and stronger t-way test patterns with deterministic `pict-cli` generation from explicit factors, levels, and optional constraints.

## Installation

### Using GitHub CLI

Run the following command and follow the prompts to select a skill and target agent:

```bash
gh skill install takeyaqa/tester-skills
```

See the [`gh skill install` documentation](https://cli.github.com/manual/gh_skill_install) for options such as `--agent`, `--scope`, and `--all`.

### Using npx

```bash
npx skills add takeyaqa/tester-skills
```

## License

This project is licensed under the MIT License—see the [LICENSE](./LICENSE) file for details.

---

If you find tester-skills useful, please consider starring the repository.
