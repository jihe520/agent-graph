English | [简体中文](README.zh-CN.md)

![Agent Graph: the git graph, not the session, as the primary element of an ADE](assets/cover.png)

# Agent Graph

An interaction design exploration: making the git graph, instead of the session, the primary element of an ADE (agent development environment).

https://github.com/user-attachments/assets/123cfb00-07ef-4b2b-b531-33635909f158

This repository is not an app and contains no implementation you can install, just three things:

| File | Contents |
| --- | --- |
| [DESIGN.md](DESIGN.md) | Design spec: interaction model, visual encoding, rules for git operations, adaptation guidelines |
| [PROMPT.md](PROMPT.md) | Implementation prompt: has an agent build this interaction model into your ADE, whatever the ADE or tech stack |
| [index.html](index.html) | Reference prototype: a single file that demos every interaction with sample data; open it directly in a browser |

DESIGN.md, PROMPT.md, and the prototype's UI are written in Chinese. Agents read them without trouble, and the prompt tells the agent to write UI text in your ADE's own language.

## Why

Most ADEs today are built around sessions: a list of sessions, and clicking one opens a conversation. Once you have several agents running at once, some things are hard to see from a session list:

- Which branch and worktree each agent is on, and where its changes land
- Who spawned which subagents, and who is reviewing whose branch
- Which agents are waiting for your approval, and which are done and ready to merge
- Committing, pushing, merging, and cleaning up worktrees still means switching to a terminal or a git client

All agent work ends up in git. So change the primary element: the git graph is the main view, and each agent sits on the branch and worktree it works in, with its status visible in real time. Sessions move to a sidebar and open when you need them.

## Interaction model

- **One graph per project**: the top section, *Now*, shows each worktree with the agents and subagents working in it; below it is the commit history. Several projects can sit side by side.
- **Status on the avatar**: running, waiting for you, done, idle. Worktrees that need you come first.
- **Act in place**: allow or deny pending requests right on the graph, and merge finished branches directly.
- **Sessions in a sidebar**: click an agent and its conversation opens on the right while the graph stays visible. Click a commit in the conversation to locate it on the graph.
- **Git quick actions**: right-click a worktree, agent, or branch to commit, push, merge, rebase, or delete. Actions that aren't available right now say why; available ones show the git command they'll run.
- **Multiple harnesses on one screen**: Claude Code, Codex, Pi, and others appear on the same graph, told apart by color and avatar.

See [DESIGN.md](DESIGN.md) for the full rules.

## Usage

This assumes you can modify your ADE's source code, for example a fork of an open-source ADE like t3code, or your own harness frontend.

Start an agent session in the ADE's source repository and send:

```text
Clone https://github.com/jihe520/agent-graph into a temporary directory, read the PROMPT.md in it, and follow its instructions in the current project.
```

The agent first studies your ADE and gives you a concept mapping and an implementation plan. Once you confirm, it implements the plan in stages. If the agent can't access the network or run commands, copy PROMPT.md, DESIGN.md, and index.html into the current project, then have it read PROMPT.md.

If you just want to see it, open the [live demo](https://jihe520.github.io/agent-graph/) or open index.html in a browser.

## Adapting to different ADEs

The prompt doesn't assume the host's tech stack or terminology. Instead, it has the agent map the concepts first:

| This model | The host may call it |
| --- | --- |
| project | project, workspace, repo |
| agent | session, thread, task, conversation |
| harness | provider, runtime, CLI agent |
| waiting for you | approval, permission request, user input |
| subagent | subagent, task, spawned agent |
| worktree | worktree, sandbox, checkout |

When the host is missing a capability (no worktrees, no subagents, only one harness, or a terminal UI), there's a fallback for each case. See the [Adaptation (适配)](DESIGN.md#适配) section of DESIGN.md.

## Status

- Still exploratory: both the design spec and the prompt will change. The next step is to get the prompt working end to end on t3code, then revise based on the results.
- All data and live updates in the prototype are simulated. The 实时 (Live) and 重置 (Reset) controls in the top bar exist only for the prototype and are not part of the interaction model.
- Out of scope: replacing your git client (viewing diffs, resolving conflicts) and rewriting the session UI (the host's existing one is reused).

## Contributing

- Run the prompt on your ADE, then open an issue with screenshots, your concept mapping table, and where it got stuck. That feedback drives revisions of DESIGN.md and PROMPT.md.
- When you change an interaction, update the prototype and DESIGN.md together to keep them consistent.

## Modifying the prototype

index.html has no dependencies and needs no build step:

- Sample projects, branches, worktrees, and agents: `seed()`; sample conversations: `LOGS`
- Harness names and colors: `HARNESS`; branch colors: `PALETTE`
- Simulated actions, commit messages, and subagents: `SCRIPT`, `SUB_SCRIPT`, `MSGS`, `TEMPLATES`, `SUB_TASK`, `SUB_DONE`
- Row heights, lane width, and simulation interval: the constants at the top of the script
