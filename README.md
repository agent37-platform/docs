# Agent 37 documentation

This repository contains the Agent 37 documentation site built with Mintlify.

The repo includes:

- Product guides
- Site configuration in `docs.json`
- AI-tool setup guides for documentation work
- An example API reference section
- Reusable Mintlify patterns for writing and navigation

## AI-assisted writing

Set up your AI coding tool to work with Mintlify's documentation skill:

```bash
npx skills add https://mintlify.com/docs
```

This command installs Mintlify's documentation skill for tools like Claude Code, Cursor, Windsurf, and others. The skill includes component reference, writing standards, and workflow guidance for maintaining this docs site.

See the files in `ai-tools/` for tool-specific setup instructions.

## Development

Install the [Mintlify CLI](https://www.npmjs.com/package/mint) to preview documentation changes locally:

```
npm i -g mint
```

Run the following command at the root of the repo, where `docs.json` is located:

```
mint dev
```

View your local preview at `http://localhost:3000`.

## Publishing changes

Commit and push your changes through your normal review workflow. If this repository is connected to a Mintlify project, the configured deployment pipeline will publish the latest approved changes.

## Need help?

### Troubleshooting

- If your dev environment isn't running: Run `mint update` to ensure you have the most recent version of the CLI.
- If a page loads as a 404: Make sure you are running in a folder with a valid `docs.json`.

### Resources
- [Mintlify documentation](https://mintlify.com/docs)
