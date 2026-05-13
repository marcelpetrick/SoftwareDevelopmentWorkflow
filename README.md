# Software Development Workflow

This document is a guideline for developers and stakeholders for frictionless and efficient work.

## Workflow Model

The development workflow is agile. It follows selected principles from Kanban and Scrum, but it is not a strict Kanban or Scrum process because some ceremonies are intentionally missing.

The workflow should allow continuous work while still providing predictability.

## Milestones and Sprints

Work is clustered into milestones. The terms milestone and sprint are used interchangeably.

A milestone is a temporal container. A useful milestone duration is usually either:

- 14 days for short iteration cycles.
- 4 weeks for longer iteration cycles.

Milestones should be organized under a functional topic. Work that belongs together from a functional or feature perspective should be handled by the development team within the same milestone or sprint.

## Issues and Tickets

Milestones contain issues, also called tickets.

Issues collect requirements from stakeholders or from inside the team. These requirements must be implemented or completed in another suitable way. An issue can also describe non-development work, such as acquiring required items.

When the work in an issue is done, the resulting artifacts can be released.

## Requirements and Acceptance Criteria

Everyone should put sufficient effort into requirements engineering and writing down what is expected.

Each issue should clearly describe:

- What needs to be done.
- What the acceptance criteria are.
- What the developer needs to know before implementation.

Acceptance criteria should be written before implementation starts. This keeps testing simple because the expected result is already defined.

Requirements should be groomed before implementation. Grooming means checking whether they are understandable and free of contradictions.

## Issue Size

Issues should be scoped so that they contain no more than one day of work whenever possible.

For simplification, it can be necessary to first write down larger topics. These should then be broken down into sub-tickets.

## Estimation

Work should be estimated using three-point estimation in person-hours:

- Best case.
- Expected case.
- Worst case.

When possible, estimation should be done by the team so that the result is not only a specialist estimation.

The expected case is used by developers as the timebox for implementation.

## Time Logging and Predictability

Developers should log their time in GitLab on a daily basis.

When developers get close to the expected-case timebox, they should inform the stakeholders and project manager before the timebox is exceeded.

This is necessary for planning. It is understandable that estimations sometimes need to be increased because of unforeseen circumstances or because something was not checked before. However, this should happen before the timebox is broken, not after.

If time is not logged daily, predictability is lost.

## Ticket Updates

If something runs longer, developers should take time before leaving the workplace to add a short note to the ticket.

The note should describe in one or two sentences:

- What was tried.
- What is still blocking.
- What is planned next.

This makes the project status clearly and transparently visible inside the ticket instead of requiring people to ask someone directly.

## Implementation and Testing Flow

The development flow is:

1. Requirements come in.
2. Requirements are groomed.
3. Estimation is done.
4. Implementation happens.
5. Testing is done against the acceptance criteria.
6. The CI pipeline runs successfully.
7. Code review is done until everyone approves that the change fits.
8. The change is merged.

## Releases

At the end of a milestone, there should be a delivery, usually through a release.

For releases, a continuous integration pipeline should be used. The pipeline should put the artifact on a release page.

The release should be announced to stakeholders because not everyone is aware of how to use GitLab or where to find release artifacts.

## Continuous Integration and Quality

The continuous integration pipeline should include:

- Linters.
- Static code analyzers.
- Documentation generation.
- Intermediate artifacts.

It is better to check more things than too few. Once a large software system has accumulated quality issues, it is hard to remove them later.
