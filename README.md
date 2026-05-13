# Software Development Workflow

This document is a guideline for developers and stakeholders for frictionless and efficient work.

Below is a cleaned-up and structured version of your workflow description.

---

# Development Workflow

The development workflow is agile and combines selected principles from **Kanban** and **Scrum**, without strictly following either framework. Since some Scrum ceremonies are intentionally omitted, the process should be understood as a **lightweight agile workflow** that supports both continuous delivery and a certain degree of planning predictability.

The workflow is organized around **milestones**, which may also be referred to as **sprints**. In this context, both terms are used interchangeably. A milestone is a **time-based planning container**, typically lasting either **two weeks** for shorter iteration cycles or **four weeks** for larger planning intervals.

## Milestones and Functional Scope

Milestones should group work by functional or feature-related topics wherever possible. This means that issues belonging to the same functional area should ideally be planned and implemented within the same milestone. This helps the development team focus on coherent topics and makes planning, testing, and delivery more predictable.

At the end of each milestone, there should usually be a tangible delivery, preferably in the form of a **release**. The release should contain the completed artifacts resulting from the issues implemented during the milestone.

## Issues and Requirements

Work is tracked through **issues**, also referred to as **tickets**. Issues collect requirements from stakeholders, the project team, or internal technical needs. They may describe implementation work, bug fixes, documentation tasks, research tasks, procurement activities, or other necessary project work.

Each issue should contain a clear description of what is expected. Particular attention should be paid to **requirements engineering**. The goal is that stakeholders, project managers, and developers invest enough effort upfront to make each issue understandable and actionable.

Every issue should include:

* A clear description of the requirement
* The expected outcome
* Acceptance criteria
* Relevant technical or functional constraints
* Dependencies or blockers, if known
* A three-point estimate

The acceptance criteria are especially important because they define when an issue can be considered done and provide the basis for testing.

## Issue Size

Issues should be cut as small as reasonably possible. Ideally, one issue should contain **no more than one working day of effort**.

For practical reasons, larger requirements may initially be written down as broader issues. However, these should later be broken down into smaller sub-tickets before implementation starts. This improves transparency, makes progress easier to track, and reduces planning risk.

## Estimation

Each issue should be estimated using a **three-point estimation** in person-hours:

* Best case
* Expected case
* Worst case

Whenever possible, estimation should be done by the team rather than by a single specialist. This helps avoid overly narrow specialist estimates and improves shared understanding within the team.

The **expected-case estimate** is used as the implementation timebox. Developers should treat this value as the planned amount of effort for completing the issue.

If it becomes clear during implementation that the estimate is no longer realistic, the developer should inform the project manager and relevant stakeholders **before** the timebox is exceeded. Estimation changes are acceptable when unforeseen circumstances arise or when assumptions turn out to be wrong, but they must be communicated early. The estimate should be adjusted before the issue has already overrun significantly.

## Time Tracking

Developers should log their time in GitLab on a daily basis.

Daily time tracking is necessary for transparency and predictability. Without regular time logging, it becomes difficult to understand actual effort, forecast delivery dates, identify risks, or improve future estimates.

If a developer approaches the expected-case estimate of an issue, they should proactively notify the project manager and stakeholders. This allows the team to react early, reassess the scope, adjust the estimate, or clarify blockers.

## Daily Progress Notes

For longer-running issues, developers should update the ticket before leaving work. A short comment of one or two sentences is sufficient.

The update should describe:

* What was tried or implemented
* What is still open or blocking progress
* What the developer plans to do next

This makes the current state of work visible to everyone involved in the project. It reduces the need for direct status requests and improves transparency.

## Requirement Grooming

Incoming requirements should be groomed before they are planned for implementation. Grooming means checking whether the issue is understandable, complete, and free of contradictions.

During grooming, the team should check whether:

* The requirement is understandable
* The expected result is clear
* Acceptance criteria are defined
* There are no contradictions or inconsistencies
* Dependencies are known
* The issue is small enough to be implemented
* The issue can be estimated

Only groomed and sufficiently clear issues should enter implementation.

## Implementation Workflow

A typical issue should pass through the following workflow:

1. Requirement is created
2. Requirement is groomed
3. Issue is estimated
4. Issue is planned into a milestone
5. Implementation starts
6. Developer logs time daily
7. Developer updates the issue when progress or blockers occur
8. Acceptance criteria are tested
9. CI pipeline runs successfully
10. Code review is performed
11. Required approvals are given
12. Changes are merged
13. Artifact is released

## Testing and Acceptance

Testing should be straightforward because acceptance criteria are defined before implementation starts. The acceptance criteria should describe what needs to be verified before the issue can be considered done.

An issue should only be completed when:

* The implementation satisfies the acceptance criteria
* Necessary tests have been performed
* The CI pipeline has passed
* Code review has been completed
* Required approvals have been given

## Code Review and Merge

Code review is mandatory before merging. The merge should only happen once the reviewers agree that the implementation is correct, maintainable, and aligned with the issue requirements.

Review should cover:

* Functional correctness
* Code quality
* Maintainability
* Test coverage where applicable
* Compliance with project conventions
* Documentation impact

## Continuous Integration and Releases

A continuous integration pipeline should be used for every relevant change. The pipeline should verify the quality of the software and produce the required artifacts.

The CI pipeline should include, where applicable:

* Build steps
* Automated tests
* Linters
* Static code analysis
* Documentation generation
* Packaging or artifact generation
* Release artifact publishing

It is better to check more quality aspects early than too few. Once a large amount of software has accumulated, it becomes much harder to remove structural quality issues later.

At the end of a milestone, the CI pipeline should publish the release artifacts to a release page or another defined delivery location.

## Stakeholder Communication

Releases should be announced to stakeholders. This is important because not every stakeholder actively follows GitLab or knows where to find release artifacts.

A release announcement should briefly explain:

* What has been released
* Which milestone or functional topic it belongs to
* Where the artifact can be found
* Any relevant notes, limitations, or follow-up work

This ensures that completed work is visible and usable outside the development team.

## Summary

The goal of this workflow is to combine continuous development with predictable milestone-based planning. Requirements should be clarified before implementation, issues should be small and estimable, developers should log time daily, and progress should be transparent directly inside GitLab.

By combining clear requirements, short feedback cycles, timeboxed implementation, code review, CI checks, and regular releases, the team can improve delivery reliability while maintaining software quality.
