# LLM research field notes

Since August 2026 I have been running the **AI Research Hub**, a local environment where several large language models work as a research team on open mathematics problems, with me as the human coordinator. Every action in the Hub goes through a logged, validated tool request, and the models keep detailed checkpoint records, so how they work, and how they fail, leaves evidence.

This repository collects what I learn from it over time, with posts on:
- failure modes, how each was caught, and the safeguards the Hub gained in response;
- what worked in coordinating models;
- other observations about LLMs as research collaborators.

Each post states its evidence and its limits.

## Posts

- **[How a team of AI models failed at research, and what caught it](posts/2026-10-how-a-team-of-ai-models-failed-at-research.md)** (October 2026)
  - 79 self-recorded errors from one model;
  - the "check a part, certify the whole" pattern across three researchers and the coding agent;
  - models that failed at the tool level;
  - why non-author review caught what self-checks missed.

## About

Marlowe Haws · [github.com/Marlowe-Haws](https://github.com/Marlowe-Haws)

The Hub itself is private for now; an open-source release is planned. Posts are drafted with the help of Claude from the Hub's own records, and I review each one before publishing.

Licensed under [CC BY 4.0](LICENSE.md): you may share and adapt these notes with credit.
