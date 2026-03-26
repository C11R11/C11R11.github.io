# Javascript Actions

The "Gold Standard" for the GitHub Marketplace.
* **Speed:** Faster than Docker because there is no container image to pull or build.
* **Cross-Platform:** Runs natively on **Linux, macOS, and Windows** runners.
* **Tooling:** You get access to the `@actions/toolkit`, which makes interacting with the GitHub API, secrets, and logs much easier than parsing strings in Bash.
* **Best For:** Most general-purpose actions (e.g., `actions/checkout`, `actions/setup-node`).

# Use cases

* Complex JSON/API Handling: You need to call the GitHub API, parse a 500-line JSON response, and make a logical decision based on it. Doing this in bash with jq is a nightmare; in JS, it's native.

* Speed (The "No-Docker" Advantage): A Docker action has to "pull" the image and "start" the container. That takes 30-60 seconds. A JS Action runs directly on the runner's host. It starts in 1 second.

* Cross-Platform Consistency: A Bash script might fail on a Windows runner. A JS Action runs on the node runtime, which is identical on Linux, Windows, and self hosted.

## Example

Imagine you want to create a tool for your team of 10 that:

* Reads all the labels on a PR.
* Checks if the user is in a specific "Approvers" team.
* Calculates a "Risk Score" based on the number of files changed.
* Comments on the PR with a formatted table.

> In Bash: You would spend 2 days fighting curl and jq syntax.
> In JS: You use github.context and a simple array.map() function. It’s cleaner, faster, and easier for another dev to read.

# Benefits

## The "Toolkit" Advantage (No more `curl` hell)
In Bash, if you want to comment on a PR or check a label, you have to write `curl` commands against the GitHub API, handle JSON parsing with `jq`, and manage Auth headers.
* **In a JS Action:** You use the `@actions/github` library. It’s a pre-authenticated SDK.
* **Benefit:** Your code is 90% logic and 10% "plumbing," instead of 90% Bash boilerplate.


# Anatomy of a JavaScript Action

## Folder Structure

```text
.github/actions/hello-js/
├── action.yml       # 1. Metadata (The "Manifest")
├── index.js         # 2. Logic (The Source)
├── package.json     # 3. Dependencies
├── .gitignore       # 4. Exclusion (Ignore node_modules)
└── dist/            # 5. The Bundle (Committed to Git)
    └── index.js     #    (Combined source + dependencies)
```

## 1. The Metadata File (`action.yml`)
This defines the interface. It tells GitHub this is a Docker action and which image to use.

```yml
name: 'JS Hello World'
description: 'A simple JavaScript Action for the lab'
inputs:
  who-to-greet:
    description: 'Who to greet'
    required: true
    default: 'DevOps Engineer'
outputs:
  time:
    description: 'The time we greeted you'
runs:
  using: 'node20'
  main: 'dist/index.js' # <--- IMPORTANT: Point to the compiled/bundled file
```

## 2. The Logic: index.js

The source code using the @actions/toolkit.

```javascript

const core = require('@actions/core');
const github = require('@actions/github');

async function run() {
  try {
    // 1. Get Inputs
    const name = core.getInput('who-to-greet');

    // 2. Logic
    core.info(`Hello, ${name}!`);
    
    // 3. Set Outputs
    core.setOutput('greeting', `Hello ${name}`);

    // 4. Debug/Warning/Error
    core.debug('Internal logic starting...');
    core.warning('Standard warning message');
  } catch (error) {
    // 5. Fail Gracefully (Crucial for DevOps observability)
    core.setFailed(`Action failed with error: ${error.message}`);
  }
}

run();
```

## 3. The Tooling: package.json
Focus on the stable library versions to avoid the "Module not found" errors you encountered.

```json
{
  "dependencies": {
    "@actions/core": "^1.11.0",
    "@actions/github": "^6.0.0"
  },
  "devDependencies": {
    "@vercel/ncc": "^0.38.4"
  },
  "scripts": {
    "build": "ncc build index.js -o dist --minify --no-cache && rm -f dist/package.json"
  }
}
```

## 4. The binary

Why? GitHub Runners do not run npm install for actions. They just run the main file.

The Solution: Use @vercel/ncc to compile the source and all node_modules into a single index.js file.

The Rule: node_modules is ignored; dist/ is committed.

| Feature | Detail |
| :--- | :--- |
| **Runtime** | `using: 'node20'` (Current standard). |
| **Speed** | Fastest (No container spin-up). |
| **OS Support** | Cross-platform (Windows, Linux, macOS). |
| **Key Library** | `@actions/core` (Inputs, Outputs, Logging). |
| **Advanced Lib** | `@actions/github` (Direct access to the Octokit API). |

# ⚡ Professional JS Action Lifecycle

1. **Initialize:** `npm init` + install `@actions/core`.
2. **Develop:** Write logic in `index.js`.
3. **Metadata:** Define inputs/outputs in `action.yml`.
4. **Bundle:** Use `@vercel/ncc` to create a `dist/` folder.
5. **Commit:** Ensure `action.yml`, `index.js`, and `dist/index.js` are in the repo.

## 💡 Why the 'dist' folder?
GitHub Actions execute the file specified in the `main:` field of `action.yml`. If you point to `index.js` but don't commit `node_modules`, the action will fail with "Module not found." Bundling with `ncc` merges everything into one file so it's ready to run instantly.

## ⚠️ The "Dist" Rule
GitHub does not install dependencies for actions at runtime. You must either:
1. Use **`ncc`** to build a single `dist/index.js` and commit it.
2. Use a **Docker Action** instead if you want to keep your code "clean" (at the cost of speed).

# Real world snippet

This is a "shortcut" that lets you write JS directly in your YAML.

```yml
- uses: actions/github-script@v7
  with:
    script: |
      const { data: pullRequest } = await github.rest.pulls.get({
        owner: context.repo.owner,
        repo: context.repo.repo,
        pull_number: context.payload.pull_request.number
      });
      console.log(`Working on PR: ${pullRequest.title}`);
```

If you can understand those 5 lines of JS, you have 90% of the knowledge needed for the "Custom Action" part of the exam and real-world DevEx.